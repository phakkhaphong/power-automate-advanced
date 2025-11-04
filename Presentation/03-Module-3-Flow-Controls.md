# บทที่ 3: Flow Controls & Advanced Workflow

## Slide 1: Title
### บทที่ 3: Flow Controls & Advanced Workflow
**การควบคุม Flow ที่ใช้สร้าง Logic ที่ซับซ้อน**

---

## Slide 2: ภาพรวม
บทนี้สอนให้ Flow "คิดเป็น" และ "ทำซ้ำ" ได้

**สิ่งที่คุณจะได้เรียนรู้**:
- Conditions (เงื่อนไข): If-Else, Switch
- Loops (วนลูป): Apply to Each, Do Until
- Scopes (ขอบเขต): จัดกลุ่ม Actions
- Parallel Processing (ประมวลผลแบบขนาน)

---

## Slide 3: If Condition
### ถ้า...แล้ว...

**If Condition** = ตรวจสอบเงื่อนไข ถ้าเป็นจริง → ทำอย่างหนึ่ง, ถ้าไม่จริง → ทำอีกอย่างหนึ่ง

**ตัวอย่างง่ายๆ**:
```
ถ้าเงินเดือน > 50000
  → ส่งไปอนุมัติ Manager
ถ้าไม่ใช่
  → อนุมัติอัตโนมัติ
```

---

## Slide 4: Multiple Conditions
### เงื่อนไขหลายข้อ

**ใช้ `and`**: ทั้งหมดต้องเป็นจริง
```
เงินเดือน > 50000 AND แผนก = "ไอที"
```

**ใช้ `or`**: มีอันใดอันหนึ่งเป็นจริง
```
เงินเดือน > 50000 OR เป็น Manager
```

---

## Slide 5: Switch Condition
### Switch = ใช้เมื่อมีหลาย Case (กรณี)

**ตัวอย่าง**:
```
Switch: สถานะ
  Case "รออนุมัติ" → ส่ง Email
  Case "อนุมัติแล้ว" → อัพเดทฐานข้อมูล
  Case "ปฏิเสธ" → ส่ง Notification
  Default → ไม่ทำอะไร
```

**เมื่อไหร่ควรใช้**: เมื่อมีหลาย Case ที่เท่ากัน

---

## Slide 6: Apply to Each
### วนลูปผ่าน Array

**Apply to Each** = วนลูปผ่านทุก item ใน Array

**ตัวอย่าง**:
```
มีนักเรียน 10 คน
วนลูป 10 ครั้ง:
  รอบที่ 1: ส่ง Email ให้นักเรียนคนที่ 1
  รอบที่ 2: ส่ง Email ให้นักเรียนคนที่ 2
  ...
```

**item()** = ข้อมูลในรอบปัจจุบัน

---

## Slide 7: Nested Loops
### ระวัง! อาจทำให้ช้ามาก

**ตัวอย่าง**:
- วนลูปนักเรียน 10 คน → วนลูปวิชา 5 วิชา = **50 รอบ!**

**Concurrency** (ทำพร้อมกัน):
- ตั้งค่าให้ทำหลายอันพร้อมกัน (เร็วกว่า)
- แต่ระวัง Rate Limits!

---

## Slide 8: Do Until
### ทำจนกว่า...

**Do Until** = วนลูปจนกว่าเงื่อนไขจะเป็นจริง

**ตัวอย่าง**:
```
วนลูป:
  - เรียก API
  - ตรวจสอบสถานะ
  - ถ้าสถานะ = "เสร็จสิ้น" → หยุด
  - ถ้ายังไม่เสร็จ → วนลูปต่อไป
```

---

## Slide 9: Timeout & Iteration Limit
### ป้องกัน Infinite Loop

**Timeout** (หมดเวลา):
- ตั้งค่า Timeout เพื่อป้องกัน Infinite Loop
- ถ้าเกินเวลาที่กำหนด → หยุด

**Iteration Limit** (จำกัดจำนวนครั้ง):
- จำกัดจำนวนครั้งที่วนลูป (เช่น 10 ครั้ง)
- ป้องกัน Loop ไม่รู้จบ

---

## Slide 10: Scope & Action Grouping
### Scope Action

**Purpose**: จัดกลุ่ม Actions และจัดการ Error Handling

**ประโยชน์**:
- **Error Isolation**: แยก Error Handling ในแต่ละ Scope
- **Variable Scope**: ตัวแปรที่สร้างใน Scope ใช้ได้ใน Scope นั้น
- **Visual Organization**: ทำให้ Flow อ่านง่ายขึ้น

---

## Slide 11: Parallel Branching
### Parallel Execution

**ทำหลาย Actions พร้อมกัน**

**Use Cases**:
- เรียก API หลายตัวพร้อมกัน
- ประมวลผลข้อมูลหลายชุด

**ข้อดี**: เร็วกว่าการทำตามลำดับ

**ข้อควรระวัง**: ต้อง Synchronize ผลลัพธ์

---

## Slide 12: Delay & Timeout Patterns
### Delay Action

**Fixed Delay**: หน่วงเวลาตามจำนวนวินาที

**Dynamic Delay**: ใช้ Expression เพื่อคำนวณเวลา Delay

**Use Cases**:
- Retry Logic
- Rate Limiting
- Scheduled Processing

---

## Slide 13: Advanced Patterns: Retry
### Retry Pattern

```
Initialize Variable: retryCount = 0
Do Until: success = true OR retryCount > 3
  - Try Action
  - If Failed: Increment retryCount, Delay 10 seconds
```

**ใช้เมื่อ**: Action อาจล้มเหลวชั่วคราว

---

## Slide 14: Advanced Patterns: Batch Processing
### Batch Processing

```
Initialize Variable: batchSize = 10
Apply to Each: items
  - Add to Batch
  - If Batch Full: Process Batch, Clear Batch
```

**ใช้เมื่อ**: มีข้อมูลจำนวนมาก ต้องประมวลผลเป็นกลุ่ม

---

## Slide 15: Performance Optimization
### 1. ลด Nested Loops
- ใช้ Filter Array ก่อน Loop
- ใช้ Select เพื่อ Transform Data
- ใช้ Parallel Processing เมื่อเป็นไปได้

### 2. Concurrency Control
- ตั้งค่า Concurrency สำหรับ Apply to Each
- ใช้ Parallel Branches สำหรับ Independent Tasks

### 3. Timeout Management
- ตั้งค่า Timeout ที่เหมาะสม
- ใช้ Retry Logic สำหรับ Transient Errors

---

## Slide 16: สรุปบทเรียน
หลังจากจบบทนี้ คุณจะสามารถ:

- ✅ ใช้ If–Else และ Switch Conditions ได้อย่างมีประสิทธิภาพ
- ✅ ใช้ Apply to Each และ Do Until Loops
- ✅ จัดกลุ่ม Actions ด้วย Scope
- ✅ สร้าง Parallel Branches
- ✅ ปรับปรุง Performance ของ Flow

---

## Slide 17: Next Steps
**พร้อมสำหรับบทต่อไป!**

**บทที่ 4**: Advanced Approvals
- Approval Components
- Multi-Level Approval
- Use Cases

**คำถาม?**


