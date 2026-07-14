# วิธี Deploy — AI-News Hub

Repo นี้คือ static site บน **GitHub Pages** (`https://syn2546.github.io/AI-News/`)
หน้าเดียว (`index.html`) มี 3 tab: **AI Daily · TH News · US News** — ข้อมูลแต่ละหมวดแยกเก็บคนละโฟลเดอร์

## โครงสร้าง repo

```
AI-News/
├── index.html          ← hub หน้าเดียว (ห้ามแก้ตอนเพิ่มข่าว)
├── deploy.sh           ← สคริปต์ push ขึ้น GitHub Pages
├── data/
│   ├── ai.js           ← manifest ของ AI Daily
│   ├── th.js           ← manifest ของ TH News
│   └── us.js           ← manifest ของ US News
├── ai/                 ← ไฟล์ digest รายวันของ AI (html + md)
├── th/                 ← ไฟล์ digest รายวันของ TH
├── us/                 ← ไฟล์ digest รายวันของ US
└── Skills/             ← คู่มือชุดนี้
```

## ขั้นตอน deploy (ทุกหมวดเหมือนกัน)

1. **เขียนไฟล์ digest** ลงโฟลเดอร์ของหมวดตัวเอง — ดูรายละเอียดใน `ai.md` / `th.md` / `us.md`
2. **ลงทะเบียนวันใหม่** — append **1 entry** ลง array ในไฟล์ `data/<หมวด>.js` (ห้ามแตะ `index.html`)
3. **ตรวจก่อน push** — เปิด `index.html` แบบ double-click แล้วดูว่า card วันใหม่ขึ้นใน tab ที่ถูกต้อง กดแล้วเปิดไฟล์ได้จริง
4. **Push ขึ้น GitHub Pages:**
   ```bash
   cd /Users/syn/AI-News && ./deploy.sh
   ```
   หรือ commit เองด้วยข้อความที่สื่อความหมายกว่า:
   ```bash
   cd /Users/syn/AI-News
   git add -A
   git commit -m "Add <หมวด> digest for YYYY-MM-DD"
   git push origin main
   ```
5. **ตรวจหน้า live** — รอ build ~30–60 วินาที แล้วเช็ค:
   ```bash
   curl -s -o /dev/null -w "%{http_code}\n" https://syn2546.github.io/AI-News/
   ```
   ต้องได้ `200` และเปิด `https://syn2546.github.io/AI-News/#<หมวด>` เห็นวันใหม่

## รูปแบบ entry ใน manifest (ทุกหมวดใช้ shape เดียวกัน)

```js
{ date:"YYYY-MM-DD", file:"<หมวด>/<หมวด>-daily-YYYY-MM-DD.html", topNews:"<พาดหัวเด่นของวัน>", newsCount:10, repoCount:5 },
```

- `file` ต้องมี prefix โฟลเดอร์ (เช่น `th/...`) เพราะ path อ้างอิงจาก hub ที่ root
- `repoCount` เป็น **optional** — ใส่เฉพาะ AI Daily; TH/US ไม่มี trending repos ให้ละไว้
- ตำแหน่งใน array ไม่สำคัญ — hub sort ตาม `date` ให้เอง (ใหม่สุดขึ้นก่อน)

## กติกาที่ห้ามพลาด

- **ห้ามแก้ `index.html`** ตอนเพิ่มข่าว — ทุกอย่างทำผ่าน `data/*.js` เท่านั้น
- ไฟล์ digest ทุกไฟล์ต้อง **self-contained** (inline CSS/JS) และลิงก์กลับ hub ด้วย `href="../index.html"` (ไฟล์อยู่ลึกกว่า hub หนึ่งชั้น)
- ทุกข่าวต้องมี **reference link** ที่กดได้จริง
- deep-link แต่ละ tab: `#ai` (default) · `#th` · `#us`
