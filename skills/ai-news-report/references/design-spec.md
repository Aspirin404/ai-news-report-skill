# Design Specification — Swiss International Style

## Visual Philosophy

The design follows **Swiss International Style** (瑞士国际主义) principles.
Inspired by Massimo Vignelli, Josef Müller-Brockmann, and *Helvetica Forever*.

### Core Design Principles

1. **Single accent color** — one brand highlight used sparingly, never multiple colors
2. **Extreme type size contrast** — hero title vs body ratio >= 8:1
3. **Sans-serif only** — system sans-serif + monospace for meta; no serif fonts anywhere
4. **Flat and direct** — no gradients, no shadows, no rounded corners on content elements
5. **Grid supremacy** — 12-column grid, asymmetric whitespace
6. **Hairline dividers** — 1px borders as structural separators
7. **Dot grid decoration only in hero** — content sections stay clean

## Tech Stack

- React 18+ with TypeScript
- Vite as build tool
- TailwindCSS + tailwindcss-animate (with shadcn/ui CSS variable system)
- React Router for client-side routing

## Color Palette

Uses CSS custom properties via `hsl(var(--name))` pattern.

**Background is warm off-white, NOT pure white:**

```css
:root {
  --background: 40 22% 96%;
  --foreground: 220 14% 11%;
  --card: 40 22% 96%;
  --card-foreground: 220 14% 11%;
  --secondary: 40 14% 92%;
  --secondary-foreground: 220 14% 11%;
  --muted: 40 14% 92%;
  --muted-foreground: 220 8% 42%;
  --accent: 230 95% 56%;          /* Klein Blue — default */
  --accent-foreground: 0 0% 100%;
  --border: 40 8% 84%;
  --input: 40 8% 84%;
  --ring: 230 95% 56%;
  --radius: 0.25rem;
}
```

### Accent Color System

Choose **exactly one** accent color per site. Default is Klein Blue.

| Name | Hex | HSL | When to use |
|------|-----|-----|-------------|
| **Klein Blue (IKB)** | `#002FA7` | `230 95% 56%` | Default. Authoritative, intellectual |
| Lemon Yellow | `#FFD500` | `49 100% 50%` | Energetic, optimistic |
| Lemon Green | `#C5E803` | `71 97% 46%` | Fresh, innovative |
| Safety Orange | `#FF6B35` | `16 100% 60%` | Urgent, bold |

If the user does not specify, use **Klein Blue**.
Never mix multiple accent colors in one site.

The accent color is used for: hero accent text, section title dots, trend numbers, pulsing indicator dot, `//` markers, KEY badge text, tag pills on highlighted cards, card borders on highlighted cards, hover states.

## Typography

### Font Stack (Required)

```html
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Noto+Sans+SC:wght@300;400;500;700&family=JetBrains+Mono:wght@300;400;500&display=swap" rel="stylesheet" />
```

| Role | Font | Fallback |
|------|------|----------|
| Sans-serif (latin) | **Inter** | system-ui, sans-serif |
| Sans-serif (CJK) | **Noto Sans SC** | system-ui, sans-serif |
| Monospace | **JetBrains Mono** | ui-monospace, monospace |

Tailwind config:

```ts
fontFamily: {
  sans: ['Inter', 'Noto Sans SC', 'system-ui', 'sans-serif'],
  mono: ['JetBrains Mono', 'ui-monospace', 'monospace'],
}
```

Body CSS:

```css
body {
  font-feature-settings: "ss01", "cv11";
  -webkit-font-smoothing: antialiased;
}
```

- `ss01`: Inter stylistic set (open digits — 6, 9 more geometric)
- `cv11`: Inter character variant (single-storey `a`)

### Type Scale

| Role | Tailwind Classes | Notes |
|------|-----------------|-------|
| Hero title | `text-4xl md:text-6xl lg:text-7xl font-light leading-[1.05] tracking-[-0.03em]` | Weight 300 (Light). Max 25 zh chars. Accent `.` is the ONLY ending punctuation — never "。." |
| Section titles | `text-4xl font-light leading-[1.05] tracking-[-0.03em] md:text-6xl lg:text-7xl` | Weight 300. Accent `.` replaces any Chinese "。" — never both. |
| Trend heading | `max-w-5xl text-4xl font-light leading-[1.05] tracking-[-0.03em] md:text-6xl lg:text-7xl` | Same as section titles |
| Trend numbers | `text-5xl md:text-7xl font-light leading-none tracking-tighter` | accent color or foreground, hover → accent |
| Trend titles | `text-xl font-medium leading-snug tracking-tight md:text-2xl` | Weight 500 |
| Stats/Numbers | `font-mono text-6xl font-light tracking-[-0.04em] md:text-7xl` | JetBrains Mono, weight 300 |
| Meta/labels | `font-mono text-[10px] uppercase tracking-[0.25em-0.3em] text-foreground/50` | All chrome text |
| Card titles | `text-xl font-semibold leading-snug tracking-tight md:text-2xl` | Weight 600 |
| Card body | `mt-3 text-[15px] leading-relaxed text-foreground/65 md:text-base md:leading-[1.7]` | Weight 400 |
| Card footer | `font-mono text-[10px] uppercase tracking-[0.25em] text-foreground/55` | Source + READ link |
| Decorative bg nums | `font-mono font-light text-[16rem] md:text-[24rem] text-foreground/[0.07] tracking-tighter` | Behind section content |
| Footer tagline | `text-5xl font-light leading-[1.05] tracking-[-0.03em] md:text-7xl lg:text-[9rem]` | Weight 300, like section titles |

**Key rule: the bigger the text, the lighter the weight.** All large display text (hero, section titles, footer tagline, stats) uses `font-light` (300). Only card titles use semibold (600) at their smaller size.

**No serif fonts anywhere.**

## Layout Components

### 1. Top Chrome (Fixed Header)

```tsx
<div className="pointer-events-none fixed inset-x-0 top-0 z-50">
  <div className="absolute inset-0 bg-background/70 backdrop-blur-md" />
  <div className="relative mx-auto flex max-w-[1400px] items-center justify-between px-6 pt-5 pb-3 font-mono text-[10px] uppercase tracking-[0.25em] text-foreground/70 md:px-10">
    <div className="flex items-center gap-3">
      <span className="h-1.5 w-1.5 animate-pulse-soft rounded-full bg-accent" />
      <span>{brand}</span>
    </div>
    <div className="pointer-events-auto flex items-center gap-2">
      {/* Archive link + Lang switch */}
    </div>
  </div>
</div>
```

- Backdrop blur, semi-transparent background
- Left: pulsing accent dot + brand name
- Right: archive link + language toggle (these ARE interactive, `pointer-events-auto`)

### 2. Hero Section

- **NOT** full viewport height — use `pt-24 pb-16 md:pt-32 md:pb-24` padding instead of `min-h-screen`
- Subtle dot grid background (35% opacity wrapper):
  ```css
  background-image: radial-gradient(hsl(var(--foreground) / 0.18) 1px, transparent 1.4px);
  background-size: 22px 22px;
  ```
- Bottom fade gradient: `bg-gradient-to-b from-transparent to-background`
- **Hero title**: `font-light leading-[1.05] tracking-[-0.03em]`
  - Size: `text-4xl md:text-6xl lg:text-7xl`
  - Per-character rise animation with staggered delay
  - Second line has accent-colored portion + accent `.` at end
- Hero section should NOT be `min-h-screen`. Use `pt-24 pb-16 md:pt-32 md:pb-24` — avoid excessive empty space above the title.
- 12-column grid below: lead paragraph (col-span-7, `text-lg md:text-xl leading-[1.7] text-foreground/80`) + meta (col-span-5, border-left)
- **Stats row**: `grid-cols-2 md:grid-cols-4`, each stat has:
  - Numbered prefix (`01`, `02`...) + hairline
  - `AnimatedCounter` in `font-mono text-6xl font-light tracking-[-0.04em] md:text-7xl`
  - Mono label below (`text-[11px] uppercase tracking-[0.2em] text-foreground/60`)
- **Keywords row**: mono tags that link to sections, with `→` on hover

### 3. Ticker Bar

```tsx
<div className="relative overflow-hidden border-y border-border bg-background py-3">
  <div className="flex w-max animate-marquee whitespace-nowrap">
    {items.map(item => (
      <div className="flex items-center gap-4 px-6 font-mono text-xs uppercase tracking-[0.25em] text-foreground/70">
        <span className="text-accent">●</span>
        <span className="text-foreground/40">{index}</span>
        <span>{headline}</span>
      </div>
    ))}
  </div>
  {/* Edge fade gradients */}
</div>
```

- Duplicated array for infinite scroll illusion
- `animate-marquee`: 40s linear infinite
- Edge fades: `w-24 bg-gradient-to-r from-background to-transparent`

### 4. Core Trends Section

- `border-t border-border bg-background py-24 md:py-32`
- Section meta: `<span className="text-accent">// </span>Section 00 · Core Trends` + page counter right
- Heading: `font-light` large text + accent words + accent `.`
- Supporting paragraph: `text-base leading-relaxed text-foreground/60 md:text-lg`
- **Trend rows**: `divide-y divide-border border-y border-border`, 12-col grid per row:
  - col-span-2: Large number (`text-5xl md:text-7xl font-light tracking-tighter`), last row always accent, others hover → accent
  - col-span-3: Keyword label (mono, tiny) + title (`text-xl font-medium md:text-2xl`)
  - col-span-7: Description (`text-sm md:text-base leading-[1.7] text-foreground/70`)
- Footer caption: `font-mono text-[10px] tracking-[0.3em] text-foreground/40`

### 5. News Sections

- `border-t border-border bg-background py-24 md:py-32`
- **Decorative background number**: `absolute right-6 top-12 font-mono font-light text-[16rem] md:right-10 md:text-[24rem] text-foreground/[0.07] tracking-tighter`
- **Section meta header**: `mb-12 md:mb-16`, accent `//` + "Section {index} · {subtitle}" left, page counter right
- **Title area**: 12-col grid
  - col-span-8: `text-4xl font-light leading-[1.05] tracking-[-0.03em] md:text-6xl lg:text-7xl` + accent `.`
  - col-span-4 (right): "Items in this section" (mono label) + padded count (`font-mono text-2xl font-light md:text-3xl`) + accent ` ·`
- **Animated divider**: hairline that slides in from left on reveal (`translate-x-0` / `-translate-x-full`)

#### News Card Grid

```tsx
<div className="mt-12 grid grid-cols-1 gap-4 md:mt-16 md:grid-cols-2 lg:grid-cols-3 grid-flow-dense [&>*]:h-full">
```

- Standard responsive grid: 1 → 2 → 3 columns
- **Highlighted cards span 2 columns**: `md:col-span-2` (via `grid-flow-dense`)
- This makes KEY items naturally larger without a separate layout

#### News Card Component

**Wrapper:**
```tsx
<div className={cn(
  "group relative flex h-full flex-col border bg-background p-6 transition-all duration-300 ease-out",
  highlight
    ? "border-accent bg-accent/[0.04] hover:-translate-y-0.5"
    : "border-border hover:-translate-y-0.5 hover:border-foreground",
)}>
```

**KEY (highlight) cards:**
- `border-accent` — accent-colored border
- `bg-accent/[0.04]` — barely-there accent background tint
- Dot: `bg-accent`
- Tag: `bg-accent text-accent-foreground` (filled accent pill)
- Shows `KEY` text in accent color
- Spans 2 columns (`md:col-span-2`)

**Normal cards:**
- `border-border` — standard gray border
- `bg-background` — no tint
- `hover:border-foreground` — border darkens on hover
- Dot: `bg-foreground/30`
- Tag: `bg-foreground text-background` (inverted black pill)
- Single column

**Card internal layout:**
```
┌─────────────────────────────────────────────┐
│ ● 01 · 05-12    [TAG]              KEY      │  ← top strip, border-b
│                                             │
│ Card Title Here (font-semibold)             │  ← text-xl md:text-2xl
│                                             │
│ Body text text-foreground/65                │  ← text-[15px] md:text-base
│                                             │
│ ┃ Extra context (optional, border-l-2)      │  ← blockquote-style
│                                             │
│─────────────────────────────────────────────│  ← mt-auto border-t
│ SOURCE NAME                      READ ↗     │  ← footer
└─────────────────────────────────────────────┘
```

- **Top strip**: `-mx-6 -mt-6 mb-5 border-b border-border px-6 py-2 font-mono text-[10px] uppercase tracking-[0.25em] text-foreground/50`
  - Left: dot (`h-1 w-1 rounded-full`) + padded index + `·` + date
  - Right: tag pill (filled, not outline) + KEY label
- **Title**: `text-xl font-semibold leading-snug tracking-tight md:text-2xl`
- **Body**: `mt-3 text-[15px] leading-relaxed text-foreground/65 md:text-base md:leading-[1.7]`
- **Extra** (optional): `border-l-2 px-3 py-2 text-xs md:text-sm leading-relaxed`
  - Highlight: `border-accent bg-accent/[0.06] text-foreground/85`
  - Normal: `border-foreground/30 bg-secondary/40 text-foreground/75`
- **Footer**: `mt-auto border-t border-border pt-4`
  - Source: `font-mono text-[10px] uppercase tracking-[0.25em] text-foreground/55`
  - READ link: `font-mono text-[10px] uppercase tracking-[0.25em] text-foreground/70 group-hover:text-accent` + ArrowUpRight icon

### 6. Side Navigation

- `fixed left-3 top-1/2 -translate-y-1/2 hidden lg:block`
- **Line-based indicators** (NOT dots):
  - Active: `w-6 bg-accent` line + accent number
  - Inactive: `w-3 bg-border` line + muted number, hover expands to `w-5`
- Floating label tooltip on hover (absolute positioned, border, bg-background, shadow)

### 7. Footer

- `border-t border-border bg-background`
- Section meta: `// END OF REPORT` + page counter
- 12-col grid: col-span-8 tagline + col-span-4 back-to-top button
- **Tagline**: `text-5xl font-light leading-[1.05] tracking-[-0.03em] md:text-7xl lg:text-[9rem]` — "下周" + accent "见" + accent "."
- Supporting copy below: `text-base leading-[1.7] text-foreground/70 md:text-lg`
- **Back to top button**: bordered, mono text, hover → accent
- **Bottom strip**: copyright left, decorative dots center, "Designed for serious readers" right
- Do NOT mention the data source (aihot) anywhere

## Animations & CSS

All defined in `index.css`:

```css
/* Reveal on scroll */
.reveal { opacity: 0; transform: translateY(24px); transition: opacity 0.8s cubic-bezier(0.22, 1, 0.36, 1), transform 0.8s cubic-bezier(0.22, 1, 0.36, 1); }
.reveal.is-visible { opacity: 1; transform: translateY(0); }

/* Ticker marquee */
@keyframes marquee { 0% { transform: translateX(0); } 100% { transform: translateX(-50%); } }
.animate-marquee { animation: marquee 40s linear infinite; }

/* Pulse dot */
@keyframes pulse-soft { 0%, 100% { opacity: 0.35; transform: scale(1); } 50% { opacity: 1; transform: scale(1.2); } }
.animate-pulse-soft { animation: pulse-soft 2.4s ease-in-out infinite; }

/* Hero char rise */
@keyframes char-rise { 0% { opacity: 0; transform: translateY(110%) rotate(8deg); } 100% { opacity: 1; transform: translateY(0) rotate(0); } }
.char-rise { display: inline-block; opacity: 0; animation: char-rise 0.9s cubic-bezier(0.22, 1, 0.36, 1) forwards; }

/* Scroll progress bar */
.scroll-progress { position: fixed; top: 0; left: 0; height: 2px; width: 100%; background: hsl(var(--accent)); transform-origin: 0 50%; z-index: 60; }

/* Reduced motion */
@media (prefers-reduced-motion: reduce) { .reveal, .char-rise, .animate-marquee { animation: none !important; transition: none !important; opacity: 1 !important; transform: none !important; } }
```

Tailwind keyframes (in `tailwind.config.ts`):
```ts
keyframes: {
  'fade-in-up': { '0%': { opacity: '0', transform: 'translateY(24px)' }, '100%': { opacity: '1', transform: 'translateY(0)' } },
},
animation: {
  'fade-in-up': 'fade-in-up 0.8s cubic-bezier(0.22, 1, 0.36, 1) forwards',
}
```

## useReveal Hook

```ts
export const useReveal = <T extends HTMLElement>(options?: IntersectionObserverInit) => {
  const ref = useRef<T | null>(null);
  const [visible, setVisible] = useState(false);
  useEffect(() => {
    const node = ref.current;
    if (!node) return;
    const observer = new IntersectionObserver(
      ([entry]) => { if (entry.isIntersecting) { setVisible(true); observer.disconnect(); } },
      { threshold: 0.15, rootMargin: "0px 0px -10% 0px", ...options },
    );
    observer.observe(node);
    return () => observer.disconnect();
  }, [options]);
  return { ref, visible };
};
```

## Responsive Breakpoints

| Breakpoint | Layout |
|------------|--------|
| < 768px (mobile) | Single column, hero `text-[12vw]`, stacked grids |
| 768-1024px (md) | 2-column news grid, hero `text-[8rem]` |
| > 1024px (lg) | 3-column news grid, hero `text-[10rem]`, side nav visible |

Max content width: `max-w-[1400px]` with `px-6 md:px-10`.

## Internationalization (i18n)

- Default language: Chinese (zh)
- Toggle to English (en) via TopChrome button
- Language state in React context
- All L10n fields rendered via `t(l10n: L10n): string`
- UI strings stored in separate file (`i18n-strings.ts`), not in data
- English hero title uses slightly smaller size to fit

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
│   ├── ScrollProgress.tsx
│   ├── LangSwitch.tsx
│   ├── ArchiveLink.tsx
│   └── Footer.tsx
├── hooks/
│   └── use-reveal.ts
├── lib/
│   ├── types.ts (or reportData.ts)
│   ├── archives.ts
│   ├── issueContext.tsx
│   ├── i18n.tsx
│   ├── i18n-strings.ts
│   └── utils.ts
└── pages/
    ├── Index.tsx (latest issue)
    ├── Issue.tsx (specific issue by id)
    ├── Archive.tsx (all issues grid)
    └── NotFound.tsx
```
