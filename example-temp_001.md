สร้าง BPMN 2.0 Diagram ใน XML format พร้อม BPMN DI (Diagram Interchange) ดังนี้:

1. **Pool**:
   - มี 1 Pool ชื่อ "<ชื่อกระบวนการ>"

2. **Lanes**:
   - เพิ่ม Lane ตาม Roles ที่ให้มา
   - ตั้งค่า `isHorizontal="true"`
   - จัดตำแหน่ง `<dc:Bounds>` ของ Lane ให้แยกตามแนวนอน (เพิ่มค่า y เช่น 0, 120, 240...)

3. **Tasks / Events**:
   - สร้าง `<bpmn:startEvent>` และ `<bpmn:endEvent>`
   - ใส่ Task ตามขั้นตอนที่ให้มา
   - จัด `<bpmndi:BPMNShape>` ให้ตำแหน่ง x ไล่จากซ้ายไปขวาในแต่ละ Lane
   - ใส่ `<bpmn:documentation>` ใน Task เพื่อบันทึกข้อมูล RACI (เช่น "R/A", "I")

4. **Sequence Flows**:
   - เชื่อม Start → Task1 → Task2 → ... → End
   - ใส่ `<bpmndi:BPMNEdge>` พร้อม `di:waypoint` ให้ทุกเส้น

5. **Output**:
   - ไฟล์ `.bpmn` ที่สามารถ import เข้า [https://bpmn.io](https://bpmn.io) แล้วเห็นเป็น Lane แยกชัดเจน
   - ไม่ใช้ placeholder graphic — ต้องมี BPMN DI ครบ

คำจำกัดความของแต่ละบทบาท:
ㆍR - Responsible (ผู้รับผิดชอบ): คือผู้ที่ลงมือทำงานนั้นๆ ให้สำเร็จ
ㆍA - Accountable (ผู้มีอามาจตัดสิน ใจ/ฝรับผิดชอบสด): คือผู้ที่เป็นเจ้าของงาน และเป็นผู้ที่ต้องรับผิดชอบผลลัพธ์สุดท้ายของงานนั้น (ในแต่ละกิจกรรม ควรมี A เพียงคนเดียว)
ㆍC - Consulted (ผู้ใช้ให้คำปรึกษา): คือผู้ที่ควรจะให้ข้อมูลหรือคำปรึกษาที่จำเป็นก่อนที่จะดีจะดำเนินงาน
ㆍI - Informed (ผู้ที่ต้องรับทราบ): คือผู้ที่ต้องได้รับแจ้งความคืบหน้าหรือผลลัพธ์ของการดำเนินงาน
RACI Chart: กระบวนการอัปโหลดรูปภาพสู่เซิร์ฟเวอร์
---
**Input Example**:
Roles:
- Sales Person AS SALES
- TechServeMobile AS TS
- SalesTeams AS ST
- Survey Platform AS SVP
- User AS USER

Steps (with RACI):
1. Go to visit store (SALES: R/A)
2. Sales person check plan visit store (SALES: R/A, TS: R/A, ST: R/A)
3. Sales checkin to visit store (SALES: R, ST: A, TS: I)
4. Sales survey and check stock (SALES: R, SVP: A)
5. Sales survey action to TakePhoto (SVP: A, TS: R, SVP: I)
6. Submit Survey (SALES: R, SVP: A)
7. Run schedule task every 30 sec (TS: R/A, SALES: I)
7. Find task and start uploading (TS: R/A, SALES: I)
8. Notify User in Application (SVP: R/A, TS: R/A, SALES: I)

---
