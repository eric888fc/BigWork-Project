# Spring Boot 全端電商平台 (E-Shop Demo)

這是一個功能完整的全端電商平台專案，模擬一個多賣家 (Multi-Seller) 的市集（如蝦皮或 Amazon）。

* **後端 (Backend):** 使用 Java Spring Boot 構建，包含 JWT 安全認證、RESTful API、JPA 資料庫管理和多角色權限控制。
* **前端 (Frontend):** 使用 Vanilla JavaScript (ES6+), HTML5, 和 CSS3 構建，不依賴任何框架 (like React/Vue)，專注於透過 `fetch` API 實作非同步 (Async/Await) 頁面渲染。

---

## ✨ 核心功能 (Features)

這個平台區分為「買家 (Buyer)」和「賣家 (Seller)」兩種角色，並提供一個公開的 API 層。
### 角色選擇:
<img width="585" height="371" alt="image" src="https://github.com/user-attachments/assets/1a3b43ad-8fb6-4227-8aab-72f842c24ec5" />

### 角色 1：公開 (Public)
* **認證 (Auth):**
    * 買家/賣家 角色分離的登入/註冊 。
      <table>
        <tr>
          <td valign="top">
            <img width="400" alt="image" src="https://github.com/user-attachments/assets/324da606-cf18-4468-8853-0244f81eb5fd" />
          </td>
          <td valign="top">
            <img width="400" alt="image" src="https://github.com/user-attachments/assets/631dae5f-73ad-4835-a3f5-e53cf91eca5c" />
          </td>
        </tr>
      </table>
    * 使用 JWT (JSON Web Tokens) 進行 API 身份驗證。
    * 支援「發送Email驗證碼」 (`/api/auth/send-code`)。
     <img width="587" height="335" alt="image" src="https://github.com/user-attachments/assets/33879d45-67e2-40a9-ada2-064fb4b7df48" />

    * 支援「忘記密碼」和「重設密碼」流程。
      <table>
        <tr>
          <td valign="top">
            <img width="491" height="407" alt="image" src="https://github.com/user-attachments/assets/76851239-4393-4aa1-9252-1e96e35fc5b3" />
          </td>
          <td valign="top">
            <img width="646" height="304" alt="image" src="https://github.com/user-attachments/assets/45391832-f064-4648-a5d9-6149183b603e" />
          </td>
          <td valign="top">
            <img width="490" height="477" alt="image" src="https://github.com/user-attachments/assets/484058ea-2a60-44d0-a90d-5d760692f055" />
          </td>
        </tr>
      </table>

### 角色 2：買家 (BUYER)
* **商品瀏覽:**
    * 在 `products.html` 瀏覽/篩選所有上架商品。
      <img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/abdd9ea2-f87e-4fad-bbcc-52c7ecabd83c" />

    * 在 `product-detail.html` 查看商品詳情、庫存和**所有顧客評價**。
* **電子錢包 (Wallet):**
    * 在 `cart.html` 頁面查看錢包餘額。
    * 支援「模擬儲值」(`POST /api/wallet/topup`)。
* **購物車 (Cart):**
    * 非同步將商品加入購物車 (`POST /api/cart/items`)。
    * 在 `cart.html` 中動態更新商品數量 (`PUT`) 和刪除商品 (`DELETE`)。
    * 購物車列表會與錢包餘額進行即時比較。
* **結帳 (Checkout):**
    * **[核心架構] 多賣家拆單:** 結帳時 (`POST /api/orders/checkout`)，後端會自動偵測購物車中來自不同賣家的商品，並將其**自動拆分**為多張獨立的 `OrderPO`（如 `OrderServiceImpl.java` 所示）。
    * 結帳時會同時驗證「商品庫存」和「錢包餘額」。
* **訂單管理 (Orders):**
    * 在 `orders.html` 瀏覽**所有**歷史訂單列表（`GET /api/orders/me`）。
    * 在 `order-detail.html` 查看特定訂單的完整詳情，包括商品快照、價格快照和買家資訊。
* **評價系統 (Ratings):**
    * **[核心架構] 已驗證的購買評價:** 買家只能在 `order-detail.html` 頁面對**已購買的訂單項目 (`OrderItemPO`)** 進行評價。
    * 支援**首次建立評價** (`POST /api/ratings`)。
    * 支援**更新/更改評價** (`PUT /api/ratings/{ratingId}`)。

### 角色 3：賣家 (SELLER)
* **電子錢包 (Wallet):**
    * 在 `seller-account.html` 查看銷售收入餘額。
    * 支援「模擬提款」(`POST /api/wallet/withdraw`)，提款前會驗證是否已設定銀行帳戶。
* **收款帳戶 (Bank Account):**
    * 在 `seller-account.html` 設定或更新收款用的銀行帳戶。
    * 後端使用 `@Valid` 和 `@Pattern` 驗證帳號格式（例如：8 位數字）。
    * 前端會正確顯示後端回傳的驗證錯誤訊息（例如：「銀行帳號必須為 8 位數字」）。
    * 為安全起見，查詢時後端只回傳**遮罩後**的帳號 (`accountNumberMasked`)。
* **訂單管理 (Orders):**
    * 在 `seller-orders.html` 瀏覽**所有「自己收到」**的訂單列表（`GET /api/seller/orders/me`）。
    * 卡片上會清楚顯示**買家是誰**以及該筆訂單包含的**商品項目**。
* **評價管理 (Ratings):**
    * 在 `seller-ratings.html` 瀏覽**所有「自己商品收到」**的評價（`GET /api/seller/ratings/me`）。
    * 卡片上會清楚顯示**評價的商品**、**買家是誰**、星星數和評論內容。

---

## 🛠️ 技術棧 (Technology Stack)

| 類別 | 技術 |
| :--- | :--- |
| **後端 (Backend)** | Java, Spring Boot, Spring Security (JWT), Spring Data JPA (Hibernate) |
| **前端 (Frontend)** | Vanilla JavaScript (ES6+ Async/Await, Fetch API), HTML5, CSS3 |
| **資料庫 (Database)** | MySQL (或 H2 / PostgreSQL) |
| **驗證 (Validation)** | `jakarta.validation` ( ` @Valid`, `@Pattern` ) |
| **Java 核心** | POJO (實體), VO/DTO (資料傳輸), DAO (儲存庫), Service, Controller 分層架構 |

---

## 🚀 如何開始 (Getting Started)

### 1. 後端 (Spring Boot)
1.  **Clone 儲存庫:**
    ```bash
    git clone [https://github.com/YourUsername/YourRepoName.git](https://github.com/YourUsername/YourRepoName.git)
    cd YourRepoName
    ```
2.  **設定資料庫:**
    * 在您的 MySQL 中建立一個新的資料庫 (Schema)，例如 `eshop_db`。
3.  **設定 `application.properties`:**
    * 進入 `src/main/resources/application.properties`。
    * 更改 `spring.datasource.url`, `username`, `password` 以符合您的 MySQL 設定。
    * 設定 `jwt.secret`（任意複雜字串）。
    * （選填） 如果要測試註冊驗證碼，請設定 `spring.mail` 相關資訊（例如 Gmail 應用程式密碼）。
4.  **啟動後端:**
    * 使用 Maven（或您的 IDE）啟動應用程式（`DemoApplication.java`）。
    * `mvn spring-boot:run`
    * 伺服器將運行在 `http://localhost:8080`。

### 2. 前端 (HTML/JS)
1.  **不需安裝:** 這是一個靜態網頁專案。
2.  **開啟檔案:**
    * 直接在瀏覽器中打開 `frontend-html/index.html`（或任何其他 `html` 檔案）。
    * （推薦：使用 `Live Server`（如 VS Code 擴充功能） 來避免 CORS 問題）。

3.  **開始使用:**
    * 點擊「註冊」並分別建立一個 `BUYER` 和一個 `SELLER` 帳號即可開始測試所有功能。

---

## 🏛️ 專案架構 (Architecture)

### API 端點結構

| 路徑 | 控制器 | 目的 |
| :--- | :--- | :--- |
| `/api/auth/**` | `AuthController` | 處理所有登入、註冊和 Token |
| `/api/public/**` | `PublicProductController` | 公開的商品和評價查詢 |
| `/api/profile/me` | `ProfileController` | （需登入）獲取/更新個人資料 |
| `/api/wallet/**` | `WalletController` | （需登入）錢包餘額、儲值/提款 |
| `/api/cart/**` | `CartController` | （買家） 購物車管理 |
| `/api/orders/**` | `OrderController` | （買家） 結帳與訂單查詢 |
| `/api/ratings/**` | `RatingController` | （買家） 新增/更新評價 |
| `/api/seller/account` | `BankAccountController` | （賣家） 收款帳戶管理 |
| `/api/seller/orders/**` | `SellerOrderController` | （賣家） 查詢收到的訂單 |
| `/api/seller/ratings/**` | `SellerRatingController` | （賣家） 查詢收到的評價 |

---

## 📄 授權 (License)

本專案採用 MIT 授權。
