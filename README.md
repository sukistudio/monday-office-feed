# monday-office-feed

ไฟล์ `feed.json` = สถานะทีม Monday สำหรับหน้า https://monday-office.vercel.app · เขียนโดย script บน Studio ทุก ≤60 วิ · ไม่มีข้อมูลส่วนตัว/ชื่อ repo private/path (ผ่าน gate เดียวกับหน้าเว็บ)

## มีอะไรใน `feed.json` (schemaVersion 3)

- `members[]` = สมาชิกทีม Monday เท่านั้น (ทีมอื่นไม่อยู่ในไฟล์นี้): `id` (session เช่น `11-tide`) · `status` (`working` · `idle` · `stuck` · `offline`) · `task` (`brief-<id>` + คำกริยาสั้นที่กำหนด) · `since` · `model` · `engine` · `brief` / `project` (ถ้ามี)
- **`engine`** (เพิ่ม 2026-10-17 · ยังเป็น schemaVersion 3): `claude` = oracle ของ Claude · `codex` = coder OMX/Codex (ชื่อ session ลงท้าย `-omx` เช่น `114-eddy-omx`) · coder Codex มี `model: "codex"` เสมอ (ไม่ระบุรุ่น)
- **`offline`** = อยู่ในรายชื่อทีมแต่ตอนนี้ไม่ได้เปิดอยู่ (ไม่ถูกตัดออกจาก `members`)
- สมาชิกใหม่ขึ้นใน feed เมื่อทีมเพิ่มชื่อในรายชื่อของทีมแล้ว · ก่อนนั้น feed ขึ้น `warnings: ["roster-missing:<id>"]`
- `events[]` · `projects[]` · `briefs[]` = metadata เท่านั้น (id · ชนิด · เวลา) ไม่มีข้อความจริง · `briefs[].title` = id ของ brief (`brief-20b`) เว้นแต่ทีมตั้งชื่อ public ให้เอง

```json
{ "schemaVersion": 3, "source": "live", "updatedAt": "2026-10-17T01:00:00+07:00",
  "members": [
    { "id": "11-tide", "status": "working", "task": "brief-20b · แจกงาน", "since": "…", "model": "fable", "engine": "claude" },
    { "id": "114-eddy-omx", "status": "idle", "task": "brief-22 · ส่งงาน DONE", "since": "…", "model": "codex", "engine": "codex" }
  ],
  "events": [ { "ts": "…", "kind": "done", "from": "114-eddy-omx", "to": "11-tide", "brief": "brief-22" } ],
  "briefs": [ { "id": "brief-20b", "title": "brief-20b", "…": "…" } ] }
```

reader ที่อ่าน v3 อยู่แล้วไม่ต้องแก้ — field ใหม่เป็นแบบเพิ่ม (ไม่รู้จัก `engine` ก็ข้ามได้)

🤖 Monday Oracle 🌙🐾 (AI) จาก [เปรม] → monday-oracle · แก้ส่วน engine/OMX โดย swell จาก [เปรม] → swell-oracle
