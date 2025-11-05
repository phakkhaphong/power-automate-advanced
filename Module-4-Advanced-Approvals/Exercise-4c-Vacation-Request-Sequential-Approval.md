# Exercise 4c: Vacation Request Sequential Approval Flow

## 🎯 วัตถุประสงค์

ใน Exercise นี้ คุณจะได้เรียนรู้การ:
- สร้าง Approval Flow ที่ใช้ Microsoft Forms เป็น Trigger
- ใช้ "Get response details" เพื่อดึงข้อมูลจากฟอร์ม
- สร้างรายการใน SharePoint List และตั้งค่า Approval Status
- ใช้ **Sequential Approval** สำหรับการอนุมัติหลายขั้นตอน
- ใช้ "Start and wait for an approval" พร้อม Dynamic Content
- ใช้ Condition เพื่อตรวจสอบผลลัพธ์การอนุมัติ
- ใช้ "For each" Loop เพื่อจัดการ Responses
- อัพเดท SharePoint Item ตามผลลัพธ์การอนุมัติ

> 💡 **หมายเหตุ**: Exercise นี้แตกต่างจาก Exercise 4b ตรงที่ใช้ **Sequential Approval**:
> - การอนุมัติจะเกิดขึ้นตามลำดับ (Step 1 → Step 2 → ...)
> - ต้องอนุมัติทีละขั้นตอน ไม่สามารถข้ามขั้นตอนได้
> - ถ้าขั้นตอนใดถูกปฏิเสธ Flow จะหยุดทันที

---

## 📋 สิ่งที่ต้องเตรียม

- Power Automate Account (ต้องมี License สำหรับ Approvals)
- Microsoft Forms Account
- SharePoint Site (สำหรับสร้าง List)
- Email Address อย่างน้อย 2 อันสำหรับทดสอบ (สำหรับ Sequential Approval)

---

## 🚀 ขั้นตอนการปฏิบัติ

### ขั้นตอนที่ 1: สร้าง Microsoft Form

1. เข้าสู่ **Microsoft Forms** (https://forms.office.com)
2. คลิก **New Form** → ตั้งชื่อฟอร์ม: `Vacation Requests`
3. เพิ่มคำถามต่อไปนี้:

   **1. Your Email Address**
   - Type: **Text**
   - Required: ✅

   **2. Vacation Start Date**
   - Type: **Date**
   - Required: ✅
   - Format: `M/d/yyyy`

   **3. Vacation End Date**
   - Type: **Date**
   - Required: ✅
   - Format: `M/d/yyyy`

   **4. Reason for you vacation**
   - Type: **Text**
   - Required: ✅

4. บันทึกฟอร์มและจดจำ **Form ID** หรือชื่อฟอร์ม

### ขั้นตอนที่ 2: สร้าง SharePoint List

1. เข้าสู่ **SharePoint Site** ของคุณ
2. สร้าง **Custom List** ชื่อ `Vacation Requests`
3. เพิ่ม Columns ต่อไปนี้:
   - **Title** (Text) - ใช้เก็บ Email Address
   - **Vacation Start** (Date and Time)
   - **Vacation End** (Date and Time)
   - **Reasoning** (Multiple lines of text)
   - **Approval status** (Choice)
     - Options: `Approval Pending`, `Approved`, `Rejected`
     - Default: `Approval Pending`

### ขั้นตอนที่ 3: สร้าง Flow ใหม่

1. เข้าสู่ **Power Automate**
2. คลิก **Create** → เลือก **Automated cloud flow**
3. ตั้งชื่อ Flow: `Exercise 4c - Vacation Request Sequential Approval`
4. ค้นหาและเลือก Trigger: **When a new response is submitted**
5. ตั้งค่า:
   - **Form Id**: เลือก `Vacation Requests` (หรือชื่อฟอร์มที่คุณสร้าง)
6. คลิก **Create**

### ขั้นตอนที่ 4: Get Response Details

1. คลิก **+ New step**
2. ค้นหาและเลือก: **Get response details**
3. ตั้งค่า:
   - **Form Id**: เลือก `Vacation Requests` (เหมือน Trigger)
   - **Response Id**: เลือก **Response Id** จาก Dynamic Content (จาก Trigger)

### ขั้นตอนที่ 5: Create Item in SharePoint

1. คลิก **+ New step**
2. ค้นหาและเลือก: **Create item**
3. ตั้งค่าในแท็บ **Parameters**:

   **Site Address***:
   - เลือก SharePoint Site ของคุณ (เช่น: `Power Platform Training - https://kt6s.sharepoint.com/sites/PowerPlatformTraining`)

   **List Name***:
   - เลือก `Vacation Requests`

   **Advanced parameters** (คลิก **Show all**):
   - **Title**: เลือก **Your Email Address** จาก Dynamic Content (จาก "Get response details")
   - **Vacation Start**: เลือก **Vacation Start Date** จาก Dynamic Content
   - **Vacation End**: เลือก **Vacation End Date** จาก Dynamic Content
   - **Reasoning**: เลือก **Reason for you vacation** จาก Dynamic Content
   - **Approval status**: พิมพ์ `Approval Pending` (เป็นข้อความคงที่)

> 💡 **หมายเหตุ**: 
> - Title จะเก็บ Email Address ของผู้ขออนุมัติ
> - Approval status ตั้งเป็น "Approval Pending" เพื่อแสดงว่ารอการอนุมัติ

### ขั้นตอนที่ 6: Start and Wait for an Approval (Sequential)

1. คลิก **+ New step**
2. ค้นหาและเลือก: **Start and wait for an approval**
3. ตั้งค่าในแท็บ **Parameters**:

   **Approval type***:
   - เลือก: `Sequential Approval`
   - **หมายเหตุ**: Sequential Approval หมายถึงการอนุมัติตามลำดับขั้นตอน

   **Title***:
   - พิมพ์: `Request for vacation approval`

   **Approval Steps**:
   - **Assigned To - 1***: 
     - ใส่ Email address ของผู้อนุมัติขั้นตอนแรก (เช่น: `somchai@msthai.onmicrosoft.com`)
     - หรือ Email ของคุณเองสำหรับทดสอบ
   - คลิก **+ Add new item** เพื่อเพิ่มขั้นตอนที่ 2
   - **Assigned To - 2***: 
     - ใส่ Email address ของผู้อนุมัติขั้นตอนที่สอง (เช่น: `phateep@msthai.onmicrosoft.com`)
     - หรือ Email ที่สองของคุณสำหรับทดสอบ
   - **หมายเหตุ**: สามารถเพิ่มขั้นตอนได้มากกว่า 2 โดยคลิก **+ Add new item**

   **Details**:
   - ใช้ Dynamic Content เพื่อสร้างข้อความ:
     ```
     [Title] has requested a vacation from [Vacation Start Date] to [Vacation End Date] for the following reason: [Reason for you vacation]
     
     Do you want to approve?
     ```
   - คลิกที่ Dynamic Content หรือพิมพ์ `@` เพื่อเลือก:
     - **Title** (Your Email Address)
     - **Vacation Start Date**
     - **Vacation End Date**
     - **Reason for you vacation**

   > 💡 **คำแนะนำ**: 
   > - ใช้ Dynamic Content จาก "Get response details" Action
   > - ข้อความจะแสดงข้อมูลครบถ้วนสำหรับการตัดสินใจอนุมัติ
   > - ใน Sequential Approval ผู้อนุมัติแต่ละขั้นตอนจะเห็นข้อความเดียวกัน

   **Advanced parameters** (Optional):
   - **Enable notifications**: `Yes` (ค่าเริ่มต้น)
   - **Enable reassignment**: `Yes` (ค่าเริ่มต้น)

> 💡 **คำอธิบาย Sequential Approval**:
> - **Step 1**: ผู้อนุมัติคนแรก (Assigned To - 1) จะได้รับ Approval Request ก่อน
> - **Step 2**: เมื่อ Step 1 อนุมัติแล้ว ผู้อนุมัติคนที่สอง (Assigned To - 2) จะได้รับ Approval Request
> - **ถ้าปฏิเสธ**: ถ้าขั้นตอนใดถูกปฏิเสธ Flow จะหยุดทันทีและไปที่ False branch
> - **ถ้าอนุมัติทั้งหมด**: เมื่อทุกขั้นตอนอนุมัติแล้ว Flow จะไปที่ True branch

### ขั้นตอนที่ 7: เพิ่ม Condition เพื่อตรวจสอบผลลัพธ์

1. คลิก **+ New step**
2. ค้นหาและเลือก: **Condition**
3. ตั้งค่าในแท็บ **Parameters**:

   **Condition expression***:
   - **Field 1**: เลือก **Outcome** จาก Dynamic Content (จาก "Start and wait for an approval")
   - **Operator**: เลือก `is equal to`
   - **Field 2**: พิมพ์ `Approve`

   > 💡 **หมายเหตุ**: 
   > - Condition จะตรวจสอบว่า Outcome = "Approve" หรือไม่
   > - ใน Sequential Approval Outcome จะเป็น "Approve" เมื่อทุกขั้นตอนอนุมัติแล้ว
   > - ถ้าใช่ → ไปที่ True branch
   > - ถ้าไม่ใช่ → ไปที่ False branch

### ขั้นตอนที่ 8: True Branch - Update Item (Approved)

เมื่อ Outcome = "Approve" (ทุกขั้นตอนอนุมัติแล้ว):

1. ในช่อง **If yes** (True branch):
2. คลิก **Add an action**
3. ค้นหาและเลือก: **For each**
4. ตั้งค่า:
   - **Select an output from previous steps***: เลือก **Responses** จาก Dynamic Content (จาก "Start and wait for an approval")
   - **หมายเหตุ**: Responses เป็น Array ที่มีข้อมูลการอนุมัติจากทุกขั้นตอน

5. ภายใน **For each** Loop:
   - คลิก **Add an action**
   - ค้นหาและเลือก: **Update item**
   - ตั้งค่า:
     - **Site Address***: เลือก SharePoint Site เดียวกับที่ใช้ใน "Create item"
     - **List Name***: เลือก `Vacation Requests`
     - **Id***: เลือก **ID** จาก Dynamic Content (จาก "Create item" Action)
     - **Advanced parameters** (คลิก **Show all**):
       - **Approval status**: พิมพ์ `Approved`
       - **Approval Comments**: เลือก **comments** จาก Dynamic Content (จาก "Start and wait for an approval")

> 💡 **คำอธิบาย**: 
> - For each Loop จะวนรอบ Responses (ซึ่งมีหลายรายการใน Sequential Approval)
> - Update Item จะอัพเดท Approval status เป็น "Approved"
> - Approval Comments จะเก็บความคิดเห็นจากผู้อนุมัติ (อาจมีหลายความคิดเห็นจากหลายขั้นตอน)

### ขั้นตอนที่ 9: False Branch - Update Item (Rejected)

เมื่อ Outcome ≠ "Approve" (ถูกปฏิเสธในขั้นตอนใดขั้นตอนหนึ่ง):

1. ในช่อง **If no** (False branch):
2. คลิก **Add an action**
3. ค้นหาและเลือก: **Update item**
4. ตั้งค่า:
   - **Site Address***: เลือก SharePoint Site เดียวกับที่ใช้ใน "Create item"
   - **List Name***: เลือก `Vacation Requests`
   - **Id***: เลือก **ID** จาก Dynamic Content (จาก "Create item" Action)
   - **Advanced parameters** (คลิก **Show all**):
     - **Approval status**: พิมพ์ `Rejected`

> 💡 **คำอธิบาย**: 
> - เมื่อถูกปฏิเสธในขั้นตอนใดขั้นตอนหนึ่ง Approval status จะเปลี่ยนเป็น "Rejected"
> - ไม่ต้องใช้ For each Loop เพราะเมื่อปฏิเสธ Flow จะหยุดทันที
> - Flow จะไม่ดำเนินการต่อถึงขั้นตอนถัดไป

### ขั้นตอนที่ 10: บันทึกและทดสอบ Flow

1. คลิก **Save** ที่มุมบนขวา
2. ไปที่ **Microsoft Forms** → ส่งฟอร์ม "Vacation Requests"
3. ตรวจสอบ Email ของผู้อนุมัติขั้นตอนแรก (Assigned To - 1) สำหรับ Approval Request
4. อนุมัติใน Email หรือใน Power Automate Portal
5. ตรวจสอบ Email ของผู้อนุมัติขั้นตอนที่สอง (Assigned To - 2) สำหรับ Approval Request
6. อนุมัติหรือปฏิเสธในขั้นตอนที่สอง
7. ตรวจสอบ SharePoint List ว่า Approval status ถูกอัพเดทถูกต้อง

> 💡 **การทดสอบ**:
> - **กรณีที่ 1**: อนุมัติทั้งสองขั้นตอน → ควรได้ Approval status = "Approved"
> - **กรณีที่ 2**: ปฏิเสธในขั้นตอนแรก → ควรได้ Approval status = "Rejected" และไม่มีการส่ง Approval Request ไปยังขั้นตอนที่สอง
> - **กรณีที่ 3**: อนุมัติขั้นตอนแรก แต่ปฏิเสธขั้นตอนที่สอง → ควรได้ Approval status = "Rejected"

---

## 📊 โครงสร้าง Flow

```
┌─────────────────────────────────┐
│ When a new response is          │
│ submitted (Vacation Requests)   │
└──────────────┬──────────────────┘
               │
               ▼
┌─────────────────────────────────┐
│ Get response details             │
│ - Form Id: Vacation Requests     │
│ - Response Id: [Dynamic]         │
└──────────────┬──────────────────┘
               │
               ▼
┌─────────────────────────────────┐
│ Create item (SharePoint)         │
│ - Site: [Your SharePoint Site]   │
│ - List: Vacation Requests        │
│ - Title: [Your Email Address]    │
│ - Vacation Start: [Start Date]   │
│ - Vacation End: [End Date]       │
│ - Reasoning: [Reason]            │
│ - Approval status: "Approval     │
│   Pending"                       │
└──────────────┬──────────────────┘
               │
               ▼
┌─────────────────────────────────┐
│ Start and wait for an approval   │
│ - Approval type: Sequential      │
│   Approval                      │
│ - Title: "Request for vacation  │
│   approval"                     │
│ - Approval Steps:               │
│   Step 1: [Manager 1 Email]     │
│   Step 2: [Manager 2 Email]     │
│ - Details: [Dynamic Content]    │
│                                 │
│ Flow:                           │
│ Step 1 → (Approve) → Step 2 →   │
│ (Approve) → Complete            │
│ Step 1 → (Reject) → Stop        │
└──────────────┬──────────────────┘
               │
               ▼
┌─────────────────────────────────┐
│ Condition                        │
│ - Outcome = "Approve"?           │
└──────┬───────────────────┬───────┘
       │                   │
   Yes │                   │ No
       ▼                   ▼
┌──────────────────┐ ┌──────────────────┐
│ For each         │ │ Update item 1    │
│ (Responses)      │ │ - Approval       │
│                  │ │   status:        │
│ Update item      │ │   "Rejected"     │
│ - Approval       │ └──────────────────┘
│   status:        │
│   "Approved"     │
│ - Approval       │
│   Comments:      │
│   [comments]     │
└──────────────────┘
```

---

## 🎓 สิ่งที่ได้เรียนรู้

✅ **Forms Integration**: ใช้ Microsoft Forms เป็น Trigger  
✅ **SharePoint Integration**: สร้างและอัพเดทรายการใน SharePoint List  
✅ **Get Response Details**: ดึงข้อมูลรายละเอียดจากฟอร์ม  
✅ **Sequential Approval**: ใช้ Sequential Approval สำหรับการอนุมัติหลายขั้นตอน  
✅ **Dynamic Content in Approval**: ใช้ Dynamic Content ใน Approval Details  
✅ **Condition Logic**: ใช้ Condition เพื่อแบ่งแยกการทำงานตามผลลัพธ์  
✅ **For each Loop**: ใช้ Loop เพื่อจัดการ Responses จากหลายขั้นตอน  
✅ **Status Management**: จัดการ Approval Status ตามผลลัพธ์  

---

## 💡 Tips & Tricks

1. **สำหรับการทดสอบ**: 
   - ใช้ Email ของตัวเอง 2 อันหรือ Email ของเพื่อนร่วมงาน
   - ส่งฟอร์มหลายครั้งเพื่อทดสอบทั้งกรณี Approve และ Reject
   - ทดสอบทั้งกรณีที่ปฏิเสธในขั้นตอนแรกและขั้นตอนที่สอง

2. **Sequential Approval vs Parallel Approval**:
   - **Sequential**: อนุมัติตามลำดับ (Step 1 → Step 2 → ...)
   - **Parallel (First to respond)**: ใครตอบก่อนใช้ได้
   - **Parallel (All must approve)**: ต้องอนุมัติทั้งหมดพร้อมกัน

3. **SharePoint List Columns**:
   - ตรวจสอบให้แน่ใจว่า Column Names ตรงกับที่ใช้ใน Flow
   - ใช้ Choice Type สำหรับ Approval status เพื่อจำกัดตัวเลือก

4. **Dynamic Content**:
   - ใช้ Dynamic Content จาก "Get response details" ใน Approval Details
   - ทำให้ข้อความอ่านง่ายและมีข้อมูลครบถ้วน

5. **For each Loop**:
   - ใน Sequential Approval Responses จะมีหลายรายการ (1 รายการต่อ 1 ขั้นตอน)
   - Loop จะวนรอบทุก Response เพื่อดึงข้อมูล

6. **Error Handling**:
   - เพิ่ม Configure run after สำหรับกรณีที่ Approval Timeout
   - ใช้ Terminate Action เพื่อจบ Flow เมื่อเกิดข้อผิดพลาด

---

## 🔄 แบบฝึกหัดเพิ่มเติม

### แบบฝึกหัด A: เพิ่ม Email Notification

แก้ไข Flow เพื่อ:
- ส่ง Email แจ้งเตือนผู้ขออนุมัติเมื่อได้รับการอนุมัติ/ปฏิเสธ
- รวมข้อมูลการลาพักผ่อนและผู้อนุมัติใน Email

### แบบฝึกหัด B: เพิ่ม Manager Notification

แก้ไข Flow เพื่อ:
- ส่ง Email แจ้งเตือน Manager แต่ละคนเมื่อถึงขั้นตอนการอนุมัติของเขา
- รวม Link ไปยัง SharePoint Item

### แบบฝึกหัด C: เพิ่ม 3-Level Approval

แก้ไข Flow เพื่อ:
- เพิ่มขั้นตอนที่ 3 (เช่น CFO Approval)
- ใช้เงื่อนไข: ถ้าจำนวนวันลาน้อยกว่า 5 วัน → 2 ขั้นตอน, มากกว่า 5 วัน → 3 ขั้นตอน

### แบบฝึกหัด D: เพิ่ม Validation

แก้ไข Flow เพื่อ:
- ตรวจสอบว่า Vacation End Date ต้องหลัง Vacation Start Date
- ถ้าไม่ถูกต้อง → ส่งข้อความแจ้งเตือนและไม่สร้าง Item

---

## ❓ คำถามที่พบบ่อย (FAQ)

**Q: ต้องใช้ License พิเศษสำหรับ Approvals หรือไม่?**  
A: ใช่ ต้องมี Power Automate Premium License

**Q: Sequential Approval แตกต่างจาก Parallel Approval อย่างไร?**  
A: 
- **Sequential**: อนุมัติตามลำดับ ขั้นตอนต่อไปจะเริ่มเมื่อขั้นตอนก่อนหน้าอนุมัติแล้ว
- **Parallel**: อนุมัติพร้อมกัน (First to respond หรือ All must approve)

**Q: ถ้าปฏิเสธในขั้นตอนแรก ขั้นตอนถัดไปจะได้รับ Approval Request หรือไม่?**  
A: ไม่ จะไม่ได้รับ เพราะ Flow จะหยุดทันทีเมื่อถูกปฏิเสธ

**Q: ทำไมต้องใช้ For each Loop ใน True Branch?**  
A: Responses เป็น Array ที่มีหลายรายการ (1 รายการต่อ 1 ขั้นตอน) ดังนั้นต้องใช้ Loop เพื่อเข้าถึงข้อมูลแต่ละรายการ

**Q: สามารถเพิ่มมากกว่า 2 ขั้นตอนได้หรือไม่?**  
A: ได้ โดยคลิก **+ Add new item** เพื่อเพิ่มขั้นตอนที่ 3, 4, ... ตามต้องการ

**Q: ต้องสร้าง SharePoint List ก่อนหรือไม่?**  
A: ใช่ ต้องสร้าง List และ Columns ก่อนสร้าง Flow

**Q: Dynamic Content ไม่แสดงใน Approval Details ทำอย่างไร?**  
A: ตรวจสอบว่าใช้ Dynamic Content จาก "Get response details" Action ไม่ใช่จาก Trigger

---

## 📝 สรุป

ใน Exercise นี้ คุณได้เรียนรู้:
- การสร้าง Approval Flow ที่เริ่มจาก Microsoft Forms
- การเก็บข้อมูลใน SharePoint List
- การใช้ **Sequential Approval** สำหรับการอนุมัติหลายขั้นตอน
- การใช้ Dynamic Content ใน Approval Details
- การจัดการผลลัพธ์การอนุมัติด้วย Condition
- การอัพเดท Status ตามผลลัพธ์

Flow นี้เป็นตัวอย่างที่ดีสำหรับการสร้างระบบอนุมัติแบบหลายขั้นตอนที่เชื่อมต่อกับ Forms และ SharePoint! 🎉

