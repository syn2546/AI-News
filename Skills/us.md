# Deploy หมวด US News (`us/`)

หมวดนี้ generate โดย skill **`us-brief`** (ติดตั้งที่ `~/.claude/skills/us-brief/SKILL.md`, สำเนาอยู่ที่ `Skills/us-brief/SKILL.md`) — สั่งรันด้วย `/us-brief` ใน Claude Code
เนื้อหา: US Market Brief — 4 market indicators (S&P 500, Nasdaq, Dow, 10-Yr Treasury) + 5 ข่าว macro/ธุรกิจ + stakeholder statements + corporate earnings

## ไฟล์ที่ต้องเขียน (ต่อ 1 วัน)

| ไฟล์ | เนื้อหา |
|---|---|
| `us/brief-YYYY-MM-DD.html` | HTML brief — warm-cream editorial theme (primary: Deep Navy `#1B365D`), self-contained |
| `us/brief-YYYY-MM-DD.md` | markdown copy เนื้อหาเดียวกัน |

ข้อกำหนดของไฟล์ HTML:
- ใช้ CSS token ตามที่กำหนดใน SKILL.md (โทนครีม/น้ำเงินเนวี — ต่างจาก TH ที่เป็นเขียวมรกต)
- ถ้ามีลิงก์กลับ hub ต้องเป็น `href="../index.html"`
- ทุกข่าว/statement/earnings ต้องมี reference link (`target="_blank" rel="noopener"`)

## ลงทะเบียนใน manifest

Append 1 entry ลง `window.HUB_DATA.us` ในไฟล์ `data/us.js`:

```js
{ date:"YYYY-MM-DD", file:"us/brief-YYYY-MM-DD.html", topNews:"<พาดหัวเด่นของวัน>", newsCount:5 },
```

**ไม่ต้องใส่ `repoCount`** (field นั้นของ AI Daily เท่านั้น) — chip บน hub จะแสดงแค่จำนวนข่าว

## ตรวจ + Push

```bash
node --check /Users/syn/AI-News/data/us.js
open /Users/syn/AI-News/index.html      # ดู tab US News ว่า card วันใหม่ขึ้นและกดเปิดได้
cd /Users/syn/AI-News && ./deploy.sh
```

หน้า live: `https://syn2546.github.io/AI-News/#us`
