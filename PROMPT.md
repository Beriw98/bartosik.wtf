# Zadanie: Strona wizytówka bartosik.wtf — migracja do Astro

## Kontekst
Mam gotową stronę wizytówkę w czystym HTML (plik `reference.html`). Chcę ją przenieść do **Astro** — zachowując identyczny wygląd, ale z czystą, komponentową strukturą. Strona ma wyglądać profesjonalnie również "od kuchni" — ktoś techniczny *będzie* zaglądał w źródło.

## Stack
- **Astro** (static site, zero JS domyślnie, islands gdzie potrzeba)
- **Vanilla CSS** (custom properties / design tokens, bez Tailwind)
- **i18n**: EN/PL z przełącznikiem w nav (client-side, jako interactive island)
- **Deploy target**: Cloudflare Pages

## Design system

### Kolory
```css
--bg:      #0c0d0f
--surface: #131418
--border:  #1f2128
--muted:   #3a3d47
--subtle:  #6b7080
--body:    #a8adb8
--heading: #ecedf0
--accent:  #4af0a0
--accent2: #2dd4bf
```

### Fonty (Google Fonts)
```
--mono:  'DM Mono', monospace
--serif: 'Instrument Serif', serif
--sans:  'DM Sans', sans-serif
```

Ogólna czcionka: `var(--sans)`, font-weight: 300, line-height: 1.7.

### Efekty
- Grain overlay na body (SVG noise filter, opacity 0.025, pointer-events: none)
- Subtle green radial gradient glow w hero (top-right)
- `scroll-behavior: smooth`
- Animacja `fadeUp` na sekcjach (opacity 0→1, translateY 16px→0, staggered delays)
- Animacja `blink` na badge (pulsujący zielony ●)

## Struktura komponentów

```
src/
├── components/
│   ├── Nav.astro          — logo + nav links (About/Stack/Contact) + badge + LangSwitcher
│   ├── LangSwitcher.astro — EN/PL toggle (to jedyny interaktywny element — needs client JS)
│   ├── Hero.astro         — eyebrow + h1 + desc + CTA buttons
│   ├── About.astro        — zdjęcie (photo.jpg) + 3 paragrafy tekstu
│   ├── Stack.astro        — tagi technologii (primary + secondary)
│   ├── WhatIBring.astro   — 4 karty w gridzie
│   ├── Availability.astro — 3-kolumnowy blok + note
│   ├── Projects.astro     — TutAll row
│   ├── Contact.astro      — h2 + subtitle + email + "or" + booking button
│   └── Footer.astro       — imię + LinkedIn link
├── i18n/
│   ├── en.json
│   └── pl.json
├── layouts/
│   └── Layout.astro       — head, fonty, global CSS, grain overlay
├── pages/
│   └── index.astro        — składa komponenty z dividerami
└── styles/
    └── global.css          — design tokens, reset, wspólne style
```

## Treści i18n

Tłumaczenia są w plikach `en.json` i `pl.json` (dołączone). Klucze i18n pasują do sekcji.

### Mechanizm i18n
- Auto-detect: localStorage → navigator.language → default EN
- Przełącznik EN/PL w nav (przyciski, aktywny ma zielone tło)
- `setLang()` ustawia `document.documentElement.lang`, updatuje treści po `data-i18n`, zapisuje do localStorage
- To jest jedyny JS na stronie — reszta jest statyczna

## Sekcje szczegółowo

### Nav (sticky, backdrop-blur)
- Logo: `bartosik.wtf` (mono, accent color)
- Links: About / Stack / Contact (mono, subtle → accent on hover)
- Badge: `● available for contracts` (border accent, pulsujący dot)
- LangSwitcher: dwa buttony EN|PL, aktywny zielony
- Na mobile (<600px): ukryj links i badge, zostaw logo + switcher

### Hero
- Eyebrow: `Senior Backend Engineer · Wrocław, Poland` (mono, accent, uppercase)
- H1: `Systems built to last` ("last" w `<em>` accent color, serif font)
- Desc: zwykły tekst (patrz en.json `hero-desc`)
- CTAs: `Get in touch →` (btn-primary, zielone tło) + `LinkedIn` (btn-ghost, border)

### About (grid: 160px foto + tekst)
- Zdjęcie: `photo.jpg` (160×160, object-fit cover, object-position center top, lekki grayscale filter, border)
- 3 paragrafy: about-p1, about-p2, about-p3 (bold na strong elementach, heading color)

### Tech Stack
- Tagi w flex-wrap
- Primary tags (accent border+color+bg): Go/Golang, Microservices, gRPC, GCP, React, Claude Code, AI-assisted dev
- Secondary tags (surface bg, border): PostgreSQL, Prometheus, Grafana, OpenTelemetry, GitLab CI, Docker, REST APIs, Kubernetes

### What I Bring (4-kolumnowy grid, 1px gap, border)
- Card 1: ⚙️ Backend architecture / Architektura
- Card 2: 🤖 AI-powered workflow
- Card 3: 🔍 Observability
- Card 4: 🧭 Senior review
- Hover: jaśniejsze tło. Na mobile: 2 kolumny.

### Availability
- 3-kolumnowy blok z accent left-border:
  - Commitment: `up to 20h / week` (green)
  - When: `Evenings CET`
  - Format: `Remote · B2B`
- Note pod spodem: `✦ Open to ongoing contracts as well as one-off projects` (accent bg tint)

### Projects
- Row: `TutAll` + opis + link `tutall.app ↗`

### Contact (text-align center)
- H2: `Let's talk.` (serif)
- Subtitle: `// part-time · remote · evenings CET · B2B` (mono)
- Email: `maciek@bartosik.wtf` (mailto link, accent, border-bottom)
- Separator: `or` / `lub`
- Booking: `Book a call →` / `Umów rozmowę →` (btn-primary, link: https://calendar.app.google/abnJyttKrz5yXWHC6)

### Footer
- Left: `Maciej Bartosik · Wrocław, PL`
- Right: LinkedIn link

## Ważne detale
- Zdjęcie (`photo.jpg`) jako osobny plik w `public/`, nie base64
- Email jako zwykły `mailto:` link, bez Cloudflare email protection
- `scroll-margin-top: 4rem` na sekcjach (offset pod sticky nav)
- Divider między każdą sekcją (1px, border color)
- Container: max-width 1000px, padding 0 3rem (mobile: 0 1.25rem)
- Sekcje mają staggered fadeUp animation delays
