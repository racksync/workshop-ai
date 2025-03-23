# การเชื่อมต่อกับ Custom Endpoints

![API Connections](https://www.google.com/search?q=multiple+API+endpoints+connection+diagram&tbm=isch)

Open-WebUI สามารถเชื่อมต่อกับ API ภายนอกนอกเหนือจาก Ollama ได้

## ประเภทของ Endpoints ที่รองรับ
- OpenAI API
- Anthropic Claude API
- Google AI (Gemini)
- Azure OpenAI
- Mistral AI
- และ API อื่นๆ ที่เข้ากันได้กับ OpenAI

## Presenter Notes (ข้อมูลสำหรับผู้บรรยาย)

> Key Takeaway: การเพิ่ม Custom Endpoints ใน Open-WebUI ช่วยให้เราสามารถใช้งาน LLMs จากหลากหลาย provider ผ่านอินเตอร์เฟซเดียวกัน ทำให้มีความยืดหยุ่นสูงในการใช้งาน ซึ่งเป็นประโยชน์ในแง่ของการเปรียบเทียบผลลัพธ์ระหว่างโมเดลต่างๆ (เช่น เปรียบเทียบคำตอบระหว่าง local Llama3 กับ GPT-4) และยังช่วยให้องค์กรสามารถเลือกใช้โมเดลที่เหมาะสมกับงานแต่ละประเภท เช่น ใช้ local model สำหรับข้อมูลภายในที่เป็นความลับ และใช้ OpenAI API สำหรับงานที่ต้องการความสามารถขั้นสูง

> Technical Terms: API Endpoints, Authentication, Multiple Model Integration, Model Fallback, API Key Management, Model Selection
