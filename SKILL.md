# Gmail & Calendar via App Password

Use this skill when user wants to interact with Gmail and Google Calendar using App Password (when OAuth is blocked).

## When to Use

- User wants to send email via Gmail SMTP
- User wants to read/send emails via IMAP
- User wants to view Calendar events
- User wants to create Calendar events
- OAuth is blocked/disabled

## Prerequisites

### 1. App Password Setup

User must create an App Password:
1. Go to https://myaccount.google.com/apppasswords
2. Sign in (must have 2-Step Verification enabled)
3. Create new App Password for "Mail"
4. Provide the 16-character password (format: `xxxx xxxx xxxx xxxx`)

### 2. Configuration

Store credentials in environment variables or config:
- Email: `[YOUR_EMAIL]@gmail.com`
- App Password: `[YOUR_APP_PASSWORD]` (16 chars with spaces)

## Commands

### Send Email (Gmail SMTP)

```python
import smtplib
from email.message import EmailMessage
import os

EMAIL = os.environ.get('GMAIL_EMAIL', 'your-email@gmail.com')
APP_PASSWORD = os.environ.get('GMAIL_APP_PASSWORD', 'xxxx xxxx xxxx xxxx')

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

### Read Email (Gmail IMAP)

```python
import imaplib
import email
import os

EMAIL = os.environ.get('GMAIL_EMAIL', 'your-email@gmail.com')
APP_PASSWORD = os.environ.get('GMAIL_APP_PASSWORD', 'xxxx xxxx xxxx xxxx')

mail = imaplib.IMAP4_SSL('imap.gmail.com')
mail.login(EMAIL, APP_PASSWORD)
mail.select('inbox')

status, messages = mail.search(None, 'ALL')
# Process messages...
mail.logout()
```

### List Calendar Events (CalDAV)

```python
import urllib.request
import base64
import urllib.parse
import os

EMAIL = os.environ.get('GMAIL_EMAIL', 'your-email@gmail.com')
APP_PASSWORD = os.environ.get('GMAIL_APP_PASSWORD', 'xxxx xxxx xxxx xxxx')

auth = base64.b64encode(f"{EMAIL}:{APP_PASSWORD}".encode()).decode()

req = urllib.request.Request(
    f'https://www.google.com/calendar/dav/{urllib.parse.quote(EMAIL)}/events/',
    method='PROPFIND'
)
req.add_header('Authorization', f'Basic {auth}')
req.add_header('Depth', '1')

with urllib.request.urlopen(req) as response:
    data = response.read().decode()
# Process calendar data...
```

### Create Calendar Event (CalDAV)

```python
import urllib.request
import base64
import urllib.parse
import os
from datetime import datetime, timedelta

EMAIL = os.environ.get('GMAIL_EMAIL', 'your-email@gmail.com')
APP_PASSWORD = os.environ.get('GMAIL_APP_PASSWORD', 'xxxx xxxx xxxx xxxx')

def create_event(title, start_datetime, duration_minutes=60):
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
```

## Features

- ✅ Send emails via Gmail SMTP
- ✅ Read emails via Gmail IMAP
- ✅ List calendar events via CalDAV
- ✅ Create calendar events via CalDAV
- ✅ No OAuth required (uses App Password)

## Limitations

- ❌ Cannot use Google Drive API
- ❌ Cannot use Google Sheets/Docs API
- ❌ Cannot use Google Contacts
- ❌ Some advanced Gmail features unavailable

## Troubleshooting

**"Authentication failed"**: Check App Password is correct and 2-Step Verification is enabled

**"Calendar not found"**: Use correct email format in CalDAV URL, remember to URL-encode

**"534-5.7.9"**: Need to create App Password, not regular password

## Full Guide

See `GUIDE.md` for comprehensive documentation including:
- Step-by-step setup instructions
- More use cases
- Troubleshooting
- Security best practices
- Ready-to-use scripts

## Notes

This skill is a fallback when OAuth is blocked. For full Google Workspace features, use gws skill with proper OAuth setup.
