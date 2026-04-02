# 📧 Gmail & Calendar Skill for OpenClaw

> OpenClaw Skill สำหรับเชื่อมต่อ Gmail และ Calendar ผ่าน App Password (ไม่ต้องใช้ OAuth)

[![OpenClaw](https://img.shields.io/badge/OpenClaw-2026.4.1-blue)](https://github.com/openclaw/openclaw)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)
[![Author](https://img.shields.io/badge/Author-Mew-purple)](https://github.com/openclaw)

---

## 📋 สารบัญ

| เอกสาร | คำอธิบาย |
|---------|-----------|
| [คู่มือการติดตั้ง](docs/01-Setup-Guide.md) | ขั้นตอนการตั้งค่า App Password และติดตั้งสกิล |
| [คำอ้างอิงสกิล](docs/02-Skill-Reference.md) | API Reference และตัวอย่างการใช้งาน |

---

## 🎯 Features

| Feature | Status | คำอธิบาย |
|---------|--------|-----------|
| **Send Email (SMTP)** | ✅ | ส่ง email ผ่าน Gmail SMTP |
| **Read Email (IMAP)** | ✅ | อ่าน email ผ่าน IMAP |
| **Calendar Events (CalDAV)** | ✅ | ดู/สร้าง calendar events |
| **Delete Spam/Promotions** | ✅ | ลบ email ขยะอัตโนมัติ |

---

## ⚡ เริ่มต้นใช้งาน

### 1. สร้าง App Password

1. ไปที่ [myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords)
2. ต้องเปิด **2-Step Verification** ก่อน
3. สร้าง App Password 16 ตัวอักษร

### 2. ตั้งค่า Environment Variables

```bash
export GMAIL_EMAIL="your-email@gmail.com"
export GMAIL_APP_PASSWORD="xxxx xxxx xxxx xxxx"
```

### 3. ดูรายละเอียดการติดตั้ง

👉 **[อ่านคู่มือติดตั้งฉบับเต็ม](docs/01-Setup-Guide.md)**

---

## 📚 โครงสร้างไฟล์

```
openclaw-gmail-calendar-skill/
├── README.md                      ← สารบัญ (ไฟล์นี้)
├── LICENSE                        ← MIT License
└── docs/
    ├── 01-Setup-Guide.md          ← คู่มือการติดตั้ง
    └── 02-Skill-Reference.md      ← API Reference
```

---

## 🛠️ ข้อมูลทั่วไป

| รายการ | รายละเอียด |
|---------|-------------|
| **ผู้พัฒนา** | โจ้ พัฒนากร |
| **ผู้เขียนสกิล** | มิว (Mew AI) |
| **สัญญาอนุญาต** | MIT License |

---

## 🔗 ลิงก์ที่เกี่ยวข้อง

- [OpenClaw Documentation](https://docs.openclaw.ai)
- [Google App Passwords](https://myaccount.google.com/apppasswords)
- [OpenClaw Community](https://discord.com/invite/clawd)

---

*สกิลนี้เป็นส่วนหนึ่งของ OpenClaw Gmail-Calendar Skill — พัฒนาโดย โจ้ พัฒนากร | ผู้เขียน: มิว (Mew AI)* 💖
