# Nyne Skills

Custom Claude Code skills for the Nyne AI platform.

## Skills Included

### 1. nyne-deep-research
Comprehensive research skill that performs deep dives into topics, companies, and people.

**Usage in Claude Code:**
```
/nyne-deep-research [query]
```

### 2. nyne-enrichment
Data enrichment skill that enriches company and person data with additional insights.

**Usage in Claude Code:**
```
/nyne-enrichment [entity type] [entity name]
```

### 3. nyne-search
People search skill that leverages the Nyne /person/search API with natural language capabilities.

**Usage in Claude Code:**
```
/nyne-search [query with filters]
```

## Installation

Clone this repository and the skills will be available in Claude Code.

```bash
git clone https://github.com/MichaelFanous2/nyne-skills.git
```

## Requirements

Each skill requires:
- Nyne API credentials (set `NYNE_API_KEY` environment variable)
- Python 3.9+
- Dependencies listed in each skill's requirements.txt

## Setup

1. Set your Nyne API key:
```bash
export NYNE_API_KEY=your_api_key_here
```

2. Install any dependencies from each skill's directory if needed.

## License

See individual skill directories for license information.
