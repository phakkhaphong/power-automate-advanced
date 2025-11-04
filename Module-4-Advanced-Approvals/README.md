# บทที่ 4: Advanced Approvals

## 🎯 ภาพรวม

บทนี้จะเรียนรู้เกี่ยวกับการสร้าง **Approval Workflows** (เวิร์กโฟลว์การอนุมัติ) ที่รองรับการทำงานจริงในองค์กร

**สิ่งที่คุณจะได้เรียนรู้**:
- ✅ Approval Components: Action, Card, Response
- ✅ Multi-Level Approval (อนุมัติหลายระดับ)
- ✅ Use Cases: Purchase Order, Leave Request
- ✅ การเข้าถึงผ่านหลายช่องทาง: Portal, Teams, Mobile

> 💡 **คิดง่ายๆ**: บทนี้สอนให้ Flow "ขออนุมัติ" และ "รอคำตอบ" ได้

---

## เนื้อหาการเรียนรู้

### 1. Approval Components: Action, Card, Condition on Response

#### Approval Action
- **Start and wait for an approval**: สร้าง Approval Request และรอ Response
- **Approval Types**: 
  - **Approve/Reject**: อนุมัติหรือปฏิเสธ
  - **Custom Responses**: หลายตัวเลือก
  - **First to Respond**: ใครตอบก่อนถือว่าใช้ได้
  - **All Must Approve**: ต้องอนุมัติทั้งหมด
- **Approval Settings**: 
  - Assign To: ผู้รับผิดชอบ
  - Due Date: กำหนดวันครบกำหนด
  - Instructions: คำแนะนำสำหรับผู้อนุมัติ

#### Approval Card
- **Customize Approval Card**: ปรับแต่ง UI ของ Approval Card
- **Dynamic Content**: แสดงข้อมูล Dynamic ใน Card
- **Attachments**: แนบไฟล์ใน Approval Request
- **Rich Text**: ใช้ HTML Formatting

#### Condition on Response
- **Check Approval Response**: ตรวจสอบผลการอนุมัติ
- **Handle Approve**: จัดการเมื่ออนุมัติ
- **Handle Reject**: จัดการเมื่อปฏิเสธ
- **Handle Custom Response**: จัดการ Response แบบ Custom

---

### 2. Use Cases: Purchase Order, Leave Request

#### Use Case 1: Purchase Order Approval
- **Workflow**:
  1. User สร้าง Purchase Order Request
  2. Send Approval Request ไปยัง Manager
  3. Manager อนุมัติ/ปฏิเสธ
  4. หากอนุมัติ: สร้าง PO ในระบบ
  5. หากปฏิเสธ: แจ้งเตือน User และเก็บ Reason
- **Multi-Level Approval**:
  - Level 1: Department Manager (สำหรับ PO < 10,000)
  - Level 2: Finance Manager (สำหรับ PO >= 10,000)
  - Level 3: CFO (สำหรับ PO >= 100,000)

#### Use Case 2: Leave Request Approval
- **Workflow**:
  1. Employee สร้าง Leave Request
  2. Send Approval Request ไปยัง Direct Manager
  3. Manager อนุมัติ/ปฏิเสธ
  4. หากอนุมัติ: อัพเดท Leave Balance และ Calendar
  5. ส่ง Email Confirmation
- **Additional Features**:
  - ตรวจสอบ Leave Balance
  - ตรวจสอบ Conflict กับ Leave อื่นๆ
  - แจ้งเตือน Team Members

---

### 3. Process Approvals ผ่าน Portal, Teams, Mobile

#### Portal Access
- **Power Automate Portal**: เข้าถึงผ่านเว็บ
- **Mobile App**: เข้าถึงผ่าน Mobile App
- **Email**: อนุมัติผ่าน Email Link
- **Approval Center**: ศูนย์กลางการจัดการ Approvals

#### Microsoft Teams Integration
- **Teams Approval**: อนุมัติผ่าน Teams
- **Teams Adaptive Cards**: ใช้ Adaptive Cards ใน Teams
- **Teams Notifications**: แจ้งเตือนใน Teams Channel
- **Teams Bot**: ใช้ Bot สำหรับ Approval

#### Mobile Access
- **Power Automate Mobile App**: อนุมัติผ่าน Mobile
- **Push Notifications**: แจ้งเตือนเมื่อมี Approval
- **Offline Support**: อนุมัติได้แม้ไม่มี Internet (Sync ทีหลัง)

---

## 📝 Lab Exercise

### Lab 2: Advanced Approval Flow (Purchase Order/Leave Request)

**Exercise นี้อยู่ในไฟล์แยก**: [Exercise-2-Advanced-Approval-Flow.md](./Exercise-2-Advanced-Approval-Flow.md)

**สิ่งที่คุณจะได้ทำ**:
1. ✅ สร้าง Approval Flow ที่รองรับหลายระดับ
2. ✅ จัดการ Approval Response (อนุมัติ/ปฏิเสธ)
3. ✅ ส่ง Notification ไปยังผู้เกี่ยวข้อง
4. ✅ จัดการ Error Cases

> 📖 **คำแนะนำ**: เปิดไฟล์ Exercise เพื่อดูขั้นตอนการทำแบบละเอียด พร้อมคำอธิบายทุกขั้นตอน

---

## Advanced Approval Patterns

### Pattern 1: Parallel Approval
```
Start Approval: Send to Manager 1, Manager 2, Manager 3
Type: First to Respond
If Approved: Process Request
```

### Pattern 2: Sequential Approval
```
Step 1: Manager Approval
Step 2: Finance Approval (only if Manager approved)
Step 3: Executive Approval (only if Finance approved)
```

### Pattern 3: Conditional Approval
```
If amount < 1000: Auto Approve
Else If amount < 10000: Manager Approval
Else: Multi-Level Approval
```

### Pattern 4: Escalation Pattern
```
Approval Request → Wait 2 days
If No Response: Send Reminder
Wait 1 more day
If Still No Response: Escalate to Manager's Manager
```

---

## Best Practices

1. **Clear Instructions**
   - ให้คำแนะนำที่ชัดเจนใน Approval Request
   - แสดงข้อมูลที่จำเป็นทั้งหมด

2. **Due Dates**
   - ตั้ง Due Date ที่เหมาะสม
   - ส่ง Reminder ก่อน Due Date

3. **Error Handling**
   - จัดการ Timeout และ Skipped Approvals
   - ส่ง Notification เมื่อเกิดปัญหา

4. **Audit Trail**
   - บันทึก Approval History
   - เก็บ Reason สำหรับ Rejection

5. **Notifications**
   - แจ้งเตือน Requestor เมื่อมีการอนุมัติ/ปฏิเสธ
   - แจ้งเตือน Approver เมื่อมี Request ใหม่

---

## สรุปบทเรียน

หลังจากจบบทนี้ คุณจะสามารถ:
- ✅ สร้าง Approval Workflows ที่ซับซ้อน
- ✅ ใช้ Approval Components ต่างๆ
- ✅ สร้าง Multi-Level Approval
- ✅ จัดการ Approval ผ่านหลายช่องทาง
- ✅ สร้าง Approval Flow สำหรับ Use Cases จริง
- ✅ จัดการ Error Cases ใน Approval Flow

---

## แบบฝึกหัดเพิ่มเติม

1. สร้าง Leave Request Approval Flow พร้อม Multi-Level Approval
2. สร้าง Expense Approval Flow ที่ตรวจสอบ Policy
3. สร้าง Approval Flow ที่ใช้ Parallel Approval
4. สร้าง Approval Flow ที่มี Escalation Logic
5. สร้าง Approval Flow ที่เชื่อมต่อกับ Teams

