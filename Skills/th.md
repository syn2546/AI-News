# Deploy หมวด TH News (`th/`)

ข่าวไทยรายวัน — ยังไม่มี skill generate อัตโนมัติ (โครงพร้อมแล้ว รอสร้าง skill `th-daily`)
เขียนเองหรือให้ agent เขียนตามข้อกำหนดนี้ได้เลย

## ไฟล์ที่ต้องเขียน (ต่อ 1 วัน)

| ไฟล์ | เนื้อหา |
|---|---|
| `th/th-daily-YYYY-MM-DD.html` | HTML brief — 10 ข่าวเด่นประเทศไทยของวัน, self-contained |
| `th/th-daily-YYYY-MM-DD.md` | markdown copy เนื้อหาเดียวกัน |

ข้อกำหนดของไฟล์ HTML:
- วันแรก: สร้าง CSS token system ของหมวดนี้เอง (แนะนำโทนที่ต่างจาก AI Daily ให้รู้ว่าอยู่คนละหมวด) — วันถัดไปใช้ token เดิมจากไฟล์ล่าสุดใน `th/`
- ลิงก์กลับ hub: `href="../index.html"`
- ทุกข่าวต้องมี reference link ไปแหล่งข่าวจริง (`target="_blank" rel="noopener"`)

## ลงทะเบียนใน manifest

Append 1 entry ลง `window.HUB_DATA.th` ในไฟล์ `data/th.js`:

```js
{ date:"YYYY-MM-DD", file:"th/th-daily-YYYY-MM-DD.html", topNews:"<พาดหัวเด่นของวัน>", newsCount:10 },
```

**ไม่ต้องใส่ `repoCount`** — chip บน hub จะแสดงแค่จำนวนข่าวให้เอง

## ตรวจ + Push

```bash
node --check /Users/syn/AI-News/data/th.js
open /Users/syn/AI-News/index.html      # ดู tab TH News
cd /Users/syn/AI-News && ./deploy.sh
```

หน้า live: `https://syn2546.github.io/AI-News/#th`
