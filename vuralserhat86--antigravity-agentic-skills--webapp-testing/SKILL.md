---
name: webapp-testing
description: Toolkit for interacting with and testing local web applications using Playwright. Supports verifying frontend functionality, debugging UI behavior, capturing browser screenshots, and viewing browser logs. Use when this capability is needed.
metadata:
  author: vuralserhat86
---

# Web Application Testing

To test local web applications, write native Python Playwright scripts.

**Helper Scripts Available**:
- `scripts/with_server.py` - Manages server lifecycle (supports multiple servers)

**Always run scripts with `--help` first** to see usage. DO NOT read the source until you try running the script first and find that a customized solution is abslutely necessary. These scripts can be very large and thus pollute your context window. They exist to be called directly as black-box scripts rather than ingested into your context window.

## Decision Tree: Choosing Your Approach

```
User task → Is it static HTML?
    ├─ Yes → Read HTML file directly to identify selectors
    │         ├─ Success → Write Playwright script using selectors
    │         └─ Fails/Incomplete → Treat as dynamic (below)
    │
    └─ No (dynamic webapp) → Is the server already running?
        ├─ No → Run: python scripts/with_server.py --help
        │        Then use the helper + write simplified Playwright script
        │
        └─ Yes → Reconnaissance-then-action:
            1. Navigate and wait for networkidle
            2. Take screenshot or inspect DOM
            3. Identify selectors from rendered state
            4. Execute actions with discovered selectors
```

## Example: Using with_server.py

To start a server, run `--help` first, then use the helper:

**Single server:**
```bash
python scripts/with_server.py --server "npm run dev" --port 5173 -- python your_automation.py
```

**Multiple servers (e.g., backend + frontend):**
```bash
python scripts/with_server.py \
  --server "cd backend && python server.py" --port 3000 \
  --server "cd frontend && npm run dev" --port 5173 \
  -- python your_automation.py
```

To create an automation script, include only Playwright logic (servers are managed automatically):
```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=True) # Always launch chromium in headless mode
    page = browser.new_page()
    page.goto('http://localhost:5173') # Server already running and ready
    page.wait_for_load_state('networkidle') # CRITICAL: Wait for JS to execute
    # ... your automation logic
    browser.close()
```

## Reconnaissance-Then-Action Pattern

1. **Inspect rendered DOM**:
   ```python
   page.screenshot(path='/tmp/inspect.png', full_page=True)
   content = page.content()
   page.locator('button').all()
   ```

2. **Identify selectors** from inspection results

3. **Execute actions** using discovered selectors

## Common Pitfall

❌ **Don't** inspect the DOM before waiting for `networkidle` on dynamic apps
✅ **Do** wait for `page.wait_for_load_state('networkidle')` before inspection

## 🔄 Workflow

> **Kaynak:** [Playwright Python Documentation](https://playwright.dev/python/docs/intro) & [Testing Hybrid Web Apps (2025)](https://github.com/microsoft/playwright)

### Aşama 1: Environment & Reconnaissance
- [ ] **Server Management**: `scripts/with_server.py --server "..."` kullanarak test ortamını (Frontend/Backend) otomatik ayağa kaldır.
- [ ] **DOM Inspection**: `networkidle` state'ine ulaştıktan sonra `page.content()` ve screenshot ile selector analizi yap.
- [ ] **Selector Strategy**: Kararlı testler için `role`, `text` veya `test-id` bazlı selector'ları belirle.

### Aşama 2: Scripting & Automation
- [ ] **Action Chain**: Playwright API kullanarak tıklama, form doldurma ve navigasyon adımlarını kodla.
- [ ] **Assertion Logic**: `expect()` metodlarıyla sayfa başlığı, element görünürlüğü veya URL doğruluğunu kontrol et.
- [ ] **Visual Testing**: Arayüz tutarlılığı için Snapshot karşılaştırmaları (`screenshot` audit) yap.

### Aşama 3: Debug & Reporting
- [ ] **Log Audit**: Tarayıcı konsol çıktılarını (Console logs) ve ağ hatalarını (Failed requests) incele.
- [ ] **Trace Analysis**: Başarısız testler için Playwright Trace Viewer kullanarak adım adım hata ayıklama yap.
- [ ] **CI/CD Alignment**: Testlerin headless modda ve sürekli entegrasyon hattında sorunsuz çalıştığını doğrula.

### Kontrol Noktaları
| Aşama | Doğrulama |
|-------|-----------|
| 1 | Sunucu port çakışmaları ve timeout değerleri optimize edildi mi? |
| 2 | Dinamik içerikler için `wait_for_selector` kullanıldı mı? |
| 3 | Test verileri (Mock data) her koşumda temizleniyor mu? |

---
*Webapp Testing v2.0 - With Workflow*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuralserhat86) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
