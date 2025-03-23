# Hands-On: ระบบ Knowledge Base อัจฉริยะด้วย RAG บน n8n

บทความนี้จะแนะนำขั้นตอนละเอียดในการสร้างระบบ Knowledge Base อัจฉริยะโดยใช้เทคโนโลยี RAG (Retrieval-Augmented Generation) บนแพลตฟอร์ม n8n อย่างเป็นขั้นเป็นตอน

## เป้าหมายของระบบ

เราจะสร้างระบบที่:
- รวบรวมและประมวลผลเอกสารภายในองค์กร
- สามารถตอบคำถามจากพนักงานได้อย่างแม่นยำ
- อ้างอิงแหล่งที่มาของข้อมูลได้ชัดเจน
- อัปเดตข้อมูลได้โดยอัตโนมัติเมื่อมีการเปลี่ยนแปลงเอกสาร

## สิ่งที่ต้องเตรียม

1. **n8n**: แพลตฟอร์มสำหรับสร้าง Workflow อัตโนมัติ
2. **Vector Database**: เช่น Qdrant, Milvus, หรือ Pinecone
3. **LLM API**: เช่น OpenAI API, Azure OpenAI, หรือ Hugging Face
4. **เอกสารภายในองค์กร**: ในรูปแบบดิจิทัล (PDF, DOCX, TXT, HTML)
5. **พื้นที่จัดเก็บ**: สำหรับเก็บไฟล์เอกสารและข้อมูลระหว่างกระบวนการ

## ขั้นตอนการทำงานโดยละเอียด

### 1. การติดตั้งและเตรียมระบบ n8n

1. **ติดตั้ง n8n**:
   ```bash
   # ติดตั้งผ่าน Docker
   docker run -it --rm \
     --name n8n \
     -p 5678:5678 \
     -v ~/.n8n:/home/node/.n8n \
     n8nio/n8n
   ```

2. **เข้าถึง n8n Dashboard**:
   - เปิดเบราว์เซอร์ไปที่ `http://localhost:5678`
   - สร้างบัญชีผู้ใช้และเข้าสู่ระบบ

3. **ติดตั้ง Community Nodes ที่จำเป็น**:
   - ไปที่ Settings > Community Nodes
   - ค้นหาและติดตั้ง nodes ต่อไปนี้:
     - n8n-nodes-openai
     - n8n-nodes-qdrant (หรือ vector database อื่นที่เลือกใช้)
     - n8n-nodes-document-processing (สำหรับการแปลงเอกสาร)

### 2. การสร้าง Workflow สำหรับการนำเข้าและประมวลผลเอกสาร

1. **สร้าง Workflow ใหม่**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "Document Ingestion Pipeline"

2. **เพิ่ม Trigger Node**:
   - เลือก "Webhook" หรือ "Watch Folder" (ขึ้นอยู่กับวิธีการนำเข้าเอกสาร)
   - กำหนดค่า:
     - ถ้าใช้ Webhook: สร้าง URL endpoint สำหรับรับไฟล์
     - ถ้าใช้ Watch Folder: ระบุโฟลเดอร์ที่จะติดตามการเปลี่ยนแปลง

3. **เพิ่ม Document Processing Node**:
   - ใช้ "Read PDF" หรือ "Parse Document" node
   - เชื่อมต่อกับ Trigger Node
   - กำหนดค่าให้อ่านเนื้อหาของเอกสาร

4. **เพิ่ม Text Splitter Node**:
   - สร้าง Function Node สำหรับแบ่งเนื้อหาเป็นชิ้นที่เหมาะสม
   - ตัวอย่าง code:
     ```javascript
     // แบ่งเอกสารเป็นส่วนๆ โดยแต่ละส่วนมีขนาดไม่เกิน 1000 ตัวอักษร
     const text = items[0].json.text;
     const chunks = [];
     const chunkSize = 1000;
     
     for (let i = 0; i < text.length; i += chunkSize) {
       chunks.push({
         json: {
           chunk: text.substring(i, i + chunkSize),
           metadata: {
             filename: items[0].json.filename,
             page: Math.floor(i / chunkSize) + 1,
             source: items[0].json.source || 'unknown'
           }
         }
       });
     }
     
     return chunks;
     ```

5. **เพิ่ม OpenAI Embedding Node**:
   - ใช้ "OpenAI" node สำหรับสร้าง embeddings
   - เชื่อมต่อกับ Text Splitter Node
   - กำหนดค่า:
     - Operation: Create Embedding
     - Model: text-embedding-ada-002 (หรือรุ่นล่าสุดที่แนะนำ)
     - Input: {{$json.chunk}}

6. **เพิ่ม Vector Database Node**:
   - ใช้ "Qdrant" node (หรือ vector database อื่น)
   - เชื่อมต่อกับ OpenAI Embedding Node
   - กำหนดค่า:
     - Operation: Upsert
     - Collection Name: knowledge_base
     - Vectors: {{$json.embedding}}
     - Payload: {{$json.metadata}}
     - ID Generation: เลือกวิธีการสร้าง ID (เช่น จาก hash ของเนื้อหา)

### 3. การสร้าง Workflow สำหรับการค้นหาและตอบคำถาม

1. **สร้าง Workflow ใหม่**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "Knowledge Base Query"

2. **เพิ่ม Trigger Node**:
   - เลือก "Webhook"
   - กำหนดค่า endpoint สำหรับรับคำถาม

3. **เพิ่ม OpenAI Embedding Node**:
   - เชื่อมต่อกับ Trigger Node
   - กำหนดค่า:
     - Operation: Create Embedding
     - Model: text-embedding-ada-002
     - Input: {{$json.query}}

4. **เพิ่ม Vector Database Query Node**:
   - เชื่อมต่อกับ OpenAI Embedding Node
   - กำหนดค่า:
     - Operation: Search
     - Collection Name: knowledge_base
     - Vector: {{$json.embedding}}
     - Limit: 5 (จำนวนผลลัพธ์ที่ต้องการ)

5. **เพิ่ม Function Node สำหรับจัดรูปแบบข้อมูล**:
   - เชื่อมต่อกับ Vector Database Query Node
   - ตัวอย่าง code:
     ```javascript
     // รวบรวมเนื้อหาที่เกี่ยวข้องพร้อมแหล่งที่มา
     const results = items[0].json.results;
     const relevantTexts = results.map(r => r.payload.chunk);
     const sources = results.map(r => r.payload.metadata);
     
     return [
       {
         json: {
           query: items[0].json.query,
           relevantTexts,
           sources
         }
       }
     ];
     ```

6. **เพิ่ม OpenAI Completion Node**:
   - เชื่อมต่อกับ Function Node
   - กำหนดค่า:
     - Operation: Create Chat Completion
     - Model: gpt-3.5-turbo หรือ gpt-4
     - Messages:
       ```
       [
         {
           "role": "system",
           "content": "คุณเป็นผู้ช่วยองค์กรอัจฉริยะ ที่ตอบคำถามตามข้อมูลที่ให้มาเท่านั้น หากไม่มีข้อมูลเพียงพอให้แจ้งว่าไม่พบข้อมูล ให้อ้างอิงแหล่งที่มาของข้อมูลทุกครั้ง"
         },
         {
           "role": "user",
           "content": "คำถาม: {{$json.query}}\n\nข้อมูลที่เกี่ยวข้อง: {{$json.relevantTexts.join('\n\n')}}"
         }
       ]
       ```

7. **เพิ่ม Respond to Webhook Node**:
   - เชื่อมต่อกับ OpenAI Completion Node
   - กำหนดค่า:
     - Response Body: 
       ```
       {
         "answer": {{$json.choices[0].message.content}},
         "sources": {{$node["Format Data"].json.sources}}
       }
       ```

### 4. การสร้างอินเทอร์เฟซสำหรับผู้ใช้

1. **ทางเลือกที่ 1: ใช้ n8n UI ด้วย Webhook**:
   - สร้าง UI อย่างง่ายด้วย HTML/JavaScript ที่เรียกใช้ Webhook URL
   - ตัวอย่าง HTML:
     ```html
     <!DOCTYPE html>
     <html>
     <head>
       <title>Knowledge Base Assistant</title>
       <script>
       async function askQuestion() {
         const question = document.getElementById('question').value;
         const response = await fetch('YOUR_WEBHOOK_URL', {
           method: 'POST',
           headers: {
             'Content-Type': 'application/json',
           },
           body: JSON.stringify({ query: question }),
         });
         const data = await response.json();
         document.getElementById('answer').innerHTML = data.answer;
         
         // แสดงแหล่งที่มา
         let sources = '<h4>แหล่งที่มา:</h4><ul>';
         data.sources.forEach(source => {
           sources += `<li>${source.filename} - หน้า ${source.page}</li>`;
         });
         sources += '</ul>';
         document.getElementById('sources').innerHTML = sources;
       }
       </script>
     </head>
     <body>
       <h1>Knowledge Base Assistant</h1>
       <input type="text" id="question" placeholder="ถามคำถามได้ที่นี่...">
       <button onclick="askQuestion()">ถาม</button>
       <div id="answer"></div>
       <div id="sources"></div>
     </body>
     </html>
     ```

2. **ทางเลือกที่ 2: ใช้ n8n HTTP Request Node กับแอปภายนอก**:
   - เชื่อมต่อกับระบบ chat ที่มีอยู่เช่น Slack, Microsoft Teams
   - สร้าง chatbot ที่รับคำถามและส่งคำตอบกลับ

### 5. การตั้งค่าการอัปเดตอัตโนมัติ

1. **สร้าง Workflow สำหรับตรวจสอบการเปลี่ยนแปลงเอกสาร**:
   - ใช้ "Cron" node สำหรับตรวจสอบเป็นระยะ
   - เชื่อมต่อกับ "HTTP Request" node เพื่อตรวจสอบแหล่งข้อมูลเอกสาร
   - หากพบการเปลี่ยนแปลง ให้เรียกใช้ Workflow "Document Ingestion Pipeline"

2. **ตั้งค่าการสำรองข้อมูล**:
   - สร้าง Workflow สำหรับสำรองข้อมูลจาก Vector Database
   - กำหนดความถี่ในการสำรองข้อมูล เช่น รายวัน หรือรายสัปดาห์

### 6. การทดสอบและปรับปรุงระบบ

1. **ทดสอบการนำเข้าเอกสาร**:
   - อัปโหลดเอกสารตัวอย่างเข้าสู่ระบบ
   - ตรวจสอบว่าเอกสารถูกประมวลผลและจัดเก็บใน Vector Database อย่างถูกต้อง

2. **ทดสอบการค้นหาและตอบคำถาม**:
   - ตั้งคำถามทดสอบที่มีคำตอบในเอกสาร
   - ประเมินความถูกต้องของคำตอบและความเกี่ยวข้องของแหล่งที่มา

3. **ปรับปรุงประสิทธิภาพ**:
   - ปรับขนาดของ chunk text หากคำตอบไม่สมบูรณ์
   - ทดลองใช้ LLM รุ่นต่างๆ เพื่อหาสมดุลระหว่างคุณภาพและต้นทุน
   - ปรับจำนวนผลลัพธ์จาก Vector Database ที่ส่งไปยัง LLM

## ตัวอย่างคำถามสำหรับทดสอบ

1. "นโยบายการลาพักร้อนประจำปีของบริษัทเป็นอย่างไร?"
2. "ขั้นตอนการขออนุมัติงบประมาณโครงการมีอะไรบ้าง?"
3. "ใครเป็นผู้อนุมัติการเบิกค่าใช้จ่ายที่มีมูลค่าเกิน 50,000 บาท?"
4. "วิธีการจองห้องประชุมออนไลน์ทำอย่างไร?"
5. "เงื่อนไขการปรับเงินเดือนประจำปีของบริษัทมีอะไรบ้าง?"

## การวัดผลความสำเร็จ

1. **เวลาในการค้นหาข้อมูล**: เปรียบเทียบเวลาที่ใช้ก่อนและหลังใช้ระบบ
2. **ความแม่นยำของคำตอบ**: สุ่มตรวจสอบความถูกต้องของคำตอบที่ระบบให้
3. **ความพึงพอใจของผู้ใช้**: สำรวจความคิดเห็นของพนักงานที่ใช้ระบบ
4. **จำนวนครั้งที่ใช้งาน**: ติดตามความถี่ในการใช้งานระบบ

## การขยายระบบในอนาคต

1. **เพิ่มแหล่งข้อมูล**: รวมข้อมูลจากระบบ CRM, ERP หรือฐานข้อมูลอื่นๆ
2. **พัฒนาอินเทอร์เฟซผู้ใช้**: สร้างแอปพลิเคชันที่ใช้งานง่ายและมีฟีเจอร์เพิ่มเติม
3. **เพิ่มการวิเคราะห์คำถาม**: วิเคราะห์แนวโน้มของคำถามเพื่อปรับปรุงเอกสารและการฝึกอบรม
4. **รองรับหลายภาษา**: ขยายระบบให้รองรับหลายภาษาสำหรับองค์กรระดับนานาชาติ

## สรุป

การสร้างระบบ Knowledge Base อัจฉริยะด้วย RAG บน n8n ช่วยให้องค์กรจัดการความรู้ได้อย่างมีประสิทธิภาพ ลดเวลาในการค้นหาข้อมูล และเพิ่มการเข้าถึงข้อมูลที่ถูกต้อง การเริ่มต้นด้วยขนาดเล็กและค่อยๆ ขยายระบบจะช่วยให้การนำไปใช้งานจริงเป็นไปอย่างราบรื่นและประสบความสำเร็จ
