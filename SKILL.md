# Gmail & Calendar via App Password

Use this skill when user wants to interact with Gmail and Google Calendar using App Password (when OAuth is blocked).

## When to Use

- User wants to send email via Gmail SMTP
- User wants to read/send emails via IMAP
- User wants to view Calendar events
- User wants to create Calendar events
- OAuth is blocked/disabled

## Setup

### 1. Create App Password

1. Go to https://myaccount.google.com/apppasswords
2. Sign in (must have 2-Step Verification enabled)
3. Create new App Password for "Mail"
4. Copy the 16-character password (format: `xxxx xxxx xxxx xxxx`)

### 2. Set Environment Variables

Add to your environment or OpenClaw config:

```bash
export GMAIL_EMAIL="your-email@gmail.com"
export GMAIL_APP_PASSWORD="xxxx xxxx xxxx xxxx"
```

Or set in OpenClaw gateway environment:
```json
{
  "gateway": {
    "env": {
      "GMAIL_EMAIL": "your-email@gmail.com",
      "GMAIL_APP_PASSWORD": "xxxx xxxx xxxx xxxx"
    }
  }
}
```

## Usage Examples

### Send Email

```python
import smtplib
import os
from email.message import EmailMessage

EMAIL = os.environ.get('GMAIL_EMAIL')
APP_PASSWORD = os.environ.get('GMAIL_APP_PASSWORD', '').replace(' ', '')

if not EMAIL or not APP_PASSWORD:
    raise ValueError("GMAIL_EMAIL and GMAIL_APP_PASSWORD required")

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

### Read Email

```python
import imaplib
import os

EMAIL = os.environ.get('GMAIL_EMAIL')
APP_PASSWORD = os.environ.get('GMAIL_APP_PASSWORD', '').replace(' ', '')

mail = imaplib.IMAP4_SSL('imap.gmail.com')
mail.login(EMAIL, APP_PASSWORD)
mail.select('inbox')
status, messages = mail.search(None, 'ALL')
# Process messages...
mail.logout()
```

### List Calendar Events

```python
import urllib.request
import base64
import urllib.parse
import os

EMAIL = os.environ.get('GMAIL_EMAIL')
APP_PASSWORD = os.environ.get('GMAIL_APP_PASSWORD', '').replace(' ', '')

auth = base64.b64encode(f"{EMAIL}:{APP_PASSWORD}".encode()).decode()
url = f'https://www.google.com/calendar/dav/{urllib.parse.quote(EMAIL)}/events/'

req = urllib.request.Request(url, method='PROPFIND')
req.add_header('Authorization', f'Basic {auth}')
req.add_header('Depth', '1')

with urllib.request.urlopen(req) as response:
    data = response.read().decode()
```

### Create Calendar Event

```python
import urllib.request
import base64
import urllib.parse
import os
from datetime import datetime, timedelta

EMAIL = os.environ.get('GMAIL_EMAIL')
APP_PASSWORD = os.environ.get('GMAIL_APP_PASSWORD', '').replace(' ', '')

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
    url = f'https://www.google.com/calendar/dav/{urllib.parse.quote(EMAIL)}/events/'
    
    req = urllib.request.Request(url, data=event_ics.encode(), method='PUT')
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
- ✅ Uses environment variables for credentials

## Limitations

- ❌ Cannot use Google Drive API
- ❌ Cannot use Google Sheets/Docs API
- ❌ Cannot use Google Contacts
- ❌ Some advanced Gmail features unavailable

## Troubleshooting

**"Authentication failed"**: Check App Password is correct and 2-Step Verification is enabled

**"Username and Password not accepted"**: 
- Remove spaces from App Password
- Create new App Password at https://myaccount.google.com/apppasswords

**"Calendar not found"**: Use correct email format in CalDAV URL, remember to URL-encode

**"534-5.7.9"**: Need to create App Password, not regular password

## Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| GMAIL_EMAIL | Your Gmail address | Yes |
| GMAIL_APP_PASSWORD | App Password (16 chars, spaces optional) | Yes |

## Notes

- This skill uses environment variables for security - never hardcode credentials
- App Password must be created at https://myaccount.google.com/apppasswords
- 2-Step Verification must be enabled on the Google account
- For full Google Workspace features, use OAuth-based integration instead
