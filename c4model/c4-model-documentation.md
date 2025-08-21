# C4 Model Documentation - Sales Management System
**Project:** Microservice-based Sales Management System with Multi-channel Support

---

# 1️⃣ เริ่มจาก Context Diagram

เป็นภาพรวมสูงสุดของระบบ (System in Context)

แสดงว่า ระบบของเราคุยกับใครบ้าง (External Systems / Users / Clients)

## System Context Overview

ระบบ Sales Management Platform เป็นศูนย์กลางที่เชื่อมต่อกับผู้ใช้งาน 3 กลุ่มหลักและระบบภายนอก 6 ระบบ

```mermaid
graph LR
    %% External Users/Actors
    ST[Sales Team<br/>📱 Mobile Users] --> SMS[Sales Management<br/>System<br/>🏢 Core Platform]
    AM[Admin/Management<br/>💻 Web Users] --> SMS
    CU[Customers<br/>🛒 End Users] --> SMS
    
    %% External Systems Integration
    SMS --> SAP[SAP<br/>🏭 ERP System]
    SMS --> TM[TechServ Mobile<br/>📲 Mobile Platform]
    SMS --> LAZ[Lazada<br/>🛍️ E-commerce]
    SMS --> SHP[Shopee<br/>🛒 E-commerce]
    SMS --> FLW[Flow<br/>💳 E-commerce]
    SMS --> LOA[Line OA<br/>💬 Messaging Platform]
    
    %% Styling
    classDef userClass fill:#e1f5fe,stroke:#01579b,stroke-width:2px
    classDef systemClass fill:#fff3e0,stroke:#e65100,stroke-width:3px
    classDef externalClass fill:#f3e5f5,stroke:#4a148c,stroke-width:2px
    
    class ST,AM,CU userClass
    class SMS systemClass
    class SAP,TM,LAZ,SHP,FLW,LOA externalClass
```

### Context Relationships Description:

#### 👥 **Primary Users:**
- **Sales Team:** ใช้ Mobile App สำหรับจัดการลูกค้าและสั่งซื้อ
- **Admin/Management:** ใช้ Web Panel สำหรับจัดการระบบและรายงาน
- **Customers:** สั่งซื้อผ่าน Online Platforms ต่างๆ

#### 🔗 **External System Integrations:**
- **SAP:** ซิงค์ข้อมูลคำสั่งซื้อและสินค้า
- **TechServ Mobile:** Platform สำหรับ Mobile App
- **E-commerce Platforms:** รับคำสั่งซื้อจาก Lazada, Shopee, Flow
- **Line OA:** สื่อสารกับลูกค้าและรับออเดอร์

---

# 2️⃣ ต่อด้วย Container Diagram

แสดงว่า ระบบถูกแบ่งออกเป็นอะไรบ้าง (Web App, Mobile App, Database, API Service ฯลฯ)

## Internal Container Architecture

ภายในระบบประกอบด้วย 8 Container หลัก ที่ทำงานร่วมกันในรูปแบบ Microservice Architecture

```mermaid
graph LR
    %% User Interfaces
    MA[Mobile App<br/>SalesTeams<br/>📱 React Native] --> OS[Order Service<br/>🛒 Node.js API]
    WA[Web Admin Panel<br/>💻 React.js] --> OS
    WA --> PS[Product Service<br/>📦 Node.js API]
    WA --> PMS[Promotion Service<br/>🎯 Node.js API]
    WA --> PYS[Payment Service<br/>💳 Node.js API]
    
    %% Microservices Communication
    OS --> PS
    OS --> PMS
    OS --> PYS
    OS --> KF[Kafka<br/>📨 Message Broker]
    
    %% Database Layer
    OS --> DB[(MongoDB<br/>📊 NoSQL Database)]
    PS --> DB
    PMS --> DB
    PYS --> DB
    
    %% Message Broker Communication
    KF --> PS
    KF --> PMS
    KF --> PYS
    
    %% External Integration Point
    PYS -.-> EXT[External Payment<br/>Gateway]
    
    %% Styling
    classDef uiClass fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    classDef serviceClass fill:#fff3e0,stroke:#e65100,stroke-width:2px
    classDef dataClass fill:#e3f2fd,stroke:#0277bd,stroke-width:2px
    classDef messageClass fill:#fce4ec,stroke:#c2185b,stroke-width:2px
    
    class MA,WA uiClass
    class OS,PS,PMS,PYS serviceClass
    class DB dataClass
    class KF messageClass
```

### Container Responsibilities:

#### 🖥️ **User Interface Layer:**
- **Mobile App (SalesTeams):** Sales team interface สำหรับการจัดการลูกค้าและออเดอร์
- **Web Admin Panel:** Management interface สำหรับการจัดการระบบและข้อมูล

#### ⚙️ **Microservice Layer:**
- **Order Service:** จัดการคำสั่งซื้อและ workflow หลัก
- **Product Service:** จัดการข้อมูลสินค้าและ inventory
- **Promotion Service:** จัดการโปรโมชั่นและส่วนลด
- **Payment Service:** ประมวลผลการชำระเงิน

#### 💾 **Data & Messaging Layer:**
- **MongoDB:** Primary database สำหรับเก็บข้อมูลทั้งหมด
- **Kafka:** Message broker สำหรับ event-driven communication

---

# 3️⃣ แล้วค่อยไปที่ Component Diagram

แบ่ง Container เป็น Component ย่อย

แสดง logic ภายใน, service, module, microservice

## Order Service Components

รายละเอียดภายใน Order Service ซึ่งเป็น core service ของระบบ

```mermaid
graph TD
    %% Order Service Internal Components
    OC[Order Controller<br/>🎮 REST API Endpoints] --> OB[Order Business Logic<br/>🧠 Core Processing]
    OC --> OV[Order Validation<br/>✅ Input Validation]
    
    OB --> OR[Order Repository<br/>💾 Data Access Layer]
    OB --> WF[Workflow Engine<br/>⚡ Order State Management]
    OB --> IN[Integration Layer<br/>🔗 External Service Calls]
    
    %% External Service Integration
    IN --> PSC[Product Service Client<br/>📦 Inventory Check]
    IN --> PMSC[Promotion Service Client<br/>🎯 Discount Calculation]
    IN --> PYSC[Payment Service Client<br/>💳 Payment Processing]
    
    %% Event Publishing
    WF --> EP[Event Publisher<br/>📡 Kafka Producer]
    
    %% Data Layer
    OR --> DB[(Order Database<br/>Collections)]
    
    %% Styling
    classDef controllerClass fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    classDef businessClass fill:#fff3e0,stroke:#e65100,stroke-width:2px
    classDef dataClass fill:#e3f2fd,stroke:#0277bd,stroke-width:2px
    classDef integrationClass fill:#fce4ec,stroke:#c2185b,stroke-width:2px
    
    class OC,OV controllerClass
    class OB,WF businessClass
    class OR,DB dataClass
    class IN,PSC,PMSC,PYSC,EP integrationClass
```

## Payment Service Components

รายละเอียดภายใน Payment Service สำหรับการประมวลผลการชำระเงิน

```mermaid
graph TD
    %% Payment Service Internal Components
    PC[Payment Controller<br/>💳 Payment API] --> PB[Payment Business Logic<br/>🧠 Transaction Processing]
    PC --> PV[Payment Validation<br/>✅ Security Validation]
    
    PB --> PR[Payment Repository<br/>💾 Transaction Storage]
    PB --> GW[Gateway Manager<br/>🌐 External Gateway Integration]
    PB --> FR[Fraud Detection<br/>🛡️ Security Checks]
    
    %% Gateway Integrations
    GW --> CC[Credit Card Gateway<br/>💳 Card Processing]
    GW --> PP[PayPal Gateway<br/>🔵 PayPal Integration]
    GW --> BB[Bank Transfer<br/>🏦 Direct Banking]
    
    %% Event & Notification
    PB --> PN[Payment Notification<br/>📧 Status Updates]
    PB --> PE[Payment Events<br/>📡 Kafka Publisher]
    
    %% Data Storage
    PR --> PDB[(Payment Database<br/>Transaction Records)]
    
    %% Styling
    classDef controllerClass fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    classDef businessClass fill:#fff3e0,stroke:#e65100,stroke-width:2px
    classDef dataClass fill:#e3f2fd,stroke:#0277bd,stroke-width:2px
    classDef gatewayClass fill:#fce4ec,stroke:#c2185b,stroke-width:2px
    classDef securityClass fill:#ffebee,stroke:#d32f2f,stroke-width:2px
    
    class PC,PV controllerClass
    class PB businessClass
    class PR,PDB dataClass
    class GW,CC,PP,BB gatewayClass
    class FR securityClass
```

---

# 4️⃣ Optional: Sequence Diagram / Flow

แสดง flow ของข้อมูลหรือ request-response ระหว่าง component / user / external system

## Sales Team Order Processing Flow

แสดงขั้นตอนการสั่งซื้อผ่าน Mobile App ของ Sales Team

```mermaid
sequenceDiagram
    participant ST as Sales Team
    participant MA as Mobile App
    participant OS as Order Service
    participant PS as Product Service
    participant PMS as Promotion Service
    participant PYS as Payment Service
    participant KF as Kafka
    participant SAP as SAP System
    
    ST->>MA: Create New Order
    MA->>OS: POST /orders
    
    OS->>PS: Check Product Availability
    PS-->>OS: Product Details & Stock
    
    OS->>PMS: Calculate Discounts
    PMS-->>OS: Promotion Details
    
    OS->>PYS: Process Payment
    PYS-->>OS: Payment Confirmation
    
    OS->>KF: Publish Order Event
    KF->>SAP: Sync Order Data
    
    OS-->>MA: Order Created Successfully
    MA-->>ST: Order Confirmation
    
    Note over ST,SAP: Complete order workflow<br/>with external integration
```

## Multi-Channel Order Integration Flow

แสดงการรับคำสั่งซื้อจาก E-commerce Platforms ต่างๆ

```mermaid
sequenceDiagram
    participant EP as E-commerce Platform<br/>(Lazada/Shopee/Flow)
    participant OS as Order Service
    participant PS as Product Service
    participant PYS as Payment Service
    participant KF as Kafka
    participant WA as Web Admin
    
    EP->>OS: Webhook: New Order
    OS->>PS: Validate Product & Stock
    PS-->>OS: Inventory Status
    
    alt Stock Available
        OS->>PYS: Reserve Payment
        PYS-->>OS: Payment Reserved
        
        OS->>KF: Publish Order Event
        KF->>WA: Notify Admin Dashboard
        
        OS-->>EP: Order Accepted
    else Out of Stock
        OS-->>EP: Order Rejected
        Note over EP,OS: Inventory insufficient
    end
    
    Note over EP,WA: Multi-channel order<br/>processing workflow
```

## Admin Management Flow

แสดงการจัดการระบบผ่าน Web Admin Panel

```mermaid
sequenceDiagram
    participant AM as Admin/Management
    participant WA as Web Admin Panel
    participant PS as Product Service
    participant PMS as Promotion Service
    participant OS as Order Service
    participant DB as MongoDB
    participant KF as Kafka
    
    AM->>WA: Login to Admin Panel
    WA->>WA: Authenticate User
    
    AM->>WA: Update Product Information
    WA->>PS: PUT /products/:id
    PS->>DB: Update Product Data
    PS->>KF: Publish Product Update Event
    
    AM->>WA: Create New Promotion
    WA->>PMS: POST /promotions
    PMS->>DB: Store Promotion Data
    PMS->>KF: Publish Promotion Event
    
    AM->>WA: View Order Analytics
    WA->>OS: GET /orders/analytics
    OS->>DB: Query Order Data
    DB-->>OS: Analytics Data
    OS-->>WA: Analytics Response
    WA-->>AM: Display Dashboard
    
    Note over AM,KF: Admin management<br/>with real-time updates
```

---

# 📊 Architecture Summary

## System Characteristics:
- **Architecture Pattern:** Microservices
- **Database:** MongoDB (NoSQL)
- **Message Broker:** Apache Kafka
- **API Style:** RESTful APIs
- **Frontend:** React.js (Web) + React Native (Mobile)

## Key Design Decisions:
1. **Event-Driven Architecture** ใช้ Kafka สำหรับ loose coupling
2. **Domain-Driven Design** แบ่ง service ตาม business domain
3. **Multi-Channel Support** รองรับหลายช่องทางการขาย
4. **Scalable Infrastructure** รองรับการขยายตัวในอนาคต

## Integration Points:
- **SAP Integration:** Real-time order synchronization
- **E-commerce APIs:** Webhook-based order integration
- **Payment Gateways:** Multiple payment method support
- **Mobile Platform:** TechServ Mobile SDK integration

---

**Location:** `/c4model/c4-model-documentation.md`
**Last Updated:** 2024
**Version:** 1.0
