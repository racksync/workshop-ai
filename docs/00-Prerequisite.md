# Session 0: การเตรียมความพร้อมก่อนเรียน

## 🔍 ภาพรวม

เพื่อให้การเรียนรู้ในหลักสูตร AI Workshop ดำเนินไปอย่างราบรื่นและได้ประสิทธิภาพสูงสุด คุณควรเตรียมความพร้อมทั้งในด้านความรู้พื้นฐาน ซอฟต์แวร์ที่จำเป็น และทรัพยากรต่างๆ ที่ต้องใช้ เอกสารนี้รวบรวมสิ่งที่ต้องเตรียมทั้งหมดสำหรับการเรียนทั้ง 8 เซสชัน



## 📋 ความรู้พื้นฐานที่ควรมี

```mermaid
mindmap
  root((ความรู้พื้นฐาน))
    ::icon(fa fa-brain)
    (พื้นฐานทั่วไป)
      ::icon(fa fa-globe)
      [ความเข้าใจคอมพิวเตอร์และอินเทอร์เน็ต]
      [ทักษะการแก้ไขปัญหาเบื้องต้น]
      [ความคุ้นเคยกับเว็บเบราว์เซอร์]
    (พื้นฐานด้านการพัฒนา)
      ::icon(fa fa-code)
      [พื้นฐานการเขียนโปรแกรม]
      [JavaScript/Python]
      [HTML และ JSON]
      [API และการเชื่อมต่อระหว่างระบบ]
    (พื้นฐานด้าน AI)
      ::icon(fa fa-robot)
      [ความเข้าใจพื้นฐาน AI]
      [แนวคิดโมเดลภาษาขนาดใหญ่]
```

### พื้นฐานทั่วไป
- ความเข้าใจพื้นฐานเกี่ยวกับคอมพิวเตอร์และระบบอินเทอร์เน็ต
- ทักษะในการแก้ไขปัญหาทั่วไปเบื้องต้น
- ความคุ้นเคยกับการใช้งานเว็บเบราว์เซอร์ต่างๆ

### พื้นฐานด้านการพัฒนา
- ความเข้าใจพื้นฐานเกี่ยวกับการเขียนโปรแกรม (ไม่จำเป็นต้องเชี่ยวชาญ)
- พื้นฐาน [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript) หรือ [Python](https://www.python.org/) จะช่วยให้เข้าใจตัวอย่างโค้ดได้ง่ายขึ้น
- ความรู้เบื้องต้นเกี่ยวกับ [HTML](https://developer.mozilla.org/en-US/docs/Web/HTML) และ [JSON](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON)
- ความเข้าใจพื้นฐานเกี่ยวกับ [API](https://www.redhat.com/en/topics/api/what-are-application-programming-interfaces) และการเชื่อมต่อระหว่างระบบ

### พื้นฐานด้าน AI
- ความเข้าใจเบื้องต้นเกี่ยวกับ AI และการเรียนรู้ของเครื่อง (ไม่จำเป็นต้องลึกซึ้ง)
- ความคุ้นเคยกับแนวคิดของ [โมเดลภาษาขนาดใหญ่ (LLMs)](https://en.wikipedia.org/wiki/Large_language_model) จะเป็นประโยชน์ต่อการเรียน

## 🛠️ เครื่องมือและซอฟต์แวร์ที่ต้องติดตั้ง

### ซอฟต์แวร์หลัก
- **[Docker](https://www.docker.com/) และ [Docker Compose](https://docs.docker.com/compose/)** - จำเป็นสำหรับเซสชัน 2, 4, 6 และ 8
- **[Node.js](https://nodejs.org/)** (เวอร์ชัน 14.x หรือใหม่กว่า) และ [npm](https://www.npmjs.com/) หรือ [yarn](https://yarnpkg.com/) - สำหรับเซสชัน 5 และ 7
- **[Python](https://www.python.org/)** (เวอร์ชัน 3.7+) และ [pip](https://pip.pypa.io/en/stable/) - สำหรับเซสชัน 4, 5 และ 8
- **[Git](https://git-scm.com/)** - สำหรับการจัดการเวอร์ชันโค้ด และดาวน์โหลดตัวอย่างโปรเจกต์

### สำหรับ n8n และ RAG (เซสชัน 2 และ 4)
- **[Docker Compose](https://docs.docker.com/compose/)** - สำหรับติดตั้ง [n8n](https://n8n.io/), [PostgreSQL](https://www.postgresql.org/), [ChromaDB](https://www.trychroma.com/) และ [MinIO](https://min.io/)
- **REST Client** (เช่น [Postman](https://www.postman.com/), [Insomnia](https://insomnia.rest/)) - สำหรับทดสอบ API

### สำหรับ Ollama และ Open-WebUI (เซสชัน 6)
- **[Docker](https://www.docker.com/)** สำหรับติดตั้ง [Open-WebUI](https://github.com/open-webui/open-webui) และ [Ollama](https://ollama.ai/)
- ถ้าไม่ใช้ Docker: ติดตั้ง Ollama ตามระบบปฏิบัติการของคุณ
  - macOS: `brew install ollama`
  - Linux: `curl -fsSL https://ollama.com/install.sh | sh`
  - Windows: ดาวน์โหลดจาก [https://ollama.com/download](https://ollama.com/download)

### สำหรับ Bolt Framework (เซสชัน 7)
- **Text editor** หรือ IDE (เช่น [Visual Studio Code](https://code.visualstudio.com/))
- **[Node.js](https://nodejs.org/)** (เวอร์ชัน 14.x หรือใหม่กว่า)
- **[npm](https://www.npmjs.com/)** หรือ **[yarn](https://yarnpkg.com/)**
- **Bolt CLI**: `npm install -g @boltframework/cli` หรือ `yarn global add @boltframework/cli`

## 🔑 API Keys และบัญชีที่ต้องเตรียม

### สำหรับ OpenAI API (เซสชัน 5 และอื่นๆ)
- สมัครบัญชีที่ [platform.openai.com](https://platform.openai.com)
- สร้าง API Key และเก็บไว้อย่างปลอดภัย
- เติมเครดิตหากต้องการใช้งานต่อหลังจากที่โควต้าทดลองฟรีหมด

### สำหรับ Google Gemini API (เซสชัน 5 และ 6)
- สมัครบัญชีที่ [Google AI Studio](https://aistudio.google.com/)
- สร้าง API Key สำหรับใช้กับ [Gemini API](https://ai.google.dev/)

### สำหรับ MinIO (หากใช้ในเซสชัน 4 - RAG)
- เตรียม Access Key และ Secret Key สำหรับ [MinIO](https://min.io/)
- ค่าเริ่มต้นคือ `minioadmin` / `minioadmin`

## 💻 ทรัพยากรฮาร์ดแวร์ที่แนะนำ

### ความต้องการขั้นต่ำ
- **CPU**: Dual-core หรือสูงกว่า
- **RAM**: อย่างน้อย 8GB (แนะนำ 16GB+ สำหรับการรันโมเดล Ollama ขนาดใหญ่)
- **พื้นที่เก็บข้อมูล**: อย่างน้อย 10GB สำหรับการติดตั้งซอฟต์แวร์และโมเดลต่างๆ
- **การเชื่อมต่ออินเทอร์เน็ต**: ความเร็วปานกลางถึงสูง

### ทรัพยากรแนะนำเพื่อประสิทธิภาพที่ดียิ่งขึ้น
- **CPU**: Quad-core หรือสูงกว่า
- **RAM**: 16GB หรือมากกว่า
- **GPU**: [NVIDIA GPU](https://www.nvidia.com/en-us/gpu/) สำหรับเร่งความเร็วของ Ollama (ถ้ามี)
- **พื้นที่เก็บข้อมูล**: 20GB หรือมากกว่า
- **การเชื่อมต่ออินเทอร์เน็ต**: ความเร็วสูง (สำคัญสำหรับการดาวน์โหลดโมเดลขนาดใหญ่)

## 📂 ข้อมูลและไฟล์ตัวอย่าง

- ตัวอย่างเอกสารหรือไฟล์ข้อความสำหรับการทดสอบระบบ RAG (เซสชัน 4)
- ไฟล์ docker-compose.yml สำหรับการติดตั้ง n8n, Open-WebUI และ Ollama (จะมีให้ในแต่ละเซสชัน)
- โค้ดตัวอย่างสำหรับการใช้งาน OpenAI API และ Gemini API (จะมีให้ในเซสชัน 5)
- ตัวอย่าง Modelfiles สำหรับการปรับแต่ง Ollama (จะมีให้ในเซสชัน 6)

## 📌 การเตรียมสภาพแวดล้อม (Environment Setup)

### สำหรับ Docker
1. ติดตั้ง Docker และ Docker Compose:
   - Windows/Mac: ดาวน์โหลดและติดตั้ง [Docker Desktop](https://www.docker.com/products/docker-desktop)
   - Linux: [ติดตั้ง Docker Engine](https://docs.docker.com/engine/install/) และ [Docker Compose](https://docs.docker.com/compose/install/)

2. ตรวจสอบการติดตั้ง:
   ```bash
   docker --version
   docker-compose --version
   ```

### สำหรับ Node.js และ npm
1. ติดตั้ง Node.js:
   - ดาวน์โหลดและติดตั้งจาก [nodejs.org](https://nodejs.org/)
   - หรือใช้ [NVM (Node Version Manager)](https://github.com/nvm-sh/nvm)

2. ตรวจสอบการติดตั้ง:
   ```bash
   node --version
   npm --version
   ```

### สำหรับ Python
1. ติดตั้ง Python:
   - ดาวน์โหลดและติดตั้งจาก [python.org](https://www.python.org/downloads/)
   - หรือใช้ [Anaconda](https://www.anaconda.com/)/[Miniconda](https://docs.conda.io/en/latest/miniconda.html)

2. ตรวจสอบการติดตั้ง:
   ```bash
   python --version
   pip --version
   ```

## 🌐 แหล่งข้อมูลเพิ่มเติม

- [Docker Documentation](https://docs.docker.com/)
- [n8n Documentation](https://docs.n8n.io/)
- [OpenAI API Documentation](https://platform.openai.com/docs/)
- [Gemini API Documentation](https://ai.google.dev/docs)
- [Ollama Documentation](https://ollama.com/docs)
- [ChromaDB Documentation](https://docs.trychroma.com/)
- [Bolt Framework Documentation](https://boltframework.com/docs)
- [แนะนำการใช้งาน Git เบื้องต้น](https://git-scm.com/book/th/v2)
- [คู่มือการเริ่มต้นใช้งาน Python ภาษาไทย](https://www.w3schools.com/python/default.asp)

## ⚠️ หมายเหตุ

- ตรวจสอบให้แน่ใจว่าคุณมีสิทธิ์ในการติดตั้งซอฟต์แวร์บนเครื่องคอมพิวเตอร์ที่จะใช้
- หากเครื่องคอมพิวเตอร์ของคุณมีทรัพยากรจำกัด คุณสามารถใช้บริการคลาวด์อย่าง [Google Colab](https://colab.research.google.com/), [AWS](https://aws.amazon.com/), หรือ [DigitalOcean](https://www.digitalocean.com/) ในบางส่วนของ workshop ได้
- เตรียมพื้นที่ว่างในดิสก์ให้เพียงพอ โดยเฉพาะสำหรับการดาวน์โหลดโมเดล Ollama ที่มักมีขนาดใหญ่
- ควรทดสอบความเร็วอินเทอร์เน็ตของคุณก่อนวันอบรม เนื่องจากการดาวน์โหลดโมเดลบางตัวอาจใช้เวลานานพอสมควร

ด้วยการเตรียมความพร้อมตามรายการข้างต้น คุณจะสามารถเข้าร่วม AI Workshop ได้อย่างราบรื่นและได้รับประโยชน์สูงสุดจากการเรียนรู้ในทุกเซสชัน!

