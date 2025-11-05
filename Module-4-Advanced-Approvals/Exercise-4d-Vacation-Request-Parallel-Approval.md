# Exercise 4d: Vacation Request Parallel Approval Flow

## 🎯 วัตถุประสงค์

ใน Exercise นี้ คุณจะได้เรียนรู้การ:
- สร้าง Approval Flow ที่ใช้ Microsoft Forms เป็น Trigger
- ใช้ "Get response details" เพื่อดึงข้อมูลจากฟอร์ม
- สร้างรายการใน SharePoint List และตั้งค่า Approval Status
- ใช้ **Parallel Branches** เพื่อสร้าง Approval พร้อมกันหลายคน
- ใช้ **Parallel Approval** (All must approve) สำหรับการอนุมัติพร้อมกัน
- ใช้ "Start and wait for an approval" พร้อม Dynamic Content ในแต่ละ Branch
- ใช้ Condition เพื่อตรวจสอบผลลัพธ์การอนุมัติ
- ใช้ "For each" Loop เพื่อจัดการ Responses
- อัพเดท SharePoint Item ตามผลลัพธ์การอนุมัติ

> 💡 **หมายเหตุ**: Exercise นี้แตกต่างจาก Exercise 4c ตรงที่ใช้ **Parallel Approval**:
> - การอนุมัติจะเกิดขึ้นพร้อมกัน (ทุกคนได้รับ Approval Request พร้อมกัน)
> - ต้องอนุมัติทั้งหมด (All must approve) หรือใครตอบก่อนใช้ได้ (First to respond)
> - ใช้ Parallel Branches เพื่อแยก Approval แต่ละคน
> - **สำคัญ**: การสร้าง Parallel Branch ต้องคลิกขวาที่เครื่องหมาย + ไม่ใช่คลิกซ้าย

---

## 📋 สิ่งที่ต้องเตรียม

- Power Automate Account (ต้องมี License สำหรับ Approvals)
- Microsoft Forms Account
- SharePoint Site (สำหรับสร้าง List)
- Email Address อย่างน้อย 2 อันสำหรับทดสอบ (สำหรับ Parallel Approval)

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
3. ตั้งชื่อ Flow: `Exercise 4d - Vacation Request Parallel Approval`
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

### ขั้นตอนที่ 6: สร้าง Parallel Branches สำหรับ Approval

> ⚠️ **สำคัญ**: การสร้าง Parallel Branch ต้องคลิกขวาที่เครื่องหมาย + ไม่ใช่คลิกซ้าย!

1. **คลิกที่ Action "Create item"** (เพื่อเลือก Action นี้)
2. **หาตำแหน่งเครื่องหมาย + ที่อยู่ด้านล่าง "Create item"**
3. **คลิกขวาที่เครื่องหมาย +** (ไม่ใช่คลิกซ้าย!)
4. จะเห็นเมนูแสดงขึ้นมา → เลือก **Add a parallel branch**

> 💡 **คำอธิบาย**: 
> - การคลิกขวาที่เครื่องหมาย + จะสร้าง Parallel Branch ที่ทำงานพร้อมกัน
> - ไม่ใช่ Sequential (ทีละขั้นตอน) แต่ทำงานพร้อมกัน

#### Branch 1: Manager 1 Approval

1. ใน Branch แรก (Branch 1):
   - คลิก **Add an action**
   - ค้นหาและเลือก: **Start and wait for an approval**
   - ตั้งค่าในแท็บ **Parameters**:

     **Approval type***:
     - เลือก: `Approve/Reject - All must approve`
     - หรือเลือก `Approve/Reject - First to respond` (ขึ้นอยู่กับความต้องการ)

     **Title***:
     - พิมพ์: `Request for vacation approval - Manager 1`

     **Assigned to***:
     - ใส่ Email address ของผู้อนุมัติคนแรก (เช่น: `somchai@msthai.onmicrosoft.com`)
     - หรือ Email ของคุณเองสำหรับทดสอบ

     **Details**:
     - ใช้ Dynamic Content เพื่อสร้างข้อความ:
       ```
       [Title] has requested a vacation from [Vacation Start Date] to [Vacation End Date] for the following reason: [Reason for you vacation]
       
       Do you want to approve?
       ```
     - คลิกที่ Dynamic Content หรือพิมพ์ `@` เพื่อเลือก:
       - **Title** (Your Email Address) - จาก "Get response details"
       - **Vacation Start Date** - จาก "Get response details"
       - **Vacation End Date** - จาก "Get response details"
       - **Reason for you vacation** - จาก "Get response details"

#### Branch 2: Manager 2 Approval

1. ใน Branch ที่สอง (Branch 2):
   - คลิก **Add an action**
   - ค้นหาและเลือก: **Start and wait for an approval**
   - ตั้งค่าในแท็บ **Parameters**:

     **Approval type***:
     - เลือก: `Approve/Reject - All must approve`
     - หรือเลือก `Approve/Reject - First to respond` (ขึ้นอยู่กับความต้องการ)

     **Title***:
     - พิมพ์: `Request for vacation approval - Manager 2`

     **Assigned to***:
     - ใส่ Email address ของผู้อนุมัติคนที่สอง (เช่น: `phateep@msthai.onmicrosoft.com`)
     - หรือ Email ที่สองของคุณสำหรับทดสอบ

     **Details**:
     - ใช้ Dynamic Content เพื่อสร้างข้อความ:
       ```
       [Title] has requested a vacation from [Vacation Start Date] to [Vacation End Date] for the following reason: [Reason for you vacation]
       
       Do you want to approve?
       ```
     - คลิกที่ Dynamic Content หรือพิมพ์ `@` เพื่อเลือก:
       - **Title** (Your Email Address) - จาก "Get response details"
       - **Vacation Start Date** - จาก "Get response details"
       - **Vacation End Date** - จาก "Get response details"
       - **Reason for you vacation** - จาก "Get response details"

> 💡 **คำอธิบาย Parallel Approval**:
> - **Branch 1 และ Branch 2 จะทำงานพร้อมกัน** (ไม่ต้องรอกัน)
> - ผู้อนุมัติทั้งสองคนจะได้รับ Approval Request พร้อมกัน
> - ถ้าใช้ "All must approve": ต้องอนุมัติทั้งสองคน
> - ถ้าใช้ "First to respond": ใครตอบก่อนใช้ได้
> - ถ้าคนใดคนหนึ่งปฏิเสธ Flow อาจจะไปที่ False branch (ขึ้นอยู่กับ Logic)

### ขั้นตอนที่ 7: เพิ่ม Condition เพื่อตรวจสอบผลลัพธ์

> 💡 **หมายเหตุ**: หลังจาก Parallel Branches เสร็จ เราต้องตรวจสอบว่าทั้งสอง Branch อนุมัติหรือไม่

1. **คลิกที่เครื่องหมาย + ที่อยู่ด้านล่าง Parallel Branches** (ตรงกลางระหว่าง Branch 1 และ Branch 2)
2. ค้นหาและเลือก: **Condition**
3. ตั้งค่าในแท็บ **Parameters**:

   **Condition expression***:
   - **Field 1**: เลือก **Outcome** จาก Dynamic Content (จาก "Start and wait for an approval" ใน Branch 1)
   - **Operator**: เลือก `is equal to`
   - **Field 2**: พิมพ์ `Approve`
   - คลิก **+ Add row** เพื่อเพิ่มเงื่อนไขที่สอง
   - **Field 1 (Row 2)**: เลือก **Outcome** จาก Dynamic Content (จาก "Start and wait for an approval" ใน Branch 2)
   - **Operator (Row 2)**: เลือก `is equal to`
   - **Field 2 (Row 2)**: พิมพ์ `Approve`
   - **Logical Operator**: เลือก `And` (ทั้งสองต้องอนุมัติ)

   > 💡 **หมายเหตุ**: 
   > - Condition จะตรวจสอบว่า Branch 1 และ Branch 2 อนุมัติทั้งคู่หรือไม่
   > - ถ้าใช่ → ไปที่ True branch
   > - ถ้าไม่ใช่ (คนใดคนหนึ่งปฏิเสธ) → ไปที่ False branch

### ขั้นตอนที่ 8: True Branch - Update Item (Approved)

เมื่อทั้งสอง Branch อนุมัติแล้ว:

1. ในช่อง **If yes** (True branch):
2. คลิก **Add an action**
3. ค้นหาและเลือก: **Update item**
4. ตั้งค่า:
   - **Site Address***: เลือก SharePoint Site เดียวกับที่ใช้ใน "Create item"
   - **List Name***: เลือก `Vacation Requests`
   - **Id***: เลือก **ID** จาก Dynamic Content (จาก "Create item" Action)
   - **Advanced parameters** (คลิก **Show all**):
     - **Approval status**: พิมพ์ `Approved`

> 💡 **คำอธิบาย**: 
> - Update Item จะอัพเดท Approval status เป็น "Approved"
> - เมื่อทั้งสอง Branch อนุมัติแล้ว Flow จะไปที่ True branch

### ขั้นตอนที่ 9: False Branch - Update Item (Rejected)

เมื่อคนใดคนหนึ่งปฏิเสธ:

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
> - เมื่อถูกปฏิเสธใน Branch ใด Branch หนึ่ง Approval status จะเปลี่ยนเป็น "Rejected"
> - Flow จะไม่ดำเนินการต่อ

### ขั้นตอนที่ 10: บันทึกและทดสอบ Flow

1. คลิก **Save** ที่มุมบนขวา
2. ไปที่ **Microsoft Forms** → ส่งฟอร์ม "Vacation Requests"
3. ตรวจสอบ Email ของผู้อนุมัติทั้งสองคน (Manager 1 และ Manager 2) สำหรับ Approval Request
   - **หมายเหตุ**: ทั้งสองคนจะได้รับ Email พร้อมกัน (Parallel)
4. อนุมัติหรือปฏิเสธใน Email หรือใน Power Automate Portal
5. ตรวจสอบ SharePoint List ว่า Approval status ถูกอัพเดทถูกต้อง

> 💡 **การทดสอบ**:
> - **กรณีที่ 1**: อนุมัติทั้งสองคน → ควรได้ Approval status = "Approved"
> - **กรณีที่ 2**: ปฏิเสธคนใดคนหนึ่ง → ควรได้ Approval status = "Rejected"
> - **กรณีที่ 3**: ถ้าใช้ "First to respond": ใครตอบก่อนใช้ได้ → ควรได้ Approval status = "Approved" (ถ้าอนุมัติ)

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
└──────┬───────────────────┬───────┘
       │                   │
       │ (Parallel)        │ (Parallel)
       ▼                   ▼
┌──────────────────┐ ┌──────────────────┐
│ Start and wait   │ │ Start and wait    │
│ for an approval  │ │ for an approval   │
│ (Manager 1)      │ │ (Manager 2)       │
│ - Type: All must │ │ - Type: All must  │
│   approve        │ │   approve         │
│ - Assigned to:   │ │ - Assigned to:    │
│   [Manager 1]    │ │   [Manager 2]     │
│ - Details:       │ │ - Details:        │
│   [Dynamic]      │ │   [Dynamic]       │
└──────┬───────────┘ └──────────┬───────┘
       │                         │
       └──────────┬──────────────┘
                  │
                  ▼
┌─────────────────────────────────┐
│ Condition                        │
│ - Outcome (Branch 1) =          │
│   "Approve" AND                 │
│ - Outcome (Branch 2) =          │
│   "Approve"?                     │
└──────┬───────────────────┬───────┘
       │                   │
   Yes │                   │ No
       ▼                   ▼
┌──────────────────┐ ┌──────────────────┐
│ Update item      │ │ Update item 1    │
│ - Approval       │ │ - Approval       │
│   status:        │ │   status:        │
│   "Approved"      │ │   "Rejected"     │
└──────────────────┘ └──────────────────┘
```

---

## 🎓 สิ่งที่ได้เรียนรู้

✅ **Forms Integration**: ใช้ Microsoft Forms เป็น Trigger  
✅ **SharePoint Integration**: สร้างและอัพเดทรายการใน SharePoint List  
✅ **Get Response Details**: ดึงข้อมูลรายละเอียดจากฟอร์ม  
✅ **Parallel Branches**: ใช้ Parallel Branches เพื่อสร้าง Approval พร้อมกัน  
✅ **Parallel Approval**: ใช้ Parallel Approval สำหรับการอนุมัติพร้อมกันหลายคน  
✅ **Dynamic Content in Approval**: ใช้ Dynamic Content ใน Approval Details  
✅ **Condition Logic**: ใช้ Condition เพื่อตรวจสอบผลลัพธ์จากหลาย Branch  
✅ **Status Management**: จัดการ Approval Status ตามผลลัพธ์  

---

## 💡 Tips & Tricks

1. **การสร้าง Parallel Branch**:
   - **สำคัญ**: ต้องคลิกขวาที่เครื่องหมาย + ไม่ใช่คลิกซ้าย
   - คลิกขวา → เลือก "Add a parallel branch"
   - Parallel Branches จะทำงานพร้อมกัน (ไม่ต้องรอกัน)

2. **สำหรับการทดสอบ**: 
   - ใช้ Email ของตัวเอง 2 อันหรือ Email ของเพื่อนร่วมงาน
   - ส่งฟอร์มหลายครั้งเพื่อทดสอบทั้งกรณี Approve และ Reject
   - ทดสอบทั้งกรณีที่อนุมัติทั้งสองคนและปฏิเสธคนใดคนหนึ่ง

3. **Parallel Approval vs Sequential Approval**:
   - **Parallel**: อนุมัติพร้อมกัน (ทุกคนได้รับพร้อมกัน)
   - **Sequential**: อนุมัติตามลำดับ (Step 1 → Step 2 → ...)

4. **Approval Types**:
   - **All must approve**: ต้องอนุมัติทั้งหมด (ใช้ใน Parallel Branch)
   - **First to respond**: ใครตอบก่อนใช้ได้ (ใช้ใน Parallel Branch)
   - **Sequential**: อนุมัติตามลำดับ (ใช้ใน Sequential Approval)

5. **SharePoint List Columns**:
   - ตรวจสอบให้แน่ใจว่า Column Names ตรงกับที่ใช้ใน Flow
   - ใช้ Choice Type สำหรับ Approval status เพื่อจำกัดตัวเลือก

6. **Dynamic Content**:
   - ใช้ Dynamic Content จาก "Get response details" ใน Approval Details
   - ทำให้ข้อความอ่านง่ายและมีข้อมูลครบถ้วน

7. **Condition Logic**:
   - ใช้ And เพื่อตรวจสอบว่าทั้งสอง Branch อนุมัติ
   - ใช้ Or ถ้าต้องการให้ใครคนใดคนหนึ่งอนุมัติก็ได้

8. **Error Handling**:
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
- ส่ง Email แจ้งเตือน Manager ทั้งสองคนเมื่อถึงขั้นตอนการอนุมัติ
- รวม Link ไปยัง SharePoint Item

### แบบฝึกหัด C: เพิ่ม 3-Person Parallel Approval

แก้ไข Flow เพื่อ:
- เพิ่ม Branch ที่ 3 (เช่น CFO Approval)
- ใช้ Condition เพื่อตรวจสอบว่าทั้ง 3 คนอนุมัติ

### แบบฝึกหัด D: ใช้ First to Respond

แก้ไข Flow เพื่อ:
- เปลี่ยนจาก "All must approve" เป็น "First to respond"
- ปรับ Condition Logic ให้ใครคนใดคนหนึ่งอนุมัติก็ได้

### แบบฝึกหัด E: เพิ่ม Validation

แก้ไข Flow เพื่อ:
- ตรวจสอบว่า Vacation End Date ต้องหลัง Vacation Start Date
- ถ้าไม่ถูกต้อง → ส่งข้อความแจ้งเตือนและไม่สร้าง Item

---

## ❓ คำถามที่พบบ่อย (FAQ)

**Q: ต้องใช้ License พิเศษสำหรับ Approvals หรือไม่?**  
A: ใช่ ต้องมี Power Automate Premium License

**Q: Parallel Approval แตกต่างจาก Sequential Approval อย่างไร?**  
A: 
- **Parallel**: อนุมัติพร้อมกัน (ทุกคนได้รับพร้อมกัน)
- **Sequential**: อนุมัติตามลำดับ (Step 1 → Step 2 → ...)

**Q: ทำไมต้องคลิกขวาที่เครื่องหมาย +?**  
A: การคลิกขวาจะสร้าง Parallel Branch ที่ทำงานพร้อมกัน ส่วนการคลิกซ้ายจะสร้าง Sequential Step

**Q: ถ้าปฏิเสธคนใดคนหนึ่ง Flow จะทำอย่างไร?**  
A: ขึ้นอยู่กับ Condition Logic ถ้าใช้ And และคนใดคนหนึ่งปฏิเสธ Flow จะไปที่ False branch

**Q: สามารถเพิ่มมากกว่า 2 Parallel Branches ได้หรือไม่?**  
A: ได้ โดยคลิกขวาที่เครื่องหมาย + เพื่อเพิ่ม Branch ที่ 3, 4, ... ตามต้องการ

**Q: ต้องสร้าง SharePoint List ก่อนหรือไม่?**  
A: ใช่ ต้องสร้าง List และ Columns ก่อนสร้าง Flow

**Q: Dynamic Content ไม่แสดงใน Approval Details ทำอย่างไร?**  
A: ตรวจสอบว่าใช้ Dynamic Content จาก "Get response details" Action ไม่ใช่จาก Trigger

**Q: "All must approve" กับ "First to respond" ต่างกันอย่างไร?**  
A: 
- **All must approve**: ต้องอนุมัติทั้งหมด
- **First to respond**: ใครตอบก่อนใช้ได้

---

## 📝 สรุป

ใน Exercise นี้ คุณได้เรียนรู้:
- การสร้าง Approval Flow ที่เริ่มจาก Microsoft Forms
- การเก็บข้อมูลใน SharePoint List
- การใช้ **Parallel Branches** เพื่อสร้าง Approval พร้อมกัน
- การใช้ **Parallel Approval** สำหรับการอนุมัติพร้อมกันหลายคน
- **การคลิกขวาที่เครื่องหมาย +** เพื่อสร้าง Parallel Branch
- การใช้ Dynamic Content ใน Approval Details
- การจัดการผลลัพธ์การอนุมัติด้วย Condition
- การอัพเดท Status ตามผลลัพธ์

Flow นี้เป็นตัวอย่างที่ดีสำหรับการสร้างระบบอนุมัติแบบ Parallel ที่เชื่อมต่อกับ Forms และ SharePoint! 🎉

