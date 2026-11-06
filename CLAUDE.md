# Claude Skill 管理專案

此專案用於管理可複用的 Claude Code Skill，以及個人全域設定的備份。

---

## 專案目標

集中維護常用 Skill（有 git 版本控制），再透過資料夾連結讓所有專案自動可用，不必每個專案各複製一份。

---

## 資料夾結構

```
f:\claude\
├── CLAUDE.md                         # 本說明文件
├── .gitattributes                    # 固定 *.sh 用 LF 換行，避免 Windows 下腳本壞掉
├── global-CLAUDE.md                  # 個人全域設定「備份」，實際生效位置是 ~/.claude/CLAUDE.md
└── skills\                           # 所有 Skill，每個 Skill 為獨立資料夾
    ├── web-security-scan\
    │   ├── SKILL.md
    │   ├── references\check-rules.md
    │   └── scripts\{scan_url.sh, scan_code.sh}
    └── engineering-task-report\
        ├── SKILL.md                  # 依框架自動選擇要讀哪份規範
        └── references\
            ├── report-rules.md       # 通用 SOP（所有框架都會讀）
            ├── vue3-rules.md         # Vue3 / Nuxt3 規範
            └── react-rules.md        # React / Next.js 規範
```

---

## 使用方式

### 方式一：全域連結（建議）

在 `C:\Users\<你>\.claude\skills\` 底下，為每個 Skill 建立資料夾連結（Junction），所有專案立刻都能用，改這裡就同步：

```powershell
New-Item -ItemType Junction -Path "$env:USERPROFILE\.claude\skills\web-security-scan"       -Target "F:\claude\skills\web-security-scan"
New-Item -ItemType Junction -Path "$env:USERPROFILE\.claude\skills\engineering-task-report" -Target "F:\claude\skills\engineering-task-report"
```

> 不要把整個 `~/.claude/skills` 換成連結，裡面可能有其他工具自動放的內容（例如 `synced/`）。每個 Skill 各連一次即可。
> 新增 Skill 後，記得替它多建一條連結。

### 方式二：只想給單一專案用

把**整個 Skill 資料夾**複製到該專案的 `.claude/skills/` 底下，不要拆開：

```powershell
Copy-Item -Recurse "F:\claude\skills\web-security-scan" "<目標專案>\.claude\skills\"
```

結果會是 `<目標專案>\.claude\skills\web-security-scan\SKILL.md`，`references\`、`scripts\` 跟著放在同一層。

### 個人全域設定

`global-CLAUDE.md` 的內容要放到 `~/.claude/CLAUDE.md` 才會生效（目前是複本，不是連結）。修改這份備份後，記得同步過去：

```powershell
Copy-Item "F:\claude\global-CLAUDE.md" "$env:USERPROFILE\.claude\CLAUDE.md" -Force
```

---

## 規範分層：什麼放哪裡

| 層級 | 放什麼 | 位置 |
|---|---|---|
| 通用 | 對話習慣、資安掃描、回報 SOP | `~/.claude/CLAUDE.md` + 全域 Skill |
| 技術棧 | Vue3 / React / Python 等規範 | Skill 的 `references/<framework>-rules.md` |
| 專案專屬 | 該專案的命名、API 路徑、業務規則 | 該專案自己的 `CLAUDE.md` |

重點：

- `references/` 是**按需讀取**，放著不會佔用上下文，所以不需要依專案增減規範檔。
- 換語言或框架時，只要新增一個 `<framework>-rules.md`，並在 `engineering-task-report/SKILL.md` 的框架對照表加一列。
- 只屬於某個專案的規則，不要放進通用 Skill，放該專案的 `CLAUDE.md`。

---

## 現有 Skills

| Skill | 說明 | 觸發情境 |
|---|---|---|
| `web-security-scan` | 網頁資安弱點掃描，涵蓋 OWASP Top 10 | 上線前安全檢查、XSS / SQL Injection / CSRF 等議題 |
| `engineering-task-report` | 工程任務完成後的標準回報與規範自檢，自動偵測 Vue3 / React | 開發任務收尾、產出任務報告、規範檢查 |

---

## 新增 Skill 規範

```
skills/<skill-name>/
├── SKILL.md          # 必要：YAML frontmatter 定義名稱與觸發條件，內容為執行流程
├── references/       # 選用：Skill 執行時需參考的規則文件
└── scripts/          # 選用：Skill 執行時需呼叫的腳本
```

`SKILL.md` frontmatter 格式：

```yaml
---
name: skill-name
description: >
    一段說明此 Skill 的功能與觸發情境，
    讓 Claude 能正確判斷何時使用。
---
```

注意：

- 各 Skill 的 `description` 不要重疊，否則同一句話會同時觸發多個 Skill。
- `SKILL.md` 內引用的檔案（`references/...`）必須真的存在。
- `SKILL.md` 內呼叫腳本時，路徑要寫 `${CLAUDE_SKILL_DIR}/scripts/xxx.sh`，不要寫相對路徑，否則從別的專案目錄執行會找不到。
- 腳本寫完先用 `bash -n <檔案>` 檢查語法。
- 只有一個 Skill 的規則因框架而異時，優先用 `references/` 分檔，不要複製出第二個 Skill。
