# การใช้งาน OpenAI API

![OpenAI API Usage](https://www.google.com/search?q=OpenAI+API+code+example&tbm=isch)

### การติดตั้งและตั้งค่า:
1. สมัคร/ล็อกอินที่ platform.openai.com
2. สร้าง API Key
3. เติมเครดิต (หลังจากหมดช่วงทดลองใช้ฟรี)

### ตัวอย่างการใช้งานใน Python:
```python
import openai

client = openai.OpenAI(api_key="your-api-key")
completion = client.chat.completions.create(
  model="gpt-3.5-turbo",
  messages=[
    {"role": "system", "content": "คุณคือผู้ช่วย AI ที่เป็นประโยชน์"},
    {"role": "user", "content": "เขียนบทความสั้นๆ เกี่ยวกับประเทศไทย"}
  ]
)
print(completion.choices[0].message.content)
```

## Presenter Notes (ข้อมูลสำหรับผู้บรรยาย)

> Key Takeaway: การเริ่มต้นใช้งาน OpenAI API นั้นไม่ซับซ้อน เริ่มจากการสมัครและรับ API key จากนั้นเลือกใช้ SDK ในภาษาที่ต้องการ หรือเรียกใช้ API โดยตรงผ่าน HTTP requests ความเข้าใจเรื่องโครงสร้าง request และ response รวมถึงพารามิเตอร์ต่างๆ เช่น temperature และ max_tokens จะช่วยให้สามารถปรับแต่งผลลัพธ์ได้ตามต้องการ

> Technical Terms: API Key, SDK (Software Development Kit), HTTP Request, JSON, Temperature parameter, Max tokens, System message, User message, Completion, Role-based messaging
