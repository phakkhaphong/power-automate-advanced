# Exercise 5a: Forms → Switch Condition → Error Handling (Try-Catch-Finally, Retry Policy, Terminate)

## 🎯 วัตถุประสงค์

ใน Exercise นี้ คุณจะได้เรียนรู้การ:
- สร้าง Flow ที่ใช้ **Microsoft Forms Trigger** ("When a new response is submitted")
- ใช้ **Get response details** เพื่อดึงข้อมูลจากฟอร์ม
- ใช้ **Switch Condition** เพื่อจัดการหลาย Case (กรณี) แทนการใช้ Condition หลายตัว
- ส่ง Email ที่แตกต่างกันตามประเภทผู้บริจาค
- สร้าง Calendar Event สำหรับผู้บริจาคประเภทพิเศษ
- **ใช้ Try-Catch-Finally Scope** เพื่อจัดการ Error Handling
- **ตั้งค่า Retry Policy** สำหรับ Actions ที่อาจล้มเหลว
- **ใช้ Terminate Flow** เพื่อหยุด Flow เมื่อเกิด Critical Error

> 💡 **หมายเหตุ**: Exercise นี้เป็นการปรับปรุงจาก Exercise 3c โดยเพิ่ม Error Handling:
> - **Try Block**: จัดกลุ่ม Actions หลักที่อาจเกิด Error
> - **Catch Block**: จัดการเมื่อเกิด Error (Failed, Skipped, Timed Out)
> - **Finally Block**: Actions ที่ต้องรันเสมอ
> - **Retry Policy**: ลองใหม่เมื่อ Action ล้มเหลว
> - **Terminate**: หยุด Flow เมื่อเกิด Critical Error

---

## 📋 สิ่งที่ต้องเตรียม

- Power Automate Account (สามารถใช้ Free Account ได้)
- Microsoft Forms Account (สำหรับสร้างฟอร์ม "Charity Form")
  - 💡 **หมายเหตุ**: ใช้ฟอร์ม "Charity Form" เดียวกับ Exercise 3c
- ความรู้พื้นฐานเรื่อง Flow Creation และการใช้งาน Condition
- Email Account ที่เชื่อมต่อกับ Power Automate
- Teams Account (สำหรับส่งข้อความ)
- Calendar Account (สำหรับสร้าง Event)

---

## 🚀 ขั้นตอนการปฏิบัติ

### ขั้นตอนที่ 1: ตรวจสอบ Charity Form

ก่อนเริ่ม Flow ตรวจสอบว่าคุณมีฟอร์ม "Charity Form" แล้วหรือไม่:

1. เข้าสู่ **Microsoft Forms** (https://forms.office.com)
2. ตรวจสอบว่ามีฟอร์มชื่อ `Charity Form` ที่มีคำถาม:
   - **What's your name?** (Text)
   - **What's your E-Mail address?** (Text)
   - **What amount do you plan on donating?** (Number)
   - **What type of donor describes you the best?** (Choice)
     - Options: `First time donor`, `Occasional donor`, `All-the-time donor`

> 💡 **หมายเหตุ**: ถ้ายังไม่มีฟอร์ม ให้สร้างตาม Exercise 3c หรือสร้างใหม่ตามขั้นตอนที่ 1 ของ Exercise 3c

### ขั้นตอนที่ 2: สร้าง Flow ใหม่พร้อม Forms Trigger

1. เข้าสู่ **Power Automate** (https://make.powerautomate.com/)
2. คลิก **Create** → เลือก **Automated cloud flow**
3. ตั้งชื่อ Flow: `Exercise 5a - Forms Switch Error Handling`
4. ค้นหาและเลือก Trigger: **When a new response is submitted**
5. คลิก **Create**

### ขั้นตอนที่ 3: ตั้งค่า Forms Trigger

1. ใน **When a new response is submitted**:
   - **Form Id**: เลือก `Charity Form` (หรือชื่อฟอร์มที่คุณสร้าง)
   - **Connection**: ตรวจสอบว่าเชื่อมต่อกับ Microsoft Forms Account ของคุณแล้ว

> 💡 **หมายเหตุ**: Trigger นี้จะทำงานอัตโนมัติเมื่อมีคนส่งฟอร์ม "Charity Form"

### ขั้นตอนที่ 4: Initialize Variables สำหรับ Error Handling

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Initialize variable**
3. ตั้งค่า:
   - **Name**: `flowStatus`
   - **Type**: `String`
   - **Value**: `In Progress`

4. เพิ่ม Action: **Initialize variable**
   - **Name**: `errorMessage`
   - **Type**: `String`
   - **Value**: (ว่างไว้)

5. เพิ่ม Action: **Initialize variable**
   - **Name**: `hasError`
   - **Type**: `Boolean`
   - **Value**: `false`

> 💡 **คำอธิบาย**: ตัวแปรเหล่านี้จะใช้สำหรับติดตามสถานะ Flow และ Error

### ขั้นตอนที่ 5: สร้าง Try Block (Scope) - Main Logic

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Scope**
3. ตั้งชื่อ Scope: `Try - Main Processing`

> 💡 **คำอธิบาย**: Scope นี้คือ "Try Block" ที่จะรัน Logic หลัก และถ้าเกิด Error จะไปที่ Catch Block

### ขั้นตอนที่ 6: เพิ่ม Get response details (ภายใน Try Block)

1. **ภายใน Scope "Try - Main Processing"** คลิก **Add an action**
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

### ขั้นตอนที่ 7: ตั้งค่า Retry Policy สำหรับ Get response details

1. คลิกที่ **Get response details** Action
2. คลิก **Settings** (ไอคอนฟันเฟือง) ที่มุมขวาบน
3. ตั้งค่า:
   - **Retry Policy**: `Exponential`
   - **Count**: `3`
   - **Interval**: `PT2S` (2 seconds)
   - **Maximum Interval**: `PT30S` (30 seconds)

> 💡 **คำอธิบาย**: Retry Policy จะลองดึงข้อมูลใหม่ 3 ครั้ง ถ้า Fail ครั้งแรก
> - Retry ครั้งที่ 1: Delay 2 วินาที
> - Retry ครั้งที่ 2: Delay 4 วินาที
> - Retry ครั้งที่ 3: Delay 8 วินาที

### ขั้นตอนที่ 8: ส่งข้อความแจ้งเตือนไปยัง Teams (ภายใน Try Block)

1. **ภายใน Scope "Try - Main Processing"** คลิก **Add an action**
2. ค้นหาและเลือก **Post message in a chat or channel**
3. ตั้งค่า:
   - **Post as**: `Flow bot`
   - **Post in**: `Chat with Flow bot`
   - **Recipient**: ใส่ Email address ของคุณ (เช่น `your-email@example.com`)
   - **Message**: ใช้ Dynamic Content เพื่อสร้างข้อความ:
     ```
     {What's your name?} has submitted a Charity form.
     ```

4. **ตั้งค่า Retry Policy**:
   - คลิก **Settings** (ไอคอนฟันเฟือง)
   - **Retry Policy**: `Fixed interval`
   - **Count**: `2`
   - **Interval**: `PT5S` (5 seconds)

> 💡 **คำอธิบาย**: Action นี้จะส่งข้อความแจ้งเตือนไปยัง Teams ทุกครั้งที่มีการส่งฟอร์ม
> - ตั้งค่า Retry Policy เพื่อลองส่งอีกครั้งถ้าล้มเหลว

### ขั้นตอนที่ 9: เพิ่ม Switch Condition (ภายใน Try Block)

1. **ภายใน Scope "Try - Main Processing"** คลิก **Add an action**
2. ค้นหาและเลือก **Switch** (หรือ **Control** → **Switch**)
3. ตั้งค่า:
   - **On**: เลือก `What type of donor describes you the best?` จาก Dynamic Content (จาก Get response details)

> 💡 **คำอธิบาย**: Switch จะตรวจสอบค่าของ "What type of donor describes you the best?" และทำงานตาม Case ที่ตรงกัน
> - **Case 1**: "First time donor" → ส่ง Email แจ้งเตือน
> - **Case 2**: "Occasional donor" → ส่ง Email ขอบคุณ
> - **Default**: "All-the-time donor" → สร้าง Calendar Event

**Switch Structure**:
```
Switch: What type of donor describes you the best?
  ├─ Case 1: "First time donor" → Send Email (V2)
  ├─ Case 2: "Occasional donor" → Send Email (V2) 1
  └─ Default: "All-the-time donor" → Create Event (V4)
```

### ขั้นตอนที่ 10: เพิ่ม Case 1 - "First time donor"

1. **ภายใน Case 1** (คลิกที่ Case 1 หรือ **Add case** ถ้ายังไม่มี)
2. ตั้งค่า Case:
   - **Equals**: พิมพ์ `First time donor` (ตรงกับค่าจากฟอร์ม)

3. **ภายใน Case 1** คลิก **Add an action**
4. ค้นหาและเลือก **Send an email (V2)**
5. ตั้งค่า:
   - **To**: ใส่ Email address ของคุณ (เช่น `your-email@example.com`)
   - **Subject**: พิมพ์ `We have a first time donor`
   - **Body**: ใช้ Dynamic Content เพื่อสร้างข้อความ:
     ```
     Let's make sure to reach out of them.
     
     Name: {What's your name?}
     Email: {What's your E-Mail address?}
     Amount: {What amount do you plan on donating?}
     ```

6. **ตั้งค่า Retry Policy**:
   - คลิก **Settings** (ไอคอนฟันเฟือง)
   - **Retry Policy**: `Exponential`
   - **Count**: `3`
   - **Interval**: `PT2S` (2 seconds)

> 💡 **คำอธิบาย**: Action นี้จะส่ง Email เมื่อผู้บริจาคเป็น "First time donor"
> - Subject: `We have a first time donor`
> - Body: รวมข้อมูลชื่อ, Email, และจำนวนเงินบริจาค
> - Retry Policy: ลองส่งอีก 3 ครั้งถ้าล้มเหลว

### ขั้นตอนที่ 11: เพิ่ม Case 2 - "Occasional donor"

1. **ภายใน Switch** คลิก **Add case** เพื่อเพิ่ม Case 2
2. ตั้งค่า Case:
   - **Equals**: พิมพ์ `Occasional donor` (ตรงกับค่าจากฟอร์ม)

3. **ภายใน Case 2** คลิก **Add an action**
4. ค้นหาและเลือก **Send an email (V2)**
5. ตั้งค่า:
   - **To**: เลือก `What's your E-Mail address?` จาก Dynamic Content (จาก Get response details)
   - **Subject**: พิมพ์ `Thank you for donating`
   - **Body**: พิมพ์ `Thank you so much more donating - you are clearly an occasional donor`

6. **ตั้งค่า Retry Policy**:
   - คลิก **Settings** (ไอคอนฟันเฟือง)
   - **Retry Policy**: `Exponential`
   - **Count**: `3`
   - **Interval**: `PT2S` (2 seconds)

> 💡 **คำอธิบาย**: Action นี้จะส่ง Email ขอบคุณไปยังผู้บริจาคเมื่อเป็น "Occasional donor"
> - To: Email จากฟอร์ม (ส่งไปยังผู้บริจาคโดยตรง)
> - Subject: `Thank you for donating`
> - Body: ข้อความขอบคุณ
> - Retry Policy: ลองส่งอีก 3 ครั้งถ้าล้มเหลว

### ขั้นตอนที่ 12: เพิ่ม Default Case - "All-the-time donor"

1. **ภายใน Switch** คลิกที่ **Default** case (หรือสร้าง Default ถ้ายังไม่มี)
2. **ภายใน Default** คลิก **Add an action**
3. ค้นหาและเลือก **Create event (V4)**
4. ตั้งค่า:
   - **Calendar id**: เลือก Calendar ของคุณ (เช่น `ปฏิทิน` หรือ `Calendar`)
   - **Subject**: พิมพ์ `We have all-time-donor! Let Discuss how to bring him /her in`
   - **Start time**: คลิก **Expression** (fx) และพิมพ์:
     ```
     addDays(utcNow(), 1)
     ```
   - **End time**: คลิก **Expression** (fx) และพิมพ์:
     ```
     addHours(addDays(utcNow(), 1), 1)
     ```
   - **Time zone**: เลือก `(UTC+07:00) Bangkok, Hanoi, Jakarta` (หรือ Time zone ที่คุณต้องการ)
   - **Required attendees**: เพิ่ม Email address ของเพื่อนร่วมงาน (เช่น `colleague@example.com`)

5. **ตั้งค่า Retry Policy**:
   - คลิก **Settings** (ไอคอนฟันเฟือง)
   - **Retry Policy**: `Fixed interval`
   - **Count**: `2`
   - **Interval**: `PT10S` (10 seconds)

> 💡 **คำอธิบาย**: Action นี้จะสร้าง Calendar Event เมื่อผู้บริจาคเป็น "All-the-time donor"
> - Subject: `We have all-time-donor! Let Discuss how to bring him /her in`
> - Start/End time: พรุ่งนี้
> - Required attendees: เชิญเพื่อนร่วมงานเข้าร่วมประชุม
> - Retry Policy: ลองสร้างอีก 2 ครั้งถ้าล้มเหลว

### ขั้นตอนที่ 13: อัพเดท Status เมื่อสำเร็จ (ภายใน Try Block)

1. **ภายใน Scope "Try - Main Processing"** (หลัง Switch) คลิก **Add an action**
2. ค้นหาและเลือก **Set variable**
3. ตั้งค่า:
   - **Name**: `flowStatus`
   - **Value**: `Completed Successfully`

> 💡 **คำอธิบาย**: อัพเดทสถานะ Flow เป็น "Completed Successfully" เมื่อ Logic หลักสำเร็จ

### ขั้นตอนที่ 14: สร้าง Catch Block (Run After) - Error Handling

1. คลิกที่ **Scope "Try - Main Processing"**
2. คลิก **...** (สามจุด) ที่มุมขวาบน → เลือก **Configure run after**
3. เลือกเงื่อนไข:
   - ✅ **is failed** → จัดการเมื่อ Action ล้มเหลว
   - ✅ **is skipped** → จัดการเมื่อ Action ถูกข้าม
   - ✅ **is timed out** → จัดการเมื่อ Action หมดเวลา
   - ❌ **is successful** → ไม่เลือก (จะไม่รันเมื่อสำเร็จ)

4. คลิก **Done**

> 💡 **คำอธิบาย**: การตั้งค่า "Configure run after" นี้จะทำให้ Actions ต่อไปจะรันเฉพาะเมื่อ Try Block ล้มเหลว (Catch Block)

### ขั้นตอนที่ 15: เพิ่ม Error Handling Actions (Catch Block)

1. เพิ่ม Action: คลิก **+ New step** (หลังจาก Configure run after)
2. ค้นหาและเลือก **Scope**
3. ตั้งชื่อ Scope: `Catch - Error Handling`

4. **ภายใน Scope "Catch - Error Handling"**:
   - เพิ่ม Action: **Set variable**
     - **Name**: `hasError`
     - **Value**: `true`

   - เพิ่ม Action: **Set variable**
     - **Name**: `flowStatus`
     - **Value**: `Failed`

   - เพิ่ม Action: **Set variable**
     - **Name**: `errorMessage`
     - **Value**: คลิก **Expression** (fx) และพิมพ์:
     ```
     concat('Error occurred in Try Block. Error: ', coalesce(outputs('Try_-_Main_Processing')?['error']?['message'], 'Unknown error'))
     ```

> 💡 **คำอธิบาย**: Actions เหล่านี้จะรันเมื่อ Try Block ล้มเหลว
> - ตั้งค่า `hasError` = `true`
> - ตั้งค่า `flowStatus` = `Failed`
> - เก็บ Error Message เพื่อใช้ใน Error Email

### ขั้นตอนที่ 16: ส่ง Error Notification Email (ภายใน Catch Block)

1. **ภายใน Scope "Catch - Error Handling"** คลิก **Add an action**
2. ค้นหาและเลือก **Send an email (V2)**
3. ตั้งค่า:
   - **To**: ใส่ Email address ของคุณ (เช่น `your-email@example.com`)
   - **Subject**: พิมพ์ `⚠️ Critical Error: Charity Form Processing Failed`
   - **Body**: ใช้ Dynamic Content เพื่อสร้างข้อความ:
     ```
     ⚠️ Critical Error Occurred!
     
     Flow Name: Exercise 5a - Forms Switch Error Handling
     Status: @{variables('flowStatus')}
     Error Message: @{variables('errorMessage')}
     Flow Run ID: @{workflow()['run']['name']}
     
     Form Response ID: @{triggerOutputs()['body']['responseId']}
     
     กรุณาตรวจสอบ Flow Run และแก้ไขปัญหา
     ```

> 💡 **คำอธิบาย**: ส่ง Email แจ้งเตือนเมื่อเกิด Error
> - Subject: `⚠️ Critical Error: Charity Form Processing Failed`
> - Body: รวมข้อมูล Error Message, Flow Run ID, และ Form Response ID

### ขั้นตอนที่ 17: ตรวจสอบ Critical Error และ Terminate Flow

1. **ภายใน Scope "Catch - Error Handling"** คลิก **Add an action**
2. ค้นหาและเลือก **Condition**
3. ตั้งค่า:
   - **Condition**: คลิก **Expression** (fx) และพิมพ์:
     ```
     contains(variables('errorMessage'), 'Critical')
     ```
     หรือใช้เงื่อนไขอื่น เช่น:
     ```
     equals(variables('flowStatus'), 'Failed')
     ```

4. ในช่อง **If yes** (Critical Error):
   - เพิ่ม Action: **Terminate**
     - **Status**: `Failed`
     - **Reason**: ใช้ Dynamic Content:
     ```
     Critical error occurred: @{variables('errorMessage')}
     ```

> 💡 **คำอธิบาย**: Terminate Flow เมื่อเกิด Critical Error
> - Status: `Failed` → Flow จะถูก Terminate เป็น Failed
> - Reason: บันทึกเหตุผลการ Terminate

### ขั้นตอนที่ 18: สร้าง Finally Block - Final Notification

1. เพิ่ม Action: คลิก **+ New step** (นอก Scopes ทั้งหมด)
2. ค้นหาและเลือก **Scope**
3. ตั้งชื่อ Scope: `Finally - Final Summary`

4. **ภายใน Scope "Finally - Final Summary"** คลิก **Add an action**
5. ค้นหาและเลือก **Send an email (V2)**
6. ตั้งค่า:
   - **To**: ใส่ Email address ของคุณ (เช่น `your-email@example.com`)
   - **Subject**: พิมพ์ `📊 Flow Execution Summary - Charity Form`
   - **Body**: ใช้ Dynamic Content เพื่อสร้างข้อความ:
     ```
     📊 Flow Execution Summary
     
     Flow Name: Exercise 5a - Forms Switch Error Handling
     Execution Status: @{variables('flowStatus')}
     Error Occurred: @{if(equals(variables('hasError'), true), 'Yes', 'No')}
     
     @{if(equals(variables('hasError'), true), concat('Error Message: ', variables('errorMessage')), 'No errors occurred.')}
     
     Flow Run ID: @{workflow()['run']['name']}
     Execution Time: @{utcNow()}
     
     ---
     Form Response Details:
     Response ID: @{triggerOutputs()['body']['responseId']}
     
     @{if(not(equals(variables('hasError'), true)), concat('Form submitted successfully by: ', triggerOutputs()['body']['responder']), 'Form processing failed.')}
     ```

> 💡 **คำอธิบาย**: Finally Block จะรันเสมอ ไม่ว่าจะ Try Block สำเร็จหรือไม่
> - ส่ง Email Summary ทุกครั้งที่ Flow รัน
> - รวมข้อมูล: Status, Error (ถ้ามี), Flow Run ID, และ Form Response Details

### ขั้นตอนที่ 19: อัพเดท Status สุดท้าย (ภายใน Finally Block)

1. **ภายใน Scope "Finally - Final Summary"** คลิก **Add an action**
2. ค้นหาและเลือก **Set variable**
3. ตั้งค่า:
   - **Name**: `flowStatus`
   - **Value**: คลิก **Expression** (fx) และพิมพ์:
     ```
     if(equals(variables('hasError'), true), 'Failed - Checked', 'Completed - Summary Sent')
     ```

> 💡 **คำอธิบาย**: อัพเดทสถานะ Flow สุดท้ายใน Finally Block

---

## 📊 Flow Structure

**Flow Structure ที่สมบูรณ์**:
```
When a new response is submitted (Charity Form)
  ↓
Initialize Variables:
  - flowStatus = "In Progress"
  - errorMessage = ""
  - hasError = false
  ↓
Try Block (Scope: Try - Main Processing)
  ├─ Get response details (Retry Policy: Exponential, 3 times)
  ├─ Post message (Teams) (Retry Policy: Fixed, 2 times)
  ├─ Switch: What type of donor describes you the best?
  │   ├─ Case 1: "First time donor"
  │   │   └─ Send Email (V2) (Retry Policy: Exponential, 3 times)
  │   ├─ Case 2: "Occasional donor"
  │   │   └─ Send Email (V2) (Retry Policy: Exponential, 3 times)
  │   └─ Default: "All-the-time donor"
  │       └─ Create Event (V4) (Retry Policy: Fixed, 2 times)
  └─ Set variable: flowStatus = "Completed Successfully"
  ↓
Catch Block (Configure Run After: Failed, Skipped, Timed Out)
  ├─ Scope: Catch - Error Handling
  │   ├─ Set variable: hasError = true
  │   ├─ Set variable: flowStatus = "Failed"
  │   ├─ Set variable: errorMessage = (Error details)
  │   ├─ Send Email (V2): Error Notification
  │   └─ Condition: Is Critical Error?
  │       └─ Yes: Terminate Flow (Status: Failed)
  ↓
Finally Block (Scope: Finally - Final Summary)
  ├─ Send Email (V2): Flow Execution Summary
  └─ Set variable: flowStatus = (Final status)
```

---

## 🧪 ขั้นตอนที่ 20: ทดสอบ Flow

### Test Case 1: Success Case - "First time donor"

1. คลิก **Save**
2. เปิดฟอร์ม "Charity Form" ใน Browser
3. กรอกข้อมูล:
   - **What's your name?**: `ทดสอบ Success 1`
   - **What's your E-Mail address?**: `test1@example.com`
   - **What amount do you plan on donating?**: `3000`
   - **What type of donor describes you the best?**: `First time donor`
4. ส่งฟอร์ม
5. รอให้ Flow รันอัตโนมัติ
6. ตรวจสอบผลลัพธ์:
   - ✅ ควรเห็น Teams Message: `ทดสอบ Success 1 has submitted a Charity form.`
   - ✅ ควรเห็น Email: `We have a first time donor` (ส่งไปยัง Email ของคุณ)
   - ✅ ควรเห็น Summary Email: `📊 Flow Execution Summary - Charity Form`
   - ✅ Summary Email แสดง Status: `Completed - Summary Sent`
   - ❌ **ไม่เห็น** Error Email
   - ❌ **ไม่เห็น** Calendar Event

### Test Case 2: Success Case - "Occasional donor"

1. เปิดฟอร์ม "Charity Form" อีกครั้ง
2. กรอกข้อมูล:
   - **What's your name?**: `ทดสอบ Success 2`
   - **What's your E-Mail address?**: `test2@example.com`
   - **What amount do you plan on donating?**: `5000`
   - **What type of donor describes you the best?**: `Occasional donor`
3. ส่งฟอร์ม
4. รอให้ Flow รันอัตโนมัติ
5. ตรวจสอบผลลัพธ์:
   - ✅ ควรเห็น Teams Message: `ทดสอบ Success 2 has submitted a Charity form.`
   - ✅ ควรเห็น Email: `Thank you for donating` (ส่งไปยัง `test2@example.com`)
   - ✅ ควรเห็น Summary Email: `📊 Flow Execution Summary - Charity Form`
   - ✅ Summary Email แสดง Status: `Completed - Summary Sent`
   - ❌ **ไม่เห็น** Error Email
   - ❌ **ไม่เห็น** Calendar Event

### Test Case 3: Success Case - "All-the-time donor"

1. เปิดฟอร์ม "Charity Form" อีกครั้ง
2. กรอกข้อมูล:
   - **What's your name?**: `ทดสอบ Success 3`
   - **What's your E-Mail address?**: `test3@example.com`
   - **What amount do you plan on donating?**: `10000`
   - **What type of donor describes you the best?**: `All-the-time donor`
3. ส่งฟอร์ม
4. รอให้ Flow รันอัตโนมัติ
5. ตรวจสอบผลลัพธ์:
   - ✅ ควรเห็น Teams Message: `ทดสอบ Success 3 has submitted a Charity form.`
   - ✅ ควรเห็น Calendar Event: `We have all-time-donor! Let Discuss how to bring him /her in` (พรุ่งนี้)
   - ✅ ควรเห็น Summary Email: `📊 Flow Execution Summary - Charity Form`
   - ✅ Summary Email แสดง Status: `Completed - Summary Sent`
   - ❌ **ไม่เห็น** Error Email

### Test Case 4: Error Case - Simulate Failure

เพื่อทดสอบ Error Handling คุณสามารถ:

1. **วิธีที่ 1: Temporarily Break Flow**
   - แก้ไข Get response details ให้ใช้ Form Id ที่ผิด
   - หรือแก้ไข Email Address ให้ผิด
   - ส่งฟอร์ม
   - ตรวจสอบว่า:
     - ✅ ควรเห็น Error Email: `⚠️ Critical Error: Charity Form Processing Failed`
     - ✅ ควรเห็น Summary Email: `📊 Flow Execution Summary - Charity Form`
     - ✅ Summary Email แสดง Status: `Failed - Checked`
     - ✅ Summary Email แสดง Error Message

2. **วิธีที่ 2: Use Invalid Email**
   - ใน Case 2 ("Occasional donor") ใช้ Email ที่ไม่มีอยู่จริง
   - ส่งฟอร์มพร้อมเลือก "Occasional donor"
   - ตรวจสอบว่า Retry Policy ทำงาน (ลองส่งอีก 3 ครั้ง)

---

## 📊 ผลลัพธ์ที่คาดหวัง

### Success Case

คุณควรจะเห็น:
- ✅ **Teams Message**: `{ชื่อ} has submitted a Charity form.`
- ✅ **Action-Specific Result**: 
  - Case 1: Email `We have a first time donor`
  - Case 2: Email `Thank you for donating`
  - Default: Calendar Event
- ✅ **Summary Email**: `📊 Flow Execution Summary - Charity Form`
- ✅ **Summary Email Status**: `Completed - Summary Sent`
- ❌ **ไม่เห็น** Error Email
- ❌ **ไม่เห็น** Terminate Flow

### Error Case

คุณควรจะเห็น:
- ✅ **Error Email**: `⚠️ Critical Error: Charity Form Processing Failed`
- ✅ **Error Email Details**: รวม Error Message, Flow Run ID
- ✅ **Summary Email**: `📊 Flow Execution Summary - Charity Form`
- ✅ **Summary Email Status**: `Failed - Checked`
- ✅ **Summary Email Error Message**: แสดงรายละเอียด Error
- ✅ **Terminate Flow** (ถ้าเป็น Critical Error)

---

## 🎓 สิ่งที่ได้เรียนรู้

✅ **Microsoft Forms Trigger**: ใช้ "When a new response is submitted" เพื่อ Trigger Flow อัตโนมัติ  
✅ **Get response details**: ดึงข้อมูลรายละเอียดทั้งหมดจากฟอร์มที่ส่งมา  
✅ **Switch Condition**: ใช้ Switch เพื่อจัดการหลาย Case จากค่าเดียว  
✅ **Try-Catch-Finally Pattern**: โครงสร้าง Error Handling มาตรฐาน  
✅ **Scope Action**: ใช้ Scope เพื่อจัดกลุ่ม Actions และสร้าง Try/Catch Blocks  
✅ **Configure Run After**: ตั้งค่าให้ Actions รันเมื่อเกิด Error  
✅ **Retry Policy**: ตั้งค่า Retry สำหรับ Actions ที่อาจล้มเหลว  
  - **Exponential Backoff**: เพิ่มช่วงเวลา Retry แบบ Exponential (2, 4, 8... วินาที)  
  - **Fixed Interval**: Retry หลังจากเวลาที่กำหนด  
✅ **Error Types**: จัดการ Error Types ต่างๆ (Failed, Skipped, Timed Out)  
✅ **Terminate Flow**: หยุด Flow เมื่อเกิด Critical Error  
✅ **Error Notification**: ส่ง Email แจ้งเตือนเมื่อเกิด Error  
✅ **Finally Block**: Actions ที่ต้องรันเสมอ ไม่ว่าจะสำเร็จหรือไม่  
✅ **Variable Management**: ใช้ Variables เพื่อติดตามสถานะ Flow และ Error  
✅ **Dynamic Content**: ใช้ Dynamic Content จาก Forms, Variables, และ Workflow Context  

---

## 💡 Try-Catch-Finally Pattern: สรุป

### Try Block (Scope)
- **Purpose**: จัดกลุ่ม Actions หลักที่อาจเกิด Error
- **Implementation**: ใช้ **Scope** Action
- **Error Isolation**: แยก Error Handling ในแต่ละ Scope

### Catch Block (Run After)
- **Purpose**: จัดการเมื่อ Try Block ล้มเหลว
- **Implementation**: ใช้ **Configure run after** (Failed, Skipped, Timed Out)
- **Error Handling Actions**: 
  - Log Error
  - Send Notification
  - Terminate Flow (ถ้าจำเป็น)

### Finally Block (Scope)
- **Purpose**: Actions ที่ต้องรันเสมอ ไม่ว่าจะสำเร็จหรือไม่
- **Implementation**: ใช้ **Scope** นอก Try/Catch Blocks
- **Use Cases**: 
  - Final Summary Email
  - Status Updates
  - Cleanup Operations

---

## 💡 Retry Policy: สรุป

### Exponential Backoff
- **เมื่อไหร่ใช้**: Network Issues, Temporary Errors
- **สูตร**: `2^retryCount` seconds (2, 4, 8, 16...)
- **ข้อดี**: ลด Load บน Server
- **ข้อเสีย**: อาจใช้เวลานาน

### Fixed Interval
- **เมื่อไหร่ใช้**: Errors ที่แก้ไขได้เร็ว
- **สูตร**: Delay คงที่ทุกครั้ง (เช่น 5 วินาที)
- **ข้อดี**: เร็วและคาดเดาได้
- **ข้อเสีย**: อาจเพิ่ม Load บน Server

### Best Practices
- ✅ ตั้ง Retry Count ที่เหมาะสม (2-5 ครั้ง)
- ✅ ใช้ Exponential Backoff สำหรับ External APIs
- ✅ ตั้ง Maximum Interval เพื่อป้องกัน Delay นานเกินไป
- ✅ Log Retry Attempts เพื่อติดตาม Performance

---

## 💡 Terminate Flow: สรุป

### Terminate Options
- **Succeeded**: Terminate เป็น Success (Flow สำเร็จ)
- **Failed**: Terminate เป็น Failed (Flow ล้มเหลว)
- **Cancelled**: Terminate เป็น Cancelled (Flow ถูกยกเลิก)

### When to Use Terminate
- ✅ Critical Error ที่ไม่สามารถแก้ไขได้
- ✅ Business Rule Violation
- ✅ Security Breach
- ✅ Maximum Retry Reached

### Best Practices
- ✅ ใช้ Terminate เฉพาะกรณี Critical
- ✅ ระบุ Reason ที่ชัดเจน
- ✅ ส่ง Notification ก่อน Terminate

---

## 💡 Tips & Tricks

1. **Try-Catch-Finally Structure**:
   ```
   Try Block (Scope)
     └─ Main Logic
   Catch Block (Configure Run After: Failed/Skipped/Timed Out)
     └─ Error Handling
   Finally Block (Scope - Outside Try/Catch)
     └─ Final Actions (Always Runs)
   ```

2. **Retry Policy Settings**:
   - Exponential: `PT2S` (2 seconds), Max: `PT30S` (30 seconds)
   - Fixed: `PT5S` (5 seconds)
   - Count: 2-5 ครั้ง

3. **Error Message Variables**:
   - ใช้ Variables เพื่อเก็บ Error Message
   - ใช้ `coalesce()` เพื่อจัดการ Null Values
   - ใช้ `concat()` เพื่อรวม Error Messages

4. **Flow Status Tracking**:
   - ใช้ Variables เพื่อติดตามสถานะ Flow
   - อัพเดท Status ใน Try, Catch, และ Finally Blocks
   - รวม Status ใน Summary Email

5. **Dynamic Content in Emails**:
   - ใช้ `workflow()['run']['name']` เพื่อดึง Flow Run ID
   - ใช้ `utcNow()` เพื่อแสดงเวลาที่รัน
   - ใช้ `triggerOutputs()` เพื่อดึงข้อมูลจาก Trigger

---

## 🔄 แบบฝึกหัดเพิ่มเติม

1. **เพิ่ม Nested Try-Catch**:
   - สร้าง Try Block ซ้อนกันภายใน Switch Cases
   - จัดการ Error สำหรับแต่ละ Case แยกกัน

2. **เพิ่ม Circuit Breaker Pattern**:
   - ตรวจสอบ Failure Count
   - ถ้า Fail มากเกินไป: เปิด Circuit Breaker (หยุดเรียก API ชั่วคราว)

3. **เพิ่ม Fallback Pattern**:
   - ใน Case ที่ส่ง Email Fail: ใช้ Teams Message แทน
   - ใน Case ที่สร้าง Calendar Event Fail: ส่ง Email แจ้งเตือน

4. **ปรับปรุง Retry Policy**:
   - ใช้ Retry Policy ที่แตกต่างกันสำหรับแต่ละ Action
   - ตั้ง Maximum Interval ที่เหมาะสม

5. **เพิ่ม Error Logging**:
   - สร้าง SharePoint List เพื่อบันทึก Errors
   - ใช้ **Create item** เพื่อบันทึก Error Details

6. **เพิ่ม Conditional Terminate**:
   - Terminate เฉพาะกรณี Critical Errors
   - สำหรับ Non-Critical Errors: ส่ง Notification และ Continue

7. **เพิ่ม Error Recovery**:
   - ใน Catch Block: ลอง Alternative Approach
   - ถ้า Recovery สำเร็จ: อัพเดท Status และ Continue

---

## ❓ คำถามที่พบบ่อย (FAQ)

**Q: Try-Catch-Finally Pattern คืออะไร?**  
A: Pattern สำหรับ Error Handling ที่มี 3 ส่วน: Try (Logic หลัก), Catch (จัดการ Error), Finally (Actions ที่รันเสมอ)

**Q: Configure Run After คืออะไร?**  
A: การตั้งค่าให้ Actions รันตามเงื่อนไข (Successful, Failed, Skipped, Timed Out)

**Q: Retry Policy คืออะไร?**  
A: การตั้งค่าให้ Action ลองใหม่เมื่อล้มเหลว (Exponential Backoff หรือ Fixed Interval)

**Q: เมื่อไหร่ควรใช้ Terminate?**  
A: เมื่อเกิด Critical Error ที่ไม่สามารถแก้ไขได้ หรือ Business Rule Violation

**Q: Finally Block จะรันเมื่อไหร่?**  
A: รันเสมอ ไม่ว่าจะ Try Block สำเร็จหรือไม่ (แม้ Terminate Flow)

**Q: ควร Retry กี่ครั้ง?**  
A: ขึ้นอยู่กับ API และ Business Requirements โดยทั่วไป 2-5 ครั้ง

**Q: Exponential Backoff คืออะไร?**  
A: การเพิ่มช่วงเวลาระหว่าง Retry แบบ Exponential (2, 4, 8, 16... วินาที)

**Q: วิธีทดสอบ Error Handling?**  
A: Temporarily Break Flow (ใช้ Form Id ที่ผิด, Email ที่ผิด) หรือใช้ Invalid Data

**Q: Scope vs Configure Run After ต่างกันอย่างไร?**  
A: Scope = จัดกลุ่ม Actions, Configure Run After = ตั้งเงื่อนไขการรัน

**Q: สามารถใช้ Try-Catch-Finally กับ Switch ได้ไหม?**  
A: ได้, สามารถใช้ Try Block ภายใน Switch Cases หรือ Switch ภายใน Try Block

---

## 📝 สรุป

ใน Exercise นี้ คุณได้เรียนรู้:
- การใช้ Microsoft Forms Trigger และ Get response details
- **การใช้ Switch Condition เพื่อจัดการหลาย Case**
- **การใช้ Try-Catch-Finally Pattern สำหรับ Error Handling**
- **การใช้ Scope เพื่อสร้าง Try/Catch Blocks**
- **การตั้งค่า Retry Policy สำหรับ Actions**
- **การจัดการ Error Types ต่างๆ (Failed, Skipped, Timed Out)**
- **การใช้ Terminate Flow เมื่อเกิด Critical Error**
- **การส่ง Error Notification และ Summary Email**
- **การใช้ Variables เพื่อติดตามสถานะ Flow และ Error**
- **การใช้ Expression Functions (`coalesce()`, `concat()`, `if()`, `workflow()`)**

**Flow Structure**:
```
When a new response is submitted (Charity Form)
  ↓
Initialize Variables (flowStatus, errorMessage, hasError)
  ↓
Try Block (Scope: Try - Main Processing)
  ├─ Get response details (Retry Policy: Exponential, 3 times)
  ├─ Post message (Teams) (Retry Policy: Fixed, 2 times)
  ├─ Switch: What type of donor describes you the best?
  │   ├─ Case 1: "First time donor" → Send Email (Retry Policy: Exponential, 3 times)
  │   ├─ Case 2: "Occasional donor" → Send Email (Retry Policy: Exponential, 3 times)
  │   └─ Default: "All-the-time donor" → Create Event (Retry Policy: Fixed, 2 times)
  └─ Set variable: flowStatus = "Completed Successfully"
  ↓
Catch Block (Configure Run After: Failed, Skipped, Timed Out)
  ├─ Set variables (hasError, flowStatus, errorMessage)
  ├─ Send Email: Error Notification
  └─ Condition: Is Critical Error? → Terminate Flow (Status: Failed)
  ↓
Finally Block (Scope: Finally - Final Summary)
  ├─ Send Email: Flow Execution Summary
  └─ Set variable: flowStatus = (Final status)
```

**Try-Catch-Finally Logic**:
```
Try:
  - ดึงข้อมูลจาก Forms
  - ส่ง Teams Message
  - Process Switch Cases
  - อัพเดท Status = "Completed Successfully"
  
Catch (เมื่อเกิด Error):
  - ตั้ง hasError = true
  - ตั้ง flowStatus = "Failed"
  - เก็บ Error Message
  - ส่ง Error Notification Email
  - ถ้าเป็น Critical Error → Terminate Flow
  
Finally (รันเสมอ):
  - ส่ง Summary Email
  - อัพเดท Final Status
```

**ข้อดีของ Error Handling**:
- ✅ Flow แข็งแกร่งและน่าเชื่อถือมากขึ้น
- ✅ จัดการ Errors ได้อย่างเป็นระบบ
- ✅ Retry Policy ลดโอกาสล้มเหลว
- ✅ Error Notifications ช่วยให้ติดตามปัญหาได้เร็ว
- ✅ Finally Block รับประกันว่า Summary จะถูกส่งเสมอ

**Best Practices**:
- ✅ ใช้ Try-Catch-Finally Pattern สำหรับ Error Handling
- ✅ ตั้ง Retry Policy ที่เหมาะสมสำหรับแต่ละ Action
- ✅ ใช้ Terminate เฉพาะกรณี Critical
- ✅ ส่ง Error Notifications ที่มีรายละเอียดครบถ้วน
- ✅ ใช้ Variables เพื่อติดตามสถานะ Flow

พร้อมสำหรับบทต่อไปแล้ว! 🎉
