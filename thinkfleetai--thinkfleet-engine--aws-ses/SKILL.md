---
name: aws-ses
description: Send email via AWS Simple Email Service using Python boto3. Use when this capability is needed.
metadata:
  author: thinkfleetai
---

# AWS SES

Send email via AWS Simple Email Service.

## Environment Variables

- `AWS_ACCESS_KEY_ID` - AWS access key
- `AWS_SECRET_ACCESS_KEY` - AWS secret key
- `AWS_DEFAULT_REGION` - SES region (e.g. `us-east-1`)
- `SES_FROM_EMAIL` - Verified sender email

## Setup

```bash
pip3 install boto3 2>/dev/null
```

## Send plain text email

```bash
python3 -c "
import boto3, os, sys
ses = boto3.client('ses', region_name=os.environ.get('AWS_DEFAULT_REGION', 'us-east-1'))
resp = ses.send_email(
    Source=os.environ['SES_FROM_EMAIL'],
    Destination={'ToAddresses': [sys.argv[1]]},
    Message={
        'Subject': {'Data': sys.argv[2]},
        'Body': {'Text': {'Data': sys.argv[3]}}
    }
)
print(f\"Sent: {resp['MessageId']}\")
" "recipient@example.com" "Subject line" "Body text"
```

## Send HTML email

```bash
python3 -c "
import boto3, os
ses = boto3.client('ses')
ses.send_email(
    Source=os.environ['SES_FROM_EMAIL'],
    Destination={'ToAddresses': ['recipient@example.com']},
    Message={
        'Subject': {'Data': 'HTML Email'},
        'Body': {'Html': {'Data': '<h1>Hello</h1><p>From ThinkFleetBot</p>'}}
    }
)
print('Sent')
"
```

## Check sending quota

```bash
python3 -c "
import boto3
ses = boto3.client('ses')
q = ses.get_send_quota()
print(f\"24h max: {q['Max24HourSend']}, sent: {q['SentLast24Hours']}, rate: {q['MaxSendRate']}/s\")
"
```

## Notes

- Sender email must be verified in SES (or domain-verified).
- In sandbox mode, recipient emails must also be verified.
- Always confirm recipient and content with the user before sending.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkfleetai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
