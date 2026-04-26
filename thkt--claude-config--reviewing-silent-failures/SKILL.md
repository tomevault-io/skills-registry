---
name: reviewing-silent-failures
description: > Use when this capability is needed.
metadata:
  author: thkt
---

# Silent Failure Review

## Detection

| ID  | Pattern                          | Fix                                     |
| --- | -------------------------------- | --------------------------------------- |
| SF1 | `catch (e) {}`                   | `catch (e) { logger.error(e); throw }`  |
| SF1 | `catch (e) { console.log(e) }`   | Show user feedback + log context        |
| SF2 | `.then(fn)` without `.catch()`   | Add `.catch()` or use try/catch         |
| SF2 | `async () => { await fn() }`     | Wrap in try/catch, handle error         |
| SF3 | No error UI states               | Add error boundary, feedback component  |
| SF4 | `value ?? defaultValue` silently | Log when using fallback                 |
| SF4 | `data?.nested?.value`            | Check and report if unexpected null     |
| SF5 | `catch { return defaultValue }`  | Log root cause before returning default |
| SF5 | `config.x \|\| fallback`         | Validate config, warn on missing keys   |

## References

| Topic     | File                                                   |
| --------- | ------------------------------------------------------ |
| Detection | `${CLAUDE_SKILL_DIR}/references/detection-patterns.md` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thkt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
