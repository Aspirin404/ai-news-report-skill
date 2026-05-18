# aihot API Reference

All data comes from [aihot.virxact.com](https://aihot.virxact.com) — a free, public
AI news aggregation service. No API key required.

## Required User-Agent

All requests must include this header:

```
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36 aihot-skill/0.2.0
```

## Endpoints

| Endpoint | Purpose |
|----------|---------|
| `GET /api/public/daily` | Latest daily digest |
| `GET /api/public/daily/{YYYY-MM-DD}` | Specific date's digest |
| `GET /api/public/dailies` | Archive index of all dailies |
| `GET /api/public/items?mode=selected&since={ISO}&take=100` | Batch fetch selected items since a date |

## Response Format — Daily Digest

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

## Response Format — Items Endpoint

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

## Fetching Strategy

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
