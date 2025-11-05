# บทที่ 6: Parent–Child Flows

## Slide 1: Title
### บทที่ 6: Parent–Child Flows
**สร้าง Flow ที่นำกลับมาใช้ซ้ำได้**

---

## Slide 2: ภาพรวม
บทนี้สอนให้สร้าง Flow ที่ "นำกลับมาใช้ซ้ำ" ได้ เหมือนฟังก์ชันในโปรแกรม

**สิ่งที่คุณจะได้เรียนรู้**:
- Child Flow Concept (ฟล็อว์ที่นำกลับมาใช้ซ้ำได้)
- HTTP Request/Response (การสื่อสารระหว่างฟล็อว์)
- Solutions (การจัดการและ Deploy ฟล็อว์)

---

## Slide 3: Child Flow คืออะไร?
### Definition

**Child Flow** = Flow ที่ถูกสร้างขึ้นเพื่อให้ Flow อื่นเรียกใช้

**Purpose**: สร้าง Logic ที่สามารถนำกลับมาใช้ซ้ำได้

**Benefits**:
- Code Reusability (นำกลับมาใช้ได้)
- Maintenance Efficiency (ดูแลรักษาง่าย)
- Consistency (สม่ำเสมอ)
- Modularity (เป็นโมดูล)

---

## Slide 4: เมื่อไหร่ควรใช้ Child Flow?
### Use Cases

**Reusable Logic**: Logic ที่ใช้ซ้ำในหลาย Flow

**Complex Operations**: Operations ที่ซับซ้อนและต้องการแยกออกมา

**Standard Processes**: กระบวนการมาตรฐานที่ใช้ทั่วทั้งองค์กร

**Shared Utilities**: ฟังก์ชัน Utility ที่ใช้ร่วมกัน

---

## Slide 5: Child Flow Structure
### โครงสร้าง Child Flow

```
Child Flow:
  Input: Parameters (รับข้อมูลจาก Parent)
  Processing: Logic ที่ต้องการ Reuse
  Output: Return Values (ส่งข้อมูลกลับไป Parent)
```

**ตัวอย่าง**:
- Input: Email Address, Subject, Body
- Processing: ส่ง Email
- Output: Message ID, Timestamp

---

## Slide 6: HTTP Request/Response
### Child Flow as HTTP Endpoint

**Trigger**: ใช้ "When an HTTP request is received" Trigger

**Request Body**: รับ Input Parameters จาก Parent Flow

**Response**: ส่ง Output กลับไปยัง Parent Flow

**Method**: GET, POST, PUT, DELETE (ส่วนใหญ่ใช้ POST)

---

## Slide 7: HTTP Request Trigger
### ตั้งค่า Child Flow

**Request Body JSON Schema**: กำหนด Schema ของ Input

**ตัวอย่าง Schema**:
```json
{
  "type": "object",
  "properties": {
    "to": {"type": "string"},
    "subject": {"type": "string"},
    "body": {"type": "string"}
  }
}
```

**Response**: ใช้ "Response" Action เพื่อส่งข้อมูลกลับ

---

## Slide 8: HTTP Request Action (Parent)
### เรียก Child Flow จาก Parent

**Method**: POST (สำหรับการเรียก Child Flow)

**URI**: URL ของ Child Flow

**Body**: ส่ง Input Parameters

**Authentication**: ใช้ Managed Identity หรือ Connection

---

## Slide 9: Solutions
### Solutions คืออะไร?

**Definition**: Container สำหรับ Flow และ Related Resources

**Purpose**: จัดการ, Deploy, และ Share Flow ระหว่าง Environments

**Benefits**:
- Version Control
- Environment Management
- Dependency Management
- Security

---

## Slide 10: Solution Types
### Managed vs Unmanaged

**Managed Solution**:
- Solution ที่ถูก Deploy และไม่สามารถแก้ไขได้
- ใช้สำหรับ Production

**Unmanaged Solution**:
- Solution ที่สามารถแก้ไขได้
- ใช้สำหรับ Development

---

## Slide 11: Solution Components
### Components ใน Solution

**Flows**: Parent และ Child Flows

**Connections**: API Connections

**Variables**: Environment Variables

**Custom Connectors**: Custom API Connectors

---

## Slide 12: Solution Deployment Process
### ขั้นตอนการ Deploy

1. **Create Solution**: สร้าง Solution ใน Source Environment
2. **Add Components**: เพิ่ม Flows และ Resources
3. **Export Solution**: Export เป็น .zip File
4. **Import Solution**: Import ไปยัง Target Environment
5. **Configure**: ตั้งค่า Connections และ Variables

---

## Slide 13: Advanced Patterns: Multiple
### Pattern 1: Multiple Child Flows

```
Parent Flow:
  - Call Child Flow 1: Validate Data
  - Call Child Flow 2: Process Data
  - Call Child Flow 3: Send Notification
```

**ใช้เมื่อ**: ต้องการแยก Logic เป็นหลายส่วน

---

## Slide 14: Advanced Patterns: Chained
### Pattern 2: Chained Child Flows

```
Parent Flow:
  - Call Child Flow A
  - Use Response from A to Call Child Flow B
  - Use Response from B to Call Child Flow C
```

**ใช้เมื่อ**: Output จาก Flow หนึ่งเป็น Input ของ Flow ถัดไป

---

## Slide 15: Advanced Patterns: Conditional
### Pattern 3: Conditional Child Flow

```
Parent Flow:
  Condition: If condition = true
    - Call Child Flow A
  Else
    - Call Child Flow B
```

**ใช้เมื่อ**: ต้องการเรียก Child Flow ตามเงื่อนไข

---

## Slide 16: Advanced Patterns: Parallel
### Pattern 4: Parallel Child Flows

```
Parent Flow:
  Parallel:
    - Call Child Flow 1
    - Call Child Flow 2
    - Call Child Flow 3
  Then: Combine Results
```

**ใช้เมื่อ**: Child Flows ทำงานอิสระจากกัน

---

## Slide 17: Best Practices
### 1. Child Flow Design

- **Single Responsibility**: Child Flow ควรทำหน้าที่เดียว
- **Clear Input/Output**: กำหนด Input และ Output ให้ชัดเจน
- **Error Handling**: จัดการ Error ใน Child Flow
- **Documentation**: เอกสารประกอบ Child Flow

---

## Slide 18: Best Practices (ต่อ)
### 2. Naming Conventions

- **Child Flow**: ใช้ชื่อที่บอกหน้าที่ชัดเจน
- **Parameters**: ใช้ชื่อที่เข้าใจง่าย
- **Variables**: ใช้ Consistent Naming

### 3. Performance

- **Minimize Calls**: ลดจำนวน Child Flow Calls
- **Parallel Processing**: ใช้ Parallel เมื่อเป็นไปได้
- **Caching**: Cache Results เมื่อเหมาะสม

---

## Slide 19: Error Handling
### การจัดการ Errors ใน Child Flow

**Child Flow**:
```
Try:
  - Main Logic
Catch:
  - Return Error Response
  {
    "success": false,
    "error": "Error message",
    "errorCode": "ERROR_CODE"
  }
```

**Parent Flow**: ตรวจสอบ Response และจัดการ Error

---

## Slide 20: Lab Exercise
### Lab 4: Parent–Child Flow

**สิ่งที่คุณจะได้ทำ**:
1. สร้าง Child Flow ที่สามารถนำกลับมาใช้ซ้ำได้
2. สร้าง Parent Flow ที่เรียกใช้ Child Flow
3. ส่งข้อมูลระหว่าง Parent และ Child Flow
4. ใช้ HTTP Request/Response สำหรับ Communication

---

## Slide 21: สรุปบทเรียน
หลังจากจบบทนี้ คุณจะสามารถ:

- ✅ เข้าใจ Concept ของ Child Flow และการ Reuse
- ✅ สร้าง Child Flow ที่ใช้ HTTP Request/Response
- ✅ สร้าง Parent Flow ที่เรียกใช้ Child Flow
- ✅ ใช้ Solutions เพื่อ Deployment และ Sharing

---

## Slide 22: Next Steps
**พร้อมสำหรับบทสุดท้าย!**

**บทที่ 7**: Governance, Security & Monitoring
- Naming Standards
- Environment Management
- Security Roles
- Debug & Optimization

**คำถาม?**




