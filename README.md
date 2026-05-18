# AI News Report Skill

An [Enter.pro](https://enter.pro) skill that generates AI industry news report websites. Specify any time period, and the project AI will fetch real-time AI news, curate the most important stories, and build a complete bilingual (zh/en) website with a modern dark-themed design.

## Features

- Fetches AI news from [aihot.virxact.com](https://aihot.virxact.com) public API (no API key needed)
- Supports any time range: "last week", "May 1-15", "last 3 days", etc.
- Bilingual Chinese/English with one-click toggle
- Dark theme with pink accent design
- Categorized news: Models, Products, Industry, Research
- Core trend analysis
- Scrolling ticker headlines
- Responsive: mobile, tablet, desktop
- Multi-issue archive with routing

## Quick Start

In your Enter.pro project, say:

> "帮我生成上周的 AI 资讯报告"

or

> "Generate an AI news report for May 1 to May 15"

The project AI will automatically fetch data and build the complete website.

## Install

### In Enter.pro

Copy `SKILL.md` into your project's `skills/ai-news-report/` directory, or tell the project AI:

> "Install this skill: https://github.com/Aspirin404/ai-news-report-skill"

### In Claude Code / Codex CLI

```bash
mkdir -p ~/.claude/skills/ai-news-report
curl -o ~/.claude/skills/ai-news-report/SKILL.md \
  https://raw.githubusercontent.com/Aspirin404/ai-news-report-skill/main/SKILL.md
```

## Example Triggers

- "帮我生成上周的 AI 资讯报告"
- "Generate AI news report for last week"
- "拉取最近 3 天的 AI 新闻做个网站"
- "Create an AI news website from May 1 to May 15"
- "做一个本月的 AI 行业动态网站"

## Design Preview

The generated website features:

- **Hero Section** — Key headline with edition badge and stats
- **Ticker Bar** — Auto-scrolling headline ticker
- **Core Trends** — 3-4 synthesized trend cards
- **News Grid** — Categorized cards with highlights
- **Dark Theme** — `#0A0A0A` background + `#E83B6C` pink accent

## Data Source

All news data comes from [AI HOT](https://aihot.virxact.com), a free public AI news aggregation service. No authentication required.

## License

MIT
