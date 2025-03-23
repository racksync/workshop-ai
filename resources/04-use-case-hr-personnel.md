# Hands-On: ระบบสนับสนุนงาน HR และการบริหารบุคลากรด้วย RAG บน n8n

บทความนี้จะแนะนำขั้นตอนละเอียดในการสร้างระบบสนับสนุนงาน HR และการบริหารบุคลากรโดยใช้เทคโนโลยี RAG (Retrieval-Augmented Generation) บนแพลตฟอร์ม n8n อย่างเป็นขั้นเป็นตอน

## เป้าหมายของระบบ

เราจะสร้างระบบที่:
- รวบรวมและประมวลผลข้อมูลนโยบาย HR คู่มือพนักงาน และเอกสารสวัสดิการต่างๆ
- สร้างระบบตอบคำถามอัตโนมัติเกี่ยวกับนโยบาย สวัสดิการ และการพัฒนาบุคลากร
- ช่วยให้ฝ่าย HR ลดเวลาในการตอบคำถามที่พบบ่อยและมุ่งเน้นงานเชิงกลยุทธ์มากขึ้น
- เชื่อมโยงกับระบบ HRIS (Human Resource Information System) ที่มีอยู่
- มอบประสบการณ์ที่สะดวกรวดเร็วสำหรับพนักงานในการเข้าถึงข้อมูล HR

## สิ่งที่ต้องเตรียม

1. **n8n**: แพลตฟอร์มสำหรับสร้าง Workflow อัตโนมัติ
2. **Vector Database**: เช่น Qdrant, Milvus, หรือ Pinecone
3. **LLM API**: เช่น OpenAI API หรือ Azure OpenAI
4. **เอกสารด้าน HR**:
   - คู่มือพนักงาน และนโยบายบริษัท
   - เอกสารสวัสดิการและสิทธิประโยชน์
   - แนวทางการพัฒนาบุคลากรและเส้นทางอาชีพ
   - ระเบียบการลา และการขอเบิกค่าใช้จ่ายต่างๆ
5. **API ของระบบ HRIS** (หากต้องการเชื่อมต่อกับข้อมูลพนักงาน)

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
     - n8n-nodes-document-processing
     - n8n-nodes-slack (หากต้องการเชื่อมต่อกับ Slack)
     - n8n-nodes-microsoft-teams (หากต้องการเชื่อมต่อกับ Microsoft Teams)

### 2. การสร้าง Workflow สำหรับการนำเข้าและประมวลผลเอกสาร HR

1. **สร้าง Workflow ใหม่**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "HR Document Ingestion Pipeline"

2. **เพิ่ม Trigger Node**:
   - เลือก "Watch Folder" node
   - กำหนดค่า:
     - Folder Path: โฟลเดอร์ที่เก็บเอกสาร HR
     - File Types: pdf, docx, xlsx, txt
     - Include Subdirectories: true

3. **เพิ่ม Switch Node สำหรับแยกประเภทไฟล์**:
   - กำหนดเงื่อนไขตามนามสกุลไฟล์:
     - Case 1: *.pdf → เชื่อมต่อกับ "Read PDF" node
     - Case 2: *.docx → เชื่อมต่อกับ "Read Document" node
     - Case 3: *.xlsx → เชื่อมต่อกับ "Read Excel" node
     - Case 4: *.txt → เชื่อมต่อกับ "Read Binary File" node

4. **เพิ่ม Function Node สำหรับการประมวลผลเอกสาร**:
   - เชื่อมต่อกับ Document Processing Nodes
   - ตัวอย่าง code:
     ```javascript
     // จัดการและจัดหมวดหมู่เอกสาร HR
     const fileName = items[0].json.filename || '';
     const content = items[0].json.content || items[0].json.text || '';
     
     // ระบุประเภทเอกสาร
     let documentType = 'general';
     
     if (/handbook|คู่มือ|พนักงาน|employee|guide/i.test(fileName)) {
       documentType = 'handbook';
     } else if (/benefit|สวัสดิการ|welfare|medical|ประกัน/i.test(fileName)) {
       documentType = 'benefits';
     } else if (/leave|ลา|absent|หยุด/i.test(fileName)) {
       documentType = 'leave_policy';
     } else if (/training|development|อบรม|พัฒนา|career|เส้นทางอาชีพ/i.test(fileName)) {
       documentType = 'training_development';
     } else if (/reimburse|expense|claim|เบิก|ค่าใช้จ่าย|expense/i.test(fileName)) {
       documentType = 'expense_claims';
     } else if (/compensation|salary|เงิน|เดือน|โบนัส|bonus/i.test(fileName)) {
       documentType = 'compensation';
     }
     
     // เพิ่ม metadata
     return [
       {
         json: {
           content: content,
           metadata: {
             filename: fileName,
             type: documentType,
             date_processed: new Date().toISOString(),
             source: 'hr_department',
             last_updated: items[0].json.mtime || new Date().toISOString()
           }
         }
       }
     ];
     ```

5. **เพิ่ม Text Splitter Node**:
   - สร้าง Function Node สำหรับแบ่งเนื้อหาเป็นชิ้นที่เหมาะสม
   - ตัวอย่าง code:
     ```javascript
     // แบ่งเอกสารเป็นส่วนๆ สำหรับการวิเคราะห์
     const text = items[0].json.content;
     const metadata = items[0].json.metadata;
     const chunks = [];
     const chunkSize = 1200;
     
     // แบ่งตามหัวข้อหากเป็นไปได้
     const headingRegex = /(?:\n|^)(?:#+\s+|หัวข้อ\s+|บท(?:ที่)?|ข้อ\s+\d+[\.:]|ส่วนที่\s+\d+|ข้อกำหนด|นโยบาย|section\s+\d+)/gi;
     const sections = text.split(headingRegex);
     
     if (sections.length <= 2 || metadata.type === 'compensation') {
       // แบ่งตามขนาดถ้าไม่มีหัวข้อชัดเจนหรือเป็นเอกสารที่ต้องการความต่อเนื่อง
       for (let i = 0; i < text.length; i += chunkSize) {
         chunks.push({
           json: {
             chunk: text.substring(i, i + chunkSize),
             metadata: {
               ...metadata,
               chunk_id: `${metadata.filename}_${Math.floor(i / chunkSize)}`,
             }
           }
         });
       }
     } else {
       // แบ่งตามหัวข้อ
       let currentHeading = "";
       for (let i = 0; i < sections.length; i++) {
         if (i % 2 === 0) {
           currentHeading = sections[i] || "ไม่ระบุหัวข้อ";
         } else {
           const content = sections[i];
           
           // ถ้าเนื้อหายาวเกินไป แบ่งเพิ่ม
           if (content.length > chunkSize * 1.5) {
             for (let j = 0; j < content.length; j += chunkSize) {
               chunks.push({
                 json: {
                   chunk: content.substring(j, j + chunkSize),
                   metadata: {
                     ...metadata,
                     heading: currentHeading,
                     chunk_id: `${metadata.filename}_${currentHeading}_${j/chunkSize}`
                   }
                 }
               });
             }
           } else {
             chunks.push({
               json: {
                 chunk: content,
                 metadata: {
                   ...metadata,
                   heading: currentHeading,
                   chunk_id: `${metadata.filename}_${currentHeading}`
                 }
               }
             });
           }
         }
       }
     }
     
     return chunks;
     ```

6. **เพิ่ม OpenAI Embedding Node**:
   - เชื่อมต่อกับ Text Splitter Node
   - กำหนดค่า:
     - Operation: Create Embedding
     - Model: text-embedding-ada-002
     - Input: {{$json.chunk}}

7. **เพิ่ม Vector Database Node**:
   - เชื่อมต่อกับ OpenAI Embedding Node
   - กำหนดค่า:
     - Operation: Upsert
     - Collection Name: hr_knowledge_base
     - Vectors: {{$json.embedding}}
     - Payload: 
       ```json
       {
         "chunk": "{{$json.chunk}}",
         "metadata": {{$json.metadata}}
       }
       ```

### 3. การเชื่อมต่อกับระบบ HRIS (ถ้ามี)

1. **สร้าง Workflow ใหม่**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "HRIS Data Sync"

2. **เพิ่ม Trigger Node**:
   - เลือก "Cron" node
   - กำหนดค่า: ให้ทำงานทุกวันหรือทุกสัปดาห์ (ขึ้นอยู่กับความถี่ในการอัปเดตข้อมูล)

3. **เพิ่ม HTTP Request Node**:
   - เชื่อมต่อกับ HRIS API
   - Method: GET
   - URL: API endpoint ของระบบ HRIS
   - Authentication: ตามที่ระบบ HRIS กำหนด
   - ตัวอย่าง:
     ```
     {
       "url": "https://your-hris-system.com/api/employees",
       "method": "GET",
       "authentication": "Basic",
       "username": "{{$env.HRIS_API_USERNAME}}",
       "password": "{{$env.HRIS_API_PASSWORD}}"
     }
     ```

4. **เพิ่ม Function Node สำหรับแปลงข้อมูล**:
   - แปลงข้อมูลพนักงานให้อยู่ในรูปแบบที่เหมาะสม
   - ตัวอย่าง code:
     ```javascript
     // แปลงข้อมูลพนักงานให้เป็นรูปแบบที่ใช้งานได้
     const employeeData = items[0].json.data || [];
     
     // สร้างข้อมูลพนักงานในรูปแบบข้อความ
     const employeeDocuments = employeeData.map(emp => {
       // สร้างเป็นรูปแบบข้อความที่มีโครงสร้าง
       const textRepresentation = `
       รหัสพนักงาน: ${emp.id || 'ไม่ระบุ'}
       ชื่อ-นามสกุล: ${emp.first_name || ''} ${emp.last_name || ''}
       ตำแหน่ง: ${emp.position || 'ไม่ระบุ'}
       แผนก: ${emp.department || 'ไม่ระบุ'}
       อีเมล: ${emp.email || 'ไม่ระบุ'}
       เบอร์โทรศัพท์: ${emp.phone || 'ไม่ระบุ'}
       วันเริ่มงาน: ${emp.hire_date || 'ไม่ระบุ'}
       ผู้บังคับบัญชา: ${emp.manager_name || 'ไม่ระบุ'}
       สถานะ: ${emp.status || 'ไม่ระบุ'}
       `;
       
       return {
         json: {
           chunk: textRepresentation,
           metadata: {
             type: 'employee_data',
             employee_id: emp.id,
             department: emp.department,
             position: emp.position,
             date_updated: new Date().toISOString(),
             source: 'hris_system'
           }
         }
       };
     });
     
     return employeeDocuments;
     ```

5. **เพิ่ม OpenAI Embedding Node**:
   - เชื่อมต่อกับ Function Node
   - Operation: Create Embedding
   - Model: text-embedding-ada-002
   - Input: {{$json.chunk}}

6. **เพิ่ม Vector Database Node**:
   - Operation: Upsert
   - Collection Name: hr_employee_data
   - Vectors: {{$json.embedding}}
   - Payload: เก็บข้อมูลพนักงานและ metadata
   - ID Generation: ใช้ employee_id เพื่อให้สามารถอัปเดตข้อมูลได้

### 4. การสร้าง Workflow สำหรับการตอบคำถาม HR

1. **สร้าง Workflow ใหม่**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "HR Query System"

2. **เพิ่ม Trigger Node**:
   - เลือก "Webhook"
   - กำหนดค่า endpoint สำหรับรับคำถาม

3. **เพิ่ม Function Node สำหรับวิเคราะห์คำถาม**:
   - วิเคราะห์คำถามเพื่อระบุหมวดหมู่และรายละเอียดที่เกี่ยวข้อง
   - ตัวอย่าง code:
     ```javascript
     // วิเคราะห์คำถามเกี่ยวกับ HR
     const query = items[0].json.query;
     
     // ตรวจสอบประเภทคำถาม
     const isBenefits = /สวัสดิการ|benefit|ประกัน|insurance|medical|ค่ารักษา|สุขภาพ|เงินช่วยเหลือ|ค่าเล่าเรียนบุตร/i.test(query);
     const isLeave = /ลา|leave|หยุด|sick|ป่วย|กิจ|พักร้อน|วันหยุด|maternity|คลอด/i.test(query);
     const isTraining = /อบรม|training|พัฒนา|development|course|skill|ทักษะ|คอร์ส|เรียน/i.test(query);
     const isExpense = /เบิก|reimburse|expense|claim|ค่าใช้จ่าย|ใบเสร็จ/i.test(query);
     const isSalary = /เงินเดือน|salary|จ่าย|payment|โบนัส|bonus|ปรับเงิน|เพิ่มเงิน|เงินเพิ่ม|เลื่อนขั้น/i.test(query);
     const isEmployeeInfo = /พนักงาน|employee|ข้อมูล.*บุคคล|ที่อยู่|เบอร์โทร|อีเมล/i.test(query);
     
     // ตรวจสอบคำถามที่เกี่ยวกับตำแหน่งงาน
     const positionRegex = /(ตำแหน่ง|position|หน้าที่|role|job)\s+([A-Za-zก-๙\s]+)/i;
     const positionMatch = query.match(positionRegex);
     const position = positionMatch ? positionMatch[2].trim() : null;
     
     // แยกคำถามที่เกี่ยวกับพนักงานเฉพาะคน
     const employeeIdRegex = /พนักงาน(รหัส|เลขที่|ไอดี|ID)?\s*[:]\s*(\w+)/i;
     const employeeIdMatch = query.match(employeeIdRegex);
     const employeeId = employeeIdMatch ? employeeIdMatch[2] : null;
     
     // กำหนดคอลเลกชันที่จะค้นหา
     const collections = [];
     
     if (isEmployeeInfo && employeeId) {
       collections.push("hr_employee_data");
     } else {
       collections.push("hr_knowledge_base");
     }
     
     // กำหนดฟิลเตอร์สำหรับการค้นหา
     let filter = {};
     
     if (isBenefits) {
       filter = { "metadata.type": "benefits" };
     } else if (isLeave) {
       filter = { "metadata.type": "leave_policy" };
     } else if (isTraining) {
       filter = { "metadata.type": "training_development" };
     } else if (isExpense) {
       filter = { "metadata.type": "expense_claims" };
     } else if (isSalary) {
       filter = { "metadata.type": "compensation" };
     }
     
     return [
       {
         json: {
           originalQuery: query,
           queryType: {
             isBenefits,
             isLeave,
             isTraining,
             isExpense,
             isSalary,
             isEmployeeInfo
           },
           position,
           employeeId,
           collections,
           filter,
           enhancedQuery: query
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

5. **เพิ่ม Function Node สำหรับค้นหาข้อมูล**:
   - สร้าง Function Node ที่จะดึงข้อมูลจาก Vector Database
   - ใช้ Collections และ filters ที่กำหนดไว้
   - ตัวอย่าง code:
     ```javascript
     // ค้นหาข้อมูลจาก Vector Database
     const collections = items[0].json.collections || ["hr_knowledge_base"];
     const embedding = items[0].json.embedding;
     const filter = items[0].json.filter || {};
     
     // สร้าง HTTP Request ไปยัง Vector Database
     const response = await $http.post({
       url: `http://your-vector-db/collections/${collections[0]}/points/search`,
       headers: {
         'Content-Type': 'application/json',
         'API-Key': '{{$env.VECTOR_DB_API_KEY}}'
       },
       body: {
         vector: embedding,
         limit: 7,
         filter: Object.keys(filter).length > 0 ? filter : undefined
       }
     });
     
     // ดึงผลลัพธ์
     const searchResults = response.data.result || [];
     
     // รวบรวมเนื้อหาที่เกี่ยวข้อง
     const relevantChunks = searchResults.map(result => result.payload.chunk);
     const metadata = searchResults.map(result => result.payload.metadata);
     
     return [
       {
         json: {
           ...items[0].json,
           relevantChunks,
           metadata,
           searchResults
         }
       }
     ];
     ```

6. **เพิ่ม Function Node สำหรับดึงข้อมูลพนักงานเพิ่มเติม (ถ้าจำเป็น)**:
   - ใช้สำหรับคำถามที่เกี่ยวข้องกับพนักงานเฉพาะคน
   - ตัวอย่าง code:
     ```javascript
     // ตรวจสอบว่าต้องดึงข้อมูลพนักงานเพิ่มหรือไม่
     const isEmployeeInfo = items[0].json.queryType.isEmployeeInfo;
     const employeeId = items[0].json.employeeId;
     
     if (isEmployeeInfo && employeeId) {
       try {
         // ดึงข้อมูลพนักงานจาก HRIS API
         const hrisResponse = await $http.get({
           url: `https://your-hris-api.com/employees/${employeeId}`,
           headers: {
             'Authorization': 'Bearer {{$env.HRIS_API_TOKEN}}'
           }
         });
         
         return [
           {
             json: {
               ...items[0].json,
               employeeData: hrisResponse.data
             }
           }
         ];
       } catch (error) {
         // หากไม่สามารถดึงข้อมูลได้ ให้ใช้ข้อมูลที่มีอยู่แล้ว
         return items;
       }
     } else {
       return items;
     }
     ```

7. **เพิ่ม OpenAI Completion Node**:
   - เชื่อมต่อกับ Function Node
   - กำหนดค่า:
     - Operation: Create Chat Completion
     - Model: gpt-3.5-turbo หรือ gpt-4
     - Messages:
       ```
       [
         {
           "role": "system",
           "content": "คุณเป็นระบบผู้ช่วยฝ่ายทรัพยากรบุคคล (HR Assistant) ที่ให้ข้อมูลเกี่ยวกับนโยบาย สวัสดิการ และการพัฒนาบุคลากร ให้ตอบคำถามตามข้อมูลที่มีอย่างถูกต้อง เป็นมิตร และมืออาชีพ ถ้าไม่มีข้อมูลที่เพียงพอ ให้แนะนำให้ติดต่อฝ่าย HR โดยตรง"
         },
         {
           "role": "user",
           "content": "คำถาม: {{$json.originalQuery}}\n\nข้อมูลที่เกี่ยวข้อง: {{$json.relevantChunks.join('\n\n')}}\n\n{{$json.employeeData ? 'ข้อมูลพนักงาน: ' + JSON.stringify($json.employeeData) : ''}}"
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
         "sources": {{$node["Search HR Documents"].json.metadata}},
         "documentTypes": {{$node["Search HR Documents"].json.metadata.map(item => item.type)}}
       }
       ```

### 5. การสร้างช่องทางการเข้าถึงสำหรับพนักงาน

#### 5.1 พัฒนา Web Portal สำหรับพนักงาน

1. **สร้าง HTML Interface สำหรับพนักงาน**:
   ```html
   <!DOCTYPE html>
   <html>
   <head>
     <title>HR Assistant</title>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <style>
       body {
         font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
         margin: 0;
         padding: 20px;
         background-color: #f9f9f9;
         color: #333;
       }
       .container {
         max-width: 800px;
         margin: 0 auto;
         background: white;
         border-radius: 8px;
         box-shadow: 0 2px 4px rgba(0,0,0,0.1);
         padding: 20px;
       }
       .header {
         text-align: center;
         margin-bottom: 20px;
         padding-bottom: 10px;
         border-bottom: 1px solid #eee;
       }
       .header h1 {
         color: #2c3e50;
         margin-bottom: 5px;
       }
       .header p {
         color: #7f8c8d;
         margin-top: 5px;
       }
       .query-section {
         margin-bottom: 30px;
       }
       .query-input {
         width: 100%;
         padding: 15px;
         border-radius: 4px;
         border: 1px solid #ddd;
         font-size: 16px;
         box-sizing: border-box;
         margin-bottom: 10px;
       }
       .query-button {
         background-color: #3498db;
         color: white;
         border: none;
         padding: 12px 20px;
         border-radius: 4px;
         cursor: pointer;
         font-size: 16px;
         width: 100%;
         transition: background-color 0.3s;
       }
       .query-button:hover {
         background-color: #2980b9;
       }
       .loading {
         text-align: center;
         display: none;
         padding: 20px;
       }
       .loading img {
         width: 50px;
         height: 50px;
       }
       .result {
         padding: 20px;
         border-radius: 4px;
         background-color: #f8f9fa;
         display: none;
         margin-top: 20px;
       }
       .result-heading {
         color: #2c3e50;
         margin-top: 0;
       }
       .answer {
         line-height: 1.6;
       }
       .sources {
         margin-top: 20px;
         font-size: 0.9em;
         color: #7f8c8d;
       }
       .source-item {
         background-color: #e9f7fe;
         padding: 8px 12px;
         border-radius: 4px;
         margin-bottom: 5px;
         font-size: 0.9em;
       }
       .common-questions {
         margin-top: 30px;
       }
       .question-chip {
         display: inline-block;
         background-color: #e1f5fe;
         padding: 8px 15px;
         border-radius: 20px;
         margin-right: 10px;
         margin-bottom: 10px;
         cursor: pointer;
         transition: background-color 0.3s;
       }
       .question-chip:hover {
         background-color: #b3e5fc;
       }
       @media (max-width: 600px) {
         .container {
           padding: 15px;
         }
       }
     </style>
   </head>
   <body>
     <div class="container">
       <div class="header">
         <h1>HR Assistant</h1>
         <p>ถามคำถามเกี่ยวกับนโยบาย HR สวัสดิการ และการพัฒนาบุคลากร</p>
       </div>
       
       <div class="query-section">
         <input type="text" id="query-input" class="query-input" placeholder="ถามคำถามเกี่ยวกับงาน HR ได้ที่นี่...">
         <button id="query-button" class="query-button">ถาม</button>
       </div>
       
       <div class="common-questions">
         <h3>คำถามที่พบบ่อย:</h3>
         <div class="question-chip" onclick="fillQuestion('ขั้นตอนการขอลาพักร้อนเป็นอย่างไร?')">ขั้นตอนการลาพักร้อน</div>
         <div class="question-chip" onclick="fillQuestion('วิธีการเบิกค่ารักษาพยาบาลมีอะไรบ้าง?')">วิธีเบิกค่ารักษาพยาบาล</div>
         <div class="question-chip" onclick="fillQuestion('มีคอร์สอบรมอะไรบ้างสำหรับตำแหน่ง Project Manager?')">คอร์สอบรมสำหรับ PM</div>
         <div class="question-chip" onclick="fillQuestion('เงื่อนไขการปรับเงินเดือนประจำปีมีอะไรบ้าง?')">การปรับเงินเดือนประจำปี</div>
       </div>
       
       <div id="loading" class="loading">
         <img src="loading.gif" alt="กำลังโหลด...">
         <p>กำลังค้นหาคำตอบ...</p>
       </div>
       
       <div id="result" class="result">
         <h3 class="result-heading">คำตอบ:</h3>
         <div id="answer" class="answer"></div>
         <div id="sources" class="sources"></div>
       </div>
     </div>
     
     <script>
       async function askQuestion() {
         const query = document.getElementById('query-input').value;
         if (!query.trim()) return;
         
         // แสดง Loading
         document.getElementById('loading').style.display = 'block';
         document.getElementById('result').style.display = 'none';
         
         try {
           const response = await fetch('YOUR_WEBHOOK_URL', {
             method: 'POST',
             headers: { 'Content-Type': 'application/json' },
             body: JSON.stringify({ query: query })
           });
           
           if (!response.ok) {
             throw new Error('Network response was not ok');
           }
           
           const data = await response.json();
           
           // ซ่อน Loading และแสดงผลลัพธ์
           document.getElementById('loading').style.display = 'none';
           document.getElementById('result').style.display = 'block';
           
           // แสดงคำตอบ
           document.getElementById('answer').innerHTML = formatAnswer(data.answer);
           
           // แสดงแหล่งที่มา
           let sourcesHTML = '<h4>แหล่งข้อมูล:</h4>';
           if (data.sources && data.sources.length > 0) {
             sourcesHTML += '<div class="source-list">';
             const uniqueSources = [];
             data.sources.forEach(source => {
               if (!uniqueSources.includes(source.filename)) {
                 uniqueSources.push(source.filename);
                 sourcesHTML += `<div class="source-item">${source.filename} - ${getDocumentTypeLabel(source.type)}</div>`;
               }
             });
             sourcesHTML += '</div>';
           } else {
             sourcesHTML += '<p>ไม่มีข้อมูลแหล่งอ้างอิง</p>';
           }
           document.getElementById('sources').innerHTML = sourcesHTML;
           
         } catch (error) {
           console.error('Error:', error);
           document.getElementById('loading').style.display = 'none';
           document.getElementById('result').style.display = 'block';
           document.getElementById('answer').innerHTML = 
             '<p style="color: #e74c3c;">เกิดข้อผิดพลาดในการค้นหาคำตอบ กรุณาลองใหม่อีกครั้งหรือติดต่อฝ่าย HR โดยตรง</p>';
         }
       }
       
       function fillQuestion(question) {
         document.getElementById('query-input').value = question;
       }
       
       function formatAnswer(text) {
         // เพิ่มการจัดรูปแบบคำตอบให้อ่านง่ายขึ้น
         text = text.replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>');
         text = text.replace(/\n\n/g, '<br><br>');
         text = text.replace(/\n/g, '<br>');
         
         // เพิ่มการจัดรูปแบบสำหรับรายการ
         text = text.replace(/^\d+\.\s+(.*)$/gm, '<li>$1</li>');
         text = text.replace(/<li>/g, '<ol><li>').replace(/<\/li>/g, '</li></ol>');
         
         // เพิ่มการจัดรูปแบบสำหรับหัวข้อ
         text = text.replace(/^#\s+(.*)$/gm, '<h2>$1</h2>');
         text = text.replace(/^##\s+(.*)$/gm, '<h3>$1</h3>');
         
         return text;
       }
       
       function getDocumentTypeLabel(type) {
         const typeLabels = {
           'handbook': 'คู่มือพนักงาน',
           'benefits': 'สวัสดิการ',
           'leave_policy': 'นโยบายการลา',
           'training_development': 'การพัฒนาและฝึกอบรม',
           'expense_claims': 'การเบิกค่าใช้จ่าย',
           'compensation': 'ค่าตอบแทน',
           'employee_data': 'ข้อมูลพนักงาน'
         };
         
         return typeLabels[type] || type;
       }
       
       // Event Listeners
       document.getElementById('query-button').addEventListener('click', askQuestion);
       document.getElementById('query-input').addEventListener('keypress', function(e) {
         if (e.key === 'Enter') {
           askQuestion();
         }
       });
     </script>
   </body>
   </html>
   ```

#### 5.2 เชื่อมต่อกับแพลตฟอร์มภายในองค์กร (LINE, Slack, หรือ Microsoft Teams)

1. **สร้าง Workflow สำหรับ LINE Bot (ถ้าใช้งาน LINE)**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "HR Assistant LINE Bot"
   - เพิ่ม "Webhook" node สำหรับรับข้อความจาก LINE
   - ส่งคำถามไปยัง "HR Query System" workflow
   - ส่งคำตอบกลับไปยัง LINE

2. **สร้าง Workflow สำหรับ Slack Bot (ถ้าใช้งาน Slack)**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "HR Assistant Slack Bot"
   - เพิ่ม "Slack" node:
     - Event: message.im
     - Workspace: เลือก Slack workspace ที่ต้องการ
   - เชื่อมต่อกับ Function Node เพื่อแยกคำถามจากข้อความ
   - ส่งคำถามไปยัง "HR Query System" workflow
   - ส่งคำตอบกลับไปยัง Slack ด้วย "Slack Send" node

3. **สร้าง Workflow สำหรับ Microsoft Teams Bot (ถ้าใช้งาน Microsoft Teams)**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "HR Assistant Teams Bot"
   - เพิ่ม "Microsoft Teams" node ที่รับข้อความส่วนตัว
   - ส่งคำถามไปยัง "HR Query System" workflow
   - ส่งคำตอบกลับไปยัง Microsoft Teams

### 6. การติดตามและวิเคราะห์การใช้งาน

1. **สร้าง Workflow สำหรับบันทึกและวิเคราะห์คำถาม**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "HR Query Analytics"

2. **เพิ่ม Function Node ในทุก Workflow ที่เกี่ยวข้องกับการตอบคำถาม**:
   - บันทึกข้อมูลการใช้งาน:
     - คำถามที่ถาม
     - เวลาที่ถาม
     - ช่องทางการถาม
     - ประเภทของคำถาม
     - ความพึงพอใจกับคำตอบ (ถ้ามี)

3. **เพิ่ม MongoDB Node หรือ Database Node**:
   - บันทึกข้อมูลลงในฐานข้อมูล
   - กำหนดค่า:
     - Operation: Insert
     - Collection: hr_query_analytics
     - Data: ข้อมูลที่บันทึก

4. **สร้าง Dashboard สำหรับทีม HR**:
   - แสดงสถิติการใช้งาน
   - แสดงคำถามที่พบบ่อย
   - แสดงความพึงพอใจโดยรวม

### 7. การทดสอบและปรับปรุงระบบ

1. **ทดสอบการนำเข้าเอกสาร HR**:
   - ทดสอบกับเอกสารตัวอย่างประเภทต่างๆ
   - ตรวจสอบการจัดหมวดหมู่และการแยกเนื้อหา

2. **ทดสอบการตอบคำถาม**:
   - สร้างชุดคำถามทดสอบ:
     - คำถามเกี่ยวกับสวัสดิการ
     - คำถามเกี่ยวกับการลา
     - คำถามเกี่ยวกับการพัฒนาบุคลากร
     - คำถามเกี่ยวกับการเบิกค่าใช้จ่าย
     - คำถามเกี่ยวกับเงินเดือนและค่าตอบแทน
   - ประเมินความถูกต้องและความเกี่ยวข้องของคำตอบ

3. **ให้ทีม HR ทดสอบระบบ**:
   - ขอให้ผู้เชี่ยวชาญด้าน HR ทดลองใช้และให้คำแนะนำ
   - ปรับปรุง prompts และวิธีการประมวลผลตามคำแนะนำ

4. **ปรับปรุงระบบตามผลการทดสอบ**:
   - ปรับปรุงการแยกหมวดหมู่เอกสาร
   - ปรับปรุงการตีความคำถาม
   - เพิ่มแหล่งข้อมูลหรือเอกสารที่ยังขาด

## ตัวอย่างคำถามสำหรับทดสอบ

1. "ขั้นตอนการขอลาพักร้อนเป็นอย่างไรบ้าง?"
2. "วิธีการเบิกค่ารักษาพยาบาลทำอย่างไร?"
3. "สวัสดิการของบริษัทมีอะไรบ้าง?"
4. "มีคอร์สฝึกอบรมอะไรบ้างสำหรับตำแหน่ง Project Manager?"
5. "จำนวนวันลาที่ได้รับในแต่ละปีมีกี่วัน?"
6. "เงื่อนไขการปรับเงินเดือนประจำปีเป็นอย่างไร?"
7. "ขั้นตอนการประเมินผลงานประจำปีมีอะไรบ้าง?"
8. "สามารถเบิกค่าใช้จ่ายสำหรับการทำงานที่บ้านได้หรือไม่?"
9. "วิธีการยื่นขอใบรับรองการทำงานทำอย่างไร?"
10. "หลักเกณฑ์การจ่ายโบนัสประจำปีคืออะไร?"

## การวัดผลความสำเร็จ

1. **ประสิทธิภาพในการตอบคำถาม**:
   - วัดความถูกต้องของคำตอบ (ควรอยู่ที่ 85% ขึ้นไป)
   - วัดความรวดเร็วในการตอบคำถาม (เป้าหมายคือภายใน 3-5 วินาที)

2. **การลดภาระงานของฝ่าย HR**:
   - วัดจำนวนคำถามที่ระบบสามารถตอบได้อัตโนมัติ
   - วัดเวลาที่ฝ่าย HR ประหยัดได้ในการตอบคำถาม
   - เป้าหมาย: ลดภาระงานลง 40-60%

3. **ความพึงพอใจของผู้ใช้**:
   - สำรวจความพึงพอใจของพนักงานต่อระบบ
   - เก็บคะแนนความพึงพอใจหลังจากได้รับคำตอบ
   - เป้าหมาย: คะแนนความพึงพอใจ 4/5 ขึ้นไป

4. **ความครอบคลุมของข้อมูล**:
   - วัดอัตราการตอบคำถามได้สำเร็จ (ไม่ต้องส่งต่อให้ HR)
   - วัดจำนวนคำถามใหม่ที่ระบบไม่สามารถตอบได้
   - เป้าหมาย: ตอบคำถามได้ 80% โดยไม่ต้องส่งต่อ

## การขยายระบบในอนาคต

1. **เพิ่มความสามารถในการจัดการเอกสาร HR ส่วนบุคคล**:
   - ให้พนักงานสามารถอัปโหลดและขอเอกสารรับรองต่างๆ ได้
   - ระบบอนุมัติการลาอัตโนมัติสำหรับกรณีที่เป็นไปตามนโยบาย

2. **เพิ่มการวิเคราะห์เชิงลึก**:
   - วิเคราะห์แนวโน้มคำถามเพื่อปรับปรุงนโยบายและการสื่อสาร
   - ติดตามระดับความพึงพอใจของพนักงานผ่านการวิเคราะห์คำถาม

3. **สร้างระบบแนะนำการพัฒนาบุคลากร**:
   - วิเคราะห์ทักษะและประวัติการอบรมของพนักงาน
   - แนะนำหลักสูตรฝึกอบรมที่เหมาะสมกับตำแหน่งและเส้นทางอาชีพ

4. **เพิ่มการรองรับหลายภาษา**:
   - รองรับองค์กรที่มีพนักงานหลากหลายเชื้อชาติ
   - สามารถสลับระหว่างภาษาไทย อังกฤษ และภาษาอื่นๆ ได้

## สรุป

การสร้างระบบสนับสนุนงาน HR และการบริหารบุคลากรด้วย RAG บน n8n ช่วยลดภาระงานประจำของฝ่าย HR และปรับปรุงประสบการณ์ของพนักงานในการเข้าถึงข้อมูล โดยระบบสามารถตอบคำถามที่พบบ่อยเกี่ยวกับนโยบาย สวัสดิการ และการพัฒนาบุคลากรได้อย่างรวดเร็วและถูกต้อง

ระบบยังช่วยให้ฝ่าย HR มีเวลามากขึ้นในการทำงานเชิงกลยุทธ์ที่มีมูลค่าสูงกว่า เช่น การพัฒนาบุคลากร การสร้างวัฒนธรรมองค์กร และการวางแผนกำลังคน ในขณะที่พนักงานสามารถเข้าถึงข้อมูลที่ต้องการได้ตลอด 24 ชั่วโมงผ่านหลายช่องทาง

การพัฒนาและปรับปรุงระบบอย่างต่อเนื่องตามความต้องการขององค์กรจะช่วยเพิ่มประสิทธิภาพในการทำงานของฝ่าย HR และยกระดับประสบการณ์ของพนักงานในระยะยาว
