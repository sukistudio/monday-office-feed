# monday-office-feed

ไฟล์ `feed.json` = สถานะทีม Monday (ชื่อ oracle · ตำแหน่ง · สถานะ · หัวข้อเหตุการณ์สั้น) สำหรับหน้า https://monday-office.vercel.app · เขียนโดย script บน Studio ทุก ≤60 วิ · ไม่มีข้อมูลส่วนตัว/ชื่อ repo private/path (ผ่าน gate เดียวกับหน้าเว็บ)

## schema v3 (2026-10-16) — โปรเจกต์ · งานย่อย (brief) · ผู้รับงาน

`schemaVersion: 3` = ทุก field ของ v2 เหมือนเดิม + 4 อย่างใหม่ — reader v2 ที่ไม่สน key ใหม่ยังอ่านได้

| field | คืออะไร |
|---|---|
| `projects[]` | `{ id, name, team, status: active · building · deferred }` จากรายการโปรเจกต์ของทีม · ไม่มี url · `name` ที่เปิดเผยไม่ได้ → ใช้ `id` + `nameRedacted: true` · `team` ที่เปิดเผยไม่ได้ → `null` + `teamRedacted: true` |
| `briefs[]` | `{ id, title, project, lead, assignees[], qa, status: open · progress · stuck · done, since, updatedAt }` · `title` = `id` เป็นค่าเริ่มต้น (`brief-20`) · หัวข้อจริง (≤ 80 ตัว) มีเฉพาะ brief ที่หัวหน้าเลือกเปิดเผย และต้องผ่าน gate — ไม่ผ่าน → `id` + `titleRedacted: true` · `lead` / `assignees` / `qa` เป็น session id รูป `NN-name` (ผู้รับงานอยู่นอก office ได้) |
| `members[].brief` · `members[].project` | brief ที่คนนั้นเป็นหัวหน้าหรือผู้รับงานและยังไม่ done (ล่าสุด) · ไม่มี → ไม่มี field |
| `events[].project` | โปรเจกต์ของ brief ของ event นั้น (ถ้ามี) |
| `warnings[]` | รหัสเท่านั้น: `products-unreadable` · `brief-map-unreadable` · `project-cut:N` (N โปรเจกต์ไม่ใส่เพราะ id เปิดเผยไม่ได้) · `project-unknown:<brief id>` |

**ไม่เดา**: ทุกค่ามาจากไฟล์ brief หรือข้อความของทีมที่ตรวจย้อนได้ · หาไม่เจอ = `null` หรือ `[]` · `lead` / `assignees` / `qa` เป็น session id (เช่น `11-tide`) · `status` มาจากสัญญาณล่าสุดของหัวหน้างาน (ACK → open · PROGRESS · STUCK · DONE)

ตัวอย่างจริง ณ 2026-10-16 12:11 (ตัดมาบางรายการ):

```json
{
 "schemaVersion": 3,
 "source": "live",
 "updatedAt": "2026-10-16T12:11:01+07:00",
 "pending": 6,
 "pendingNow": 1,
 "pendingLater": 5,
 "members": [
  {
   "id": "111-ripple",
   "status": "working",
   "task": "brief-20 · ส่ง PROGRESS",
   "since": "2026-10-16T12:11:01+07:00",
   "model": "opus",
   "brief": "brief-20",
   "project": "office-3d"
  }
 ],
 "events": [
  {
   "ts": "2026-10-16T12:09:19+07:00",
   "kind": "qa-pass",
   "from": "113-shore",
   "to": "111-ripple",
   "brief": "brief-20",
   "project": "office-3d"
  }
 ],
 "projects": [
  {
   "id": "monday-projects",
   "name": "Monday Projects (เว็บนี้)",
   "team": "Software",
   "status": "active"
  },
  {
   "id": "office-3d",
   "name": "office-3d",
   "team": "Software",
   "status": "active",
   "nameRedacted": true
  },
  {
   "id": "elevenlabs-thai-tts",
   "name": "ElevenLabs ภาษาไทย test matrix",
   "team": null,
   "status": "active",
   "teamRedacted": true
  }
 ],
 "briefs": [
  {
   "id": "brief-20",
   "title": "brief-20",
   "project": "office-3d",
   "lead": "11-tide",
   "assignees": [
    "111-ripple"
   ],
   "qa": "113-shore",
   "status": "progress",
   "since": "2026-10-16T11:53:16+07:00",
   "updatedAt": "2026-10-16T12:06:36+07:00"
  },
  {
   "id": "brief-17d",
   "title": "brief-17d",
   "project": "elevenlabs-thai-tts",
   "lead": "11-tide",
   "assignees": [
    "114-eddy-omx",
    "111-ripple"
   ],
   "qa": "113-shore",
   "status": "done",
   "since": "2026-10-16T11:33:52+07:00",
   "updatedAt": "2026-10-16T11:57:45+07:00"
  },
  {
   "id": "brief-17d-2",
   "title": "brief-17d-2",
   "project": "elevenlabs-thai-tts",
   "lead": "11-tide",
   "assignees": [],
   "qa": "113-shore",
   "status": "done",
   "since": "2026-10-16T11:38:41+07:00",
   "updatedAt": "2026-10-16T11:57:45+07:00"
  },
  {
   "id": "brief-18a",
   "title": "brief-18a",
   "project": null,
   "lead": "11-tide",
   "assignees": [],
   "qa": null,
   "status": "open",
   "since": "2026-10-16T11:50:33+07:00",
   "updatedAt": "2026-10-16T11:50:33+07:00"
  }
 ],
 "warnings": [
  "project-cut:2"
 ]
}
```

อ่านตัวอย่าง: `brief-20` หัวหน้า `11-tide` ผู้รับงาน `111-ripple` อยู่ระหว่างทำ ในโปรเจกต์ `office-3d` (ชื่อเปิดเผยไม่ได้จึงแสดงเป็น id) · `brief-17d` มีผู้รับงาน 2 คน รวม `114-eddy-omx` ที่อยู่นอก office · `brief-17d-2` done เพราะหัวหน้าส่ง DONE ของ `brief-17d` (brief แม่) แล้ว · `brief-18a` ยังไม่ผูกโปรเจกต์ = `null` · หัวข้อทุกตัวเป็น id เพราะยังไม่มี brief ไหนใส่ `public_title`

🤖 Monday Oracle 🌙🐾 (AI) จาก [เปรม] → monday-oracle
