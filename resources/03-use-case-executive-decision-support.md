# Hands-On: ระบบสนับสนุนการตัดสินใจสำหรับผู้บริหารด้วย RAG บน n8n

บทความนี้จะแนะนำขั้นตอนละเอียดในการสร้างระบบสนับสนุนการตัดสินใจสำหรับผู้บริหารโดยใช้เทคโนโลยี RAG (Retrieval-Augmented Generation) บนแพลตฟอร์ม n8n อย่างเป็นขั้นเป็นตอน

## เป้าหมายของระบบ

เราจะสร้างระบบที่:
- รวบรวมและประมวลผลข้อมูลจากหลายแหล่งสำหรับการตัดสินใจของผู้บริหาร
- วิเคราะห์ข้อมูลทางธุรกิจและนำเสนอข้อมูลเชิงลึก
- ตอบคำถามเชิงวิเคราะห์โดยอ้างอิงจากเอกสารและรายงานภายในองค์กร
- นำเสนอข้อมูลในรูปแบบที่เข้าใจง่ายและนำไปใช้ประโยชน์ได้ทันที
- รักษาความปลอดภัยของข้อมูลที่สำคัญและมีความอ่อนไหว

## สิ่งที่ต้องเตรียม

1. **n8n**: แพลตฟอร์มสำหรับสร้าง Workflow อัตโนมัติ
2. **Vector Database**: เช่น Qdrant, Milvus, หรือ Pinecone
3. **LLM API**: แนะนำให้ใช้ GPT-4 สำหรับการวิเคราะห์ข้อมูลทางธุรกิจที่ซับซ้อน
4. **แหล่งข้อมูล**:
   - รายงานทางการเงิน (PDF, Excel)
   - รายงานการวิเคราะห์ตลาด
   - ข้อมูลจากระบบ BI และ Analytics
   - ข้อมูลจากระบบ ERP
   - เอกสารกลยุทธ์และแผนธุรกิจ
5. **API Connections**: การเชื่อมต่อกับระบบข้อมูลต่างๆ ในองค์กร เช่น Power BI, Tableau, Google Analytics

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
     - n8n-nodes-langchain
     - n8n-nodes-chart-js (สำหรับสร้างแผนภูมิและกราฟ)
     - n8n-nodes-databases (เพื่อเชื่อมต่อกับฐานข้อมูล)

### 2. การสร้างระบบนำเข้าและประมวลผลข้อมูลจากหลายแหล่ง

#### 2.1 การนำเข้าข้อมูลจากรายงานทางการเงินและเอกสาร

1. **สร้าง Workflow ใหม่**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "Executive Data Ingestion - Reports"

2. **เพิ่ม Trigger Node สำหรับรายงานและเอกสาร**:
   - เลือก "Watch Folder" node
   - กำหนดค่า:
     - Folder Path: โฟลเดอร์ที่เก็บรายงานและเอกสาร
     - File Types: pdf, xlsx, docx, pptx
     - Include Subdirectories: true

3. **เพิ่ม Switch Node สำหรับแยกประเภทไฟล์**:
   - กำหนดเงื่อนไขตามนามสกุลไฟล์:
     - Case 1: *.pdf → เชื่อมต่อกับ "Read PDF" node
     - Case 2: *.xlsx → เชื่อมต่อกับ "Read Excel" node
     - Case 3: *.docx → เชื่อมต่อกับ "Read Document" node

4. **เพิ่ม Document Processing Nodes**:
   - สร้าง node ตามประเภทไฟล์ที่รองรับ
   - สำหรับไฟล์ PDF:
     ```javascript
     // แยกหน้าและดึงข้อมูล
     const pdfData = items[0].json.content;
     const fileName = items[0].json.filename;
     const fileDate = items[0].json.mtime || new Date().toISOString();
     
     // ดึงข้อมูล metadata จากชื่อไฟล์
     const reportTypeRegex = /(financial|market|sales|performance|strategy)/i;
     const reportType = fileName.match(reportTypeRegex) 
       ? fileName.match(reportTypeRegex)[0].toLowerCase() 
       : 'general';
     
     // ดึงข้อมูลช่วงเวลาจากชื่อไฟล์
     const periodRegex = /(Q[1-4]|[12][0-9]{3}|Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)/gi;
     const period = fileName.match(periodRegex) || [];
     
     return [
       {
         json: {
           content: pdfData,
           metadata: {
             filename: fileName,
             reportType: reportType,
             period: period.join('-'),
             date: fileDate,
             source: 'document_repository'
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
     const chunkSize = 1500; // ใหญ่กว่าปกติเพื่อให้มีบริบทมากขึ้น
     
     // แบ่งตามหัวข้อถ้าเป็นไปได้
     const headingRegex = /(?:\n|^)(?:#+|CHAPTER|บท(?:ที่)?|ส่วน(?:ที่)?|หัวข้อ|[IVX]+\.)\s+([^\n]+)/gi;
     const sections = text.split(headingRegex);
     
     if (sections.length <= 2) { // ถ้าแบ่งตามหัวข้อไม่ได้หรือได้น้อยเกินไป
       // แบ่งตามขนาด
       for (let i = 0; i < text.length; i += chunkSize) {
         chunks.push({
           json: {
             chunk: text.substring(i, i + chunkSize),
             metadata: {
               ...metadata,
               chunk_id: `${metadata.filename}_${Math.floor(i / chunkSize)}`,
               start_pos: i,
               end_pos: Math.min(i + chunkSize, text.length)
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
           
           // หากเนื้อหายาวเกินไป ให้แบ่งย่อย
           if (content.length > chunkSize * 2) {
             for (let j = 0; j < content.length; j += chunkSize) {
               chunks.push({
                 json: {
                   chunk: content.substring(j, j + chunkSize),
                   metadata: {
                     ...metadata,
                     heading: currentHeading,
                     chunk_id: `${metadata.filename}_${currentHeading}_${Math.floor(j / chunkSize)}`,
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
     - Collection Name: executive_reports
     - Vectors: {{$json.embedding}}
     - Payload: 
       ```json
       {
         "chunk": "{{$json.chunk}}",
         "metadata": {{$json.metadata}}
       }
       ```

#### 2.2 การนำเข้าข้อมูลจากระบบ BI และ APIs

1. **สร้าง Workflow ใหม่**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "Executive Data Ingestion - Business Systems"

2. **เพิ่ม Trigger Node**:
   - เลือก "Cron" node
   - กำหนดค่า: ให้ทำงานทุกวันหรือทุกสัปดาห์

3. **เพิ่ม HTTP Request Nodes**:
   - สร้าง HTTP Request nodes สำหรับแต่ละ API
   - ตัวอย่าง สำหรับระบบ Power BI:
     - Method: GET
     - URL: https://api.powerbi.com/v1.0/myorg/groups/{{groupId}}/reports/{{reportId}}
     - Headers: Authorization, Content-Type
     - Authentication: OAuth2 or API Key

4. **เพิ่ม Function Node สำหรับแปลงข้อมูล**:
   - เชื่อมต่อกับ HTTP Request Node
   - แปลงข้อมูลจาก API ให้เป็นรูปแบบที่เหมาะกับการเก็บใน Vector Database
   - ตัวอย่าง code:
     ```javascript
     // แปลงข้อมูลจาก BI API
     const biData = items[0].json;
     const dashboardName = biData.name || 'Untitled Dashboard';
     const reportDate = new Date().toISOString();
     
     // แยก metrics และ insights
     const metrics = [];
     const insights = [];
     
     // สำหรับข้อมูลจาก Power BI
     if (biData.datasetId) {
       // สร้าง text representation ของ dashboard
       let textRepresentation = `Dashboard: ${dashboardName}\n`;
       textRepresentation += `Date: ${reportDate}\n\n`;
       
       if (biData.reports && Array.isArray(biData.reports)) {
         biData.reports.forEach(report => {
           textRepresentation += `Report: ${report.name}\n`;
           
           if (report.metrics && Array.isArray(report.metrics)) {
             textRepresentation += "Key Metrics:\n";
             report.metrics.forEach(metric => {
               textRepresentation += `- ${metric.name}: ${metric.value}\n`;
               metrics.push({
                 name: metric.name,
                 value: metric.value,
                 report: report.name
               });
             });
           }
         });
       }
       
       return [
         {
           json: {
             chunk: textRepresentation,
             metadata: {
               source: 'business_intelligence',
               type: 'dashboard',
               name: dashboardName,
               date: reportDate,
               metrics: metrics,
               insights: insights
             }
           }
         }
       ];
     }
     ```

5. **เพิ่ม OpenAI Node สำหรับสร้าง Insights**:
   - Operation: Create Chat Completion
   - Model: gpt-4
   - Messages:
     ```
     [
       {
         "role": "system",
         "content": "คุณเป็นผู้เชี่ยวชาญด้านการวิเคราะห์ข้อมูลทางธุรกิจ ให้วิเคราะห์ข้อมูลต่อไปนี้และสร้าง insights สำคัญ 3-5 ประเด็น รวมถึงแนวโน้มที่น่าสนใจ"
       },
       {
         "role": "user",
         "content": "วิเคราะห์ข้อมูลต่อไปนี้:\n\n{{$json.chunk}}\n\nกรุณาเขียน insights สำคัญในรูปแบบที่ผู้บริหารระดับสูงจะเข้าใจได้ง่าย"
       }
     ]
     ```

6. **เพิ่ม Function Node เพื่อผสานข้อมูลและ Insights**:
   - รวมข้อมูลดิบกับ insights ที่สร้างขึ้น
   - เตรียมข้อมูลสำหรับการสร้าง embedding

7. **เพิ่ม OpenAI Embedding Node**:
   - Operation: Create Embedding
   - Model: text-embedding-ada-002
   - Input: {{$json.combinedContent}}

8. **เพิ่ม Vector Database Node**:
   - Operation: Upsert
   - Collection Name: executive_insights
   - Vectors: {{$json.embedding}}
   - Payload ควรประกอบด้วย:
     - ข้อมูลดิบ
     - Insights ที่สร้างขึ้น
     - Metadata ที่เกี่ยวข้อง

### 3. การสร้างระบบสำหรับการค้นหาและวิเคราะห์ข้อมูล

1. **สร้าง Workflow ใหม่**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "Executive Analytics Query"

2. **เพิ่ม Trigger Node**:
   - เลือก "Webhook" node
   - กำหนดค่า endpoint สำหรับรับคำถาม

3. **เพิ่ม Function Node สำหรับวิเคราะห์คำถาม**:
   - วิเคราะห์คำถามว่าต้องการข้อมูลประเภทใด
   - ตัวอย่าง code:
     ```javascript
     // วิเคราะห์คำถามเพื่อเตรียมการค้นหา
     const query = items[0].json.query;
     
     // ตรวจสอบประเภทคำถาม
     const isComparison = /เปรียบเทียบ|เทียบ|แตกต่าง|ต่าง|กว่า|มากกว่า|น้อยกว่า/i.test(query);
     const isTrend = /แนวโน้ม|trend|ทิศทาง|อนาคต|คาดการณ์|forecast|predict/i.test(query);
     const isPerformance = /ผลประกอบการ|ผลการดำเนินงาน|ผลกำไร|รายได้|ต้นทุน|performance/i.test(query);
     
     // ตรวจสอบช่วงเวลา
     const timeRegex = /(ไตรมาส|ปี|เดือน|Q[1-4]|[12][0-9]{3}|Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)/gi;
     const timePeriods = query.match(timeRegex) || [];
     
     // ตรวจสอบแผนก/หน่วยธุรกิจ
     const departmentRegex = /แผนก\s*([ก-๙A-Za-z0-9]+)|ฝ่าย\s*([ก-๙A-Za-z0-9]+)|หน่วย\s*([ก-๙A-Za-z0-9]+)|ส่วน\s*([ก-๙A-Za-z0-9]+)|สาขา\s*([ก-๙A-Za-z0-9]+)|บริษัท\s*([ก-๙A-Za-z0-9]+)/gi;
     const departments = [];
     let match;
     while ((match = departmentRegex.exec(query)) !== null) {
       departments.push(match[1] || match[2] || match[3] || match[4] || match[5] || match[6]);
     }
     
     return [
       {
         json: {
           originalQuery: query,
           queryType: {
             isComparison,
             isTrend,
             isPerformance
           },
           timePeriods,
           departments,
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

5. **เพิ่ม Function Node สำหรับกำหนดคอลเลกชันที่จะค้นหา**:
   - ตัดสินใจว่าจะค้นหาจากคอลเลกชันใดบ้าง
   - ตัวอย่าง code:
     ```javascript
     // กำหนดแหล่งข้อมูลที่จะค้นหาตามประเภทคำถาม
     const queryType = items[0].json.queryType;
     const collections = [];
     
     // เพิ่มคอลเลกชันตามประเภทคำถาม
     if (queryType.isPerformance) {
       collections.push("executive_reports");
       collections.push("executive_insights");
     } else if (queryType.isTrend) {
       collections.push("executive_insights");
       collections.push("executive_reports");
     } else {
       collections.push("executive_reports");
       collections.push("executive_insights");
     }
     
     return [
       {
         json: {
           ...items[0].json,
           collections
         }
       }
     ];
     ```

6. **เพิ่ม Multi-Vector Database Query Node**:
   - สร้าง Function Node ที่จะค้นหาข้อมูลจากหลาย Collection
   - ตัวอย่าง code:
     ```javascript
     // ค้นหาจากหลาย Collection
     const collections = items[0].json.collections;
     const embedding = items[0].json.embedding;
     const results = [];
     
     // สร้าง HTTP requests สำหรับแต่ละ collection
     for (const collection of collections) {
       // ปรับตามการเชื่อมต่อและโครงสร้าง API ของ Vector Database ที่ใช้
       const response = await $http.post({
         url: `http://your-vector-db-server/collections/${collection}/points/search`,
         body: {
           vector: embedding,
           limit: 5
         },
         headers: {
           'Content-Type': 'application/json'
         }
       });
       
       if (response.data && response.data.result) {
         results.push({
           collection,
           results: response.data.result
         });
       }
     }
     
     return [
       {
         json: {
           ...items[0].json,
           searchResults: results
         }
       }
     ];
     ```

7. **เพิ่ม Function Node สำหรับรวมและจัดเรียงผลลัพธ์**:
   - รวมผลลัพธ์จากทุก Collection และจัดเรียงตามความเกี่ยวข้อง
   - ตัวอย่าง code:
     ```javascript
     // รวมและจัดเรียงผลลัพธ์
     const searchResults = items[0].json.searchResults;
     let allResults = [];
     
     // รวมผลลัพธ์จากทุก collection
     searchResults.forEach(searchResult => {
       const collection = searchResult.collection;
       searchResult.results.forEach(result => {
         allResults.push({
           ...result,
           collection
         });
       });
     });
     
     // จัดเรียงตาม score
     allResults.sort((a, b) => b.score - a.score);
     
     // เลือกผลลัพธ์ที่ดีที่สุด
     const topResults = allResults.slice(0, 10);
     
     // สร้างข้อมูลสำหรับ LLM
     const relevantChunks = topResults.map(r => r.payload.chunk);
     const metadata = topResults.map(r => ({
       collection: r.collection,
       ...r.payload.metadata
     }));
     
     return [
       {
         json: {
           ...items[0].json,
           relevantChunks,
           metadata,
           topResults
         }
       }
     ];
     ```

8. **เพิ่ม Function Node เพื่อดึงข้อมูลเพิ่มเติมจาก API (ถ้าจำเป็น)**:
   - หากต้องการข้อมูลปัจจุบันที่เฉพาะเจาะจง
   - ตัวอย่าง code:
     ```javascript
     // ตรวจสอบว่าต้องดึงข้อมูลเพิ่มเติมหรือไม่
     const query = items[0].json.originalQuery;
     const metadata = items[0].json.metadata;
     
     // ตรวจสอบว่าต้องการข้อมูลเรียลไทม์หรือไม่
     const needsRealtimeData = /ปัจจุบัน|ล่าสุด|real-time|เรียลไทม์|วันนี้|สัปดาห์นี้|เดือนนี้/i.test(query);
     
     if (needsRealtimeData) {
       try {
         // ตัวอย่างการดึงข้อมูลจาก API ภายใน
         const response = await $http.get({
           url: 'http://your-internal-api/dashboard/latest',
           headers: {
             'Authorization': 'Bearer YOUR_TOKEN'
           }
         });
         
         return [
           {
             json: {
               ...items[0].json,
               realtimeData: response.data
             }
           }
         ];
       } catch (error) {
         // หากมีข้อผิดพลาด ให้ดำเนินการต่อโดยไม่มีข้อมูลเรียลไทม์
         return [
           {
             json: {
               ...items[0].json,
               realtimeData: null,
               realtimeError: error.message
             }
           }
         ];
       }
     } else {
       return items; // ส่งต่อข้อมูลเดิมหากไม่ต้องการข้อมูลเรียลไทม์
     }
     ```

9. **เพิ่ม OpenAI Completion Node**:
   - เชื่อมต่อกับ Function Node
   - กำหนดค่า:
     - Operation: Create Chat Completion
     - Model: gpt-4
     - Messages:
       ```
       [
         {
           "role": "system",
           "content": "คุณเป็นผู้ช่วยวิเคราะห์ข้อมูลสำหรับผู้บริหารระดับสูง ให้ตอบคำถามโดยใช้ข้อมูลที่เกี่ยวข้องที่ได้รับมา วิเคราะห์แนวโน้ม สรุปประเด็นสำคัญ และนำเสนอข้อมูลเชิงลึก ตอบในรูปแบบที่เป็นมืออาชีพ กระชับ และมีการจัดรูปแบบที่อ่านง่าย ใช้หัวข้อและจุดสรุปเมื่อเหมาะสม เสนอมุมมองที่เป็นประโยชน์ต่อการตัดสินใจทางธุรกิจ"
         },
         {
           "role": "user",
           "content": "คำถาม: {{$json.originalQuery}}\n\nข้อมูลที่เกี่ยวข้อง:\n{{$json.relevantChunks.join('\n\n')}}\n\n{{$json.realtimeData ? 'ข้อมูลปัจจุบัน: ' + JSON.stringify($json.realtimeData) : ''}}"
         }
       ]
       ```

10. **เพิ่ม Function Node สำหรับสร้างกราฟและแผนภูมิ (ถ้าจำเป็น)**:
    - วิเคราะห์คำถามและข้อมูลเพื่อตัดสินว่าควรสร้างภาพข้อมูลหรือไม่
    - ถ้าเป็นคำถามเกี่ยวกับการเปรียบเทียบหรือแนวโน้ม ให้สร้างภาพข้อมูลที่เหมาะสม
    - ใช้ Chart.js Node หรือเตรียม JSON สำหรับสร้างกราฟ

11. **เพิ่ม Respond to Webhook Node**:
    - เชื่อมต่อกับ OpenAI Completion Node (และ Chart Node ถ้ามี)
    - กำหนดค่า:
      - Response Body:
        ```
        {
          "answer": {{$json.choices[0].message.content}},
          "sources": {{$node["Combine Results"].json.metadata}},
          "chartData": {{$node["Generate Charts"] ? $node["Generate Charts"].json.chartData : null}}
        }
        ```

### 4. การสร้างหน้า Dashboard สำหรับผู้บริหาร

1. **สร้าง HTML Interface**:
   ```html
   <!DOCTYPE html>
   <html>
   <head>
     <title>Executive Decision Support</title>
     <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
     <style>
       body {
         font-family: 'Arial', sans-serif;
         margin: 0;
         padding: 20px;
         color: #333;
       }
       .container {
         max-width: 1200px;
         margin: 0 auto;
       }
       .header {
         display: flex;
         justify-content: space-between;
         align-items: center;
         margin-bottom: 20px;
         padding-bottom: 10px;
         border-bottom: 1px solid #eee;
       }
       .query-section {
         margin-bottom: 30px;
       }
       .query-input {
         width: 80%;
         padding: 12px;
         font-size: 16px;
         border: 1px solid #ddd;
         border-radius: 4px;
       }
       .query-button {
         padding: 12px 20px;
         font-size: 16px;
         background-color: #0066cc;
         color: white;
         border: none;
         border-radius: 4px;
         cursor: pointer;
       }
       .loading {
         display: none;
         text-align: center;
         padding: 20px;
       }
       .results-section {
         display: flex;
         flex-wrap: wrap;
       }
       .text-results {
         flex: 1;
         min-width: 300px;
         padding: 20px;
         background-color: #f9f9f9;
         border-radius: 4px;
       }
       .chart-container {
         flex: 1;
         min-width: 450px;
         height: 400px;
         padding: 20px;
       }
       .sources {
         margin-top: 20px;
         font-size: 0.9em;
         color: #666;
         border-top: 1px solid #eee;
         padding-top: 10px;
       }
       @media (max-width: 768px) {
         .results-section {
           flex-direction: column;
         }
       }
     </style>
   </head>
   <body>
     <div class="container">
       <div class="header">
         <h1>ระบบสนับสนุนการตัดสินใจสำหรับผู้บริหาร</h1>
       </div>
       
       <div class="query-section">
         <input type="text" id="query-input" class="query-input" placeholder="ถามคำถามเกี่ยวกับข้อมูลธุรกิจ..." />
         <button id="query-button" class="query-button">วิเคราะห์</button>
       </div>
       
       <div id="loading" class="loading">
         <img src="loading.gif" alt="Loading..." />
         <p>กำลังวิเคราะห์ข้อมูล...</p>
       </div>
       
       <div class="results-section">
         <div class="text-results" id="text-results"></div>
         <div class="chart-container">
           <canvas id="data-chart"></canvas>
         </div>
       </div>
       
       <div class="sources" id="sources"></div>
     </div>
     
     <script>
       // ฟังก์ชันสำหรับส่งคำถามไปยัง API และแสดงผลลัพธ์
       async function askQuestion() {
         const query = document.getElementById('query-input').value;
         if (!query.trim()) return;
         
         // แสดง Loading
         document.getElementById('loading').style.display = 'block';
         document.getElementById('text-results').innerHTML = '';
         document.getElementById('sources').innerHTML = '';
         
         try {
           const response = await fetch('YOUR_WEBHOOK_URL', {
             method: 'POST',
             headers: {
               'Content-Type': 'application/json',
             },
             body: JSON.stringify({ query }),
           });
           
           const data = await response.json();
           
           // ซ่อน Loading
           document.getElementById('loading').style.display = 'none';
           
           // แสดงข้อความตอบกลับ
           document.getElementById('text-results').innerHTML = formatAnswer(data.answer);
           
           // แสดงแหล่งที่มา
           let sourcesHTML = '<h3>แหล่งข้อมูล:</h3><ul>';
           if (data.sources && data.sources.length > 0) {
             data.sources.forEach(source => {
               sourcesHTML += `<li>${source.filename || source.name || 'ไม่ระบุ'} ${source.date ? `(${formatDate(source.date)})` : ''}</li>`;
             });
           } else {
             sourcesHTML += '<li>ไม่มีข้อมูลแหล่งที่มา</li>';
           }
           sourcesHTML += '</ul>';
           document.getElementById('sources').innerHTML = sourcesHTML;
           
           // สร้างกราฟ (ถ้ามี)
           if (data.chartData) {
             createChart(data.chartData);
           }
         } catch (error) {
           console.error('Error:', error);
           document.getElementById('loading').style.display = 'none';
           document.getElementById('text-results').innerHTML = 
             '<p class="error">เกิดข้อผิดพลาดในการวิเคราะห์ข้อมูล กรุณาลองอีกครั้ง</p>';
         }
       }
       
       // จัดรูปแบบคำตอบให้อ่านง่าย
       function formatAnswer(answer) {
         // แยกหัวข้อและสร้าง HTML
         let formattedAnswer = answer.replace(/\n## (.*)/g, '<h2>$1</h2>');
         formattedAnswer = formattedAnswer.replace(/\n# (.*)/g, '<h1>$1</h1>');
         formattedAnswer = formattedAnswer.replace(/\n\* (.*)/g, '<li>$1</li>');
         formattedAnswer = formattedAnswer.replace(/\n\d+\. (.*)/g, '<li>$1</li>');
         
         // เพิ่ม HTML tags สำหรับรายการที่แยกเป็นหัวข้อย่อย
         if (formattedAnswer.includes('<li>')) {
           formattedAnswer = formattedAnswer.replace(/<li>/g, '<ul><li>');
           formattedAnswer = formattedAnswer.replace(/<\/li>\n/g, '</li></ul>\n');
         }
         
         // แยกย่อหน้า
         formattedAnswer = formattedAnswer.replace(/\n\n/g, '<br><br>');
         
         return formattedAnswer;
       }
       
       // สร้างกราฟจากข้อมูล
       function createChart(chartData) {
         const canvas = document.getElementById('data-chart');
         const ctx = canvas.getContext('2d');
         
         // ถ้ามีกราฟเดิมอยู่ ให้ทำลายก่อน
         if (window.myChart) {
           window.myChart.destroy();
         }
         
         // สร้างกราฟใหม่
         window.myChart = new Chart(ctx, {
           type: chartData.type || 'bar',
           data: {
             labels: chartData.labels,
             datasets: chartData.datasets
           },
           options: chartData.options || {
             responsive: true,
             maintainAspectRatio: false
           }
         });
       }
       
       // จัดรูปแบบวันที่
       function formatDate(dateString) {
         const date = new Date(dateString);
         return date.toLocaleDateString('th-TH', {
           year: 'numeric',
           month: 'long',
           day: 'numeric'
         });
       }
       
       // เพิ่ม Event Listener
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

### 5. การตั้งค่าการรักษาความปลอดภัยและการเข้าถึงข้อมูล

1. **สร้าง Workflow สำหรับการตรวจสอบสิทธิ์**:
   - คลิก "Create Workflow"
   - ตั้งชื่อ "Auth Check Middleware"

2. **เพิ่ม Function Node สำหรับตรวจสอบการเข้าถึง**:
   ```javascript
   // ตรวจสอบสิทธิ์การเข้าถึง
   const authToken = $node.req.headers.authorization;
   
   if (!authToken || !authToken.startsWith('Bearer ')) {
     return [
       {
         json: {
           error: 'Unauthorized',
           message: 'ไม่พบข้อมูลการยืนยันตัวตน',
           status: 401
         }
       }
     ];
   }
   
   const token = authToken.replace('Bearer ', '');
   
   try {
     // ตัวอย่างการตรวจสอบ token กับระบบภายใน
     const response = await $http.post({
       url: 'https://your-auth-server/verify',
       body: {
         token: token
       }
     });
     
     if (!response.data || !response.data.valid) {
       throw new Error('Invalid token');
     }
     
     // ตรวจสอบสิทธิ์การเข้าถึงข้อมูลผู้บริหาร
     if (!response.data.roles.includes('executive') && 
         !response.data.roles.includes('admin')) {
       return [
         {
           json: {
             error: 'Forbidden',
             message: 'ไม่มีสิทธิ์เข้าถึงข้อมูลสำหรับผู้บริหาร',
             status: 403
           }
         }
       ];
     }
     
     // ส่งต่อข้อมูลผู้ใช้
     return [
       {
         json: {
           ...items[0].json,
           user: {
             id: response.data.userId,
             name: response.data.name,
             roles: response.data.roles,
             department: response.data.department
           }
         }
       }
     ];
     
   } catch (error) {
     return [
       {
         json: {
           error: 'Authentication Error',
           message: error.message,
           status: 401
         }
       }
     ];
   }
   ```

3. **เชื่อมต่อ Auth Middleware กับ Workflow หลัก**:
   - ปรับ Webhook Node ในทุก Workflow ให้เรียกใช้ Auth Check Middleware ก่อนดำเนินการต่อ

### 6. การทดสอบและปรับปรุงระบบ

1. **ทดสอบการนำเข้าข้อมูล**:
   - เตรียมรายงานและข้อมูลตัวอย่างสำหรับทดสอบ
   - ตรวจสอบว่าข้อมูลถูกแบ่งและจัดเก็บในฐานข้อมูลเวกเตอร์อย่างถูกต้อง

2. **ทดสอบการตอบคำถาม**:
   - เตรียมชุดคำถามทดสอบที่หลากหลายประเภท เช่น:
     - คำถามเกี่ยวกับผลประกอบการ
     - คำถามเกี่ยวกับแนวโน้มตลาด
     - คำถามเปรียบเทียบข้อมูลระหว่างแผนกหรือช่วงเวลา
   - วัดความถูกต้องและความเร็วในการตอบคำถาม

3. **ทดสอบกับผู้บริหารจริง**:
   - ขอให้ผู้บริหารทดลองใช้ระบบและให้ข้อเสนอแนะ
   - รวบรวมความคิดเห็นเกี่ยวกับความถูกต้อง ความเร็ว และการนำเสนอข้อมูล

4. **ปรับปรุงประสิทธิภาพ**:
   - เพิ่มประสิทธิภาพ prompt และวิธีการแบ่งข้อมูล
   - ปรับปรุงการตีความคำถาม
   - พัฒนาการนำเสนอข้อมูลวิเคราะห์และกราฟ

## ตัวอย่างคำถามสำหรับทดสอบ

1. "เปรียบเทียบผลประกอบการระหว่างแผนกขายและแผนกการตลาดในไตรมาสที่ผ่านมา"
2. "แนวโน้มยอดขายในช่วง 6 เดือนที่ผ่านมาเป็นอย่างไร และมีปัจจัยใดที่ส่งผลกระทบบ้าง?"
3. "สรุปผลการดำเนินงานของบริษัทในปี 2023 เทียบกับเป้าหมายที่ตั้งไว้"
4. "วิเคราะห์ส่วนแบ่งการตลาดของผลิตภัณฑ์หลักเทียบกับคู่แข่งในปัจจุบัน"
5. "สถานการณ์การเงินและสภาพคล่องของบริษัทเป็นอย่างไรในเดือนที่ผ่านมา?"
6. "ประสิทธิภาพของแคมเปญการตลาดล่าสุดเทียบกับงบประมาณที่ใช้ไป"
7. "ประเด็นความเสี่ยงที่สำคัญที่ควรให้ความสนใจในไตรมาสถัดไปมีอะไรบ้าง?"
8. "หน่วยธุรกิจใดมีอัตราการเติบโตสูงสุดในปีที่ผ่านมา และมีปัจจัยความสำเร็จอะไรบ้าง?"

## การวัดผลความสำเร็จ

1. **ความถูกต้องของการตอบคำถาม**:
   - วัดความถูกต้องของข้อมูลที่นำเสนอเทียบกับแหล่งข้อมูลต้นฉบับ
   - ประเมินโดยผู้เชี่ยวชาญด้านข้อมูล

2. **ความเร็วในการตอบสนอง**:
   - เปรียบเทียบเวลาที่ผู้บริหารใช้ในการหาข้อมูลแบบเดิมกับการใช้ระบบใหม่
   - เป้าหมาย: ลดเวลาจากหลายชั่วโมงเหลือไม่กี่นาที

3. **คุณภาพของการวิเคราะห์**:
   - ประเมินความลึกและความครอบคลุมของการวิเคราะห์
   - ตรวจสอบว่าสามารถให้ข้อมูลเชิงลึกที่มีคุณค่าต่อการตัดสินใจหรือไม่

4. **ความพึงพอใจของผู้ใช้**:
   - สำรวจความพึงพอใจของผู้บริหารที่ใช้งานระบบ
   - รวบรวมข้อเสนอแนะเพื่อการปรับปรุง

## การขยายระบบในอนาคต

1. **เพิ่มแหล่งข้อมูลภายนอก**:
   - เชื่อมต่อกับข้อมูลข่าว บทวิเคราะห์ตลาด และรายงานอุตสาหกรรมภายนอก
   - เพิ่มการวิเคราะห์คู่แข่งและตลาด

2. **พัฒนาระบบการคาดการณ์**:
   - เพิ่มโมเดล ML สำหรับการคาดการณ์แนวโน้มในอนาคต
   - เชื่อมต่อกับเครื่องมือ Time Series Analysis

3. **สร้างการแจ้งเตือนอัจฉริยะ**:
   - ระบบแจ้งเตือนเมื่อพบความผิดปกติในข้อมูลหรือตัวชี้วัดสำคัญ
   - การแจ้งเตือนตามบริบทและตำแหน่งงานของผู้บริหาร

4. **พัฒนาแอพพลิเคชั่นมือถือ**:
   - สร้าง mobile interface สำหรับผู้บริหารที่ต้องเดินทางบ่อย
   - เพิ่มฟีเจอร์การแจ้งเตือนและดูข้อมูลย่อบนมือถือ

## สรุป

การสร้างระบบสนับสนุนการตัดสินใจสำหรับผู้บริหารด้วย RAG บน n8n ช่วยให้ผู้บริหารสามารถเข้าถึงข้อมูลและการวิเคราะห์ที่จำเป็นได้อย่างรวดเร็วและแม่นยำ โดยระบบสามารถดึงข้อมูลจากหลายแหล่ง วิเคราะห์ และสรุปผลในรูปแบบที่เข้าใจได้ง่าย ช่วยลดเวลาในการค้นหาข้อมูล เพิ่มประสิทธิภาพในการตัดสินใจ และเพิ่มความได้เปรียบในการแข่งขันทางธุรกิจ การพัฒนาระบบอย่างต่อเนื่องและการเพิ่มแหล่งข้อมูลจะช่วยให้ระบบมีความสามารถมากขึ้นและสามารถตอบสนองความต้องการของผู้บริหารได้ดียิ่งขึ้นในอนาคต
