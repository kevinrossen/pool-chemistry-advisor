---
name: pool-chemistry-advisor
description: Analyze pool water chemistry using TFP (Trouble Free Pool) methodology. Use when the user mentions pool chemistry, pool maintenance, water testing, chlorine levels, FC, CYA, pH, alkalinity, calcium hardness, SLAM, Pool Math, pool automation, n8n pool workflow, or any pool water analysis task. Also use when working on n8n workflows related to pool data, weather-based pool recommendations, or LLM agent prompts for pool chemistry.
---

# Pool Chemistry Advisor Skill

Provide pool chemistry analysis and recommendations grounded in the Trouble Free Pool (TFP) methodology.

## Pool Profile

Customize these values for your pool. Edit them before installing the skill.

- Location: YOUR_CITY, YOUR_STATE
- Pool volume: ~YOUR_VOLUME gallons
- Surface: YOUR_SURFACE (plaster, pebble, vinyl, fiberglass)
- Chlorination: YOUR_METHOD (manual liquid chlorine, SWG, etc.)
- Test kit: YOUR_KIT (TF-100, Taylor K-2006, strips, etc.)
- Tracking: Pool Math app by TroubleFreePool
- SWG: yes / no (if yes, CYA target is 60–80 instead of 30–60)
- Pool Math Share URL: https://troublefreepool.com/mypool/YOUR_SHARE_CODE
- Pool Math User ID: YOUR_USER_ID (tfp-XXXXXX, see below for how to find it)

## Pool Math Live Data

When the user asks you to check their pool, analyze their chemistry, or says something like "how's my pool," fetch live data from Pool Math.

**If you have a Pool Math User ID (tfp-XXXXXX):**
Fetch directly: `https://api.poolmathapp.com/share/{userId}.json`
This returns overview data + 25 recent logs.

**If you only have a share URL (troublefreepool.com/mypool/XXXXXX):**
Two-step resolution:
1. Extract the code from the URL (e.g., `5sRX8Cp`)
2. Fetch `https://api.poolmathapp.com/share/{code}.json` — read `pools[0].pool.userId` from the response
3. Fetch `https://api.poolmathapp.com/share/{userId}.json` — this returns the full data with recent logs

To find your share URL: open Pool Math app → Settings → Share → enable sharing → copy the share link.

The API returns JSON with:
- `pools[0].pool.overview` — current readings (fc, cc, ph, ta, cya, ch) with timestamps
- `pools[0].pool.volume` — pool volume in gallons
- `pools[0].pool.userId` — the tfp- user ID (needed for full data with logs)
- `pools[0].recentLogs` — recent test logs, chemical additions, and maintenance entries (only returned with tfp- userId)

Use this data as the primary input for analysis. Combine with weather data when available.

## Core Workflow

When analyzing pool chemistry data:

1. **Check FC vs CYA first** — this is the single most critical metric. Refer to the FC/CYA table in `references/pool-chemistry-reference.md` for target, minimum, and SLAM levels.
2. **Evaluate CC** — must be ≤ 0.5 ppm. If higher, recommend SLAM.
3. **Check pH** — target 7.2–7.8, ideal 7.4–7.6. Lower with muriatic acid only (never dry acid on plaster).
4. **Check TA** — target 60–80 ppm. Only raise with baking soda if below 50.
5. **Check CH** — target depends on surface: 250–400 ppm for plaster/pebble, 150–250 for vinyl/fiberglass.
6. **Calculate/check CSI** — target -0.3 to +0.3. Especially important for plaster/pebble.
7. **Factor in weather** — temperature, UV index, rain, and wind all affect FC demand significantly.
8. **Include testing reminders** — Based on the testing schedule in `references/pool-chemistry-reference.md`, remind the owner which tests are due or overdue.

## Key Rules

- FC target depends entirely on CYA level — never evaluate FC in isolation.
- **Always aim for the high end of the FC target range for the current CYA level.** The minimum FC in the range is not a target — it's a warning line. If FC reaches the minimum, you're within the city limits of Swampville. We don't want to visit Swampville. Dose recommendations should always target the upper portion of the FC range to maintain a comfortable buffer against daily burn-off.
- Only recommend: liquid chlorine, muriatic acid, baking soda, CYA (stabilizer), calcium chloride, borax.
- Never recommend: stabilized chlorine (tabs/pucks), algaecide, phosphate remover, dry acid, clarifiers, or pool store specialty products.
- Dose liquid chlorine in the evening after sunset.
- Before SLAM, always lower pH to 7.2 first.
- Be specific with dosing amounts — use Pool Math or the dosing formulas in `references/pool-chemistry-reference.md` scaled to the owner's pool volume.
- When building n8n workflows or LLM agent prompts for pool analysis, reference `references/pool-chemistry-reference.md` for the complete context document.

## Output Format (Daily Digest)

When called from a daily pool digest workflow (or any automated analysis), structure the response as:

1. **One short paragraph** — FC burn rate estimate with reasoning. Include the math (ppm lost over what period), predicted FC at next test, and whether that's within target for the current CYA level.
2. **Action items** — A bulleted list of up to 5 short, specific action items for today. Each bullet should include exact chemical amounts in oz where applicable. If nothing needs doing, just say "No action needed."

Keep the total response concise — this goes to a mobile push notification. No headers, no tables, no markdown formatting. Plain text with bullet points (use - for bullets).

## Reference Material

For detailed parameter ranges, FC/CYA tables, SLAM procedure, weather factors, chemical dosing formulas, and LLM agent prompt context, read:

→ `references/pool-chemistry-reference.md`

This reference file contains the full context document suitable for embedding in n8n LLM Agent system prompts.
