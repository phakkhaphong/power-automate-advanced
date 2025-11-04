# บทที่ 5: Best Practices & Error Handling

## Slide 1: Title
### บทที่ 5: Best Practices & Error Handling
**จัดการปัญหาและลองใหม่เมื่อเกิด Error**

---

## Slide 2: ภาพรวม
บทนี้สอนให้ Flow "จัดการปัญหา" และ "ลองใหม่" เมื่อเกิด Error

**สิ่งที่คุณจะได้เรียนรู้**:
- Try–Catch–Finally Pattern
- Retry Policy (ลองใหม่เมื่อเกิด Error)
- Error Types: Failed, Skipped, Timed Out
- Error Recovery Patterns

---

## Slide 3: Try-Catch-Finally Pattern
### Try Block (Scope)

**Purpose**: จัดกลุ่ม Actions ที่อาจเกิด Error

**Scope Action**: ใช้ Scope เพื่อสร้าง Try Block

**Error Isolation**: แยก Error Handling ในแต่ละ Scope

**Nested Scopes**: สร้าง Scope ซ้อนกันได้

---

## Slide 4: Catch Block
### Configure Run After

**ตั้งค่าให้รันเมื่อเกิด Error**:
- ✅ **is failed**: Action ล้มเหลว
- ✅ **is skipped**: Action ถูกข้าม
- ✅ **is timed out**: Action หมดเวลา
- ✅ **is successful**: Action สำเร็จ

**Error Handling Actions**:
- Log Error
- Send Notification
- Rollback Operations
- Retry Logic

---

## Slide 5: Finally Block
### Actions ที่ต้องรันเสมอ

**Purpose**: Actions ที่ต้องรันเสมอ ไม่ว่าจะสำเร็จหรือไม่

**Implementation**: ใช้ Actions นอก Scope ที่จะรันเสมอ

**Use Cases**:
- Cleanup Operations
- Final Notifications
- Status Updates

---

## Slide 6: Retry Policy
### ตั้งค่า Retry สำหรับ Actions

**Retry Types**:
- **Fixed Interval**: Retry หลังจากเวลาที่กำหนด
- **Exponential Backoff**: เพิ่มช่วงเวลา Retry แบบ Exponential

**Retry Settings**:
- **Retry Count**: จำนวนครั้งที่ Retry (เช่น 3 ครั้ง)
- **Retry Delay**: ระยะเวลาระหว่าง Retry

---

## Slide 7: Exponential Backoff
### ตัวอย่างการ Retry

**Exponential Backoff**:
- Retry ครั้งที่ 1: Delay 2 วินาที
- Retry ครั้งที่ 2: Delay 4 วินาที
- Retry ครั้งที่ 3: Delay 8 วินาที

**สูตร**: `2^retryCount` seconds

**ใช้เมื่อ**: Network Issues หรือ Temporary Errors

---

## Slide 8: Error Types: Failed
### Failed Actions

**Common Causes**:
- Invalid Input
- API Errors
- Authentication Failures
- Network Issues

**Handling**:
- Catch Error
- Log Details
- Send Notification
- Alternative Path

---

## Slide 9: Error Types: Skipped
### Skipped Actions

**Common Causes**:
- Condition ที่ไม่ผ่าน
- Missing Required Data
- Predecessor Action Failed

**Handling**:
- Check Skip Reason
- Provide Default Value
- Continue Flow

---

## Slide 10: Error Types: Timed Out
### Timed Out Actions

**Common Causes**:
- Long-Running Operations
- Network Latency
- Resource Unavailability

**Handling**:
- Increase Timeout
- Retry with Backoff
- Alternative Approach

---

## Slide 11: Terminate Flow
### หยุด Flow อย่างสมบูรณ์

**Terminate Options**:
- **Succeeded**: Terminate เป็น Success
- **Failed**: Terminate เป็น Failed
- **Cancelled**: Terminate เป็น Cancelled

**Use Cases**:
- Critical Error ที่ไม่สามารถแก้ไขได้
- Business Rule Violation
- Security Breach

---

## Slide 12: Wait Until Pattern
### รอจนกว่าเงื่อนไขจะเป็นจริง

**Implementation**:
- Use Do Until Loop
- Check Condition
- Delay Between Checks
- Timeout Protection

**Use Cases**:
- Wait for File Creation
- Wait for Status Change
- Poll API for Results

---

## Slide 13: Advanced Pattern: Circuit Breaker
### ป้องกันการเรียก API ซ้ำๆ

```
Initialize Variable: circuitBreakerOpen = false
Initialize Variable: failureCount = 0

If circuitBreakerOpen = true:
  - Skip API Call
  - Return Cached Data
Else:
  - Try API Call
  - If Failed: Increment failureCount
  - If failureCount >= threshold: Set circuitBreakerOpen = true
```

---

## Slide 14: Advanced Pattern: Fallback
### Pattern 2: Fallback Pattern

```
Try:
  - Call Primary API
Catch:
  - Call Secondary API (Fallback)
  - If Secondary Also Failed: Use Default Value
```

**ใช้เมื่อ**: มี API สำรอง

---

## Slide 15: Advanced Pattern: Compensation
### Pattern 3: Compensation Pattern

```
Try:
  - Create Order
  - Process Payment
Catch:
  - Rollback Order (Compensation)
  - Refund Payment (Compensation)
```

**ใช้เมื่อ**: ต้อง Rollback Transaction

---

## Slide 16: Best Practices
### 1. Error Handling Structure

```
Try:
  - Main Logic
Catch (Failed):
  - Log Error
  - Retry (if applicable)
  - Alternative Path
Finally:
  - Cleanup
  - Final Status
```

---

## Slide 17: Best Practices (ต่อ)
### 2. Retry Strategy
- ใช้ Exponential Backoff สำหรับ Transient Errors
- ตั้ง Retry Limits เพื่อป้องกัน Infinite Loops
- Log Retry Attempts เพื่อติดตาม Performance

### 3. Error Logging
- Log Error Details: Message, Code, Stack Trace
- Include Context: Input Data, Flow Run ID
- Store Logs: SharePoint, Database, Log Analytics

---

## Slide 18: Lab Exercise
### Lab 3: Error Handling

**สิ่งที่คุณจะได้ทำ**:
1. สร้าง Error Handling ด้วย Try–Catch–Finally Pattern
2. ตั้งค่า Retry Policy สำหรับ Actions
3. จัดการ Error Types ต่างๆ (Failed, Skipped, Timed Out)
4. ส่ง Notification เมื่อเกิด Error

---

## Slide 19: สรุปบทเรียน
หลังจากจบบทนี้ คุณจะสามารถ:

- ✅ ใช้ Try–Catch–Finally Pattern ได้อย่างถูกต้อง
- ✅ ตั้งค่า Run After และ Retry Policy
- ✅ จัดการ Error Types ต่างๆ
- ✅ สร้าง Error Handling ที่แข็งแกร่งและน่าเชื่อถือ

---

## Slide 20: Next Steps
**พร้อมสำหรับบทต่อไป!**

**บทที่ 6**: Parent–Child Flows
- Child Flow Concept
- HTTP Request/Response
- Solutions

**คำถาม?**


