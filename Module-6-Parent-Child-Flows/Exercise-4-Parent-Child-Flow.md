# Exercise 4: Parent–Child Flow สำหรับ Reuse

## 🎯 วัตถุประสงค์

ใน Exercise นี้ คุณจะได้เรียนรู้การ:
- สร้าง Child Flow ที่สามารถนำกลับมาใช้ซ้ำได้
- สร้าง Parent Flow ที่เรียกใช้ Child Flow
- ส่งข้อมูลระหว่าง Parent และ Child Flow
- ใช้ HTTP Request/Response สำหรับ Communication

---

## 📋 สิ่งที่ต้องเตรียม

- Power Automate Account
- Email Address สำหรับทดสอบ
- SharePoint List (Optional) สำหรับ Parent Flow

---

## 🚀 ขั้นตอนการปฏิบัติ

### ส่วนที่ 1: สร้าง Child Flow (Send Notification Email)

#### ขั้นตอนที่ 1: สร้าง Child Flow ใหม่

1. เข้าสู่ **Power Automate**
2. คลิก **Create** → เลือก **Instant cloud flow**
3. ตั้งชื่อ Flow: `Child Flow - Send Notification Email`
4. เลือก Trigger: **When an HTTP request is received**
5. คลิก **Create**

#### ขั้นตอนที่ 2: ตั้งค่า HTTP Request Trigger

1. คลิกที่ **When an HTTP request is received**
2. คลิก **Use sample payload to generate schema**
3. วาง JSON ตัวอย่าง:

```json
{
  "to": "user@example.com",
  "subject": "Test Subject",
  "body": "Test Body"
}
```

4. คลิก **Done**

> 💡 **คำอธิบาย**: Child Flow จะรับข้อมูลผ่าน HTTP Request

#### ขั้นตอนที่ 3: Initialize Variables (รับข้อมูลจาก Parent)

1. เพิ่ม Action: **Initialize variable**
   - **Name**: `toEmail`
   - **Type**: `String`
   - **Value**: `triggerBody()?['to']`

2. เพิ่ม Action: **Initialize variable**
   - **Name**: `emailSubject`
   - **Type**: `String`
   - **Value**: `triggerBody()?['subject']`

3. เพิ่ม Action: **Initialize variable**
   - **Name**: `emailBody`
   - **Type**: `String`
   - **Value**: `triggerBody()?['body']`

> 💡 **คำอธิบาย**: รับข้อมูลจาก HTTP Request Body และเก็บในตัวแปร

#### ขั้นตอนที่ 4: ส่ง Email

1. เพิ่ม Action: **Send an email (V2)**
   - **To**: `variables('toEmail')`
   - **Subject**: `variables('emailSubject')`
   - **Body**: `variables('emailBody')`

#### ขั้นตอนที่ 5: ส่ง Response กลับไปยัง Parent

1. เพิ่ม Action: **Response**
   - **Status Code**: `200`
   - **Body**: คลิก **Expression** และพิมพ์:

```json
{
  "success": true,
  "messageId": "@{outputs('Send_an_email_(V2)')?['body/MessageId']}",
  "timestamp": "@{utcNow()}",
  "to": "@{variables('toEmail')}"
}
```

> 💡 **คำอธิบาย**: Response จะส่งข้อมูลกลับไปยัง Parent Flow

#### ขั้นตอนที่ 6: บันทึก Child Flow URL

1. คลิก **Save**
2. คลิกที่ **When an HTTP request is received**
3. คัดลอก **HTTP POST URL** ไว้ (จะใช้ใน Parent Flow)

---

### ส่วนที่ 2: สร้าง Parent Flow (Order Processing)

#### ขั้นตอนที่ 1: สร้าง Parent Flow ใหม่

1. คลิก **Create** → เลือก **Instant cloud flow**
2. ตั้งชื่อ Flow: `Parent Flow - Order Processing`
3. เลือก Trigger: **Manually trigger a flow**
4. คลิก **Create**

#### ขั้นตอนที่ 2: Initialize Variables

1. เพิ่ม Action: **Initialize variable**
   - **Name**: `orderNumber`
   - **Type**: `String`
   - **Value**: `ORD-2024-001`

2. เพิ่ม Action: **Initialize variable**
   - **Name**: `customerEmail`
   - **Type**: `String`
   - **Value**: `your-email@example.com` (เปลี่ยนเป็น Email ของคุณ)

3. เพิ่ม Action: **Initialize variable**
   - **Name**: `orderStatus`
   - **Type**: `String`
   - **Value**: `Processing`

#### ขั้นตอนที่ 3: Process Order (Simulate)

1. เพิ่ม Action: **Scope**
   - ตั้งชื่อ: `Process Order`

2. ภายใน Scope:
   - เพิ่ม Action: **Compose**
     - **Inputs**: `Processing order...`
   
   - เพิ่ม Action: **Delay**
     - **Count**: `2`
     - **Unit**: `Second`

> 💡 **คำอธิบาย**: จำลองการประมวลผล Order

#### ขั้นตอนที่ 4: เรียก Child Flow (Send Notification)

1. เพิ่ม Action: **HTTP**
   - **Method**: `POST`
   - **URI**: วาง **HTTP POST URL** ที่คัดลอกจาก Child Flow
   - **Headers**: 
     - **Content-Type**: `application/json`
   - **Body**: คลิก **Expression** และพิมพ์:

```json
{
  "to": "@{variables('customerEmail')}",
  "subject": "@{concat('Order Confirmation: ', variables('orderNumber'))}",
  "body": "@{concat('Your order ', variables('orderNumber'), ' has been processed successfully. Status: ', variables('orderStatus'))}"
}
```

> 💡 **คำอธิบาย**: ส่งข้อมูลไปยัง Child Flow ผ่าน HTTP Request

#### ขั้นตอนที่ 5: ตรวจสอบ Response จาก Child Flow

1. เพิ่ม Action: **Parse JSON**
   - **Content**: `body('HTTP')` (Response จาก Child Flow)
   - **Schema**: คลิก **Use sample payload** และวาง:

```json
{
  "success": true,
  "messageId": "abc123",
  "timestamp": "2024-01-01T00:00:00Z",
  "to": "user@example.com"
}
```

2. เพิ่ม Action: **Condition**
   - **Condition**: `success` (จาก Parse JSON) `is equal to` `true`

3. ในช่อง **If yes**:
   - เพิ่ม Action: **Set variable**
     - **Name**: `orderStatus`
     - **Value**: `Completed`

   - เพิ่ม Action: **Compose**
     - **Inputs**: `Order processed and notification sent successfully!`

4. ในช่อง **If no**:
   - เพิ่ม Action: **Send an email (V2)**
     - **Subject**: `⚠️ Notification Failed`
     - **Body**: `Failed to send notification for order @{variables('orderNumber')}`

---

## 🧪 ทดสอบ Flow

### ทดสอบ Child Flow

1. เปิด Child Flow
2. คลิก **Test** → **Manually**
3. ใส่ JSON Input:
```json
{
  "to": "your-email@example.com",
  "subject": "Test from Child Flow",
  "body": "This is a test message"
}
```
4. คลิก **Run flow**
5. ตรวจสอบว่าได้รับ Email และ Response ถูกต้อง

### ทดสอบ Parent Flow

1. เปิด Parent Flow
2. คลิก **Test** → **Manually** → **Run flow**
3. ตรวจสอบว่า:
   - Order ถูกประมวลผล
   - Child Flow ถูกเรียก
   - Email ถูกส่ง
   - Response ถูกจัดการ

---

## 🎓 สิ่งที่ได้เรียนรู้

✅ **Child Flow Concept**: สร้าง Flow ที่นำกลับมาใช้ซ้ำได้  
✅ **HTTP Request/Response**: สื่อสารระหว่าง Flows  
✅ **Parent–Child Pattern**: แยก Logic ออกเป็น Modules  
✅ **Error Handling**: จัดการเมื่อ Child Flow Fail  
✅ **Reusability**: ใช้ Child Flow หลายครั้ง  

---

## 💡 Tips & Tricks

1. **Child Flow Naming**: ตั้งชื่อให้บอกหน้าที่ชัดเจน
2. **Error Handling**: จัดการ Error ใน Child Flow และ Parent Flow
3. **Logging**: Log Input/Output สำหรับ Debug
4. **Testing**: ทดสอบ Child Flow แยกก่อน แล้วค่อยทดสอบ Parent Flow

---

## 🔄 แบบฝึกหัดเพิ่มเติม

### แบบฝึกหัด A: Multiple Child Flows

สร้าง Parent Flow ที่เรียก Child Flow หลายตัว:
1. Child Flow 1: Validate Data
2. Child Flow 2: Process Data
3. Child Flow 3: Send Notification

### แบบฝึกหัด B: Chained Child Flows

สร้าง Parent Flow ที่:
1. เรียก Child Flow A
2. ใช้ Response จาก A เรียก Child Flow B
3. ใช้ Response จาก B เรียก Child Flow C

---

## ❓ คำถามที่พบบ่อย (FAQ)

**Q: Child Flow ต้องใช้ HTTP Request Trigger หรือไม่?**  
A: ใช่ เพื่อให้ Parent Flow เรียกได้

**Q: สามารถส่งข้อมูลหลายตัวไปยัง Child Flow ได้หรือไม่?**  
A: ได้ ใส่ใน JSON Body ของ HTTP Request

**Q: Child Flow สามารถเรียก Child Flow อื่นได้หรือไม่?**  
A: ได้ แต่อาจทำให้ซับซ้อน ควรระวัง

---

## 📝 สรุป

ใน Exercise นี้ คุณได้เรียนรู้:
- การสร้าง Child Flow
- การเรียก Child Flow จาก Parent Flow
- การส่งข้อมูลระหว่าง Flows
- Error Handling Pattern

Flow ของคุณจะ Modular และ Reusable มากขึ้น! 🎉




