# การติดตั้ง n8n ด้วย Docker Compose

## ตารางเนื้อหา
| ระดับความยาก | วัตถุประสงค์ |
|---------------|----------------|
| ปานกลาง      | เรียนรู้การติดตั้งและตั้งค่า n8n ด้วย Docker Compose |
| URL           | [LINK](https://docs.n8n.io/hosting/installation/server-setups/docker-compose/) |

## เนื้อหา
บทความนี้จะแนะนำวิธีการติดตั้ง n8n ด้วย Docker Compose อย่างละเอียด เหมาะสำหรับผู้ที่ต้องการใช้งาน n8n แบบ Self-Hosting

### ขั้นตอนการติดตั้ง

#### 1. ติดตั้ง Docker และ Docker Compose
- ติดตั้ง Docker และ Docker Compose ตามคำแนะนำในเอกสารของ [Docker](https://docs.docker.com/get-docker/) และ [Docker Compose](https://docs.docker.com/compose/install/)
- ตรวจสอบการติดตั้งด้วยคำสั่ง:
  ```bash
  docker --version
  docker compose version
  ```

#### 2. สร้างโฟลเดอร์โปรเจกต์
- สร้างโฟลเดอร์สำหรับเก็บไฟล์การตั้งค่า:
  ```bash
  mkdir n8n-compose && cd n8n-compose
  ```

#### 3. สร้างไฟล์ `.env`
- สร้างไฟล์ `.env` เพื่อกำหนดค่าการตั้งค่า:
  ```env
  DOMAIN_NAME=example.com
  SUBDOMAIN=n8n
  GENERIC_TIMEZONE=Europe/Berlin
  SSL_EMAIL=user@example.com
  ```

#### 4. สร้างโฟลเดอร์ `local-files`
- สร้างโฟลเดอร์สำหรับแชร์ไฟล์ระหว่าง n8n และระบบโฮสต์:
  ```bash
  mkdir local-files
  ```

#### 5. สร้างไฟล์ `docker-compose.yml`
- สร้างไฟล์ `docker-compose.yml` ด้วยเนื้อหาดังนี้:
  ```yaml
  version: '3.1'
  services:
    traefik:
      image: traefik:v2.4
      command:
        - "--api.insecure=true"
        - "--providers.docker=true"
        - "--entrypoints.web.address=:80"
        - "--entrypoints.websecure.address=:443"
        - "--certificatesresolvers.myresolver.acme.tlschallenge=true"
        - "--certificatesresolvers.myresolver.acme.email=${SSL_EMAIL}"
        - "--certificatesresolvers.myresolver.acme.storage=/letsencrypt/acme.json"
      ports:
        - "80:80"
        - "443:443"
      volumes:
        - "/var/run/docker.sock:/var/run/docker.sock:ro"
        - "./letsencrypt:/letsencrypt"
    n8n:
      image: n8nio/n8n
      environment:
        - N8N_BASIC_AUTH_ACTIVE=true
        - N8N_BASIC_AUTH_USER=admin
        - N8N_BASIC_AUTH_PASSWORD=admin
        - N8N_HOST=${SUBDOMAIN}.${DOMAIN_NAME}
        - N8N_PORT=5678
        - N8N_PROTOCOL=https
        - GENERIC_TIMEZONE=${GENERIC_TIMEZONE}
      volumes:
        - "./local-files:/files"
      labels:
        - "traefik.http.routers.n8n.rule=Host(`${SUBDOMAIN}.${DOMAIN_NAME}`)"
        - "traefik.http.routers.n8n.entrypoints=websecure"
        - "traefik.http.routers.n8n.tls.certresolver=myresolver"
  ```

#### 6. เริ่มต้น Docker Compose
- รันคำสั่งเพื่อเริ่มต้นบริการ:
  ```bash
  docker compose up -d
  ```

#### 7. ทดสอบการใช้งาน
- เปิดเบราว์เซอร์และเข้าไปที่ URL:
  ```
  https://n8n.example.com
  ```

### หมายเหตุ
- การตั้งค่านี้เหมาะสำหรับการใช้งานใน Production
- ควรตรวจสอบความปลอดภัยของรหัสผ่านและการตั้งค่า Network