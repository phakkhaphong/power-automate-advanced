# Exercise 4b: Vacation Request Approval Flow

## 🎯 วัตถุประสงค์

ใน Exercise นี้ คุณจะได้เรียนรู้การ:
- สร้าง Approval Flow ที่ใช้ Microsoft Forms เป็น Trigger
- ใช้ "Get response details" เพื่อดึงข้อมูลจากฟอร์ม
- สร้างรายการใน SharePoint List และตั้งค่า Approval Status
- ใช้ "Start and wait for an approval" พร้อม Dynamic Content
- ใช้ Condition เพื่อตรวจสอบผลลัพธ์การอนุมัติ
- ใช้ "For each" Loop เพื่อจัดการ Responses
- อัพเดท SharePoint Item ตามผลลัพธ์การอนุมัติ

---

## 📋 สิ่งที่ต้องเตรียม

- Power Automate Account (ต้องมี License สำหรับ Approvals)
- Microsoft Forms Account
- SharePoint Site (สำหรับสร้าง List)
- Email Address สำหรับทดสอบ

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
3. ตั้งชื่อ Flow: `Exercise 4b - Vacation Request Approval`
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

### ขั้นตอนที่ 6: Start and Wait for an Approval

1. คลิก **+ New step**
2. ค้นหาและเลือก: **Start and wait for an approval**
3. ตั้งค่าในแท็บ **Parameters**:

   **Approval type***:
   - เลือก: `Approve/Reject - First to respond`

   **Title***:
   - พิมพ์: `Vacation Request`

   **Assigned to***:
   - ใส่ Email address ของผู้ที่ต้องอนุมัติ (เช่น: `phakkhaphong@kt6s.onmicrosoft.com`)
   - หรือ Email ของคุณเองสำหรับทดสอบ

   **Details**:
   - ใช้ Dynamic Content เพื่อสร้างข้อความ:
     ```
     [Title] has requested a vacation from [Vacation Start Date] to [Vacation End Date] for the following reason : [Reason for you vacation]
     
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
   > - ถ้าใช่ → ไปที่ True branch
   > - ถ้าไม่ใช่ → ไปที่ False branch

### ขั้นตอนที่ 8: True Branch - Update Item (Approved)

เมื่อ Outcome = "Approve":

1. ในช่อง **If yes** (True branch):
2. คลิก **Add an action**
3. ค้นหาและเลือก: **For each**
4. ตั้งค่า:
   - **Select an output from previous steps***: เลือก **Responses** จาก Dynamic Content (จาก "Start and wait for an approval")
   - **หมายเหตุ**: Responses เป็น Array ที่มีข้อมูลการอนุมัติ

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
> - For each Loop จะวนรอบ Responses (แม้ว่าจะมี 1 รายการในกรณี First to respond)
> - Update Item จะอัพเดท Approval status เป็น "Approved"
> - Approval Comments จะเก็บความคิดเห็นจากผู้อนุมัติ

### ขั้นตอนที่ 9: False Branch - Update Item (Rejected)

เมื่อ Outcome ≠ "Approve" (คือ "Reject"):

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
> - เมื่อถูกปฏิเสธ Approval status จะเปลี่ยนเป็น "Rejected"
> - ไม่ต้องใช้ For each Loop เพราะมีเพียง 1 รายการ

### ขั้นตอนที่ 10: บันทึกและทดสอบ Flow

1. คลิก **Save** ที่มุมบนขวา
2. ไปที่ **Microsoft Forms** → ส่งฟอร์ม "Vacation Requests"
3. ตรวจสอบ Email สำหรับ Approval Request
4. อนุมัติหรือปฏิเสธใน Email หรือใน Power Automate Portal
5. ตรวจสอบ SharePoint List ว่า Approval status ถูกอัพเดทถูกต้อง

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
│ - Approval type: First to       │
│   respond                       │
│ - Title: "Vacation Request"     │
│ - Assigned to: [Manager Email]  │
│ - Details: [Dynamic Content]    │
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
✅ **Dynamic Content in Approval**: ใช้ Dynamic Content ใน Approval Details  
✅ **Condition Logic**: ใช้ Condition เพื่อแบ่งแยกการทำงานตามผลลัพธ์  
✅ **For each Loop**: ใช้ Loop เพื่อจัดการ Responses  
✅ **Status Management**: จัดการ Approval Status ตามผลลัพธ์  

---

## 💡 Tips & Tricks

1. **สำหรับการทดสอบ**: 
   - ใช้ Email ของตัวเองเป็น Approver
   - ส่งฟอร์มหลายครั้งเพื่อทดสอบทั้งกรณี Approve และ Reject

2. **SharePoint List Columns**:
   - ตรวจสอบให้แน่ใจว่า Column Names ตรงกับที่ใช้ใน Flow
   - ใช้ Choice Type สำหรับ Approval status เพื่อจำกัดตัวเลือก

3. **Dynamic Content**:
   - ใช้ Dynamic Content จาก "Get response details" ใน Approval Details
   - ทำให้ข้อความอ่านง่ายและมีข้อมูลครบถ้วน

4. **For each Loop**:
   - ในกรณี "First to respond" Responses อาจมีเพียง 1 รายการ
   - แต่ Loop ช่วยให้ Flow รองรับหลาย Responses ในอนาคต

5. **Error Handling**:
   - เพิ่ม Configure run after สำหรับกรณีที่ Approval Timeout
   - ใช้ Terminate Action เพื่อจบ Flow เมื่อเกิดข้อผิดพลาด

---

## 🔄 แบบฝึกหัดเพิ่มเติม

### แบบฝึกหัด A: เพิ่ม Email Notification

แก้ไข Flow เพื่อ:
- ส่ง Email แจ้งเตือนผู้ขออนุมัติเมื่อได้รับการอนุมัติ/ปฏิเสธ
- รวมข้อมูลการลาพักผ่อนใน Email

### แบบฝึกหัด B: เพิ่ม Manager Notification

แก้ไข Flow เพื่อ:
- ส่ง Email แจ้งเตือน Manager เมื่อมีคำขอใหม่
- รวม Link ไปยัง SharePoint Item

### แบบฝึกหัด C: เพิ่ม Validation

แก้ไข Flow เพื่อ:
- ตรวจสอบว่า Vacation End Date ต้องหลัง Vacation Start Date
- ถ้าไม่ถูกต้อง → ส่งข้อความแจ้งเตือนและไม่สร้าง Item

---

## ❓ คำถามที่พบบ่อย (FAQ)

**Q: ต้องใช้ License พิเศษสำหรับ Approvals หรือไม่?**  
A: ใช่ ต้องมี Power Automate Premium License

**Q: ทำไมต้องใช้ For each Loop ใน True Branch?**  
A: Responses เป็น Array ดังนั้นต้องใช้ Loop เพื่อเข้าถึงข้อมูลแต่ละรายการ แม้ว่าจะมีเพียง 1 รายการก็ตาม

**Q: สามารถใช้ All must approve แทน First to respond ได้หรือไม่?**  
A: ได้ แต่ต้องปรับ Logic ใน For each Loop เพื่อจัดการหลาย Responses

**Q: ต้องสร้าง SharePoint List ก่อนหรือไม่?**  
A: ใช่ ต้องสร้าง List และ Columns ก่อนสร้าง Flow

**Q: Dynamic Content ไม่แสดงใน Approval Details ทำอย่างไร?**  
A: ตรวจสอบว่าใช้ Dynamic Content จาก "Get response details" Action ไม่ใช่จาก Trigger

---

## 📝 สรุป

ใน Exercise นี้ คุณได้เรียนรู้:
- การสร้าง Approval Flow ที่เริ่มจาก Microsoft Forms
- การเก็บข้อมูลใน SharePoint List
- การใช้ Dynamic Content ใน Approval Details
- การจัดการผลลัพธ์การอนุมัติด้วย Condition
- การอัพเดท Status ตามผลลัพธ์

Flow นี้เป็นตัวอย่างที่ดีสำหรับการสร้างระบบอนุมัติที่เชื่อมต่อกับ Forms และ SharePoint! 🎉

