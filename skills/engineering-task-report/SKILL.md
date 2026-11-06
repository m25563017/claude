---
name: engineering-task-report
description: >
  工程任務完成報告 Skill。當使用者完成一個開發任務、功能實作、或 Bug 修復後，
  需要產出結構化的工作回報，或對程式碼進行規範自檢時觸發。
  適用情境：「幫我整理剛才做了什麼」、「產出任務報告」、「檢查有沒有違反規範」、「收尾」。
  會自動偵測專案框架（Vue3 / Nuxt3 / React / Next.js / 其他），只載入對應的規範檔進行審查。
---

# Engineering Task Report Skill

協助開發者在任務完成後，依照標準 SOP 產出結構化的工程回報，並進行程式碼規範自檢。

---

## 執行流程

### Step 1：確認任務框架與內容

先看專案的 `package.json`（`vue`、`nuxt`、`react`、`next` 等依賴）與本次對話內容，判斷框架。
判斷不出來、或不確定本次改了什麼時，再向使用者詢問。

| 偵測結果 | 載入的規範檔 |
|---|---|
| Vue3 / Nuxt3 | `references/vue3-rules.md` + `references/report-rules.md` |
| React / Next.js | `references/react-rules.md` + `references/report-rules.md` |
| 其他或框架無關 | 只用 `references/report-rules.md` |

**只讀對應的檔案**，不要把不相關框架的規範也讀進來。
新增框架時，只要在 `references/` 加一個 `<framework>-rules.md`，並在上表加一列。

### Step 2：規範自我檢查

先做通用檢查，再依框架做專項檢查。完整規則請讀對應的 rules 檔，下面只列高頻違規項目。

#### 通用 AI 工作流檢查（`references/report-rules.md`）

- [ ] AI 工具使用是否記錄？
- [ ] 程式碼品質是否通過審核？
- [ ] 測試是否覆蓋關鍵路徑？

#### Vue3 專項（`references/vue3-rules.md`）

- [ ] SFC 標籤順序：`<script setup>` → `<template>` → `<style>`
- [ ] 是否完全避免了 `v-if` 與 `v-for` 寫在同一標籤？
- [ ] 所有 `v-for` 是否都有綁定 `:key`？
- [ ] 是否誤用 `provide` / `inject`？
- [ ] 是否使用原生 `alert()`（應改用 `$notify`）？
- [ ] Tailwind class 是否皆有 `tw:` 前綴？
- [ ] 外部套件是否皆以 `$` 開頭命名？
- [ ] 是否有使用 `any` 型別？
- [ ] Pinia 解構是否正確使用了 `storeToRefs`？
- [ ] 註解是否描述「意圖」而非「行為」？
- [ ] [SSR 專案] 瀏覽器 API 是否包在 `if (process.client)` 或 `<ClientOnly>` 內？

#### React / Next.js 專項（`references/react-rules.md`）

- [ ] 所有 Tailwind class 是否都有 `tw:` 前綴？
- [ ] 是否使用了 `class` 而非 `className`？
- [ ] 是否出現 Class Component（`extends React.Component`）？
- [ ] `.map()` 是否都有唯一的 `key`（禁止用 index）？
- [ ] Props callback 是否用 `on*` 前綴，內部處理函式是否用 `handle*`？
- [ ] Hooks 是否都在最頂層呼叫，沒有放在條件式或迴圈內？
- [ ] API 請求是否集中在 `lib/api.ts`，沒有在元件內寫死路徑？
- [ ] Zustand store 是否只存跨元件共享的狀態？
- [ ] 是否出現 Emoji 字元？圖示是否改用 SVG？
- [ ] 是否使用 `any` 型別？
- [ ] 註解是否描述「意圖」而非「行為」？

### Step 3：產出報告

只輸出「本次偵測到的框架」那一段規範檢查，不要列出不相關的框架。

---

## 報告格式

```markdown
## 今日開發摘要

- （條列式說明完成的功能或任務）

## 規範檢查

### <框架名稱> 規範
- [ ] （把 Step 2 該框架的檢查項目逐項列出，標示通過 / 違規）

### AI 工作流規範
- [ ] AI 工具使用是否記錄？
- [ ] 程式碼品質是否通過審核？
- [ ] 測試是否覆蓋關鍵路徑？

## 違規與修復建議

- （若有違規，列出位置與正確改法；沒有就寫「無」）

## AI 使用記錄

- 使用工具：（列出本次用到的 AI 工具）
- 主要貢獻：（AI 生成 / 人工調整 / 純人工）
- 人工審核：已完成 / 待確認
```

---

## 重要原則

1. **如實回報** — 若有違規項目，誠實列出，不要掩蓋
2. **給出修復建議** — 發現違規時，同時提供正確的改法
3. **高頻違規優先** — `tw:` 前綴、`key` 使用、`any` 型別是最常出現的問題
4. **用繁體中文輸出** — 報告語言與使用者一致
5. **詳細規則參考** — 各規範細節請見 `references/` 目錄下的對應文件
