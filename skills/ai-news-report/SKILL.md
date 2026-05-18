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
follows **Swiss International Style** (瑞士国际主义): pure white background, extreme
black typography contrast, single accent color, grid-based layout, hairline dividers,
monospace meta labels. Responsive, bilingual (zh/en), with animated ticker headlines,
core trend analysis, and categorized news cards.

## Resources

- **API reference**: [references/api.md](references/api.md) — aihot endpoints, response formats, fetching strategy
- **Data model**: [references/data-model.md](references/data-model.md) — TypeScript types, storage format, content rules
- **Design specification**: [references/design-spec.md](references/design-spec.md) — Swiss Style principles, color palette, typography, layout components, animations

## Generation Workflow

### Step 1: Parse Time Range

Interpret the user's natural language time specification:

| User says | Resolved range |
|-----------|---------------|
| "上周" / "last week" | Previous Monday → Sunday |
| "这周" / "this week" | This Monday → today |
| "最近3天" / "last 3 days" | (today - 3) → yesterday |
| "5月1日到5月15日" / "May 1 to May 15" | Exact dates |
| "本月" / "this month" | 1st of current month → today |

### Step 2: Fetch Data

Read [references/api.md](references/api.md) for full endpoint documentation. Use both
the daily digest endpoint and the batch items endpoint, then merge results.

### Step 3: Filter & Curate

From the fetched data, select 30-40 items across 3-4 categories. For each item:
- Translate title and summary to both zh and en
- Preserve the original `sourceUrl`
- Mark 1-2 most important items per section as `highlight: true`
- Assign appropriate tags

See [references/data-model.md](references/data-model.md) for category targets and content rules.

### Step 4: Generate Report Content

Create the `WeeklyIssue` object (see [references/data-model.md](references/data-model.md) for types):

1. **Edition**: "EP {N}" — auto-increment from existing archives
2. **Range**: Format as "YYYY / MM / DD — MM / DD"
3. **heroLead + heroLeadAccent**: One compelling sentence. Must be unique.
4. **trendsLead**: Brief intro to trends. Must be unique.
5. **coreTrends**: 3-4 insights synthesized from the news
6. **meta**: Title, description, keywords, stats
7. **sections**: 3-4 categorized news sections
8. **tickerHeadlines**: 8-10 short headline phrases

### Step 5: Build the Website

Read [references/design-spec.md](references/design-spec.md) for the full visual specification.

**New project** — scaffold the full site:
1. Vite + React + TypeScript + TailwindCSS (shadcn/ui CSS variables)
2. Types in `src/lib/types.ts`, data in `src/lib/archives.ts`
3. Components following the design spec's component list and layout rules
4. Pages: Home (latest issue), Archive (grid), IssueDetail (by ID)
5. React Router + i18n context
6. `tailwind.config.ts` with CSS variable color system + animation keyframes

**Existing project** — just add the new `WeeklyIssue` to the front of `archives`.

### Step 6: Verify

- Build passes (`npm run build`)
- All L10n fields have both `zh` and `en` values
- No duplicate IDs across issues
- News items have valid `url` fields where available

## Error Handling

- **Empty data**: Inform user, suggest different date range
- **Partial data**: Proceed with available data, note gaps
- **Build failure**: Check types, imports, TailwindCSS classes

## What This Skill Does NOT Do

- Does not deploy or publish (user handles via Enter.pro or other hosting)
- Does not require API keys or authentication
- Does not modify existing issues — only adds new ones
