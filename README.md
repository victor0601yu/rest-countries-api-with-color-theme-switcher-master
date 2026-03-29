# REST Countries API with Color Theme Switcher

這是一個使用 **Vue 3** + **Vite** 提供支援的單頁應用程式 (SPA)，透過串接 [REST Countries API](https://restcountries.com) 來顯示世界各國的相關資訊。

## ✨ 主要功能 (Features)

*   **瀏覽所有國家**：在首頁以卡片網格的列表形式查看全球所有國家。
*   **搜尋國家**：提供文字輸入框，可依據國家名稱快速搜尋過濾。
*   **區域過濾**：提供下拉選單，可以依照區域 (Africa, America, Asia, Europe, Oceania) 過濾國家列表。
*   **詳細資訊頁面**：點擊首頁各個國家卡片，可進入獨立頁面查看更詳細的資訊（包含：原生名稱、人口數、所在與次分區、首都、頂級網域、使用貨幣及語言）。
*   **邊界國家導覽**：在國家詳細頁面中會列出相鄰的邊界國家，點擊即可快速跳轉至該鄰國的資訊頁面。
*   **深色/淺色主題切換**：提供深色暗黑模式與淺色模式切換功能，並使用 localStorage 記憶使用者的偏好設定。
*   **RWD 響應式設計**：良好適配各種螢幕尺寸（Desktop, Tablet, Mobile）。

## 🛠 技術堆疊 (Tech Stack)

*   **前端框架**: [Vue 3](https://vuejs.org/) (使用 Composition API 與 `<script setup>`)
*   **路由管理**: [Vue Router 4](https://router.vuejs.org/)
*   **建置工具**: [Vite](https://vitejs.dev/)
*   **樣式**: Scoped CSS，並依賴原生 CSS 變數 (`var(--color-surface)` 等) 來實現主題切換。

## 🚀 如何在本地端執行 (Project Setup)

本專案的主要程式碼放置於 `web` 資料夾底下。請先確保您的環境已經安裝好 [Node.js](https://nodejs.org/)。

1. **進入專案資料夾**：
   ```bash
   cd web
   ```

2. **安裝所需套件 (Dependencies)**：
   ```bash
   npm install
   ```

3. **啟動開發伺服器 (Development Server)**：
   ```bash
   npm run dev
   ```
   啟動後，終端機會顯示本地端測試網址（通常為 `http://localhost:5173/`），請於瀏覽器中開啟以查看應用程式。

4. **編譯打包為正式機版本 (Production Build)**：
   ```bash
   npm run build
   ```
   這會產生一個可以直接部署的 `dist` 資料夾。

5. **預覽打包後的檔案**：
   ```bash
   npm run preview
   ```

## 🔌 API 參考

專案內使用的主要 API 端點：
- 取得所有國家清單與基礎資訊：
  `GET https://restcountries.com/v3.1/all?fields=name,population,region,capital,flags,cca2`
- 取得單一國家的詳細資訊：
  `GET https://restcountries.com/v3.1/alpha/{code}`
