# ชุดเริ่มต้น AI แบบโฮสต์เอง

**ชุดเริ่มต้น AI แบบโฮสต์เอง** เป็นเทมเพลต Docker Compose แบบโอเพ่นซอร์สที่ออกแบบมาเพื่อเริ่มต้นสภาพแวดล้อมการพัฒนา AI และ low-code ในเครื่องได้อย่างรวดเร็ว

![n8n.io - Screenshot](https://github.com/racksync/workshop-ai/blob/main/hands-on/n8n-services/assets/n8n-demo.gif)

จัดทำโดย <https://github.com/n8n-io> ซึ่งรวมแพลตฟอร์ม n8n แบบโฮสต์เองเข้ากับรายการผลิตภัณฑ์และส่วนประกอบ AI ที่เข้ากันได้ เพื่อเริ่มต้นสร้างเวิร์กโฟลว์ AI แบบโฮสต์เองได้อย่างรวดเร็ว

> [!TIP]
> [อ่านประกาศ](https://blog.n8n.io/self-hosted-ai/)

### สิ่งที่รวมอยู่

✅ [**n8n แบบโฮสต์เอง**](https://n8n.io/) - แพลตฟอร์ม low-code ที่มีการผสานรวมกว่า 400 รายการและส่วนประกอบ AI ขั้นสูง

✅ [**Ollama**](https://ollama.com/) - แพลตฟอร์ม LLM ข้ามแพลตฟอร์มสำหรับติดตั้งและรัน LLMs ล่าสุดในเครื่อง

✅ [**Qdrant**](https://qdrant.tech/) - ฐานข้อมูลเวกเตอร์แบบโอเพ่นซอร์สที่มีประสิทธิภาพสูงพร้อม API ที่ครอบคลุม

✅ [**PostgreSQL**](https://www.postgresql.org/) - ฐานข้อมูลที่แข็งแกร่งในโลก Data Engineering ที่สามารถจัดการข้อมูลจำนวนมากได้อย่างปลอดภัย

### สิ่งที่คุณสามารถสร้างได้

⭐️ **AI Agents** สำหรับการจัดตารางนัดหมาย

⭐️ **สรุปไฟล์ PDF ของบริษัท** อย่างปลอดภัยโดยไม่มีการรั่วไหลของข้อมูล

⭐️ **บอท Slack ที่ฉลาดขึ้น** เพื่อการสื่อสารในบริษัทและการดำเนินงานด้าน IT ที่ดีขึ้น

⭐️ **การวิเคราะห์เอกสารทางการเงินส่วนตัว** ด้วยต้นทุนที่ต่ำที่สุด



### การรัน n8n ด้วย Docker Compose ด้วยวิธีที่ง่ายที่สุด

Clone repository นี้ไปยังเครื่องของคุณ:

```bash
git clone https://github.com/racksync/workshop-ai.git
cd hands-on/n8n-services
docker compose up -d
```

#### สำหรับผู้ใช้ Nvidia GPU

```
cd workshop-ai/hands-on/n8n-services
docker compose -f docker-compose-full.yml --profile gpu-nvidia up
```

> [!NOTE]
> หากคุณยังไม่เคยใช้ Nvidia GPU กับ Docker มาก่อน โปรดทำตาม
> [คำแนะนำ Docker ของ Ollama](https://github.com/ollama/ollama/blob/main/docs/docker.md)

### สำหรับผู้ใช้ AMD GPU บน Linux

```
cd workshop-ai/hands-on/n8n-services
docker compose -f docker-compose-full.yml --profile gpu-amd up
```

#### สำหรับผู้ใช้ Mac / Apple Silicon

หากคุณใช้ Mac ที่มีโปรเซสเซอร์ M1 หรือใหม่กว่า คุณไม่สามารถเปิดเผย GPU ของคุณให้กับอินสแตนซ์ Docker ได้ น่าเสียดายที่มีสองตัวเลือกในกรณีนี้:

1. รันชุดเริ่มต้นบน CPU ทั้งหมด เช่นในส่วน "สำหรับทุกคน" ด้านล่าง
2. รัน Ollama บน Mac ของคุณเพื่อการประมวลผลที่เร็วขึ้น และเชื่อมต่อกับ n8n instance

หากคุณต้องการรัน Ollama บน Mac ของคุณ โปรดตรวจสอบ
[หน้าแรกของ Ollama](https://ollama.com/)
สำหรับคำแนะนำการติดตั้ง และรันชุดเริ่มต้นดังนี้:

```
cd workshop-ai/hands-on/n8n-services
docker compose up
```

##### สำหรับผู้ใช้ Mac ที่รัน OLLAMA ในเครื่อง

หากคุณกำลังรัน OLLAMA ในเครื่องบน Mac ของคุณ (ไม่ใช่ใน Docker) คุณจำเป็นต้องแก้ไขตัวแปรสภาพแวดล้อม OLLAMA_HOST ในการกำหนดค่าบริการ n8n อัปเดตส่วน x-n8n ในไฟล์ Docker Compose ของคุณดังนี้:

```yaml
x-n8n: &service-n8n
  # ...existing code...
  environment:
    # ...existing code...
    - OLLAMA_HOST=host.docker.internal:11434
```

นอกจากนี้ หลังจากที่คุณเห็นข้อความ "Editor is now accessible via: <http://localhost:5678/>":

1. ไปที่ <http://localhost:5678/home/credentials>
2. คลิกที่ "Local Ollama service"
3. เปลี่ยน Base URL เป็น "http://host.docker.internal:11434/"

#### สำหรับทุกคน

```
cd workshop-ai/hands-on/n8n-services
docker compose -f docker-compose-full.yml --profile cpu up
```

## ⚡️ เริ่มต้นใช้งานอย่างรวดเร็ว

หัวใจหลักของชุดเริ่มต้น AI แบบโฮสต์เองคือไฟล์ Docker Compose ที่กำหนดค่าไว้ล่วงหน้าด้วยการตั้งค่าเครือข่ายและการจัดเก็บข้อมูล เพื่อลดความจำเป็นในการติดตั้งเพิ่มเติม
หลังจากทำตามขั้นตอนการติดตั้งข้างต้นแล้ว ให้ทำตามขั้นตอนด้านล่างเพื่อเริ่มต้น:

1. เปิด <http://localhost:5678/> ในเบราว์เซอร์ของคุณเพื่อกำหนดค่า n8n คุณจะต้องทำเพียงครั้งเดียว
2. เปิดเวิร์กโฟลว์ที่รวมอยู่:
   <http://localhost:5678/workflow/srOnR8PAY3u4RSwb>
3. คลิกปุ่ม **Chat** ที่ด้านล่างของแคนวาสเพื่อเริ่มรันเวิร์กโฟลว์
4. หากนี่เป็นครั้งแรกที่คุณรันเวิร์กโฟลว์ คุณอาจต้องรอจนกว่า Ollama จะดาวน์โหลด Llama3.2 เสร็จสิ้น คุณสามารถตรวจสอบบันทึกคอนโซล Docker เพื่อดูความคืบหน้า

เพื่อเปิด n8n ได้ทุกเมื่อ ให้ไปที่ <http://localhost:5678/> ในเบราว์เซอร์ของคุณ

ด้วย n8n instance ของคุณ คุณจะสามารถเข้าถึงการผสานรวมกว่า 400 รายการและชุดของโหนด AI พื้นฐานและขั้นสูง เช่น
[AI Agent](https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.agent/),
[Text classifier](https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.text-classifier/),
และ [Information Extractor](https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.information-extractor/)
เพื่อให้ทุกอย่างอยู่ในเครื่อง เพียงจำไว้ว่าให้ใช้โหนด Ollama สำหรับโมเดลภาษาและ Qdrant เป็นฐานข้อมูลเวกเตอร์ของคุณ

> [!NOTE]
> ชุดเริ่มต้นนี้ออกแบบมาเพื่อช่วยให้คุณเริ่มต้นกับเวิร์กโฟลว์ AI แบบโฮสต์เอง แม้ว่าจะไม่ได้ปรับให้เหมาะสมสำหรับสภาพแวดล้อมการผลิตอย่างเต็มที่ แต่ก็รวมส่วนประกอบที่แข็งแกร่งที่ทำงานร่วมกันได้ดีสำหรับโครงการต้นแบบ คุณสามารถปรับแต่งให้ตรงกับความต้องการเฉพาะของคุณได้

## การอัปเกรด

* ### สำหรับการตั้งค่า Nvidia GPU:

```bash
docker compose -f docker-compose-full.yml --profile gpu-nvidia pull
docker compose create && docker compose -f docker-compose-full.yml --profile gpu-nvidia up
```

* ### สำหรับผู้ใช้ Mac / Apple Silicon

```
docker compose pull
docker compose create && docker compose up
```

* ### สำหรับการตั้งค่า Non-GPU:

```bash
docker compose -f docker-compose-full.yml --profile cpu pull
docker compose create && docker compose -f docker-compose-full.yml --profile cpu up
```

## 👓 การอ่านที่แนะนำ

n8n เต็มไปด้วยเนื้อหาที่มีประโยชน์สำหรับการเริ่มต้นอย่างรวดเร็วด้วยแนวคิดและโหนด AI หากคุณพบปัญหา ให้ไปที่ [support](#support)

- [AI agents สำหรับนักพัฒนา: จากทฤษฎีสู่การปฏิบัติด้วย n8n](https://blog.n8n.io/ai-agents/)
- [บทแนะนำ: สร้างเวิร์กโฟลว์ AI ใน n8n](https://docs.n8n.io/advanced-ai/intro-tutorial/)
- [แนวคิด Langchain ใน n8n](https://docs.n8n.io/advanced-ai/langchain/langchain-n8n/)
- [การสาธิตความแตกต่างที่สำคัญระหว่าง agents และ chains](https://docs.n8n.io/advanced-ai/examples/agent-chain-comparison/)
- [ฐานข้อมูลเวกเตอร์คืออะไร?](https://docs.n8n.io/advanced-ai/examples/understand-vector-databases/)

## 🎥 วิดีโอแนะนำ

- [การติดตั้งและการใช้งาน Local AI สำหรับ n8n](https://www.youtube.com/watch?v=xz_X2N-hPg0)

## 🛍️ เทมเพลต AI เพิ่มเติม

สำหรับแนวคิดเวิร์กโฟลว์ AI เพิ่มเติม โปรดไปที่ [**แกลเลอรีเทมเพลต AI อย่างเป็นทางการของ n8n**](https://n8n.io/workflows/?categories=AI) จากแต่ละเวิร์กโฟลว์ ให้เลือกปุ่ม **Use workflow** เพื่อเพิ่มเวิร์กโฟลว์ลงใน n8n instance ของคุณโดยอัตโนมัติ

### เรียนรู้แนวคิดสำคัญของ AI

- [AI Agent Chat](https://n8n.io/workflows/1954-ai-agent-chat/)
- [AI chat กับแหล่งข้อมูลใด ๆ (โดยใช้เวิร์กโฟลว์ n8n)](https://n8n.io/workflows/2026-ai-chat-with-any-data-source-using-the-n8n-workflow-tool/)
- [แชทกับ OpenAI Assistant (โดยเพิ่มหน่วยความจำ)](https://n8n.io/workflows/2098-chat-with-openai-assistant-by-adding-a-memory/)
- [ใช้ LLM แบบโอเพ่นซอร์ส (ผ่าน Hugging Face)](https://n8n.io/workflows/1980-use-an-open-source-llm-via-huggingface/)
- [แชทกับเอกสาร PDF โดยใช้ AI (อ้างอิงแหล่งที่มา)](https://n8n.io/workflows/2165-chat-with-pdf-docs-using-ai-quoting-sources/)
- [AI agent ที่สามารถดึงข้อมูลจากเว็บเพจ](https://n8n.io/workflows/2006-ai-agent-that-can-scrape-webpages/)

### เทมเพลต AI ในเครื่อง

- [ผู้ช่วยรหัสภาษี](https://n8n.io/workflows/2341-build-a-tax-code-assistant-with-qdrant-mistralai-and-openai/)
- [แยกเอกสารเป็นโน้ตการศึกษาโดยใช้ MistralAI และ Qdrant](https://n8n.io/workflows/2339-breakdown-documents-into-study-notes-using-templating-mistralai-and-qdrant/)
- [ผู้ช่วยเอกสารทางการเงินโดยใช้ Qdrant และ](https://n8n.io/workflows/2335-build-a-financial-documents-assistant-using-qdrant-and-mistralai/) [Mistral.ai](http://mistral.ai/)
- [คำแนะนำสูตรอาหารโดยใช้ Qdrant และ Mistral](https://n8n.io/workflows/2333-recipe-recommendations-with-qdrant-and-mistral/)

## เคล็ดลับและเทคนิค

### การเข้าถึงไฟล์ในเครื่อง

ชุดเริ่มต้น AI แบบโฮสต์เองจะสร้างโฟลเดอร์ที่ใช้ร่วมกัน (โดยค่าเริ่มต้น ตั้งอยู่ในไดเรกทอรีเดียวกัน) ซึ่งถูกเมานต์ไปยังคอนเทนเนอร์ n8n และอนุญาตให้ n8n เข้าถึงไฟล์ในดิสก์ได้ โฟลเดอร์นี้ในคอนเทนเนอร์ n8n ตั้งอยู่ที่ `/data/shared` -- นี่คือเส้นทางที่คุณต้องใช้ในโหนดที่โต้ตอบกับระบบไฟล์ในเครื่อง

**โหนดที่โต้ตอบกับระบบไฟล์ในเครื่อง**

- [อ่าน/เขียนไฟล์จากดิสก์](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.filesreadwrite/)
- [ตัวกระตุ้นไฟล์ในเครื่อง](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.localfiletrigger/)
- [รันคำสั่ง](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.executecommand/)

## 📜 ใบอนุญาต

โปรเจกต์นี้ได้รับอนุญาตภายใต้ Apache License 2.0 - ดูไฟล์ [LICENSE](LICENSE) สำหรับรายละเอียด

## 💬 การสนับสนุน

เข้าร่วมการสนทนาใน [n8n Forum](https://community.n8n.io/) ซึ่งคุณสามารถ:

- **แชร์ผลงานของคุณ**: แสดงสิ่งที่คุณสร้างด้วย n8n และสร้างแรงบันดาลใจให้ผู้อื่นในชุมชน
- **ถามคำถาม**: ไม่ว่าคุณจะเพิ่งเริ่มต้นหรือเป็นมืออาชีพที่มีประสบการณ์ ชุมชนและทีมงานของเราพร้อมสนับสนุนคุณในทุกความท้าทาย
- **เสนอไอเดีย**: มีไอเดียสำหรับฟีเจอร์หรือการปรับปรุงหรือไม่? แจ้งให้เราทราบ! เราพร้อมรับฟังสิ่งที่คุณอยากเห็นในอนาคต
