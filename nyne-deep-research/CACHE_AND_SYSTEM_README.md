# Nyne Deep Research - Complete System Documentation

**Last Updated:** January 29, 2026
**Purpose:** Comprehensive documentation for context preservation across sessions

---

## SYSTEM OVERVIEW

This tool generates comprehensive "dossiers" on people by:
1. Enriching profile data via Nyne.ai API (email/LinkedIn → full profile)
2. Fetching Twitter/Instagram following lists
3. Using LLM (Gemini) to analyze psychographic patterns
4. Generating a 12-section markdown dossier

---

## FILE LOCATIONS

### Main Code
```
/Users/michaelfanous/nyne-deep-research/deep_research.py    # Main script (USE THIS)
/Users/michaelfanous/nyne-deep-research/.env                 # API keys
```

### Cache System
```
/Users/michaelfanous/deep_research_test/nyne-deep-research/following_cache.json           # MAIN CACHE
/Users/michaelfanous/deep_research_test/nyne-deep-research/following_cache_BACKUP_*.json  # Backups
/Users/michaelfanous/deep_research_test/nyne-deep-research/prefetch_following.py          # Prefetch script
```

### Enrichment Source Files (used by prefetch)
```
/Users/michaelfanous/nyne_enrich/enriched_faire_investors.json
/Users/michaelfanous/nyne_enrich/vip_hour_enriched_full.json
```

### Generated Dossiers
```
/Users/michaelfanous/*_dossier.md    # Output files go to home directory
```

---

## CACHE STRUCTURE

The `following_cache.json` has this structure:

```json
{
  "by_email": {
    "person@company.com": {
      "email": "person@company.com",
      "linkedin": "linkedin-username",
      "twitter_url": "https://twitter.com/handle",
      "instagram_url": "https://instagram.com/handle",
      "platform": "twitter",
      "social_url": "https://twitter.com/handle",
      "name": "Person Name",
      "following": {
        "result": {
          "total_count": 200,
          "interactions": [...]
        }
      },
      "fetched_at": "2026-01-28 10:30:00"
    }
  },
  "by_linkedin": { ... same entries indexed by linkedin username ... },
  "by_twitter": { ... same entries indexed by twitter URL ... },
  "by_instagram": { ... same entries indexed by instagram URL ... }
}
```

### Cache Lookup Flow
```
User provides: email or linkedin
        ↓
Check by_email[email] or by_linkedin[username]
        ↓
Get person's twitter_url and instagram_url from entry
        ↓
Look up by_twitter[twitter_url] → Twitter following data
Look up by_instagram[instagram_url] → Instagram following data
        ↓
Merge and return to avoid live API calls
```

---

## CURRENT CACHE STATUS (as of Jan 29, 2026)

| Index | Count | Notes |
|-------|-------|-------|
| by_email | ~318 | Primary lookup |
| by_linkedin | ~307 | Secondary lookup |
| by_twitter | ~298 | Twitter following cached |
| by_instagram | ~35 | Instagram mostly NOT cached |

**KNOWN ISSUE:** Cache file was corrupted on Jan 29 during failed prefetch run.
- Backup saved: `following_cache_BACKUP_20260129_122256.json`
- Issue: Missing 7 closing braces in JSON (325,845 opens vs 325,838 closes)
- Needs repair before use

---

## HOW TO RUN DEEP RESEARCH

### Basic Usage
```bash
cd /Users/michaelfanous/nyne-deep-research
python deep_research.py --email "person@company.com" --output /Users/michaelfanous/person_dossier.md
```

### With LinkedIn (better results)
```bash
python deep_research.py --email "person@company.com" --linkedin "https://linkedin.com/in/username" --output /Users/michaelfanous/person_dossier.md
```

### LinkedIn Only (sometimes finds different/better Twitter)
```bash
python deep_research.py --linkedin "https://linkedin.com/in/username" --output /Users/michaelfanous/person_dossier.md
```

---

## WHAT TAKES TIME

| Step | Time | Cacheable? |
|------|------|------------|
| Enrichment API | 10-15s | No (always runs) |
| Twitter following fetch | 30-60s | YES - this is cached |
| Instagram following fetch | 30-60s | YES - this is cached |
| LLM batch analysis (N batches) | 5-8s each | No |
| LLM cluster analysis (5 parallel) | 15-20s total | No |
| LLM final synthesis | 10-15s | No |

**The cache saves 60-120 seconds** by skipping Twitter/Instagram API calls.
**LLM calls always run** (~16 Gemini API calls per dossier).

---

## HOW TO PREFETCH MORE DATA

### 1. Ensure .env is configured
```bash
cd /Users/michaelfanous/deep_research_test/nyne-deep-research
cat .env  # Should have NYNE_API_KEY and NYNE_API_SECRET
```

### 2. Run prefetch script
```bash
python prefetch_following.py
```

This will:
- Read enrichment files (enriched_faire_investors.json, vip_hour_enriched_full.json)
- Find people with Twitter/Instagram URLs
- Skip anyone already in cache
- Fetch and cache new following data

### 3. Monitor progress
The script prints progress as it goes. It saves checkpoints every 10 successful fetches.

---

## CACHE REPAIR PROCEDURE

If cache JSON is corrupted:

### 1. Identify the issue
```bash
python3 -c "
import json
with open('following_cache.json') as f:
    content = f.read()
print(f'Opens: {content.count(\"{\")}')
print(f'Closes: {content.count(\"}\")}')
"
```

### 2. Repair by adding missing braces
```python
# Find last valid entry, truncate, add closing braces
# Structure: {"by_email": {...}, "by_linkedin": {...}, "by_twitter": {...}, "by_instagram": {...}}
# Each section needs: closing }, and main dict needs final }
```

### 3. Validate repair
```bash
python3 -c "import json; json.load(open('following_cache.json')); print('Valid!')"
```

---

## DOSSIER SECTIONS (12 total, no politics/landmines)

1. **Identity Snapshot** - Name, role, location, age, emails, social profiles
2. **Personal Life & Hobbies** - Sports, entertainment, passions, family
3. **Career DNA** - Full trajectory with dates and "superpower"
4. **Psychographic Profile** - Archetypes, values, aspirations, tribes
5. **Social Graph Analysis** - Professional network, personal interests, inner circle
6. **Interest Cluster Deep Dive** - Sports, Music, Food, Causes, Tech, Geographic, Personal Network
7. **Content & Voice Analysis** - Topics, tone, humor, recent posts
8. **Key Relationships (Top 25)** - Most important accounts they follow
9. **Conversation Starters (30+)** - Professional, personal, shared experience hooks
10. **Recommendations & How Others See Them** - LinkedIn insights
11. **"Creepy Good" Insights** - Non-obvious patterns
12. **Approach Strategy** - Best angle, shared connections, topics to reference

---

## API CREDENTIALS

Stored in `/Users/michaelfanous/nyne-deep-research/.env`:
```
NYNE_API_KEY=...
NYNE_API_SECRET=...
GEMINI_API_KEY=...
```

User also provided credentials directly:
- API Key: 21da69bbb8240f7e859da5d07969e831
- API Secret: pe0e43ce515092

---

## RECENT CHANGES (Jan 29, 2026)

### 1. Added Cache Integration to deep_research.py
- Location: Lines ~64-160 (after NYNE_BASE_URL)
- Functions added:
  - `load_following_cache()` - Loads cache file once
  - `normalize_linkedin_username()` - Extracts username from URL
  - `lookup_following_from_cache()` - Checks cache before live API
- Modified `deep_research()` function to check cache first

### 2. Removed Politics & Landmines from Prompts
- `DOSSIER_PROMPT` - Removed political leanings, Politics/Policy cluster, Warnings section
- `BATCH_ANALYSIS_PROMPT` - Changed "Politics (what leaning?)" to "Philanthropy, Climate, Health, Education"
- `CAUSES_POLITICS_CLUSTER_PROMPT` → renamed to `CAUSES_VALUES_CLUSTER_PROMPT`
- `SYNTHESIS_PROMPT` - Removed political references and Warnings section
- Dossier now has 12 sections (was 13)

### 3. Added dotenv loading to prefetch_following.py
- Added `from dotenv import load_dotenv; load_dotenv()` at top

---

## TROUBLESHOOTING

### "No Twitter/Instagram found"
- The enrichment API didn't find social profiles for this person
- Try LinkedIn-only lookup (sometimes finds different data)

### "Cache hit but still slow"
- Cache only saves following API calls
- LLM analysis (16 Gemini calls) still runs every time

### "API returned 401"
- Check .env file has correct NYNE_API_KEY and NYNE_API_SECRET
- Make sure dotenv is loaded (check for `from dotenv import load_dotenv`)

### "JSONDecodeError in cache"
- Cache file corrupted
- Use backup: `cp following_cache_BACKUP_*.json following_cache.json`
- Or repair manually (see Cache Repair Procedure above)

### "Gemini rate limit (429)"
- Wait 30-60 seconds and retry
- Some cluster analyses may fail but dossier still generates

---

## NEXT STEPS / TODO

1. ~~**Repair cache file**~~ - DONE (Jan 29, 2026) - Fixed corrupted JSON
2. **Prefetch running** - Started Jan 29, 2026 - Fetching 259 records (Twitter + Instagram)
   - Monitor: `tail -f /Users/michaelfanous/deep_research_test/nyne-deep-research/prefetch_output.log`
   - Check progress: `ps aux | grep prefetch`
3. **Consider dossier caching** - Cache final dossiers to skip LLM calls on repeat lookups
4. **Upgrade Gemini SDK** - Current google.generativeai is deprecated, switch to google.genai

## PREFETCH STATUS (Jan 29, 2026)

Started background prefetch:
- **Records to fetch:** 259 (both Twitter AND Instagram where available)
- **Already cached:** 145 Twitter, 0 Instagram
- **Running in background:** PID started, logging to prefetch_output.log
- **Expected time:** ~30-60 minutes (depends on API rate limits)

To check if complete:
```bash
# Check if still running
ps aux | grep prefetch

# Check final stats
tail -20 /Users/michaelfanous/deep_research_test/nyne-deep-research/prefetch_output.log

# Verify cache after completion
python3 -c "import json; c=json.load(open('/Users/michaelfanous/deep_research_test/nyne-deep-research/following_cache.json')); print(f'twitter:{len(c.get(\"by_twitter\",{}))} instagram:{len(c.get(\"by_instagram\",{}))}')"
```

---

## COMMANDS CHEAT SHEET

```bash
# Run research
cd /Users/michaelfanous/nyne-deep-research
python deep_research.py --email "x@y.com" --output /Users/michaelfanous/name_dossier.md

# Check cache stats
python3 -c "import json; c=json.load(open('/Users/michaelfanous/deep_research_test/nyne-deep-research/following_cache.json')); print(f'email:{len(c.get(\"by_email\",{}))} linkedin:{len(c.get(\"by_linkedin\",{}))} twitter:{len(c.get(\"by_twitter\",{}))} instagram:{len(c.get(\"by_instagram\",{}))}')"

# Lookup specific person in cache
python3 -c "import json; c=json.load(open('/Users/michaelfanous/deep_research_test/nyne-deep-research/following_cache.json')); e=c.get('by_email',{}).get('person@company.com'); print('Found!' if e else 'Not in cache')"

# Run prefetch
cd /Users/michaelfanous/deep_research_test/nyne-deep-research
python prefetch_following.py

# Validate cache JSON
python3 -c "import json; json.load(open('/Users/michaelfanous/deep_research_test/nyne-deep-research/following_cache.json')); print('Valid!')"
```
