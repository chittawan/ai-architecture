BPMN = มุมมอง "Process & Business Flow"
- แสดง swimlane → เห็นว่าใคร/ระบบไหนทำ activity ไหน
Swimlane: Customer / Order Service / Payment Service / Logistics
- Flow: Customer place order → Order Service create → Payment Service authorize → Logistics deliver
Mermaid = มุมมอง "System & Technical Flow"



Enterprise Architecture View ที่ควรมี
1. Business / Process View (BPMN)
- Mermaid ยังไม่รองรับ BPMN โดยตรง แต่ผมเขียน pseudo-flow แทนได้
```mermaid
flowchart LR
  subgraph Customer
    A[Place Order]
  end

  subgraph OrderService
    B[Validate Order]
    C[Create Sales Order]
  end

  subgraph PaymentService
    D[Process Payment]
  end

  A --> B --> C --> D
```

```mermaid
flowchart LR
  Customer([Customer]) -->|Place Order| APIGW
  APIGW --> OrderService[Order Service]
  OrderService -->|Validate| CRM[(CRM)]
  OrderService -->|Create Sales Order| SAP[(SAP)]
  OrderService -->|Payment Request| Payment[Payment Gateway]
  Payment -->|Confirm| OrderService
  OrderService -->|Delivery Request| Logistics[Logistics]
  Logistics -->|Delivery Confirm| OrderService
  OrderService -->|Invoice| SAP
  SAP --> CRM
  OrderService -->|Notify| Customer
```


2. Application / Integration View (Mermaid System Context)

```mermaid
flowchart LR
  Customer -->|Order API| OrderService
  OrderService -->|Payment API| PaymentService
  OrderService -->|Delivery API| LogisticsService
  OrderService -->|SAP RFC| SAP
```

3. Integration Flow / Sequence (Mermaid Sequence)
```mermaid
sequenceDiagram
  Customer->>OrderService: Place Order
  OrderService->>PaymentService: Request Payment
  PaymentService-->>OrderService: Payment Success
  OrderService->>LogisticsService: Create Delivery
  LogisticsService-->>OrderService: Delivery Confirm
  OrderService-->>Customer: Order Complete
```

4. Data / Information View (Mermaid ERD)
```mermaid
erDiagram
  CUSTOMER ||--o{ ORDER : places
  ORDER ||--|{ PAYMENT : has
  ORDER ||--|{ SHIPMENT : includes
```
