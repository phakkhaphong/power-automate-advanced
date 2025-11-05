

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
