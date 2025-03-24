# การติดตั้ง n8n ด้วย Docker Compose

![docker compose](https://www.google.com/search?q=docker+compose+containers&tbm=isch)

```yaml
services:
  n8n:
    image: n8nio/n8n:latest
    ports:
      - "5678:5678"
    environment:
      - N8N_HOST=${N8N_HOST:-localhost}
      - N8N_PROTOCOL=${N8N_PROTOCOL:-http}
      # ส่วนอื่นๆ ตัดออกเพื่อความกระชับ
    volumes:
      - n8n_data:/home/node/.n8n
    depends_on:
      - postgres
```

## Presenter Notes (ข้อมูลสำหรับผู้บรรยาย)

> Key Takeaway: การใช้ Docker Compose ทำให้การติดตั้งและการจัดการ n8n เป็นเรื่องง่าย ไม่ต้องกังวลเรื่องการตั้งค่าสภาพแวดล้อมที่ซับซ้อน

- Docker Compose ช่วยให้เราสามารถสร้าง containers หลายตัวพร้อมๆ กันได้
- ในตัวอย่าง เรามี n8n และ postgres (สำหรับเก็บข้อมูล) ทำงานร่วมกัน
- อธิบายส่วนต่างๆ ในไฟล์ docker-compose.yml:
  - `image`: ระบุ image ที่จะใช้ ในที่นี้คือ n8nio/n8n:latest
  - `ports`: การ map port 5678 จาก container มายัง host
  - `environment`: ตัวแปรสภาพแวดล้อมที่จำเป็น
  - `volumes`: การเก็บข้อมูลแบบถาวร
  - `depends_on`: กำหนดลำดับการทำงาน ต้องรอ postgres พร้อมก่อน
- คำสั่งพื้นฐานที่ใช้:
  - `docker-compose up -d`: เริ่มการทำงาน
  - `docker-compose logs -f`: ดู logs
  - `docker-compose down`: หยุดการทำงาน
- เมื่อรัน n8n สำเร็จ สามารถเข้าใช้งานได้ที่ http://localhost:5678

Technical Terms:
- Docker
- Docker Compose
- Container
- Image
- Volume
- Port Mapping
- Environment Variables
