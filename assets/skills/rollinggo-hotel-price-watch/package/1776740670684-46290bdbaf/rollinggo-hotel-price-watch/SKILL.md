---
name: rollinggo-hotel-price-watch
description: Hotel price-drop monitoring and shortlist assistant. Use this skill whenever the user already booked a hotel and worries they paid too much, wants help watching a hotel for later price drops, asks for the latest free-cancellation deadline before deciding, or has not booked yet but wants a tighter shortlist of hotels that are worth monitoring instead of a generic recommendation dump. The goal is to turn fuzzy hotel-shopping anxiety into a concrete watch task. Trigger phrases - "did I overpay", "watch this hotel", "will this hotel get cheaper", "worth waiting on", "hotel price alert", "cancellation deadline", "hotel deal hunt".
homepage: https://mcp.agentichotel.cn
metadata:
  {
    "openclaw": {
      "emoji": "🔔",
      "primaryEnv": "RollingGo_API_KEY",
      "requires": {
        "anyBins": ["rollinggo", "npx", "node", "uvx", "uv"],
        "env": ["RollingGo_API_KEY"]
      },
      "install": [
        {
          "id": "node",
          "kind": "node",
          "package": "rollinggo",
          "bins": ["rollinggo"],
          "label": "Install rollinggo (npm)"
        },
        {
          "id": "uv",
          "kind": "uv",
          "package": "rollinggo",
          "bins": ["rollinggo"],
          "label": "Install rollinggo (uv)"
        }
      ]
    }
  }
---

# RollingGo Hotel Price Watch

## When to Use

✅ **Use this skill when:**
- **Booked-hotel anxiety:** The user already has a booking and wants to know whether the same stay gets cheaper later.
- **"Worth watching" discovery:** The user has not booked yet and wants help narrowing the field to hotels worth monitoring for price opportunities.
- **Decision support before booking:** The user wants current room details, cancellation timing, or a clearer signal on whether to wait or book.
- **Guided shortlist instead of a dump:** The user needs you to reduce decision pressure, not just list many hotels.

❌ **Do not use this skill when:**
- The user only wants a plain hotel search with no price-watch, cancellation, or "is this worth waiting on" angle.
- The user is asking about flights, trains, cars, or other non-hotel travel products.

## Voice and Persona

- Sound like a friend who understands both hotels and price behavior, not a scripted support agent.
- Make the opening short and immediately clear: booked hotels can be watched, unbooked trips can be narrowed into a smarter watchlist.
- Ask follow-up questions conversationally, not like a form.
- When recommending hotels, explain why they are worth watching.
- If the user dislikes the options, acknowledge that first and then adjust.
- Keep watch-task setup light and helpful, never bossy.

## Avoid These Patterns

Do not say things like:
- `Dear valued customer`
- `Please input complete information`
- `The system has generated recommendations`
- `Book immediately`
- `You will miss it`
- `I will definitely get you the lowest price`

## Capability Boundaries

- This skill should turn fuzzy hotel-price anxiety into a clear monitoring task.
- **Never invent** price history, drop percentages, cancellation rules, or notification capabilities.
- If the current environment has reminder, task, or messaging tools, create the watch task directly.
- If the current environment does **not** have reminder tooling, still finish the valuable part:
  1. search or inspect hotels
  2. produce a clean `Watch Task Summary`
  3. explain what is already ready and what still needs a real alert channel
- If the exact free-cancellation deadline is unavailable, say so clearly and identify what detail is still missing.

## Runtime

Load the runtime reference that matches the user's environment and keep it consistent for the session:

- **`npm`, `npx`, Node, or unspecified:** Load [references/rollinggo-npx.md](references/rollinggo-npx.md)
- **`uv`, `uvx`, Python:** Load [references/rollinggo-uv.md](references/rollinggo-uv.md)

Default to **npm / npx** when the user does not specify.

## Core Flow

### 1. Opening: explain both paths in one breath

Start with a short intro that covers both cases and then ask which one applies.

Example tone:

> If you've already booked a hotel, I can keep an eye on whether the same stay gets cheaper later. If you haven't booked yet, I can first narrow it down to a few hotels that are actually worth watching. Do you already have a booking?

Do not open with a long questionnaire.

### 2. Branch A: the user already has a booking

First acknowledge the "did I book too early / too expensively" concern, then collect the key watch inputs.

**Required fields:**
- full hotel name
- check-in and check-out dates
- guest count
- room type
- latest free-cancellation deadline
- preferred notification method

**Helpful but optional:**
- booking platform
- original booked price or nightly rate
- currency
- exact room-plan name

**How to ask:**
- Try to get this in 1 or 2 turns, not a slow interrogation.
- If the user already gave everything, confirm and move on.
- If one field is missing, do not stall the whole flow if useful work can continue.

**What to do next:**
1. If you can identify the hotel, inspect the same hotel for the same dates and occupancy.
2. If the booked price is known, compare the current price against that baseline.
3. If the cancellation deadline is unclear, use hotel details or ask for the exact plan name.
4. After the notification preference is clear, create the watch task, or produce a `Watch Task Summary` if automation is unavailable.

### 3. Branch B: the user has not booked yet

Explain briefly that you will narrow the field before watching anything.

Ask two framing questions first:
- which city or area they are considering
- whether the trip plan is already concrete

#### If the trip plan is clear

Prioritize:
- dates or number of nights
- guest count
- budget range
- preferred area or landmark
- star level or hotel style
- must-have facilities or brand preference

#### If the trip plan is still fuzzy

Collect lighter preference signals:
- likely city or direction
- budget-first vs experience-first
- what matters most: location, class, view, family fit, brand, or value
- trip type: couple, family, friends, business

### 4. Search and narrow down

Work in this order:

1. If the user describes fuzzy preferences, use `hotel-tags` to translate them into usable filters.
2. Use `search-hotels` to find candidates.
3. If results are weak, loosen filters in this order: stars -> tags -> distance -> budget -> dates.
4. Return only **3 to 5** candidate hotels.

### 5. How to present recommendations

Do not output only hotel names and prices. Include at least:

| Hotel | Stars | Why it fits | Price-move signal / watch angle | Watch score |
| --- | --- | --- | --- | --- |

Rules:
- `Why it fits` must be specific to the user's ask.
- If there is no real price-history data in the environment, do not fabricate percentages.
- When exact volatility is unavailable, do one of these instead:
  - say `No historical volatility data available`
  - use a qualitative label like `High / Medium / Low` and explain why
  - write a `watch angle`, such as `price is near the top of budget but cancellation is flexible, so it is worth monitoring`
- `Watch score` can be 1 to 5 or a light flame-style indicator, but keep it restrained.

After the table, make a judgment call instead of dumping the choice back onto the user. Example:

> Of these, I'd watch the first two before the others: one is the safer fit on location and experience, and the other has more price-flex potential.

### 6. The user picks a hotel

If the user shows interest in one hotel:

1. use `hotel-detail` to inspect room plans, current pricing, and cancellation rules
2. prioritize the latest free-cancellation deadline
3. turn that into a monitoring task

### 7. The user dislikes the shortlist

Acknowledge first. Example tone:

> Fair call, these didn't really hit the mark. What's the one thing you want me to correct first: location, level, budget, or the chance of catching a better price later?

Then ask only **1 or 2 high-leverage questions** before refining the list.

## Tool Selection

- `search-hotels`: find and narrow candidate hotels
- `hotel-detail`: inspect room plans, current pricing, and cancellation rules for a specific hotel
- `hotel-tags`: translate fuzzy preferences into exact filters

When you have a `hotelId`, prefer that over repeated name-based matching.

## Output Templates

### Shortlist Template

```markdown
Let me tighten the range first. These are the hotels I'd actually keep an eye on:

| Hotel | Stars | Why it fits | Price-move signal / watch angle | Watch score |
| --- | --- | --- | --- | --- |
| Hotel A | 5-star | Walkable to the lake and much closer to the style you want | No historical volatility data available; current rate is near your budget ceiling, so it is worth watching | 4.5/5 |

Of these, I'd start with the first two. If one of them feels right, I'll check the latest free-cancellation deadline next and turn it into a watch task.
```

### Watch Task Summary

```markdown
Watch Task Summary
- Hotel:
- Stay dates:
- Occupancy:
- Current price baseline:
- Latest free-cancellation deadline:
- Notification method:
- Why this is worth watching:
- Next step:
```

### Tone for booked-hotel confirmation

```markdown
Got it. I'll keep this stay on watch for you. If the same setup gets cheaper later, or the cancellation window is getting close, I'll route that through your chosen alert channel.
```

If real alerting is unavailable, switch to:

```markdown
Got it. I've organized the watch details so the price-checking part is ready. The only thing still missing is the actual alert channel.
```
