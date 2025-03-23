# การสร้าง Workflow พื้นฐาน

![n8n workflow](https://www.google.com/search?q=n8n+workflow+example&tbm=isch)

## ตัวอย่าง: รับข้อมูลจาก Google Sheet และส่งการแจ้งเตือนผ่าน Line

1. สร้าง Trigger ด้วย Schedule node (ทุก 1 ชั่วโมง)
2. เชื่อมต่อกับ Google Sheets node
3. ส่งข้อมูลไปยัง Line Notify node

## Presenter Notes (ข้อมูลสำหรับผู้บรรยาย)

> Key Takeaway: การสร้าง workflow ใน n8n เป็นเรื่องง่ายด้วยการเชื่อมต่อ nodes ต่างๆ เข้าด้วยกัน

- อธิบายว่า workflow ใน n8n ประกอบด้วย nodes ที่เชื่อมต่อกัน
- ในตัวอย่างนี้ เราใช้ 3 nodes หลัก:
  1. **Schedule node**: เป็น trigger ที่จะเริ่มทำงานตามเวลาที่กำหนด (ทุก 1 ชั่วโมง)
  2. **Google Sheets node**: ดึงข้อมูลจาก Google Sheet ที่กำหนด
  3. **Line Notify node**: ส่งข้อความแจ้งเตือนไปยัง Line
- แต่ละ node จะมีการตั้งค่าเฉพาะ:
  - Schedule: ตั้งเวลาการทำงาน (cron expression)
  - Google Sheets: การเชื่อมต่อ account, เลือก spreadsheet, sheet, range
  - Line Notify: เชื่อมต่อกับ Line token, กำหนดข้อความที่จะส่ง
- สามารถสร้าง workflow ที่ซับซ้อนมากขึ้นได้ เช่น:
  - เพิ่ม Filter node เพื่อกรองข้อมูล
  - ใช้ Function node เพื่อปรับแต่งข้อมูล
  - แยกการส่งข้อความตามเงื่อนไข
- เน้นย้ำว่าการทดสอบแต่ละ node สามารถทำได้ระหว่างสร้าง workflow

Technical Terms:
- Workflow
- Nodes
- Trigger
- Schedule (Cron Job)
- API Authentication
- Data Transformation
- Webhook
