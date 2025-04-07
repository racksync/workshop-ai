# การใช้งาน Command Line Interface (CLI) ใน n8n

## ตารางเนื้อหา
| ระดับความยาก | วัตถุประสงค์ |
|---------------|----------------|
| ปานกลาง      | เรียนรู้การใช้งาน CLI ของ n8n เพื่อจัดการ Workflow และ Credentials |
| URL      | [LINK](https://docs.n8n.io/hosting/cli-commands)|

## เนื้อหา
บทความนี้จะแนะนำการใช้งาน Command Line Interface (CLI) ของ n8n สำหรับการจัดการ Workflow และ Credentials รวมถึงการตั้งค่าต่าง ๆ โดยไม่ต้องใช้ UI ของ n8n

### คำสั่ง CLI ที่สำคัญ

#### 1. การเริ่มต้น Workflow
สามารถเริ่ม Workflow ที่บันทึกไว้ได้โดยใช้ ID ของ Workflow:
```bash
n8n execute --id=<ID>
```
ตัวอย่าง:
```bash
n8n execute --id=5
```

#### 2. การเปลี่ยนสถานะของ Workflow
เปิดหรือปิดการใช้งาน Workflow:
- ปิดการใช้งาน Workflow:
  ```bash
  n8n update:workflow --id=<ID> --active=false
  ```
- เปิดการใช้งาน Workflow:
  ```bash
  n8n update:workflow --id=<ID> --active=true
  ```

#### 3. การ Export Workflow
Export Workflow ทั้งหมดหรือเฉพาะ ID ที่ต้องการ:
- Export Workflow ทั้งหมด:
  ```bash
  n8n export:workflow --all --output=backups/latest/
  ```
- Export Workflow เฉพาะ ID:
  ```bash
  n8n export:workflow --id=<ID> --output=file.json
  ```

#### 4. การ Import Workflow
Import Workflow จากไฟล์ JSON:
- Import Workflow:
  ```bash
  n8n import:workflow --input=file.json
  ```

#### 5. การจัดการ Credentials
- Export Credentials:
  ```bash
  n8n export:credentials --all --output=backups/latest/
  ```
- Import Credentials:
  ```bash
  n8n import:credentials --input=file.json
  ```

#### 6. การ Reset User Management
รีเซ็ตการจัดการผู้ใช้กลับไปสู่สถานะเริ่มต้น:
```bash
n8n user-management:reset
```

#### 7. การตรวจสอบความปลอดภัย
รันการตรวจสอบความปลอดภัยใน n8n:
```bash
n8n audit
```

### หมายเหตุ
- คำสั่ง CLI เหล่านี้เหมาะสำหรับการจัดการ n8n แบบ Self-Hosting
- ควรตรวจสอบสิทธิ์การเข้าถึงและการตั้งค่าความปลอดภัยก่อนใช้งานคำสั่งใด ๆ