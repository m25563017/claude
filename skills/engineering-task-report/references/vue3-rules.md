# 前端開發與程式風格規範 (Vue3)

## 1. 核心框架規範

- **版本**：Vue 3 (Composition API / `<script setup>`)。
- **組件通信 (Communication)**：
    - 嚴格使用 `props` 進行數據下傳，`emits` 進行事件上傳。
    - **禁止使用** `provide` / `inject`，以確保組件間的依賴關係透明。

## 2. 樣式規範 (Tailwind CSS v4+)

- **版本**：Tailwind CSS v4 以上。
- **前綴規則**：所有 Tailwind 類名必須加上 `tw:` 前綴。
    - 正確範例：`<div class="tw:flex tw:items-center tw:p-4">`。
    - 錯誤範例：`<div class="flex items-center">`。

## 3. 通知與彈窗規範 (Notification)

- **禁用原生方法**：嚴格禁止使用瀏覽器原生的 `alert()`。
- **標準套件**：必須使用 `@pieda/core` 的 `useNotify`。
- **實作要求**：
    - 必須宣告變數為 `$notify`。
    - **禁止包裝**：請直接在邏輯中調用 `$notify.alert`，不要將此寫法封裝成自定義方法 (Method/Function)。
- **程式碼範例**：

    ```typescript
    import { useNotify } from "@pieda/core";

    const $notify = useNotify();

    // 直接調用，不可包裝成其他 function
    $notify.alert({
        title: "系統通知",
        message: "發生錯誤，請稍後再試",
        variant: "error",
    });
    ```

## 4. API 與資料請求規範

- **集中管理**
    - 所有 API 請求必須透過 `stores/myApi.ts` 中定義的方法呼叫

- **路徑規範**
    - API URL 必須統一定義於 `$api` 物件中
    - 禁止在組件內直接寫死字串路徑

## 5. 工程進度報告產出標準

每次回報需包含以下內容：

- **今日開發摘要**
    - 條列式說明完成的功能或任務

- **規範檢查**
    - 是否誤用 `provide/inject`
    - 是否使用原生 `alert()`

- **Tailwind 檢查**
    - class 是否皆有 `tw:` 前綴

## 6. Vue 模板與命名規範

### 6.1 組件結構順序

- **強制要求**：單檔案組件 (SFC) 內的標籤排列順序必須統一為：
    1. `<script setup>`
    2. `<template>`
    3. `<style>`

### 6.2 CSS 與樣式撰寫標準

- **優先級**：樣式以 **Tailwind CSS** 為主。
- **轉移至 `<style>` 之判定**：
    - 若單一個標籤上的 Tailwind class **超過 6 個**，請將樣式抽離並寫入 `<style>` 區塊中。
- **巢狀規範**：
    - 在 `<style>` 中編寫 CSS 時，請盡量使用**巢狀 (Nesting)** 寫法。
    - **限制**：巢狀深度**不得超過三層**，以維持代碼可讀性與效能。

### 6.3 語義化容器命名策略 (Semantic Container Naming)

結合 **BEM 簡化版** 與 **Functional CSS** 的優勢：

- **命名慣例**：
    - 採用 **kebab-case** (連字元命名法)，以與 JavaScript 的 camelCase 及 Tailwind 的前綴邏輯進行視覺區隔。
- **核心規則**：
    - **重要容器 (Key Container)**：必須具備語義化的類名。
    - **位置順序**：語義化類名必須作為 `class` 屬性中的**第一個項目**，其後才接續 Tailwind 類名。

#### **判定原則 (何時命名？)**

| 類別 | 說明與範例 |
| **必須命名** | 頁面最外層佈局容器 (Layout Wrappers)、具獨立功能的組件主體 (Component Roots)、複雜表單或大型資料清單的包覆層。 |
| **不需命名** | 僅為了排版存在的輔助層 (如 `tw:flex` 中繼層)、單純的文字裝飾標籤 (如 `span`, `b`)。 |

#### **正確範例**：

```vue
<template>
    <section class="main-content-wrapper tw:flex tw:flex-col tw:min-h-screen">
        <div class="user-profile-card tw:p-6 tw:bg-white tw:shadow-lg">
            <h2 class="tw:text-xl">{{ userName }}</h2>
        </div>
    </section>
</template>

<style scoped>
.main-content-wrapper {
    /* 只有當 Tailwind class 超過 6 個或需特殊處理時才寫在此 */
    .user-profile-card {
        /* 巢狀示範，不超過三層 */
        &:hover {
            filter: brightness(0.95);
        }
    }
}
</style>
```

## 7. Vue 模板與命名規範

### 規則與慣例

- **`v-for` / `v-if` 必須用 `<template>` 包裝**。
- **禁止** 在同一個 HTML 標籤上同時使用 `v-if` 與 `v-for`。
- **強制** `v-for` 必須搭配具備唯一性的 `:key`。
- 正確範例：

```vue
<template v-if="items.length > 0">
    <template v-for="item in items" :key="item.id">
        <ListItem :item="item" />
    </template>
</template>
```

- **事件命名與回調 (Callback)**
    - **Props 接收的事件**：使用 `on*` 前綴 (例如：`onClose`, `onConfirm`)。
    - **內部處理函式**：使用 `handle*` 前綴 (例如：`handleDeleteClick`, `handleSubmit`)。
      _註：這能幫 AI 區分「傳進來的 function」與「組件內部定義的 function」。_
- **v-model 更新事件**
    - 統一使用 `@update:modelValue` 或 `@update:propertyName` 模式。
- **變數與 Store 命名慣例**
    - **Store 實例**：統一為 `xxxStore` (小駝峰)，例如 `const authStore = useAuthStore()`。
    - **外部工具/插件實例**：統一使用 `$` 前綴，例如 `$notify`、`$api`、`$ajax`。

### 檢查清單 (AI Review 用)

- [ ] 是否完全避免了 `v-if` 與 `v-for` 寫在同一標籤？
- [ ] 所有 `v-for` 是否都有綁定 `:key`？
- [ ] 外部套件是否皆以 `$` 開頭命名 (如 `$notify`)？
- [ ] 是否在組件內部使用了 `handle*` 命名本地處理函式？
