# Workshop: ระบบ RAG ด้วย n8n

![RAG system](https://www.google.com/search?q=retrieval+augmented+generation+system+diagram&tbm=isch)

## การติดตั้ง n8n พร้อม Vector Database สำหรับระบบ RAG

```yaml
services:
  n8n:
    image: n8nio/n8n:latest
    # ... ตัดออกเพื่อความกระชับ
  postgres:
    image: postgres:14
    # ... ตัดออกเพื่อความกระชับ
  chroma:
    image: chromadb/chroma:latest
    container_name: chromadb
    ports:
      - "8000:8000"
    volumes:
      - chroma_data:/chroma/chroma
```

## Presenter Notes (ข้อมูลสำหรับผู้บรรยาย)

> Key Takeaway: การติดตั้งระบบ RAG แบบสมบูรณ์สามารถทำได้ง่ายด้วย Docker Compose โดยรวม n8n, Vector Database และบริการอื่นๆ ที่จำเป็น

- ในส่วน workshop นี้ เราจะติดตั้ง ecosystem สำหรับระบบ RAG ประกอบด้วย:
  1. **n8n**: สำหรับจัดการ workflow
  2. **PostgreSQL**: เป็นฐานข้อมูลสำหรับ n8n
  3. **ChromaDB**: เป็น Vector Database สำหรับเก็บ embeddings
- อธิบายขั้นตอนการติดตั้ง:
  1. สร้างไฟล์ docker-compose.yml ตามตัวอย่าง
  2. รันคำสั่ง `docker-compose up -d` เพื่อเริ่มการทำงาน
  3. ตรวจสอบการทำงานที่ port ต่างๆ:
     - n8n: http://localhost:5678
     - ChromaDB: http://localhost:8000
- ยกตัวอย่างการสร้าง workflow สำหรับ RAG:
  1. การดึงข้อมูลจากไฟล์เอกสาร (PDF, DOCX)
  2. การแปลงเป็นข้อความและแบ่งเป็น chunks
  3. การส่งข้อความไปสร้าง embeddings ด้วย OpenAI API
  4. การเก็บ embeddings ใน ChromaDB
  5. การสร้าง webhook สำหรับรับคำถาม
- จุดสำคัญ: การเชื่อมต่อกับ ChromaDB ผ่าน API จะใช้ HTTP Request node

Technical Terms:
- Docker Compose
- ChromaDB
- Vector Database
- Embeddings
- HTTP API
- Webhook
- Text Chunking
- Knowledge Base
