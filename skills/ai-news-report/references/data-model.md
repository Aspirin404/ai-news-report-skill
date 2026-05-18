# Data Model

TypeScript types for the report. Place in `src/lib/types.ts`.

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
  id: string;                  // dynamic, e.g. "models", "agents", "funding", "regulation"
  index: string;               // "01" | "02" | "03" ...
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
  edition: string;             // e.g. "EP 1"
  range: string;               // e.g. "2026 / 05 / 11 — 05 / 17"
  description: L10n;
  keywords: L10n[];            // clickable keyword tags in hero
  stats: Array<{ label: L10n; value: string }>;
}

/** A complete report issue */
export interface ReportIssue {
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

## Data Storage

Data is stored in `src/lib/archives.ts`:

```typescript
import type { ReportIssue } from './types';

const ep1: ReportIssue = { /* ... */ };

// Newest first
export const archives: ReportIssue[] = [ep1];
```

## Content Categories

Categories are **NOT fixed**. Analyze the fetched news content and derive 3-6 meaningful sections based on what actually happened during the time period. Common categories include but are not limited to:

- Model Releases & Updates (模型更新)
- Product Launches (产品发布)
- AI Agents & Tools (智能体工具)
- Industry Trends (行业动态)
- Big Tech Moves (大公司动向)
- Funding & Investment (投融资事件)
- Research & Papers (研究论文)
- Regulation & Policy (监管政策)
- Open Source (开源生态)
- Hardware & Compute (算力硬件)

**Rules:**
- Choose 3-6 categories per issue based on actual content density
- Each section should have at least 4 items; merge sparse categories
- Name sections descriptively — don't force content into predefined buckets
- Total: 25-40 curated items per issue

## Content Rules

1. **Every issue must be unique**: heroLead, heroLeadAccent, trendsLead, meta.description, and tickerHeadlines must all be generated fresh. Never copy from another issue.
2. **Sections are flat**: 3-6 top-level categories only. No sub-categories.
3. **L10n is mandatory**: Every user-facing text field uses `{ zh: "中文", en: "English" }`. English translations should be natural and professional, not literal.
4. **Source attribution**: Every NewsItem should include the `url` field when available.
5. **Highlights**: Mark 1-2 most significant items per section with `highlight: true`.
