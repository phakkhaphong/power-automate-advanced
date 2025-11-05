# Exercise 4a: Basic Approval Flow with Teams Notification

## 🎯 วัตถุประสงค์

ใน Exercise นี้ คุณจะได้เรียนรู้การ:
- สร้าง Approval Flow พื้นฐาน
- ใช้ "Start and wait for an approval" Action
- ส่ง Notification ผลลัพธ์การอนุมัติไปยัง Teams
- ใช้ Dynamic Content จาก Approval Response

---

## 📋 สิ่งที่ต้องเตรียม

- Power Automate Account (ต้องมี License สำหรับ Approvals)
- Microsoft Teams Account
- Email Address สำหรับทดสอบ

---

## 🚀 ขั้นตอนการปฏิบัติ

### ขั้นตอนที่ 1: สร้าง Flow ใหม่

1. เข้าสู่ **Power Automate**
2. คลิก **Create** → เลือก **Automated cloud flow**
3. ตั้งชื่อ Flow: `Exercise 4a - Basic Approval Teams`
4. เลือก Trigger: **Manually trigger a flow**
5. คลิก **Create**

### ขั้นตอนที่ 2: เพิ่ม Approval Action

1. คลิก **+ New step**
2. ค้นหาและเลือก: **Start and wait for an approval**

### ขั้นตอนที่ 3: ตั้งค่า Approval Parameters

ในหน้า **Parameters** ของ "Start and wait for an approval" Action:

1. **Approval type***:
   - เลือก: `Approve/Reject - First to respond`
   - (Dropdown จะแสดงตัวเลือก: Approve/Reject - First to respond, Approve/Reject - All must approve, Custom responses)

2. **Title***:
   - ใส่: `Do you approve?`

3. **Assigned to***:
   - ใส่ Email address ของผู้ที่จะอนุมัติ (เช่น: `phakkhaphong@kt6s.onmicrosoft.com`)
   - หรือเลือกจาก Address Book
   - หมายเหตุ: ใส่ Email ของคุณเองสำหรับทดสอบ

4. **Details**:
   - ใส่: `These the details`
   - (Optional field - สามารถเว้นว่างได้)

5. **Item link**:
   - (Optional) สามารถเว้นว่างได้
   - หรือใส่ Link ไปยังเอกสาร/รายการที่ต้องการอนุมัติ

6. **Item link description**:
   - (Optional) สามารถเว้นว่างได้

### ขั้นตอนที่ 4: ตั้งค่า Advanced Parameters (Optional)

1. คลิก **Show all** ในส่วน Advanced parameters

2. **Enable notifications**:
   - ตั้งค่า: `Yes` (ค่าเริ่มต้น)
   - เปิด/ปิดการแจ้งเตือน

3. **Enable reassignment**:
   - ตั้งค่า: `Yes` (ค่าเริ่มต้น)
   - อนุญาตให้ผู้รับมอบหมายสามารถมอบหมายให้ผู้อื่นได้

### ขั้นตอนที่ 5: เพิ่ม Teams Notification Action

1. คลิก **+ New step** (หลังจาก Approval Action)
2. ค้นหาและเลือก: **Post message in a chat or channel**

### ขั้นตอนที่ 6: ตั้งค่า Teams Message Parameters

ในหน้า **Parameters** ของ "Post message in a chat or channel" Action:

1. **Post as***:
   - เลือก: `Flow bot`
   - (ตัวเลือกอื่นๆ: User, Adaptive card)

2. **Post in***:
   - เลือก: `Chat with Flow bot`
   - (ตัวเลือกอื่นๆ: Channel, Group chat)

3. **Recipient***:
   - ใส่ Email address ของผู้รับ (เช่น: `phakkhaphong@kt6s.onmicrosoft.com`)
   - ควรเป็น Email เดียวกับผู้ที่ส่ง Approval หรือ Email ของคุณสำหรับทดสอบ

4. **Message***:
   - พิมพ์: `The outcome of approval is : `
   - หลังจากข้อความ ให้คลิกที่ Dynamic Content หรือพิมพ์ `@` เพื่อเลือก Dynamic Content
   - เลือก: **Outcome** (จาก "Start and wait for an approval" Action)
   - ผลลัพธ์ควรเป็น: `The outcome of approval is : [Outcome]`

   > 💡 **คำแนะนำ**: 
   > - ใช้ Rich Text Editor เพื่อจัดรูปแบบข้อความ
   > - สามารถใช้ Bold, Italic, หรือ Formatting อื่นๆ ได้
   > - Dynamic Content จะแสดงผลลัพธ์เป็น "Approve" หรือ "Reject"

### ขั้นตอนที่ 7: บันทึกและทดสอบ Flow

1. คลิก **Save** ที่มุมบนขวา
2. คลิก **Test** → เลือก **Manually** → คลิก **Test**
3. คลิก **Run flow** → คลิก **Done**
4. ตรวจสอบ Email สำหรับ Approval Request
5. อนุมัติหรือปฏิเสธใน Email หรือใน Power Automate Portal
6. ตรวจสอบ Teams Message ที่ได้รับผลลัพธ์

---

## 📊 โครงสร้าง Flow

```
┌─────────────────────────────┐
│  Manually trigger a flow    │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│ Start and wait for an       │
│ approval                    │
│ - Approval type:            │
│   Approve/Reject - First    │
│   to respond                │
│ - Title: "Do you approve?"  │
│ - Assigned to: [Email]      │
│ - Details: "These the       │
│   details"                  │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│ Post message in a chat or   │
│ channel                     │
│ - Post as: Flow bot         │
│ - Post in: Chat with        │
│   Flow bot                  │
│ - Recipient: [Email]        │
│ - Message: "The outcome of  │
│   approval is : [Outcome]"  │
└─────────────────────────────┘
```

---

## 🎓 สิ่งที่ได้เรียนรู้

✅ **Basic Approval Flow**: สร้าง Approval Flow พื้นฐาน  
✅ **Approval Configuration**: ตั้งค่า Approval Parameters  
✅ **Teams Integration**: ส่งข้อความไปยัง Teams  
✅ **Dynamic Content**: ใช้ Outcome จาก Approval Response  
✅ **Flow Bot**: ใช้ Flow bot เพื่อส่งข้อความใน Teams  

---

## 💡 Tips & Tricks

1. **สำหรับการทดสอบ**: 
   - ใช้ Email ของตัวเองเป็น Approver และ Recipient
   - ตรวจสอบ Email Inbox สำหรับ Approval Request

2. **Approval Types**:
   - **Approve/Reject - First to respond**: ใครตอบก่อนใช้ได้
   - **Approve/Reject - All must approve**: ต้องอนุมัติทั้งหมด
   - **Custom responses**: กำหนดตัวเลือกเอง

3. **Teams Message Formatting**:
   - ใช้ Rich Text Editor เพื่อจัดรูปแบบข้อความ
   - สามารถเพิ่ม Emoji หรือ Formatting อื่นๆ ได้

4. **Dynamic Content**:
   - Outcome จะมีค่าเป็น "Approve" หรือ "Reject"
   - สามารถใช้ใน Condition เพื่อแบ่งแยกการทำงานได้

---

## 🔄 แบบฝึกหัดเพิ่มเติม

### แบบฝึกหัด A: เพิ่ม Condition ตามผลลัพธ์

แก้ไข Flow เพื่อ:
- ถ้า Outcome = "Approve": ส่งข้อความ "✅ Approval successful!"
- ถ้า Outcome = "Reject": ส่งข้อความ "❌ Approval rejected!"

### แบบฝึกหัด B: ส่งไปยัง Channel แทน Chat

แก้ไข Flow เพื่อ:
- ส่งข้อความไปยัง Teams Channel แทน Chat
- ใช้ "Post in" = "Channel"
- เลือก Channel ที่ต้องการ

---

## ❓ คำถามที่พบบ่อย (FAQ)

**Q: ต้องใช้ License พิเศษสำหรับ Approvals หรือไม่?**  
A: ใช่ ต้องมี Power Automate Premium License

**Q: สามารถอนุมัติผ่าน Mobile ได้หรือไม่?**  
A: ได้ ผ่าน Power Automate Mobile App หรือ Email

**Q: Outcome มีค่าอะไรบ้าง?**  
A: Outcome จะมีค่าเป็น "Approve" หรือ "Reject" ขึ้นอยู่กับการตอบกลับ

**Q: Flow bot คืออะไร?**  
A: Flow bot เป็น Bot ที่ใช้ส่งข้อความใน Teams โดยอัตโนมัติ แทนการส่งจาก User Account

**Q: ทำไมต้องตั้งค่า "Post as" เป็น "Flow bot"?**  
A: เพื่อให้ข้อความแสดงผลจาก Flow bot แทน User Account ทำให้เห็นชัดเจนว่าเป็นการแจ้งเตือนอัตโนมัติ

---

## 📝 สรุป

ใน Exercise นี้ คุณได้เรียนรู้:
- การสร้าง Approval Flow พื้นฐาน
- การตั้งค่า Approval Parameters
- การส่ง Notification ไปยัง Teams
- การใช้ Dynamic Content จาก Approval Response

Flow นี้เป็นพื้นฐานสำหรับการสร้าง Approval Flow ที่ซับซ้อนขึ้นต่อไป! 🎉

