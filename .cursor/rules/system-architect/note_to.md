# System Architect Rules - Todo List

## 📌 Current Status
- ✅ sa_001.mdc - YAML to BPMN Generator (v2.0 - Enhanced with validation, gateways, subprocesses)
- ✅ sa_002.mdc - BPMN to System Context Diagram
- ✅ sa_999.mdc - Auto Regeneration Tool
- ✅ sa_003.mdc - BPMN to Sequence Diagram
- ✅ สร้าง sa_004.mdc - Generate Component/Container Diagram จาก Service Mapping
- [ ] สร้าง sa_005.mdc - Generate ERD/Data Model จาก Business Entities
- [ ] สร้าง sa_validate.mdc - Validate YAML input structure และ BPMN output
- [ ] สร้าง sa_template.mdc - Generate project template structure อัตโนมัติ
- [ ] เพิ่ม Service Mapping section ใน input YAML template
- [ ] สร้าง sa_c4model.mdc - Generate full C4 Model diagrams

## 📋 Phase 1: Foundation (Priority: High)
- [ ] **sa_validate.mdc** - Input/Output Validation
  - Validate YAML structure before processing
  - Check BPMN XML validity
  - Error reporting with clear messages
  - Support dry-run mode

- [ ] **Enhance input_roles_act.yaml template**
  - Add service mapping section
  - Add integration protocols (REST, RFC, MQ, Event)
  - Add business entities section
  - Add metadata section (version, author, date)

## 📋 Phase 2: Extended Views
- [x] **sa_003.mdc** - Sequence Diagram Generator
  - Parse BPMN flows and generate Mermaid sequence
  - Support sync/async patterns
  - Include conditional flows
  - Add timing annotations

- [x] **sa_004.mdc** - Component/Container Diagram
  - Map Activities to Services
  - Show technology stack
  - Generate C4 Container diagram
  - Support deployment view

## 📋 Phase 3: Data & Templates
- [ ] **sa_005.mdc** - ERD/Data Model Generator
  - Extract entities from activities
  - Generate Mermaid ERD
  - Support relationship types
  - Include data attributes

- [ ] **sa_template.mdc** - Project Template Generator
  - Auto-create project structure
  - Generate pre-filled YAML template
  - Include example data
  - Setup regeneration script

## 📋 Phase 4: Advanced Features
- [ ] **sa_c4model.mdc** - Full C4 Model Suite
  - Context diagram (Level 1)
  - Container diagram (Level 2)
  - Component diagram (Level 3)
  - Integration with existing rules

- [ ] **sa_api.mdc** - API Documentation Generator
  - Extract API definitions from activities
  - Generate OpenAPI/Swagger spec
  - Include request/response examples

## 🎯 Quick Wins
- [ ] Create comprehensive test cases
- [ ] Add error handling examples
- [ ] Improve architect-template README
- [ ] Create video tutorials
- [ ] Add Thai language documentation

## 💡 Future Ideas
- [ ] Integration with draw.io
- [ ] Export to Visio format
- [ ] AI-powered activity suggestions
- [ ] Automated process optimization
- [ ] Real-time collaboration support

## 📝 Notes
- Focus on maintaining backward compatibility
- Keep rules modular and composable
- Prioritize user experience and clear error messages
- Support both Thai and English content seamlessly

---
Last Updated: ${new Date().toISOString().split('T')[0]}
