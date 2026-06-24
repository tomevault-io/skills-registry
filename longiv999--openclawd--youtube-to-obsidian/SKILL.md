---
name: youtube-to-obsidian
description: Lấy transcript từ YouTube video, phân tích với AI (giải thích khái niệm, đề xuất kỹ năng cần học, tóm tắt) và tự động tạo ghi chú trong Obsidian. Dùng khi cần lưu nội dung video YouTube để học tập, nghiên cứu. Triggers: "lưu video youtube", "transcript youtube", "ghi chú video", "youtube to obsidian", "phân tích video". Use when this capability is needed.
metadata:
  author: longiv999
---

# YouTube to Obsidian

Tự động lấy transcript từ YouTube video, **phân tích với AI** và lưu thành ghi chú Obsidian.

## Tính năng

- 📥 Lấy transcript từ YouTube (hỗ trợ nhiều ngôn ngữ)
- 🤖 Phân tích với AI (**Anthropic Claude 3.5 Sonnet**) để:
  - Tóm tắt nội dung chính
  - Giải thích các khái niệm mới (Expert AI Role)
  - Đề xuất kỹ năng cần học thêm (Skills Gaps)
  - Tạo checklist điểm cần nhớ
  - Đặt câu hỏi suy ngẫm
  - Gợi ý chủ đề liên quan (backlinks)
- 📝 Tạo note Obsidian với template đầy đủ

## Cách sử dụng

### Cơ bản (Mặc định dùng Anthropic Sonnet)
```bash
uv run {baseDir}/scripts/youtube_to_obsidian.py --url "https://www.youtube.com/watch?v=VIDEO_ID"
```

### Dùng Gemini thay thế
```bash
uv run {baseDir}/scripts/youtube_to_obsidian.py --url "URL" --provider gemini
```

### Không dùng AI
```bash
uv run {baseDir}/scripts/youtube_to_obsidian.py --url "URL" --no-ai
```

## API Key

Công cụ ưu tiên sử dụng **Anthropic API**. Hãy cài đặt Key bằng một trong các cách:

1. Environment variable (Khuyến nghị): `export ANTHROPIC_API_KEY=sk-ant-...`
2. CLI argument: `--api-key your_key`
3. Moltbot config: Tự động lấy từ `anthropic:default` profile.

Nếu muốn dùng Gemini, cần set `GEMINI_API_KEY`.

## Template Note

Note được tạo với các sections:

| Section | Mô tả |
|---------|-------|
| 📝 Tóm tắt | Tóm tắt ngắn gọn từ AI |
| 📚 Nội dung chính | Chi tiết các điểm chính |
| 💡 Khái niệm mới | Giải thích các khái niệm |
| 🎯 Kỹ năng cần học | Đề xuất skills để học thêm |
| ✅ Checklist | Điểm cần nhớ |
| ❓ Câu hỏi | Câu hỏi suy ngẫm |
| 🔗 Chủ đề liên quan | Backlinks đến notes khác |
| 📜 Transcript | Nội dung đầy đủ (collapsible) |
| 🏷️ Tags | Auto-generated tags |

## 🔄 Content Repurposing (Multi-Platform)

Tạo content đa platform từ video YouTube - chuyển đổi thành 5 formats để phân phối đa kênh.

### Tạo content đa platform
```bash
uv run {baseDir}/scripts/youtube_to_obsidian.py --url "URL" --repurpose
```

### Chọn platforms cụ thể
```bash
uv run {baseDir}/scripts/youtube_to_obsidian.py --url "URL" --repurpose --platforms social thread
```

### Output Formats

| Format | Platform | Độ dài | Mô tả |
|--------|----------|--------|-------|
| Social Post | Facebook/LinkedIn | 100-200 từ | Hook → Value → CTA |
| Thread | Twitter/Instagram | 5-7 slides | Carousel format |
| Email | Newsletter | 300-500 từ | Subject + Preview + Body |
| Summary | Intro/Bio | 50-100 từ | TL;DR bullet points |
| Hooks | Video/Ads | 5 hooks | Curiosity, Pain, Benefit, Contrarian, Story |

### Output Files

Khi dùng `--repurpose`, tạo thêm file `{title}_repurposed.md` bên cạnh note Obsidian chính.

```
YouTube Notes/
├── Video Title.md              # Note chính với AI analysis
└── Video Title_repurposed.md   # Multi-platform content (5 formats)
```

## Lưu ý

- Nếu video không có transcript, sẽ báo lỗi
- AI analysis cần ~5-10 giây tùy độ dài video
- Dùng `--no-ai` nếu muốn nhanh hơn

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/longiv999) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
