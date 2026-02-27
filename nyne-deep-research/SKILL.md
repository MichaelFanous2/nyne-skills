---
name: deep-research
description: Generate comprehensive person dossiers from email/LinkedIn using Nyne.ai enrichment + LLM analysis.
homepage: https://github.com/MichaelFanous2/nyne-deep-research
metadata: {"clawdbot":{"emoji":"🔍","requires":{"bins":["python3"],"env":["NYNE_API_KEY","NYNE_API_SECRET","GEMINI_API_KEY"]},"install":[{"id":"pip","kind":"shell","command":"pip install -r requirements.txt","label":"Install dependencies (pip)"}]}}
---

# deep-research

Generate a comprehensive person dossier with psychographic analysis, interests, conversation starters, and "creepy good" insights.

## Quick Start

```bash
# Best results: provide BOTH email AND LinkedIn
python deep_research.py --email "ceo@company.com" --linkedin "https://linkedin.com/in/ceo" --output dossier.md

# Email only
python deep_research.py --email "john@company.com" --output dossier.md

# LinkedIn only
python deep_research.py --linkedin "https://linkedin.com/in/johndoe" --output dossier.md

# Get raw JSON (no LLM processing)
python deep_research.py --email "john@company.com" --json --output data.json
```

## How It Works

1. **Enrichment API** (`/person/enrichment`) - Gets profile data from email/LinkedIn
2. **Following API** (`/person/interactions`) - Gets Twitter/Instagram following list
3. **Articles API** (`/person/articlesearch`) - Finds press mentions
4. **LLM Analysis** - 13 parallel calls to cluster and synthesize data

## Output Dossier Sections (13 total)

1. **Identity Snapshot** - Name, role, location, age, emails, social profiles
2. **Personal Life & Hobbies** - Sports, entertainment, family (with @handle evidence)
3. **Career DNA** - Full trajectory + "Superpower"
4. **Psychographic Profile** - Archetype, values, political lean
5. **Social Graph Analysis** - Professional network, inner circle
6. **Interest Cluster Deep Dive** - Sports, music, causes, hidden interests (with specific handles)
7. **Content & Voice Analysis** - Topics, tone, recent posts
8. **Key Relationships (Top 25)** - Most important connections with context
9. **Conversation Starters (30+)** - Professional, personal, shared experience hooks
10. **Recommendations & How Others See Them** - LinkedIn recommendations patterns
11. **Warnings & Landmines** - Sensitive topics to avoid
12. **"Creepy Good" Insights** - Non-obvious patterns
13. **Approach Strategy** - Best angle, shared connections, what NOT to do

## Raw JSON Structure (--json flag)

```json
{
  "enrichment": {
    "result": {
      "firstname": "John",
      "lastname": "Doe",
      "headline": "CEO at Acme Inc",
      "summary": "...",
      "birthday": "1985-03-15",
      "careers_info": [
        {"company": "Acme Inc", "title": "CEO", "start_date": "2020-01"}
      ],
      "schools_info": [
        {"school_name": "Stanford", "degree": "MBA"}
      ],
      "social_profiles": {
        "twitter": {"url": "https://twitter.com/johndoe"},
        "linkedin": {"url": "https://linkedin.com/in/johndoe"},
        "github": {"url": "https://github.com/johndoe"},
        "strava": {"url": "https://strava.com/athletes/123"},
        "pinterest": {"url": "https://pinterest.com/johndoe"},
        "angellist": {"url": "https://angel.co/johndoe"}
      },
      "newsfeed": [
        {"content": "LinkedIn post text...", "date": "2024-01-15"}
      ],
      "emails": ["john@company.com"],
      "altemails": ["john.personal@gmail.com"],
      "recommendations": ["Great leader...", "Visionary..."]
    }
  },
  "following_twitter": {
    "result": {
      "interactions": [
        {
          "actor": {
            "username": "elonmusk",
            "display_name": "Elon Musk",
            "bio": "...",
            "followers_count": "180000000"
          },
          "relationship_type": "following"
        }
      ]
    }
  },
  "following_instagram": {
    "result": {
      "interactions": [...]
    }
  },
  "articles": {
    "result": {
      "articles": [
        {"title": "...", "url": "...", "source": "TechCrunch", "date": "2024-01-15"}
      ]
    }
  }
}
```

## Key Fields to Extract

| Field | Path | Description |
|-------|------|-------------|
| Name | `enrichment.result.firstname/lastname` | Full name |
| Emails | `enrichment.result.emails` + `altemails` | All email addresses |
| Twitter | `enrichment.result.social_profiles.twitter.url` | Auto-discovered Twitter |
| Following | `following_twitter.result.interactions[].actor` | Who they follow (psychographics) |
| LinkedIn Posts | `enrichment.result.newsfeed[]` | Recent posts with dates |
| Career | `enrichment.result.careers_info[]` | Work history |
| Education | `enrichment.result.schools_info[]` | Schools attended |
| Birthday | `enrichment.result.birthday` | Birth date if available |

## CLI Options

```bash
--email       Person's email address
--linkedin    LinkedIn profile URL
--twitter     Twitter URL (skip auto-discovery)
--instagram   Instagram URL (skip auto-discovery)
--output      Output file path (default: stdout)
--json        Output raw JSON data (skip LLM dossier)
--llm         Force LLM provider: gemini, openai, or anthropic
-q            Quiet mode (no progress output)
```

## Environment Variables

```bash
NYNE_API_KEY=your_key        # Required - from nyne.ai
NYNE_API_SECRET=your_secret  # Required - from nyne.ai
GEMINI_API_KEY=your_key      # Recommended (or OPENAI_API_KEY / ANTHROPIC_API_KEY)
```

## Pro Tips

- **Always provide BOTH email AND LinkedIn** for best match confidence
- **Twitter unlocks psychographics** - who they follow reveals interests, values, hobbies
- **Use `--json` for raw data** then fetch additional social profiles (Strava, GitHub, Pinterest)
- **Low-follower accounts** in following list often indicate close friends/family
- **Check `social_profiles`** in raw response - can WebFetch these for more context
