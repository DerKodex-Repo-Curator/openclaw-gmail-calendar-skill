# 📧 OpenClaw Gmail & Calendar Skill

> สกิลสำหรับเชื่อมต่อ Gmail และ Google Calendar กับ OpenClaw โดยใช้ App Password

[![OpenClaw](https://img.shields.io/badge/OpenClaw-Personal%20AI%20Assistant-blue)](https://github.com/openclaw/openclaw)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

---

## ✨ คุณสมบัติ

- ✅ **ส่ง Email** - ส่ง email ผ่าน Gmail SMTP
- ✅ **อ่าน Email** - อ่าน email ผ่าน IMAP
- ✅ **Calendar** - ดู/สร้าง events ผ่าน CalDAV
- 🔒 **ปลอดภัย** - ใช้ App Password แทน OAuth
- 🚫 **ไม่ต้อง OAuth** - ไม่โดน Google บล็อก

---

## 📋 ความต้องการ

| รายการ | รายละเอียด |
|---------|-------------|
| OpenClaw | เวอร์ชันล่าสุด |
| Python | 3.8+ |
| บัญชี Gmail | ที่มี 2-Step Verification |
| App Password | 16 ตัวอักษร |

---

## 🚀 การติดตั้ง

### 1. สร้าง App Password

```bash
# 1. ไปที่ https://myaccount.google.com/apppasswords
# 2. เปิด 2-Step Verification ก่อน (ถ้ายังไม่เปิด)
# 3. สร้าง App Password สำหรับ "Mail"
# 4. บันทึกรหัส 16 ตัว (รูปแบบ: xxxx xxxx xxxx xxxx)
```

### 2. ติดตั้งสกิล

```bash
# คัดลอกไฟล์ไปยัง OpenClaw
cp -r gmail-calendar /app/skills/
```

### 3. ตั้งค่า Credentials

แก้ไขโค้ดเพื่อใส่ email และ app password ของคุณ:

```python
EMAIL = "your-email@gmail.com"
APP_PASSWORD = "xxxx xxxx xxxx xxxx"
```

---

## 📖 การใช้งาน

### ส่ง Email

```python
import smtplib
from email.message import EmailMessage

EMAIL = "your-email@gmail.com"
APP_PASSWORD = "xxxx xxxx xxxx xxxx"

msg = EmailMessage()
msg['From'] = EMAIL
msg['To'] = 'recipient@example.com'
msg['Subject'] = 'หัวข้อ'
msg.set_content('เนื้อหา')

with smtplib.SMTP('smtp.gmail.com', 587) as server:
    server.starttls()
    server.login(EMAIL, APP_PASSWORD)
    server.send_message(msg)
```

### อ่าน Calendar Events

```python
import urllib.request
import base64
import urllib.parse

EMAIL = "your-email@gmail.com"
APP_PASSWORD = "xxxx xxxx xxxx xxxx"

auth = base64.b64encode(f"{EMAIL}:{APP_PASSWORD}".encode()).decode()
url = f'https://www.google.com/calendar/dav/{urllib.parse.quote(EMAIL)}/events/'

req = urllib.request.Request(url, method='PROPFIND')
req.add_header('Authorization', f'Basic {auth}')
req.add_header('Depth', '1')

with urllib.request.urlopen(req) as response:
    data = response.read().decode()
```

### สร้าง Calendar Event

```python
from datetime import datetime, timedelta
import urllib.request
import base64
import urllib.parse

# สร้าง event...
```

ดูรายละเอียดเพิ่มเติมใน [GUIDE.md](GUIDE.md)

---

## ❌ สิ่งที่ทำไม่ได้

| Service | สถานะ |
|---------|--------|
| Google Drive | ❌ ต้องใช้ OAuth |
| Google Sheets | ❌ ต้องใช้ OAuth |
| Google Docs | ❌ ต้องใช้ OAuth |
| Google Contacts | ❌ ต้องใช้ OAuth |

---

## 🔐 ความปลอดภัย

- ✅ ใช้ App Password แทนรหัสผ่านหลัก
- ✅ ไม่จำเป็นต้องให้สิทธิ์ OAuth
- ✅ ปิด 2-Step Verification ไม่ได้ (ต้องเปิด)
- ⚠️ เก็บ App Password ให้ปลอดภัย!

---

## 📚 เอกสาร

- [SKILL.md](SKILL.md) - คู่มือสั้นสำหรับ AI
- [GUIDE.md](GUIDE.md) - คู่มือฉบับสมบูรณ์

---

## 🤝 มีส่วนร่วม

ยินดีรับ contribution! กรุณาสร้าง Pull Request หรือ Issue ได้เลย

---

## 📄 License

MIT License - ดูรายละเอียดใน [LICENSE](LICENSE)

---

## 🙏 ขอบคุณ

- [OpenClaw](https://github.com/openclaw/openclaw) - Personal AI Assistant
- [Google Calendar CalDAV](https://developers.google.com/calendar) - Calendar API

---

**Made with ❤️ for OpenClaw**
