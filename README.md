# Pool Chemistry Advisor

AI-powered pool water chemistry analysis using the **[Trouble Free Pool (TFP)](https://www.troublefreepool.com/) methodology**. Give it your test results and weather data, get specific dosing amounts and action items — grounded in TFP principles, not pool store upselling.

Available for **ChatGPT Custom GPTs**, **Claude.ai Projects**, and **Claude Code**.

## Get Pool Math First

This tool is designed to work alongside **[Pool Math](https://www.troublefreepool.com/blog/pool-math/)** — the official app from Trouble Free Pool for tracking your water chemistry. Available on [iOS](https://apps.apple.com/us/app/pool-math-by-troublefreepool/id1228819745) and [Android](https://play.google.com/store/apps/details?id=com.troublefreepool.poolmath).

If you're not already using Pool Math, start there. It's the best way to log your test results, track trends, and get accurate dosing calculations. **Please purchase the app and support the TFP community** — the people behind Trouble Free Pool have done incredible work making pool care accessible and science-based, and Pool Math is how they keep the lights on.

This tool extends Pool Math by feeding your data through an AI that understands TFP methodology — it doesn't replace it.

## What It Does

- Analyzes FC, CC, pH, TA, CH, and CYA against TFP target ranges
- Evaluates FC relative to CYA (the most critical metric in TFP methodology)
- Calculates FC burn rate from test history and predicts next reading
- Factors in weather (temperature, UV index, rain) to adjust recommendations
- Provides specific dosing amounts in oz scaled to your pool volume
- Flags when SLAM is needed and walks through the procedure
- Reminds you which tests are due based on the TFP testing schedule
- Only recommends TFP-approved chemicals: liquid chlorine, muriatic acid, baking soda, CYA, calcium chloride, borax
- **Fetches live data from Pool Math** — just say "how's my pool?" and it pulls your latest readings automatically

## Pool Math Integration

All three versions support pulling live data from Pool Math via its share API. To enable this:

1. Open the **Pool Math** app on your phone
2. Go to **Settings** → **Share** → enable sharing
3. Copy your share link — it looks like `https://troublefreepool.com/mypool/XXXXXXX`
4. Provide this URL to the AI (see platform-specific instructions below)

Once configured, you can say "how's my pool?" or "check my pool" and the AI will fetch your latest test results, recent chemical additions, and maintenance history directly from Pool Math. No need to manually type in your readings.

---

## Setup — Choose Your Platform

### Option 1: ChatGPT Custom GPT

**[Try it now](https://chatgpt.com/g/g-69bb372dbafc81919c305b6601e5307c-pool-chemistry-advisor-tfp)** — no setup required, just open the link and start chatting (requires ChatGPT Plus).

Want to create your own customized version? Follow the setup below.

**Setup:**

1. Go to [chatgpt.com/gpts/editor](https://chatgpt.com/gpts/editor) (requires ChatGPT Plus)
2. Click **Create a GPT**
3. Use the values from `chatgpt/gpt-description.md` for the Name, Description, and Conversation Starters
4. Open `chatgpt/gpt-instructions.md` from this repo
5. Copy the entire contents and paste into the **Instructions** field
6. Save — choose "Only me", "Anyone with a link", or "Public" (GPT Store)

Unlike the Claude versions, the ChatGPT GPT asks users for their pool profile in the first message, so no pre-editing is needed. Anyone with access to the GPT can use it with their own pool details. Users just provide their Pool Math share URL in conversation and the GPT fetches their data.

**Optional: Add the Pool Math Action** for automatic data fetching:
1. In the GPT editor, scroll to **Actions** → **Create new action**
2. Paste the contents of `chatgpt/pool-math-action.yaml` into the Schema field
3. Set Authentication to **None** (the Pool Math share API is public)

**Personal use tip:** At the very top of the instructions you'll see `POOL_MATH_USER_ID = none`. Give the GPT your Pool Math share URL once to discover your tfp- user ID, then hardcode it (e.g., `POOL_MATH_USER_ID = tfp-000000`) so "how's my pool?" works every conversation. For shared/public GPTs, leave it as `none` — users provide their share URL in conversation.

**Files:**
```
chatgpt/
├── gpt-description.md     # Name, description, conversation starters for the GPT editor
├── gpt-instructions.md    # Full instructions — paste into the GPT Instructions field
└── pool-math-action.yaml  # OpenAPI schema for Pool Math API Action (optional)
```

---

### Option 2: Claude.ai Project (Web & Mobile)

Best for: using from claude.ai on desktop or the Claude iOS/Android app. Works in any conversation within the project.

**Setup:**

1. Go to [claude.ai](https://claude.ai) and click **Projects** in the sidebar
2. Click **Create Project** and name it something like "Pool Chemistry"
3. For the project description, enter: `Analyze pool water chemistry using Trouble Free Pool (TFP) methodology. Provide specific dosing recommendations, FC burn rate predictions, and action items based on Pool Math data and weather conditions. Fetch live pool data when a share key is available.`
4. In the project settings, find **Custom Instructions** (or **Project Instructions**)
5. Open `claude-project/project-instructions.md` from this repo
6. **Edit the Pool Profile section** at the top — replace the `YOUR_*` placeholders with your pool's details, including your Pool Math share URL
7. Copy the entire contents and paste into the Custom Instructions field
8. Save

Every conversation you start within that project will have the full TFP methodology loaded. You can access it from the Claude iOS app too — just open a conversation in the project. Say "how's my pool?" and it will fetch your live Pool Math data.

**Files:**
```
claude-project/
└── project-instructions.md     # Single self-contained doc — paste into Project Instructions
```

---

### Option 3: Claude Code (CLI)

Best for: developers, automation, N8N workflows, daily digest notifications.

The Claude Code version is a full skill with separate reference documentation. It activates automatically when you mention pool chemistry topics.

**Install:**

```bash
git clone https://github.com/kevinrossen/pool-chemistry-advisor.git ~/.claude/skills/pool-chemistry-advisor
```

After cloning, only the `claude-code/` directory needs to be in your skills folder:

```bash
# Or if you prefer to keep it clean:
git clone https://github.com/kevinrossen/pool-chemistry-advisor.git /tmp/pool-chem
cp -r /tmp/pool-chem/claude-code/ ~/.claude/skills/pool-chemistry-advisor/
```

Then edit the Pool Profile section in both files:
- `~/.claude/skills/pool-chemistry-advisor/SKILL.md`
- `~/.claude/skills/pool-chemistry-advisor/references/pool-chemistry-reference.md`

Replace the `YOUR_*` placeholders with your pool's details (volume, surface, location, etc.), including your Pool Math share URL or user ID.

**Files:**
```
claude-code/
├── SKILL.md                              # Skill definition — workflow, rules, output format
└── references/
    └── pool-chemistry-reference.md       # Full TFP methodology reference
```

---

## Repo Structure

```
pool-chemistry-advisor/
├── README.md
├── LICENSE
├── chatgpt/                              # Option 1: ChatGPT Custom GPT
│   ├── gpt-description.md
│   ├── gpt-instructions.md
│   └── pool-math-action.yaml             # OpenAPI schema for live Pool Math data
├── claude-project/                       # Option 2: Claude.ai Project (web & mobile)
│   └── project-instructions.md
└── claude-code/                          # Option 3: Claude Code skill (CLI)
    ├── SKILL.md
    └── references/
        └── pool-chemistry-reference.md
```

## Attribution

The pool chemistry knowledge in this project is based on the **[Trouble Free Pool (TFP)](https://www.troublefreepool.com/)** methodology. TFP is an independent, community-driven resource for science-based pool and hot tub care. The FC/CYA relationship table, SLAM process, and BBB approach are all concepts developed and popularized by the TFP community.

**This project is not affiliated with, endorsed by, or sponsored by Trouble Free Pool.** It is an independent tool built by a TFP community member to make TFP methodology more accessible through AI.

If this is useful to you, the best way to give back is to:

1. **[Buy Pool Math](https://www.troublefreepool.com/blog/pool-math/)** — support TFP directly
2. **[Join the TFP forum](https://www.troublefreepool.com/forums/)** — learn from the community, ask questions, help others
3. **[Get a TF-100 test kit](https://tftestkits.net/)** — accurate testing is the foundation of everything

## TFP Methodology

Key principles from the [Trouble Free Pool](https://www.troublefreepool.com/) approach:

- **FC/CYA relationship is everything.** The correct chlorine level depends entirely on your stabilizer level.
- **BBB (Bleach, Baking soda, Borax).** Simple chemicals. No algaecides, no phosphate removers, no pool store products.
- **SLAM when needed.** Shock, Level, And Maintain — a sustained process, not a one-time shock.
- **Test, don't guess.** A good test kit (TF-100 or Taylor K-2006) is essential.

To learn more, visit [TFP Pool School](https://www.troublefreepool.com/blog/pool-school/).

## Contributing

Feedback welcome! If you have suggestions — additional chemistry edge cases, better dosing formulas, regional weather considerations, SWG-specific tuning — please open an issue or PR.

## Disclaimer

This project provides recommendations based on the TFP methodology and should be used as a guide alongside your own test results and judgment. Always verify recommendations with your test kit before adding chemicals. The authors are not responsible for any damage to pool equipment or surfaces.

This project is not affiliated with, endorsed by, or sponsored by Trouble Free Pool, Pool Math, or TFTestKits.

## License

MIT
