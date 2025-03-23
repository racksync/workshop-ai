# การใช้งาน API กับภาษาโปรแกรมมิ่ง

![API Integration](https://www.google.com/search?q=API+integration+with+programming+languages&tbm=isch)

## Python
```python
import requests

response = requests.post(
    "http://localhost:11434/api/chat",
    json={
        "model": "llama3",
        "messages": [{"role": "user", "content": "สวัสดี"}]
    }
)

print(response.json()["message"]["content"])
```

## Node.js
```javascript
const axios = require('axios');

async function chatWithModel() {
  const response = await axios.post('http://localhost:11434/api/chat', {
    model: 'llama3',
    messages: [{ role: 'user', content: 'สวัสดี' }]
  });
  
  console.log(response.data.message.content);
}
```

## Presenter Notes (ข้อมูลสำหรับผู้บรรยาย)
