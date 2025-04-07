# ติดตั้ง n8n บน GCP Free Tier

## ตารางเนื้อหา
| ระดับความยาก | วัตถุประสงค์ |
|---------------|----------------|
| ง่าย          | เรียนรู้การติดตั้ง n8n บน GCP VM โดยใช้ Free Tier และการตั้งค่า Firewall เพื่อใช้งานผ่าน External IP |

## เนื้อหา
บทความนี้จะแนะนำวิธีการติดตั้ง n8n บน Google Cloud Platform (GCP) VM โดยใช้ Free Tier ซึ่งไม่จำเป็นต้องมี Domain ของตัวเอง สามารถใช้งานผ่าน External IP ได้ทันที เหมาะสำหรับผู้ที่ต้องการทดลองใช้งาน n8n แบบ Self-Hosting

### ขั้นตอนการติดตั้ง

#### 1. สมัครใช้งาน GCP และเปิดใช้งาน Free Tier
- สมัครบัญชี GCP และเปิดใช้งาน Free Tier ซึ่งจะได้รับสิทธิ์ใช้งาน VM ประเภท e2-micro (1 vCPU, 1GB RAM) และพื้นที่เก็บข้อมูล 30GB แบบ Standard Persistent Disk ฟรี

#### 2. สร้าง VM Instance
- สร้าง VM Instance โดยเลือกประเภท e2-micro และตั้งค่าให้อนุญาต HTTP และ HTTPS Traffic

#### 3. ติดตั้ง Docker และ Docker-Compose
- SSH เข้าไปยัง VM Instance และติดตั้ง Docker
- ติดตั้ง Docker-Compose โดยทำตามคำแนะนำใน [n8n Docker-Compose Guide](https://docs.n8n.io/hosting/installation/server-setups/docker-compose/#3-install-docker-compose)

#### 4. ติดตั้งและรัน n8n
- ใช้คำสั่งด้านล่างเพื่อเริ่มต้น n8n:
  ```bash
  docker-compose up -d
  ```

#### 5. ตั้งค่า Firewall และเปิดพอร์ต 5678
- ไปที่ **VPC Network > Firewall** ใน GCP
- สร้างกฎ Firewall ใหม่เพื่ออนุญาตการใช้งาน TCP บนพอร์ต 5678:
  - ชื่อ: `allow-n8n`
  - Target: All instances in the network
  - Source IP Ranges: `0.0.0.0/0`
  - Protocol & Ports: TCP, port `5678`

#### 6. ทดสอบการใช้งาน
- เปิดเบราว์เซอร์และเข้าไปที่ URL:
  ```
  http://<YOUR_VM_EXTERNAL_IP>:5678
  ```

### หมายเหตุ
- การตั้งค่านี้เหมาะสำหรับการทดลองใช้งานและการเรียนรู้เท่านั้น ไม่เหมาะสำหรับงานที่ต้องการประสิทธิภาพสูง
- ควรตรวจสอบการใช้งานและค่าใช้จ่ายเพื่อให้อยู่ในขอบเขตของ Free Tier