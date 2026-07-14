# Deploy หมวด TH News (`th/`)

หมวดนี้ generate โดย skill **`th-brief`** (ติดตั้งที่ `~/.claude/skills/th-brief/SKILL.md`, สำเนาอยู่ที่ `Skills/th-brief/SKILL.md`) — สั่งรันด้วย `/th-brief` ใน Claude Code
เนื้อหา: Thailand Morning Brief — 3 market indicators (SET, USD/THB, Gold) + 5 ข่าวธุรกิจ/เศรษฐกิจ + stakeholder statements + corporate earnings

## ไฟล์ที่ต้องเขียน (ต่อ 1 วัน)

| ไฟล์ | เนื้อหา |
|---|---|
| `th/brief-YYYY-MM-DD.html` | HTML brief — warm-cream editorial theme (primary: Deep Emerald `#0F4C3A`), self-contained |
| `th/brief-YYYY-MM-DD.md` | markdown copy เนื้อหาเดียวกัน |

ข้อกำหนดของไฟล์ HTML:
- ใช้ CSS token ตามที่กำหนดใน SKILL.md (โทนครีม/เขียวมรกต — ต่างจาก AI Daily ที่เป็น dark theme)
- ถ้ามีลิงก์กลับ hub ต้องเป็น `href="../index.html"`
- ทุกข่าว/statement/earnings ต้องมี reference link (`target="_blank" rel="noopener"`)

## ลงทะเบียนใน manifest

Append 1 entry ลง `window.HUB_DATA.th` ในไฟล์ `data/th.js`:

```js
{ date:"YYYY-MM-DD", file:"th/brief-YYYY-MM-DD.html", topNews:"<พาดหัวเด่นของวัน>", newsCount:5 },
```

**ไม่ต้องใส่ `repoCount`** (field นั้นของ AI Daily เท่านั้น) — chip บน hub จะแสดงแค่จำนวนข่าว

## ตรวจ + Push

```bash
node --check /Users/syn/AI-News/data/th.js
open /Users/syn/AI-News/index.html      # ดู tab TH News ว่า card วันใหม่ขึ้นและกดเปิดได้
cd /Users/syn/AI-News && ./deploy.sh
```

หน้า live: `https://syn2546.github.io/AI-News/#th`
