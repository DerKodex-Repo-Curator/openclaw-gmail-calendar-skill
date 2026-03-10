# คู่มือสกิล Gmail & Calendar ผ่าน App Password

> **สำหรับผู้ดูแลระบบ OpenClaw ที่ต้องการเชื่อมต่อ Gmail + Calendar โดยไม่ใช้ OAuth**

---

## ภาพรวม

สกิลนี้ใช้สำหรับเชื่อมต่อกับ Gmail และ Google Calendar โดยใช้ **App Password** แทน OAuth

### ข้อดี
- ✅ ไม่ต้องผ่าน OAuth consent screen (ไม่โดน Google บล็อก)
- ✅ ใช้งานได้ทันที
- ✅ ไม่ต้องมี Google Workspace องค์กร

### ข้อจำกัด
- ❌ ใช้ได้เฉพาะ Gmail และ Calendar
- ❌ ไม่รองรับ Google Drive, Sheets, Docs, Contacts

---

## ส่วนที่ 1: ความเข้าใจเบื้องต้น

### 1.1 App Password คืออะไร?

App Password คือรหัสผ่าน 16 ตัวอักษรที่ Google สร้างให้สำหรับแอปที่ไม่รองรับ OAuth โดยตรง

### 1.2 ทำไมต้องใช้ App Password?

เมื่อ OAuth app ไม่ได้รับการ verify จาก Google จะถูกบล็อก โดยเฉพาะ:
- บัญชี Gmail ส่วนตัว
- OAuth app ที่ยังอยู่ในโหมดทดสอบ

### 1.3 Services ที่รองรับ App Password

| Service | Protocol | สถานะ |
|---------|----------|--------|
| Gmail | SMTP/IMAP | ✅ ใช้ได้ |
| Calendar | CalDAV | ✅ ใช้ได้ |
| Contacts | CardDAV | ❌ ไม่ได้ |
| Drive | WebDAV | ❌ ไม่ได้ |

---

## ส่วนที่ 2: ขั้นตอนการสร้าง App Password (สำหรับผู้ใช้)

### 2.1 เปิด 2-Step Verification

ก่อนสร้าง App Password ต้องเปิด 2-Step Verification ก่อน:

1. ไปที่: https://myaccount.google.com/signinoptions/two-step-verification
2. ล็อกอินด้วยบัญชี Google
3. กด "Get Started"
4. ทำตามขั้นตอนเพิ่มวิธียืนยันตัวตน (แนะนำใช้ Authenticator App)

### 2.2 สร้าง App Password

1. ไปที่: https://myaccount.google.com/apppasswords
2. ล็อกอิน (ถ้าถูกถาม)
3. ช่อง "Select app" → เลือก "Other (Custom name)"
4. พิมพ์ชื่อ เช่น: `OpenClaw`
5. กด "Generate"
6. **บันทึกรหัส 16 ตัวอักษรที่แสดงขึ้นมา** (รูปแบบ: `xxxx xxxx xxxx xxxx`)

> ⚠️ **สำคัญ:** รหัสนี้จะแสดงเพียงครั้งเดียว ต้องบันทึกทันที!

### 2.3 ตัวอย่าง App Password

```
App Password ที่สร้าง: xxxx xxxx xxxx xxxx
                       ↑↑↑↑ ↑↑↑↑ ↑↑↑↑ ↑↑↑↑
                  (16 ตัวอักษร มีช่องว่าง)
```

---

## ส่วนที่ 3: การตั้งค่าสกิลใน OpenClaw

### 3.1 สร้างโฟลเดอร์สกิล

```bash
mkdir -p /app/skills/gmail-calendar
```

### 3.2 สร้างไฟล์ SKILL.md

สร้างไฟล์ `/app/skills/gmail-calendar/SKILL.md` ดังนี้:

```markdown
# Gmail & Calendar via App Password

Use this skill when user wants to interact with Gmail and Google Calendar using App Password.

## When to Use

- User wants to send email via Gmail SMTP
- User wants to read emails via IMAP
- User wants to view/create Calendar events
- OAuth is blocked/disabled

## Prerequisites

### App Password Setup

1. Go to https://myaccount.google.com/apppasswords
2. Enable 2-Step Verification first
3. Create App Password for "Mail"
4. Provide the 16-character password

## Credentials

Configure these in your code:
- Email: YOUR_EMAIL@gmail.com
- App Password: xxxx xxxx xxxx xxxx

## Commands

### Send Email

python:
```python
import smtplib
from email.message import EmailMessage

EMAIL = "your-email@gmail.com"
APP_PASSWORD = "xxxx xxxx xxxx xxxx"

msg = EmailMessage()
msg['From'] = EMAIL
msg['To'] = 'recipient@example.com'
msg['Subject'] = 'Subject'
msg.set_content('Body')

with smtplib.SMTP('smtp.gmail.com', 587) as server:
    server.starttls()
    server.login(EMAIL, APP_PASSWORD)
    server.send_message(msg)
```

### Read Calendar Events

python:
```python
import urllib.request
import base64
import re
from datetime import datetime

EMAIL = "your-email@gmail.com"
APP_PASSWORD = "xxxx xxxx xxxx xxxx"

auth = base64.b64encode(f"{EMAIL}:{APP_PASSWORD}".encode()).decode()

req = urllib.request.Request(
    f'https://www.google.com/calendar/dav/{urllib.parse.quote(EMAIL)}/events/',
    method='REPORT'
)
req.add_header('Authorization', f'Basic {auth}')
req.add_header('Depth', '1')
req.add_header('Content-Type', 'application/xml')

# CalDAV XML query here...
```

## Features

- ✅ Send emails via Gmail SMTP
- ✅ Read emails via IMAP
- ✅ List/create calendar events via CalDAV
- ❌ Drive, Sheets, Docs, Contacts not supported
```

---

## ส่วนที่ 4: Use Cases ตัวอย่าง

### 4.1 ส่ง Email แจ้งเตือน

```python
import smtplib
from email.message import EmailMessage

def send_email(to, subject, body):
    EMAIL = "your-email@gmail.com"
    APP_PASSWORD = "xxxx xxxx xxxx xxxx"
    
    msg = EmailMessage()
    msg['From'] = EMAIL
    msg['To'] = to
    msg['Subject'] = subject
    msg.set_content(body)
    
    with smtplib.SMTP('smtp.gmail.com', 587) as server:
        server.starttls()
        server.login(EMAIL, APP_PASSWORD)
        server.send_message(msg)

# ใช้งาน
send_email(
    to="boss@example.com", 
    subject="รายงานประจำวัน", 
    body="สวัสดีครับ นี่คือรายงานประจำวัน..."
)
```

### 4.2 อ่าน Email ล่าสุด

```python
import imaplib
import email

def get_recent_emails(max_results=5):
    EMAIL = "your-email@gmail.com"
    APP_PASSWORD = "xxxx xxxx xxxx xxxx"
    
    mail = imaplib.IMAP4_SSL('imap.gmail.com')
    mail.login(EMAIL, APP_PASSWORD)
    mail.select('inbox')
    
    status, messages = mail.search(None, 'ALL')
    email_ids = messages[0].split()[-max_results:]
    
    emails = []
    for eid in email_ids:
        status, msg_data = mail.fetch(eid, '(RFC822)')
        msg = email.message_from_bytes(msg_data[0][1])
        emails.append({
            'from': msg['From'],
            'subject': msg['Subject'],
            'date': msg['Date']
        })
    
    mail.logout()
    return emails

# ใช้งาน
for e in get_recent_emails(3):
    print(f"From: {e['from']}")
    print(f"Subject: {e['subject']}")
    print("---")
```

### 4.3 สร้าง Calendar Event

```python
import urllib.request
import base64
from datetime import datetime, timedelta

def create_calendar_event(title, start_datetime, duration_minutes=60):
    EMAIL = "your-email@gmail.com"
    APP_PASSWORD = "xxxx xxxx xxxx xxxx"
    
    end_dt = start_datetime + timedelta(minutes=duration_minutes)
    
    event_ics = f"""BEGIN:VCALENDAR
VERSION:2.0
BEGIN:VEVENT
DTSTART:{start_datetime.strftime('%Y%m%dT%H%M%SZ')}
DTEND:{end_dt.strftime('%Y%m%dT%H%M%SZ')}
SUMMARY:{title}
END:VEVENT
END:VCALENDAR"""
    
    auth = base64.b64encode(f"{EMAIL}:{APP_PASSWORD}".encode()).decode()
    
    req = urllib.request.Request(
        f'https://www.google.com/calendar/dav/{urllib.parse.quote(EMAIL)}/events/',
        data=event_ics.encode(),
        method='PUT'
    )
    req.add_header('Authorization', f'Basic {auth}')
    req.add_header('Content-Type', 'text/calendar')
    
    with urllib.request.urlopen(req) as response:
        return response.status == 201

# ใช้งาน
from datetime import datetime
create_calendar_event(
    title="ประชุมทีม",
    start_datetime=datetime(2026, 3, 15, 10, 0),  # 10:00 น.
    duration_minutes=60
)
```

### 4.4 อ่าน Calendar Events

```python
import urllib.request
import base64
import re

def get_calendar_events():
    EMAIL = "your-email@gmail.com"
    APP_PASSWORD = "xxxx xxxx xxxx xxxx"
    
    import urllib.parse
    auth = base64.b64encode(f"{EMAIL}:{APP_PASSWORD}".encode()).decode()
    
    req = urllib.request.Request(
        f'https://www.google.com/calendar/dav/{urllib.parse.quote(EMAIL)}/events/',
        method='PROPFIND'
    )
    req.add_header('Authorization', f'Basic {auth}')
    req.add_header('Depth', '1')
    
    with urllib.request.urlopen(req) as response:
        data = response.read().decode()
    
    # Extract event URLs
    events = re.findall(r'/calendar/dav/[^/]+/events/([^.]+\.ics)', data)
    return events

# ใช้งาน
for event_url in get_calendar_events()[:10]:
    print(event_url)
```

---

## ส่วนที่ 5: การแก้ปัญหา

### 5.1 Error: "Authentication failed"

**สาเหตุ:** App Password ไม่ถูกต้อง

**วิธีแก้:**
1. ตรวจสอบว่าพิมพ์ App Password ถูกต้อง
2. ลองสร้าง App Password ใหม่
3. ตรวจสอบว่าเปิด 2-Step Verification แล้ว

### 5.2 Error: "Calendar not found"

**สาเหตุ:** Email ใน URL ไม่ถูกต้อง

**วิธีแก้:**
1. ใช้ URL encoding สำหรับ email: `urllib.parse.quote(email)`
2. ตรวจสอบว่า email ตรงกับบัญชีที่สร้าง App Password

### 5.3 Error: "534-5.7.9 Application-specific password required"

**สาเหตุ:** ยังไม่ได้สร้าง App Password

**วิธีแก้:**
1. สร้าง App Password ตามขั้นตอนในส่วนที่ 2

### 5.4 Error: "SMTP AuthenticationError"

**สาเหตุ:** ปิดการเข้าถึง "Less secure apps"

**วิธีแก้:**
1. ไม่ต้องแก้ไขอะไร - App Password ไม่เกี่ยวกับ "Less secure apps"
2. ตรวจสอบว่าใช้ App Password ถูกต้อง

---

## ส่วนที่ 6: ความปลอดภัย

### 6.1 ห้ามเปิดเผยข้อมูลเหล่านี้

- ❌ App Password (16 ตัวอักษร)
- ❌ Email ของผู้ใช้
- ❌ Refresh Tokens
- ❌ OAuth Client Secrets

### 6.2 แนวปฏิบัติที่ดี

1. **เก็บ App Password ในที่ปลอดภัย** - ใช้ password manager
2. **สร้าง App Password เฉพาะแอป** - แยกตาม use case
3. **เปลี่ยน App Password ถ้าสงสัยว่ารั่วไหล**
4. **ใช้ Environment Variables** - เก็บใน config ไม่ใช่ใน code

### 6.3 ตัวอย่างการใช้ Environment Variables

```python
import os
import smtplib
from email.message import EmailMessage

EMAIL = os.environ.get('GMAIL_EMAIL')
APP_PASSWORD = os.environ.get('GMAIL_APP_PASSWORD')

# ใช้ใน code...
```

---

## ส่วนที่ 7: สคริปต์สำเร็จรูป

### 7.1 ส่ง Email

```bash
python3 << 'EOF'
import smtplib, os, sys
from email.message import EmailMessage

EMAIL = os.environ.get('GMAIL_EMAIL', 'your-email@gmail.com')
APP_PASSWORD = os.environ.get('GMAIL_APP_PASSWORD', 'xxxx xxxx xxxx xxxx')
TO = sys.argv[1] if len(sys.argv) > 1 else EMAIL
SUBJECT = sys.argv[2] if len(sys.argv) > 2 else 'Test'
BODY = sys.argv[3] if len(sys.argv) > 3 else 'Hello!'

msg = EmailMessage()
msg['From'], msg['To'], msg['Subject'] = EMAIL, TO, SUBJECT
msg.set_content(BODY)

with smtplib.SMTP('smtp.gmail.com', 587) as s:
    s.starttls()
    s.login(EMAIL, APP_PASSWORD)
    s.send_message(msg)

print("ส่งสำเร็จ!")
EOF
```

**ใช้งาน:**
```bash
python3 send_email.py "to@example.com" "หัวข้อ" "เนื้อหา"
```

### 7.2 ดู Calendar Events

```bash
python3 << 'EOF'
import urllib.request, base64, re, os, urllib.parse
from datetime import datetime

EMAIL = os.environ.get('GMAIL_EMAIL', 'your-email@gmail.com')
APP_PASSWORD = os.environ.get('GMAIL_APP_PASSWORD', 'xxxx xxxx xxxx xxxx')

auth = base64.b64encode(f"{EMAIL}:{APP_PASSWORD}".encode()).decode()
url = f'https://www.google.com/calendar/dav/{urllib.parse.quote(EMAIL)}/events/'

req = urllib.request.Request(url, method='PROPFIND')
req.add_header('Authorization', f'Basic {auth}')
req.add_header('Depth', '1')

with urllib.request.urlopen(req) as r:
    data = r.read().decode()

events = re.findall(r'/calendar/dav/[^/]+/events/([^.]+)\.ics', data)
print(f"พบ {len(events)} events")
for e in events[:10]:
    print(f"  - {e}")
EOF
```

---

## ส่วนที่ 8: สรุป

| หัวข้อ | รายละเอียด |
|--------|-------------|
| **สิ่งที่ได้** | เชื่อมต่อ Gmail + Calendar โดยไม่ต้องใช้ OAuth |
| **วิธีการ** | ใช้ App Password (16 ตัวอักษร) |
| **ข้อดี** | ตั้งค่าง่าย ไม่โดนบล็อก |
| **ข้อจำกัด** | ใช้ได้เฉพาะ Gmail + Calendar |
| **ความปลอดภัย** | เก็บ App Password ให้ปลอดภัย อย่าเปิดเผย |

---

> **เขียนโดย:** Mew (OpenClaw Assistant)
> **อัปเดตล่าสุด:** 2026-03-11
> **เวอร์ชัน:** 1.0
