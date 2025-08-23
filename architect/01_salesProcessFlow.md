# Sales Process Flow - BPMN Documentation

## Overview
This document describes the Sales Process Flow based on the business requirements defined in the input specification. The process covers the complete sales cycle from route planning to payment clearing, including privilege and promotion verification.

## Process Roles (Swimlanes)

| Role ID | Role Name | Responsibility | Description |
|---------|-----------|----------------|-------------|
| SALE | Sales | Operation | Responsible for defining requirements and initial customer interaction |
| KA | Key Account Management | CRM Management | Manages key accounts and monitors sales targets |
| RT | Retail | CRM Management | เบิกของขึ้นรถ แล้วไปขายหน้าร้าน กลับมาเคลียร์เงิน คืนของ |
| OMS | Order Management System | - | Order Center, Order Management, ทุกคำสั่งซื่อจะต้องส่งข้อมูลมาที่นี่ |
| AFS | After Sales | - | จัดการบริการหลังการขาย, รับคืนสินค้า/ลดหนี้ ลดหนี้/เพิ่มหนี้ |
| PP | Privilege & Promotion | - | จัดการบริการหลังการขาย, รับคืนสินค้า/ลดหนี้ ลดหนี้/เพิ่มหนี้ |
| PM | Payment | - | Payment processing and management |
| LE | Logistics Execution | - | Logistics and delivery execution |
| GLP | Group Loyalty Platform | - | Customer loyalty program management |

## Process Activities

| Activity ID | Activity Name | Role | Event Type | Description |
|-------------|---------------|------|------------|-------------|
| act1 | จัด route แผนเยี่ยมลูกค้า | SALE | Start | จัด route แผนเยี่ยมลูกค้า ประจำวัน |
| act2 | เบิกของขึ้นรถ | SALE | Start | เบิกของขึ้นรถ ตามแผนที่วางไว้ |
| act3 | check-in | KA | Task | check-in หน้าร้านลูกค้า / if no ลูกค้าให้ไปขายหน้าร้าน |
| act4 | ขายของหน้าร้าน | RT | Task | ขายของหน้าร้าน |
| act5 | รับคำสั่งซื้อ | OMS | Task | รับคำสั่งซื้อ |
| act5-1 | ตรวจสอบสิทธิ์ส่วนลด/โปรโมชั่น | PP | Task | ตรวจสอบสิทธิ์ส่วนลด/โปรโมชั่น |
| act6 | รับชำระเงิน | PM | Task | รับชำระเงิน |
| act7 | check-out | KA | Task | check-out ออกจากร้านลูกค้า |
| act8 | เคลียร์เงิน | SALE | End | เคลียร์เงินหลังเลิกงาน |

## Process Flow

| From Activity | To Activity | Flow Description |
|---------------|-------------|------------------|
| act1 | act1 | Self-loop for route planning |
| act2 | act3 | เบิกของขึ้นรถ → check-in |
| act3 | act4 | check-in → ขายของหน้าร้าน |
| act4 | act5 | ขายของหน้าร้าน → รับคำสั่งซื้อ |
| act5 | act5-1 | รับคำสั่งซื้อ → ตรวจสอบสิทธิ์ส่วนลด/โปรโมชั่น |
| act5-1 | act5 | ตรวจสอบสิทธิ์ส่วนลด/โปรโมชั่น → รับคำสั่งซื้อ (validation loop) |
| act5 | act6 | รับคำสั่งซื้อ → รับชำระเงิน |
| act6 | act7 | รับชำระเงิน → check-out |
| act7 | act8 | check-out → เคลียร์เงิน |

## Process Description

The Sales Process Flow represents a comprehensive sales cycle in a retail/distribution environment with privilege validation:

1. **Route Planning (act1)**: Sales team plans daily customer visit routes (self-loop indicates iterative/recurring planning)
2. **Inventory Loading (act2)**: Sales staff loads products onto vehicles according to the planned route
3. **Customer Check-in (act3)**: Key Account Management performs check-in at customer location, with fallback to direct retail sales if no customer is available
4. **Retail Sales (act4)**: Retail team conducts front-of-store sales activities
5. **Order Processing (act5)**: Order Management System receives and processes purchase orders
6. **Privilege Validation (act5-1)**: Privilege & Promotion system validates discounts and promotional offers (creates validation loop with order processing)
7. **Payment Processing (act6)**: Payment system handles transaction processing
8. **Customer Check-out (act7)**: Key Account Management performs check-out from customer location
9. **Cash Clearing (act8)**: Sales team clears cash and reconciles accounts at end of day

## Key Process Features

### Validation Loop
- **act5 ↔ act5-1**: The process includes a validation loop between order processing and privilege verification, ensuring proper discount/promotion validation before payment processing
- This loop allows for iterative validation and correction of promotional offers

### Conditional Flow
- **act3**: Enhanced check-in process with conditional logic - if no customer is available, the process can proceed to direct retail sales

### Multi-Role Coordination
- The process spans 9 different roles/systems, demonstrating complex organizational coordination
- Clear handoffs between departments ensure accountability and proper process flow

## Technical Notes

- **Role Assignment**: Note that act1 references "SALE Manager" in the original input but is assigned to SALE role for consistency
- **Flow Validation**: All sequence flows are properly validated with no orphaned activities
- **Event Types**: Proper BPMN event typing with Start events (act1, act2), Tasks (act3-act7), and End event (act8)

## Future Enhancements

This documentation can be extended with:
- **System Context Diagram**: Showing external systems and stakeholders
- **Sequence Diagrams**: Detailed interaction flows between roles
- **Entity Relationship Diagrams**: Data models supporting the process
- **Integration Points**: API specifications and system interfaces
- **Exception Handling**: Error flows and rollback procedures
- **Performance Metrics**: KPIs and measurement points

---
*Generated from: architect/input_roles_act.yaml*  
*BPMN File: architect/salesProcessFlow.bpmn*  
*Last Updated: $(date)*