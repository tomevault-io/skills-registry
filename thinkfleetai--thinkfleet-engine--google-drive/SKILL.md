---
name: google-drive
description: Upload, download, and manage files in Google Drive via the Drive API using curl. Use when this capability is needed.
metadata:
  author: thinkfleetai
---

# Google Drive

File operations via Google Drive API v3.

## Environment Variables

- `GOOGLE_ACCESS_TOKEN` - OAuth2 access token with `drive` scope

## List files

```bash
curl -s -H "Authorization: Bearer $GOOGLE_ACCESS_TOKEN" \
  "https://www.googleapis.com/drive/v3/files?pageSize=10&fields=files(id,name,mimeType,modifiedTime)" \
  | jq '.files[]'
```

## Download a file

```bash
curl -s -H "Authorization: Bearer $GOOGLE_ACCESS_TOKEN" \
  "https://www.googleapis.com/drive/v3/files/FILE_ID?alt=media" -o /tmp/downloaded-file
```

## Upload a file

```bash
curl -s -X POST \
  -H "Authorization: Bearer $GOOGLE_ACCESS_TOKEN" \
  -F "metadata={\"name\":\"myfile.txt\"};type=application/json;charset=UTF-8" \
  -F "file=@/tmp/myfile.txt" \
  "https://www.googleapis.com/upload/drive/v3/files?uploadType=multipart" \
  | jq '{id, name}'
```

## Search files

```bash
curl -s -H "Authorization: Bearer $GOOGLE_ACCESS_TOKEN" \
  "https://www.googleapis.com/drive/v3/files?q=name%20contains%20'report'&fields=files(id,name)" \
  | jq '.files[]'
```

## Notes

- For large files (>5MB), use resumable upload.
- Always confirm before deleting files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkfleetai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
