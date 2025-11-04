# บทที่ 4: Advanced Approvals

## Slide 1: Title
### บทที่ 4: Advanced Approvals
**สร้าง Approval Workflows ที่รองรับการทำงานจริงในองค์กร**

---

## Slide 2: ภาพรวม
บทนี้สอนให้ Flow "ขออนุมัติ" และ "รอคำตอบ" ได้

**สิ่งที่คุณจะได้เรียนรู้**:
- Approval Components: Action, Card, Response
- Multi-Level Approval (อนุมัติหลายระดับ)
- Use Cases: Purchase Order, Leave Request
- การเข้าถึงผ่านหลายช่องทาง: Portal, Teams, Mobile

---

## Slide 3: Approval Action
### Start and wait for an approval

**Approval Types**:
- **Approve/Reject**: อนุมัติหรือปฏิเสธ
- **Custom Responses**: หลายตัวเลือก
- **First to Respond**: ใครตอบก่อนถือว่าใช้ได้
- **All Must Approve**: ต้องอนุมัติทั้งหมด

**Approval Settings**:
- Assign To: ผู้รับผิดชอบ
- Due Date: กำหนดวันครบกำหนด
- Instructions: คำแนะนำสำหรับผู้อนุมัติ

---

## Slide 4: Approval Card
### Customize Approval Card

**ปรับแต่ง UI ของ Approval Card**:
- **Dynamic Content**: แสดงข้อมูล Dynamic ใน Card
- **Attachments**: แนบไฟล์ใน Approval Request
- **Rich Text**: ใช้ HTML Formatting

**ตัวอย่าง**:
```
Title: Purchase Order Approval Request
Content:
  PO Number: {poNumber}
  Amount: {poAmount}
  Requestor: {requestor}
```

---

## Slide 5: Condition on Response
### ตรวจสอบผลการอนุมัติ

**การจัดการ Response**:
- **Handle Approve**: จัดการเมื่ออนุมัติ
- **Handle Reject**: จัดการเมื่อปฏิเสธ
- **Handle Custom Response**: จัดการ Response แบบ Custom

**ตัวอย่าง**:
```
If approvalResponse = "Approve"
  Then: Continue to next level
Else If approvalResponse = "Reject"
  Then: Send rejection email, Terminate flow
```

---

## Slide 6: Use Case 1: Purchase Order
### Purchase Order Approval Workflow

**Workflow**:
1. User สร้าง Purchase Order Request
2. Send Approval Request ไปยัง Manager
3. Manager อนุมัติ/ปฏิเสธ
4. หากอนุมัติ: สร้าง PO ในระบบ
5. หากปฏิเสธ: แจ้งเตือน User และเก็บ Reason

---

## Slide 7: Multi-Level Approval
### ระดับการอนุมัติตามจำนวนเงิน

**Level 1**: Department Manager (สำหรับ PO < 10,000)  
**Level 2**: Finance Manager (สำหรับ PO >= 10,000 และ < 100,000)  
**Level 3**: CFO (สำหรับ PO >= 100,000)

**Logic**:
```
If poAmount < 10000 → Level 1
Else If poAmount < 100000 → Level 2
Else → Level 3
```

---

## Slide 8: Use Case 2: Leave Request
### Leave Request Approval Workflow

**Workflow**:
1. Employee สร้าง Leave Request
2. Send Approval Request ไปยัง Direct Manager
3. Manager อนุมัติ/ปฏิเสธ
4. หากอนุมัติ: อัพเดท Leave Balance และ Calendar
5. ส่ง Email Confirmation

**Additional Features**:
- ตรวจสอบ Leave Balance
- ตรวจสอบ Conflict กับ Leave อื่นๆ
- แจ้งเตือน Team Members

---

## Slide 9: Portal Access
### การเข้าถึงผ่านหลายช่องทาง

**Portal Access**:
- **Power Automate Portal**: เข้าถึงผ่านเว็บ
- **Mobile App**: เข้าถึงผ่าน Mobile App
- **Email**: อนุมัติผ่าน Email Link
- **Approval Center**: ศูนย์กลางการจัดการ Approvals

---

## Slide 10: Microsoft Teams Integration
### Teams Approval

**Teams Integration**:
- **Teams Approval**: อนุมัติผ่าน Teams
- **Teams Adaptive Cards**: ใช้ Adaptive Cards ใน Teams
- **Teams Notifications**: แจ้งเตือนใน Teams Channel
- **Teams Bot**: ใช้ Bot สำหรับ Approval

---

## Slide 11: Mobile Access
### Power Automate Mobile App

**Mobile Features**:
- อนุมัติผ่าน Mobile
- **Push Notifications**: แจ้งเตือนเมื่อมี Approval
- **Offline Support**: อนุมัติได้แม้ไม่มี Internet (Sync ทีหลัง)

---

## Slide 12: Advanced Patterns: Parallel Approval
### Pattern 1: Parallel Approval

```
Start Approval: Send to Manager 1, Manager 2, Manager 3
Type: First to Respond
If Approved: Process Request
```

**ใช้เมื่อ**: ต้องการให้ Manager ใดคนหนึ่งอนุมัติ

---

## Slide 13: Advanced Patterns: Sequential Approval
### Pattern 2: Sequential Approval

```
Step 1: Manager Approval
Step 2: Finance Approval (only if Manager approved)
Step 3: Executive Approval (only if Finance approved)
```

**ใช้เมื่อ**: ต้องอนุมัติตามลำดับ

---

## Slide 14: Advanced Patterns: Conditional Approval
### Pattern 3: Conditional Approval

```
If amount < 1000: Auto Approve
Else If amount < 10000: Manager Approval
Else: Multi-Level Approval
```

**ใช้เมื่อ**: ต้องการ Auto Approve สำหรับจำนวนเงินน้อย

---

## Slide 15: Advanced Patterns: Escalation
### Pattern 4: Escalation Pattern

```
Approval Request → Wait 2 days
If No Response: Send Reminder
Wait 1 more day
If Still No Response: Escalate to Manager's Manager
```

**ใช้เมื่อ**: ต้องการติดตามและ Escalate

---

## Slide 16: Best Practices
### 1. Clear Instructions
- ให้คำแนะนำที่ชัดเจนใน Approval Request
- แสดงข้อมูลที่จำเป็นทั้งหมด

### 2. Due Dates
- ตั้ง Due Date ที่เหมาะสม
- ส่ง Reminder ก่อน Due Date

### 3. Error Handling
- จัดการ Timeout และ Skipped Approvals
- ส่ง Notification เมื่อเกิดปัญหา

---

## Slide 17: Lab Exercise
### Lab 2: Advanced Approval Flow

**สิ่งที่คุณจะได้ทำ**:
1. สร้าง Approval Flow ที่รองรับหลายระดับ
2. จัดการ Approval Response (อนุมัติ/ปฏิเสธ)
3. ส่ง Notification ไปยังผู้เกี่ยวข้อง
4. จัดการ Error Cases

---

## Slide 18: สรุปบทเรียน
หลังจากจบบทนี้ คุณจะสามารถ:

- ✅ สร้าง Approval Workflows ที่ซับซ้อน
- ✅ ใช้ Approval Components ต่างๆ
- ✅ สร้าง Multi-Level Approval
- ✅ จัดการ Approval ผ่านหลายช่องทาง
- ✅ จัดการ Error Cases ใน Approval Flow

---

## Slide 19: Next Steps
**พร้อมสำหรับบทต่อไป!**

**บทที่ 5**: Best Practices & Error Handling
- Try-Catch-Finally Pattern
- Retry Policy
- Error Recovery Patterns

**คำถาม?



