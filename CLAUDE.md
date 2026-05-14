# Claude Skill 管理專案

此專案用於管理可複用的 Claude Code Skill 與專案模板。

---

## 專案目標

統一管理各類 Skill 與設定模板，需要時直接複製對應資料夾到目標專案使用。

---

## 資料夾結構

```
f:\claude\
├── CLAUDE.md                         # 本說明文件
├── .claude.md                        # 個人全域 Claude 設定（非此專案專用）
└── skills\                           # 所有 Skill，每個 Skill 為獨立資料夾
    ├── web-security-scan\
    │   ├── SKILL.md                  # Skill 定義（觸發條件、執行流程）
    │   ├── references\
    │   │   └── check-rules.md       # 各安全項目判斷規則
    │   └── scripts\
    │       ├── scan_url.sh          # URL 遠端掃描腳本
    │       └── scan_code.sh         # 程式碼靜態分析腳本
    ├── engineering-task-report\
    │   ├── SKILL.md                  # Skill 定義
    │   └── references\
    │       ├── report-rules.md      # 工程任務通用 SOP
    │       └── vue3-rules.md        # Vue3 開發規範
    └── react-engineering-report\
        ├── SKILL.md                  # Skill 定義
        └── references\
            └── react-rules.md       # React / Next.js 開發規範
```

---

## 使用方式

### 複製 Skill 到目標專案

每個 Skill 資料夾設計為**自給自足**，可整包複製：

```bash
# 範例：複製 web-security-scan 到目標專案
cp -r skills/web-security-scan/ /path/to/target-project/
```

複製後目標專案結構：

```
target-project\
├── web-security-scan\
│   ├── SKILL.md
│   ├── references\
│   └── scripts\
```

再將 `SKILL.md` 移至 Claude 識別的 skill 位置（`.claude/skills/`）：

```bash
mkdir -p .claude/skills
mv web-security-scan/SKILL.md .claude/skills/web-security-scan.md
```

> 注意：`references/` 和 `scripts/` 需放在目標專案根目錄，路徑需與 SKILL.md 內的引用一致。

---

## 現有 Skills

| Skill                       | 說明                                    | 觸發情境                                      |
| --------------------------- | --------------------------------------- | --------------------------------------------- |
| `web-security-scan`         | 網頁資安弱點掃描，涵蓋 OWASP Top 10    | 上線前安全檢查、XSS/SQL Injection/CSRF 等議題 |
| `engineering-task-report`   | Vue3 工程任務完成後的標準回報格式       | 開發任務收尾、Vue3 規範檢查                   |
| `react-engineering-report`  | React / Next.js 工程任務完成後的回報格式 | 開發任務收尾、React 規範檢查                  |

---

## 新增 Skill 規範

新增 Skill 時須遵守以下結構：

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
