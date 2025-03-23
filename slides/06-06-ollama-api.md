# การใช้งาน Ollama API

![API Endpoints](https://www.google.com/search?q=API+endpoints+diagram&tbm=isch)

Ollama มาพร้อมกับ RESTful API ที่ช่วยให้คุณเชื่อมต่อกับ LLM จากแอปพลิเคชันได้

```bash
# Chat Completion API
curl -X POST http://localhost:11434/api/chat -d '{
  "model": "llama3",
  "messages": [
    { "role": "user", "content": "สวัสดี!" }
  ]
}'

# Generation API
curl -X POST http://localhost:11434/api/generate -d '{
  "model": "llama3",
  "prompt": "เขียนบทความเกี่ยวกับประเทศไทย"
}'
```

## Presenter Notes (ข้อมูลสำหรับผู้บรรยาย)

> Key Takeaway: Ollama API ทำให้เราสามารถเชื่อมต่อ LLMs เข้ากับแอปพลิเคชันของเราได้ง่าย ไม่ต้องพึ่งพา API เชิงพาณิชย์ โดย API จะทำงานที่พอร์ต 11434 โดยอัตโนมัติเมื่อเริ่ม Ollama ซึ่งมี endpoint หลักๆ คือ `/api/chat` สำหรับการสนทนาแบบมีบทบาท และ `/api/generate` สำหรับการสร้างข้อความ นอกจากนี้ยังสามารถกำหนดพารามิเตอร์ต่างๆ ได้ เช่น temperature, top_p และยังรองรับการ stream response ทำให้สามารถแสดงผลแบบทีละตัวอักษรเหมือน ChatGPT ได้

> Technical Terms: RESTful API, HTTP Request, JSON, Chat Completion, Text Generation, Streaming Response, Endpoint
