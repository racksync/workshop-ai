# วิธีการใช้งาน Gemini API

![Gemini API Usage](https://www.google.com/search?q=Google+Gemini+API+code+example&tbm=isch)

### การติดตั้งและตั้งค่า:
1. สมัคร/ล็อกอินที่ aistudio.google.com
2. สร้าง API Key
3. เลือกแผนการใช้งาน (มีทั้งแบบฟรีและ Enterprise)

### ตัวอย่างการใช้งานใน Python:
```python
import google.generativeai as genai
import os

# ตั้งค่า API key
genai.configure(api_key=os.environ["GEMINI_API_KEY"])

# เรียกใช้โมเดล Gemini
model = genai.GenerativeModel('gemini-pro')

# สร้างคำตอบ
response = model.generate_content("เขียนโปรแกรม Python สำหรับคำนวณเลขฟีโบนัชชี")
print(response.text)
```

## Presenter Notes (ข้อมูลสำหรับผู้บรรยาย)

> Key Takeaway: Gemini API มีวิธีการใช้งานที่คล้ายคลึงกับ OpenAI API แต่มีรายละเอียดปลีกย่อยที่แตกต่างกันบ้างในเรื่องของ SDK และโครงสร้างของ request/response อย่างไรก็ตาม Google มีการพัฒนา SDK อย่างต่อเนื่องเพื่อให้ใช้งานได้ง่ายขึ้น และที่สำคัญคือ Gemini มีโควตาฟรีที่มากกว่า OpenAI ทำให้สามารถทดลองใช้งานได้มากขึ้นโดยไม่มีค่าใช้จ่าย สำหรับการใช้งานกับรูปภาพจำเป็นต้องใช้ Gemini Pro Vision ซึ่งมีวิธีการใช้งานที่แตกต่างออกไปเล็กน้อย

> Technical Terms: GoogleGenerativeAI, GenerativeModel, generate_content, API configuration, Content generation, Multimodal input, Streaming response
