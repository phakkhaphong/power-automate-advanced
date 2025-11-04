# บทที่ 6: Parent–Child Flows

## 🎯 ภาพรวม

บทนี้จะเรียนรู้เกี่ยวกับ **Parent–Child Flows** (ฟล็อว์แม่-ลูก) เพื่อนำกลับมาใช้ซ้ำได้

**สิ่งที่คุณจะได้เรียนรู้**:
- ✅ Child Flow Concept (ฟล็อว์ที่นำกลับมาใช้ซ้ำได้)
- ✅ HTTP Request/Response (การสื่อสารระหว่างฟล็อว์)
- ✅ Solutions (การจัดการและ Deploy ฟล็อว์)

> 💡 **คิดง่ายๆ**: บทนี้สอนให้สร้าง Flow ที่ "นำกลับมาใช้ซ้ำ" ได้ เหมือนฟังก์ชันในโปรแกรม

---

## เนื้อหาการเรียนรู้

### 1. Concept: Child Flow & Reuse

#### Child Flow คืออะไร?
- **Definition**: Flow ที่ถูกสร้างขึ้นเพื่อให้ Flow อื่นเรียกใช้
- **Purpose**: สร้าง Logic ที่สามารถนำกลับมาใช้ซ้ำได้
- **Benefits**: 
  - Code Reusability
  - Maintenance Efficiency
  - Consistency
  - Modularity

#### When to Use Child Flow?
- **Reusable Logic**: Logic ที่ใช้ซ้ำในหลาย Flow
- **Complex Operations**: Operations ที่ซับซ้อนและต้องการแยกออกมา
- **Standard Processes**: กระบวนการมาตรฐานที่ใช้ทั่วทั้งองค์กร
- **Shared Utilities**: ฟังก์ชัน Utility ที่ใช้ร่วมกัน

#### Child Flow Structure
```
Child Flow:
  Input: Parameters (รับข้อมูลจาก Parent)
  Processing: Logic ที่ต้องการ Reuse
  Output: Return Values (ส่งข้อมูลกลับไป Parent)
```

---

### 2. Communication: HTTP Request/Response

#### Child Flow as HTTP Endpoint
- **Trigger**: ใช้ "When an HTTP request is received" Trigger
- **Request Body**: รับ Input Parameters จาก Parent Flow
- **Response**: ส่ง Output กลับไปยัง Parent Flow

#### HTTP Request Trigger
- **Method**: GET, POST, PUT, DELETE
- **Request Body JSON Schema**: กำหนด Schema ของ Input
- **Response**: ใช้ "Response" Action เพื่อส่งข้อมูลกลับ

#### HTTP Request Action (Parent Flow)
- **Method**: POST (สำหรับการเรียก Child Flow)
- **URI**: URL ของ Child Flow
- **Body**: ส่ง Input Parameters
- **Authentication**: ใช้ Managed Identity หรือ Connection

---

### 3. ใช้ Solutions เพื่อ Deployment และ Sharing

#### Solutions คืออะไร?
- **Definition**: Container สำหรับ Flow และ Related Resources
- **Purpose**: จัดการ, Deploy, และ Share Flow ระหว่าง Environments
- **Benefits**:
  - Version Control
  - Environment Management
  - Dependency Management
  - Security

#### Solution Types
- **Managed Solution**: Solution ที่ถูก Deploy และไม่สามารถแก้ไขได้
- **Unmanaged Solution**: Solution ที่สามารถแก้ไขได้

#### Solution Components
- **Flows**: Parent และ Child Flows
- **Connections**: API Connections
- **Variables**: Environment Variables
- **Custom Connectors**: Custom API Connectors

#### Solution Deployment Process
1. **Create Solution**: สร้าง Solution ใน Source Environment
2. **Add Components**: เพิ่ม Flows และ Resources
3. **Export Solution**: Export เป็น .zip File
4. **Import Solution**: Import ไปยัง Target Environment
5. **Configure**: ตั้งค่า Connections และ Variables

---

## 📝 Lab Exercise

### Lab 4: Parent–Child Flow สำหรับ Reuse

**Exercise นี้อยู่ในไฟล์แยก**: [Exercise-4-Parent-Child-Flow.md](./Exercise-4-Parent-Child-Flow.md)

**สิ่งที่คุณจะได้ทำ**:
1. ✅ สร้าง Child Flow ที่สามารถนำกลับมาใช้ซ้ำได้
2. ✅ สร้าง Parent Flow ที่เรียกใช้ Child Flow
3. ✅ ส่งข้อมูลระหว่าง Parent และ Child Flow
4. ✅ ใช้ HTTP Request/Response สำหรับ Communication

> 📖 **คำแนะนำ**: เปิดไฟล์ Exercise เพื่อดูขั้นตอนการทำแบบละเอียด พร้อมคำอธิบายทุกขั้นตอน

---

## Advanced Patterns

### Pattern 1: Multiple Child Flows
```
Parent Flow:
  - Call Child Flow 1: Validate Data
  - Call Child Flow 2: Process Data
  - Call Child Flow 3: Send Notification
```

### Pattern 2: Chained Child Flows
```
Parent Flow:
  - Call Child Flow A
  - Use Response from A to Call Child Flow B
  - Use Response from B to Call Child Flow C
```

### Pattern 3: Conditional Child Flow
```
Parent Flow:
  Condition: If condition = true
    - Call Child Flow A
  Else
    - Call Child Flow B
```

### Pattern 4: Parallel Child Flows
```
Parent Flow:
  Parallel:
    - Call Child Flow 1
    - Call Child Flow 2
    - Call Child Flow 3
  Then: Combine Results
```

---

## Best Practices

### 1. Child Flow Design
- **Single Responsibility**: Child Flow ควรทำหน้าที่เดียว
- **Clear Input/Output**: กำหนด Input และ Output ให้ชัดเจน
- **Error Handling**: จัดการ Error ใน Child Flow
- **Documentation**: เอกสารประกอบ Child Flow

### 2. Naming Conventions
- **Child Flow**: ใช้ชื่อที่บอกหน้าที่ชัดเจน
- **Parameters**: ใช้ชื่อที่เข้าใจง่าย
- **Variables**: ใช้ Consistent Naming

### 3. Solution Management
- **Version Control**: ใช้ Version Numbers
- **Environment Variables**: ใช้ Environment Variables สำหรับ Configuration
- **Dependencies**: จัดการ Dependencies อย่างระมัดระวัง

### 4. Performance
- **Minimize Calls**: ลดจำนวน Child Flow Calls
- **Parallel Processing**: ใช้ Parallel เมื่อเป็นไปได้
- **Caching**: Cache Results เมื่อเหมาะสม

---

## การจัดการ Errors ใน Child Flow

### Error Handling Pattern
```
Child Flow:
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

### Parent Flow Error Handling
```
Parent Flow:
  Call Child Flow
  Parse Response
  Condition: If success = false
    - Handle Error
    - Log Error
    - Send Notification
```

---

## สรุปบทเรียน

หลังจากจบบทนี้ คุณจะสามารถ:
- ✅ เข้าใจ Concept ของ Child Flow และการ Reuse
- ✅ สร้าง Child Flow ที่ใช้ HTTP Request/Response
- ✅ สร้าง Parent Flow ที่เรียกใช้ Child Flow
- ✅ ใช้ Solutions เพื่อ Deployment และ Sharing
- ✅ จัดการ Dependencies และ Environment Variables
- ✅ สร้าง Modular และ Reusable Flows

---

## แบบฝึกหัดเพิ่มเติม

1. สร้าง Child Flow สำหรับการ Validate Email Format
2. สร้าง Child Flow สำหรับการ Calculate Discount
3. สร้าง Parent Flow ที่เรียกใช้ Child Flow หลายตัว
4. สร้าง Solution ที่ประกอบด้วย Parent และ Child Flows
5. Deploy Solution ไปยัง Environment อื่น

