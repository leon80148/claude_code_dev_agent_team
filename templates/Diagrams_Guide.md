# 📊 圖表規範指南 - 流程圖與架構圖標準

## 🎯 概述

本指南定義了在 AI Agent 開發團隊中使用的各類圖表標準，確保所有 agent 產出的技術文檔具有一致性和專業性。

## 📋 圖表類型與用途

### 1. 流程圖 (Flowchart)
**用途**：描述業務流程、算法邏輯、決策過程
**負責Agent**：agent_product_manager, agent_ui_designer
**使用工具**：Mermaid, PlantUML

### 2. C4 架構圖 (C4 Model)
**用途**：系統架構的多層次描述
**負責Agent**：agent_product_manager, agent_full_stack_developer
**使用工具**：Mermaid, PlantUML, Structurizr

### 3. 時序圖 (Sequence Diagram)
**用途**：描述系統組件間的交互時序
**負責Agent**：agent_full_stack_developer
**使用工具**：Mermaid, PlantUML

### 4. ER圖 (Entity Relationship)
**用途**：數據模型設計
**負責Agent**：agent_product_manager, agent_full_stack_developer
**使用工具**：Mermaid, dbdiagram.io

## 🔧 工具使用規範

### Mermaid 使用規範

#### 流程圖語法
```mermaid
flowchart TD
    %% 節點定義
    Start([開始/結束 - 圓角矩形])
    Process[處理過程 - 矩形]
    Decision{判斷條件 - 菱形}
    Data[/數據輸入輸出 - 平行四邊形/]
    SubProcess[[子流程 - 雙邊框矩形]]
    
    %% 連接線
    Start --> Process
    Process --> Decision
    Decision -->|是| Data
    Decision -->|否| SubProcess
    
    %% 樣式定義
    style Start fill:#90EE90
    style Decision fill:#FFB6C1
```

#### C4 模型 - Level 1: 系統上下文
```mermaid
graph TB
    %% 定義樣式類
    classDef user fill:#08427B,color:#fff
    classDef system fill:#1168BD,color:#fff
    classDef external fill:#999999,color:#fff
    
    %% 節點定義
    U[fa:fa-user 用戶]:::user
    S[目標系統<br/>主要功能描述]:::system
    E1[外部系統1<br/>集成說明]:::external
    E2[外部系統2<br/>集成說明]:::external
    
    %% 關係定義
    U -->|使用| S
    S -->|調用API| E1
    S -->|發送數據| E2
```

#### C4 模型 - Level 2: 容器圖
```mermaid
graph TB
    subgraph "系統邊界"
        subgraph "前端層"
            W[Web應用<br/>React 18.x<br/>託管: CloudFront]
            M[移動應用<br/>React Native<br/>發布: App Store]
        end
        
        subgraph "應用層"
            A[API網關<br/>Kong/Nginx<br/>部署: K8s]
            S1[微服務1<br/>Node.js 18.x<br/>業務邏輯A]
            S2[微服務2<br/>Python 3.11<br/>業務邏輯B]
        end
        
        subgraph "數據層"
            DB[(主數據庫<br/>PostgreSQL 14<br/>RDS)]
            Cache[(緩存<br/>Redis 7.0<br/>ElastiCache)]
            Queue[消息隊列<br/>RabbitMQ<br/>Amazon MQ]
        end
    end
    
    %% 連接關係
    W --> A
    M --> A
    A --> S1
    A --> S2
    S1 --> DB
    S1 --> Cache
    S2 --> Queue
```

#### C4 模型 - Level 3: 組件圖
```mermaid
graph LR
    subgraph "API服務內部組件"
        subgraph "表現層"
            RC[路由控制器<br/>Express Router]
            MW[中間件<br/>認證/日誌/限流]
        end
        
        subgraph "業務層"
            US[用戶服務<br/>用戶管理邏輯]
            PS[產品服務<br/>產品管理邏輯]
            OS[訂單服務<br/>訂單處理邏輯]
        end
        
        subgraph "數據訪問層"
            UR[用戶倉儲<br/>User Repository]
            PR[產品倉儲<br/>Product Repository]
            OR[訂單倉儲<br/>Order Repository]
        end
        
        subgraph "基礎設施層"
            Logger[日誌服務]
            Monitor[監控服務]
            Config[配置管理]
        end
    end
    
    RC --> MW
    MW --> US
    MW --> PS
    MW --> OS
    US --> UR
    PS --> PR
    OS --> OR
    US --> Logger
    PS --> Monitor
```

#### C4 模型 - Level 4: 代碼級別
```mermaid
classDiagram
    %% 接口定義
    class IUserService {
        <<interface>>
        +createUser(data: UserDTO): Promise~User~
        +getUserById(id: string): Promise~User~
        +updateUser(id: string, data: UserDTO): Promise~User~
        +deleteUser(id: string): Promise~boolean~
    }
    
    %% 實現類
    class UserService {
        -userRepository: IUserRepository
        -validator: IValidator
        -logger: ILogger
        +createUser(data: UserDTO): Promise~User~
        +getUserById(id: string): Promise~User~
        +updateUser(id: string, data: UserDTO): Promise~User~
        +deleteUser(id: string): Promise~boolean~
        -validateUserData(data: UserDTO): ValidationResult
        -checkPermissions(userId: string): boolean
    }
    
    %% 數據模型
    class User {
        +id: string
        +email: string
        +name: string
        +role: UserRole
        +createdAt: Date
        +updatedAt: Date
    }
    
    class UserDTO {
        +email: string
        +name: string
        +password: string
    }
    
    %% 依賴關係
    UserService ..|> IUserService : implements
    UserService --> User : uses
    UserService --> UserDTO : uses
    UserService --> IUserRepository : depends
```

### 時序圖規範
```mermaid
sequenceDiagram
    autonumber
    participant U as 用戶
    participant F as 前端應用
    participant G as API網關
    participant A as 認證服務
    participant B as 業務服務
    participant D as 數據庫
    participant C as 緩存
    
    U->>F: 發起登錄請求
    F->>G: POST /api/login
    G->>A: 驗證憑證
    
    alt 緩存命中
        A->>C: 查詢用戶緩存
        C-->>A: 返回用戶數據
    else 緩存未命中
        A->>D: 查詢用戶
        D-->>A: 用戶數據
        A->>C: 更新緩存
    end
    
    A-->>G: JWT Token
    G-->>F: 登錄成功
    F-->>U: 顯示主頁
    
    Note over U,F: 後續API調用
    U->>F: 請求數據
    F->>G: GET /api/data + JWT
    G->>A: 驗證Token
    A-->>G: Token有效
    G->>B: 轉發請求
    B->>D: 查詢數據
    D-->>B: 返回數據
    B-->>G: 業務數據
    G-->>F: 響應數據
    F-->>U: 顯示數據
```

### ER圖規範
```mermaid
erDiagram
    %% 實體定義
    USER {
        uuid id PK "主鍵"
        string email UK "唯一郵箱"
        string name "用戶名"
        enum role "角色: ADMIN|USER|GUEST"
        datetime created_at "創建時間"
        datetime updated_at "更新時間"
    }
    
    PROFILE {
        uuid id PK
        uuid user_id FK "關聯用戶"
        string avatar_url "頭像URL"
        json preferences "用戶偏好設置"
        text bio "個人簡介"
    }
    
    PRODUCT {
        uuid id PK
        string sku UK "庫存單位"
        string name "產品名稱"
        decimal price "價格"
        int stock "庫存數量"
        enum status "狀態: ACTIVE|INACTIVE"
    }
    
    ORDER {
        uuid id PK
        string order_no UK "訂單號"
        uuid user_id FK
        decimal total_amount "總金額"
        enum status "訂單狀態"
        datetime created_at
    }
    
    ORDER_ITEM {
        uuid id PK
        uuid order_id FK
        uuid product_id FK
        int quantity "數量"
        decimal unit_price "單價"
        decimal subtotal "小計"
    }
    
    %% 關係定義
    USER ||--o| PROFILE : has
    USER ||--o{ ORDER : places
    ORDER ||--|{ ORDER_ITEM : contains
    PRODUCT ||--o{ ORDER_ITEM : includes
```

## 📐 圖表設計原則

### 1. 一致性原則
- 使用統一的顏色方案
- 保持相同的符號約定
- 遵循統一的命名規範

### 2. 簡潔性原則
- 避免過度複雜的圖表
- 每個圖表聚焦單一主題
- 適當使用子圖分解複雜度

### 3. 可讀性原則
- 添加必要的註釋和說明
- 使用清晰的標籤
- 保持合理的佈局間距

### 4. 完整性原則
- 包含所有關鍵組件
- 標註重要的數據流向
- 說明關鍵的技術決策

## 🎨 顏色規範

### 系統組件顏色
- 🟦 **核心系統**：#1168BD
- 🟩 **內部服務**：#438543
- 🟨 **外部系統**：#999999
- 🟥 **用戶/角色**：#08427B

### 狀態顏色
- ✅ **成功/正常**：#90EE90
- ⚠️ **警告/待定**：#FFD700
- ❌ **錯誤/失敗**：#FF6B6B
- ℹ️ **信息/說明**：#87CEEB

## 📝 文檔集成規範

### 在PRD中使用
1. 業務流程圖必須包含在「業務流程」章節
2. C4架構圖必須包含在「技術架構」章節
3. 數據模型ER圖必須包含在「數據模型」章節

### 在技術文檔中使用
1. API時序圖用於說明接口調用流程
2. 組件圖用於說明模組依賴關係
3. 類圖用於說明代碼結構

## 🔄 更新維護

### 版本控制
- 圖表源碼與文檔一起進行版本控制
- 重大變更需要標註版本號
- 保留歷史版本供追溯

### 同步更新
- 代碼變更時同步更新架構圖
- 業務流程調整時更新流程圖
- 數據模型變更時更新ER圖

## 📚 參考資源

### 官方文檔
- [Mermaid官方文檔](https://mermaid-js.github.io/)
- [C4 Model官網](https://c4model.com/)
- [PlantUML文檔](https://plantuml.com/)

### 最佳實踐
- [架構圖繪製最佳實踐](https://www.infoq.cn/article/architecture-diagrams)
- [流程圖設計指南](https://www.lucidchart.com/blog/flowchart-best-practices)

---

**重要提示**：
- 所有Agent在產出技術文檔時必須遵循本規範
- 圖表是溝通的重要工具，請確保準確性和可讀性
- 定期review和更新圖表，保持與實際系統的一致性