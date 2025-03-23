# Hands-On: ระบบวิเคราะห์เอกสารสัญญาและกฎหมายด้วย RAG บน n8n

บทความนี้จะแนะนำขั้นตอนละเอียดในการสร้างระบบวิเคราะห์เอกสารสัญญาและกฎหมายโดยใช้เทคโนโลยี RAG (Retrieval-Augmented Generation) บนแพลตฟอร์ม n8n อย่างเป็นขั้นเป็นตอน

## เป้าหมายของระบบ

เราจะสร้างระบบที่:
- รวบรวมและประมวลผลเอกสารสัญญาและกฎหมายขององค์กร
- สามารถตอบคำถามเกี่ยวกับเงื่อนไข ข้อกำหนด และความเสี่ยงในสัญญา
- เปรียบเทียบเงื่อนไขระหว่างสัญญาต่างๆ ได้
- ช่วยระบุประเด็นสำคัญในเอกสารที่มีความยาวและซับซ้อน
- อ้างอิงแหล่งที่มาในเอกสารสัญญาได้อย่างแม่นยำ

## สิ่งที่ต้องเตรียม

1. **n8n**: แพลตฟอร์มสำหรับสร้าง Workflow อัตโนมัติ
2. **Vector Database**: เช่น Qdrant, Milvus, หรือ Pinecone
3. **LLM API**: เช่น OpenAI API (แนะนำใช้ GPT-4 สำหรับงานด้านกฎหมายที่ต้องการความแม่นยำสูง)
4. **เอกสารสัญญาและกฎหมาย**: ในรูปแบบดิจิทัล (PDF, DOCX)
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
     - n8n-nodes-document-processing (สำหรับการแปลงเอกสาร PDF)
     - n8n-nodes-langchain (เพิ่มความสามารถในการประมวลผลเนื้อหาทางกฎหมาย)

### 2. การสร้าง Workflow สำหรับการนำเข้าและประมวลผลเอกสารสัญญา

1. **สร้าง Workflow ใหม่**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "Legal Document Ingestion Pipeline"

2. **เพิ่ม Trigger Node**:
   - เลือก "Webhook" หรือ "Watch Folder" (ขึ้นอยู่กับวิธีการนำเข้าเอกสาร)
   - กำหนดค่า:
     - ถ้าใช้ Webhook: สร้าง URL endpoint สำหรับรับไฟล์สัญญา
     - ถ้าใช้ Watch Folder: ระบุโฟลเดอร์ที่จะติดตามการเปลี่ยนแปลง (เช่น แฟ้มเก็บสัญญา)

3. **เพิ่ม Document Processing Node**:
   - ใช้ "Read PDF" node
   - เชื่อมต่อกับ Trigger Node
   - กำหนดค่าให้อ่านเนื้อหาของเอกสารและเก็บข้อมูล metadata เพิ่มเติม:
     - ชื่อสัญญา
     - วันที่จัดทำสัญญา
     - คู่สัญญา
     - ประเภทสัญญา

4. **เพิ่ม Function Node สำหรับแยกข้อมูลสัญญา**:
   - สร้าง Function Node เพื่อค้นหาและแยกข้อมูลสำคัญในสัญญา
   - ตัวอย่าง code:
     ```javascript
     // แยกข้อมูลสำคัญจากเอกสารสัญญา
     const text = items[0].json.text;
     
     // ค้นหาข้อมูลคู่สัญญา
     const partyRegex = /between\s+(.*?)\s+and\s+(.*?)[\s,\.]/i;
     const partyMatch = text.match(partyRegex);
     const party1 = partyMatch ? partyMatch[1] : 'Unknown';
     const party2 = partyMatch ? partyMatch[2] : 'Unknown';
     
     // ค้นหาวันที่สัญญา
     const dateRegex = /dated\s+(\d{1,2}(?:st|nd|rd|th)?\s+\w+\s+\d{4})/i;
     const dateMatch = text.match(dateRegex);
     const contractDate = dateMatch ? dateMatch[1] : 'Unknown';
     
     // เพิ่ม metadata เข้าไปในต้นฉบับ
     const metadata = {
       filename: items[0].json.filename,
       contractType: items[0].json.filename.split('_')[0] || 'Unknown',
       party1,
       party2,
       contractDate,
       source: items[0].json.source || 'unknown'
     };
     
     return [
       {
         json: {
           ...items[0].json,
           metadata
         }
       }
     ];
     ```

5. **เพิ่ม Text Splitter Node**:
   - สร้าง Function Node สำหรับแบ่งเนื้อหาสัญญาเป็นส่วน
   - สำหรับเอกสารกฎหมาย ควรแบ่งตามโครงสร้างที่มีความหมาย เช่น แบ่งตามมาตรา/ข้อ
   - ตัวอย่าง code:
     ```javascript
     // แบ่งเอกสารตามมาตรา/ข้อในสัญญา
     const text = items[0].json.text;
     const metadata = items[0].json.metadata;
     
     // แบ่งตามมาตรา/ข้อ (ตัวอย่าง: ปรับตามรูปแบบของสัญญา)
     const sectionRegex = /(?:Section|ข้อ|มาตรา|CLAUSE)\s+\d+[\.\:]/gi;
     let sections = text.split(sectionRegex);
     
     // ถ้าไม่สามารถแบ่งตามมาตรา/ข้อได้ ให้แบ่งตามขนาด
     if (sections.length <= 1) {
       const chunkSize = 1200;
       sections = [];
       for (let i = 0; i < text.length; i += chunkSize) {
         sections.push(text.substring(i, i + chunkSize));
       }
     }
     
     // สร้าง chunks พร้อมกับ metadata
     const chunks = sections.map((section, index) => {
       return {
         json: {
           chunk: section.trim(),
           metadata: {
             ...metadata,
             section: index + 1,
             type: 'legal_document'
           }
         }
       };
     });
     
     return chunks;
     ```

6. **เพิ่ม OpenAI Embedding Node**:
   - ใช้ "OpenAI" node สำหรับสร้าง embeddings
   - เชื่อมต่อกับ Text Splitter Node
   - กำหนดค่า:
     - Operation: Create Embedding
     - Model: text-embedding-ada-002 (หรือรุ่นล่าสุดที่แนะนำ)
     - Input: {{$json.chunk}}

7. **เพิ่ม Vector Database Node**:
   - ใช้ "Qdrant" node (หรือ vector database อื่น)
   - เชื่อมต่อกับ OpenAI Embedding Node
   - กำหนดค่า:
     - Operation: Upsert
     - Collection Name: legal_documents
     - Vectors: {{$json.embedding}}
     - Payload: 
       ```json
       {
         "chunk": "{{$json.chunk}}",
         "metadata": {{$json.metadata}}
       }
       ```
     - ID Generation: ใช้การรวมกันของชื่อไฟล์และเลขส่วน (section)

### 3. การสร้าง Workflow สำหรับการค้นหาและวิเคราะห์สัญญา

1. **สร้าง Workflow ใหม่**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "Legal Document Query"

2. **เพิ่ม Trigger Node**:
   - เลือก "Webhook"
   - กำหนดค่า endpoint สำหรับรับคำถามเกี่ยวกับสัญญา

3. **เพิ่ม Function Node สำหรับแปลงคำถาม**:
   - สร้าง Function Node เพื่อแปลงคำถามให้เหมาะสมกับการค้นหาในเอกสารทางกฎหมาย
   - ตัวอย่าง code:
     ```javascript
     // ปรับแต่งคำถามให้เหมาะกับการค้นหาในเอกสารกฎหมาย
     const query = items[0].json.query;
     
     // ระบุบริษัทหรือสัญญาเฉพาะ
     let specificContract = null;
     let specificParty = null;
     let specificYear = null;
     
     // ค้นหาชื่อบริษัทในคำถาม
     const companyRegex = /บริษัท\s+([^\s,\.]+)|([A-Z][A-Za-z\s]+\b(?:Corp|Inc|Co|Ltd|Company))/g;
     const companyMatch = query.match(companyRegex);
     if (companyMatch) specificParty = companyMatch[0];
     
     // ค้นหาปีในคำถาม
     const yearRegex = /(25\d{2}|20\d{2})/g;
     const yearMatch = query.match(yearRegex);
     if (yearMatch) specificYear = yearMatch[0];
     
     return [
       {
         json: {
           originalQuery: query,
           enhancedQuery: query,
           filters: {
             specificParty,
             specificYear
           }
         }
       }
     ];
     ```

4. **เพิ่ม OpenAI Embedding Node**:
   - เชื่อมต่อกับ Function Node
   - กำหนดค่า:
     - Operation: Create Embedding
     - Model: text-embedding-ada-002
     - Input: {{$json.enhancedQuery}}

5. **เพิ่ม Vector Database Query Node**:
   - เชื่อมต่อกับ OpenAI Embedding Node
   - กำหนดค่า:
     - Operation: Search
     - Collection Name: legal_documents
     - Vector: {{$json.embedding}}
     - Limit: 7 (สำหรับเอกสารกฎหมายอาจจำเป็นต้องดึงข้อมูลมากขึ้น)
     - Filter: ใช้ filter ตาม metadata ถ้าคำถามระบุบริษัทหรือปีเฉพาะ

6. **เพิ่ม Function Node สำหรับจัดรูปแบบผลลัพธ์**:
   - เชื่อมต่อกับ Vector Database Query Node
   - ตัวอย่าง code:
     ```javascript
     // จัดรูปแบบผลลัพธ์สำหรับการวิเคราะห์ทางกฎหมาย
     const results = items[0].json.results;
     const originalQuery = items[0].json.originalQuery;
     
     // รวบรวมเนื้อหาที่เกี่ยวข้อง
     const relevantTexts = results.map(r => r.payload.chunk);
     
     // จัดกลุ่มตามสัญญา
     const contractGroups = {};
     results.forEach(r => {
       const contractName = r.payload.metadata.filename;
       if (!contractGroups[contractName]) {
         contractGroups[contractName] = [];
       }
       contractGroups[contractName].push({
         section: r.payload.metadata.section,
         text: r.payload.chunk,
         parties: [r.payload.metadata.party1, r.payload.metadata.party2].filter(Boolean),
         contractDate: r.payload.metadata.contractDate
       });
     });
     
     return [
       {
         json: {
           query: originalQuery,
           relevantTexts,
           contractGroups,
           sources: results.map(r => r.payload.metadata)
         }
       }
     ];
     ```

7. **เพิ่ม OpenAI Completion Node**:
   - เชื่อมต่อกับ Function Node
   - กำหนดค่า:
     - Operation: Create Chat Completion
     - Model: gpt-4 (แนะนำให้ใช้รุ่นที่มีประสิทธิภาพสูงสุดสำหรับงานกฎหมาย)
     - Messages:
       ```
       [
         {
           "role": "system",
           "content": "คุณเป็นผู้เชี่ยวชาญด้านกฎหมายและวิเคราะห์สัญญา ให้วิเคราะห์เนื้อหาในสัญญาตามข้อมูลที่ให้มาอย่างถูกต้อง ตรงประเด็น ถ้ามีการเปรียบเทียบระหว่างสัญญา ให้ระบุความแตกต่างอย่างชัดเจน ทุกครั้งต้องอ้างอิงที่มาจากสัญญาหรือเอกสารทางกฎหมายเสมอ"
         },
         {
           "role": "user",
           "content": "คำถาม: {{$json.query}}\n\nข้อมูลที่เกี่ยวข้อง: {{$json.relevantTexts.join('\n\n')}}\n\nข้อมูลจำแนกตามสัญญา: {{JSON.stringify($json.contractGroups)}}"
         }
       ]
       ```

8. **เพิ่ม Respond to Webhook Node**:
   - เชื่อมต่อกับ OpenAI Completion Node
   - กำหนดค่า:
     - Response Body: 
       ```
       {
         "answer": {{$json.choices[0].message.content}},
         "sources": {{$node["Format Results"].json.sources}},
         "contracts": Object.keys({{$node["Format Results"].json.contractGroups}})
       }
       ```

### 4. การสร้าง Workflow สำหรับเปรียบเทียบสัญญา

1. **สร้าง Workflow ใหม่**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "Contract Comparison"

2. **เพิ่ม Trigger Node**:
   - เลือก "Webhook"
   - กำหนดค่า endpoint สำหรับรับคำขอเปรียบเทียบสัญญา

3. **เพิ่ม Function Node สำหรับจัดเตรียมข้อมูลเปรียบเทียบ**:
   - กำหนดค่าให้รับพารามิเตอร์:
     - contract1: ชื่อสัญญาฉบับแรก
     - contract2: ชื่อสัญญาฉบับที่สอง
     - aspect: ประเด็นที่ต้องการเปรียบเทียบ (เช่น "การยกเลิก", "การรับประกัน")

4. **เพิ่ม HTTP Request Node สำหรับดึงข้อมูลสัญญา**:
   - ทำ HTTP Request ไปยังฐานข้อมูลหรือ API ที่เก็บสัญญา
   - ดึงเนื้อหาของทั้งสองสัญญาที่ต้องการเปรียบเทียบ

5. **เพิ่ม OpenAI Completion Node สำหรับเปรียบเทียบ**:
   - กำหนดค่า:
     - Operation: Create Chat Completion
     - Model: gpt-4
     - Messages:
       ```
       [
         {
           "role": "system",
           "content": "คุณเป็นผู้เชี่ยวชาญด้านกฎหมายที่สามารถเปรียบเทียบสัญญาได้อย่างละเอียด แม่นยำ ให้ระบุความเหมือน ความแตกต่าง และประเด็นสำคัญระหว่างสัญญาสองฉบับตามประเด็นที่กำหนด"
         },
         {
           "role": "user",
           "content": "กรุณาเปรียบเทียบประเด็น {{$json.aspect}} ระหว่างสัญญาต่อไปนี้:\n\nสัญญาที่ 1 ({{$json.contract1Name}}):\n{{$json.contract1Content}}\n\nสัญญาที่ 2 ({{$json.contract2Name}}):\n{{$json.contract2Content}}\n\nให้ระบุความเหมือน ความแตกต่าง และบทวิเคราะห์โดยละเอียด"
         }
       ]
       ```

6. **เพิ่ม Respond to Webhook Node**:
   - เชื่อมต่อกับ OpenAI Completion Node
   - แสดงผลการเปรียบเทียบในรูปแบบตาราง

### 5. การสร้างอินเทอร์เฟซสำหรับผู้ใช้ในฝ่ายกฎหมาย

1. **ทางเลือกที่ 1: สร้างหน้า Web UI เฉพาะสำหรับฝ่ายกฎหมาย**:
   - สร้าง HTML/CSS/JavaScript สำหรับอินเทอร์เฟซที่ออกแบบเฉพาะสำหรับงานกฎหมาย
   - ตัวอย่าง HTML:
     ```html
     <!DOCTYPE html>
     <html>
     <head>
       <title>Legal Document Analysis</title>
       <style>
         body { font-family: Arial, sans-serif; margin: 0; padding: 20px; }
         .container { max-width: 1200px; margin: 0 auto; }
         .query-section { margin-bottom: 20px; }
         .results { border: 1px solid #ccc; padding: 20px; }
         .sources { margin-top: 20px; font-size: 0.9em; color: #666; }
         .comparison-table { width: 100%; border-collapse: collapse; }
         .comparison-table th, .comparison-table td { 
           border: 1px solid #ddd; padding: 8px; text-align: left; 
         }
         .highlight { background-color: #ffff99; }
       </style>
       <script>
       async function analyzeLegalDocument() {
         const query = document.getElementById('legal-query').value;
         const response = await fetch('YOUR_WEBHOOK_URL/legal-query', {
           method: 'POST',
           headers: {
             'Content-Type': 'application/json',
           },
           body: JSON.stringify({ query }),
         });
         const data = await response.json();
         document.getElementById('analysis-result').innerHTML = data.answer;
         
         // แสดงแหล่งที่มา
         let sources = '<h4>แหล่งอ้างอิง:</h4><ul>';
         data.sources.forEach(source => {
           sources += `<li>${source.filename} - ข้อ/มาตรา ${source.section} (วันที่: ${source.contractDate})</li>`;
         });
         sources += '</ul>';
         document.getElementById('sources').innerHTML = sources;
         
         // แสดงรายการสัญญาที่เกี่ยวข้อง
         document.getElementById('related-contracts').innerHTML = 
           `<h4>สัญญาที่เกี่ยวข้อง:</h4>
           <ul>${data.contracts.map(c => `<li>${c}</li>`).join('')}</ul>`;
       }
       
       async function compareContracts() {
         const contract1 = document.getElementById('contract1').value;
         const contract2 = document.getElementById('contract2').value;
         const aspect = document.getElementById('comparison-aspect').value;
         
         const response = await fetch('YOUR_WEBHOOK_URL/compare-contracts', {
           method: 'POST',
           headers: {
             'Content-Type': 'application/json',
           },
           body: JSON.stringify({ contract1, contract2, aspect }),
         });
         const data = await response.json();
         document.getElementById('comparison-results').innerHTML = data.comparison;
       }
       </script>
     </head>
     <body>
       <div class="container">
         <h1>ระบบวิเคราะห์เอกสารสัญญาและกฎหมาย</h1>
         
         <div class="query-section">
           <h2>วิเคราะห์สัญญา</h2>
           <input type="text" id="legal-query" size="80" placeholder="ถามคำถามเกี่ยวกับสัญญาหรือเงื่อนไขทางกฎหมาย...">
           <button onclick="analyzeLegalDocument()">วิเคราะห์</button>
           <div class="results" id="analysis-result"></div>
           <div class="sources" id="sources"></div>
           <div id="related-contracts"></div>
         </div>
         
         <div class="query-section">
           <h2>เปรียบเทียบสัญญา</h2>
           <select id="contract1">
             <option value="">เลือกสัญญาฉบับแรก</option>
             <!-- รายการสัญญาจะถูกเพิ่มโดย JavaScript -->
           </select>
           <select id="contract2">
             <option value="">เลือกสัญญาฉบับที่สอง</option>
             <!-- รายการสัญญาจะถูกเพิ่มโดย JavaScript -->
           </select>
           <input type="text" id="comparison-aspect" placeholder="ระบุประเด็นที่ต้องการเปรียบเทียบ (เช่น การยกเลิก)">
           <button onclick="compareContracts()">เปรียบเทียบ</button>
           <div class="results" id="comparison-results"></div>
         </div>
       </div>
     </body>
     </html>
     ```

2. **ทางเลือกที่ 2: สร้าง Integration กับระบบจัดการเอกสารที่มีอยู่**:
   - ใช้ n8n เชื่อมต่อกับระบบจัดการเอกสารที่ทีมกฎหมายใช้งานอยู่
   - เพิ่ม HTTP Request Node เพื่อส่งและรับข้อมูลจากระบบภายนอก

### 6. การเพิ่มฟีเจอร์พิเศษสำหรับงานกฎหมาย

1. **การตรวจจับความเสี่ยงในสัญญา**:
   - สร้าง Workflow สำหรับวิเคราะห์ความเสี่ยงโดยเฉพาะ
   - ตั้งค่า system prompt สำหรับ LLM ให้ค้นหาเงื่อนไขที่อาจเป็นปัญหาหรือมีความเสี่ยง

2. **การแนะนำแม่แบบสัญญา**:
   - สร้างฐานข้อมูลแม่แบบสัญญามาตรฐาน
   - เพิ่ม Workflow ที่สามารถแนะนำแม่แบบสัญญาที่เหมาะสมตามความต้องการ

3. **การติดตามวันหมดอายุและกำหนดเวลาสำคัญในสัญญา**:
   - สร้าง Workflow สำหรับดึงข้อมูลวันที่สำคัญจากสัญญา
   - เชื่อมต่อกับระบบปฏิทินเพื่อสร้างการแจ้งเตือนอัตโนมัติ

### 7. การทดสอบและประเมินผลระบบ

1. **ทดสอบด้วยสัญญาตัวอย่าง**:
   - นำสัญญาตัวอย่างที่มีโครงสร้างและเนื้อหาแตกต่างกันมาทดสอบระบบ
   - ตั้งคำถามหลากหลายรูปแบบเพื่อทดสอบประสิทธิภาพการค้นหาและตอบคำถาม

2. **ทดสอบการเปรียบเทียบสัญญา**:
   - เลือกสัญญาสองฉบับที่มีทั้งส่วนที่เหมือนและแตกต่างกัน
   - ทดสอบความสามารถในการระบุความแตกต่างที่สำคัญ

3. **ให้ผู้เชี่ยวชาญด้านกฎหมายประเมิน**:
   - ขอให้ทนายความหรือผู้เชี่ยวชาญด้านกฎหมายทดลองใช้ระบบ
   - รวบรวมข้อเสนอแนะเพื่อปรับปรุงความแม่นยำและการใช้งาน

## ตัวอย่างคำถามสำหรับทดสอบ

1. "สัญญาซื้อขายกับบริษัท ABC มีเงื่อนไขการยกเลิกอย่างไรบ้าง?"
2. "เปรียบเทียบข้อกำหนดเรื่องการรับประกันสินค้าระหว่างสัญญาปี 2022 และ 2023"
3. "มีสัญญาใดบ้างที่มีระยะเวลาผูกพันมากกว่า 3 ปี?"
4. "หากเกิดกรณีพิพาท สัญญากับบริษัท XYZ จะใช้กฎหมายของประเทศใดในการตัดสิน?"
5. "สัญญาจ้างงานฉบับล่าสุดมีข้อกำหนดเรื่องการรักษาความลับอย่างไรบ้าง?"
6. "ในสัญญาเช่าพื้นที่ทั้งหมด มีข้อกำหนดเรื่องการปรับขึ้นค่าเช่าอย่างไร?"

## การวัดผลความสำเร็จ

1. **เวลาที่ประหยัดได้**: เปรียบเทียบเวลาที่ใช้ในการวิเคราะห์สัญญาแบบเดิมกับการใช้ระบบใหม่
2. **ความแม่นยำในการตรวจจับประเด็นสำคัญ**: ตรวจสอบว่าระบบสามารถระบุประเด็นสำคัญในสัญญาได้ครบถ้วนหรือไม่
3. **คุณภาพของการเปรียบเทียบ**: ประเมินความถูกต้องและความครอบคลุมของผลการเปรียบเทียบสัญญา
4. **ความพึงพอใจของทีมกฎหมาย**: สำรวจความคิดเห็นของผู้ใช้งานในฝ่ายกฎหมายและจัดซื้อ

## การขยายระบบในอนาคต

1. **ฟีเจอร์การร่างสัญญาอัตโนมัติ**: เพิ่มความสามารถในการสร้างร่างสัญญาโดยอัตโนมัติตามพารามิเตอร์ที่กำหนด
2. **การเชื่อมต่อกับฐานข้อมูลกฎหมายภายนอก**: รวมข้อมูลจากฐานข้อมูลกฎหมายหรือคำพิพากษาที่เกี่ยวข้อง
3. **การปรับให้รองรับเอกสารภาษาต่างประเทศ**: ขยายระบบให้สามารถวิเคราะห์สัญญาที่เป็นภาษาอังกฤษหรือภาษาอื่นๆ
4. **การเพิ่มเครื่องมือติดตามกฎหมายใหม่**: สร้างระบบที่ติดตามการเปลี่ยนแปลงของกฎหมายที่อาจมีผลต่อสัญญาปัจจุบัน

## สรุป

การสร้างระบบวิเคราะห์เอกสารสัญญาและกฎหมายด้วย RAG บน n8n ช่วยให้ฝ่ายกฎหมายและฝ่ายจัดซื้อสามารถจัดการกับเอกสารสัญญาที่มีความซับซ้อนได้อย่างมีประสิทธิภาพมากขึ้น ลดเวลาในการวิเคราะห์ ลดความเสี่ยงในการพลาดประเด็นสำคัญ และเพิ่มความสามารถในการเปรียบเทียบเงื่อนไขระหว่างสัญญาต่างๆ ได้อย่างรวดเร็ว การปรับแต่งระบบให้เหมาะกับลักษณะเฉพาะของเอกสารทางกฎหมายและความต้องการของทีมกฎหมายจะช่วยเพิ่มประสิทธิภาพการทำงานได้อย่างเห็นได้ชัด
