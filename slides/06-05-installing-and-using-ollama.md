# การติดตั้งและใช้งาน Ollama

![Ollama Installation](https://www.google.com/search?q=ollama+installation+terminal&tbm=isch)

## การติดตั้ง Ollama
- **macOS**: `brew install ollama`
- **Linux**: `curl -fsSL https://ollama.com/install.sh | sh`
- **Windows**: ดาวน์โหลดจาก ollama.com/download

## คำสั่งพื้นฐาน
```bash
# ดาวน์โหลดโมเดล
ollama pull llama3

# แชทกับ AI
ollama run llama3
```

## Presenter Notes (ข้อมูลสำหรับผู้บรรยาย)

> Key Takeaway: การติดตั้ง Ollama ทำได้ง่ายบนทุกระบบปฏิบัติการหลัก และการใช้งานก็เรียบง่ายด้วยคำสั่งไม่กี่คำสั่ง ทำให้เริ่มต้นได้อย่างรวดเร็ว สิ่งที่ควรทราบคือโมเดลต่างๆ มีขนาดต่างกัน เช่น llama3 มีขนาดประมาณ 4-8GB ขึ้นอยู่กับ variant คำสั่ง `ollama list` ใช้ดูรายชื่อโมเดลที่ติดตั้งแล้ว และ `ollama rm [model]` ใช้ลบโมเดลที่ไม่ต้องการ จุดเด่นของ Ollama คือความง่าย ไม่ต้องตั้งค่าอะไรมาก ใช้ RAM ในการรันน้อยกว่าตัวเลือกอื่นๆ

> Technical Terms: Command Line Interface, Model Download, Package Manager, Terminal Commands, Model Variants, Quantization
