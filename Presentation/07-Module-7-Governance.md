# บทที่ 7: Governance, Security & Monitoring

## Slide 1: Title
### บทที่ 7: Governance, Security & Monitoring
**การกำกับดูแล, ความปลอดภัย และการติดตาม**

---

## Slide 2: ภาพรวม
บทนี้สอนให้ Flow "มีมาตรฐาน", "ปลอดภัย" และ "ทำงานได้ดี"

**สิ่งที่คุณจะได้เรียนรู้**:
- Naming Standards (มาตรฐานการตั้งชื่อ)
- Environment Management (จัดการสภาพแวดล้อม)
- Security Roles (บทบาทด้านความปลอดภัย)
- Monitoring & Analytics (การติดตามและวิเคราะห์)
- Debug & Optimization (ดีบักและปรับปรุงประสิทธิภาพ)

---

## Slide 3: Flow Naming Conventions
### มาตรฐานการตั้งชื่อ Flow

**Format**: `[Environment]-[Module]-[Function]-[Version]`

**Examples**:
- `PROD-Finance-PurchaseOrderApproval-v1`
- `DEV-HR-LeaveRequestProcessing-v2`
- `TEST-IT-UserProvisioning-v1`

**Best Practices**:
- ใช้ชื่อที่บอกหน้าที่ชัดเจน
- หลีกเลี่ยงชื่อที่สั้นเกินไปหรือยาวเกินไป
- รวม Version Number

---

## Slide 4: Action Naming
### ตั้งชื่อ Action ให้ชัดเจน

**Format**: `[Action Type] - [Description]`

**Examples**:
- `HTTP Request - Get User Data`
- `Parse JSON - Parse Order Response`
- `Condition - Check Approval Status`

**Best Practices**:
- ตั้งชื่อให้อธิบาย Action
- หลีกเลี่ยงชื่อ Default เช่น "Condition 2"

---

## Slide 5: Variable Naming
### ตั้งชื่อตัวแปรตามมาตรฐาน

**Format**: `[Prefix][Description]`

**Prefixes**:
- `str`: String (e.g., `strCustomerName`)
- `int`: Integer (e.g., `intOrderCount`)
- `bool`: Boolean (e.g., `boolIsApproved`)
- `arr`: Array (e.g., `arrOrderItems`)
- `obj`: Object (e.g., `objOrderData`)

---

## Slide 6: Environments
### จัดการ Environments

**Environment Types**:
- **Development**: สำหรับการพัฒนาและทดสอบ
- **Test/UAT**: สำหรับการทดสอบ User Acceptance
- **Production**: สำหรับการใช้งานจริง

**Best Practices**:
- ใช้ Environment แยกตาม SDLC
- จำกัด Access ตาม Role
- ใช้ Environment Variables สำหรับ Configuration

---

## Slide 7: Solutions
### จัดการและ Deploy Flow

**Purpose**: จัดการและ Deploy Flow ระหว่าง Environments

**Solution Structure**:
- **Components**: Flows, Connections, Variables
- **Dependencies**: External Dependencies
- **Versioning**: Version Management

**Deployment Process**:
1. Create Solution in Source Environment
2. Add Components
3. Export Solution
4. Import to Target Environment
5. Configure Connections and Variables

---

## Slide 8: Environment Variables
### เก็บ Configuration Values

**Use Cases**:
- API Endpoints
- Connection Strings
- Feature Flags
- Threshold Values

**Benefits**:
- Environment-Specific Configuration
- Easy Updates
- Security (Sensitive Data)

---

## Slide 9: Security Roles
### บทบาทด้านความปลอดภัย

**Security Roles**:
- **Flow Owner**: เจ้าของ Flow ที่มีสิทธิ์แก้ไข
- **Flow User**: ผู้ใช้ Flow ที่สามารถรันได้
- **Environment Admin**: ผู้ดูแล Environment
- **Solution Admin**: ผู้ดูแล Solution

---

## Slide 10: Access Control
### สิทธิ์การเข้าถึง

**Share Flow**: แชร์ Flow กับ Users หรือ Groups

**Permissions**:
- **Can Edit**: แก้ไข Flow ได้
- **Can View**: ดู Flow ได้
- **Can Run**: รัน Flow ได้

**Best Practices**:
- ใช้ Principle of Least Privilege
- ใช้ Groups แทน Individual Users
- Review Permissions เป็นประจำ

---

## Slide 11: Data Security
### ความปลอดภัยของข้อมูล

**Best Practices**:
- **Sensitive Data**: อย่าเก็บ Sensitive Data ใน Flow
- **Connection Security**: ใช้ Secure Connections
- **Data Encryption**: ใช้ Encryption สำหรับ Data in Transit
- **Audit Logging**: เปิด Audit Logging

---

## Slide 12: Flow Checker
### ตรวจสอบ Flow ก่อน Save

**Checks**:
- Syntax Errors
- Missing Required Fields
- Invalid Expressions
- Connection Issues

**Best Practices**:
- Run Flow Checker ก่อน Save
- แก้ไข Errors ทันที
- Review Warnings

---

## Slide 13: Analytics
### Flow Run History

**Key Metrics**:
- **Success Rate**: อัตราความสำเร็จ
- **Failure Rate**: อัตราความล้มเหลว
- **Average Duration**: ระยะเวลาเฉลี่ย
- **Peak Times**: เวลาที่ใช้งานมากที่สุด

---

## Slide 14: Performance Metrics
### ตัวชี้วัดประสิทธิภาพ

**Metrics**:
- **Execution Time**: เวลาที่ใช้ในการรัน
- **Action Duration**: ระยะเวลาของแต่ละ Action
- **API Call Count**: จำนวนครั้งที่เรียก API
- **Error Frequency**: ความถี่ในการเกิด Error

---

## Slide 15: Monitoring Dashboard
### Dashboard สำหรับติดตาม

**Key Metrics**:
- Total Runs
- Success Rate
- Failure Rate
- Average Duration

**Alerts**:
- High Failure Rate
- Long Execution Time
- Unusual Activity

---

## Slide 16: Debug Techniques
### เทคนิคการ Debug

**Run History**:
- ดูรายละเอียดการรันแต่ละครั้ง
- Input/Output ของแต่ละ Action
- Execution Time
- Error Messages

**Test Mode**: ทดสอบ Flow โดยไม่ส่งผลกระทบต่อ Production

---

## Slide 17: Optimization: ลด Nested Loops
### Before vs After

**Before (Inefficient)**:
```
Apply to Each: items
  Apply to Each: subItems
    Apply to Each: details
      Process Detail
```

**After (Optimized)**:
```
Filter Array: items where status = "Active"
Select: Transform to required format
Apply to Each: filteredItems
  Process Item
```

---

## Slide 18: Optimization: Filter Array
### Before vs After

**Before (Inefficient)**:
```
Apply to Each: allItems
  Condition: If item.status = "Active"
    Process Item
```

**After (Optimized)**:
```
Filter Array: allItems where status = "Active"
Apply to Each: activeItems
  Process Item
```

---

## Slide 19: Optimization: Parallel Processing
### Before vs After

**Before (Sequential)**:
```
Action 1 → Wait
Action 2 → Wait
Action 3 → Wait
```

**After (Parallel)**:
```
Parallel:
  Action 1
  Action 2
  Action 3
```

---

## Slide 20: Best Practices Summary
### สรุป Best Practices

**1. Naming Standards**:
- ใช้ Consistent Naming Convention
- ตั้งชื่อให้บอกหน้าที่ชัดเจน

**2. Environment Management**:
- แยก Development, Test, Production
- ใช้ Solutions สำหรับ Deployment

**3. Security**:
- ใช้ Principle of Least Privilege
- อย่าเก็บ Sensitive Data ใน Flow

---

## Slide 21: Best Practices Summary (ต่อ)
### สรุป Best Practices (ต่อ)

**4. Monitoring**:
- ตรวจสอบ Flow Run History
- ตั้งค่า Alerts สำหรับ Errors

**5. Optimization**:
- ลด Nested Loops
- ใช้ Filter Array
- ใช้ Parallel Processing
- ใช้ Select แทน Loop เมื่อเป็นไปได้

---

## Slide 22: สรุปบทเรียน
หลังจากจบบทนี้ คุณจะสามารถ:

- ✅ ตั้งชื่อ Flow, Action, Variable อย่างเป็นมาตรฐาน
- ✅ จัดการ Environments และ Solutions
- ✅ กำหนด Security Roles และสิทธิ์การเข้าถึง
- ✅ ใช้ Flow Checker และ Analytics
- ✅ Debug และ Optimize Flow

---

## Slide 23: สรุปหลักสูตร
### Power Automate Advanced

**สิ่งที่ได้เรียนรู้ทั้งหมด**:
1. ✅ JSON in Workflows
2. ✅ Advanced Fundamentals
3. ✅ Flow Controls
4. ✅ Advanced Approvals
5. ✅ Error Handling
6. ✅ Parent–Child Flows
7. ✅ Governance, Security & Monitoring

---

## Slide 24: ขอบคุณ
### Thank You!

**พร้อมสำหรับการสร้าง Flow ขั้นสูงแล้ว!**

**คำถาม?**


