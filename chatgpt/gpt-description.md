# Custom GPT — Setup Fields

Use these values when creating your Custom GPT at https://chatgpt.com/gpts/editor

## Name

Pool Chemistry Advisor (TFP)

## Description

Pool water chemistry analysis and recommendations using Trouble Free Pool (TFP) methodology. Give it your test results and weather — get specific dosing amounts and action items. Connects to Pool Math for live data. Supports manual chlorine and SWG pools.

## Conversation Starters

- My Pool Math share URL is troublefreepool.com/mypool/XXXXXX. How's my pool?
- My pool test: FC 4, pH 7.6, CC 0, CYA 40, TA 70, CH 300. Water is 88°F. What should I do?
- FC dropped from 7 to 3 in two days. CYA is 50. Is that normal or should I worry?
- I think I need to SLAM. CC is 1.0 and water is slightly cloudy. CYA 40. Walk me through it.

## Instructions

Copy the entire contents of `gpt-instructions.md` into the Instructions field.

## Action (Optional — enables live Pool Math data)

1. In the GPT editor, scroll down to **Actions** and click **Create new action**
2. Copy the contents of `pool-math-action.yaml` and paste into the **Schema** field
3. Set Authentication to **None** (the Pool Math share API is public)
4. Save

This allows the GPT to fetch live pool data when a user provides their Pool Math share key.

## Personal Use: Add Your User ID

At the very top of the instructions, you'll see:

```
POOL_MATH_USER_ID = none
```

To find your user ID: give the GPT your share URL from Pool Math (troublefreepool.com/mypool/XXXXXX) once. It will resolve it to a tfp- user ID. Then hardcode that ID:

```
POOL_MATH_USER_ID = tfp-000000
```

Now "how's my pool?" works immediately every conversation without providing your URL.

For shared/public GPTs, leave it as `none` — users provide their share URL in conversation.
