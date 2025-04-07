# การตั้งค่า Task Runners ใน n8n

## ตารางเนื้อหา
| ระดับความยาก | วัตถุประสงค์ |
|---------------|----------------|
| ปานกลาง      | เรียนรู้การตั้งค่า Task Runners ใน n8n เพื่อเพิ่มประสิทธิภาพการทำงาน |
| URL      | [LINK](https://docs.n8n.io/hosting/configuration/task-runners/) |

## เนื้อหา
บทความนี้จะแนะนำการตั้งค่า Task Runners ใน n8n ซึ่งเป็นกลไกที่ช่วยให้การประมวลผลโค้ดใน Code Node มีความปลอดภัยและมีประสิทธิภาพมากขึ้น โดย Task Runners สามารถทำงานได้ในสองโหมด คือ Internal และ External

### โหมดการทำงานของ Task Runners
1. **Internal Mode**
   - Task Runner จะทำงานเป็น Child Process ของ n8n
   - ใช้ทรัพยากรของ n8n โดยตรง

2. **External Mode**
   - Task Runner จะทำงานแยกออกจาก n8n โดยใช้ Orchestrator เช่น Kubernetes
   - เหมาะสำหรับการใช้งานที่ต้องการความปลอดภัยและการแยกทรัพยากร

### การตั้งค่า External Mode

#### 1. ตั้งค่า Environment Variables
เพิ่ม Environment Variables ต่อไปนี้ใน n8n:
- เปิดใช้งาน Task Runners:
  ```bash
  N8N_RUNNERS_ENABLED=true
  ```
- ตั้งค่าโหมด External:
  ```bash
  N8N_RUNNERS_MODE=external
  ```
- กำหนด Shared Secret:
  ```bash
  N8N_RUNNERS_AUTH_TOKEN=<random_secure_shared_secret>
  ```
- ตั้งค่า Broker Address:
  ```bash
  N8N_RUNNERS_BROKER_LISTEN_ADDRESS=0.0.0.0
  ```

#### 2. ตั้งค่า Task Runner Container
ใช้ Docker Image ของ n8n และตั้งค่า Environment Variables:
- กำหนด Broker URI:
  ```bash
  N8N_RUNNERS_TASK_BROKER_URI=localhost:5679
  ```
- ตั้งค่า Concurrency:
  ```bash
  N8N_RUNNERS_MAX_CONCURRENCY=5
  ```
- ตั้งค่า Auto Shutdown Timeout:
  ```bash
  N8N_RUNNERS_AUTO_SHUTDOWN_TIMEOUT=15
  ```

#### 3. ทดสอบการทำงาน
- รัน Task Runner Container และตรวจสอบการเชื่อมต่อกับ n8n
- ใช้คำสั่ง `docker logs` เพื่อตรวจสอบสถานะ

### ตัวอย่างโค้ดและคำสั่ง

#### ตัวอย่างการตั้งค่าใน Docker Compose
```yaml
version: '3.1'
services:
  n8n:
    image: n8nio/n8n
    environment:
      - N8N_RUNNERS_ENABLED=true
      - N8N_RUNNERS_MODE=external
      - N8N_RUNNERS_AUTH_TOKEN=my_secure_token
      - N8N_RUNNERS_BROKER_LISTEN_ADDRESS=0.0.0.0
  task-runner:
    image: n8nio/n8n
    command: ["/usr/local/bin/task-runner-launcher", "javascript"]
    environment:
      - N8N_RUNNERS_TASK_BROKER_URI=n8n:5679
      - N8N_RUNNERS_AUTH_TOKEN=my_secure_token
      - N8N_RUNNERS_MAX_CONCURRENCY=5
```

### หมายเหตุ
- การตั้งค่า External Mode เหมาะสำหรับการใช้งานใน Production
- ควรตรวจสอบความปลอดภัยของ Shared Secret และการตั้งค่า Network