# อ่านข้อมูลสลิปโอนเงินไทยจาก Telegram ด้วย SpaceOCR และบันทึกลง Google Sheets

## ตารางเนื้อหา
| ระดับความยาก | วัตถุประสงค์ |
|---------------|----------------|
| ปานกลาง      | เรียนรู้การสร้าง Workflow ใน n8n เพื่ออ่านข้อมูลจากสลิปโอนเงินไทยที่ส่งผ่าน Telegram และบันทึกข้อมูลลง Google Sheets |
| Template      | [LINKS](https://n8n.io/workflows/3008-extract-thai-bank-slip-data-from-line-using-spaceocr-and-save-to-google-sheets/) |

## เนื้อหา
บทความนี้จะแนะนำวิธีการสร้าง Workflow ใน n8n เพื่อรับข้อมูลจากสลิปโอนเงินไทยที่ส่งผ่าน Telegram และใช้ SpaceOCR ในการดึงข้อมูลสำคัญ เช่น ผู้ส่ง ผู้รับ จำนวนเงิน และรหัสธุรกรรม จากนั้นบันทึกข้อมูลลง Google Sheets โดยอัตโนมัติ

### ขั้นตอนการติดตั้งและตั้งค่า

#### 1. เตรียมความพร้อม
- สร้าง Telegram Bot และเปิดใช้งาน API
- สมัครใช้งาน SpaceOCR และรับ API Key จาก [SpaceOCR](https://spaceocr.com/)
- สร้าง Google Sheet เพื่อเก็บข้อมูล โดยมีคอลัมน์ดังนี้:
  - `Date` (วันที่)
  - `Time` (เวลา)
  - `Sender` (ผู้ส่ง)
  - `Receiver` (ผู้รับ)
  - `Bank Name` (ชื่อธนาคาร)
  - `Amount` (จำนวนเงิน)
  - `Transaction ID` (รหัสธุรกรรม)

#### 2. ตั้งค่า Workflow ใน n8n
1. **Webhook Node**
   - Method: `POST`
   - Path: `/telegram-webhook`

2. **HTTP Request Node**
   - ดึง URL ของภาพจากข้อความที่ส่งมาจาก Telegram

3. **SpaceOCR Node**
   - ใช้ API Key จาก SpaceOCR เพื่อดึงข้อมูลจากภาพ

4. **Google Sheets Node**
   - เชื่อมต่อกับ Google Sheets และแมปข้อมูลที่ดึงมา เช่น ผู้ส่ง ผู้รับ จำนวนเงิน ฯลฯ ลงในคอลัมน์ที่กำหนด

#### 3. Deploy และทดสอบ
- เปิดใช้งาน Workflow ใน n8n
- ตั้งค่า Webhook URL ใน Telegram Bot
- ส่งภาพสลิปโอนเงินไปยัง Telegram Bot
- ตรวจสอบข้อมูลที่บันทึกใน Google Sheets

### ตัวอย่างโค้ดและคำสั่ง

#### ตัวอย่างการตั้งค่า Webhook URL
```bash
https://<YOUR_N8N_INSTANCE>/webhook/telegram-webhook
```

#### ตัวอย่างการตั้งค่า Google Sheets Node
- ชื่อ Sheet: `TransactionData`
- คอลัมน์: `Date`, `Time`, `Sender`, `Receiver`, `Bank Name`, `Amount`, `Transaction ID`

### หมายเหตุ
- Workflow นี้เหมาะสำหรับการทดลองใช้งานและการเรียนรู้การเชื่อมต่อบริการต่าง ๆ ด้วย n8n
- ควรตรวจสอบการตั้งค่าและสิทธิ์การเข้าถึงของ Google Sheets เพื่อความปลอดภัย