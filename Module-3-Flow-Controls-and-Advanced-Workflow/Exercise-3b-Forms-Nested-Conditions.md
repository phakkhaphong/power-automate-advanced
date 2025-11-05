# Exercise 3b: Forms → Nested Conditions → Email/Calendar

## 🎯 วัตถุประสงค์

ใน Exercise นี้ คุณจะได้เรียนรู้การ:
- สร้าง Flow ที่ใช้ **Microsoft Forms Trigger** ("When a new response is submitted")
- ใช้ **Get response details** เพื่อดึงข้อมูลจากฟอร์ม
- ใช้ **Nested Conditions** (เงื่อนไขซ้อนกัน) เพื่อสร้าง Logic ที่ซับซ้อน
- ส่ง Email เมื่อเงื่อนไขเป็นจริง
- สร้าง Calendar Event เมื่อเงื่อนไขซ้อนเป็นจริง

> 💡 **หมายเหตุ**: Exercise นี้แสดงการใช้งาน **Nested Conditions** (เงื่อนไขซ้อนกัน):
> - Condition หลัก: ตรวจสอบจำนวนเงินบริจาค > 3,000
> - Condition ซ้อน (Condition 1): ตรวจสอบจำนวนเงินบริจาค > 10,000
> - สร้าง Logic ที่ซับซ้อนขึ้นตามเงื่อนไขที่ซ้อนกัน

---

## 📋 สิ่งที่ต้องเตรียม

- Power Automate Account (สามารถใช้ Free Account ได้)
- Microsoft Forms Account (สำหรับสร้างฟอร์ม)
- ความรู้พื้นฐานเรื่อง Flow Creation และการใช้งาน Condition
- Email Account ที่เชื่อมต่อกับ Power Automate
- Teams Account (สำหรับส่งข้อความ)
- Calendar Account (สำหรับสร้าง Event)

---

## 🚀 ขั้นตอนการปฏิบัติ

### ขั้นตอนที่ 1: สร้าง Microsoft Form ("Charity Form")

ก่อนเริ่ม Flow ต้องสร้างฟอร์มก่อน:

1. เข้าสู่ **Microsoft Forms** (https://forms.office.com)
2. คลิก **New Form** เพื่อสร้างฟอร์มใหม่
3. ตั้งชื่อฟอร์ม: `Charity Form`
4. เพิ่มคำถามตามลำดับ:

   **คำถามที่ 1: What's your name?**
   - ประเภท: **Text**
   - ตั้งค่า: Short answer

   **คำถามที่ 2: What's your E-Mail address?**
   - ประเภท: **Text**
   - ตั้งค่า: Short answer

   **คำถามที่ 3: What amount do you plan on donating?**
   - ประเภท: **Number**
   - ตั้งค่า: 
     - Placeholder: `Enter a number between 0 and 1000000. Do not use decimal points or commas. Do not add $.`
     - Validation: Number between 0 and 1000000

   **คำถามที่ 4: What type of donor describes you the best?**
   - ประเภท: **Choice**
   - ตั้งค่า: 
     - Options:
       - `First time donor`
       - `Occasional donor`
       - `All-the-time donor`
     - Allow multiple selections: **No** (Radio button)

5. คลิก **Share** เพื่อเผยแพร่ฟอร์ม (หรือเก็บไว้สำหรับทดสอบ)

> 💡 **หมายเหตุ**: เก็บ Link ของฟอร์มไว้เพื่อทดสอบ Flow ในภายหลัง

### ขั้นตอนที่ 2: สร้าง Flow ใหม่พร้อม Forms Trigger

1. เข้าสู่ **Power Automate** (https://powerautomate.microsoft.com)
2. คลิก **Create** → เลือก **Automated cloud flow**
3. ตั้งชื่อ Flow: `Exercise 3b - Forms Nested Conditions`
4. ค้นหาและเลือก Trigger: **When a new response is submitted**
5. คลิก **Create**

### ขั้นตอนที่ 3: ตั้งค่า Forms Trigger

1. ใน **When a new response is submitted**:
   - **Form Id**: เลือก `Charity Form` (หรือชื่อฟอร์มที่คุณสร้าง)
   - **Connection**: ตรวจสอบว่าเชื่อมต่อกับ Microsoft Forms Account ของคุณแล้ว

> 💡 **หมายเหตุ**: Trigger นี้จะทำงานอัตโนมัติเมื่อมีคนส่งฟอร์ม "Charity Form"

### ขั้นตอนที่ 4: เพิ่ม Get response details

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Get response details**
3. ตั้งค่า:
   - **Form Id**: เลือก `Charity Form` (เหมือน Trigger)
   - **Response Id**: เลือก `Response Id` จาก Dynamic Content (จาก Trigger)

> 💡 **คำอธิบาย**: Get response details จะดึงข้อมูลรายละเอียดทั้งหมดจากฟอร์มที่ส่งมา
> - ข้อมูลที่ได้: ชื่อ, Email, จำนวนเงินบริจาค, ประเภทผู้บริจาค

**ข้อมูลที่สามารถเข้าถึงได้**:
- `What's your name?` → ชื่อผู้บริจาค
- `What's your E-Mail address?` → Email ผู้บริจาค
- `What amount do you plan on donating?` → จำนวนเงินบริจาค (Number)
- `What type of donor describes you the best?` → ประเภทผู้บริจาค

### ขั้นตอนที่ 5: ส่งข้อความแจ้งเตือนไปยัง Teams

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Post message in a chat or channel**
3. ตั้งค่า:
   - **Post as**: `Flow bot`
   - **Post in**: `Chat with Flow bot`
   - **Recipient**: ใส่ Email address ของคุณ (เช่น `your-email@example.com`)
   - **Message**: ใช้ Dynamic Content เพื่อสร้างข้อความ:
     ```
     {What's your name?} has submitted a Charity form.
     ```

> 💡 **คำอธิบาย**: Action นี้จะส่งข้อความแจ้งเตือนไปยัง Teams ทุกครั้งที่มีการส่งฟอร์ม

### ขั้นตอนที่ 6: เพิ่ม Condition หลัก (ตรวจสอบจำนวนเงินบริจาค > 3,000)

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Condition**
3. ตั้งค่า Condition Expression:
   - **Field 1**: คลิก **Expression** (fx) และพิมพ์:
     ```
     int(body('Get_response_details')?['What amount do you plan on donating?'])
     ```
     หรือเลือก `What amount do you plan on donating?` จาก Dynamic Content แล้วคลิก **Expression** เพื่อแปลงเป็น Integer
   - **Operator**: เลือก `is greater than`
   - **Field 2**: พิมพ์ `3000` (เป็น Number)

> 💡 **คำอธิบาย**: 
> - `int()` → แปลงค่าเป็น Integer (จำนวนเต็ม)
> - Condition นี้จะตรวจสอบว่าจำนวนเงินบริจาคมากกว่า 3,000 หรือไม่
> - **True Branch**: จำนวนเงินบริจาค > 3,000 → ส่ง Email "Whale Alerts"
> - **False Branch**: จำนวนเงินบริจาค ≤ 3,000 → ไม่ทำอะไร

**Condition Structure**:
```
Condition: int(Donation Amount) > 3000
  ├─ True Branch: Send Email + Condition 1 (Nested)
  └─ False Branch: (Empty)
```

### ขั้นตอนที่ 7: เพิ่ม Action ใน True Branch (ส่ง Email "Whale Alerts")

1. **ภายใน True Branch** (ด้านซ้าย) คลิก **Add an action**
2. ค้นหาและเลือก **Send an email (V2)**
3. ตั้งค่า:
   - **To**: เลือก `What's your E-Mail address?` จาก Dynamic Content (จาก Get response details)
   - **Subject**: พิมพ์ `Whale Alerts`
   - **Body**: ใช้ Dynamic Content เพื่อสร้างข้อความ:
     ```
     {What's your name?} donated more than 3,000.
     ```

> 💡 **คำอธิบาย**: Action นี้จะส่ง Email "Whale Alerts" เมื่อจำนวนเงินบริจาคมากกว่า 3,000

**ตัวอย่าง Email ที่ส่งออกไป**:
- **To**: Email จากฟอร์ม (เช่น `donor@example.com`)
- **Subject**: `Whale Alerts`
- **Body**: `สมชาย ใจดี donated more than 3,000.`

### ขั้นตอนที่ 8: เพิ่ม Condition ซ้อน (Condition 1) ใน True Branch

1. **ภายใน True Branch** (หลังจาก Send Email) คลิก **Add an action**
2. ค้นหาและเลือก **Condition** (จะสร้าง Condition ซ้อนกัน)
3. ตั้งชื่อ Condition: `Condition 1` (หรือชื่ออื่นตามต้องการ)
4. ตั้งค่า Condition Expression:
   - **Field 1**: คลิก **Expression** (fx) และพิมพ์:
     ```
     int(body('Get_response_details')?['What amount do you plan on donating?'])
     ```
   - **Operator**: เลือก `is greater than`
   - **Field 2**: พิมพ์ `10000` (เป็น Number)

> 💡 **คำอธิบาย**: 
> - Condition นี้จะตรวจสอบว่าจำนวนเงินบริจาคมากกว่า 10,000 หรือไม่
> - **True Branch**: จำนวนเงินบริจาค > 10,000 → สร้าง Calendar Event
> - **False Branch**: จำนวนเงินบริจาค ≤ 10,000 → ไม่ทำอะไร

**Nested Condition Structure**:
```
Condition: int(Donation Amount) > 3000
  ├─ True Branch:
  │   ├─ Send Email (V2) - "Whale Alerts"
  │   └─ Condition 1: int(Donation Amount) > 10000
  │       ├─ True Branch: Create Event (V4)
  │       └─ False Branch: (Empty)
  └─ False Branch: (Empty)
```

### ขั้นตอนที่ 9: เพิ่ม Action ใน True Branch ของ Condition 1 (สร้าง Calendar Event)

1. **ภายใน True Branch ของ Condition 1** (ด้านซ้าย) คลิก **Add an action**
2. ค้นหาและเลือก **Create event (V4)**
3. ตั้งค่า:
   - **Calendar id**: เลือก Calendar ของคุณ (เช่น `ปฏิทิน` หรือ `Calendar`)
   - **Subject**: พิมพ์ `Whale donated above 10,000`
   - **Start time**: คลิก **Expression** (fx) และพิมพ์:
     ```
     addDays(utcNow(), 1)
     ```
     หรือใช้ Dynamic Content: เลือก `utcNow()` แล้วเพิ่ม `addDays()` expression
   - **End time**: คลิก **Expression** (fx) และพิมพ์:
     ```
     addDays(utcNow(), 1)
     ```
     หรือใช้ Dynamic Content และเพิ่ม `addHours()` เพื่อเพิ่มเวลา:
     ```
     addHours(addDays(utcNow(), 1), 1)
     ```
   - **Time zone**: เลือก `(UTC+07:00) Bangkok, Hanoi, Jakarta` (หรือ Time zone ที่คุณต้องการ)

> 💡 **คำอธิบาย**: 
> - `addDays(utcNow(), 1)` → เพิ่มวันจากวันปัจจุบัน 1 วัน (พรุ่งนี้)
> - `addHours(..., 1)` → เพิ่มชั่วโมง 1 ชั่วโมง (สำหรับ End time)
> - Action นี้จะสร้าง Calendar Event เมื่อจำนวนเงินบริจาคมากกว่า 10,000

**ตัวอย่าง Calendar Event**:
- **Subject**: `Whale donated above 10,000`
- **Start time**: พรุ่งนี้ เวลา 00:00
- **End time**: พรุ่งนี้ เวลา 01:00
- **Time zone**: (UTC+07:00) Bangkok, Hanoi, Jakarta

### ขั้นตอนที่ 10: ทดสอบ Flow

#### ทดสอบกรณีที่ 1: จำนวนเงินบริจาค ≤ 3,000 (False Branch)

1. คลิก **Save**
2. เปิดฟอร์ม "Charity Form" ใน Browser
3. กรอกข้อมูล:
   - **What's your name?**: `ทดสอบ 1`
   - **What's your E-Mail address?**: `test1@example.com`
   - **What amount do you plan on donating?**: `2000`
   - **What type of donor describes you the best?**: `First time donor`
4. ส่งฟอร์ม
5. รอให้ Flow รันอัตโนมัติ
6. ตรวจสอบผลลัพธ์:
   - ควรเห็น Teams Message: `ทดสอบ 1 has submitted a Charity form.`
   - ไม่เห็น Email "Whale Alerts"
   - ไม่เห็น Calendar Event

#### ทดสอบกรณีที่ 2: จำนวนเงินบริจาค > 3,000 แต่ ≤ 10,000 (True Branch แต่ Condition 1 = False)

1. เปิดฟอร์ม "Charity Form" อีกครั้ง
2. กรอกข้อมูล:
   - **What's your name?**: `ทดสอบ 2`
   - **What's your E-Mail address?**: `test2@example.com`
   - **What amount do you plan on donating?**: `5000`
   - **What type of donor describes you the best?**: `Occasional donor`
3. ส่งฟอร์ม
4. รอให้ Flow รันอัตโนมัติ
5. ตรวจสอบผลลัพธ์:
   - ควรเห็น Teams Message: `ทดสอบ 2 has submitted a Charity form.`
   - ควรเห็น Email "Whale Alerts": `ทดสอบ 2 donated more than 3,000.`
   - ไม่เห็น Calendar Event

#### ทดสอบกรณีที่ 3: จำนวนเงินบริจาค > 10,000 (True Branch และ Condition 1 = True)

1. เปิดฟอร์ม "Charity Form" อีกครั้ง
2. กรอกข้อมูล:
   - **What's your name?**: `ทดสอบ 3`
   - **What's your E-Mail address?**: `test3@example.com`
   - **What amount do you plan on donating?**: `15000`
   - **What type of donor describes you the best?**: `All-the-time donor`
3. ส่งฟอร์ม
4. รอให้ Flow รันอัตโนมัติ
5. ตรวจสอบผลลัพธ์:
   - ควรเห็น Teams Message: `ทดสอบ 3 has submitted a Charity form.`
   - ควรเห็น Email "Whale Alerts": `ทดสอบ 3 donated more than 3,000.`
   - ควรเห็น Calendar Event: `Whale donated above 10,000` (พรุ่งนี้)

---

## 📊 ผลลัพธ์ที่คาดหวัง

### กรณีที่ 1: จำนวนเงินบริจาค ≤ 3,000

คุณควรจะเห็น:
- ✅ **Teams Message**: `{ชื่อ} has submitted a Charity form.`
- ❌ **ไม่เห็น** Email "Whale Alerts"
- ❌ **ไม่เห็น** Calendar Event

### กรณีที่ 2: จำนวนเงินบริจาค > 3,000 แต่ ≤ 10,000

คุณควรจะเห็น:
- ✅ **Teams Message**: `{ชื่อ} has submitted a Charity form.`
- ✅ **Email "Whale Alerts"**: `{ชื่อ} donated more than 3,000.`
- ❌ **ไม่เห็น** Calendar Event

### กรณีที่ 3: จำนวนเงินบริจาค > 10,000

คุณควรจะเห็น:
- ✅ **Teams Message**: `{ชื่อ} has submitted a Charity form.`
- ✅ **Email "Whale Alerts"**: `{ชื่อ} donated more than 3,000.`
- ✅ **Calendar Event**: `Whale donated above 10,000` (พรุ่งนี้)

---

## 🎓 สิ่งที่ได้เรียนรู้

✅ **Microsoft Forms Trigger**: ใช้ "When a new response is submitted" เพื่อ Trigger Flow อัตโนมัติ  
✅ **Get response details**: ดึงข้อมูลรายละเอียดทั้งหมดจากฟอร์มที่ส่งมา  
✅ **Nested Conditions**: สร้างเงื่อนไขซ้อนกันเพื่อสร้าง Logic ที่ซับซ้อน  
✅ **Expression Functions**: ใช้ `int()` เพื่อแปลงค่าเป็น Integer  
✅ **Dynamic Content**: ใช้ Dynamic Content จาก Forms ใน Actions  
✅ **Email Integration**: ส่ง Email ผ่าน Office 365 Outlook  
✅ **Teams Integration**: ส่งข้อความไปยัง Teams Chat/Channel  
✅ **Calendar Integration**: สร้าง Calendar Event อัตโนมัติ  
✅ **Date/Time Functions**: ใช้ `addDays()`, `addHours()`, `utcNow()` เพื่อจัดการวันที่/เวลา  

---

## 💡 Tips & Tricks

1. **การใช้ Expression `int()` ใน Condition**:
   - ข้อมูลจาก Forms อาจเป็น Text หรือ Number
   - ใช้ `int()` เพื่อแปลงเป็น Integer ก่อนเปรียบเทียบ
   - ตัวอย่าง: `int(body('Get_response_details')?['What amount do you plan on donating?'])`

2. **การจัดการวันที่/เวลา**:
   - `utcNow()` → วันที่/เวลาปัจจุบัน (UTC)
   - `addDays(utcNow(), 1)` → เพิ่มวัน 1 วัน
   - `addHours(utcNow(), 1)` → เพิ่มชั่วโมง 1 ชั่วโมง
   - `addMinutes(utcNow(), 30)` → เพิ่มนาที 30 นาที

3. **Nested Conditions Best Practices**:
   - หลีกเลี่ยงการซ้อนกันมากเกินไป (อ่านยาก)
   - ใช้ชื่อที่ชัดเจนสำหรับแต่ละ Condition
   - จัดกลุ่ม Actions ที่เกี่ยวข้องเข้าด้วยกัน

4. **การ Debug Expression**:
   - ใช้ **Compose** เพื่อทดสอบ Expression ก่อน
   - ดู Output ของ Compose เพื่อตรวจสอบว่าถูกต้องหรือไม่

5. **การจัดการ Error**:
   - ใช้ **Configure run after** เพื่อจัดการ Error
   - ใช้ **Scope** เพื่อจัดกลุ่ม Actions และจัดการ Error

---

## 🔄 แบบฝึกหัดเพิ่มเติม

1. **เพิ่ม Switch Condition สำหรับ Donor Type**:
   - ใช้ Switch เพื่อจัดการ "First time donor", "Occasional donor", "All-the-time donor"
   - ส่ง Email ที่แตกต่างกันตามประเภทผู้บริจาค

2. **เพิ่มการตรวจสอบ Email Format**:
   - ใช้ Condition เพื่อตรวจสอบว่า Email ถูกต้องหรือไม่
   - ถ้า Email ไม่ถูกต้อง → ส่ง Error Message

3. **เพิ่มการบันทึกข้อมูลลง SharePoint List**:
   - สร้าง SharePoint List เพื่อบันทึกข้อมูลการบริจาค
   - ใช้ **Create item** เพื่อบันทึกข้อมูลทุกครั้งที่มีการส่งฟอร์ม

4. **เพิ่มการส่ง Email แจ้งเตือนไปยัง Admin**:
   - เมื่อจำนวนเงินบริจาค > 10,000 → ส่ง Email แจ้งเตือนไปยัง Admin
   - รวมข้อมูล: ชื่อ, Email, จำนวนเงิน, ประเภทผู้บริจาค

5. **เพิ่มการ Format จำนวนเงิน**:
   - ใช้ `formatNumber()` เพื่อ Format จำนวนเงิน (เช่น 15,000)
   - ใช้ใน Email Body และ Teams Message

6. **เพิ่มการสร้าง Event ที่มีรายละเอียดมากขึ้น**:
   - เพิ่ม Description ใน Calendar Event
   - รวมข้อมูล: ชื่อ, Email, จำนวนเงิน, ประเภทผู้บริจาค

---

## ❓ คำถามที่พบบ่อย (FAQ)

**Q: ทำไมต้องใช้ Get response details?**  
A: เพราะ Trigger "When a new response is submitted" ให้เฉพาะ Response Id, ต้องใช้ Get response details เพื่อดึงข้อมูลรายละเอียดทั้งหมดจากฟอร์ม

**Q: Nested Conditions คืออะไร?**  
A: Nested Conditions คือการสร้าง Condition ซ้อนกัน (Condition ภายใน Condition) เพื่อสร้าง Logic ที่ซับซ้อนขึ้น

**Q: ทำไมต้องใช้ `int()` ใน Condition?**  
A: เพราะข้อมูลจาก Forms อาจเป็น Text หรือ Number, ใช้ `int()` เพื่อแปลงเป็น Integer ก่อนเปรียบเทียบเพื่อความแม่นยำ

**Q: วิธีใช้ Expression ใน Condition?**  
A: คลิกไอคอน **Expression** (fx) ใน Field แล้วพิมพ์ Expression (เช่น `int(body('Get_response_details')?['What amount do you plan on donating?'])`)

**Q: สามารถซ้อน Conditions หลายชั้นได้ไหม?**  
A: ได้ แต่ไม่แนะนำให้ซ้อนกันมากเกินไป (เช่น 3-4 ชั้น) เพราะจะทำให้อ่านยากและ Debug ยาก

**Q: วิธีทดสอบ Flow ที่ใช้ Forms Trigger?**  
A: ต้องส่งฟอร์มจริงๆ เพื่อ Trigger Flow, หรือใช้ **Test** mode ใน Power Automate เพื่อจำลองการส่งฟอร์ม

**Q: Calendar Event จะสร้างเมื่อไหร่?**  
A: Calendar Event จะสร้างเมื่อจำนวนเงินบริจาค > 10,000 (ผ่านทั้ง Condition หลักและ Condition 1)

**Q: วิธีเปลี่ยน Time zone ของ Calendar Event?**  
A: เลือก Time zone ในฟิลด์ **Time zone** ของ Create event (V4) Action

---

## 📝 สรุป

ใน Exercise นี้ คุณได้เรียนรู้:
- การสร้าง Microsoft Form และใช้เป็น Trigger
- **การใช้ Get response details เพื่อดึงข้อมูลจากฟอร์ม**
- **การใช้ Nested Conditions เพื่อสร้าง Logic ที่ซับซ้อน**
- การส่ง Email และ Teams Message ตามเงื่อนไข
- การสร้าง Calendar Event อัตโนมัติ
- การใช้ Expression functions (`int()`, `addDays()`, `utcNow()`)

**Flow Structure**:
```
When a new response is submitted (Charity Form)
  ↓
Get response details
  ↓
Post message (Teams) - แจ้งเตือนการส่งฟอร์ม
  ↓
Condition: int(Donation Amount) > 3000
  ├─ True Branch:
  │   ├─ Send Email (V2) - "Whale Alerts"
  │   └─ Condition 1: int(Donation Amount) > 10000
  │       ├─ True Branch: Create Event (V4) - "Whale donated above 10,000"
  │       └─ False Branch: (Empty)
  └─ False Branch: (Empty)
```

**Condition Logic**:
```
ถ้า จำนวนเงินบริจาค > 3,000
  → ส่ง Email "Whale Alerts"
  → ถ้า จำนวนเงินบริจาค > 10,000
      → สร้าง Calendar Event
ถ้าไม่ใช่
  → ไม่ทำอะไร
```

**ข้อดีของการใช้ Nested Conditions**:
- ✅ สร้าง Logic ที่ซับซ้อนได้
- ✅ แยกการทำงานตามเงื่อนไขหลายชั้น
- ✅ ทำให้ Flow ยืดหยุ่นมากขึ้น
- ✅ ลดการสร้าง Flow หลายตัว

**คำแนะนำ**:
- ⚠️ หลีกเลี่ยงการซ้อนกันมากเกินไป (แนะนำไม่เกิน 3-4 ชั้น)
- ⚠️ ใช้ชื่อที่ชัดเจนสำหรับแต่ละ Condition
- ⚠️ ทดสอบทุกกรณีเพื่อให้แน่ใจว่า Logic ถูกต้อง

พร้อมสำหรับบทต่อไปแล้ว! 🎉

