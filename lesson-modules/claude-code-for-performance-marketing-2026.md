# Claude Code for Performance Marketing Professionals (2026)

## Comprehensive Research Report

---

# Table of Contents

1. [Role 1: Creative Marketer](#1-creative-marketer)
2. [Role 2: UAM (User Acquisition Manager)](#2-uam-user-acquisition-manager)
3. [Role 3: Creative Producer](#3-creative-producer)
4. [Role 4: Product Marketing Manager (PMM)](#4-product-marketing-manager-pmm)
5. [Cross-Role Infrastructure: MCP Servers](#5-cross-role-mcp-server-ecosystem)
6. [Cross-Role Infrastructure: CLAUDE.md Setup](#6-cross-role-claudemd-setup)
7. [Cross-Role Infrastructure: GitHub Actions & Scheduled Tasks](#7-cross-role-github-actions--scheduled-tasks)

---

# 1. Creative Marketer

**Role summary:** Creates ad creative concepts, writes copy, manages creative strategy.

## 1.1 Daily Tools/Technologies

- **Ad platforms:** Meta Ads Manager, Google Ads, TikTok Ads Manager
- **Copy/content tools:** Google Docs, Notion, Figma (for briefs)
- **Competitive intelligence:** Meta Ad Library, LinkedIn Ad Library, SimilarWeb
- **Collaboration:** Slack, Asana/Monday
- **Analytics:** GA4, platform-native dashboards

## 1.2 Tasks Claude Code Can Automate

### A. Ad Copy Generation at Scale

Claude Code can read a CSV of existing ad copy with performance data, identify winning patterns, and generate hundreds of new variants respecting platform character limits.

**Headless command:**
```bash
claude -p "Read ads-export.csv. Filter ads with CTR > 1.5%. For each winning ad, generate 5 headline variants (max 30 chars) and 5 description variants (max 90 chars) using PAS, AIDA, and BAB frameworks. Output to new-variants.csv with columns: original_id, framework, headline, description" \
  --allowedTools "Read,Write,Bash" \
  --output-format json
```

**Key technique:** Use two sub-agents instead of one -- one for headlines, one for descriptions -- each with tighter prompts. This produces better quality than a single monolithic prompt.

### B. Competitor Ad Intelligence

The `/spy` skill from the HeyOz Meta Ads skills package queries the Meta Ad Library API, pulls active competitor ads, classifies them by hook type (discount, testimonial, urgency, social proof), and diffs against a prior baseline to flag new trends.

**Skill command:**
```
/spy --competitors "page_id_1,page_id_2,page_id_3" --country US --save-baseline
```

For LinkedIn competitive intelligence (high-signal for B2B), Kamil Rextin (42 Agency) built a Claude Code agent that:
1. Takes a company URL as input
2. Calls a `/competitors` skill to map the competitive landscape
3. Scrapes LinkedIn Ad Library for each competitor
4. Analyzes messaging themes and volume
5. Generates a branded PDF report in ~5 minutes
6. Runs on a cron schedule (e.g., every Monday at 9am)

### C. Hook & Copy Variation Engine

The `/hooks` skill generates 50+ copy variations from a seed hook using psychological frameworks:

```
/hooks --seed "Stop wasting money on ads that don't convert" \
  --frameworks "PAS,BAB,AIDA" \
  --count 50 \
  --audience "DTC ecommerce founders, $50K-$500K/mo ad spend"
```

Output: CSV formatted for Meta's bulk upload tool, tagged by emotional register and funnel stage.

### D. Creative Fatigue Detection

The `/fatigue-scan` skill analyzes 14-day rolling time-series data and statistically detects performance decline:

```
/fatigue-scan --lookback 14 --hook-rate-floor 0.25 --ctr-decline-threshold 15
```

Flags HIGH/MEDIUM/LOW risk creatives, identifies ads below hook-rate floor, and generates specific replacement copy recommendations.

## 1.3 Relevant MCP Servers

| MCP Server | Purpose |
|---|---|
| **Meta Ads MCP** (Pipeboard) | Read/write Meta campaign data, creative performance metrics |
| **Google Ads MCP** (cohnen/google-marketing-solutions) | Campaign performance, keyword analytics |
| **TikTok Ads MCP** (AdsMCP) | Creative performance data, campaign management |
| **Slack MCP** | Post creative performance alerts, share reports |
| **Notion MCP** | Sync creative briefs, update status boards |
| **Ahrefs MCP** | Competitive content intelligence |

## 1.4 Useful Hooks

**PostToolUse hook -- Slack alert on copy generation:**
```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "curl -X POST -H 'Content-type: application/json' --data '{\"text\":\"Claude finished generating ad copy variants. Review ready in /creatives/\"}' $SLACK_WEBHOOK_URL"
          }
        ]
      }
    ]
  }
}
```

**PreToolUse hook -- Brand voice enforcement:**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Check if the content being written follows the brand voice guidelines: professional but approachable, no jargon, no superlatives like 'best' or 'amazing'. If it violates these rules, respond with {\"ok\": false, \"reason\": \"specific violation\"}."
          }
        ]
      }
    ]
  }
}
```

## 1.5 Custom Skills/Commands

### `/creative-brief` skill

```yaml
---
name: creative-brief
description: Generate a structured creative brief from campaign parameters
disable-model-invocation: true
allowed-tools: Read, Write, Bash
---

Generate a creative brief for: $ARGUMENTS

Include:
1. Campaign objective and KPIs
2. Target audience persona (demographics, psychographics, pain points)
3. Key message hierarchy (primary, secondary, supporting)
4. Tone and voice guidelines (reference @docs/brand-voice.md)
5. Platform-specific requirements (dimensions, character limits, format)
6. Competitive context (what angles competitors are using)
7. Creative do's and don'ts
8. 3 concept directions with rationale

Output as markdown to ./briefs/brief-{date}.md
```

### `/copy-matrix` skill

```yaml
---
name: copy-matrix
description: Generate a full copy testing matrix from a winning hook
disable-model-invocation: true
---

Take the winning hook: "$ARGUMENTS"

Generate a testing matrix:
- 4 headline angles (problem, benefit, comparison, social proof)
- 4 CTA styles (direct, soft, incentive, curiosity)
- 3 body copy lengths (short/medium/long)
- Tag each with: funnel_stage, emotional_register, framework_used

Output as CSV to ./copy-matrices/matrix-{date}.csv
Format: headline, body, cta, funnel_stage, emotion, framework, char_count
```

## 1.6 Headless Automation Scripts

**Weekly competitor intelligence report:**
```bash
#!/bin/bash
# Run every Monday at 8am via cron
# crontab: 0 8 * * 1 /path/to/competitor-intel.sh

claude -p "Pull active ads from Meta Ad Library for competitors: [PageID1, PageID2, PageID3]. Compare against last week's baseline in ./data/competitor-baseline.json. Identify new creative angles, messaging themes, and offer types. Generate a competitive intelligence report and save to ./reports/competitor-intel-$(date +%Y-%m-%d).md. Update the baseline file." \
  --allowedTools "Read,Write,Bash(curl *),Bash(python *)" \
  --max-turns 20 \
  --output-format json
```

**Daily creative performance digest:**
```bash
#!/bin/bash
# crontab: 0 9 * * * /path/to/creative-digest.sh

claude -p "Connect to Meta Ads via MCP. Pull performance data for all active creatives from the last 24 hours. Identify: top 3 performers by ROAS, bottom 3 by CTR, any creative with frequency > 4.0 or hook rate < 0.25. Format as a Slack message and post to #creative-team channel." \
  --allowedTools "Read,Bash,mcp__meta_ads__*,mcp__slack__*" \
  --max-turns 15
```

## 1.7 GitHub Actions / Scheduled Tasks

```yaml
# .github/workflows/weekly-creative-report.yml
name: Weekly Creative Performance Report
on:
  schedule:
    - cron: "0 9 * * 1"  # Monday 9am
  workflow_dispatch:

jobs:
  creative-report:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Generate creative report
        uses: anthropics/claude-code-action@v1
        with:
          prompt: |
            Analyze all ad creatives from the past 7 days.
            Calculate: CTR trends, ROAS by creative angle, frequency fatigue indicators.
            Generate a creative performance report with:
            - Top 5 and Bottom 5 creatives
            - Creative fatigue alerts
            - Recommended new angles based on winning patterns
            - Next week's creative testing priorities
            Save report to reports/creative-weekly-{date}.md
          allowed_tools: "Read,Write,Bash,mcp__meta_ads__*,mcp__google_ads__*"
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

---

# 2. UAM (User Acquisition Manager)

**Role summary:** Buys ads, manages campaigns on Meta/Google/TikTok, optimizes ROAS/CPI.

## 2.1 Daily Tools/Technologies

- **Ad platforms:** Meta Ads Manager, Google Ads (Search, PMax, UAC), TikTok Ads Manager, Apple Search Ads
- **Attribution/MMP:** AppsFlyer, Adjust, Branch, Singular
- **Analytics:** GA4, Amplitude, Mixpanel, internal BI dashboards
- **Data warehouse:** BigQuery, Redshift
- **Reporting:** Google Sheets, Looker, Tableau
- **Automation:** Internal scripts, Revealbot, Smartly.io

## 2.2 Tasks Claude Code Can Automate

### A. Performance Bleed Detection (Critical)

The `/bleed-check` skill monitors spend in real-time and pauses bleeding ad sets:

```
/bleed-check --threshold-spend 50 --threshold-conversions 0 --window-hours 6
```

This identifies ad sets spending above $50 with zero conversions in the last 6 hours, pauses them via API, and sends a Slack alert with metrics and Ads Manager deep links. Recommended: run every 6 hours via cron.

**Headless cron automation:**
```bash
# crontab: 0 */6 * * * /path/to/bleed-check.sh
claude -p "Check Meta Ads account for bleeding ad sets: any ad set that spent more than \$100 in the last 6 hours with 0 conversions. Pause them and post a summary to Slack #ua-alerts with ad set names, spend amounts, and Ads Manager links." \
  --allowedTools "mcp__meta_ads__*,mcp__slack__*,Read,Bash" \
  --max-turns 10 \
  --max-budget-usd 0.50
```

### B. Budget Reallocation Engine

The `/rebalance` skill shifts budget from underperformers to top performers:

```
/rebalance --roas-floor 0.85 --max-shift-pct 30 --lookback 7 --execute
```

Calculates account ROAS benchmarks, flags ad sets below 85% of average, caps reallocation at 30% per execution, and applies budget changes via API.

### C. Cross-Platform Campaign Analysis

**Headless command for daily cross-platform report:**
```bash
claude -p "Pull yesterday's performance data from Meta Ads, Google Ads, and TikTok Ads via MCP servers. Normalize metrics (CPI, ROAS, CTR, CPM) across platforms. Create a unified performance dashboard comparing: spend, installs, CPI, D1 retention proxy, ROAS by platform and campaign. Identify which platform/campaign combinations are above/below CPI targets. Output to ./reports/daily-ua-$(date +%Y-%m-%d).md and post summary to Slack #ua-team." \
  --allowedTools "mcp__meta_ads__*,mcp__google_ads__*,mcp__tiktok_ads__*,mcp__slack__*,Read,Write,Bash(python *)" \
  --max-turns 25
```

### D. UTM Link Generation & Validation

```bash
claude -p "Read the campaign plan in ./campaigns/q2-plan.csv. For each campaign row, generate properly formatted UTM links following our naming convention (lowercase, underscores, format: source_medium_campaign_content_term). Validate against our existing UTM log in Google Sheets to prevent duplicates and naming drift. Output all links to ./campaigns/q2-utm-links.csv and append to the master UTM log." \
  --allowedTools "Read,Write,mcp__google_sheets__*"
```

### E. Automated Bid & Budget Recommendations

```bash
claude -p "Read the last 30 days of campaign data from BigQuery table marketing.campaign_performance. Calculate: 7-day moving average CPI by campaign, ROAS trend by week, spend pacing vs. monthly budget. Flag campaigns where CPI is trending >15% above target. Recommend bid adjustments and budget shifts. Output recommendations as a structured JSON to ./reports/bid-recommendations.json" \
  --allowedTools "mcp__bigquery__*,Read,Write,Bash(python *)" \
  --output-format json
```

## 2.3 Relevant MCP Servers

| MCP Server | Purpose | Key Feature |
|---|---|---|
| **Pipeboard** (Meta + Google) | Unified ad platform access | OAuth auth, no credential storage |
| **Google Ads MCP** (Official) | Google Ads API bridge | Campaign data, keyword analytics |
| **Meta Ads MCP** (Pipeboard) | Meta campaign management | Read + write operations |
| **TikTok Ads MCP** (AdsMCP) | TikTok campaign management | Creative performance, budget control |
| **BigQuery MCP** (Google Cloud) | Data warehouse queries | SQL on marketing data, fully managed remote server |
| **Adspirer** | Multi-platform ad management | 5 slash commands, performance review skill |
| **Google Sheets MCP** | Report output, UTM tracking | Read/write spreadsheet data |
| **Slack MCP** | Alerts and report distribution | Real-time notifications |
| **GA4 MCP** | Traffic and conversion analysis | Audience segments, conversion paths |
| **SegmentStream** | Cross-channel attribution | Budget optimization, 30+ platform connections |
| **Windsor.ai** | Multi-touch attribution | Shows how Meta + Google work together |

## 2.4 Useful Hooks

**PostToolUse hook -- Log every MCP ad platform action:**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "mcp__meta_ads__.*|mcp__google_ads__.*|mcp__tiktok_ads__.*",
        "hooks": [
          {
            "type": "command",
            "command": "jq -c '{timestamp: (now | todate), tool: .tool_name, input: .tool_input}' >> ~/ua-audit-log.jsonl"
          }
        ]
      }
    ]
  }
}
```

**PreToolUse hook -- Safety gate for budget changes:**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "mcp__meta_ads__update.*|mcp__google_ads__update.*",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "This is a write operation to an ad platform. Check: 1) Is the budget change less than 30% of current budget? 2) Is the campaign not in learning phase? If either condition fails, respond with {\"ok\": false, \"reason\": \"explanation\"}."
          }
        ]
      }
    ]
  }
}
```

**Notification hook -- Alert when Claude needs human approval:**
```json
{
  "hooks": {
    "Notification": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "osascript -e 'display notification \"Claude needs UA approval\" with title \"UA Alert\"'"
          }
        ]
      }
    ]
  }
}
```

## 2.5 Custom Skills/Commands

### `/ua-daily` skill

```yaml
---
name: ua-daily
description: Generate daily UA performance report across all platforms
disable-model-invocation: true
allowed-tools: Read, Write, Bash, mcp__meta_ads__*, mcp__google_ads__*, mcp__tiktok_ads__*, mcp__slack__*
---

Generate daily UA report for $ARGUMENTS (default: yesterday):

1. Pull spend, installs, CPI, ROAS from Meta, Google, TikTok via MCP
2. Pull attribution data from BigQuery table `marketing.attribution`
3. Calculate: platform-level CPI, blended ROAS, D1/D7 retention proxy
4. Compare against weekly targets in ./targets/ua-targets.json
5. Identify top 3 campaigns by incremental ROAS
6. Flag any campaign with CPI > 1.3x target
7. Output full report to ./reports/ua-daily-{date}.md
8. Post executive summary to Slack #ua-team
```

### `/campaign-launch` skill

```yaml
---
name: campaign-launch
description: Set up new campaigns across platforms with proper structure
disable-model-invocation: true
---

Launch campaign: $ARGUMENTS

Pre-launch checklist:
1. Validate UTM parameters against naming convention
2. Check pixel/CAPI tracking is firing on target events
3. Verify audience sizes meet minimum thresholds
4. Confirm creative assets meet platform specs
5. Generate campaign structure (campaign > ad set > ad mapping)
6. Set initial budgets per the daily budget allocation in ./config/budgets.json
7. Create dry-run manifest for review before deploying

Output: campaign-manifest.json for review, then deploy on approval.
```

### `/weekly-report` skill (Meta-specific)

```
/weekly-report --date-range last_7d --compare previous_7d --format slack,markdown --top-n 3 --slack-channel #growth
```

Pulls 7-day metrics, calculates WoW changes with % deltas, identifies top/bottom performers, generates 3 action recommendations, and posts to Slack.

## 2.6 Headless Automation Scripts

**Hourly spend pacing monitor:**
```bash
#!/bin/bash
# crontab: 0 * * * * /path/to/spend-pacing.sh
HOUR=$(date +%H)
claude -p "It's hour $HOUR of the day. Check daily spend pacing across all active Meta Ads campaigns. Calculate: current spend vs. expected spend at this hour (linear pacing). Flag any campaign >20% over pace or >30% under pace. Post alerts to Slack #ua-alerts only if issues found." \
  --allowedTools "mcp__meta_ads__*,mcp__slack__*,Read" \
  --max-turns 8 \
  --max-budget-usd 0.30
```

**Monthly budget allocation optimizer:**
```bash
#!/bin/bash
# crontab: 0 10 1 * * /path/to/monthly-budget.sh
claude -p "Analyze last 30 days of campaign performance from BigQuery. Calculate marginal ROAS by platform and campaign type. Generate an optimal budget allocation for next month's \$X total budget, maximizing blended ROAS subject to minimum spend constraints per platform. Output allocation to ./budgets/allocation-$(date +%Y-%m).json with rationale." \
  --allowedTools "mcp__bigquery__*,Read,Write,Bash(python *)" \
  --max-turns 20
```

## 2.7 GitHub Actions / Scheduled Tasks

```yaml
# .github/workflows/ua-bleed-monitor.yml
name: UA Bleed Monitor (Every 6 Hours)
on:
  schedule:
    - cron: "0 */6 * * *"

jobs:
  bleed-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run bleed detection
        uses: anthropics/claude-code-action@v1
        with:
          prompt: |
            Check all active Meta and Google ad sets.
            Flag any ad set spending >$100 with 0 conversions in last 6 hours.
            Pause flagged ad sets.
            Post alert to Slack with details and Ads Manager links.
          allowed_tools: "mcp__meta_ads__*,mcp__google_ads__*,mcp__slack__*"
          max_turns: 10
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          META_ACCESS_TOKEN: ${{ secrets.META_ACCESS_TOKEN }}
```

```yaml
# .github/workflows/daily-ua-report.yml
name: Daily UA Performance Report
on:
  schedule:
    - cron: "0 9 * * *"  # 9am daily

jobs:
  daily-report:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Generate daily UA report
        uses: anthropics/claude-code-action@v1
        with:
          prompt: |
            Generate a comprehensive daily UA report:
            1. Pull data from Meta, Google, TikTok via MCP
            2. Calculate CPI, ROAS, spend by platform/campaign
            3. Compare to yesterday and 7-day average
            4. Flag anomalies (>20% CPI spike, >15% ROAS drop)
            5. Post summary to Slack #ua-team
            6. Save full report to reports/
          allowed_tools: "mcp__meta_ads__*,mcp__google_ads__*,mcp__tiktok_ads__*,mcp__bigquery__*,mcp__slack__*,Read,Write,Bash(python *)"
```

---

# 3. Creative Producer

**Role summary:** Designer who receives briefs from creative marketers and produces actual visuals (ads, banners, landing pages).

## 3.1 Daily Tools/Technologies

- **Design:** Figma, Adobe Creative Suite (Photoshop, Illustrator, After Effects)
- **Prototyping:** Figma, Framer
- **Asset management:** Google Drive, Dropbox, Brandfolder
- **Collaboration:** Slack, Asana/Monday, Notion
- **Video:** CapCut, Premiere Pro, DaVinci Resolve
- **AI image generation:** Midjourney, DALL-E, Gemini

## 3.2 Tasks Claude Code Can Automate

### A. Bulk Creative Variation Generation

The `/bulk-creative` skill generates 50-500 ad variations programmatically:

```
/bulk-creative --brief "Product: AI marketing tool, Offer: Free trial, Audience: Growth marketers" \
  --variations 200 \
  --formats "1:1,9:16,1.91:1" \
  --seed-headlines headlines.csv
```

This creates React templates with swappable headlines, CTAs, colors, and image positions, then renders to PNG via Puppeteer (headless browser). Generates a manifest JSON compatible with the `/deploy-ads` skill.

### B. Figma MCP Integration (Two-Way)

As of March 2026, the Figma MCP server enables:

**Design to Code:** Pull design context (variables, components, layout data) directly into Claude Code to generate production code from Figma designs.

**Code to Design (new in Feb 2026):** Capture live UI built with Claude Code and convert it into fully editable Figma frames. This is currently supported in Claude Code only.

**Configuration (remote server, recommended):**
```json
{
  "mcpServers": {
    "figma": {
      "url": "https://mcp.figma.com",
      "transport": "streamable-http"
    }
  }
}
```

**Practical creative workflow:**
```
> Read the ad template frames on Figma page "Q2-Ads" and generate 50
  variations using the headline list in headlines.csv. Export each
  variation as a PNG in 1:1 and 9:16 formats.
```

### C. Automated Ad Image Generation Pipeline

Full pipeline from brand spec to deployed ads (as demonstrated in the NoCodeSaaS case study):

1. **Brand constants file** -- colors, fonts, value props, audience profiles
2. **Creative matrix** -- topics x personas x visual styles (e.g., 7 x 5 x 4 = 140 unique ads)
3. **Image generation** -- via Gemini API with brand-specific prompts
4. **Quality control gallery** -- local HTML gallery with delete buttons for rapid QA
5. **Meta API upload** -- automatic campaign structure creation

**Scale achieved:** 250 different ads for a single conference, produced by a non-developer.

### D. Landing Page Generation

Claude Code generates production-ready HTML/CSS/JS landing pages:

```bash
claude -p "Create a landing page for our Q2 campaign. Product: [product]. Offer: [offer]. Use our brand colors from ./brand/constants.json. Include: hero section with headline + CTA, social proof section with 3 testimonials, feature grid (3 columns), FAQ section, sticky CTA bar. Optimize for mobile. Add UTM parameter capture via JavaScript. Output to ./landing-pages/q2-campaign/" \
  --allowedTools "Read,Write,Bash"
```

For A/B testing, generate variants:
```bash
claude -p "Create 4 variants of the landing page in ./landing-pages/q2-campaign/: Variant A (original), Variant B (move social proof above fold), Variant C (video hero instead of image), Variant D (shorter form with only email field). Each variant should be a separate HTML file with identical tracking but different test IDs." \
  --allowedTools "Read,Write,Bash"
```

## 3.3 Relevant MCP Servers

| MCP Server | Purpose |
|---|---|
| **Figma MCP** (Official remote server) | Two-way design-code sync, component access, layer generation |
| **Canva MCP** | Template-based design generation |
| **Google Drive MCP** | Asset storage and retrieval |
| **Slack MCP** | Brief intake, review requests, delivery notifications |
| **Notion MCP** | Brief management, status tracking |

## 3.4 Useful Hooks

**PostToolUse hook -- Auto-optimize images after generation:**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "FILE=$(jq -r '.tool_input.file_path' | head -1); if [[ \"$FILE\" == *.png ]]; then optipng -o2 \"$FILE\" 2>/dev/null; fi"
          }
        ]
      }
    ]
  }
}
```

**Stop hook -- Verify all creative assets meet platform specs:**
```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "agent",
            "prompt": "Check all files in ./creatives/output/. Verify: 1) Image dimensions match platform specs (1080x1080 for feed, 1080x1920 for stories), 2) File sizes are under 30MB, 3) No text covers more than 20% of image area. Report any violations.",
            "timeout": 60
          }
        ]
      }
    ]
  }
}
```

## 3.5 Custom Skills/Commands

### `/resize-batch` skill

```yaml
---
name: resize-batch
description: Resize ad creatives to multiple platform formats
disable-model-invocation: true
allowed-tools: Bash, Read, Write
---

Resize creatives in $ARGUMENTS directory to all platform formats:

1. Read all image files in the specified directory
2. For each image, create versions:
   - 1080x1080 (Meta/Instagram feed)
   - 1080x1920 (Stories/Reels/TikTok)
   - 1200x628 (Meta link ads / Google Display)
   - 300x250 (Google Display banner)
   - 728x90 (Google Display leaderboard)
3. Use smart cropping to preserve focal point
4. Save to ./output/{format_name}/{original_filename}
5. Generate a manifest.json mapping originals to all variants
```

### `/creative-qc` skill

```yaml
---
name: creative-qc
description: Quality check creative assets against platform requirements
disable-model-invocation: true
---

QC creative assets in: $ARGUMENTS

Check every file against:
- Platform dimension requirements (Meta, Google, TikTok)
- File size limits (Meta: 30MB images, 4GB video; Google: 5.12MB)
- Aspect ratio compliance
- Color profile (sRGB for web)
- Text-to-image ratio (flag if >20% text area)

Output QC report to ./reports/creative-qc-{date}.md with PASS/FAIL per asset.
```

## 3.6 Headless Automation Scripts

**Batch render creative variations from Figma:**
```bash
#!/bin/bash
claude -p "Read the creative brief at ./briefs/latest-brief.md. Connect to Figma via MCP and read the template frames on page 'Ad Templates'. For each template frame, substitute the headline/CTA/offer text from the brief. Export all variations as PNG in 1:1 and 9:16 formats to ./creatives/batch-$(date +%Y%m%d)/. Generate a manifest.json listing all outputs." \
  --allowedTools "Read,Write,Bash,mcp__figma__*" \
  --max-turns 30
```

**Automated landing page deployment pipeline:**
```bash
#!/bin/bash
claude -p "Read the approved landing page in ./landing-pages/approved/. Minify HTML/CSS/JS. Add GA4 tracking snippet from ./config/tracking.js. Add UTM parameter capture script. Deploy to Vercel using the CLI. Output the live URL." \
  --allowedTools "Read,Write,Bash" \
  --max-turns 15
```

## 3.7 GitHub Actions / Scheduled Tasks

```yaml
# .github/workflows/creative-asset-pipeline.yml
name: Creative Asset Pipeline
on:
  push:
    paths:
      - 'briefs/*.md'  # Trigger when a new brief is pushed

jobs:
  generate-assets:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Generate creative variations
        uses: anthropics/claude-code-action@v1
        with:
          prompt: |
            Read the latest brief in briefs/.
            Generate ad copy variations (5 headlines, 5 descriptions).
            Create HTML mockups for each variation in creatives/html/.
            Run QC checks on all outputs.
            Create a PR with the generated assets for review.
          allowed_tools: "Read,Write,Bash"
```

---

# 4. Product Marketing Manager (PMM)

**Role summary:** Creates funnels, validates ideas, runs A/B tests, analyzes user behavior.

## 4.1 Daily Tools/Technologies

- **Analytics:** Amplitude, Mixpanel, GA4, Heap
- **A/B testing:** Optimizely, VWO, LaunchDarkly, Statsig
- **Data warehouse:** BigQuery, Snowflake
- **Product analytics:** FullStory, Hotjar, LogRocket
- **CRM/lifecycle:** HubSpot, Braze, Customer.io, Iterable
- **Research:** UserTesting, Typeform, SurveyMonkey
- **Docs/collaboration:** Notion, Google Docs, Confluence

## 4.2 Tasks Claude Code Can Automate

### A. Funnel Analysis & Drop-off Detection

```bash
claude -p "Query Mixpanel via MCP for our signup funnel: page_view > signup_start > email_verified > onboarding_complete > first_value_action. Pull last 30 days data. Calculate: conversion rate at each step, drop-off rate, median time between steps. Compare to previous 30 days. Identify the biggest drop-off point and hypothesize 3 causes based on the data patterns. Output to ./reports/funnel-analysis-$(date +%Y-%m-%d).md" \
  --allowedTools "mcp__mixpanel__*,Read,Write,Bash(python *)"
```

### B. A/B Test Analysis & Hypothesis Generation

The `/ab-test-setup` skill from the Marketing Skills package designs and plans experiments:

```
/ab-test-setup "Test moving social proof above the fold on pricing page"
```

For automated A/B test result analysis:
```bash
claude -p "Read the A/B test results in ./data/ab-test-pricing-page.csv. Calculate: conversion rate per variant, statistical significance (chi-squared), confidence interval, sample size adequacy, expected revenue impact at current traffic levels. Determine if we have enough data to call a winner. If yes, recommend the winning variant with projected annual revenue impact. If no, calculate days needed to reach 95% significance. Output analysis to ./reports/ab-test-results.md" \
  --allowedTools "Read,Write,Bash(python *)"
```

### C. User Behavior Cohort Analysis

```bash
claude -p "Connect to Amplitude via MCP. Pull user cohorts: users who completed onboarding in last 30 days. Segment by: acquisition source (organic, paid_meta, paid_google, referral). For each cohort, calculate: D1, D7, D30 retention, average sessions per week, feature adoption rate (top 5 features). Identify which acquisition source produces highest-LTV users. Output to ./reports/cohort-analysis-$(date +%Y-%m-%d).md" \
  --allowedTools "mcp__amplitude__*,Read,Write,Bash(python *)"
```

### D. Landing Page CRO Analysis

The `/page-cro` skill from the Marketing Skills package:

```
/page-cro https://yoursite.com/pricing
```

This evaluates: value proposition clarity, CTA dominance, objection handling, social proof specificity, headline-to-source alignment. Produces structured reports with scores and prioritized recommendations.

For competitive CRO comparison:
```bash
claude -p "Analyze our pricing page at https://oursite.com/pricing and 3 competitor pricing pages: [url1, url2, url3]. Score each on: value prop clarity (1-10), CTA visibility (1-10), social proof strength (1-10), objection handling (1-10), mobile experience (1-10). Identify our biggest gaps and generate 5 specific improvement hypotheses ranked by ICE (Impact/Confidence/Ease) framework." \
  --allowedTools "Read,Write,Bash(python *),Bash(curl *)"
```

### E. Onboarding Optimization

```
/onboarding-cro --url https://app.oursite.com/onboarding --funnel "signup,email_verify,profile_setup,first_action,aha_moment"
```

### F. Automated Experiment Velocity

Generate 64 landing page variants for testing:
```bash
claude -p "Read our current pricing page at ./pages/pricing.html. Generate 8 variants, each testing a single variable: Variant 1 (headline change), Variant 2 (CTA copy change), Variant 3 (social proof placement), Variant 4 (pricing table layout), Variant 5 (hero image vs. video), Variant 6 (form length), Variant 7 (urgency element), Variant 8 (trust badges). Each variant as a separate HTML file. Add Optimizely experiment tracking code. Output to ./experiments/pricing-test-$(date +%Y%m%d)/" \
  --allowedTools "Read,Write,Bash"
```

## 4.3 Relevant MCP Servers

| MCP Server | Purpose | Key Feature |
|---|---|---|
| **Amplitude MCP** (Official) | User behavior analytics | Funnels, retention cohorts, event trends, experiments |
| **Mixpanel MCP** (Official) | Product analytics | Funnels, flows, retention, session replays via natural language |
| **GA4 MCP** | Traffic & conversion analytics | Audience segments, conversion paths |
| **BigQuery MCP** (Google Cloud, fully managed) | Data warehouse queries | SQL on marketing data warehouse |
| **HubSpot MCP** | CRM & pipeline analysis | Contact queries, campaign performance |
| **Notion MCP** | Documentation & briefs | Experiment docs, PRDs, results |
| **Slack MCP** | Team communication | Test result alerts, stakeholder updates |
| **Google Sheets MCP** | Report output, data sharing | Experiment trackers, metrics dashboards |
| **Klaviyo MCP** | Email/SMS analytics | Flow performance, segment analysis |

## 4.4 Useful Hooks

**Stop hook -- Verify analysis completeness:**
```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Check if the analysis includes: 1) Statistical significance calculation, 2) Sample size adequacy check, 3) Confidence intervals, 4) Practical significance (not just statistical), 5) Recommended next action. If any are missing, respond with {\"ok\": false, \"reason\": \"Missing: [list]\"}."
          }
        ]
      }
    ]
  }
}
```

**SessionStart hook -- Load experiment context:**
```json
{
  "hooks": {
    "SessionStart": [
      {
        "matcher": "startup",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Current active experiments:' && cat ./experiments/active-experiments.json 2>/dev/null || echo 'No active experiments file found'"
          }
        ]
      }
    ]
  }
}
```

## 4.5 Custom Skills/Commands

### `/funnel-health` skill

```yaml
---
name: funnel-health
description: Automated funnel health check across key conversion flows
disable-model-invocation: true
allowed-tools: Read, Write, Bash, mcp__amplitude__*, mcp__mixpanel__*, mcp__bigquery__*
---

Run funnel health check for: $ARGUMENTS (default: all funnels)

For each funnel defined in ./config/funnels.json:
1. Pull last 7 days conversion data from Amplitude/Mixpanel via MCP
2. Calculate step-by-step conversion rates
3. Compare to 30-day baseline
4. Flag any step with >10% conversion drop vs. baseline
5. Segment by: platform (iOS/Android/Web), acquisition source, user cohort
6. Generate hypothesis for each flagged drop
7. Suggest 3 A/B test ideas per flagged step, ranked by ICE

Output: ./reports/funnel-health-{date}.md
Post summary to Slack #product-marketing
```

### `/experiment-tracker` skill

```yaml
---
name: experiment-tracker
description: Track and analyze active A/B tests
disable-model-invocation: true
---

Experiment tracking for: $ARGUMENTS

1. Read active experiments from ./experiments/active-experiments.json
2. For each experiment, pull latest results from the testing platform
3. Calculate: current sample size, conversion rate per variant, p-value, days remaining to significance
4. Classify each test: WINNER_FOUND, NEEDS_MORE_DATA, NO_EFFECT, NEGATIVE_RESULT
5. For WINNER_FOUND: calculate projected annual revenue impact
6. For NEEDS_MORE_DATA: calculate exact days remaining
7. Update ./experiments/active-experiments.json with latest status
8. Post digest to Slack #experiments
```

### `/user-research-synthesis` skill

```yaml
---
name: user-research-synthesis
description: Synthesize user research data into actionable insights
disable-model-invocation: true
---

Synthesize research data from: $ARGUMENTS

1. Read all files in the specified directory (interview transcripts, survey results, session recordings notes)
2. Extract key themes using affinity mapping approach
3. Identify top 5 user pain points with frequency count
4. Map pain points to current funnel stages
5. Generate insight cards: Observation > Inference > Opportunity > Test Hypothesis
6. Prioritize opportunities using ICE framework
7. Output to ./research/synthesis-{date}.md
```

## 4.6 Headless Automation Scripts

**Weekly funnel health report:**
```bash
#!/bin/bash
# crontab: 0 9 * * 1 /path/to/funnel-health.sh
claude -p "Run a comprehensive funnel health check. Pull data from Amplitude for all 4 core funnels: signup, onboarding, activation, monetization. Compare this week to last week and to 30-day average. Flag any step with >5% conversion drop. For each flag, generate 3 hypotheses. Post executive summary to Slack #product-marketing and full report to Notion." \
  --allowedTools "mcp__amplitude__*,mcp__slack__*,mcp__notion__*,Read,Write,Bash(python *)" \
  --max-turns 25
```

**Daily experiment status check:**
```bash
#!/bin/bash
# crontab: 0 8 * * * /path/to/experiment-check.sh
claude -p "Check status of all active A/B tests listed in ./experiments/active.json. For each test, calculate current significance level. If any test has reached p<0.05 with sufficient sample size, flag as READY_TO_CALL and post to Slack #experiments with recommendation. If any test is running >30 days without significance, flag as CONSIDER_STOPPING." \
  --allowedTools "mcp__amplitude__*,mcp__slack__*,Read,Write,Bash(python *)" \
  --max-turns 15
```

**Automated cohort retention report:**
```bash
#!/bin/bash
# crontab: 0 10 1 * * /path/to/monthly-cohort.sh
claude -p "Generate monthly cohort retention analysis. Pull from Amplitude: D1, D7, D14, D30 retention for each weekly signup cohort in the past month. Segment by acquisition source and pricing plan. Identify which cohorts have improving/declining retention. Correlate with any product changes (read CHANGELOG.md). Generate retention curve visualizations as HTML. Save report to ./reports/retention-$(date +%Y-%m).html" \
  --allowedTools "mcp__amplitude__*,Read,Write,Bash(python *)" \
  --max-turns 30
```

## 4.7 GitHub Actions / Scheduled Tasks

```yaml
# .github/workflows/weekly-funnel-report.yml
name: Weekly Funnel Health Report
on:
  schedule:
    - cron: "0 9 * * 1"

jobs:
  funnel-report:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Generate funnel report
        uses: anthropics/claude-code-action@v1
        with:
          prompt: |
            Run comprehensive funnel health analysis:
            1. Pull data from Amplitude/Mixpanel for all core funnels
            2. Calculate WoW conversion rate changes at each step
            3. Identify biggest drop-off points
            4. Generate hypotheses and test recommendations
            5. Save full report and post Slack summary
          allowed_tools: "mcp__amplitude__*,mcp__mixpanel__*,mcp__slack__*,Read,Write,Bash(python *)"
```

```yaml
# .github/workflows/experiment-monitor.yml
name: Daily Experiment Monitor
on:
  schedule:
    - cron: "0 8 * * *"

jobs:
  check-experiments:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Check experiment status
        uses: anthropics/claude-code-action@v1
        with:
          prompt: |
            Check all active A/B tests in experiments/active.json.
            Calculate statistical significance for each.
            If any test has reached significance, alert the team.
            Update experiment status file and commit changes.
          allowed_tools: "mcp__amplitude__*,mcp__slack__*,Read,Write,Bash(python *),Bash(git *)"
```

---

# 5. Cross-Role MCP Server Ecosystem

## Complete MCP Server Reference for Performance Marketing

### Ad Platform MCP Servers

| Server | GitHub/Source | Platforms | Capabilities |
|---|---|---|---|
| **Pipeboard Meta Ads** | pipeboard-co/meta-ads-mcp | Meta (FB, IG) | Read/write campaigns, creatives, metrics; OAuth auth |
| **Pipeboard Google Ads** | pipeboard.co | Google Ads | Campaign data, keyword analytics |
| **Google Ads MCP** (Official) | google-marketing-solutions/google_ads_mcp | Google Ads | Full API access via MCP |
| **Google Ads MCP** (cohnen) | cohnen/mcp-google-ads | Google Ads | Campaign info, performance metrics, keyword analytics |
| **TikTok Ads MCP** | AdsMCP/tiktok-ads-mcp-server | TikTok | Campaign management, creative performance |
| **Adspirer** | adspirer.com | Meta + Google + TikTok | 5 slash commands, multi-platform management |
| **SegmentStream** | segmentstream.com | 30+ platforms | Cross-channel attribution, budget optimization |
| **Windsor.ai** | windsor.ai | Multiple | Multi-touch attribution |
| **Adzviser** | adzviser.com | Google + Meta | High-level dashboard view |

### Analytics & Data MCP Servers

| Server | Source | Key Feature |
|---|---|---|
| **Amplitude MCP** (Official) | amplitude.com/docs/amplitude-ai/amplitude-mcp | Funnels, retention, events, experiments |
| **Mixpanel MCP** (Official) | docs.mixpanel.com/docs/features/mcp | Funnels, flows, retention, metadata management |
| **GA4 MCP** | Community | Traffic trends, conversion paths, audience segments |
| **BigQuery MCP** (Google Cloud) | Fully managed remote server | SQL queries on data warehouse |
| **Ahrefs MCP** | Official | SEO data, backlink analysis, keyword research |
| **Semrush MCP** | Official | Competitive intelligence, domain analytics |

### Design & Creative MCP Servers

| Server | Source | Key Feature |
|---|---|---|
| **Figma MCP** (Official remote) | developers.figma.com | Two-way design-code sync, layer generation, component access |
| **Canva MCP** | Via Composio | Template-based design access |

### Collaboration & Productivity MCP Servers

| Server | Source | Key Feature |
|---|---|---|
| **Slack MCP** (Official Anthropic) | Built-in connector | Channel messaging, alerts, report distribution |
| **Notion MCP** (Official Anthropic) | Built-in connector | Page CRUD, database queries, search |
| **Google Sheets MCP** | Multiple implementations | Spreadsheet read/write, chart generation |
| **Google Drive MCP** (Official Anthropic) | Built-in connector | File access, asset retrieval |
| **HubSpot MCP** | Official | CRM data, pipeline analysis, campaign tracking |
| **Klaviyo MCP** | Official | Email/SMS metrics, flow analytics |

### Automation & Orchestration MCP Servers

| Server | Source | Key Feature |
|---|---|---|
| **Zapier MCP** | Official | 8,000+ app connections, lead routing |
| **Make MCP** | Official | Multi-step workflows, data enrichment |
| **n8n MCP** | Self-hosted | Full programming flexibility, self-hosted workflows |
| **Composio** | composio.dev | Bridge to 150+ marketing apps, OAuth management |

### MCP Configuration Example (.mcp.json)

```json
{
  "mcpServers": {
    "meta-ads": {
      "command": "npx",
      "args": ["-y", "@pipeboard/meta-ads-mcp"],
      "env": {
        "META_ACCESS_TOKEN": "${META_ACCESS_TOKEN}",
        "META_ACCOUNT_ID": "${META_ACCOUNT_ID}"
      }
    },
    "google-ads": {
      "command": "npx",
      "args": ["-y", "@google-marketing-solutions/google_ads_mcp"],
      "env": {
        "GOOGLE_ADS_DEVELOPER_TOKEN": "${GOOGLE_ADS_DEVELOPER_TOKEN}",
        "GOOGLE_ADS_CUSTOMER_ID": "${GOOGLE_ADS_CUSTOMER_ID}"
      }
    },
    "figma": {
      "url": "https://mcp.figma.com",
      "transport": "streamable-http"
    },
    "amplitude": {
      "url": "https://mcp.amplitude.com/mcp",
      "transport": "streamable-http"
    },
    "mixpanel": {
      "url": "https://mcp.mixpanel.com",
      "transport": "streamable-http"
    },
    "bigquery": {
      "url": "https://mcp.googleapis.com/bigquery",
      "transport": "streamable-http"
    },
    "slack": {
      "command": "npx",
      "args": ["-y", "@anthropic/slack-mcp"],
      "env": {
        "SLACK_BOT_TOKEN": "${SLACK_BOT_TOKEN}"
      }
    },
    "google-sheets": {
      "command": "npx",
      "args": ["-y", "mcp-google-sheets"],
      "env": {
        "GOOGLE_CREDENTIALS": "${GOOGLE_CREDENTIALS}"
      }
    },
    "notion": {
      "command": "npx",
      "args": ["-y", "@anthropic/notion-mcp"],
      "env": {
        "NOTION_TOKEN": "${NOTION_TOKEN}"
      }
    }
  }
}
```

---

# 6. Cross-Role CLAUDE.md Setup

## Recommended CLAUDE.md Structure for Marketing Teams

```markdown
# Marketing Team Context

## Brand
- Brand guide: @docs/brand-voice.md
- Glossary: @docs/glossary.md
- Anti-personas: @docs/anti-personas.md

## Naming Conventions
- UTM format: lowercase, underscores only
- Source values: meta, google, tiktok, email, organic
- Medium values: paid_social, paid_search, cpc, cpm, email, organic
- Campaign format: {product}_{objective}_{audience}_{date}

## Performance Thresholds
- ROAS floor: 0.85x account average
- CPI ceiling: 1.3x target
- Frequency cap: 4.0
- Hook rate floor: 0.25
- CTR minimum: 0.8%
- Bleed threshold: $100 spend with 0 conversions

## Active Experiments
- See: @experiments/active-experiments.json

## Platform-Specific Rules
- Meta: Always use CAPI + pixel deduplication
- Google: Never recommend Broad Match without Smart Bidding
- Learning phase: No edits during active learning
- Special Ad Categories: Check housing/credit/finance compliance

## Reporting Cadence
- Daily: UA performance digest (9am, Slack #ua-team)
- Weekly: Creative report (Monday 9am), Funnel health (Monday 9am)
- Monthly: Cohort retention, Budget reallocation

## Directory Structure
- /briefs -- Creative briefs
- /campaigns -- Campaign manifests and UTM logs
- /creatives -- Generated ad assets
- /reports -- All automated reports
- /experiments -- A/B test configs and results
- /data -- Raw data exports
- /config -- Thresholds, targets, funnel definitions
```

## Separate Context Files

**docs/brand-voice.md:**
- Tone: Professional but approachable
- Prohibited words: "best", "amazing", "revolutionary", "game-changing"
- Required elements: Specific numbers over vague claims
- Voice samples from top-performing copy

**docs/metrics-definitions.md:**
- LTV calculation methodology
- CAC calculation (include/exclude list)
- ROAS definition (7-day click-through vs. 1-day view-through)
- Cohort retention methodology

**config/funnels.json:**
- Definition of all tracked funnels with step names and event names

---

# 7. Cross-Role GitHub Actions & Scheduled Tasks

## Complete Automation Schedule

| Time | Frequency | Task | Primary Role |
|---|---|---|---|
| Every 6 hours | 4x daily | Bleed detection (pause bleeding ad sets) | UAM |
| Every hour | Hourly | Spend pacing monitor | UAM |
| 8:00 AM daily | Daily | Experiment status check | PMM |
| 9:00 AM daily | Daily | UA performance report | UAM |
| 9:00 AM daily | Daily | Creative performance digest | Creative Marketer |
| 9:00 AM Monday | Weekly | Creative performance report | Creative Marketer |
| 9:00 AM Monday | Weekly | Funnel health report | PMM |
| 9:00 AM Monday | Weekly | Competitor ad intelligence | Creative Marketer |
| 9:00 AM Monday | Weekly | UA weekly summary | UAM |
| 10:00 AM 1st of month | Monthly | Cohort retention analysis | PMM |
| 10:00 AM 1st of month | Monthly | Budget allocation optimizer | UAM |

## Pre-Built Skill Packages Worth Installing

### 1. AI Marketing Suite (zubair-trabzada/ai-marketing-claude)
- 15 marketing skills with parallel subagents
- Skills: audit, copy, emails, social, ads, funnel, competitors, landing, launch, proposal, report, report-pdf, seo, brand
- PDF report generation via reportlab
- Command pattern: `/market [skill-name] [parameter]`

### 2. Claude Ads (AgriciDaniel/claude-ads)
- 13 slash commands covering Google, Meta, YouTube, LinkedIn, TikTok, Microsoft, Apple
- 6 parallel audit agents running 190+ checks
- Weighted scoring system (Ads Health Score 0-100)
- 11 industry templates (SaaS, ecommerce, mobile-app, etc.)
- Command pattern: `/ads [platform/action] [parameter]`

### 3. Digital Marketing Pro (indranilbanerjee/digital-marketing-pro)
- 118 slash commands with `/dm:` prefix
- 25 specialized agents
- 65 Python scripts for deterministic execution
- 67 MCP server integrations (14 HTTP connectors)
- Multilingual support

### 4. Marketing Skills (coreyhaines31/marketingskills)
- 37 skills across 9 categories
- CRO: page-cro, signup-flow-cro, onboarding-cro, form-cro, popup-cro, paywall-upgrade-cro
- Content: copywriting, copy-editing, cold-email, email-sequence, social-content
- SEO: seo-audit, ai-seo, programmatic-seo, site-architecture
- Paid: paid-ads, ad-creative
- Measurement: analytics-tracking, ab-test-setup
- Foundation skill: product-marketing-context (referenced by all other skills)

### 5. 10 Meta Ads Skills (HeyOz)
- /spy, /bulk-creative, /deploy-ads, /bleed-check, /fatigue-scan
- /rebalance, /setup-capi, /hooks (copy engine), /audience-audit, /weekly-report
- Estimated savings: 25-30 hours/week, $2,500-$4,500/week in recovered capacity

---

## Key Statistics (2026)

- Claude Code annualized revenue run rate: $2.5 billion (March 2026)
- 42.8% of developers and technical marketers use Claude or Claude Code
- Marketing teams report 75% reduction in repetitive strategic analysis time
- 300% average ROI increase for performance marketing with agentic layers
- 15-20% reduction in CAC for companies implementing AI-driven marketing automation (McKinsey)
- Non-developer API revenue from Anthropic surged 410% in 2026
- MCP ecosystem: 10,000+ servers as of early 2026

---

## Sources

- [How to Use Claude Code for Growth Marketing Automation in 2026](https://stormy.ai/blog/claude-code-growth-marketing-automation-2026)
- [15 Best MCP Servers for Marketers in 2026 - SegmentStream](https://segmentstream.com/blog/articles/best-mcp-servers-for-marketers)
- [Claude Code for Marketing - Firecrawl](https://www.firecrawl.dev/blog/claude-code-for-marketing)
- [What 4 Gen Marketers Are Building with Claude Code - MKT1](https://newsletter.mkt1.co/p/real-marketers-claude-code-builds)
- [10 Claude Code Projects for Marketing Teams - AdventurePPC](https://www.adventureppc.com/blog/10-claude-code-projects-for-marketing-teams-to-try-this-week)
- [10 Claude Code Skills for Meta Ads - HeyOz](https://heyoz.com/blogs/claude-code-skills-for-meta-ads)
- [Claude Ads - 190+ Checks Audit Skill](https://github.com/AgriciDaniel/claude-ads)
- [AI Marketing Suite for Claude Code](https://github.com/zubair-trabzada/ai-marketing-claude)
- [Digital Marketing Pro Plugin](https://github.com/indranilbanerjee/digital-marketing-pro)
- [Marketing Skills for Claude Code](https://github.com/coreyhaines31/marketingskills)
- [How I Built an Automated Meta Ad Machine - NoCodeSaaS](https://www.nocodesaas.io/p/how-i-built-an-automated-ad-machine)
- [Claude Code for Growth Marketing - DEV Community](https://dev.to/danishashko/claude-code-for-growth-marketing-hell-yeah-i7i)
- [Maximizing Creative Velocity with Claude Code - Stormy AI](https://stormy.ai/blog/maximizing-creative-velocity-claude-code-meta-ads)
- [Building Marketing AI Agent Army - Stormy AI](https://stormy.ai/blog/how-to-build-marketing-ai-agent-army-claude-skills)
- [Pipeboard - Ad Platform MCP](https://pipeboard.co/)
- [Google Ads MCP - Official Google](https://developers.google.com/google-ads/api/docs/developer-toolkit/mcp-server)
- [Meta Ads MCP - Pipeboard GitHub](https://github.com/pipeboard-co/meta-ads-mcp)
- [TikTok Ads MCP Server](https://github.com/AdsMCP/tiktok-ads-mcp-server)
- [Figma MCP Server Guide](https://help.figma.com/hc/en-us/articles/32132100833559-Guide-to-the-Figma-MCP-server)
- [Claude Code to Figma - Figma Blog](https://www.figma.com/blog/introducing-claude-code-to-figma/)
- [Amplitude MCP Server - Official](https://amplitude.com/docs/amplitude-ai/amplitude-mcp)
- [Mixpanel MCP Server - Official](https://docs.mixpanel.com/docs/features/mcp)
- [BigQuery MCP Server - Google Cloud](https://cloud.google.com/blog/products/data-analytics/using-the-fully-managed-remote-bigquery-mcp-server-to-build-data-ai-agents)
- [Google Sheets MCP - Composio](https://composio.dev/toolkits/googlesheets/framework/claude-code)
- [Claude Code Hooks Guide - Official Docs](https://code.claude.com/docs/en/hooks-guide)
- [Claude Code Headless Mode - Official Docs](https://code.claude.com/docs/en/headless)
- [Claude Code Skills - Official Docs](https://code.claude.com/docs/en/skills)
- [Claude Code GitHub Actions - Official](https://code.claude.com/docs/en/github-actions)
- [Adspirer - Claude Code Setup](https://www.adspirer.com/docs/ai-clients/claude-code)
- [Why Learning Claude Code is Smart for Marketers - AdventurePPC](https://www.adventureppc.com/blog/why-learning-claude-code-in-2026-is-the-smartest-career-move-for-marketers)
- [Claude Code for Non-Coders - Stormy AI](https://stormy.ai/blog/agentic-engineering-guide-claude-code-marketers)
- [MCP Servers for Marketing B2B SaaS - GrowthSpree](https://www.growthspreeofficial.com/blogs/mcp-servers-b2b-saas-marketing-complete-guide)
- [Automate Meta Ads with Claude AI and MCP](https://dev.to/rupa_tiwari_dd308948d710f/how-to-automate-meta-ads-with-claude-ai-and-mcp-real-workflows-real-results-m9o)
- [Claude Code Scheduled Tasks Guide](https://smartscope.blog/en/generative-ai/claude/claude-code-scheduled-automation-guide/)
