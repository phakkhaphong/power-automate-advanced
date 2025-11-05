# Exercise 1c: Parse JSON → Filter Array → Loop → Teams Messaging

## 🎯 วัตถุประสงค์

ใน Exercise นี้ คุณจะได้เรียนรู้การ:
- สร้าง Flow ที่รับข้อมูล JSON
- แปลง JSON String เป็น Object ที่ใช้งานได้ (Parse JSON)
- ใช้ Filter Array เพื่อกรองข้อมูลตามเงื่อนไข
- วนลูปผ่าน Array ที่กรองแล้ว
- ส่งข้อความไปยัง Teams Chat/Channel สำหรับแต่ละรายการที่ผ่านเงื่อนไข

> 💡 **หมายเหตุ**: Exercise นี้แตกต่างจาก Exercise 1a ที่เพิ่ม **Filter Array** เพื่อกรองเฉพาะพนักงานที่เงินเดือนเกิน 50,000 บาท

---

## 📋 สิ่งที่ต้องเตรียม

- Power Automate Account (สามารถใช้ Free Account ได้)
- ความรู้พื้นฐานเรื่อง JSON (ถ้ายังไม่รู้ จะสอนในขั้นตอน)
- ควรทำ Exercise 1a เสร็จก่อน (เพื่อเข้าใจพื้นฐาน Parse JSON และ Loop)

---

## 🚀 ขั้นตอนการปฏิบัติ

### ขั้นตอนที่ 1: สร้าง Flow ใหม่และตั้งค่า JSON Input

1. เข้าสู่ **Power Automate** (https://make.powerautomate.com/)
2. คลิก **Create** → เลือก **Instant cloud flow**
3. ตั้งชื่อ Flow: `Exercise 2c - Parse JSON Filter Teams`
4. เลือก Trigger: **Manually trigger a flow**
5. คลิก **Create**
6. ใน **Manually trigger a flow** คลิก **Add an input** → เลือก **JSON**
7. ตั้งชื่อ Input: `JSON input` (ชนิดข้อมูล: Text)

> 💡 **หมายเหตุ**: JSON Input จะให้คุณสามารถป้อนข้อมูล JSON เมื่อรัน Flow ได้

### ขั้นตอนที่ 2: Parse JSON (แปลง JSON String เป็น Object)

**ก่อนเริ่ม**: เตรียมข้อมูล JSON ตัวอย่างด้านล่างไว้สำหรับทดสอบ:

```json
{
  "year": 2025,
  "employees": [
    {
      "id": 1,
      "name": "สมชาย ใจดี",
      "department": "ไอที",
      "salary": 50000
    },
    {
      "id": 2,
      "name": "สมหญิง รักงาน",
      "department": "ทรัพยากรบุคคล",
      "salary": 45000
    },
    {
      "id": 3,
      "name": "วิชัย ทำงานดี",
      "department": "การเงิน",
      "salary": 55000
    },
    {
      "id": 4,
      "name": "วิไล เก่งมาก",
      "department": "ไอที",
      "salary": 60000
    }
  ]
}
```

> 💡 **หมายเหตุ**: JSON นี้มีโครงสร้าง 2 ชั้น:
> - **ชั้นที่ 1**: Object หลักที่มี Key `year` (ปี) และ `employees` (Array ของพนักงาน)
> - **ชั้นที่ 2**: แต่ละ Object ใน Array `employees` มี Key `id`, `name`, `department`, `salary`

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Parse JSON**
3. ตั้งค่า:
   - **Content**: เลือก `JSON input` จาก Dynamic Content (จาก Manual Trigger)
   - **Schema**: คลิก **Use sample payload to generate schema**
   - วาง JSON ตัวอย่างด้านบนลงไป
   - คลิก **Done**

> 💡 **คำอธิบาย**: Parse JSON จะแปลง JSON String ให้เป็น Object ที่ Power Automate เข้าใจและสามารถใช้งานได้

**หลังจาก Parse JSON แล้ว คุณสามารถเข้าถึงข้อมูลได้:**

**ชั้นที่ 1 (Root Level):**
- `body('Parse_JSON')?['year']` → `2025` (ปี)
- `body('Parse_JSON')?['employees']` → Array ของพนักงานทั้งหมด (4 คน)

**ชั้นที่ 2 (Array Level):**
- `body('Parse_JSON')?['employees'][0]` → พนักงานคนแรก (index 0)
- `body('Parse_JSON')?['employees'][0]?['name']` → `"สมชาย ใจดี"`
- `body('Parse_JSON')?['employees'][0]?['department']` → `"ไอที"`
- `body('Parse_JSON')?['employees'][0]?['salary']` → `50000`

**ตัวอย่างการเข้าถึงข้อมูล 2 ชั้น:**
```
ชั้นที่ 1:
- year → 2025
- employees → [Array ของพนักงาน 4 คน]

ชั้นที่ 2 (employees[0]):
- id → 1
- name → "สมชาย ใจดี"
- department → "ไอที"
- salary → 50000

employees[1]:
- name → "สมหญิง รักงาน"
- department → "ทรัพยากรบุคคล"
- salary → 45000
```

### ขั้นตอนที่ 3: Filter Array (กรองพนักงานที่เงินเดือนเกิน 50,000)

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Filter array**
3. ตั้งค่า:
   - **From**: เลือก `Body employees` จาก Parse JSON output
   - **เงื่อนไข**: คลิก **Add a condition**
   - **เงื่อนไขที่ 1**:
     - **Choose a value**: เลือก `Body salary` จาก Dynamic Content (หรือใช้ Expression: `item()?['salary']`)
     - **เงื่อนไข**: `is greater than`
     - **Value**: `50000`

> 💡 **คำอธิบาย**: Filter Array จะกรอง Array และคืนค่าเฉพาะรายการที่ตรงตามเงื่อนไข

**ผลลัพธ์ของ Filter Array:**
- **Input**: พนักงานทั้งหมด 4 คน
  - สมชาย: 50000 (ไม่เกิน - **ถูกกรองออก**)
  - สมหญิง: 45000 (ไม่เกิน - **ถูกกรองออก**)
  - วิชัย: 55000 (เกิน - **ผ่านเงื่อนไข**)
  - วิไล: 60000 (เกิน - **ผ่านเงื่อนไข**)

- **Output**: พนักงาน 2 คน (เฉพาะที่เงินเดือน > 50000)
  - วิชัย: 55000
  - วิไล: 60000

**ตัวอย่างเงื่อนไขอื่นๆ:**
- `salary is greater than 50000` → เงินเดือนมากกว่า 50,000
- `salary is greater than or equal to 50000` → เงินเดือนมากกว่าหรือเท่ากับ 50,000
- `salary is less than 50000` → เงินเดือนน้อยกว่า 50,000
- `department is equal to 'ไอที'` → แผนกเท่ากับ "ไอที"

### ขั้นตอนที่ 4: ส่งข้อความเริ่มต้น (Post Message - นอก Loop)

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Post message in a chat or channel**
3. ตั้งค่า:
   - **Post as**: `Flow bot`
   - **Post in**: `Chat with Flow bot`
   - **Recipient**: ใส่ Email address ของคุณ (เช่น `your-email@example.com`)
   - **Message**: ใช้ Dynamic Content หรือ Expression เพื่อสร้างข้อความ:
     ```
     📋 รายชื่อพนักงานที่เงินเดือนเกิน 50,000 บาท (ปี body('Parse_JSON')?['year'])
     ```
     หรือใช้ Expression:
     ```
     concat('📋 รายชื่อพนักงานที่เงินเดือนเกิน 50,000 บาท (ปี ', body('Parse_JSON')?['year'], ')')
     ```

> 💡 **คำอธิบาย**: Action นี้จะส่งข้อความก่อนเริ่มวนลูป เพื่อแจ้งเตือนว่ากำลังแสดงรายชื่อพนักงานที่ผ่านเงื่อนไข

### ขั้นตอนที่ 5: วนลูปผ่าน Array ที่กรองแล้ว (Apply to Each)

1. เพิ่ม Action: คลิก **+ New step**
2. ค้นหาและเลือก **Apply to each**
3. ตั้งค่า:
   - **Select an output from previous steps**: เลือก `Body` จาก Filter array output

> 💡 **คำอธิบาย**: Apply to Each จะวนลูปผ่านทุก item ใน Array ที่กรองแล้ว (ในที่นี้มี 2 คน แทนที่จะเป็น 4 คน)

**Loop through items using Apply to Each:**

**item()** = ข้อมูลในรอบปัจจุบัน (เฉพาะที่ผ่านเงื่อนไข)
- รอบที่ 1: `item()` = { "id": 3, "name": "วิชัย ทำงานดี", "department": "การเงิน", "salary": 55000 }
- รอบที่ 2: `item()` = { "id": 4, "name": "วิไล เก่งมาก", "department": "ไอที", "salary": 60000 }

**Access Values ใน Loop:**
- `item()?['name']` → ชื่อพนักงานในรอบปัจจุบัน
- `item()?['department']` → แผนกในรอบปัจจุบัน
- `item()?['salary']` → เงินเดือนในรอบปัจจุบัน

**ตัวอย่างการใช้งาน:**
```
Apply to Each: body('Filter_array')?['body']
  - item()?['name']       → รอบที่ 1: "วิชัย ทำงานดี", รอบที่ 2: "วิไล เก่งมาก"
  - item()?['department'] → รอบที่ 1: "การเงิน", รอบที่ 2: "ไอที"
  - item()?['salary']     → รอบที่ 1: 55000, รอบที่ 2: 60000
```

### ขั้นตอนที่ 6: ส่งข้อความสำหรับแต่ละพนักงาน (Post Message - ใน Loop)

1. **ภายใน Apply to Each** คลิก **Add an action**
2. ค้นหาและเลือก **Post message in a chat or channel**
3. ตั้งค่า:
   - **Post as**: `Flow bot`
   - **Post in**: `Chat with Flow bot`
   - **Recipient**: เลือก Email address เดียวกัน (หรือใช้ Dynamic Content)
   - **Message**: ใช้ Dynamic Content หรือ Expression เพื่อสร้างข้อความ:
     ```
     ['name'] | แผนก: ['department'] | เงินเดือน: ['salary']
     ```
     หรือใช้ Expression:
     ```
     concat('👤 ', item()?['name'], ' | แผนก: ', item()?['department'], ' | เงินเดือน: ', item()?['salary'])
     ```

> 💡 **คำอธิบาย**: Action นี้จะส่งข้อความแยกสำหรับแต่ละพนักงานที่ผ่านเงื่อนไข โดยใช้ `item()` เพื่อเข้าถึงข้อมูลในแต่ละรอบ

**ตัวอย่างข้อความที่ส่งออกไป:**
- รอบที่ 1: `👤 วิชัย ทำงานดี | แผนก: การเงิน | เงินเดือน: 55000`
- รอบที่ 2: `👤 วิไล เก่งมาก | แผนก: ไอที | เงินเดือน: 60000`

> ⚠️ **หมายเหตุ**: จะแสดงเฉพาะ 2 คน (ไม่ใช่ 4 คน) เพราะถูกกรองด้วย Filter Array

### ขั้นตอนที่ 7: ทดสอบ Flow

1. คลิก **Save**
2. คลิก **Test** → **Manually** → **Run flow**
3. ในหน้า **Run flow**:
   - วาง JSON ตัวอย่างด้านบนลงในช่อง **JSON input**
   - คลิก **Run flow**
4. รอให้ Flow รันเสร็จ
5. ตรวจสอบข้อความใน Teams Chat:
   - ข้อความแรก: `📋 รายชื่อพนักงานที่เงินเดือนเกิน 50,000 บาท (ปี 2025)`
   - ข้อความถัดไป: ข้อความแยกสำหรับแต่ละพนักงานที่ผ่านเงื่อนไข (2 ข้อความ แทนที่จะเป็น 4 ข้อความ)

---

## 📊 ผลลัพธ์ที่คาดหวัง

คุณควรจะเห็นข้อความใน Teams Chat:
- **ข้อความที่ 1**: ข้อความเริ่มต้นพร้อมปีและเงื่อนไข (เช่น `📋 รายชื่อพนักงานที่เงินเดือนเกิน 50,000 บาท (ปี 2025)`)
- **ข้อความที่ 2-3**: ข้อความแยกสำหรับพนักงานที่ผ่านเงื่อนไข (2 ข้อความ แทนที่จะเป็น 4 ข้อความ)
  - `👤 วิชัย ทำงานดี | แผนก: การเงิน | เงินเดือน: 55000`
  - `👤 วิไล เก่งมาก | แผนก: ไอที | เงินเดือน: 60000`

**ข้อแตกต่างจาก Exercise 1a:**
- **Exercise 1a**: แสดงพนักงานทั้งหมด 4 คน
- **Exercise 1c**: แสดงเฉพาะพนักงานที่เงินเดือนเกิน 50,000 บาท (2 คน)

---

## 🎓 สิ่งที่ได้เรียนรู้

✅ **JSON Basics**: เข้าใจโครงสร้าง JSON Objects และ Arrays (2 ชั้น)  
✅ **Parse JSON**: แปลง JSON String เป็น Object ที่ใช้งานได้ และเข้าถึงข้อมูลหลายชั้น  
✅ **Filter Array**: กรอง Array ตามเงื่อนไขเพื่อได้เฉพาะข้อมูลที่ต้องการ  
✅ **Apply to Each**: วนลูปผ่าน Array ที่กรองแล้ว เพื่อประมวลผลแต่ละรายการ  
✅ **Dynamic Content**: ใช้ `item()` เพื่อเข้าถึงข้อมูลในแต่ละรอบของ Loop  
✅ **Teams Integration**: ส่งข้อความไปยัง Teams Chat/Channel  

---

## 💡 Tips & Tricks

1. **Filter Array กับ Apply to Each ต่างกันอย่างไร?**
   - **Filter Array**: ใช้กรองข้อมูลก่อน (ลดจำนวนรายการ)
   - **Apply to Each**: ใช้ทำอะไรบางอย่างกับแต่ละ Item (อาจไม่ลดจำนวน)

2. **เงื่อนไขที่ใช้ใน Filter Array:**
   - `is greater than` → มากกว่า (>)
   - `is greater than or equal to` → มากกว่าหรือเท่ากับ (>=)
   - `is less than` → น้อยกว่า (<)
   - `is less than or equal to` → น้อยกว่าหรือเท่ากับ (<=)
   - `is equal to` → เท่ากับ (=)
   - `is not equal to` → ไม่เท่ากับ (!=)
   - `contains` → มีข้อความที่ระบุ
   - `starts with` → ขึ้นต้นด้วย
   - `ends with` → ลงท้ายด้วย

3. **เงื่อนไขหลายเงื่อนไข (AND/OR):**
   - คลิก **Add a condition** เพื่อเพิ่มเงื่อนไขเพิ่มเติม
   - เงื่อนไขหลายเงื่อนไขจะใช้ AND (ทั้งเงื่อนไขต้องเป็นจริง)
   - สำหรับ OR ต้องใช้ Filter Array แยกแล้วรวมด้วย Union

4. **เมื่อไหร่ควรใช้ Filter Array?**
   - เมื่อต้องการแสดงเฉพาะข้อมูลที่ตรงตามเงื่อนไข
   - เมื่อต้องการลดจำนวนรายการก่อน Loop (เพิ่มประสิทธิภาพ)
   - เมื่อต้องการแยกข้อมูลตามเงื่อนไขก่อนประมวลผล

5. **ถ้าต้องการกรองหลายเงื่อนไข:**
   - เงื่อนไขที่ 1: `salary is greater than 50000`
   - เงื่อนไขที่ 2: `department is equal to 'ไอที'`
   - ผลลัพธ์: พนักงานที่เงินเดือน > 50000 **และ** แผนก = "ไอที"

---

## 🔄 แบบฝึกหัดเพิ่มเติม

1. **เปลี่ยนเงื่อนไข**: ลองกรองพนักงานที่เงินเดือนมากกว่าหรือเท่ากับ 50,000 บาท (รวม 50,000 ด้วย)

2. **กรองตามแผนก**: กรองเฉพาะพนักงานแผนก "ไอที" แล้วส่งข้อความ

3. **เงื่อนไขหลายเงื่อนไข**: กรองพนักงานที่เงินเดือน > 50,000 **และ** แผนก = "ไอที"

4. **ใช้ JOIN**: แทนการส่งทีละรายการ ใช้ JOIN เพื่อรวมข้อความทั้งหมดเป็นข้อความเดียว (ดู Exercise 1b)

5. **นับจำนวน**: ใช้ `length()` เพื่อนับจำนวนพนักงานที่ผ่านเงื่อนไข:
   ```
   length(body('Filter_array')?['body'])
   ```

6. **แสดงสรุป**: ส่งข้อความสรุปจำนวนพนักงานที่ผ่านเงื่อนไข:
   ```
   concat('📊 พบพนักงานที่เงินเดือนเกิน 50,000 บาท จำนวน ', length(body('Filter_array')?['body']), ' คน')
   ```

---

## ❓ คำถามที่พบบ่อย (FAQ)

**Q: Filter Array กับ Apply to Each ต่างกันอย่างไร?**  
A: Filter Array ใช้กรองข้อมูลก่อน (ลดจำนวนรายการ), Apply to Each ใช้ทำอะไรบางอย่างกับแต่ละ Item

**Q: ทำไมต้องใช้ Filter Array ก่อน Apply to Each?**  
A: เพื่อลดจำนวนรายการที่ต้องประมวลผล ทำให้ Flow เร็วขึ้นและส่งข้อความน้อยลง

**Q: เงื่อนไขใน Filter Array ใช้ได้กับข้อมูลประเภทไหน?**  
A: ใช้ได้กับ Number (มากกว่า, น้อยกว่า), String (เท่ากับ, contains), Boolean (true/false)

**Q: สามารถกรองหลายเงื่อนไขพร้อมกันได้ไหม?**  
A: ได้ โดยคลิก **Add a condition** - เงื่อนไขหลายเงื่อนไขจะใช้ AND (ทั้งเงื่อนไขต้องเป็นจริง)

**Q: เมื่อไหร่ควรใช้ Filter Array?**  
A: เมื่อต้องการแสดงเฉพาะข้อมูลที่ตรงตามเงื่อนไข หรือต้องการลดจำนวนรายการก่อน Loop

---

## 📝 สรุป

ใน Exercise นี้ คุณได้เรียนรู้:
- การรับและ Parse JSON
- การใช้ Filter Array เพื่อกรองข้อมูลตามเงื่อนไข
- การวนลูปผ่าน Array ที่กรองแล้วด้วย Apply to Each
- การเข้าถึงข้อมูลใน Loop ด้วย item()
- การส่งข้อความไปยัง Teams Chat/Channel

**ข้อแตกต่างจาก Exercise 1a:**
- **Exercise 1a**: แสดงพนักงานทั้งหมด 4 คน
- **Exercise 1c**: แสดงเฉพาะพนักงานที่เงินเดือนเกิน 50,000 บาท (2 คน) โดยใช้ Filter Array

**Flow Structure:**
```
Manual Trigger (JSON Input)
  ↓
Parse JSON
  ↓
Filter Array (salary > 50000)
  ↓
Post Message (เริ่มต้น)
  ↓
Apply to Each (Array ที่กรองแล้ว)
  └── Post Message (แต่ละพนักงาน)
```

พร้อมสำหรับบทต่อไปแล้ว! 🎉

