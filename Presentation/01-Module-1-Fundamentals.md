# บทที่ 2: Advanced Fundamentals

## Slide 1: Title
### บทที่ 2: Advanced Fundamentals
**ความรู้พื้นฐานที่สำคัญสำหรับ Flow ขั้นสูง**

---

## Slide 2: ภาพรวม
บทนี้เหมือนเรียนคำศัพท์พื้นฐานก่อนที่จะสร้างประโยคยาวๆ

**สิ่งที่คุณจะได้เรียนรู้**:
- Triggers และ Actions ที่ใช้งานจริง
- Variables (ตัวแปร) สำหรับเก็บข้อมูล
- Expressions (นิพจน์) สำหรับคำนวณและจัดการข้อมูล
- Data Types (ประเภทข้อมูล) ต่างๆ

---

## Slide 3: Trigger Types
### Trigger = สิ่งที่ทำให้ Flow เริ่มทำงาน

**ประเภทของ Trigger**:
- **Manual Trigger**: กดปุ่มเพื่อรัน Flow (ใช้สำหรับทดสอบ)
- **Scheduled Trigger**: รัน Flow อัตโนมัติตามเวลา (เช่น ทุกวัน 9 นาฬิกา)
- **Event-based Triggers**: รัน Flow เมื่อมีเหตุการณ์เกิดขึ้น (เช่น ได้รับ Email, ไฟล์ใหม่)

**Trigger Conditions**: ตั้งเงื่อนไขว่า Flow จะเริ่มเมื่อไหร่

---

## Slide 4: Action Fundamentals
### Action = คำสั่งที่ Flow จะทำ

**Action Execution Order**:
- **Sequential** (ตามลำดับ): ทำทีละอัน ตามลำดับ
- **Parallel** (ขนาน): ทำหลายอันพร้อมกัน

**Action Input/Output**:
- **Input** = ข้อมูลที่ใส่เข้าไป (เช่น Email Address)
- **Output** = ข้อมูลที่ได้ออกมา (เช่น Email ที่ส่งสำเร็จ)

---

## Slide 5: Dynamic Content
### Dynamic Content = ข้อมูลที่เปลี่ยนได้ตามข้อมูลจริง

**ตัวอย่าง**:
- **Static Content**: "สวัสดี" (ไม่เปลี่ยน)
- **Dynamic Content**: `@{triggerBody()?['name']}` (เปลี่ยนตามชื่อจริง)

**การผสมผสาน**:
- `concat('สวัสดี', variables('name'), '!')` → "สวัสดีสมชาย!"

---

## Slide 6: Variables (ตัวแปร)
### Variable = กล่องเก็บข้อมูลที่สามารถเปลี่ยนค่าได้

**การใช้งานตัวแปร**:
- **Initialize Variable**: สร้างตัวแปรใหม่ (เหมือนสร้างกล่อง)
- **Set Variable**: เปลี่ยนค่าในตัวแปร (เหมือนใส่ของใหม่ในกล่อง)
- **Increment Variable**: เพิ่มค่าตัวแปร (เช่น นับจำนวน +1)

**Variable Scopes**:
- **Global**: ใช้ได้ทุกที่ใน Flow
- **Local**: ใช้ได้เฉพาะใน Scope นั้น

---

## Slide 7: Variable Types
### ประเภทของตัวแปร

- **String** (ข้อความ): `"สวัสดี"`
- **Number** (ตัวเลข): `123`, `45.67`
- **Boolean** (จริง/เท็จ): `true`, `false`
- **Array** (รายการ): `["a", "b", "c"]`
- **Object** (วัตถุ): `{"name": "สมชาย"}`

---

## Slide 8: Expressions สำคัญ: Coalesce
### Coalesce Function
**ใช้ทำอะไร**: ตรวจสอบว่าค่าเป็น null หรือไม่ ถ้าเป็น null ให้ใช้ค่า Default

**ตัวอย่าง**:
```
coalesce(triggerBody()?['name'], 'ไม่ระบุชื่อ')
```

**อธิบาย**: ถ้ามีชื่อ → ใช้ชื่อ, ถ้าไม่มีชื่อ → ใช้ "ไม่ระบุชื่อ"

---

## Slide 9: Expressions สำคัญ: GUID & Replace
### GUID Function
**ใช้ทำอะไร**: สร้าง ID ที่ไม่ซ้ำ (Unique ID)

**ตัวอย่าง**: `guid()`  
**ผลลัพธ์**: `"a1b2c3d4-e5f6-7890-abcd-ef1234567890"`

### Replace Function
**ใช้ทำอะไร**: แทนที่ข้อความใน String

**ตัวอย่าง**: `replace('สวัสดีโลก', 'โลก', 'Power Automate')`  
**ผลลัพธ์**: `"สวัสดีPower Automate"`

---

## Slide 10: Expressions สำคัญ: DateTime
### UTC Now Function
**ใช้ทำอะไร**: ดึงวันที่และเวลาปัจจุบัน

**ตัวอย่าง**: `utcNow()`  
**ผลลัพธ์**: `"2024-01-15T10:30:00Z"`

**ใช้เมื่อไหร่**: บันทึกเวลา, สร้าง Timestamp

**Format DateTime**:
- `formatDateTime(utcNow(), 'yyyy-MM-dd')` → `"2024-01-15"`

---

## Slide 11: Null Handling
### Null = ไม่มีค่า (ว่างเปล่า)

**วิธีการจัดการ**:
- **Coalesce**: ใช้ค่า Default เมื่อเป็น null
  ```
  coalesce(variables('name'), 'ไม่ระบุชื่อ')
  ```

- **Optional Chaining**: ใช้ `?` เพื่อหลีกเลี่ยง Error
  ```
  object?['property']
  ```

---

## Slide 12: Data Types: String
### String Functions

- **ต่อ String**: `concat('สวัสดี', ' ', 'คุณ')` → `"สวัสดี คุณ"`
- **ตัด String**: `substring('สวัสดี', 0, 3)` → `"สวั"`
- **นับความยาว**: `length('สวัสดี')` → `6`
- **แปลงเป็นตัวใหญ่**: `toUpper('hello')` → `"HELLO"`
- **แปลงเป็นตัวเล็ก**: `toLower('HELLO')` → `"hello"`

---

## Slide 13: Data Types: Number
### Number Functions

- **บวก**: `add(10, 5)` → `15`
- **ลบ**: `sub(10, 5)` → `5`
- **คูณ**: `mul(10, 5)` → `50`
- **หาร**: `div(10, 5)` → `2`
- **เปรียบเทียบ**: `greater(10, 5)` → `true`
- **ปัดเศษ**: `round(3.7)` → `4`

---

## Slide 14: Data Types: Boolean & Array
### Boolean
- **AND**: `and(true, false)` → `false`
- **OR**: `or(true, false)` → `true`
- **NOT**: `not(true)` → `false`

### Array
- **นับจำนวน**: `length(array)` → จำนวน items
- **ดึงตัวแรก**: `first(array)` → item แรก
- **ดึงตัวสุดท้าย**: `last(array)` → item สุดท้าย

---

## Slide 15: Best Practices
### 1. Always Use Null Safety
- ใช้ `?` สำหรับ Optional Chaining
- ใช้ `coalesce()` สำหรับ Default Values

### 2. Type Conversion
- ตรวจสอบ Data Type ก่อนใช้งาน
- ใช้ Functions เช่น `string()`, `int()`, `float()` เมื่อจำเป็น

### 3. Variable Management
- ตั้งชื่อตัวแปรให้ชัดเจน
- ใช้ Variables สำหรับค่าที่ต้องใช้ซ้ำ

---

## Slide 16: สรุปบทเรียน
หลังจากจบบทนี้ คุณจะสามารถ:

- ✅ เข้าใจ Trigger และ Action ในระดับ Advanced
- ✅ ใช้ Variables อย่างมีประสิทธิภาพ
- ✅ ใช้ Expressions สำคัญต่างๆ ได้อย่างถูกต้อง
- ✅ จัดการ Null และ Data Types ได้อย่างปลอดภัย

---

## Slide 17: Next Steps
**พร้อมสำหรับบทต่อไป!**

**บทที่ 3**: Flow Controls & Advanced Workflow
- Conditions และ Loops
- Scopes และ Parallel Processing

**คำถาม?**



