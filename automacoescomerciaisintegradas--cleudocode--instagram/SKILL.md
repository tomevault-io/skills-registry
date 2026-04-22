---
name: instagram
description: Integração com Instagram Graph API para publicação de mídia e gestão de perfil. Use when this capability is needed.
metadata:
  author: automacoescomerciaisintegradas
---

# Instagram Skill

Esta skill permite ao Cleudocode interagir com a API do Instagram para automatizar posts de ofertas.

## Funcionalidades

- **Publicação de Mídia**: Postar fotos e vídeos (Reels).
- **OAuth2 Flow**: Gerenciar troca de códigos por tokens de acesso.
- **Leitura de Perfil**: Obter mídia recente e informações do usuário.

## Configuração

Defina suas credenciais no `.env`:

```env
INSTAGRAM_CLIENT_ID=seu-id
INSTAGRAM_CLIENT_SECRET=seu-secret
INSTAGRAM_REDIRECT_URI=https://seu-backend.com/oauth/callback
```

## Uso

### Publicar Imagem

```xml
<tool code="instagram">
action:publish_photo url:"https://url-da-imagem.jpg" caption:"Confira esta oferta!"
</tool>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/automacoescomerciaisintegradas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
