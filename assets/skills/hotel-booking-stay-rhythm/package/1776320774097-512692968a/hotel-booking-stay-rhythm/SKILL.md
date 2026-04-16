---
name: hotel-booking-stay-rhythm
description: >
  Optimize multi-city business travel by analyzing your schedule and recommending the least fatiguing
  hotel booking strategy — when to arrive early, where to stay multiple nights, and how to minimize
  transit exhaustion. Use when the user has multiple meetings across cities and wants to optimize
  check-in timing, location, and stay duration based on fatigue cost. Trigger phrases — "optimize my
  business trip", "multi-city travel plan", "reduce travel fatigue", "when should I check in",
  "住宿节奏优化", "多城市出差安排", "减少疲劳".
homepage: https://mcp.agentichotel.cn
metadata:
  {
    "openclaw": {
      "emoji": "🗓️",
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

# Stay Rhythm Optimizer

> Plan your multi-city stays to minimize fatigue, not just cost.

## When to Use

✅ **Use this skill when:**
- A business traveler has 3+ meetings across different cities within a 2-week window and needs to decide check-in timing
- The user wants to know whether to arrive the night before or same-day for each meeting
- The user is planning a multi-city tour and wants to optimize where to stay multiple nights vs. single nights
- The user mentions fatigue, jet lag, or exhaustion as a concern in their travel planning

❌ **Don't use this skill when:**
- The user has a single-city trip with no schedule complexity
- The request is purely about finding the cheapest hotel (use generic search instead)
- The request is about non-hotel travel (flights, trains, car rentals)

## API Key

Resolution order: `--api-key` flag → `RollingGo_API_KEY` env var.

No key yet? Apply at: https://mcp.agentichotel.cn/apply

## Runtime

Default: **npm/npx**. Load the matching reference and keep it for the session.

- **npm/npx (default):** Load [references/rollinggo-searchhotel-skill/references/rollinggo-npx.md](references/rollinggo-searchhotel-skill/references/rollinggo-npx.md)
- **uv/uvx:** Load [references/rollinggo-searchhotel-skill/references/rollinggo-uv.md](references/rollinggo-searchhotel-skill/references/rollinggo-uv.md)

## Version Freshness

Always use the latest release:

- **npx:** `npx --yes --package rollinggo@latest rollinggo ...`
- **uvx:** `uvx --refresh --from rollinggo rollinggo ...`

## Workflow

### Step 1: Gather Schedule and Constraints

Collect the following before building the fatigue model:

| Input | Required | Notes |
|-------|----------|-------|
| Multi-segment itinerary | ✅ | Meeting times + locations (city or venue) |
| Transportation mode | Optional | Flight / high-speed rail / driving (default: flight) |
| Earliest acceptable departure time | Optional | Default: 06:00 |
| Latest acceptable arrival time | Optional | Default: 23:00 |
| Fatigue sensitivity | Optional | High-intensity / Balanced / Low-intensity (default: Balanced) |

Example prompt when the user provides only meeting dates:
> "To optimize your stay rhythm, I need a bit more detail:
> ① What time does each meeting start and end?
> ② Are you flying or taking the train between cities?
> ③ Do you have a hard limit on how early you're willing to depart or how late you're willing to arrive?
> ④ How sensitive are you to fatigue — can you handle back-to-back travel, or do you prefer buffer days?"

### Step 2: Build the Fatigue Cost Model

For each segment of the itinerary, calculate a fatigue score based on:

| Factor | Penalty |
|--------|---------|
| Late-night arrival (after 22:00) | +3 points |
| Early-morning departure (before 07:00) | +2 points |
| Same-day arrival + meeting (< 3 hours buffer) | +4 points |
| Consecutive travel days (no rest day) | +1 point per day after day 3 |
| Cross-timezone travel (≥ 2 hours) | +2 points |

Generate a fatigue timeline showing cumulative fatigue across the trip. Identify high-risk segments
(fatigue score ≥ 6) and recommend mitigation strategies:
- Arrive the night before instead of same-day
- Add a rest day between segments
- Choose a hotel closer to the venue to reduce transit time

### Step 3: Generate Stay Rhythm Recommendations

Based on the fatigue model, output a structured stay plan:

```
Stay Rhythm Plan — 15-day Beijing / Guangzhou / Chengdu Trip

Segment 1: Beijing (Apr 12–14)
├─ Meeting: Apr 13, 09:00–17:00 at Beijing International Convention Center
├─ Recommendation: Arrive Apr 12 evening (avoid same-day + early meeting)
├─ Check-in: Apr 12 · Check-out: Apr 14
└─ Fatigue score: 2 (low risk)

Segment 2: Guangzhou (Apr 15–17)
├─ Meeting: Apr 16, 14:00–18:00 at Guangzhou Baiyun Hotel
├─ Recommendation: Arrive Apr 15 morning (meeting is afternoon, 3+ hour buffer OK)
├─ Check-in: Apr 15 · Check-out: Apr 17
└─ Fatigue score: 3 (moderate)

Segment 3: Chengdu (Apr 18–20)
├─ Meeting: Apr 19, 09:00–12:00 at Chengdu High-Tech Zone
├─ Recommendation: Arrive Apr 18 evening + add rest day Apr 20 (cumulative fatigue = 5)
├─ Check-in: Apr 18 · Check-out: Apr 21
└─ Fatigue score: 5 → 2 after rest day (mitigated)

Total nights: 9 · Peak fatigue: 5 (mitigated) · Estimated cost: ¥7,200
```

### Step 4: Search Hotels for Each Segment

For each recommended stay segment, run `search-hotels` with:
- `--place` set to the meeting city
- `--check-in-date` and `--stay-nights` derived from the stay plan
- `--distance-in-meter` set to prioritize proximity to the meeting venue (default: 3000m)
- `--preferred-tag` set to `business-hotel` or `conference-facilities` if available

Example for Segment 1 (Beijing):

```bash
npx --yes --package rollinggo@latest rollinggo search-hotels \
  --origin-query "business hotel near Beijing International Convention Center" \
  --place "Beijing International Convention Center" \
  --place-type "landmark" \
  --country-code CN \
  --check-in-date 2026-04-12 \
  --stay-nights 2 \
  --adult-count 1 \
  --size 15 \
  --distance-in-meter 3000 \
  --preferred-tag "business-hotel" \
  --format table
```

Extract `hotelId` from results and run `hotel-detail` for pricing and room availability.

### Step 5: Present Optimized Plan + Hotel Options

Present the stay rhythm plan first, then list hotel options for each segment with:
- Distance to venue
- Price per night
- Booking URL

Example output:
```
Segment 1: Beijing (Apr 12–14) — 2 nights near Convention Center

Hotel                        Distance  Price/night  Total
────────────────────────────────────────────────────────────
Novotel Beijing Peace        800m      ¥680         ¥1,360
Holiday Inn Express BICC     1.2km     ¥520         ¥1,040
Crowne Plaza Beijing         1.5km     ¥880         ¥1,760

💡 Recommendation: Holiday Inn Express offers the best value/proximity balance.
   Arriving Apr 12 evening gives you a full night's rest before the 09:00 meeting.
```

## Key Rules

- **Must** calculate and display fatigue scores for each segment — the user needs to see the reasoning
  behind "arrive the night before" recommendations, not just accept them blindly.
- **Never** recommend same-day arrival for meetings starting before 10:00 unless the user explicitly
  requests high-intensity scheduling.
- **Must** prioritize hotel proximity to the meeting venue over star rating or price — a 15-minute
  walk vs. 45-minute commute has measurable fatigue impact.
- **Must** flag cumulative fatigue when consecutive travel days exceed 3 — recommend a rest day or
  extended stay even if it increases total nights.
- **Never** optimize purely for cost — if the cheapest option requires a 06:00 departure after a
  22:00 arrival the night before, reject it and surface the next-best option.

## When No Results

If no hotels are found within the recommended distance and dates, follow this loosening order:

1. Increase `--distance-in-meter` to 5000 (but warn the user about increased commute time)
2. Increase `--size` to 30
3. Remove `--preferred-tag` filters
4. Ask the user if they are willing to adjust check-in/check-out dates by ±1 day

**Never relax without asking:**
- The "arrive night before" recommendation for early meetings — if no hotels are available, suggest
  alternative meeting cities or dates, not same-day arrival.
- The rest day recommendation when cumulative fatigue ≥ 6 — removing it defeats the skill's purpose.

## References
- Full RollingGo command reference: `references/rollinggo-searchhotel-skill/SKILL.md`
