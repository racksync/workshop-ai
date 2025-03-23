# Session 7: Bolt Framework

## 🔍 ภาพรวม

Bolt Framework เป็นเฟรมเวิร์กการพัฒนาเว็บแอปพลิเคชันที่มุ่งเน้นความรวดเร็วและประสิทธิภาพ โดยรวมทั้งส่วน front-end และ back-end ไว้ในเฟรมเวิร์กเดียว ทำให้นักพัฒนาสามารถสร้างแอปพลิเคชันได้อย่างรวดเร็วโดยไม่จำเป็นต้องตั้งค่าสภาพแวดล้อมที่ซับซ้อน ในเซสชันนี้ เราจะเรียนรู้เกี่ยวกับ Bolt Framework ตั้งแต่พื้นฐาน การติดตั้ง การพัฒนาแอปพลิเคชัน และการนำไปใช้งานจริง

## 🎯 วัตถุประสงค์การเรียนรู้

- เข้าใจหลักการทำงานและประโยชน์ของ Bolt Framework
- สามารถติดตั้งและตั้งค่า Bolt Framework ได้
- เรียนรู้การพัฒนาแอปพลิเคชันด้วย Bolt ทั้ง front-end และ back-end
- เข้าใจเรื่อง routing, middleware และ templating ใน Bolt
- สามารถเชื่อมต่อ Bolt กับฐานข้อมูลและบริการภายนอกได้
- เรียนรู้แนวทางในการ deploy แอปพลิเคชัน Bolt ในสภาพแวดล้อมต่างๆ

## 📚 เนื้อหา

### 1. Bolt Framework คืออะไร

Bolt Framework เป็นเฟรมเวิร์กที่ช่วยให้นักพัฒนาสามารถสร้างแอปพลิเคชันเว็บได้อย่างรวดเร็วและมีประสิทธิภาพ โดยมีลักษณะเด่นคือการรวมทั้ง front-end และ back-end ไว้ในเฟรมเวิร์กเดียว

#### 1.1 ประวัติและที่มา

Bolt Framework เป็นเฟรมเวิร์กที่ได้รับแรงบันดาลใจจากหลักการเขียนโค้ดที่สะอาดและมีประสิทธิภาพ พัฒนาขึ้นเพื่อแก้ปัญหาในการพัฒนาเว็บแอปพลิเคชันที่ต้องใช้หลายเทคโนโลยีและเฟรมเวิร์กร่วมกัน ทำให้การพัฒนาและบำรุงรักษามีความซับซ้อน

#### 1.2 คุณสมบัติหลักของ Bolt Framework

- **Unified Development**: รวมการพัฒนาทั้ง front-end และ back-end ในโค้ดเบสเดียว
- **Performance**: เน้นประสิทธิภาพการทำงานที่รวดเร็ว ลดเวลาโหลดเพจ
- **Modular Architecture**: สถาปัตยกรรมแบบโมดูลที่ช่วยให้แบ่งโค้ดเป็นส่วนย่อยที่จัดการได้ง่าย
- **Built-in Utilities**: มีเครื่องมือและฟังก์ชันที่จำเป็นสำหรับการพัฒนาเว็บแบบครบวงจร
- **API Integration**: รองรับการเชื่อมต่อกับ API ภายนอกได้อย่างง่ายดาย
- **Minimal Configuration**: ต้องการการตั้งค่าน้อย สามารถเริ่มต้นพัฒนาได้อย่างรวดเร็ว
- **TypeScript Support**: รองรับ TypeScript เพื่อการพัฒนาที่มีความปลอดภัยยิ่งขึ้น

#### 1.3 เปรียบเทียบกับเฟรมเวิร์กอื่น

| คุณสมบัติ | Bolt | Express.js | Next.js | Ruby on Rails |
|----------|------|------------|---------|---------------|
| ภาษา | JavaScript/TypeScript | JavaScript | JavaScript/TypeScript | Ruby |
| Front-end & Back-end | ✅ (รวมในเฟรมเวิร์กเดียว) | ❌ (เฉพาะ back-end) | ✅ (React) | ✅ |
| การตั้งค่า | น้อย | ปานกลาง | ปานกลาง | มาก |
| ความเร็วในการพัฒนา | เร็วมาก | เร็ว | เร็ว | เร็วมาก |
| การรองรับ API | ดีเยี่ยม | ดีเยี่ยม | ดี | ดี |
| ชุมชนและระบบนิเวศ | กำลังเติบโต | ใหญ่มาก | ใหญ่มาก | ใหญ่ |

### 2. การติดตั้งและตั้งค่า Bolt Framework

#### 2.1 ข้อกำหนดเบื้องต้น

- Node.js (เวอร์ชัน 14.x หรือสูงกว่า)
- npm หรือ yarn
- ความรู้พื้นฐานเกี่ยวกับ JavaScript/TypeScript
- เข้าใจพื้นฐานของ HTTP และการพัฒนาเว็บ

#### 2.2 การติดตั้ง Bolt CLI

```bash
# ติดตั้ง Bolt CLI แบบ global
npm install -g @boltframework/cli

# หรือใช้ yarn
yarn global add @boltframework/cli
```

#### 2.3 การสร้างโปรเจกต์ใหม่

```bash
# สร้างโปรเจกต์ใหม่
bolt create my-app
cd my-app

# ติดตั้ง dependencies
npm install
# หรือ
yarn
```

#### 2.4 โครงสร้างไฟล์พื้นฐาน

โครงสร้างโปรเจกต์ Bolt มีลักษณะดังนี้:

```
my-app/
├── .bolt/                # ไฟล์การตั้งค่าของ Bolt
├── node_modules/         # ไลบรารี node
├── public/               # ไฟล์ static เช่น รูปภาพ, CSS, JavaScript
├── src/
│   ├── controllers/      # Controller classes
│   ├── models/           # Data models
│   ├── views/            # Templates/Views
│   ├── routes/           # Route definitions
│   ├── services/         # Business logic
│   ├── middleware/       # Custom middleware
│   └── app.js            # หรือ app.ts (entry point)
├── tests/                # ไฟล์ทดสอบ
├── .env                  # ตัวแปรสภาพแวดล้อม
├── .gitignore
├── package.json
├── tsconfig.json         # (ถ้าใช้ TypeScript)
└── README.md
```

#### 2.5 การรันโปรเจกต์

```bash
# รันในโหมด development
npm run dev
# หรือ
yarn dev

# สร้าง production build
npm run build
# หรือ
yarn build

# รันในโหมด production
npm start
# หรือ
yarn start
```

### 3. การพัฒนา Back-end ด้วย Bolt

#### 3.1 การกำหนดเส้นทาง (Routing)

ใน Bolt เราสามารถกำหนดเส้นทางได้ง่ายๆ ดังนี้:

```javascript
// src/routes/index.js
const { Router } = require('@boltframework/core');
const HomeController = require('../controllers/HomeController');

const router = new Router();

// ใช้งานแบบ function
router.get('/', (req, res) => {
  res.send('Hello World!');
});

// ใช้งานแบบ controller method
router.get('/welcome', HomeController.welcome);

// เส้นทาง API
router.post('/api/data', HomeController.createData);
router.get('/api/data', HomeController.getData);
router.put('/api/data/:id', HomeController.updateData);
router.delete('/api/data/:id', HomeController.deleteData);

module.exports = router;
```

#### 3.2 การสร้าง Controller

Controller ใน Bolt ช่วยจัดการตรรกะการทำงานและจัดการ request/response:

```javascript
// src/controllers/HomeController.js
class HomeController {
  // แสดงหน้า welcome
  static welcome(req, res) {
    res.render('welcome', {
      title: 'Welcome to Bolt',
      message: 'Getting started with Bolt Framework'
    });
  }

  // สร้างข้อมูล
  static async createData(req, res) {
    try {
      const data = req.body;
      // ทำการบันทึกข้อมูล
      const result = await DataService.create(data);
      res.status(201).json(result);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  }

  // ดึงข้อมูล
  static async getData(req, res) {
    try {
      const items = await DataService.getAll();
      res.json(items);
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  }

  // อัปเดตข้อมูล
  static async updateData(req, res) {
    try {
      const { id } = req.params;
      const data = req.body;
      const result = await DataService.update(id, data);
      res.json(result);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  }

  // ลบข้อมูล
  static async deleteData(req, res) {
    try {
      const { id } = req.params;
      await DataService.delete(id);
      res.status(204).end();
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  }
}

module.exports = HomeController;
```

#### 3.3 Middleware

Middleware ใน Bolt ช่วยจัดการกระบวนการทำงานระหว่าง request และ response:

```javascript
// src/middleware/authMiddleware.js
module.exports = function authMiddleware(req, res, next) {
  // ตรวจสอบการยืนยันตัวตน
  const token = req.headers.authorization;
  
  if (!token) {
    return res.status(401).json({ message: 'Authentication required' });
  }
  
  try {
    // ตรวจสอบ token (ตัวอย่าง)
    const decoded = verifyToken(token);
    req.user = decoded;
    next();
  } catch (error) {
    res.status(401).json({ message: 'Invalid token' });
  }
};

// การใช้งาน middleware ในไฟล์ routes
router.get('/protected', authMiddleware, Controller.protectedRoute);
```

#### 3.4 การเชื่อมต่อกับฐานข้อมูล

Bolt สามารถเชื่อมต่อกับฐานข้อมูลได้หลากหลาย เช่น MongoDB, MySQL, PostgreSQL หรืออื่นๆ:

```javascript
// src/services/database.js
const mongoose = require('mongoose');
const { config } = require('@boltframework/core');

// ใช้การตั้งค่าจาก .env
const connectionString = config.get('MONGODB_URI');

async function connectDatabase() {
  try {
    await mongoose.connect(connectionString, {
      useNewUrlParser: true,
      useUnifiedTopology: true
    });
    console.log('Connected to MongoDB');
  } catch (error) {
    console.error('Database connection error:', error);
    process.exit(1);
  }
}

module.exports = { connectDatabase };

// เรียกใช้ในไฟล์ app.js
const { connectDatabase } = require('./services/database');
connectDatabase();
```

#### 3.5 การสร้าง Model

Model ช่วยในการจัดการโครงสร้างข้อมูลและการทำงานกับฐานข้อมูล:

```javascript
// src/models/User.js
const mongoose = require('mongoose');
const bcrypt = require('bcrypt');

const userSchema = new mongoose.Schema({
  username: {
    type: String,
    required: true,
    unique: true
  },
  email: {
    type: String,
    required: true,
    unique: true
  },
  password: {
    type: String,
    required: true
  },
  createdAt: {
    type: Date,
    default: Date.now
  }
});

// Method สำหรับการเข้ารหัส password
userSchema.pre('save', async function(next) {
  if (this.isModified('password')) {
    this.password = await bcrypt.hash(this.password, 10);
  }
  next();
});

// Method สำหรับการตรวจสอบ password
userSchema.methods.comparePassword = async function(candidatePassword) {
  return bcrypt.compare(candidatePassword, this.password);
};

const User = mongoose.model('User', userSchema);

module.exports = User;
```

### 4. การพัฒนา Front-end ด้วย Bolt

#### 4.1 การใช้งาน Template Engine

Bolt สนับสนุนหลาย template engine เช่น EJS, Pug, Handlebars ฯลฯ:

```javascript
// การตั้งค่า template engine ใน app.js
const { Bolt } = require('@boltframework/core');
const app = new Bolt();

app.setViewEngine('ejs');
app.setViewsDirectory('src/views');
```

ตัวอย่างไฟล์ EJS:

```html
<!-- src/views/welcome.ejs -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title><%= title %></title>
  <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
  <header>
    <h1><%= title %></h1>
  </header>
  
  <main>
    <p><%= message %></p>
    
    <% if (user) { %>
      <p>Welcome back, <%= user.name %>!</p>
    <% } else { %>
      <p>Please log in to continue.</p>
    <% } %>
  </main>
  
  <footer>
    <p>&copy; <%= new Date().getFullYear() %> My Bolt App</p>
  </footer>
  
  <script src="/js/script.js"></script>
</body>
</html>
```

#### 4.2 การใช้งานกับ Modern Front-end Frameworks

Bolt สามารถทำงานร่วมกับ React, Vue.js หรือ Angular ได้:

**React Example:**

```javascript
// การตั้งค่าใน app.js
const { Bolt } = require('@boltframework/core');
const app = new Bolt();

// ตั้งค่า static directory สำหรับ React build
app.setStaticDirectory('build');

// API routes
app.use('/api', apiRoutes);

// Catch-all route สำหรับ SPA
app.get('*', (req, res) => {
  res.sendFile('index.html', { root: 'build' });
});
```

**API Service ที่ใช้ในหน้า React:**

```javascript
// src/services/ApiService.js
import axios from 'axios';

const API_URL = '/api';

export const ApiService = {
  async getData() {
    const response = await axios.get(`${API_URL}/data`);
    return response.data;
  },
  
  async createData(data) {
    const response = await axios.post(`${API_URL}/data`, data);
    return response.data;
  },
  
  async updateData(id, data) {
    const response = await axios.put(`${API_URL}/data/${id}`, data);
    return response.data;
  },
  
  async deleteData(id) {
    await axios.delete(`${API_URL}/data/${id}`);
  }
};
```

#### 4.3 การจัดการ Static Assets

Bolt จัดการไฟล์ static เช่น CSS, JavaScript, รูปภาพได้อย่างง่ายดาย:

```javascript
// การตั้งค่าใน app.js
const { Bolt } = require('@boltframework/core');
const app = new Bolt();

// กำหนด directory สำหรับไฟล์ static
app.setStaticDirectory('public');
```

โครงสร้างไฟล์ static:

```
public/
├── css/
│   └── styles.css
├── js/
│   └── script.js
├── images/
│   └── logo.png
└── favicon.ico
```

การเรียกใช้ในไฟล์ template:

```html
<link rel="stylesheet" href="/css/styles.css">
<script src="/js/script.js"></script>
<img src="/images/logo.png" alt="Logo">
```

### 5. การทำงานกับ API

#### 5.1 การสร้าง RESTful API

```javascript
// src/routes/api.js
const { Router } = require('@boltframework/core');
const ProductController = require('../controllers/ProductController');
const authMiddleware = require('../middleware/authMiddleware');

const router = new Router();

// Public API endpoints
router.get('/products', ProductController.getAllProducts);
router.get('/products/:id', ProductController.getProductById);

// Protected API endpoints
router.post('/products', authMiddleware, ProductController.createProduct);
router.put('/products/:id', authMiddleware, ProductController.updateProduct);
router.delete('/products/:id', authMiddleware, ProductController.deleteProduct);

module.exports = router;

// นำไปใช้ในไฟล์ app.js
app.use('/api', require('./routes/api'));
```

#### 5.2 การเชื่อมต่อกับ External API

```javascript
// src/services/ExternalApiService.js
const axios = require('axios');
const { config } = require('@boltframework/core');

class ExternalApiService {
  constructor() {
    this.apiKey = config.get('EXTERNAL_API_KEY');
    this.baseURL = config.get('EXTERNAL_API_URL');
    
    this.client = axios.create({
      baseURL: this.baseURL,
      headers: {
        'Authorization': `Bearer ${this.apiKey}`,
        'Content-Type': 'application/json'
      }
    });
  }
  
  async fetchData(endpoint, params = {}) {
    try {
      const response = await this.client.get(endpoint, { params });
      return response.data;
    } catch (error) {
      console.error(`Error fetching data from ${endpoint}:`, error.message);
      throw error;
    }
  }
  
  async postData(endpoint, data = {}) {
    try {
      const response = await this.client.post(endpoint, data);
      return response.data;
    } catch (error) {
      console.error(`Error posting data to ${endpoint}:`, error.message);
      throw error;
    }
  }
}

module.exports = new ExternalApiService();
```

การใช้งาน External API Service:

```javascript
// ใน controller
const ExternalApiService = require('../services/ExternalApiService');

class WeatherController {
  static async getWeather(req, res) {
    try {
      const { city } = req.query;
      const weatherData = await ExternalApiService.fetchData('/weather', { city });
      res.json(weatherData);
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  }
}
```

### 6. การ Deploy Bolt Application

#### 6.1 การเตรียมแอปพลิเคชันสำหรับ Production

```bash
# สร้าง production build
npm run build
# หรือ
yarn build

# ตรวจสอบ build
ls -la dist/
```

กำหนดตัวแปรสภาพแวดล้อมสำหรับ production ใน `.env.production`:

```
NODE_ENV=production
PORT=8080
MONGODB_URI=mongodb://production-db-server:27017/myapp
SESSION_SECRET=your-strong-production-secret
```

## การติดตั้ง Bolt Framework ด้วย Docker Compose

แทนที่จะใช้ PM2 และ Nginx ในการดูแลแอปพลิเคชัน Bolt Framework เราสามารถใช้ Docker Compose ได้เพื่อความสะดวกและง่ายในการจัดการ:

```yaml
version: '3.8'

services:
  bolt-app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: bolt-app
    restart: always
    environment:
      - NODE_ENV=production
      - PORT=8080
      - MONGODB_URI=mongodb://mongo:27017/boltapp
    ports:
      - "8080:8080"
    depends_on:
      - mongo
    volumes:
      - ./:/app
      - /app/node_modules

  mongo:
    image: mongo:4.4
    container_name: bolt-mongodb
    restart: always
    volumes:
      - mongo-data:/data/db
    ports:
      - "27017:27017"

  nginx:
    image: nginx:alpine
    container_name: bolt-nginx
    restart: always
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/conf.d:/etc/nginx/conf.d
      - ./nginx/ssl:/etc/nginx/ssl
      - ./nginx/www:/var/www/html
    depends_on:
      - bolt-app

volumes:
  mongo-data:
```

### การตั้งค่า Nginx (สร้างในโฟลเดอร์ nginx/conf.d/default.conf)

```nginx
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;

    location / {
        proxy_pass http://bolt-app:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

### Dockerfile สำหรับแอป Bolt

```dockerfile
FROM node:16-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install --production

COPY . .

EXPOSE 8080

CMD ["node", "dist/app.js"]
```

### การเริ่มใช้งาน

```bash
# สร้างและเริ่มต้นคอนเทนเนอร์ทั้งหมด
docker-compose up -d

# ดูล็อก
docker-compose logs -f

# หยุดการทำงาน
docker-compose down
```

### 7. Testing และ Debugging ใน Bolt

#### 7.1 Unit Testing

```javascript
// tests/controllers/HomeController.test.js
const { expect } = require('chai');
const sinon = require('sinon');
const HomeController = require('../../src/controllers/HomeController');
const DataService = require('../../src/services/DataService');

describe('HomeController', () => {
  describe('getData', () => {
    it('should return all data items', async () => {
      // Arrange
      const mockData = [{ id: 1, name: 'Test' }];
      const req = {};
      const res = {
        json: sinon.spy()
      };
      sinon.stub(DataService, 'getAll').resolves(mockData);
      
      // Act
      await HomeController.getData(req, res);
      
      // Assert
      expect(res.json.calledOnceWith(mockData)).to.be.true;
      
      // Cleanup
      DataService.getAll.restore();
    });
    
    it('should handle errors', async () => {
      // Arrange
      const req = {};
      const res = {
        status: sinon.stub().returnsThis(),
        json: sinon.spy()
      };
      sinon.stub(DataService, 'getAll').rejects(new Error('Database error'));
      
      // Act
      await HomeController.getData(req, res);
      
      // Assert
      expect(res.status.calledOnceWith(500)).to.be.true;
      expect(res.json.calledOnce).to.be.true;
      expect(res.json.args[0][0]).to.have.property('error');
      
      // Cleanup
      DataService.getAll.restore();
    });
  });
});
```

#### 7.2 Integration Testing

```javascript
// tests/integration/api.test.js
const request = require('supertest');
const { expect } = require('chai');
const { Bolt } = require('@boltframework/core');
const app = new Bolt();

// ตั้งค่า app ก่อนเริ่ม test
before(async () => {
  // โหลด routes และ setup app
  app.use('/api', require('../../src/routes/api'));
});

describe('API Routes', () => {
  describe('GET /api/products', () => {
    it('should return all products', async () => {
      const res = await request(app.server)
        .get('/api/products')
        .expect('Content-Type', /json/)
        .expect(200);
        
      expect(res.body).to.be.an('array');
    });
  });
  
  describe('POST /api/products', () => {
    it('should require authentication', async () => {
      const res = await request(app.server)
        .post('/api/products')
        .send({ name: 'New Product', price: 10 })
        .expect('Content-Type', /json/)
        .expect(401);
        
      expect(res.body).to.have.property('message');
      expect(res.body.message).to.include('Authentication');
    });
  });
});
```

#### 7.3 Debugging และการเชื่อมโยงกับเครื่องมืออื่น

Bolt มีเครื่องมือสำหรับการ debug ที่ช่วยให้การค้นหาและแก้ไขปัญหาทำได้ง่ายขึ้น และสามารถทำงานร่วมกับเครื่องมือที่เราได้เรียนรู้จากเซสชันอื่น เช่น การเชื่อมต่อกับ OpenAI API (เซสชัน 5) หรือการทำงานร่วมกับ n8n (เซสชัน 2):

```javascript
// การใช้ debug logger
const { Logger } = require('@boltframework/core');
const logger = new Logger('app:component');

function someFunction() {
  logger.debug('Debug message');
  logger.info('Info message');
  logger.warn('Warning message');
  logger.error('Error message', new Error('Something went wrong'));
}
```

การตั้งค่าระดับ log:

```javascript
// ใน .env
LOG_LEVEL=debug

// ใน app.js
const { configureLogging } = require('@boltframework/core');
configureLogging({ level: process.env.LOG_LEVEL || 'info' });
```

## 🛠️ Workshop: สร้าง API backend สำหรับ Todo Application ด้วย Bolt

### สิ่งที่ต้องมี

1. Node.js (เวอร์ชัน 14.x หรือสูงกว่า)
2. npm หรือ yarn
3. MongoDB (ติดตั้งในเครื่องหรือใช้บริการ cloud)
4. IDE หรือ text editor (เช่น VS Code)

### ขั้นตอน

1. **ตั้งค่าโปรเจกต์**

```bash
# สร้างโปรเจกต์ใหม่
bolt create todo-api
cd todo-api

# ติดตั้ง dependencies
npm install
```

2. **ตั้งค่าฐานข้อมูล MongoDB**

```bash
# ติดตั้ง mongoose
npm install mongoose
```

สร้างไฟล์ `.env`:

```
PORT=3000
MONGODB_URI=mongodb://localhost:27017/todo-app
```

3. **สร้าง Database Service**

```javascript
// src/services/database.js
const mongoose = require('mongoose');
const { config } = require('@boltframework/core');

const connectionString = config.get('MONGODB_URI');

async function connectDatabase() {
  try {
    await mongoose.connect(connectionString, {
      useNewUrlParser: true,
      useUnifiedTopology: true
    });
    console.log('Connected to MongoDB');
  } catch (error) {
    console.error('Database connection error:', error);
    process.exit(1);
  }
}

module.exports = { connectDatabase };
```

4. **สร้าง Todo Model**

```javascript
// src/models/Todo.js
const mongoose = require('mongoose');

const todoSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true,
    trim: true
  },
  description: {
    type: String,
    trim: true
  },
  completed: {
    type: Boolean,
    default: false
  },
  priority: {
    type: String,
    enum: ['low', 'medium', 'high'],
    default: 'medium'
  },
  dueDate: {
    type: Date
  },
  createdAt: {
    type: Date,
    default: Date.now
  },
  updatedAt: {
    type: Date,
    default: Date.now
  }
});

// อัปเดต updatedAt ทุกครั้งที่มีการแก้ไข
todoSchema.pre('save', function(next) {
  this.updatedAt = Date.now();
  next();
});

const Todo = mongoose.model('Todo', todoSchema);

module.exports = Todo;
```

5. **สร้าง Todo Service**

```javascript
// src/services/TodoService.js
const Todo = require('../models/Todo');

class TodoService {
  // ดึงรายการทั้งหมด
  static async getAll(filters = {}) {
    return Todo.find(filters).sort({ createdAt: -1 });
  }
  
  // ดึงรายการโดย ID
  static async getById(id) {
    return Todo.findById(id);
  }
  
  // สร้างรายการใหม่
  static async create(todoData) {
    const todo = new Todo(todoData);
    return todo.save();
  }
  
  // อัปเดตรายการ
  static async update(id, todoData) {
    return Todo.findByIdAndUpdate(
      id,
      { ...todoData, updatedAt: Date.now() },
      { new: true, runValidators: true }
    );
  }
  
  // ลบรายการ
  static async delete(id) {
    return Todo.findByIdAndDelete(id);
  }
  
  // เปลี่ยนสถานะ completed
  static async toggleCompleted(id) {
    const todo = await Todo.findById(id);
    if (!todo) return null;
    
    todo.completed = !todo.completed;
    todo.updatedAt = Date.now();
    return todo.save();
  }
}

module.exports = TodoService;
```

6. **สร้าง Todo Controller**

```javascript
// src/controllers/TodoController.js
const TodoService = require('../services/TodoService');

class TodoController {
  // ดึงรายการทั้งหมด
  static async getAllTodos(req, res) {
    try {
      const filters = {};
      
      // กรองตาม query parameters
      if (req.query.completed) {
        filters.completed = req.query.completed === 'true';
      }
      
      if (req.query.priority) {
        filters.priority = req.query.priority;
      }
      
      const todos = await TodoService.getAll(filters);
      res.json(todos);
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  }
  
  // ดึงรายการโดย ID
  static async getTodoById(req, res) {
    try {
      const { id } = req.params;
      const todo = await TodoService.getById(id);
      
      if (!todo) {
        return res.status(404).json({ message: 'Todo not found' });
      }
      
      res.json(todo);
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  }
  
  // สร้างรายการใหม่
  static async createTodo(req, res) {
    try {
      const todoData = req.body;
      const todo = await TodoService.create(todoData);
      res.status(201).json(todo);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  }
  
  // อัปเดตรายการ
  static async updateTodo(req, res) {
    try {
      const { id } = req.params;
      const todoData = req.body;
      const todo = await TodoService.update(id, todoData);
      
      if (!todo) {
        return res.status(404).json({ message: 'Todo not found' });
      }
      
      res.json(todo);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  }
  
  // ลบรายการ
  static async deleteTodo(req, res) {
    try {
      const { id } = req.params;
      const todo = await TodoService.delete(id);
      
      if (!todo) {
        return res.status(404).json({ message: 'Todo not found' });
      }
      
      res.status(204).end();
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  }
  
  // เปลี่ยนสถานะ completed
  static async toggleTodoCompleted(req, res) {
    try {
      const { id } = req.params;
      const todo = await TodoService.toggleCompleted(id);
      
      if (!todo) {
        return res.status(404).json({ message: 'Todo not found' });
      }
      
      res.json(todo);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  }
}

module.exports = TodoController;
```

7. **สร้าง API Routes**

```javascript
// src/routes/api.js
const { Router } = require('@boltframework/core');
const TodoController = require('../controllers/TodoController');

const router = new Router();

// Todo routes
router.get('/todos', TodoController.getAllTodos);
router.get('/todos/:id', TodoController.getTodoById);
router.post('/todos', TodoController.createTodo);
router.put('/todos/:id', TodoController.updateTodo);
router.delete('/todos/:id', TodoController.deleteTodo);
router.patch('/todos/:id/toggle', TodoController.toggleTodoCompleted);

module.exports = router;
```

8. **ตั้งค่า App**

```javascript
// src/app.js
const { Bolt } = require('@boltframework/core');
const { connectDatabase } = require('./services/database');
const apiRoutes = require('./routes/api');

// เริ่มต้นแอปพลิเคชัน
const app = new Bolt();

// เชื่อมต่อฐานข้อมูล
connectDatabase();

// Middleware
app.use(Bolt.json()); // สำหรับ parsing JSON
app.use(Bolt.cors()); // เปิดใช้งาน CORS

// Routes
app.use('/api', apiRoutes);

// Error handling
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({
    message: 'Something went wrong!',
    error: process.env.NODE_ENV === 'production' ? {} : err.message
  });
});

// เริ่มต้น server
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

9. **ทดสอบ API**

รัน application:

```bash
npm run dev
```

ทดสอบด้วย curl หรือ Postman:

```bash
# สร้างรายการใหม่
curl -X POST http://localhost:3000/api/todos \
  -H 'Content-Type: application/json' \
  -d '{"title": "Learn Bolt Framework", "priority": "high", "description": "Complete the tutorial"}'

# ดึงรายการทั้งหมด
curl http://localhost:3000/api/todos

# ดึงรายการตาม ID
curl http://localhost:3000/api/todos/TASK_ID

# อัปเดตรายการ
curl -X PUT http://localhost:3000/api/todos/TASK_ID \
  -H 'Content-Type: application/json' \
  -d '{"title": "Master Bolt Framework", "completed": true}'

# เปลี่ยนสถานะ completed
curl -X PATCH http://localhost:3000/api/todos/TASK_ID/toggle

# ลบรายการ
curl -X DELETE http://localhost:3000/api/todos/TASK_ID
```

10. **การ Deploy บน Server**

เตรียม application สำหรับ production:

```bash
npm run build
```

สร้างไฟล์ `ecosystem.config.js` สำหรับ PM2:

```javascript
module.exports = {
  apps: [{
    name: 'todo-api',
    script: './dist/app.js',
    instances: 'max',
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'production',
      PORT: 8080
    }
  }]
};
```

Deploy:

```bash
# Copy files to server
scp -r dist package.json package-lock.json ecosystem.config.js user@your-server:/path/to/app

# SSH to server
ssh user@your-server

# Navigate to app directory
cd /path/to/app

# Install dependencies
npm install --production

# Start with PM2
pm2 start ecosystem.config.js
```

## 📚 แหล่งข้อมูลเพิ่มเติม

- [Bolt Framework Documentation](https://boltframework.com/docs)
- [Bolt Framework GitHub Repository](https://github.com/boltframework/bolt)
- [Node.js Documentation](https://nodejs.org/en/docs/)
- [MongoDB Documentation](https://docs.mongodb.com/)
- [Mongoose Documentation](https://mongoosejs.com/docs/guide.html)
- [PM2 Documentation](https://pm2.keymetrics.io/docs/usage/quick-start/)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [Docker Documentation](https://docs.docker.com/)

## 📌 สรุป

Bolt Framework เป็นเครื่องมือที่ทรงพลังสำหรับการพัฒนาเว็บแอปพลิเคชันแบบเต็มรูปแบบ โดยรวมทั้ง front-end และ back-end ไว้ในเฟรมเวิร์กเดียว ช่วยให้นักพัฒนาสามารถสร้างแอปพลิเคชันได้อย่างรวดเร็วและมีประสิทธิภาพ ด้วยความสามารถในการรองรับทั้ง API, template engines, และการเชื่อมต่อกับฐานข้อมูลหลากหลายชนิด ทำให้ Bolt เป็นตัวเลือกที่ดีสำหรับโปรเจกต์ขนาดเล็กถึงขนาดกลาง

การออกแบบแบบโมดูลาร์ของ Bolt ช่วยให้โค้ดมีความเป็นระเบียบและง่ายต่อการบำรุงรักษา ในขณะที่เครื่องมือและฟังก์ชันที่มีมาให้ช่วยลดเวลาในการพัฒนาลงอย่างมาก นอกจากนี้ Bolt ยังมีความยืดหยุ่นสูง สามารถปรับแต่งได้ตามความต้องการ และรองรับการ scale ในสภาพแวดล้อมการผลิตจริง

ด้วยการเรียนรู้และฝึกฝนใน workshop นี้ คุณได้เรียนรู้พื้นฐานของ Bolt Framework และสามารถนำไปประยุกต์ใช้ในการพัฒนาโปรเจกต์จริงต่อไปได้อย่างมีประสิทธิภาพ
