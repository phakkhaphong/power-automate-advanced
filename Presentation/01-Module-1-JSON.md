# บทที่ 1: JSON in Workflows – The Core of Power Automate

## Slide 1: Title
### บทที่ 1: JSON in Workflows
**The Core of Power Automate**

---

## Slide 2: ภาพรวม
**JSON** = รูปแบบการเก็บข้อมูลที่คอมพิวเตอร์เข้าใจง่าย

- เปรียบเหมือนตาราง Excel ที่เขียนด้วยโค้ด
- เป็นภาษาโปรแกรมที่ใช้สื่อสารระหว่างระบบต่างๆ
- ทุก Action ใน Power Automate ใช้ JSON

---

## Slide 3: JSON Basics: Objects
### JSON Objects (วัตถุ JSON)
การเก็บข้อมูลแบบคู่ **Key-Value**

**ตัวอย่าง**:
```json
{
  "name": "สมชาย",
  "age": 20,
  "city": "กรุงเทพ"
}
```

- `"name"` = **Key** (ชื่อของข้อมูล)
- `"สมชาย"` = **Value** (ค่าของข้อมูล)

---

## Slide 4: JSON Basics: Arrays
### JSON Arrays (อาร์เรย์ JSON)
รายการข้อมูลหลายตัว เรียงเป็นแถว

**Array ง่ายๆ**:
```json
["แอปเปิ้ล", "ส้ม", "กล้วย"]
```

**Array of Objects**:
```json
[
  { "id": 1, "name": "สมชาย" },
  { "id": 2, "name": "สมหญิง" }
]
```

---

## Slide 5: Key-Value Pairs
### Key-Value คืออะไร?
การจับคู่ระหว่าง **ชื่อ** กับ **ค่า**

**ตัวอย่างในชีวิตจริง**:
- หนังสือ: ชื่อหนังสือ (Key) = เนื้อหา (Value)
- พจนานุกรม: คำ (Key) = ความหมาย (Value)

**ใน Power Automate**:
- Dynamic Content = Value
- ชื่อ Field = Key

---

## Slide 6: Parse JSON Action
### แปลง JSON String เป็น Object
**ปัญหาที่พบบ่อย**:
- ข้อมูล JSON มักเป็น **String** (ข้อความ)
- ต้องแปลงให้เป็น **Object** (วัตถุ) ก่อนใช้งาน

**วิธีการ**:
1. ใช้ Action **Parse JSON**
2. ใส่ JSON String ที่ต้องการแปลง
3. กำหนด **Schema** (โครงสร้าง)

---

## Slide 6A: Parse JSON to Extract items[]
### ใช้ Parse JSON เพื่อดึงข้อมูล Array

**ตัวอย่าง JSON Input:**
```json
{
  "orderId": "ORD-001",
  "items": [
    { "product": "แอปเปิ้ล", "quantity": 5, "price": 30 },
    { "product": "ส้ม", "quantity": 3, "price": 25 },
    { "product": "กล้วย", "quantity": 10, "price": 20 }
  ]
}
```

**ขั้นตอน**:
1. ใช้ **Parse JSON** Action
2. ใส่ JSON String ใน Content field
3. Generate Schema จาก sample payload

**หลัง Parse JSON แล้ว:**
- `body('Parse_JSON')?['orderId']` → `"ORD-001"`
- `body('Parse_JSON')?['items']` → Array ของ items

**Extract items[]:**
```
body('Parse_JSON')?['items']
```

**ผลลัพธ์**:
```json
[
  { "product": "แอปเปิ้ล", "quantity": 5, "price": 30 },
  { "product": "ส้ม", "quantity": 3, "price": 25 },
  { "product": "กล้วย", "quantity": 10, "price": 20 }
]
```

---

## Slide 6B: Access Values Using Index
### เข้าถึงข้อมูลด้วย Index เช่น items[0], items[1]

**หลังจาก Parse JSON แล้ว:**

**Access items โดยใช้ Index:**
- `body('Parse_JSON')?['items'][0]` → Item แรก (index 0)
- `body('Parse_JSON')?['items'][1]` → Item ที่สอง (index 1)
- `body('Parse_JSON')?['items'][2]` → Item ที่สาม (index 2)

**Access Properties ใน Object:**
- `body('Parse_JSON')?['items'][0]?['product']` → `"แอปเปิ้ล"`
- `body('Parse_JSON')?['items'][0]?['quantity']` → `5`
- `body('Parse_JSON')?['items'][0]?['price']` → `30`

**ตัวอย่าง:**
```
items[0].product  → "แอปเปิ้ล"
items[0].quantity → 5
items[0].price    → 30

items[1].product  → "ส้ม"
items[1].quantity → 3
items[1].price    → 25
```

**Power Automate Expression:**
```
body('Parse_JSON')?['items'][0]?['product']
body('Parse_JSON')?['items'][1]?['quantity']
```

---

## Slide 7: Extract Data from Arrays
### Apply to Each = วนลูป (Loop)

**ขั้นตอน**:
1. ใช้ **Parse JSON** เพื่อ extract `items[]`
2. ใช้ **Apply to Each** เพื่อวนลูปผ่าน items

**ตัวอย่าง**:
- มี items 3 รายการ → วนลูป 3 ครั้ง
- แต่ละครั้งประมวลผล 1 item

**item()** = ข้อมูลในรอบปัจจุบัน
- รอบที่ 1: `item()` = items[0] = { "product": "แอปเปิ้ล", ... }
- รอบที่ 2: `item()` = items[1] = { "product": "ส้ม", ... }
- รอบที่ 3: `item()` = items[2] = { "product": "กล้วย", ... }

**Access Values ใน Loop:**
- `item()?['product']` → ชื่อสินค้าในรอบปัจจุบัน
- `item()?['quantity']` → จำนวนในรอบปัจจุบัน
- `item()?['price']` → ราคาในรอบปัจจุบัน

**ตัวอย่างการใช้งาน:**
```
Apply to Each: body('Parse_JSON')?['items']
  - item()?['product']  → รอบที่ 1: "แอปเปิ้ล"
  - item()?['quantity'] → รอบที่ 1: 5
  - item()?['price']    → รอบที่ 1: 30
```

---

## Slide 8: Filtering Arrays
### Filter Array Action
กรองข้อมูลตามเงื่อนไข

**ตัวอย่าง**:
- กรองพนักงานที่มีเงินเดือน > 50000
- กรอง Order ที่สถานะ = "Pending"

**เงื่อนไข**:
- `item()?['property'] eq 'value'`
- ใช้ `and`, `or` สำหรับหลายเงื่อนไข

---

## Slide 9: Select Action
### Transform Array
แปลง Array จากรูปแบบหนึ่งไปอีกรูปแบบหนึ่ง

**ตัวอย่าง**:
- สร้าง Object ใหม่จาก Properties ที่เลือก
- ใช้ Expressions ใน Map ฟังก์ชัน

**ข้อดี**:
- ไม่ต้องวนลูป
- เร็วกว่า Apply to Each

---

## Slide 10: Expressions ที่ใช้กับ JSON
### String Functions
- `first()`: ดึง Item แรกจาก Array
- `last()`: ดึง Item สุดท้ายจาก Array
- `join()`: รวม Array เป็น String

### Table Functions
- `createHTMLTable()`: สร้าง HTML Table
- `createCSVTable()`: สร้าง CSV Table

---

## Slide 10A: Convert List to String - join()
### ใช้ join() เพื่อรวม List เป็น String

**ตัวอย่าง 1: รวม Email Addresses**
```json
Input Array: ["user1@example.com", "user2@example.com", "user3@example.com"]
```

**Expression:**
```
join(variables('emailList'), '; ')
```

**ผลลัพธ์:**
```
"user1@example.com; user2@example.com; user3@example.com"
```

**ตัวอย่าง 2: รวมชื่อสินค้า**
```json
Input Array: ["แอปเปิ้ล", "ส้ม", "กล้วย"]
```

**Expression:**
```
join(variables('products'), ', ')
```

**ผลลัพธ์:**
```
"แอปเปิ้ล, ส้ม, กล้วย"
```

**Syntax:**
```
join(array, delimiter)
```
- `array`: Array ที่ต้องการรวม
- `delimiter`: ตัวคั่นระหว่าง items (เช่น `,` `;` ` - `)

---

## Slide 10B: Create HTML Table from List of Objects
### แปลง List of Objects เป็น HTML Table

**ตัวอย่างข้อมูล:**
```json
[
  { "id": 1, "name": "สมชาย", "department": "ไอที", "salary": 50000 },
  { "id": 2, "name": "สมหญิง", "department": "HR", "salary": 45000 },
  { "id": 3, "name": "วิชัย", "department": "การเงิน", "salary": 55000 }
]
```

**ขั้นตอน:**
1. ใช้ **Select** Action เพื่อแปลง Array
2. ใช้ Expression `createHTMLTable()` 

**Expression:**
```
createHTMLTable(outputs('Select')?['body'])
```

**ผลลัพธ์ HTML:**
```html
<table>
  <thead>
    <tr>
      <th>id</th>
      <th>name</th>
      <th>department</th>
      <th>salary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>สมชาย</td>
      <td>ไอที</td>
      <td>50000</td>
    </tr>
    ...
  </tbody>
</table>
```

**ใช้ใน Email Body:**
- Copy HTML Table ที่ได้
- วางใน Email Action → Body (HTML format)

---

## Slide 10C: Create CSV Table from List of Objects
### แปลง List of Objects เป็น CSV Table

**ตัวอย่างข้อมูล:**
```json
[
  { "id": 1, "name": "สมชาย", "department": "ไอที", "salary": 50000 },
  { "id": 2, "name": "สมหญิง", "department": "HR", "salary": 45000 }
]
```

**Expression:**
```
createCSVTable(outputs('Select')?['body'])
```

**ผลลัพธ์ CSV:**
```csv
id,name,department,salary
1,สมชาย,ไอที,50000
2,สมหญิง,HR,45000
```

**ใช้ CSV สำหรับ:**
- Export ข้อมูลไปยัง Excel
- Attach ไฟล์ CSV ใน Email
- ส่งข้อมูลไปยังระบบอื่น

**Tips:**
- CSV ใช้ comma (`,`) เป็นตัวคั่น
- เหมาะสำหรับการ Export ข้อมูลจำนวนมาก

---

## Slide 11: Core Concept
### ทุก Action = JSON Input → JSON Output
```
Trigger → JSON Output
    ↓
Action 1 → JSON Output
    ↓
Action 2 → JSON Output (ใช้ Output จาก Action 1)
    ↓
Action N → Final JSON Output
```

**Dynamic Content** = การอ้างอิง JSON Output จาก Actions ก่อนหน้า

---

## Slide 12: Lab Exercise
### Lab 1: JSON → Parse JSON → Loop & Select → HTML Table

**สิ่งที่คุณจะได้ทำ**:
1. สร้าง Flow ที่รับ JSON String
2. แปลง JSON String เป็น Object ด้วย Parse JSON
3. วนลูปผ่าน Array เพื่อดึงข้อมูล
4. แปลงข้อมูลเป็น HTML Table สวยงาม

---

## Slide 13: สรุปบทเรียน
หลังจากเรียนจบบทนี้ คุณจะสามารถ:

- ✅ เข้าใจโครงสร้าง JSON Objects และ Arrays
- ✅ Parse JSON String เป็น Object
- ✅ วนลูปผ่าน Arrays ด้วย Apply to Each
- ✅ กรองและเลือกข้อมูลจาก Arrays
- ✅ สร้าง HTML/CSV Tables จาก JSON Data

---

## Slide 14: คำศัพท์ที่ควรรู้
- **JSON**: รูปแบบการเก็บข้อมูลแบบ Key-Value
- **Object**: วัตถุที่เก็บข้อมูลแบบ Key-Value
- **Array**: รายการข้อมูลหลายตัว
- **Parse**: แปลง String เป็น Object
- **Loop**: วนลูป (ทำซ้ำ)
- **Filter**: กรองข้อมูลตามเงื่อนไข

---

## Slide 15: คำถามที่พบบ่อย
**Q: JSON ต่างจาก Excel อย่างไร?**  
A: JSON เป็นรูปแบบข้อมูลที่คอมพิวเตอร์อ่านได้ง่าย ส่วน Excel เป็นไฟล์ที่มนุษย์อ่านได้

**Q: ทำไมต้อง Parse JSON?**  
A: เพราะข้อมูลที่เราได้รับมาส่วนใหญ่เป็น String (ข้อความ) ต้องแปลงเป็น Object ก่อนใช้งาน

**Q: Apply to Each ใช้เมื่อไหร่?**  
A: ใช้เมื่อต้องการทำอะไรบางอย่างกับทุก item ใน Array

---

## Slide 16: Next Steps
**พร้อมสำหรับบทต่อไป!**

**บทที่ 2**: Advanced Fundamentals
- Triggers และ Actions
- Variables และ Expressions
- Data Types

**คำถาม?**


