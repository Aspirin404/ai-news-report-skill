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
| Accent | `--accent` | One of the four Swiss accent colors below |

### Accent Color System

Choose **exactly one** accent color per site. Default is Klein Blue (IKB).

| Name | Hex | HSL | When to use |
|------|-----|-----|-------------|
| **Klein Blue (IKB)** | `#002FA7` | `222 100% 33%` | Default. Authoritative, intellectual |
| Lemon Yellow | `#FFD500` | `49 100% 50%` | Energetic, optimistic |
| Lemon Green | `#C5E803` | `71 97% 46%` | Fresh, innovative |
| Safety Orange | `#FF6B35` | `16 100% 60%` | Urgent, bold |

If the user does not specify a preference, use **Klein Blue** (`#002FA7`).
Never mix multiple accent colors in one site.

**High contrast black-on-white** is the foundation. The accent color is used only for:
highlighted hero text, trend keywords, pulsing indicator dots, section comment markers
(`// Section 01`), and important news badges.

## Typography

### Font Stack (Required)

Load via Google Fonts in `index.html`:

```html
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<link
  href="https://fonts.googleapis.com/css2?family=Inter:wght@200;300;400;500;600&family=Noto+Sans+SC:wght@200;300;400;500&family=JetBrains+Mono:wght@300;400;500&display=swap"
  rel="stylesheet"
/>
```

| Role | Font | Fallback |
|------|------|----------|
| Sans-serif (latin) | **Inter** | system-ui, sans-serif |
| Sans-serif (CJK) | **Noto Sans SC** | "PingFang SC", "Microsoft YaHei", sans-serif |
| Monospace | **JetBrains Mono** | "SF Mono", "Fira Code", monospace |

Key weights used:
- **200 (ExtraLight)**: Hero titles at extreme sizes (5rem+)
- **300 (Light)**: Section titles, large display text, stats numbers
- **400 (Regular)**: Body text, descriptions
- **500 (Medium)**: Emphasis text, card titles

Configure in Tailwind:

```ts
fontFamily: {
  sans: ['"Inter"', '"Noto Sans SC"', 'system-ui', 'sans-serif'],
  mono: ['"JetBrains Mono"', '"SF Mono"', '"Fira Code"', 'monospace'],
}
```

Body CSS:

```css
body {
  font-feature-settings: "ss01", "cv11";
  -webkit-font-smoothing: antialiased;
}
```

- `ss01`: Inter stylistic set (open digits)
- `cv11`: Inter character variant (single-storey a)

### Type Scale

| Role | Style |
|------|-------|
| Hero title | 4-6rem desktop / 10vw mobile, **font-weight: 300 (Light)**, tracking `-0.03em`, Inter/Noto Sans SC. Must NOT exceed 6rem. **Larger type = lighter weight — this is the Swiss core rule.** |
| Section titles | 2-4rem, font-weight 300 (Light), tracking `-0.03em`. Append accent-colored `.` dot at end (e.g. "模型发布.") |
| Section meta | JetBrains Mono, 10-11px, font-weight 400, uppercase, letter-spacing `0.25-0.4em`, reduced opacity |
| Card titles | Inter/Noto Sans SC, 18-22px, font-weight 500 (Medium) — heavier than body but NOT bold |
| Card body text | Inter/Noto Sans SC, 14-16px, font-weight 400 (Regular), line-height 1.7-1.8, color `hsl(var(--muted-foreground))` |
| Card source/footer | JetBrains Mono, 11-12px, font-weight 400, uppercase, letter-spacing `0.15em`, muted color |
| Numbers/Stats | JetBrains Mono, large, font-weight 300 (Light), used as visual anchors |
| Section markers | `// Section 01 · Title` pattern (JetBrains Mono, accent-colored `//`) |

**Key rule: the bigger the text, the lighter the weight.** Hero and section titles must use weight 300 (Light), never 600/700/bold. This creates the elegant Swiss contrast: extreme size + hairline strokes. Bold at display sizes looks heavy and amateurish.

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
- Small kicker: `// Issue EP 1 · AI Industry Report` (adapt title based on time range — e.g. "Daily", "Report", "Digest", not always "Weekly")
- Hero title: **4-6rem max** (NOT 10rem+). Bold, tight tracking, with accent-colored emphasis characters
- Title text should reflect the actual time range (e.g. "AI 日报", "AI 资讯", "AI 月报"), NOT hardcoded as "AI 周报" or "AI Weekly"
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

- Section meta: `// SECTION 00 · CORE TRENDS` (mono, uppercase, wide tracking)
- Large heading (4xl-7xl), font-weight 300 (Light): trendsLead with accent-colored `.` dot at end
- Supporting paragraph in muted foreground
- Trend rows as a divided list (`divide-y divide-border border-y border-border`)
- Each row: large number (accent), title, keyword pill, description
- Footer caption strip with stats

### 5. News Sections

- One `<section>` per category
- **Section header row**: Left = `// SECTION {index} · {SUBTITLE}` (mono, uppercase, wide tracking). Right = page counter `{current} / {total}` (mono)
- **Section title**: 4xl-6xl, font-weight 300 (Light), accent-colored dot `.` appended at end of title (e.g. "模型发布.")
- **Decorative background number**: Giant `01`/`02`/`03` (20-24rem), opacity 4-6%, font-weight 200, positioned behind right side. Creates depth layering.
- **Right sidebar meta** (desktop only): "ITEMS IN THIS SECTION" (mono, tiny, uppercase) + item count as large mono number
- News cards in responsive grid (2-3 columns)

#### News Card Structure

Each card is a bordered rectangle with clear visual hierarchy:

```
┌─────────────────────────────────────────┐
│ ● 01 · 05-17              KEY           │  ← top strip (mono, uppercase)
│                                         │
│ Card Title Here                         │  ← weight 500 (Medium), 18-22px
│                                         │
│ Card body text lorem ipsum dolor sit    │  ← weight 400 (Regular), 14-16px
│ amet, line-height 1.7-1.8, muted color │     line-height 1.7-1.8
│                                         │
│ SOURCE NAME              READ ↗         │  ← mono, uppercase, 11-12px
└─────────────────────────────────────────┘
```

- **Top strip**: accent dot `●` + zero-padded index + `·` + date (MM-DD) on left; `KEY` badge on right for highlighted items (mono, uppercase)
- **Title**: font-weight 500 (Medium), NOT bold/700. Heavier than body but still medium.
- **Body**: font-weight 400, muted foreground, relaxed line-height (1.7-1.8)
- **Footer**: source name (mono, uppercase, muted) on left + "READ ↗" link on right (mono, uppercase)
- **Highlighted cards**: accent-colored left border (3-4px solid) — NOT full background tint
- **Normal cards**: thin border (`border border-border`)
- **Hover**: subtle `translateY(-2px)` lift + shadow
- Links to sourceUrl when available

### 6. Side Navigation

- Fixed vertical nav on left edge
- Dot indicators for each section
- Active section highlighted with accent color

### 7. Footer

- Simple credits (e.g. "Built with Enter.pro")
- Do NOT mention the data source (aihot) in the footer or anywhere user-facing

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
