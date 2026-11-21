# 冰島 12 天自駕環島行程地圖

互動式行程地圖（2026/7/14 – 7/25）。行程存在 Firebase 雲端資料庫，**只有管理者（lily830116@gmail.com）能編輯**，其他人打開網址只能檢視；管理者一儲存，所有人的地圖都會更新。

## 檔案說明

| 檔案 | 用途 |
|---|---|
| `index.html` | 整個網頁（地圖底圖、程式、內建原始行程都在裡面） |
| `firestore.rules` | Firebase 資料庫安全規則（決定誰能讀、誰能改） |
| `README.md` | 本說明 |

---

## 一次性設定（約 15–20 分鐘）

### A. Firebase（專案：iceland-b4321）

到 <https://console.firebase.google.com> 打開 **iceland-b4321** 專案。

1. **建立 Firestore 資料庫**
   左側「建構 → Firestore Database」→「建立資料庫」→ 選「以正式版模式啟動」→ 位置選 `asia-east1 (台灣)`（建立後不能更改）。
2. **貼上安全規則**
   Firestore Database →「規則」分頁 → 刪掉原本內容，貼上 `firestore.rules` 的全部內容 →「發布」。
3. **開啟 Google 登入**
   左側「建構 → Authentication」→「開始使用」→「登入方式」→ 選 **Google** → 啟用 → 選擇支援電子郵件 → 儲存。
4. **加入網站網域（重要）**
   Authentication →「設定」→「授權網域」→「新增網域」→ 輸入你的 Netlify 網域，例如 `xxxx.netlify.app`（不含 `https://`）。之後若改用自訂網域，也要再加進來。

> 網頁裡的 Firebase 設定（apiKey 等）本來就會公開，這是正常的；真正擋住別人修改的是第 2 步的安全規則。

### B. GitHub

1. 到 <https://github.com/new> 建立一個 repository，例如 `iceland-map`（Public 或 Private 都可以）。
2. 進入 repository →「Add file → Upload files」→ 把 `index.html`、`firestore.rules`、`README.md` 拖進去 →「Commit changes」。

### C. Netlify 連動 GitHub

- **沿用現在的 Netlify 網站（網址不變）**：進入網站 →「Project configuration → Build & deploy → Continuous deployment → Repository」→「Link repository」→ 選 GitHub → 選剛剛的 repository。
- **或建立新網站**：Netlify 首頁「Add new project → Import an existing project」→ GitHub → 選 repository。

Build 設定：**Build command 留空、Publish directory 留空（或填 `/`）**，Branch 用 `main`。之後只要 GitHub 上的檔案有變更，Netlify 會自動重新部署。

### D. 第一次把行程放上雲端

1. 打開網站，右上角狀態會顯示「雲端尚無資料（顯示內建行程）」。
2. 按右上角的 🔒 → 用 **lily830116@gmail.com** 登入 → 出現「編輯行程」按鈕。
3. 按「編輯行程」→ 直接按「儲存並同步到雲端」。
4. 狀態變成「雲端行程 · 更新時間」就完成了。

---

## 日常使用

| 想做的事 | 怎麼做 | 需要動 GitHub 嗎？ |
|---|---|---|
| 修改行程、景點、住宿 | 網站上 🔒 登入 →「編輯行程」→ 儲存 | 不用，所有人即時更新 |
| 分享給旅伴 | 直接給 Netlify 網址 | 不用 |
| 改網頁外觀、換底圖 | 修改 `index.html` 後上傳到 GitHub | 要，Netlify 會自動部署 |
| 改錯想找回舊版 | Firebase → Firestore → `trips/iceland/history`，每次儲存都有一份；複製該筆 `daysJson` 內容存成 `.json`，在「編輯行程 → 匯入 JSON」後儲存 | 不用 |
| 備份 | 「編輯行程 → 匯出 JSON」 | 不用 |

### 權限

- 只有 `lily830116@gmail.com` 能修改，其他帳號登入會顯示「沒有編輯權限」並自動登出。
- 就算有人修改網頁程式，Firebase 安全規則也會拒絕非管理者寫入。
- 要新增可編輯的人：同時修改 `firestore.rules` 的 `isOwner()`（重新發布規則）和 `index.html` 裡的 `OWNER_EMAIL`。

### 沒有網路時

網頁會顯示「離線（顯示上次的行程）」，使用這台裝置上次讀到的雲端行程；從沒連上過的裝置則顯示 `index.html` 內建的原始行程。地圖底圖已內嵌在檔案裡，離線也能看。

## 常見問題

- **登入時出現 `auth/unauthorized-domain`**：A-4 的授權網域沒有加入目前的網址。
- **按儲存出現 `permission-denied`**：確認登入的是 lily830116@gmail.com，並確認 A-2 的規則已「發布」。
- **手機上登入視窗沒跳出來**：允許該網站開啟彈出式視窗後再試一次；網頁也會自動改用整頁跳轉登入。
