# บทที่ 7: Governance, Security & Monitoring

## 🎯 ภาพรวม

บทนี้จะเรียนรู้เกี่ยวกับ **Governance, Security & Monitoring** (การกำกับดูแล, ความปลอดภัย และการติดตาม) สำหรับ Power Automate ในระดับองค์กร

**สิ่งที่คุณจะได้เรียนรู้**:
- ✅ Naming Standards (มาตรฐานการตั้งชื่อ)
- ✅ Environment Management (จัดการสภาพแวดล้อม)
- ✅ Security Roles (บทบาทด้านความปลอดภัย)
- ✅ Monitoring & Analytics (การติดตามและวิเคราะห์)
- ✅ Debug & Optimization (ดีบักและปรับปรุงประสิทธิภาพ)

> 💡 **คิดง่ายๆ**: บทนี้สอนให้ Flow "มีมาตรฐาน", "ปลอดภัย" และ "ทำงานได้ดี"

---

## เนื้อหาการเรียนรู้

### 1. การตั้งชื่อ Flow, Action, Variable อย่างเป็นมาตรฐาน

#### Flow Naming Conventions
- **Format**: `[Environment]-[Module]-[Function]-[Version]`
- **Examples**:
  - `PROD-Finance-PurchaseOrderApproval-v1`
  - `DEV-HR-LeaveRequestProcessing-v2`
  - `TEST-IT-UserProvisioning-v1`
- **Best Practices**:
  - ใช้ชื่อที่บอกหน้าที่ชัดเจน
  - หลีกเลี่ยงชื่อที่สั้นเกินไปหรือยาวเกินไป
  - ใช้ Consistent Format
  - รวม Version Number

#### Action Naming
- **Format**: `[Action Type] - [Description]`
- **Examples**:
  - `HTTP Request - Get User Data`
  - `Parse JSON - Parse Order Response`
  - `Condition - Check Approval Status`
- **Best Practices**:
  - ตั้งชื่อให้อธิบาย Action
  - หลีกเลี่ยงชื่อ Default เช่น "Condition 2"
  - ใช้ชื่อที่เข้าใจง่าย

#### Variable Naming
- **Format**: `[Prefix][Description]`
- **Prefixes**:
  - `str`: String (e.g., `strCustomerName`)
  - `int`: Integer (e.g., `intOrderCount`)
  - `bool`: Boolean (e.g., `boolIsApproved`)
  - `arr`: Array (e.g., `arrOrderItems`)
  - `obj`: Object (e.g., `objOrderData`)
- **Best Practices**:
  - ใช้ Prefix ที่บอก Data Type
  - ใช้ชื่อที่อธิบายตัวแปร
  - ใช้ camelCase หรือ PascalCase

---

### 2. การใช้ Environments & Solutions

#### Environments
- **Purpose**: แยก Development, Testing, และ Production
- **Environment Types**:
  - **Development**: สำหรับการพัฒนาและทดสอบ
  - **Test/UAT**: สำหรับการทดสอบ User Acceptance
  - **Production**: สำหรับการใช้งานจริง
- **Best Practices**:
  - ใช้ Environment แยกตาม SDLC
  - จำกัด Access ตาม Role
  - ใช้ Environment Variables สำหรับ Configuration

#### Solutions
- **Purpose**: จัดการและ Deploy Flow ระหว่าง Environments
- **Solution Structure**:
  - **Components**: Flows, Connections, Variables
  - **Dependencies**: External Dependencies
  - **Versioning**: Version Management
- **Deployment Process**:
  1. Create Solution in Source Environment
  2. Add Components
  3. Export Solution
  4. Import to Target Environment
  5. Configure Connections and Variables

#### Environment Variables
- **Purpose**: เก็บ Configuration Values
- **Use Cases**:
  - API Endpoints
  - Connection Strings
  - Feature Flags
  - Threshold Values
- **Benefits**:
  - Environment-Specific Configuration
  - Easy Updates
  - Security (Sensitive Data)

---

### 3. Security Role และสิทธิ์การเข้าถึง

#### Security Roles
- **Flow Owner**: เจ้าของ Flow ที่มีสิทธิ์แก้ไข
- **Flow User**: ผู้ใช้ Flow ที่สามารถรันได้
- **Environment Admin**: ผู้ดูแล Environment
- **Solution Admin**: ผู้ดูแล Solution

#### Access Control
- **Share Flow**: แชร์ Flow กับ Users หรือ Groups
- **Permissions**:
  - **Can Edit**: แก้ไข Flow ได้
  - **Can View**: ดู Flow ได้
  - **Can Run**: รัน Flow ได้
- **Best Practices**:
  - ใช้ Principle of Least Privilege
  - ใช้ Groups แทน Individual Users
  - Review Permissions เป็นประจำ

#### Data Security
- **Sensitive Data**: อย่าเก็บ Sensitive Data ใน Flow
- **Connection Security**: ใช้ Secure Connections
- **Data Encryption**: ใช้ Encryption สำหรับ Data in Transit
- **Audit Logging**: เปิด Audit Logging

---

### 4. Flow Checker & Analytics

#### Flow Checker
- **Purpose**: ตรวจสอบ Flow ก่อน Save
- **Checks**:
  - Syntax Errors
  - Missing Required Fields
  - Invalid Expressions
  - Connection Issues
- **Best Practices**:
  - Run Flow Checker ก่อน Save
  - แก้ไข Errors ทันที
  - Review Warnings

#### Analytics
- **Flow Run History**: ดูประวัติการรัน Flow
  - Success Rate
  - Failure Rate
  - Average Duration
  - Peak Times
- **Performance Metrics**:
  - Execution Time
  - Action Duration
  - API Call Count
  - Error Frequency
- **Usage Analytics**:
  - Flow Usage by User
  - Flow Usage by Time
  - Popular Flows

#### Monitoring Dashboard
- **Key Metrics**:
  - Total Runs
  - Success Rate
  - Failure Rate
  - Average Duration
- **Alerts**:
  - High Failure Rate
  - Long Execution Time
  - Unusual Activity

---

### 5. Debug และ Optimization

#### Debug Techniques
- **Run History**: ดูรายละเอียดการรันแต่ละครั้ง
  - Input/Output ของแต่ละ Action
  - Execution Time
  - Error Messages
- **Test Mode**: ทดสอบ Flow โดยไม่ส่งผลกระทบต่อ Production
- **Breakpoints**: ใช้ Terminate เพื่อหยุด Flow ณ จุดที่กำหนด

#### Optimization Strategies

##### 1. ลด Nested Loops
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

##### 2. ใช้ Filter Array
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

##### 3. ใช้ Parallel Processing
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

##### 4. ใช้ Select แทน Loop
**Before (Loop)**:
```
Initialize Array: result
Apply to Each: items
  Append to Array: Transform item
```

**After (Select)**:
```
Select: Transform items to new format
```

##### 5. Cache Results
```
Initialize Variable: cachedData
Condition: If cachedData is empty
  - Get Data
  - Set Variable: cachedData
Else
  - Use cachedData
```

---

## Best Practices Summary

### 1. Naming Standards
- ✅ ใช้ Consistent Naming Convention
- ✅ ตั้งชื่อให้บอกหน้าที่ชัดเจน
- ✅ หลีกเลี่ยงชื่อ Default
- ✅ รวม Version Number

### 2. Environment Management
- ✅ แยก Development, Test, Production
- ✅ ใช้ Solutions สำหรับ Deployment
- ✅ ใช้ Environment Variables
- ✅ จำกัด Access ตาม Role

### 3. Security
- ✅ ใช้ Principle of Least Privilege
- ✅ Review Permissions เป็นประจำ
- ✅ อย่าเก็บ Sensitive Data ใน Flow
- ✅ ใช้ Secure Connections

### 4. Monitoring
- ✅ ตรวจสอบ Flow Run History
- ✅ ตั้งค่า Alerts สำหรับ Errors
- ✅ Review Performance Metrics
- ✅ Track Usage Analytics

### 5. Optimization
- ✅ ลด Nested Loops
- ✅ ใช้ Filter Array
- ✅ ใช้ Parallel Processing
- ✅ ใช้ Select แทน Loop เมื่อเป็นไปได้
- ✅ Cache Results เมื่อเหมาะสม

---

## สรุปบทเรียน

หลังจากจบบทนี้ คุณจะสามารถ:
- ✅ ตั้งชื่อ Flow, Action, Variable อย่างเป็นมาตรฐาน
- ✅ จัดการ Environments และ Solutions
- ✅ กำหนด Security Roles และสิทธิ์การเข้าถึง
- ✅ ใช้ Flow Checker และ Analytics
- ✅ Debug และ Optimize Flow
- ✅ สร้าง Flow ที่พร้อมสำหรับ Production

---

## แบบฝึกหัดเพิ่มเติม

1. สร้าง Naming Convention Document สำหรับองค์กร
2. สร้าง Solution และ Deploy ไปยัง Environment อื่น
3. ตั้งค่า Environment Variables สำหรับ Configuration
4. สร้าง Monitoring Dashboard สำหรับ Flow Analytics
5. Optimize Flow ที่มี Performance Issues
6. ตั้งค่า Security Roles และ Permissions
7. Review และ Update Flow Documentation

