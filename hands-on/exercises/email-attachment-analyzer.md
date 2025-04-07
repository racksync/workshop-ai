# วิเคราะห์ไฟล์แนบในอีเมลด้วย AI และบันทึกข้อมูลลง Google Drive และ Telegram

## ตารางเนื้อหา
| ระดับความยาก | วัตถุประสงค์ |
|---------------|----------------|
| ปานกลาง      | เรียนรู้การสร้าง Workflow ใน n8n เพื่อวิเคราะห์ไฟล์แนบในอีเมล (PDF และรูปภาพ) ด้วย AI และบันทึกข้อมูลลง Google Drive พร้อมส่งสรุปผ่าน Telegram |
| Template      | [LINK](https://n8n.io/workflows/3169-ai-email-analyzer-process-pdfs-images-and-save-to-google-drive-telegram/) |

## เนื้อหา
บทความนี้จะแนะนำวิธีการสร้าง Workflow ใน n8n เพื่อวิเคราะห์ไฟล์แนบในอีเมล เช่น PDF และรูปภาพ โดยใช้ AI ในการสรุปเนื้อหา จากนั้นบันทึกข้อมูลลง Google Drive และส่งสรุปผ่าน Telegram

### ขั้นตอนการติดตั้งและตั้งค่า

#### 1. เตรียมความพร้อม
- ตั้งค่า IMAP สำหรับอีเมลที่ต้องการตรวจสอบ
- สมัครใช้งาน AI Models เช่น DeepSeek, Gemini, และ OpenRouter
- ตั้งค่า Google Drive และ Telegram Bot

#### 2. ตั้งค่า Workflow ใน n8n
1. **Email Trigger (IMAP)**
   - ตั้งค่าให้ตรวจสอบอีเมลใหม่ที่เข้ามา

2. **Check for Attachments**
   - ตรวจสอบว่าอีเมลมีไฟล์แนบหรือไม่

3. **Process Attachments**
   - แยกไฟล์แนบเป็น PDF และรูปภาพ
   - ใช้ AI Model ในการดึงข้อมูลจาก PDF และวิเคราะห์เนื้อหารูปภาพ

4. **Summarize Email Content**
   - ใช้ AI Model สรุปเนื้อหาอีเมลและไฟล์แนบ

5. **Save Summaries**
   - บันทึกข้อมูลสรุปลง Google Drive

6. **Send Final Summary**
   - ส่งสรุปเนื้อหาผ่าน Telegram ไปยัง Chat ID ที่กำหนด

#### 3. Deploy และทดสอบ
- เปิดใช้งาน Workflow ใน n8n
- ส่งอีเมลพร้อมไฟล์แนบไปยังอีเมลที่ตั้งค่าไว้
- ตรวจสอบข้อมูลที่บันทึกใน Google Drive และข้อความสรุปใน Telegram

### ตัวอย่างโค้ดและคำสั่ง

#### ตัวอย่างการตั้งค่า IMAP Node
```yaml
IMAP Configuration:
  - Host: imap.example.com
  - Port: 993
  - Secure: true
  - User: your-email@example.com
  - Password: your-password
```

#### ตัวอย่างการตั้งค่า Telegram Node
```yaml
Telegram Configuration:
  - Bot Token: <YOUR_BOT_TOKEN>
  - Chat ID: <YOUR_CHAT_ID>
```

#### ตัวอย่างการตั้งค่า Google Drive Node
```yaml
Google Drive Configuration:
  - Folder: Summaries
  - File Type: PDF/Text
```

### หมายเหตุ
- Workflow นี้เหมาะสำหรับการทดลองใช้งานและการเรียนรู้การเชื่อมต่อบริการต่าง ๆ ด้วย n8n
- ควรตรวจสอบการตั้งค่าและสิทธิ์การเข้าถึงของ Google Drive และ Telegram เพื่อความปลอดภัย