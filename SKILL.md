---
name: ai-news-report
description: >
  Generate an AI industry news report website. Fetches real-time AI news from the
  aihot public API for any user-specified time period, then builds a complete,
  responsive, bilingual (Chinese/English) news report website following Swiss
  International Style design (white background, extreme typographic contrast, single
  accent color, grid layout, hairline dividers). Trigger when the user says: "generate
  AI news report", "生成 AI 资讯报告", "帮我做一个 AI 新闻网站", "create AI news website
  for last week", "拉取最近一周的 AI 新闻", "make an AI report from May 1 to May 15",
  or any request involving collecting AI news and presenting them as a styled website.
when_to_use: >
  Use when the user wants to create a website that displays AI industry news for
  a specific time period. Also use for: "更新周报", "新一期周报", "weekly update",
  "帮我看看这周 AI 圈发生了什么", "AI news digest", "AI 行业动态".
---

# AI News Report Generator

Generate a complete AI industry news report website from any time period. The site
follows **Swiss International Style** (瑞士国际主义) design principles: pure white
background, extreme black typography contrast, single accent color, grid-based layout,
hairline dividers, monospace meta labels. Responsive, bilingual (zh/en), with animated
ticker headlines, core trend analysis, and categorized news cards.

## Data Source: aihot API

All data comes from [aihot.virxact.com](https://aihot.virxact.com) — a free, public
AI news aggregation service. No API key required.

**Required User-Agent** (all requests must include this):

```
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36 aihot-skill/0.2.0
```

### Endpoints

| Endpoint | Purpose |
|----------|---------|
| `GET /api/public/daily` | Latest daily digest |
| `GET /api/public/daily/{YYYY-MM-DD}` | Specific date's digest |
| `GET /api/public/dailies` | Archive index of all dailies |
| `GET /api/public/items?mode=selected&since={ISO}&take=100` | Batch fetch selected items since a date |

### Response Format — Daily Digest

```json
{
  "date": "2026-05-14",
  "generatedAt": "2026-05-14T00:00:38.741Z",
  "sections": [
    {
      "label": "模型发布/更新",
      "items": [
        {
          "title": "News title",
          "summary": "Summary text",
          "sourceUrl": "https://...",
          "sourceName": "Source name"
        }
      ]
    }
  ]
}
```

### Response Format — Items Endpoint

```json
{
  "items": [
    {
      "title": "News title",
      "summary": "Summary text",
      "category": "ai-models",
      "sourceUrl": "https://...",
      "sourceName": "Source name",
      "publishedAt": "2026-05-14T08:00:00Z"
    }
  ]
}
```

Category values: `ai-models` | `ai-products` | `industry` | `paper` | `tip`

---

## Data Model

Use these TypeScript types in `src/lib/types.ts`:

```typescript
/** Bilingual text — all user-facing strings must use this */
export interface L10n {
  zh: string;
  en: string;
}

/** A single news item */
export interface NewsItem {
  id: string;
  date: string;                // YYYY-MM-DD
  title: L10n;
  summary: L10n;
  source: L10n;
  url?: string;                // from aihot sourceUrl
  tag?: L10n;                  // optional category tag
  highlight?: boolean;         // mark 1-2 most important per section
  extra?: L10n;                // optional extra context
}

/** A news section (one category) */
export interface ReportSection {
  id: string;                  // "models" | "products" | "industry" | "research"
  index: string;               // "01" | "02" | "03" | "04"
  title: L10n;
  subtitle?: L10n;
  items: NewsItem[];
}

/** A core trend insight */
export interface CoreTrend {
  no: string;                  // "01", "02", "03"
  title: L10n;
  keyword: L10n;
  desc: L10n;
}

/** Report metadata */
export interface ReportMeta {
  title: string;
  description: L10n;
  keywords: string[];
  stats: Array<{ label: L10n; value: string }>;
}

/** A complete report issue */
export interface WeeklyIssue {
  id: string;                  // slug, e.g. "ep1"
  edition: string;             // display name, e.g. "EP 1"
  range: string;               // e.g. "2026 / 05 / 11 — 05 / 17"
  publishedAt: string;         // ISO date, e.g. "2026-05-18"
  heroLead: L10n;              // hero section lead text
  heroLeadAccent: L10n;        // hero section accent/highlight text
  trendsLead: L10n;            // trends section lead text
  meta: ReportMeta;
  coreTrends: CoreTrend[];     // 3-4 trends
  sections: ReportSection[];   // 3-4 categorized sections
  tickerHeadlines: L10n[];     // 8-10 scrolling headlines
}
```

Data is stored in `src/lib/archives.ts`:

```typescript
import type { WeeklyIssue } from './types';

const ep1: WeeklyIssue = { /* ... */ };

// Newest first
export const archives: WeeklyIssue[] = [ep1];
```

---

## Website Design Specification

### Tech Stack
- React 18+ with TypeScript
- Vite as build tool
- TailwindCSS (with shadcn/ui CSS variable system)
- React Router for client-side routing

### Visual Style: Swiss International Style (瑞士国际主义)

The design follows **Swiss International Style** principles — information-driven,
grid-based, extreme typographic contrast, minimal decoration. Inspired by Massimo
Vignelli, Josef Müller-Brockmann, and *Helvetica Forever*.

**Core design principles** (violating any one will break the aesthetic):

1. **Single accent color** — one brand highlight used sparingly, never multiple colors
2. **Extreme type size contrast** — hero title vs body ratio >= 8:1
3. **Sans-serif only** — system sans-serif + monospace for meta; no serif fonts
4. **Flat and direct** — no gradients, no shadows, no rounded corners on content elements
5. **Grid supremacy** — all elements align to a grid system, asymmetric whitespace
6. **Hairline dividers** — 1px borders as structural separators
7. **Dot grid decoration only in hero** — content pages stay clean

### Color Palette (Light Theme)

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

### Typography

- **Hero title**: Extremely large (10-12rem desktop, 15vw mobile), **bold**, tight tracking (`-0.04em`), sans-serif
- **Section meta labels**: Monospace, 10-11px, uppercase, wide letter-spacing (`0.25-0.4em`), reduced opacity — the "Swiss comment" pattern: `// Section 01 · Title`
- **Body text**: System sans-serif stack, 14-18px, relaxed line height
- **Numbers/Stats**: Monospace, large weight, used as visual anchors
- **No serif fonts anywhere** — this is a hard rule

### Page Structure & Routes

| Route | Page | Content |
|-------|------|---------|
| `/` | Home | Latest issue — full report view |
| `/archive` | Archive | Grid of all past issues as cards |
| `/issue/:id` | Issue Detail | Specific issue view (same layout as home) |

### Layout Components

#### 1. Top Chrome (Fixed Header)
- Minimal fixed bar at the very top
- Left: pulsing accent dot + site name + edition number
- Right: date range
- Monospace, tiny uppercase text, low opacity
- Pointer-events none (decorative, not interactive)

#### 2. Hero Section
- Full viewport height (`min-h-screen`)
- Subtle dot grid background pattern:
  ```css
  background-image: radial-gradient(hsl(var(--foreground) / 0.18) 1px, transparent 1.4px);
  background-size: 22px 22px;
  ```
- Small kicker line: `// Issue EP 1 · AI Industry Weekly`
- Massive title with per-character rise animation (staggered `animationDelay`)
- Accent-colored characters for emphasis (e.g. "周报" in accent color)
- Two-column grid below: lead paragraph (col-span-7) + meta stats (col-span-5)
- Meta stats as `AnimatedCounter` components with monospace labels
- Scroll-down arrow indicator at bottom

#### 3. Ticker Bar
- Horizontal auto-scrolling headlines from `tickerHeadlines`
- Bordered top and bottom (`border-y border-border`)
- Each headline: accent dot + index number + text
- CSS `animate-marquee` for infinite horizontal scroll
- Edge fade gradients on left and right

#### 4. Core Trends Section
- Section meta header: `// Section 00 · Core Trends` (monospace, uppercase)
- Large heading (4xl-7xl): trendsLead text with accent-colored words
- Supporting paragraph in muted foreground
- Trend rows as a divided list (`divide-y divide-border border-y border-border`)
- Each row: large number (accent color), title, keyword pill, description
- Last row optionally has accent styling
- Footer caption strip with stats

#### 5. News Sections
- One `<section>` per category (models / products / industry / research)
- Giant decorative section number in background (24rem, 4% opacity)
- Section meta header with `// Section {index} · {subtitle}`
- Large section title (4xl-6xl)
- News cards in responsive grid
- Each card:
  - Top index strip: index number + date (mono, uppercase) + optional tag badge + highlight badge
  - Title in bold
  - Summary text in muted foreground
  - Source attribution at bottom
  - Highlighted cards: accent border, subtle accent background tint
  - Hover: translateY(-0.5) lift effect
  - Links to sourceUrl when available

#### 6. Side Navigation
- Fixed vertical nav on the left edge
- Dot indicators for each section
- Active section highlighted with accent color

#### 7. Footer
- Credits and data source attribution

### Animations & Interactions

- **Reveal on scroll**: Elements start `opacity: 0; translateY(24px)` and transition to visible
  when entering viewport (IntersectionObserver-based `useReveal` hook)
- **Character rise**: Hero title characters animate in with staggered delays
- **Ticker marquee**: Infinite horizontal CSS animation
- **Counter animation**: Stats count up on reveal
- **Hover lift**: Cards translate up slightly on hover
- **Pulse dot**: Small accent-colored dot with soft pulse animation

### Responsive Breakpoints
- Mobile: < 768px — single column, smaller hero text (15vw), stacked layout
- Tablet: 768-1024px — 2-column news grid
- Desktop: > 1024px — 3-column news grid, 12-column editorial grid layout

### Internationalization (i18n)
- Default language: Chinese (zh)
- Toggle to English (en) via button in header
- Language state stored in React context
- All L10n fields rendered based on current language
- Helper: `t(l10n: L10n): string` returns text for current language

---

## Generation Workflow

When the user requests an AI news report for a specific time period, follow these steps:

### Step 1: Parse Time Range

Interpret the user's natural language time specification:

| User says | Resolved range |
|-----------|---------------|
| "上周" / "last week" | Previous Monday → Sunday |
| "这周" / "this week" | This Monday → today |
| "最近3天" / "last 3 days" | (today - 3) → yesterday |
| "5月1日到5月15日" / "May 1 to May 15" | 2026-05-01 → 2026-05-15 |
| "本月" / "this month" | 1st of current month → today |

Calculate the date list (YYYY-MM-DD for each day in range).

### Step 2: Fetch Data from aihot API

```bash
UA="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36 aihot-skill/0.2.0"

# Option A: Fetch each day's daily digest
for date in YYYY-MM-DD YYYY-MM-DD ...; do
  curl -s -H "User-Agent: $UA" "https://aihot.virxact.com/api/public/daily/$date"
done

# Option B: Batch fetch (recommended for ranges > 3 days)
curl -s -H "User-Agent: $UA" \
  "https://aihot.virxact.com/api/public/items?mode=selected&since=YYYY-MM-DDT00:00:00Z&take=200"
```

Use **both** methods and merge results for maximum coverage.

### Step 3: Filter & Curate

From the fetched data:

| Category | Section ID | Target count |
|----------|-----------|-------------|
| Model Releases & Updates | `models` | 8-10 items |
| Product & Platform Updates | `products` | 8-10 items |
| Industry & Business | `industry` | 6-8 items |
| Research & Papers | `research` | 4-6 items |

**Total: 30-40 curated items.**

For each item:
- Translate title and summary to both zh and en (natural, professional translations)
- Preserve the original `sourceUrl`
- Mark 1-2 most important items per section as `highlight: true`
- Assign appropriate tags

### Step 4: Generate Report Content

Create the `WeeklyIssue` object:

1. **Edition**: "EP {N}" — auto-increment from existing archives
2. **Range**: Format as "YYYY / MM / DD — MM / DD"
3. **heroLead + heroLeadAccent**: One compelling sentence summarizing the period's biggest stories. Must be unique.
4. **trendsLead**: Brief intro to the trends section. Must be unique.
5. **coreTrends**: 3-4 trend insights synthesized from the news. Each with a catchy keyword.
6. **meta**: Title, unique description, relevant keywords, stats (item count, trend count, source count)
7. **sections**: The 3-4 categorized news sections
8. **tickerHeadlines**: 8-10 short headline phrases for the scrolling ticker

### Step 5: Build the Website

If this is a new project (no existing site structure), scaffold the full website:

1. Set up Vite + React + TypeScript + TailwindCSS (with shadcn/ui CSS variables)
2. Create type definitions in `src/lib/types.ts`
3. Create data file `src/lib/archives.ts`
4. Build components following the editorial design spec above:
   - `src/components/report/TopChrome.tsx`
   - `src/components/report/Hero.tsx`
   - `src/components/report/Ticker.tsx`
   - `src/components/report/Trends.tsx`
   - `src/components/report/ReportSection.tsx`
   - `src/components/report/NewsCard.tsx`
   - `src/components/report/SideNav.tsx`
   - `src/components/report/Footer.tsx`
   - `src/components/report/AnimatedCounter.tsx`
5. Create `src/hooks/use-reveal.ts` for scroll-triggered reveal animations
6. Create pages:
   - `src/pages/Home.tsx` → renders latest issue
   - `src/pages/Archive.tsx` → grid of all issues
   - `src/pages/IssueDetail.tsx` → renders specific issue by ID
7. Set up React Router in `src/App.tsx`
8. Create i18n context in `src/lib/i18n.tsx`
9. Configure `tailwind.config.ts` with the CSS variable color system and animation keyframes

If the site already exists, just add the new `WeeklyIssue` to the front of the `archives` array.

### Step 6: Verify

- Ensure the build passes (`npm run build` succeeds)
- Check that all L10n fields have both `zh` and `en` values
- Verify no duplicate IDs across issues
- Confirm news items have valid `url` fields where available

---

## Content Rules

1. **Every issue must be unique**: heroLead, heroLeadAccent, trendsLead, meta.description, and tickerHeadlines must all be generated fresh from the actual news content. Never copy from another issue.
2. **Sections are flat**: 3-4 top-level categories only (models / products / industry / research). No sub-categories.
3. **L10n is mandatory**: Every user-facing text field uses `{ zh: "中文", en: "English" }`. English translations should be natural and professional, not literal.
4. **Source attribution**: Every NewsItem should include the `url` field (from aihot's `sourceUrl`) when available.
5. **Highlights**: Mark 1-2 most significant items per section with `highlight: true`.

---

## Error Handling

- **aihot API returns empty data**: Inform the user that no news was found for the specified period. Suggest trying a different date range.
- **Partial data**: If some days have no data, proceed with available data and note the gaps.
- **Build failure**: Check TypeScript types match, ensure all imports are correct, verify TailwindCSS classes are valid.

## What This Skill Does NOT Do

- Does not deploy or publish the website (user handles this via Enter.pro publish)
- Does not require any API keys or authentication
- Does not modify existing issues — only adds new ones
