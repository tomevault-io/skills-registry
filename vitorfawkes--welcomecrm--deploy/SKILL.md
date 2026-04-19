---
name: deploy
description: Deploy edge function para o Supabase Use when this capability is needed.
metadata:
  author: vitorfawkes
---

Deploy da edge function $ARGUMENTS para produção.

1. Verificar que a function existe em `supabase/functions/$ARGUMENTS/`
2. Rodar `npx tsc --noEmit` para verificar tipos
3. Executar: `npx supabase functions deploy $ARGUMENTS --project-ref szyrzxvlptqqheizyrxu`
4. Verificar se o deploy foi bem sucedido
5. Reportar resultado ao usuário

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vitorfawkes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
