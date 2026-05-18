# AI News Report Skill

A [Claude Code](https://code.claude.com) / [Enter Code](https://enter.pro/code) plugin that generates AI industry news report websites. Specify any time period, and the AI will fetch real-time news, curate the most important stories, and build a complete bilingual (zh/en) website following Swiss International Style design.

## Features

- Fetches AI news from [aihot.virxact.com](https://aihot.virxact.com) public API (no API key needed)
- Supports any time range: "last week", "May 1-15", "last 3 days", etc.
- Bilingual Chinese/English with one-click toggle
- Swiss International Style: white background, bold typography, single accent color
- Categorized news: Models, Products, Industry, Research
- Core trend analysis with synthesized insights
- Scrolling ticker headlines
- Responsive: mobile, tablet, desktop
- Multi-issue archive with routing

## Install

### As a Claude Code / Enter Code Plugin

```bash
claude plugin add https://github.com/Aspirin404/ai-news-report-skill
```

### Manual Install

```bash
# Personal (all projects)
mkdir -p ~/.claude/skills/ai-news-report/references
curl -o ~/.claude/skills/ai-news-report/SKILL.md \
  https://raw.githubusercontent.com/Aspirin404/ai-news-report-skill/main/skills/ai-news-report/SKILL.md
curl -o ~/.claude/skills/ai-news-report/references/api.md \
  https://raw.githubusercontent.com/Aspirin404/ai-news-report-skill/main/skills/ai-news-report/references/api.md
curl -o ~/.claude/skills/ai-news-report/references/data-model.md \
  https://raw.githubusercontent.com/Aspirin404/ai-news-report-skill/main/skills/ai-news-report/references/data-model.md
curl -o ~/.claude/skills/ai-news-report/references/design-spec.md \
  https://raw.githubusercontent.com/Aspirin404/ai-news-report-skill/main/skills/ai-news-report/references/design-spec.md

# Or project-level
mkdir -p .claude/skills/ai-news-report/references
# ... same curl commands with .claude/ prefix
```

## Quick Start

After installation, say:

> "帮我生成上周的 AI 资讯报告"

or

> "Generate an AI news report for May 1 to May 15"

## Example Triggers

- "帮我生成上周的 AI 资讯报告"
- "Generate AI news report for last week"
- "拉取最近 3 天的 AI 新闻做个网站"
- "Create an AI news website from May 1 to May 15"
- "做一个本月的 AI 行业动态网站"
- "AI news digest" / "AI 行业动态"

## Repo Structure

```
ai-news-report-skill/
├── .claude-plugin/
│   ├── plugin.json              ← Plugin metadata
│   └── marketplace.json         ← Marketplace listing
├── skills/
│   └── ai-news-report/
│       ├── SKILL.md             ← Main skill (workflow + references)
│       └── references/
│           ├── api.md           ← aihot API endpoints & response formats
│           ├── data-model.md    ← TypeScript types & content rules
│           └── design-spec.md   ← Swiss Style design specification
└── README.md
```

## Design

The generated website follows Swiss International Style principles:

- **White background** with pure black typography
- **Single accent color** for highlights (customizable)
- **Extreme type contrast** — hero titles at 10-12rem vs 14px body
- **Monospace section markers** — `// Section 01 · Title`
- **Dot grid** decoration in hero section only
- **Hairline dividers** — 1px borders as structure
- **No serif, no shadows, no gradients, no rounded corners**

## Data Source

All news data comes from [AI HOT](https://aihot.virxact.com), a free public AI news aggregation service. No authentication required.

## License

MIT
