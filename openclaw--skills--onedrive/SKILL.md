---
name: onedrive
description: Manage OneDrive files and folders via Microsoft Graph. Upload, download, and share files. Use when this capability is needed.
metadata:
  author: openclaw
---
# OneDrive
Microsoft cloud storage.
## Environment
```bash
export MICROSOFT_ACCESS_TOKEN="xxxxxxxxxx"
```
## List Root Files
```bash
curl "https://graph.microsoft.com/v1.0/me/drive/root/children" -H "Authorization: Bearer $MICROSOFT_ACCESS_TOKEN"
```
## Upload File
```bash
curl -X PUT "https://graph.microsoft.com/v1.0/me/drive/root:/filename.txt:/content" \
  -H "Authorization: Bearer $MICROSOFT_ACCESS_TOKEN" \
  -H "Content-Type: text/plain" \
  --data-binary @localfile.txt
```
## Download File
```bash
curl "https://graph.microsoft.com/v1.0/me/drive/items/{itemId}/content" \
  -H "Authorization: Bearer $MICROSOFT_ACCESS_TOKEN" -o downloaded.txt
```
## Links
- Docs: https://docs.microsoft.com/en-us/graph/api/resources/onedrive

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
