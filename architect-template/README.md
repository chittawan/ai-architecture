# Architect Template (v2.0)

## 🚀 What's New in v2.0
- ✅ Process metadata support
- ✅ Enhanced role types (participant/lane)
- ✅ Gateway support (XOR, AND, OR)
- ✅ Advanced activity attributes (SLA, risk, API)
- ✅ Conditional flows
- ✅ Subprocess support
- ✅ Message/Signal events
- ✅ Input validation

## 📁 โครงสร้างไฟล์

```
architect-template/
├──  input_roles_act.yaml     # ไฟล์ input หลัก (v2.0 format)
├── regenerate promt          # คำสั่งสำหรับ regenerate
├── 01_*.bpmn                 # BPMN diagram (จะถูกสร้างขึ้น)
├── 01_*.md                   # Documentation (จะถูกสร้างขึ้น)
├── 02_*SystemContext.md      # System Context (จะถูกสร้างขึ้น)
└── 03_*Sequence.md           # Sequence Diagram (จะถูกสร้างขึ้น)
```

## 🎯 วิธีการใช้งาน

### 1. แก้ไข input_roles_act.yaml
แก้ไข roles, activities, flows และ elements อื่นๆ ตามความต้องการ

### 2. Regenerate ไฟล์
วิธีที่ 1: ใช้ rule โดยตรง
```
@system-architect/sa_999
```

วิธีที่ 2: ใช้คำสั่งสั้น
```
regenerate architect-template
```

วิธีที่ 3: Regenerate ทุกโปรเจกต์
```
regenerate all
```

## 📋 โครงสร้าง YAML v2.0

### Basic Structure (Compatible with v1.x)
```yaml
**output-location : /{this}/*

roles:
  - id: ROLE_ID
    name: Role Name
    responsibility: Role Responsibility  
    description: Role Description

activities:
  - id: act1
    role: ROLE_ID
    name: Activity Name
    eventType: start|end|intermediate
    description: Activity Description

flows:
  - from: act1
    to: act2
```

### Enhanced Structure (v2.0 Features)
```yaml
**output-location : /{this}/*

# Process metadata
process:
  name: Process Name
  description: Process Description

# Enhanced roles
roles:
  - id: ROLE_ID
    name: Role Name
    type: participant|lane  # participant=external, lane=internal
    responsibility: Role Responsibility
    system: Technology/System Name
    description: Role Description

# Enhanced activities  
activities:
  - id: act1
    role: ROLE_ID
    name: Activity Name
    type: task|userTask|serviceTask|sendTask|receiveTask
    eventType: start|end|intermediate|timer|message|signal|error
    sla: 30m|2h|1d  # Service Level Agreement
    risk: high|medium|low
    system: Target System
    api: /api/endpoint
    description: Activity Description

# Gateways (new)
gateways:
  - id: gw1
    name: Gateway Name
    type: exclusive|parallel|inclusive|eventBased
    role: ROLE_ID
    defaultFlow: flow_id

# Enhanced flows
flows:
  - id: flow1  # optional ID
    from: element1
    to: element2
    name: Flow Label  # optional
    condition: expression  # for conditional flows
    type: sequence|message|association

# Subprocesses (optional)
subprocesses:
  - id: sub1
    name: Subprocess Name
    role: ROLE_ID
    type: embedded|callActivity|transaction
    activities: [act1, act2, act3]

# Messages/Signals (optional)
messages:
  - id: msg1
    name: Message Name
    from: ROLE_ID
    to: activity_id
```

## 🎨 ตัวอย่างการใช้งาน

### 1. Simple Process (Basic)
- ใช้โครงสร้างพื้นฐานเหมือนเดิม
- เหมาะสำหรับ process ง่ายๆ

### 2. Complex Process (Advanced)
- ใช้ Gateway สำหรับ decision points
- กำหนด SLA และ risk levels
- ระบุ system และ API endpoints
- เพิ่ม subprocess สำหรับ grouped activities

### 3. Integration Process
- ใช้ message events
- ระบุ external participants
- แสดง async communications

## 📝 หมายเหตุ
- ไฟล์ ` input_roles_act.yaml` มีช่องว่างข้างหน้า (leading space)
- สามารถใช้ภาษาไทยและอังกฤษผสมกันได้
- `{this}` จะถูกแทนที่ด้วยชื่อ directory ปัจจุบัน
- Features ใหม่ทั้งหมดเป็น optional - ยังใช้ format เดิมได้
- sa_001 v2.0 จะ validate input และแจ้ง error ที่ชัดเจน
