# บทที่ 5: Best Practices & Error Handling

## 🎯 ภาพรวม

บทนี้จะเรียนรู้เกี่ยวกับ **Best Practices** (วิธีที่ดี) และ **Error Handling** (การจัดการข้อผิดพลาด)

**สิ่งที่คุณจะได้เรียนรู้**:
- ✅ Try–Catch–Finally Pattern
- ✅ Retry Policy (ลองใหม่เมื่อเกิด Error)
- ✅ Error Types: Failed, Skipped, Timed Out
- ✅ Error Recovery Patterns

> 💡 **คิดง่ายๆ**: บทนี้สอนให้ Flow "จัดการปัญหา" และ "ลองใหม่" เมื่อเกิด Error

---

## เนื้อหาการเรียนรู้

### 1. Try–Catch–Finally Pattern

#### Try Block (Scope)
- **Purpose**: จัดกลุ่ม Actions ที่อาจเกิด Error
- **Scope Action**: ใช้ Scope เพื่อสร้าง Try Block
- **Error Isolation**: แยก Error Handling ในแต่ละ Scope
- **Nested Scopes**: สร้าง Scope ซ้อนกันได้

#### Catch Block (Run After)
- **Configure Run After**: ตั้งค่าให้รันเมื่อเกิด Error
- **Error Types**: 
  - **Failed**: Action ล้มเหลว
  - **Skipped**: Action ถูกข้าม
  - **Timed Out**: Action หมดเวลา
  - **Succeeded**: Action สำเร็จ
- **Error Handling Actions**: 
  - Log Error
  - Send Notification
  - Rollback Operations
  - Retry Logic

#### Finally Block
- **Purpose**: Actions ที่ต้องรันเสมอ ไม่ว่าจะสำเร็จหรือไม่
- **Implementation**: ใช้ Actions นอก Scope ที่จะรันเสมอ
- **Use Cases**: 
  - Cleanup Operations
  - Final Notifications
  - Status Updates

---

### 2. Configure Run After และ Retry Policy

#### Configure Run After
- **Setting**: คลิกที่ Action → "Configure run after"
- **Options**:
  - ✅ is successful
  - ✅ is skipped
  - ✅ has failed
  - ✅ has timed out
- **Multiple Conditions**: เลือกหลายเงื่อนไขได้

#### Retry Policy
- **Action Retry**: ตั้งค่า Retry สำหรับแต่ละ Action
- **Retry Types**:
  - **Fixed Interval**: Retry หลังจากเวลาที่กำหนด
  - **Exponential Backoff**: เพิ่มช่วงเวลา Retry แบบ Exponential
- **Retry Count**: จำนวนครั้งที่ Retry
- **Retry Delay**: ระยะเวลาระหว่าง Retry

---

### 3. Error Handling: Failed, Skipped, Timed Out

#### Failed Actions
- **Common Causes**: 
  - Invalid Input
  - API Errors
  - Authentication Failures
  - Network Issues
- **Handling**: 
  - Catch Error
  - Log Details
  - Send Notification
  - Alternative Path

#### Skipped Actions
- **Common Causes**: 
  - Condition ที่ไม่ผ่าน
  - Missing Required Data
  - Predecessor Action Failed
- **Handling**: 
  - Check Skip Reason
  - Provide Default Value
  - Continue Flow

#### Timed Out Actions
- **Common Causes**: 
  - Long-Running Operations
  - Network Latency
  - Resource Unavailability
- **Handling**: 
  - Increase Timeout
  - Retry with Backoff
  - Alternative Approach

---

### 4. Terminate Flow & Asynchronous Actions

#### Terminate Flow
- **Purpose**: หยุด Flow อย่างสมบูรณ์
- **Terminate Options**:
  - **Succeeded**: Terminate เป็น Success
  - **Failed**: Terminate เป็น Failed
  - **Cancelled**: Terminate เป็น Cancelled
- **Use Cases**: 
  - Critical Error ที่ไม่สามารถแก้ไขได้
  - Business Rule Violation
  - Security Breach

#### Asynchronous Actions
- **Run After**: ใช้ Configure Run After เพื่อรัน Action แบบ Async
- **Fire and Forget**: ส่ง Notification โดยไม่รอผลลัพธ์
- **Parallel Processing**: รันหลาย Actions พร้อมกัน

---

### 5. Trigger Conditions & Wait Until Pattern

#### Trigger Conditions
- **Purpose**: เริ่ม Flow เฉพาะเมื่อเงื่อนไขเป็นจริง
- **Implementation**: ตั้งค่าใน Trigger Settings
- **Use Cases**: 
  - Filter Items by Condition
  - Validate Input Before Processing
  - Skip Processing for Certain Cases

#### Wait Until Pattern
- **Purpose**: รอจนกว่าเงื่อนไขจะเป็นจริง
- **Implementation**: 
  - Use Do Until Loop
  - Check Condition
  - Delay Between Checks
  - Timeout Protection
- **Use Cases**: 
  - Wait for File Creation
  - Wait for Status Change
  - Poll API for Results

---

## 📝 Lab Exercises

### Exercise 5a: Forms → Switch Condition → Error Handling

**Exercise นี้อยู่ในไฟล์แยก**: [Exercise-5a-Forms-Switch-Error-Handling.md](./Exercise-5a-Forms-Switch-Error-Handling.md)

**สิ่งที่คุณจะได้ทำ**:
1. ✅ สร้าง Flow ที่ใช้ Switch Condition
2. ✅ ใช้ Error Handling ด้วย Try–Catch–Finally Pattern
3. ✅ ตั้งค่า Retry Policy สำหรับ Actions
4. ✅ จัดการ Error Types ต่างๆ (Failed, Skipped, Timed Out)
5. ✅ ส่ง Notification เมื่อเกิด Error

---

> 📖 **คำแนะนำ**: เปิดไฟล์ Exercise เพื่อดูขั้นตอนการทำแบบละเอียด พร้อมคำอธิบายทุกขั้นตอน

---

## Best Practices

### 1. Error Handling Structure
```
Try:
  - Main Logic
Catch (Failed):
  - Log Error
  - Retry (if applicable)
  - Alternative Path
Catch (Timed Out):
  - Increase Timeout
  - Retry
Catch (Skipped):
  - Check Skip Reason
  - Provide Default
Finally:
  - Cleanup
  - Final Status
```

### 2. Retry Strategy
- **Use Exponential Backoff**: สำหรับ Transient Errors
- **Set Retry Limits**: ป้องกัน Infinite Loops
- **Log Retry Attempts**: ติดตาม Performance
- **Different Strategies**: สำหรับ Error Types ต่างๆ

### 3. Error Logging
- **Log Error Details**: Message, Code, Stack Trace
- **Include Context**: Input Data, Flow Run ID
- **Store Logs**: SharePoint, Database, Log Analytics
- **Error Categories**: Critical, Warning, Info

### 4. Notification Strategy
- **Critical Errors**: Immediate Notification
- **Warnings**: Daily Summary
- **Success**: Optional Notification
- **Escalation**: Notify Manager if Critical

---

## Advanced Error Patterns

### Pattern 1: Circuit Breaker
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

### Pattern 2: Fallback Pattern
```
Try:
  - Call Primary API
Catch:
  - Call Secondary API (Fallback)
  - If Secondary Also Failed: Use Default Value
```

### Pattern 3: Compensation Pattern
```
Try:
  - Create Order
  - Process Payment
Catch:
  - Rollback Order (Compensation)
  - Refund Payment (Compensation)
```

---

## สรุปบทเรียน

หลังจากจบบทนี้ คุณจะสามารถ:
- ✅ ใช้ Try–Catch–Finally Pattern ได้อย่างถูกต้อง
- ✅ ตั้งค่า Run After และ Retry Policy
- ✅ จัดการ Error Types ต่างๆ (Failed, Skipped, Timed Out)
- ✅ ใช้ Terminate Flow และ Asynchronous Actions
- ✅ สร้าง Trigger Conditions และ Wait Until Patterns
- ✅ สร้าง Error Handling ที่แข็งแกร่งและน่าเชื่อถือ

---

## แบบฝึกหัดเพิ่มเติม

1. สร้าง Flow ที่มี Try–Catch–Finally สำหรับการเรียก API
2. สร้าง Flow ที่ใช้ Retry Policy แบบ Exponential Backoff
3. สร้าง Flow ที่มี Circuit Breaker Pattern
4. สร้าง Flow ที่ใช้ Fallback Pattern สำหรับ API Calls
5. สร้าง Flow ที่มี Compensation Pattern สำหรับ Transaction

