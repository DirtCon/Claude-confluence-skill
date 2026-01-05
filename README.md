# Claude Code Confluence Work Logger

Automatically document your work sessions in Confluence using Claude Code. When you finish working on something (fixing bugs, writing scripts, solving problems), just ask Claude to document it and get a complete Confluence page with all the details.

## What This Does

Work on something → Ask Claude to document it → Get a formatted Confluence page with:
- What you did
- Files involved
- Code with syntax highlighting
- Step-by-step instructions for others
- Notes and context

Perfect for knowledge sharing and creating "how-to" docs from your actual work.

## Prerequisites

Before you start, make sure you have:

1. **Python 3.7 or higher**
   - Check: `python --version` or `python3 --version`
   - Download: https://www.python.org/downloads/

2. **Claude Code installed**
   - Install: https://claude.ai/download

3. **Confluence Cloud account**
   - You need access to a Confluence Cloud instance (atlassian.net)
   - You'll need permission to create pages

4. **Git** (to clone this repo)
   - Check: `git --version`
   - Download: https://git-scm.com/downloads

## Installation

### Step 1: Clone this repository

```bash
git clone https://github.com/DirtCon/Claude-confluence-skill.git
cd Claude-confluence-skill
```

Or download the ZIP from GitHub and extract it.

### Step 2: Install Python dependencies

```bash
pip install requests
```

Or if you have Python 3 specifically:
```bash
pip3 install requests
```

### Step 3: Copy the skill to your project

Copy the `.claude` folder to your project directory where you'll use Claude Code:

```bash
# Example: copy to your project
cp -r .claude /path/to/your/project/
```

Or if you want to use it globally for all projects, copy to your home directory:

```bash
# macOS/Linux
cp -r .claude ~/.claude

# Windows (PowerShell)
Copy-Item -Recurse .claude $HOME/.claude
```

## Configuration

### Step 1: Create your config file

Navigate to where you copied the `.claude` folder, then:

```bash
# Copy the example config
cp .claude/skills/confluence/config.json.example .claude/skills/confluence/config.json
```

### Step 2: Get your Confluence API token

1. Go to https://id.atlassian.com/manage/api-tokens
2. Click **"Create API token"**
3. Give it a name like "Claude Code"
4. **Copy the token** (you won't see it again!)

### Step 3: Find your Confluence details

You need:
- **confluence_url**: Your Confluence URL (e.g., `https://yourcompany.atlassian.net/wiki`)
- **email**: Your Atlassian account email
- **api_token**: The token from Step 2
- **space_key**: The space where docs will be created

**To find your space key:**
- Go to your Confluence space
- Look at the URL: `https://yourcompany.atlassian.net/wiki/spaces/SPACEKEY/...`
- The part after `/spaces/` is your space key (e.g., "DEV", "WORK")

### Step 4: Edit your config.json

Open `.claude/skills/confluence/config.json` and fill in your details:

```json
{
  "confluence_url": "https://yourcompany.atlassian.net/wiki",
  "email": "your-email@example.com",
  "api_token": "your-api-token-here",
  "space_key": "WORK"
}
```

**Important:** Don't commit this file to git! It's already in `.gitignore`.

### Step 5: Test your setup

```bash
cd .claude/skills/confluence
python confluence_api.py test-connection
```

You should see:
```
Connection successful
Connected to: https://yourcompany.atlassian.net/wiki
Authenticated as: your-email@example.com
```

If you see this, you're ready to go! 🎉

## Usage

### Basic Usage

1. Open Claude Code in your project:
   ```bash
   cd your-project
   claude
   ```

2. Work on something (fix a bug, write a script, etc.)

3. When done, ask Claude:
   ```
   Document this work in Confluence
   ```

4. Claude will ask what task you're documenting

5. Claude creates a Confluence page and gives you the URL

### Example 1: Bug Fix

**You:** *(spend time fixing a Zscaler issue)*

**You:** "Document this work in Confluence"

**Claude:** "What task are you documenting?"

**You:** "Fixed Zscaler VPN connection dropping"

**Result:** Page titled "Fixed Zscaler VPN connection dropping - 2026-01-05" with:
- Problem description
- What was changed
- Config files modified
- How others can apply the fix

### Example 2: Script Documentation

**You:** *(write a Python data export script)*

**You:** "Document this work in Confluence"

**Claude:** "What task are you documenting?"

**You:** "Python script for daily data export"

**Result:** Page with:
- What the script does and why
- How to use it (with examples)
- Full code with syntax highlighting
- Requirements and dependencies

### Example 3: Task Hierarchies

For follow-up work on the same topic:

**Claude:** "Is this a follow-up to a previous task?"

**You:** "Yes, parent is 'Python script for daily data export'"

**Result:** Creates a child page under the original:
```
Python script for daily data export
└── Added error handling and retry logic - 2026-01-06
```

## Features

- ✅ **5-minute setup** - Single config file, no environment variables
- ✅ **Automatic documentation** - Claude reviews your work and generates pages
- ✅ **Task hierarchies** - Organize related work into parent/child pages
- ✅ **Code highlighting** - Syntax-highlighted code blocks
- ✅ **Team ready** - Easy to share, each person uses their own API token
- ✅ **Secure** - Config file never committed to git

## Troubleshooting

### "Config file not found"
**Fix:** Make sure you copied `config.json.example` to `config.json`

### "Authentication failed" or 401 error
**Fix:**
- Check your email and API token in `config.json`
- Make sure there are no extra spaces
- Try regenerating your API token

### "Space not found"
**Fix:**
- Check your `space_key` in `config.json`
- Space keys are case-sensitive (usually uppercase like "WORK", "DEV")
- Verify you have access to that space

### "requests library not found"
**Fix:** Install it: `pip install requests`

### Test each component:
```bash
# Test connection
python .claude/skills/confluence/confluence_api.py test-connection

# Verify space
python .claude/skills/confluence/confluence_api.py verify-space
```

## File Structure

```
.claude/skills/confluence/
├── SKILL.md                   # Main skill workflow
├── confluence_api.py          # Confluence API client
├── templates.py               # Page content templates
├── config.json.example        # Config template (safe to commit)
├── config.json               # Your credentials (DON'T commit!)
├── config.md                 # Detailed setup guide
└── README.md                 # Skill documentation
```

## Team Setup

Want to share this with your team?

1. **They clone your project** (or this repo)
2. **Each person:**
   - Copies `config.json.example` to `config.json`
   - Gets their own API token from https://id.atlassian.com/manage/api-tokens
   - Fills in their credentials
   - Runs `pip install requests`
   - Tests: `python .claude/skills/confluence/confluence_api.py test-connection`

**Security:** Each person uses their own API token. Never share tokens or commit `config.json` to git!

## Advanced Usage

### Create Confluence spaces
If your space doesn't exist, you can create it:
```bash
python .claude/skills/confluence/confluence_api.py create-space \
  --key "WORK" \
  --name "Work Documentation" \
  --description "Documentation for work projects"
```

### Manual page creation
You can also create pages manually:
```bash
# Create a page
python .claude/skills/confluence/confluence_api.py create-page \
  --title "My Page" \
  --content-file content.html \
  --space "WORK"

# Create a child page
python .claude/skills/confluence/confluence_api.py create-page \
  --title "Child Page" \
  --content-file content.html \
  --space "WORK" \
  --parent-id "123456"
```

## Requirements Summary

- Python 3.7+
- `requests` library (`pip install requests`)
- Claude Code
- Confluence Cloud instance
- API token with page creation permissions

## Support

For issues or questions:
1. Check the troubleshooting section above
2. See detailed docs in `.claude/skills/confluence/config.md`
3. Open an issue on GitHub

## License

MIT License - Feel free to use and modify

## Credits

Created for use with Claude Code by Anthropic.
