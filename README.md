# Nyne Skills for Claude Code

Three powerful skills for the Nyne AI platform, ready to use in Claude Code.

## Quick Start

1. **Clone this repository**
   ```bash
   git clone https://github.com/MichaelFanous2/nyne-skills.git
   cd nyne-skills
   ```

2. **Open in Claude Code**
   ```bash
   claude .
   ```

3. **Set your Nyne API credentials**
   ```bash
   export NYNE_API_KEY=your_api_key
   export NYNE_API_SECRET=your_api_secret
   ```

   Get credentials at: https://api.nyne.ai

4. **Start using the skills!**

## Skills Included

### nyne-search
Search for people using natural language queries. Find professionals by role, company, location, industry, or any combination.

**In Claude Code:**
```
/nyne-search "SDR or account executive at Explorium, Altrata, People Data Labs"
```

See `skills/nyne-search/SKILL.md` for full documentation.

### nyne-enrichment
Enrich any person by email, phone, LinkedIn URL, or name. Returns contact info, social profiles, work history, and education.

**In Claude Code:**
```
/nyne-enrichment email@example.com
```

See `skills/nyne-enrichment/SKILL.md` for full documentation.

### nyne-deep-research
Generate comprehensive person dossiers with psychographic analysis, interests, conversation starters, and insights.

**In Claude Code:**
```
/deep-research --email john@company.com --linkedin https://linkedin.com/in/johndoe
```

See `skills/nyne-deep-research/SKILL.md` for full documentation.

## Requirements

- **Nyne API Key & Secret** — Get free at https://api.nyne.ai
- **Python 3.9+** — For skill execution
- **Claude Code** — Latest version

## Environment Setup

**Option 1: Export in your shell (temporary)**
```bash
export NYNE_API_KEY="your_key"
export NYNE_API_SECRET="your_secret"
```

**Option 2: Create a `.env` file (persistent)**
Create `.env` in the repository root:
```
NYNE_API_KEY=your_key
NYNE_API_SECRET=your_secret
```

Then load it before using:
```bash
source .env
```

**Option 3: Add to your shell profile (permanent)**
Add to `~/.bashrc`, `~/.zshrc`, etc:
```bash
export NYNE_API_KEY="your_key"
export NYNE_API_SECRET="your_secret"
```

## For Deep-Research Skill

You'll also need an LLM API key (choose one):

```bash
# Gemini (recommended)
export GEMINI_API_KEY="your_key"

# OR OpenAI
export OPENAI_API_KEY="your_key"

# OR Anthropic
export ANTHROPIC_API_KEY="your_key"
```

## License

See individual skill directories for license information.
