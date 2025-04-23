# ชุดเริ่มต้น AI แบบโฮสต์เอง (n8n-services)

**เทมเพลต Docker Compose สำหรับ n8n + AI + เครื่องมือประกอบ**  
เลือกใช้งานได้หลายรูปแบบตามความต้องการ

![n8n.io - Screenshot](https://github.com/racksync/workshop-ai/blob/main/hands-on/n8n-services/assets/n8n-demo.gif)

## รายการบริการ
- **n8n:** ระบบอัตโนมัติและการจัดการเวิร์กโฟลว์
- **Ollama:** LLM (Large Language Model) สำหรับการประมวลผลภาษา
- **Qdrant:** ฐานข้อมูลเวกเตอร์ (Vector Database) สำหรับการจัดเก็บและค้นหาข้อมูล
- **Redis:** ระบบจัดการแคชและคิว
- **MinIO:** ระบบจัดเก็บข้อมูลแบบ S3-compatible
- **PostgreSQL:** ฐานข้อมูลเชิงสัมพันธ์
- **PgAdmin:** UI สำหรับจัดการ PostgreSQL
- **Cloudflared:** เชื่อมต่อกับ Cloudflare Tunnel
- **RedisInsight:** UI สำหรับจัดการ Redis

---

## เลือกโหมดการใช้งาน

> **แต่ละโหมดเหมาะกับการใช้งานที่แตกต่างกัน:**
>
> - **โหมดที่ 1 (`docker-compose.yml`):** เหมาะสำหรับผู้เริ่มต้นหรือทดลองใช้งาน n8n + vector database แบบเบา ๆ ไม่ต้องการ LLM ใน container เหมาะกับการเรียนรู้หรือทดสอบฟีเจอร์พื้นฐาน
> - **โหมดที่ 2 (`docker-compose-ollama.yml`):** เหมาะกับผู้ที่ต้องการใช้งาน LLM (Ollama) ใน container เดียวกันกับ n8n สะดวกสำหรับการทดลอง AI workflow แบบครบวงจรในเครื่องเดียว
> - **โหมดที่ 3 (`docker-compose-full.yml`):** **แนะนำสำหรับการใช้งานจริง/องค์กร (on premise/production)** เพราะมีบริการเสริมครบถ้วน (Redis, MinIO ฯลฯ) รองรับงาน production, ปรับแต่งและควบคุมบริการต่าง ๆ ได้อิสระ, สามารถแยก Ollama ออกไปติดตั้งบนเครื่องอื่นหรือคลัสเตอร์ได้ง่าย เหมาะกับการเก็บข้อมูลภายในองค์กรและขยายระบบในอนาคต

### 1. `docker-compose.yml` (โหมดเบา/พื้นฐาน)
- **บริการ:**  
  - n8n (workflow automation)
  - PostgreSQL (database)
  - PgAdmin (UI จัดการ postgres)
  - Qdrant (vector database)
  - Cloudflared (Cloudflare tunnel)
- **ไม่มี Ollama**
- **เหมาะสำหรับ:** ทดลอง n8n + vector DB, ไม่ต้องการ LLM ใน container

> **ข้อดีของโหมดนี้:**  
> - เหมาะสำหรับผู้เริ่มต้นหรือผู้ที่ต้องการทดลองใช้งาน n8n และ vector database ได้อย่างรวดเร็ว  
> - ใช้งานง่าย ไม่ซับซ้อน บริการน้อย ดูแลรักษาง่าย  
> - เหมาะกับการเรียนรู้ ทดสอบ หรือสาธิตฟีเจอร์พื้นฐาน  
> - ใช้ทรัพยากรเครื่องน้อย เหมาะกับเครื่องสเปกต่ำหรือ dev environment

**เริ่มต้น:**
```bash
git clone https://github.com/racksync/workshop-ai.git
cd workshop-ai/hands-on/n8n-services
docker compose up -d
```

---

### 2. `docker-compose-ollama.yml` (โหมด AI ครบวงจร + Ollama ใน container)
- **บริการ:**  
  - n8n, postgres, pgadmin, qdrant, cloudflared
  - **Ollama** (เลือก profile: cpu/gpu/gpu-amd)
  - n8n-import (import credentials/workflows อัตโนมัติ)
- **เหมาะสำหรับ:** ต้องการรัน LLM (Ollama) ใน container เดียวกัน

> **ข้อดีของโหมดนี้:**  
> - ติดตั้งและใช้งาน LLM (Ollama) ได้ใน container เดียวกับ n8n สะดวกและรวดเร็ว  
> - เหมาะกับการทดลอง AI workflow แบบครบวงจรในเครื่องเดียว  
> - รองรับทั้ง CPU และ GPU (Nvidia/AMD) เลือก profile ได้ตามเครื่อง  
> - ลดขั้นตอนการตั้งค่า Ollama แยกต่างหาก เหมาะกับ dev/test/demo

**ตัวอย่างการรัน:**
- **CPU:**  
  ```bash
  docker compose -f docker-compose-ollama.yml --profile cpu up -d
  ```
- **Nvidia GPU:**  
  ```bash
  docker compose -f docker-compose-ollama.yml --profile gpu-nvidia up -d
  ```
- **AMD GPU:**  
  ```bash
  docker compose -f docker-compose-ollama.yml --profile gpu-amd up -d
  ```

---

### 3. `docker-compose-full.yml` (โหมด production-like ครบเครื่อง)
- **บริการ:**  
  - n8n, postgres, pgadmin, qdrant, cloudflared
  - **Redis** (cache/queue)
  - **RedisInsight** (UI จัดการ redis)
  - **MinIO** (S3-compatible object storage)
- **ไม่มี Ollama ในตัว** (ต้องรัน Ollama แยกเอง หรือใช้ Ollama บน host)
- **เหมาะสำหรับ:** สภาพแวดล้อมครบเครื่อง, เชื่อมต่อ Ollama ภายนอก

> **ข้อดีของโหมดนี้ (on premise/production):**  
> - เหมาะกับการใช้งานในองค์กรหรือ production ที่ต้องการความเสถียรและความปลอดภัย  
> - มีบริการเสริมครบถ้วน (Redis, MinIO ฯลฯ) รองรับงานจริงและการขยายระบบ  
> - ปรับแต่งและควบคุมบริการต่าง ๆ ได้อิสระ  
> - สามารถแยก Ollama ออกไปติดตั้งบนเครื่องอื่นหรือคลัสเตอร์ได้ง่าย  
> - เหมาะกับการใช้งาน on premise ที่ต้องการเก็บข้อมูลภายในองค์กร

**ตัวอย่างการรัน (CPU):**
```bash
docker compose -f docker-compose-full.yml up -d
```
> หากต้องการใช้ Ollama ให้ติดตั้ง Ollama บนเครื่อง หรือรัน container Ollama แยกต่างหาก  
> จากนั้นตั้งค่า `OLLAMA_HOST` ใน `.env` หรือ environment variable ให้ชี้ไปที่ Ollama instance ที่ต้องการ

---

## การเชื่อมต่อ Ollama (LLM)

- **Ollama ใน container:** ใช้ `docker-compose-ollama.yml` พร้อม profile ที่ต้องการ
- **Ollama บน host (เช่น Mac/Windows):**  
  - ติดตั้ง Ollama [ดูวิธีที่นี่](https://ollama.com/)
  - ตั้งค่า `OLLAMA_HOST=host.docker.internal:11434` (ค่าดีฟอลต์ใน compose รองรับแล้ว)
  - รัน n8n ด้วย compose ปกติหรือ full

---

## การเข้าถึงบริการ

- **n8n:** [http://localhost:5678/](http://localhost:5678/)
- **PgAdmin:** [http://localhost:5050/](http://localhost:5050/) (user/pass ดูใน `.env`)
- **Qdrant:** [http://localhost:6333/](http://localhost:6333/)
- **RedisInsight:** [http://localhost:5540/](http://localhost:5540/) (เฉพาะ full)
- **MinIO:** [http://localhost:9000/](http://localhost:9000/) (เฉพาะ full, UI: [http://localhost:9001/](http://localhost:9001/))

---

## การอัปเกรด

- **อัปเดตบริการ:**
  ```bash
  docker compose -f docker-compose-full.yml pull
  docker compose -f docker-compose-full.yml up -d
  ```
  (เปลี่ยนชื่อไฟล์ compose ตามที่ใช้งาน)

---

## โฟลเดอร์ข้อมูล

- `./n8n_data` : ข้อมูล workflow n8n
- `./shared` : โฟลเดอร์แชร์ไฟล์เข้า workflow (mount ที่ `/data/shared`)
- `./qdrant_data`, `./redis_data`, `./minio_data` : ข้อมูลบริการประกอบ

---

## เอกสารและแหล่งเรียนรู้

- [AI agents สำหรับนักพัฒนา: จากทฤษฎีสู่การปฏิบัติด้วย n8n](https://blog.n8n.io/ai-agents/)
- [บทแนะนำ: สร้างเวิร์กโฟลว์ AI ใน n8n](https://docs.n8n.io/advanced-ai/intro-tutorial/)
- [แนวคิด Langchain ใน n8n](https://docs.n8n.io/advanced-ai/langchain/langchain-n8n/)
- [ฐานข้อมูลเวกเตอร์คืออะไร?](https://docs.n8n.io/advanced-ai/examples/understand-vector-databases/)

---

## สนับสนุนและชุมชน

- ถาม-ตอบและพูดคุย: [n8n Forum](https://community.n8n.io/)

---

## License

โปรเจกต์นี้อยู่ภายใต้ Apache License 2.0 - ดูไฟล์ [LICENSE](LICENSE) สำหรับรายละเอียด
