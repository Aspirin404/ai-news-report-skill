---
name: ai-news-report
description: >
  Generate an AI industry news report website. Fetches real-time AI news from the
  aihot public API for any user-specified time period, then builds a complete,
  responsive, bilingual (Chinese/English) news report website with dark theme and
  modern card-based design. Trigger when the user says: "generate AI news report",
  "生成 AI 资讯报告", "帮我做一个 AI 新闻网站", "create AI news website for last week",
  "拉取最近一周的 AI 新闻", "make an AI report from May 1 to May 15", or any request
  involving collecting AI news and presenting them as a styled website.
metadata:
  version: 1.0.0
  author: Aspirin404
  license: MIT
---

# AI News Report Generator

Generate a complete AI industry news report website from any time period. The site
features a dark-themed, responsive design with bilingual (zh/en) support, animated
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
- TailwindCSS for styling
- React Router for client-side routing

### Color Palette

| Role | Value |
|------|-------|
| Background | `hsl(0, 0%, 4%)` — near-black |
| Surface | `hsl(0, 0%, 8%)` — card backgrounds |
| Surface Hover | `hsl(0, 0%, 12%)` |
| Border | `hsl(0, 0%, 15%)` |
| Primary / Accent | `#E83B6C` — vibrant pink |
| Primary Hover | `#D42F5E` |
| Text Primary | `hsl(0, 0%, 95%)` |
| Text Secondary | `hsl(0, 0%, 65%)` |
| Text Muted | `hsl(0, 0%, 45%)` |

### Page Structure & Routes

| Route | Page | Content |
|-------|------|---------|
| `/` | Home | Latest issue — full report view |
| `/archive` | Archive | Grid of all past issues as cards |
| `/issue/:id` | Issue Detail | Specific issue view (same layout as home) |

### Layout Components

#### 1. Header / Navigation
- Site title: "AI Weekly" or user-customized name
- Language toggle button (zh ↔ en)
- Link to `/archive`
- Sticky top, blurred glass background

#### 2. Hero Section
- Large heading with `heroLead` text, `heroLeadAccent` highlighted in pink
- Edition badge (e.g. "EP 1") and date range
- Meta stats row (e.g. "30+ news", "4 trends", "10 sources")
- Subtle gradient or grid background pattern

#### 3. Ticker Bar
- Horizontal auto-scrolling headlines from `tickerHeadlines`
- Pink left accent bar
- Infinite loop animation with CSS

#### 4. Core Trends Section
- Section lead text from `trendsLead`
- 3-4 trend cards in a grid
- Each card: number badge (01, 02...), title, keyword pill, description
- Pink accent on number and keyword

#### 5. News Sections
- One section per category (models / products / industry / research)
- Section header with index number, title, subtitle
- News items as cards in a responsive grid (1-col mobile, 2-col tablet, 3-col desktop)
- Each card: date, title, summary, source tag, optional highlight badge
- Highlighted cards get a pink left border or glow
- Cards link to `url` (sourceUrl) when available

#### 6. Footer
- "Built with Enter.pro" credit
- Data source attribution: "Data from aihot.virxact.com"

### Responsive Breakpoints
- Mobile: < 640px — single column, stacked layout
- Tablet: 640-1024px — 2-column news grid
- Desktop: > 1024px — 3-column news grid, side-by-side trends

### Animations
- Fade-in on scroll for sections
- Ticker infinite scroll
- Hover lift effect on cards (translateY + shadow)
- Smooth page transitions

### Internationalization (i18n)
- Default language: Chinese (zh)
- Toggle to English (en) via button
- Language state stored in React context or URL parameter
- All L10n fields rendered based on current language
- Helper function: `t(l10n: L10n): string` returns text for current language

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
3. **heroLead + heroLeadAccent**: One compelling sentence summarizing the period's biggest stories. Must be unique to this issue.
4. **trendsLead**: Brief intro to the trends section. Must be unique.
5. **coreTrends**: 3-4 trend insights synthesized from the news. Each with a catchy keyword.
6. **meta**: Title, unique description, relevant keywords, stats (item count, trend count, source count)
7. **sections**: The 3-4 categorized news sections
8. **tickerHeadlines**: 8-10 short headline phrases for the scrolling ticker

### Step 5: Build the Website

If this is a new project (no existing site structure), scaffold the full website:

1. Set up Vite + React + TypeScript + TailwindCSS
2. Create the type definitions in `src/lib/types.ts`
3. Create the data file `src/lib/archives.ts`
4. Build all page components:
   - `src/components/Header.tsx` — nav with language toggle
   - `src/components/HeroSection.tsx` — hero with lead text and stats
   - `src/components/TickerBar.tsx` — scrolling headlines
   - `src/components/CoreTrends.tsx` — trend cards grid
   - `src/components/NewsSection.tsx` — categorized news cards
   - `src/components/NewsCard.tsx` — individual news card
   - `src/components/Footer.tsx` — credits
5. Create pages:
   - `src/pages/Home.tsx` — renders latest issue
   - `src/pages/Archive.tsx` — grid of all issues
   - `src/pages/IssueDetail.tsx` — renders specific issue by ID
6. Set up React Router in `src/App.tsx`
7. Create i18n context in `src/lib/i18n.tsx`
8. Configure TailwindCSS with the color palette above

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
