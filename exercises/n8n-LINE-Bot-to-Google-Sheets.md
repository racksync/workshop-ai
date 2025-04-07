# บันทึกไฟล์จาก LINE Bot ลง Google Drive และบันทึก URL ลง Google Sheets ด้วย n8n

## ตารางเนื้อหา
| ระดับความยาก | วัตถุประสงค์ |
|---------------|----------------|
| ปานกลาง      | เรียนรู้การสร้าง Workflow ใน n8n เพื่อบันทึกไฟล์จาก LINE Bot ลง Google Drive และบันทึก URL ลง Google Sheets |
| Template | [LINKS](https://n8n.io/workflows/3191-automatically-save-and-organize-line-message-files-in-google-drive-with-sheets-logging/) |

## เนื้อหา
บทความนี้จะแนะนำวิธีการสร้าง Workflow ใน n8n เพื่อรับไฟล์จาก LINE Bot และบันทึกไฟล์เหล่านั้นลงใน Google Drive พร้อมทั้งบันทึก URL ของไฟล์ลงใน Google Sheets โดยใช้ Node ต่าง ๆ ที่ n8n มีให้

### ขั้นตอนการติดตั้งและตั้งค่า

#### 1. Import Template
- ไปที่ n8n Template Library และค้นหา "Line Save File to Google Drive and Log File’s URL"
- กด Import เพื่อนำ Flow เข้ามาใน n8n ของคุณ

#### 2. ตั้งค่า Credentials
- LINE Webhook: กรอกข้อมูล Channel Access Token และตั้งค่า Webhook URL ให้ตรงกับ Node "LINE Webhook Listener"
- Google Drive และ Google Sheets: ใส่ Credential ให้เรียบร้อย

#### 3. สร้าง Google Sheets
- เตรียม Sheet ชื่อ "config" เพื่อเก็บค่า Parent Folder ID, Store by Date, Store by File Type ฯลฯ
- สร้าง Sheet ชื่อ "fileList" เพื่อเก็บข้อมูลไฟล์ที่อัปโหลด เช่น ชื่อไฟล์, วันที่, URL, และประเภทไฟล์

#### 4. Deploy และทดสอบ
- กด Deploy Flow
- ลองส่งไฟล์จาก LINE Bot และตรวจสอบว่าไฟล์ถูกอัปโหลดขึ้น Google Drive และมี Log ใน Google Sheets หรือไม่

### ตัวอย่างโค้ดและคำสั่ง

#### ตัวอย่างการตั้งค่า Webhook URL
```bash
https://<YOUR_N8N_INSTANCE>/webhook/line
```

#### ตัวอย่างการตั้งค่า Google Sheets Node
- ชื่อ Sheet: `fileList`
- คอลัมน์: `FileName`, `Date`, `URL`, `FileType`

#### ตัวอย่างการตั้งค่า Google Drive Node
- โฟลเดอร์: ใช้ Parent Folder ID จาก Sheet "config"

### หมายเหตุ
- Workflow นี้เหมาะสำหรับการทดลองใช้งานและการเรียนรู้การเชื่อมต่อบริการต่าง ๆ ด้วย n8n
- ควรตรวจสอบการตั้งค่าและสิทธิ์การเข้าถึงของ Google Drive และ Google Sheets เพื่อความปลอดภัย