# Prerequisites - สิ่งที่ต้องเตรียมก่อนเข้าร่วม Workshop

![AI Workshop Preparation](https://www.google.com/search?q=ai+workshop+preparation&tbm=isch)

การเตรียมความพร้อมก่อนเข้าร่วม Workshop จะช่วยให้คุณได้รับประโยชน์สูงสุดจากการเรียนรู้ และสามารถทำ hands-on exercises ได้อย่างราบรื่น ขอให้เตรียมสิ่งต่อไปนี้ให้พร้อมก่อนวันเริ่ม workshop

## ความรู้พื้นฐานที่ควรมี

เพื่อให้ได้ประโยชน์สูงสุดจาก workshop นี้ ผู้เข้าร่วมควรมีความรู้พื้นฐานดังนี้:

- ความเข้าใจพื้นฐานเกี่ยวกับ AI และ Machine Learning
- พื้นฐานการเขียนโปรแกรม (JavaScript หรือ Python)
- ความเข้าใจเบื้องต้นเกี่ยวกับ REST APIs
- ประสบการณ์การใช้ Command Line Interface (CLI) เบื้องต้น

> **หมายเหตุ:** ไม่จำเป็นต้องเป็นผู้เชี่ยวชาญด้าน AI หรือการเขียนโปรแกรมขั้นสูง workshop นี้ออกแบบมาให้เข้าใจง่ายสำหรับผู้ที่มีพื้นฐานเบื้องต้น

## เครื่องมือและซอฟต์แวร์ที่ต้องติดตั้ง

โปรดติดตั้งซอฟต์แวร์ต่อไปนี้ล่วงหน้า:

### จำเป็นต้องมี

- **Web Browser** รุ่นล่าสุด (Chrome, Firefox, Edge, หรือ Safari)
- **Code Editor** เช่น Visual Studio Code ([download](https://code.visualstudio.com/))
- **Node.js** (เวอร์ชัน 18+ แนะนำ) ([download](https://nodejs.org/))
  ```bash
  # ตรวจสอบการติดตั้ง
  node --version
  npm --version
  ```
- **Python** (เวอร์ชัน 3.8+ แนะนำ) ([download](https://www.python.org/downloads/))
  ```bash
  # ตรวจสอบการติดตั้ง
  python --version
  # หรือ
  python3 --version
  ```

### แนะนำให้มี

- **Docker** สำหรับรัน containers ([download](https://www.docker.com/products/docker-desktop/))
  ```bash
  # ตรวจสอบการติดตั้ง
  docker --version
  ```
- **Git** สำหรับการจัดการโค้ด ([download](https://git-scm.com/downloads))
  ```bash
  # ตรวจสอบการติดตั้ง
  git --version
  ```
- **Ollama** สำหรับรัน Local LLMs ([download](https://ollama.com/download))

## API Keys และ Accounts ที่ต้องเตรียม

คุณจะได้ใช้งาน APIs จากผู้ให้บริการต่างๆ ในระหว่าง workshop โปรดลงทะเบียนและเตรียม API keys ดังนี้:

### OpenAI API (สำคัญ)

1. ลงทะเบียนที่ [OpenAI Platform](https://platform.openai.com/)
2. สร้าง API key ใหม่
3. เติมเงินขั้นต่ำ $5 (หรือตามที่ต้องการใช้งาน)
4. เก็บ API key ไว้ในที่ปลอดภัย (จะใช้ในระหว่าง workshop)

### Google AI Studio / Gemini API (ทางเลือก)

1. ลงทะเบียนที่ [Google AI Studio](https://aistudio.google.com/)
2. สร้าง API key สำหรับใช้งาน Gemini
3. เก็บ API key ไว้ในที่ปลอดภัย

### GitHub Account (แนะนำ)

ลงทะเบียน [GitHub account](https://github.com/signup) สำหรับการแชร์และจัดการโค้ด

## ข้อกำหนดด้านฮาร์ดแวร์

### สำหรับส่วนใหญ่ของ Workshop

- **CPU**: 2 cores ขึ้นไป
- **RAM**: อย่างน้อย 8GB (แนะนำ 16GB ขึ้นไป)
- **พื้นที่ว่าง**: อย่างน้อย 20GB
- **การเชื่อมต่ออินเทอร์เน็ต**: ความเร็วที่เสถียร

### สำหรับการรัน Local LLMs (Ollama)

การรัน LLMs บนเครื่องส่วนตัวต้องการทรัพยากรเพิ่มเติมตามขนาดโมเดล:

| ขนาดโมเดล | RAM ขั้นต่ำ | RAM แนะนำ | GPU Memory |
|------------|---------|----------------|------------|
| 7B         | 8GB     | 16GB           | 8GB        |
| 13B        | 16GB    | 32GB           | 16GB       |
| 33B+       | 32GB+   | 64GB+          | 24GB+      |

> **หมายเหตุ:** สำหรับผู้ที่ไม่มีฮาร์ดแวร์ที่รองรับ local LLMs เราจะใช้ cloud-based APIs เป็นทางเลือก

## การทดสอบการติดตั้งก่อนเริ่ม Workshop

เพื่อให้แน่ใจว่าสภาพแวดล้อมพร้อมใช้งาน คุณสามารถรันคำสั่งทดสอบดังต่อไปนี้:


### ทดสอบ Ollama (ถ้าติดตั้งไว้)

```bash
# ทดสอบดึงโมเดลขนาดเล็ก
ollama pull tinyllama
```

## การเตรียมการด้านอื่นๆ


### 1. ตั้งงบประมาณสำหรับการใช้ API

เตรียมงบประมาณเล็กน้อยสำหรับค่า API (โดยปกติประมาณ $5-10 เพียงพอสำหรับ workshop):
- OpenAI API: ราคาขึ้นอยู่กับโมเดลและจำนวน tokens
  - GPT-4: $0.03 / 1K tokens (input), $0.06 / 1K tokens (output)
  - GPT-3.5-turbo: $0.0005 / 1K tokens (input), $0.0015 / 1K tokens (output)
- Gemini Pro: $0.0025 / 1K tokens (input), $0.0075 / 1K tokens (output)


### 2. เตรียมข้อมูลสำหรับ Project ทดลอง

หากมีข้อมูลเฉพาะที่ต้องการใช้สำหรับโปรเจค RAG หรือ AI Agents ให้เตรียมไฟล์ข้อมูลเหล่านั้นไว้ล่วงหน้า (เช่น PDF, CSV, หรือข้อมูลในรูปแบบอื่นๆ)


## RACKSYNC CO., LTD.

[RACKSYNC](https://github.com/racksync) เป็นบริษัทที่มีความเชี่ยวชาญในการพัฒนาโซลูชั่นด้าน IoT และระบบอัตโนมัติ เรามุ่งมั่นในการสร้างเทคโนโลยีที่เชื่อมต่อโลกเข้าด้วยกันผ่านระบบ IoT ที่มีประสิทธิภาพและเสถียร

### บริการของเรา
- การออกแบบและพัฒนาระบบ IoT แบบครบวงจร
- โซลูชั่นเชื่อมต่อสำหรับอุตสาหกรรม 4.0
- ระบบอัตโนมัติสำหรับบ้านและอาคารอัจฉริยะ
- การฝึกอบรมและเวิร์คช็อปด้าน IoT

## ติดต่อเรา
- **โทร**: 08 5880 8885
- **อีเมล**: info@racksync.com
- **เว็บไซต์**: https://racksync.com
- **Facebook**: https://www.facebook.com/racksync

© 2007-2025 RACKSYNC CO., LTD. All rights reserved.
