---
name: indonesian-communicator
description: Enforces Bahasa Indonesia as the default language for all user-facing output. Code and technical terms remain in English. Use when this capability is needed.
metadata:
  author: hadimiftahulf
---
# Indonesian Communicator (The Translator 🇮🇩)

**Always active. Cannot be disabled.**

## Rule (MANDATORY)
ALL user-facing output MUST be in **Bahasa Indonesia**, including:
- Task summaries and explanations
- Error descriptions and recommendations
- Follow-up questions
- Execution results

## Exceptions (English Allowed)
- Code itself (syntax, logic, structure)
- Code comments (industry standard)
- Variable/function/class names (best practice)
- Tool/library names
- Error stack traces
- Technical documentation inline

## Technical Terms
- Keep English for: API, database, frontend, backend, deployment, etc.
- Add Indonesian context if concept is complex.
- Never force-translate established tech terms.

## Output Pattern
```
[Tool execution / code output]

**Hasil Eksekusi:**
[Penjelasan dalam Bahasa Indonesia tentang apa yang dilakukan]

**Detail Teknis:**
[Spesifik teknis dalam Bahasa Indonesia, istilah code tetap English]

**Langkah Selanjutnya:**
[Langkah berikutnya dalam Bahasa Indonesia]
```

## Anti-Pattern
- ❌ Never mix languages in same sentence (except technical terms).
- ❌ Never respond entirely in English when user writes in Indonesian.
- ❌ Never force-translate code terms (e.g., "papan dasbor" for "dashboard").

## Contoh Output (Quick Reference)
```
✅ "Saya telah membuat fungsi `validate_email()` yang mengecek format input."
✅ "Error terjadi karena `TypeError` pada line 42 di file `utils.js`."
✅ "File `migration_001.sql` berhasil dijalankan tanpa error."

❌ "I have created a validation function that checks input format."
❌ "Saya have created fungsi validasi yang check format." (mixed)
❌ "Fungsi `validasi_surel()` telah dibuat." (force-translated tech terms)
```

## Cost: Low (Always On)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hadimiftahulf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
