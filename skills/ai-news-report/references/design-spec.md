# Design Specification — Swiss International Style

## Visual Philosophy

The design follows **Swiss International Style** (瑞士国际主义) principles.
Inspired by Massimo Vignelli, Josef Müller-Brockmann, and *Helvetica Forever*.

### Core Design Principles

Violating any one of these will break the aesthetic:

1. **Single accent color** — one brand highlight used sparingly, never multiple colors
2. **Extreme type size contrast** — hero title vs body ratio >= 8:1
3. **Sans-serif only** — system sans-serif + monospace for meta; no serif fonts anywhere
4. **Flat and direct** — no gradients, no shadows, no rounded corners on content elements
5. **Grid supremacy** — all elements align to a grid system, asymmetric whitespace
6. **Hairline dividers** — 1px borders as structural separators
7. **Dot grid decoration only in hero** — content pages stay clean

## Tech Stack

- React 18+ with TypeScript
- Vite as build tool
- TailwindCSS (with shadcn/ui CSS variable system)
- React Router for client-side routing

## Color Palette (Light Theme)

Uses CSS custom properties via `hsl(var(--name))` pattern:

| Role | CSS Variable | Value |
|------|-------------|-------|
| Background | `--background` | `0 0% 100%` (pure white) |
| Foreground | `--foreground` | `0 0% 0%` (pure black) |
| Card | `--card` | `0 0% 100%` (white) |
| Border | `--border` | `0 0% 89.8%` (light gray hairline) |
| Muted | `--muted` | `0 0% 96.1%` (off-white) |
| Muted Foreground | `--muted-foreground` | `0 0% 45.1%` (medium gray) |
| Accent | `--accent` | Single brand color for all highlights |

**High contrast black-on-white** is the foundation. The accent color is used only for:
highlighted hero text, trend keywords, pulsing indicator dots, section comment markers
(`// Section 01`), and important news badges. Never use multiple accent colors.

## Typography

| Role | Style |
|------|-------|
| Hero title | 10-12rem desktop / 15vw mobile, **bold**, tracking `-0.04em`, sans-serif |
| Section meta | Monospace, 10-11px, uppercase, letter-spacing `0.25-0.4em`, reduced opacity |
| Body text | System sans-serif, 14-18px, relaxed line height |
| Numbers/Stats | Monospace, large, used as visual anchors |
| Section markers | `// Section 01 · Title` pattern (monospace, accent-colored `//`) |

**No serif fonts anywhere** — this is a hard rule.

## Layout Components

### 1. Top Chrome (Fixed Header)

- Minimal fixed bar at the very top
- Left: pulsing accent dot + site name + edition number
- Right: date range
- Monospace, tiny uppercase text, low opacity
- `pointer-events: none` (decorative, not interactive)

### 2. Hero Section

- Full viewport height (`min-h-screen`)
- Subtle dot grid background:
  ```css
  background-image: radial-gradient(hsl(var(--foreground) / 0.18) 1px, transparent 1.4px);
  background-size: 22px 22px;
  ```
- Small kicker: `// Issue EP 1 · AI Industry Weekly`
- Massive title with per-character rise animation (staggered `animationDelay`)
- Accent-colored characters for emphasis (e.g. "周报" in accent)
- 12-column grid below: lead paragraph (col-span-7) + meta stats (col-span-5)
- `AnimatedCounter` components with monospace labels
- Scroll-down arrow at bottom

### 3. Ticker Bar

- Horizontal auto-scrolling headlines
- Bordered top and bottom (`border-y border-border`)
- Each headline: accent dot `●` + index number + text
- CSS `animate-marquee` for infinite horizontal scroll
- Edge fade gradients on left and right

### 4. Core Trends Section

- Section meta: `// Section 00 · Core Trends`
- Large heading (4xl-7xl): trendsLead with accent-colored words
- Supporting paragraph in muted foreground
- Trend rows as a divided list (`divide-y divide-border border-y border-border`)
- Each row: large number (accent), title, keyword pill, description
- Footer caption strip with stats

### 5. News Sections

- One `<section>` per category
- Giant decorative section number in background (24rem, 4% opacity)
- Section meta header: `// Section {index} · {subtitle}`
- Large section title (4xl-6xl)
- News cards in responsive grid
- Each card:
  - Top index strip: index + date (mono, uppercase) + tag badge + highlight badge
  - Title in bold
  - Summary in muted foreground
  - Source attribution at bottom
  - Highlighted cards: accent border + subtle accent background tint (`bg-accent/[0.04]`)
  - Hover: `translateY(-0.5)` lift
  - Links to sourceUrl when available

### 6. Side Navigation

- Fixed vertical nav on left edge
- Dot indicators for each section
- Active section highlighted with accent color

### 7. Footer

- Credits and data source attribution

## Animations & Interactions

| Animation | Technique |
|-----------|-----------|
| Reveal on scroll | `opacity: 0; translateY(24px)` → visible (IntersectionObserver `useReveal` hook) |
| Character rise | Hero title chars stagger in with `animationDelay` |
| Ticker marquee | Infinite horizontal CSS animation (`animate-marquee`) |
| Counter animation | Stats count up on reveal |
| Hover lift | Cards `translateY(-0.5)` on hover |
| Pulse dot | Small accent dot with soft pulse keyframes |

## Responsive Breakpoints

| Breakpoint | Layout |
|------------|--------|
| < 768px | Single column, hero text 15vw, stacked |
| 768-1024px | 2-column news grid |
| > 1024px | 3-column news grid, 12-col editorial grid |

## Internationalization (i18n)

- Default language: Chinese (zh)
- Toggle to English (en) via button
- Language state in React context
- All L10n fields rendered based on current language
- Helper: `t(l10n: L10n): string`

## Component File Structure

```
src/
├── components/report/
│   ├── TopChrome.tsx
│   ├── Hero.tsx
│   ├── AnimatedCounter.tsx
│   ├── Ticker.tsx
│   ├── Trends.tsx
│   ├── ReportSection.tsx
│   ├── NewsCard.tsx
│   ├── SideNav.tsx
│   └── Footer.tsx
├── hooks/
│   └── use-reveal.ts
├── lib/
│   ├── types.ts
│   ├── archives.ts
│   ├── i18n.tsx
│   └── utils.ts
└── pages/
    ├── Home.tsx
    ├── Archive.tsx
    └── IssueDetail.tsx
```
