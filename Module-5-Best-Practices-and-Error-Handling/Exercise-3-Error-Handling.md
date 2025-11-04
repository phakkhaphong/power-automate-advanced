# Exercise 3: Error Handling ด้วย Try–Catch–Finally + Retry Policy

## 🎯 วัตถุประสงค์

ใน Exercise นี้ คุณจะได้เรียนรู้การ:
- สร้าง Error Handling ด้วย Try–Catch–Finally Pattern
- ตั้งค่า Retry Policy สำหรับ Actions
- จัดการ Error Types ต่างๆ (Failed, Skipped, Timed Out)
- ส่ง Notification เมื่อเกิด Error

---

## 📋 สิ่งที่ต้องเตรียม

- Power Automate Account
- API Endpoint สำหรับทดสอบ (หรือใช้ Mock API)
- Email Address สำหรับทดสอบ

---

## 🚀 ขั้นตอนการปฏิบัติ

### ขั้นตอนที่ 1: สร้าง Flow ใหม่

1. เข้าสู่ **Power Automate**
2. คลิก **Create** → เลือก **Instant cloud flow**
3. ตั้งชื่อ Flow: `Exercise 3 - Error Handling`
4. เลือก Trigger: **Manually trigger a flow**
5. คลิก **Create**

### ขั้นตอนที่ 2: Initialize Variables

1. เพิ่ม Action: **Initialize variable**
   - **Name**: `maxRetries`
   - **Type**: `Integer`
   - **Value**: `3`

2. เพิ่ม Action: **Initialize variable**
   - **Name**: `retryCount`
   - **Type**: `Integer`
   - **Value**: `0`

3. เพิ่ม Action: **Initialize variable**
   - **Name**: `success`
   - **Type**: `Boolean`
   - **Value**: `false`

4. เพิ่ม Action: **Initialize variable**
   - **Name**: `apiResponse`
   - **Type**: `String`
   - **Value**: (ว่างไว้)

> 💡 **คำอธิบาย**: ตัวแปรเหล่านี้จะใช้สำหรับควบคุมการ Retry

### ขั้นตอนที่ 3: สร้าง Try Block (Scope)

1. เพิ่ม Action: **Scope**
   - ตั้งชื่อ: `Try - API Call`

2. ภายใน Scope นี้:
   - เพิ่ม Action: **HTTP**
     - **Method**: `GET`
     - **URI**: `https://jsonplaceholder.typicode.com/posts/1` (Mock API สำหรับทดสอบ)
     - หรือใช้ API อื่นที่อาจล้มเหลวได้

> 💡 **คำอธิบาย**: Scope นี้คือ "Try Block" ที่จะลองทำอะไรบางอย่าง

### ขั้นตอนที่ 4: ตั้งค่า Retry Policy

1. คลิกที่ **HTTP** Action
2. คลิก **Settings** (ไอคอนฟันเฟือง)
3. ตั้งค่า:
   - **Retry Policy**: `Exponential`
   - **Count**: `3`
   - **Interval**: `PT2S` (2 seconds)
   - **Maximum Interval**: `PT60S` (60 seconds)

> 💡 **คำอธิบาย**: Retry Policy จะลองใหม่ 3 ครั้ง ถ้า Fail ครั้งแรก

### ขั้นตอนที่ 5: จัดการ Response

1. ภายใน Scope (หลัง HTTP Action):
   - เพิ่ม Action: **Set variable**
     - **Name**: `apiResponse`
     - **Value**: `body('HTTP')` (Output จาก HTTP Action)

   - เพิ่ม Action: **Set variable**
     - **Name**: `success`
     - **Value**: `true`

### ขั้นตอนที่ 6: สร้าง Catch Block (Run After)

1. คลิกที่ **Scope** (Try Block)
2. คลิก **...** (สามจุด) → **Configure run after**
3. เลือก:
   - ✅ **is failed**
   - ✅ **is skipped**
   - ✅ **is timed out**

4. เพิ่ม Action: **Increment variable**
   - **Name**: `retryCount`
   - **Value**: `1`

5. เพิ่ม Action: **Condition**
   - **Condition**: `variables('retryCount')` `is less than` `variables('maxRetries')`

6. ในช่อง **If yes** (ถ้ายัง Retry ไม่ครบ):
   - เพิ่ม Action: **Delay**
     - **Duration**: คลิก **Expression** และพิมพ์:
     ```
     mul(power(2, variables('retryCount')), 2)
     ```
     > 💡 Exponential Backoff: 2, 4, 8 วินาที

   - เพิ่ม Action: **HTTP** (Copy จาก Try Block)
   - เพิ่ม Action: **Set variable** → `success` = `true`

7. ในช่อง **If no** (ถ้า Retry ครบแล้ว):
   - เพิ่ม Action: **Send an email (V2)**
     - **To**: `your-email@example.com`
     - **Subject**: `⚠️ Critical Error: API Call Failed`
     - **Body**: 
     ```
     API Call ล้มเหลวหลังจาก Retry @{variables('retryCount')} ครั้ง
     
     กรุณาตรวจสอบ API Endpoint และลองใหม่อีกครั้ง
     ```

   - เพิ่ม Action: **Terminate**
     - **Status**: `Failed`
     - **Reason**: `API Call failed after maximum retries`

### ขั้นตอนที่ 7: สร้าง Finally Block

1. เพิ่ม Action: **Send an email (V2)** (นอก Scope)
   - **To**: `your-email@example.com`
   - **Subject**: `📊 Flow Execution Summary`
   - **Body**: 
   ```
   Flow Execution Summary:
   
   Status: @{if(equals(variables('success'), true), '✅ Success', '❌ Failed')}
   Retry Count: @{variables('retryCount')}
   API Response: @{variables('apiResponse')}
   ```

> 💡 **คำอธิบาย**: Finally Block จะรันเสมอ ไม่ว่าจะสำเร็จหรือไม่

### ขั้นตอนที่ 8: ทดสอบ Flow

#### Test Case 1: Success Case
1. ใช้ API ที่ทำงานได้: `https://jsonplaceholder.typicode.com/posts/1`
2. รัน Flow
3. ตรวจสอบว่า Success และได้รับ Email Summary

#### Test Case 2: Failure Case
1. ใช้ API ที่ล้มเหลว: `https://invalid-api-url.com/data`
2. รัน Flow
3. ตรวจสอบว่า:
   - Retry 3 ครั้ง
   - ส่ง Error Email
   - ส่ง Summary Email

---

## 🎓 สิ่งที่ได้เรียนรู้

✅ **Try–Catch–Finally Pattern**: โครงสร้าง Error Handling มาตรฐาน  
✅ **Retry Policy**: ตั้งค่าให้ Retry อัตโนมัติ  
✅ **Configure Run After**: จัดการเมื่อ Action Fail  
✅ **Error Types**: Failed, Skipped, Timed Out  
✅ **Exponential Backoff**: เพิ่มช่วงเวลา Retry แบบ Exponential  

---

## 💡 Tips & Tricks

1. **เลือก Retry Policy ให้เหมาะสม**:
   - Fixed Interval: สำหรับ Errors ที่แก้ไขได้เร็ว
   - Exponential Backoff: สำหรับ Network Issues

2. **ตั้งค่า Timeout**: อย่าให้ Retry นานเกินไป

3. **Log Errors**: บันทึก Error Details เพื่อ Debug

---

## 🔄 แบบฝึกหัดเพิ่มเติม

### แบบฝึกหัด A: Circuit Breaker Pattern

สร้าง Flow ที่:
1. ตรวจสอบ Failure Count
2. ถ้า Fail มากเกินไป: เปิด Circuit Breaker (หยุดเรียก API ชั่วคราว)
3. ใช้ Cached Data แทน

### แบบฝึกหัด B: Fallback Pattern

สร้าง Flow ที่:
1. ลองเรียก Primary API
2. ถ้า Fail: เรียก Secondary API
3. ถ้า Fail อีก: ใช้ Default Value

---

## ❓ คำถามที่พบบ่อย (FAQ)

**Q: ควร Retry กี่ครั้ง?**  
A: ขึ้นอยู่กับ API และ Business Requirements โดยทั่วไป 3-5 ครั้ง

**Q: Exponential Backoff คืออะไร?**  
A: คือการเพิ่มช่วงเวลาระหว่าง Retry แบบ Exponential (2, 4, 8, 16...)

**Q: Finally Block จะรันเมื่อไหร่?**  
A: รันเสมอ ไม่ว่าจะ Try Block สำเร็จหรือไม่

---

## 📝 สรุป

ใน Exercise นี้ คุณได้เรียนรู้:
- Try–Catch–Finally Pattern
- Retry Policy Configuration
- Error Handling Strategies
- Notification เมื่อเกิด Error

Flow ของคุณจะแข็งแกร่งและน่าเชื่อถือมากขึ้น! 🎉


