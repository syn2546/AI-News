# Deploy หมวด AI Daily (`ai/`)

หมวดนี้ generate โดย skill `ai-daily` (อยู่ที่ `~/.claude/skills/ai-daily/SKILL.md`) — สั่งรันด้วย `/ai-daily` ใน Claude Code แล้ว skill จัดการข้อ 1–2 ให้เอง เหลือแค่ตรวจและ push

## ไฟล์ที่ต้องเขียน (ต่อ 1 วัน)

| ไฟล์ | เนื้อหา |
|---|---|
| `ai/ai-daily-YYYY-MM-DD.html` | HTML brief — dark theme, 10 ข่าว AI + 5 trending repos, self-contained |
| `ai/ai-daily-YYYY-MM-DD.md` | markdown copy เนื้อหาเดียวกัน |

ข้อกำหนดของไฟล์ HTML:
- ใช้ CSS token เดิมจากไฟล์วันล่าสุดใน `ai/` เพื่อให้หน้าตาเหมือนกันทุกวัน
- ลิงก์กลับ hub: `href="../index.html"`
- ทุกข่าวและทุก repo ต้องมี reference link (`target="_blank" rel="noopener"`)

## ลงทะเบียนใน manifest

Append 1 entry ลง `window.HUB_DATA.ai` ในไฟล์ `data/ai.js`:

```js
{ date:"YYYY-MM-DD", file:"ai/ai-daily-YYYY-MM-DD.html", topNews:"<พาดหัวเด่น 1-2 ข่าวแรก>", newsCount:10, repoCount:5 },
```

หมวดนี้เป็นหมวดเดียวที่มี `repoCount` (มี trending repos)

## ตรวจ + Push

```bash
# ตรวจ syntax manifest
node --check /Users/syn/AI-News/data/ai.js

# เปิดดูใน tab AI ว่า card วันใหม่ขึ้นและกดเปิดได้
open /Users/syn/AI-News/index.html

# push
cd /Users/syn/AI-News && ./deploy.sh
```

หน้า live: `https://syn2546.github.io/AI-News/#ai`
