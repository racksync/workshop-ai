# การติดตั้งและตั้งค่า

![Development Environment Setup](https://www.google.com/search?q=nodejs+development+environment+setup&tbm=isch)

## ข้อกำหนดเบื้องต้น
- Node.js (เวอร์ชัน 14.x หรือสูงกว่า)
- npm หรือ yarn
- ความรู้พื้นฐานเกี่ยวกับ JavaScript/TypeScript
- เข้าใจพื้นฐานของ HTTP และการพัฒนาเว็บ

## การติดตั้ง Bolt CLI
```bash
# ติดตั้ง Bolt CLI แบบ global
npm install -g @boltframework/cli

# หรือใช้ yarn
yarn global add @boltframework/cli
```

## การสร้างโปรเจกต์ใหม่
```bash
# สร้างโปรเจกต์ใหม่
bolt create my-app
cd my-app

# ติดตั้ง dependencies
npm install
# หรือ
yarn
```

## Presenter Notes (ข้อมูลสำหรับผู้บรรยาย)

> Key Takeaway: การติดตั้ง Bolt Framework ทำได้ง่ายผ่าน npm หรือ yarn ซึ่งคล้ายกับการติดตั้งเฟรมเวิร์กอื่นๆ ในระบบนิเวศ JavaScript สำหรับผู้เรียนที่มีประสบการณ์กับ Node.js อยู่แล้ว กระบวนการนี้จะคุ้นเคย เน้นย้ำความสำคัญของการใช้เวอร์ชัน Node.js ที่ถูกต้อง (14.x หรือสูงกว่า) เพื่อความเข้ากันได้ แนะนำให้อธิบายเพิ่มเติมเกี่ยวกับการตรวจสอบการติดตั้งที่สำเร็จ และวิธีการแก้ไขปัญหาพื้นฐานที่อาจเกิดขึ้นระหว่างการติดตั้ง เช่น ปัญหาสิทธิ์การเข้าถึง หรือความขัดแย้งของเวอร์ชัน Node.js

> Technical Terms: CLI (Command Line Interface), NPM (Node Package Manager), Dependencies, Global Installation, Project Initialization
