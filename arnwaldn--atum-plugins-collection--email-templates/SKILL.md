---
name: email-templates
description: HTML email template authoring pattern library for web projects — React Email (component-based JSX templates, preview dev server, integration with Resend/Postmark/SendGrid), MJML (markup language that compiles to cross-client HTML tables), Handlebars + juice (inline CSS postprocessing), email-specific HTML quirks (tables for layout, inline CSS only, Outlook conditional comments, Gmail 102 KB clipping, dark mode CSS variables, CSS custom properties), responsive email patterns (media queries vs fluid layouts), preheader text, alt text for images, testing across clients (Litmus, Email on Acid, Mailpit). Use when designing or maintaining HTML email templates: authoring welcome emails, password resets, order confirmations, receipts, newsletters, or any branded communication. Differentiates from email-transactional (backend sending logic) by focusing on the template authoring and cross-client rendering. Complements ghost-expert (Ghost newsletters), sanity-expert (Sanity-managed email content), and wordpress-expert (WooCommerce email templates). Use when this capability is needed.
metadata:
  author: arnwaldn
---

# Email Templates Patterns

Les emails HTML sont **le pire** format web à maintenir : les clients (Outlook, Gmail, Apple Mail, Yahoo) supportent un sous-ensemble différent de CSS et HTML. Ces patterns couvrent les solutions modernes pour s'en sortir.

## 1. Decision tree

```
Projet React / Next.js ?
└── React Email (recommandé 2025) — DX imbattable, preview local

Projet non-React (Python, PHP, Go) ?
├── MJML (compile vers HTML cross-client)
└── Handlebars + juice (inline CSS)

Besoin du meilleur cross-client sans overhead ?
└── MJML

Besoin de templates managés par le client ?
└── SendGrid Dynamic Templates ou Postmark Templates (UI dans le provider)
```

## 2. React Email (moderne, recommandé)

### Setup

```bash
npm install @react-email/components @react-email/render
npm install -D react-email
```

```
my-project/
├── emails/
│   ├── WelcomeEmail.tsx
│   ├── PasswordReset.tsx
│   ├── OrderConfirmation.tsx
│   └── _components/
│       ├── EmailLayout.tsx
│       └── EmailButton.tsx
└── package.json
```

### Template type (avec layout shared)

```tsx
// emails/_components/EmailLayout.tsx
import {
  Body,
  Container,
  Font,
  Head,
  Html,
  Img,
  Preview,
  Section,
  Tailwind,
} from '@react-email/components'

export function EmailLayout({
  preview,
  children,
}: {
  preview: string
  children: React.ReactNode
}) {
  return (
    <Html>
      <Head>
        <Font
          fontFamily="Inter"
          fallbackFontFamily="Arial"
          webFont={{
            url: 'https://fonts.gstatic.com/s/inter/v13/UcC73FwrK3iLTeHuS_fvQtMwCp50KnMa1ZL7.woff2',
            format: 'woff2',
          }}
          fontWeight={400}
          fontStyle="normal"
        />
      </Head>
      <Preview>{preview}</Preview>
      <Tailwind>
        <Body className="bg-gray-100 py-10 font-sans">
          <Container className="mx-auto max-w-xl rounded bg-white p-8">
            <Section className="mb-6">
              <Img
                src="https://example.com/logo.png"
                width="120"
                height="40"
                alt="MyApp"
              />
            </Section>
            {children}
          </Container>
        </Body>
      </Tailwind>
    </Html>
  )
}
```

### Utilisation

```tsx
// emails/OrderConfirmation.tsx
import { Heading, Text, Button, Section } from '@react-email/components'
import { EmailLayout } from './_components/EmailLayout'

type Props = {
  customerName: string
  orderId: string
  total: string
  items: { name: string; quantity: number; price: string }[]
}

export function OrderConfirmation({ customerName, orderId, total, items }: Props) {
  return (
    <EmailLayout preview={`Order ${orderId} confirmed`}>
      <Heading className="text-2xl font-bold">Thanks for your order, {customerName}!</Heading>
      <Text>Your order #{orderId} has been confirmed.</Text>

      <Section className="my-6">
        {items.map((item, i) => (
          <div key={i} className="flex justify-between border-b py-2">
            <Text className="m-0">
              {item.quantity}x {item.name}
            </Text>
            <Text className="m-0 font-semibold">{item.price}</Text>
          </div>
        ))}
        <div className="flex justify-between py-2 text-lg font-bold">
          <Text className="m-0">Total</Text>
          <Text className="m-0">{total}</Text>
        </div>
      </Section>

      <Button
        href={`https://example.com/orders/${orderId}`}
        className="rounded bg-black px-6 py-3 text-white"
      >
        View order
      </Button>
    </EmailLayout>
  )
}

// Default export pour react-email dev server
export default OrderConfirmation
```

### Preview dev server

```bash
npx react-email dev
# Ouvre http://localhost:3000 avec hot reload
```

Le dev server liste tous les templates dans `emails/`, affiche le rendu, permet de tester avec des props mockées.

### Render en HTML (dans le backend)

```typescript
import { render } from '@react-email/render'
import { OrderConfirmation } from '@/emails/OrderConfirmation'

const html = render(<OrderConfirmation customerName="Arnaud" orderId="123" total="€59.99" items={[...]} />)

// Envoyer via Resend / Postmark / SES
await resend.emails.send({
  from: 'orders@example.com',
  to: 'user@example.com',
  subject: `Order ${orderId} confirmed`,
  html,
})
```

## 3. MJML (non-React, cross-client max)

```bash
npm install mjml
```

```xml
<!-- emails/welcome.mjml -->
<mjml>
  <mj-head>
    <mj-title>Welcome to MyApp</mj-title>
    <mj-preview>Thanks for signing up!</mj-preview>
    <mj-attributes>
      <mj-all font-family="Inter, Arial, sans-serif" />
      <mj-text font-size="16px" line-height="1.5" color="#333" />
    </mj-attributes>
  </mj-head>
  <mj-body background-color="#f6f6f6">
    <mj-section background-color="#ffffff" padding="40px">
      <mj-column>
        <mj-image src="https://example.com/logo.png" width="120px" alt="MyApp" />
        <mj-text font-size="24px" font-weight="bold">Welcome, {{firstName}}!</mj-text>
        <mj-text>Thanks for signing up. Get started by exploring your dashboard.</mj-text>
        <mj-button href="https://example.com/dashboard" background-color="#000" color="#fff">
          Go to dashboard
        </mj-button>
      </mj-column>
    </mj-section>
  </mj-body>
</mjml>
```

```typescript
import mjml2html from 'mjml'
import Handlebars from 'handlebars'
import fs from 'node:fs/promises'

// Compile au build ou au runtime
const mjmlSource = await fs.readFile('emails/welcome.mjml', 'utf-8')
const { html, errors } = mjml2html(mjmlSource)

// Then Handlebars pour les variables
const template = Handlebars.compile(html)
const rendered = template({ firstName: 'Arnaud' })

// Send via Postmark / SES
```

## 4. Handlebars + juice (legacy mais fonctionne partout)

```bash
npm install handlebars juice
```

```html
<!-- emails/welcome.hbs -->
<!DOCTYPE html>
<html>
<head>
  <style>
    body { font-family: Arial, sans-serif; background: #f6f6f6; margin: 0; padding: 40px; }
    .container { max-width: 600px; margin: 0 auto; background: #fff; padding: 40px; border-radius: 4px; }
    h1 { font-size: 24px; font-weight: bold; }
    .btn { display: inline-block; background: #000; color: #fff; padding: 12px 24px; text-decoration: none; border-radius: 4px; }
  </style>
</head>
<body>
  <div class="container">
    <h1>Welcome, {{firstName}}!</h1>
    <p>Thanks for signing up.</p>
    <a href="https://example.com/dashboard" class="btn">Go to dashboard</a>
  </div>
</body>
</html>
```

```typescript
import Handlebars from 'handlebars'
import juice from 'juice'
import fs from 'node:fs/promises'

const source = await fs.readFile('emails/welcome.hbs', 'utf-8')
const template = Handlebars.compile(source)
const html = template({ firstName: 'Arnaud' })

// Inline CSS pour les clients qui ignorent <style>
const inlined = juice(html)
```

## 5. Cross-client quirks (top 10)

### 1. Tables pour le layout

```html
<!-- Outlook ne gère pas flex/grid -->
<table cellspacing="0" cellpadding="0" border="0" width="100%">
  <tr>
    <td width="50%">Column 1</td>
    <td width="50%">Column 2</td>
  </tr>
</table>
```

### 2. CSS inline obligatoire

```html
<!-- BON (Outlook OK) -->
<p style="font-size: 16px; color: #333;">Text</p>

<!-- MAUVAIS (Gmail app supprime les classes) -->
<p class="text">Text</p>
```

### 3. Outlook conditional comments

```html
<!--[if mso]>
<table role="presentation" width="600" cellspacing="0" cellpadding="0" border="0">
  <tr><td>
<![endif]-->

<!-- Contenu moderne -->
<div style="max-width: 600px;">...</div>

<!--[if mso]>
  </td></tr>
</table>
<![endif]-->
```

### 4. Gmail 102 KB clipping

Gmail **tronque** les emails > 102 KB. Si ton email fait 110 KB, les utilisateurs voient "Message clipped [View entire message]".

→ Optimiser : compress le HTML, retirer les commentaires, éviter les CSS inutilisés.

### 5. Dark mode (iOS, Outlook)

```html
<style>
  @media (prefers-color-scheme: dark) {
    body, .container { background: #1a1a1a !important; color: #fff !important; }
  }
</style>

<!-- Meta pour iOS -->
<meta name="color-scheme" content="light dark">
<meta name="supported-color-schemes" content="light dark">
```

### 6. Images alt text

```html
<img src="https://example.com/hero.jpg" alt="Order #123 confirmed" width="600" height="300" style="display: block;" />
```

**Règle** : toujours mettre `alt` + `width` + `height` + `display: block` (sinon Gmail ajoute un espace).

### 7. Preheader text

Les 50-100 premiers chars sont affichés en preview dans l'inbox. Utiliser `<Preview>` en React Email ou du texte caché :

```html
<div style="display: none; font-size: 1px; color: #f6f6f6;">
  Thanks for your order #123 — see details inside
</div>
```

### 8. Buttons — bulletproof "VML" pour Outlook

```html
<!--[if mso]>
<v:roundrect xmlns:v="urn:schemas-microsoft-com:vml" href="https://example.com" style="height:40px;v-text-anchor:middle;width:200px;" arcsize="10%" strokecolor="#000" fillcolor="#000">
  <w:anchorlock/>
  <center style="color:#ffffff;font-family:sans-serif;font-size:16px;">Click me</center>
</v:roundrect>
<![endif]-->
<!--[if !mso]><!-- -->
<a href="https://example.com" style="background: #000; color: #fff; padding: 12px 24px; border-radius: 4px; text-decoration: none;">Click me</a>
<!--<![endif]-->
```

React Email et MJML génèrent ce pattern automatiquement.

### 9. Web fonts inconsistants

- **Apple Mail, iOS Mail** : supportent `@font-face` web fonts
- **Outlook desktop** : ignore, utilise la font system
- **Gmail** : ignore

→ Toujours déclarer un fallback solide : `Inter, -apple-system, Arial, sans-serif`.

### 10. Background images

```html
<!-- Outlook VML fallback pour background -->
<td background="hero.jpg" bgcolor="#000">
  <!--[if gte mso 9]>
  <v:rect xmlns:v="urn:schemas-microsoft-com:vml" fill="true" stroke="false" style="width:600px;height:300px;">
    <v:fill type="frame" src="hero.jpg" color="#000" />
    <v:textbox inset="0,0,0,0">
  <![endif]-->
  <div>Content on top of background</div>
  <!--[if gte mso 9]>
    </v:textbox>
  </v:rect>
  <![endif]-->
</td>
```

## 6. Testing

### Local

```bash
# Mailpit — mailserver local + web UI
docker run -d -p 8025:8025 -p 1025:1025 axllent/mailpit

# Config SMTP dans ton backend
SMTP_HOST=localhost SMTP_PORT=1025
```

Tous les emails envoyés en dev arrivent dans http://localhost:8025.

### Cross-client testing

- **Litmus** (payant) — preview sur 90+ clients réels
- **Email on Acid** (payant) — similaire
- **Mail Tester** (gratuit) — score spam + SPF/DKIM/DMARC
- **Mailjet Mailer** (freemium) — aperçus cross-client

## 7. Responsive email

### Option A — media queries (moderne, cassé sur Outlook)

```html
<style>
  @media (max-width: 600px) {
    .container { width: 100% !important; }
    .column { display: block !important; width: 100% !important; }
  }
</style>
```

### Option B — fluid layout (universal)

```html
<table width="100%" style="max-width: 600px;">
  <!-- Contenu -->
</table>
```

**Règle** : utiliser fluid layout comme base, media queries comme enhancement.

## Anti-patterns

1. **CSS externe ou `<style>` non-inliné** → ignoré par la moitié des clients
2. **Flexbox / Grid** → cassé sur Outlook (engine Word)
3. **Animations CSS, transitions** → ignorées, juste inutile
4. **Images lourdes non compressées** → > 102 KB = Gmail clipping
5. **Pas d'`alt` sur les images** → hideux quand les images sont désactivées
6. **Pas de preheader** → preview inbox vide ou avec du HTML cassé
7. **Buttons en `<button>`** → Outlook les ignore, utiliser `<a>` stylé
8. **SVG inline** → cassé partout sauf Apple Mail
9. **JavaScript** → supprimé par tous les clients, tentative = spam
10. **Emojis dans le subject** → OK mais parfois cassés sur Outlook

## Checklist

- [ ] **Framework choisi** (React Email / MJML / Handlebars + juice)
- [ ] **Layout shared** pour header/footer/style
- [ ] **Preheader text** défini
- [ ] **Alt text** sur toutes les images
- [ ] **Width + height** explicites sur images
- [ ] **Dark mode** testé
- [ ] **Mobile responsive** testé
- [ ] **Plain text fallback** généré
- [ ] **Variables** échappées (XSS)
- [ ] **Cross-client testing** (Litmus/Email on Acid pour les clients critiques)
- [ ] **Taille < 100 KB** (pour éviter Gmail clipping)
- [ ] **Unsubscribe link** présent si l'email pourrait être marketing

## Quand déléguer

- **Backend sending logic** → skill `email-transactional` (dans `atum-stack-backend`)
- **Marketing emails (Ghost newsletters)** → `ghost-expert` (dans `atum-cms-ecom`)
- **Shopify transactional emails** → `shopify-expert` (dans `atum-cms-ecom`) + Shopify Notifications
- **WooCommerce emails** → `wordpress-expert` + skill `woocommerce-patterns` (dans `atum-cms-ecom`)
- **Audit spam score** → Mail Tester, MXToolbox

## Ressources

- https://react.email/docs
- https://documentation.mjml.io/
- https://www.litmus.com/community/learning
- https://www.caniemail.com/ — compatibility reference pour HTML/CSS email
- https://htmlemail.io/inline/ — inliner CSS en ligne

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/arnwaldn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
