---
name: hotel-booking-accessibility
description: >
  Match hotels to travelers with specific accessibility needs using verified evidence from hotel data
  and real guest reviews. Use when the user needs wheelchair-accessible rooms, step-free access,
  roll-in showers, low beds, or other accessibility features — and wants confidence levels, not just
  OTA labels. Trigger phrases — "accessible hotel", "wheelchair friendly hotel", "barrier-free room",
  "hotel for disabled", "无障碍酒店", "轮椅入住", "无门槛淋浴".
homepage: https://mcp.agentichotel.cn
metadata:
  {
    "openclaw": {
      "emoji": "♿",
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

# Accessibility Needs Mapper

> Find hotels that genuinely meet your accessibility requirements — not just those that claim to.

## When to Use

✅ **Use this skill when:**
- A traveler uses a wheelchair or mobility aid and needs to verify ramp access, roll-in showers, or elevator reach
- An older traveler or family with a stroller needs step-free routes and low-threshold bathrooms
- The user has received an "accessible" label from an OTA but cannot tell what it actually covers
- The user wants to see a per-requirement confidence breakdown (confirmed / reported / unknown / problematic) before booking

❌ **Don't use this skill when:**
- The user just wants a general hotel search with no specific accessibility requirements
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

### Step 1: Gather Required Information

Collect before searching. Ask if not already provided:

| Input | Required | Notes |
|-------|----------|-------|
| Destination | ✅ | City or landmark (e.g. "Hangzhou West Lake area") |
| Check-in / check-out dates | ✅ | |
| Number of adults | ✅ | |
| Specific accessibility needs | ✅ | Ask the user to list concrete requirements, not just "accessible" |

Example prompt when accessibility needs are vague:
> "To find the best match, could you tell me which of these matter to you?
> ① Wheelchair ramp / step-free entrance  ② Roll-in shower (no threshold)
> ③ Bed height for wheelchair transfer  ④ Elevator reaching all floors
> ⑤ Wide doorways (≥80 cm)  ⑥ Grab bars in bathroom
> Feel free to add anything else."

### Step 2: Fetch Accessibility-Relevant Tags

Run `hotel-tags` and filter for tags related to accessibility, facilities, and room features.

```bash
npx --yes --package rollinggo@latest rollinggo hotel-tags
```

Look for tags matching: `accessible`, `wheelchair`, `barrier-free`, `elevator`, `family-friendly`,
`ground-floor rooms`, or equivalent Chinese tags. Note the exact tag strings for use in Step 3.

### Step 3: Search Hotels

Use accessibility tags as `--required-tag` or `--preferred-tag` based on the user's must-have list.
`--star-ratings` is not set by default — accessibility quality does not correlate with star rating.

```bash
npx --yes --package rollinggo@latest rollinggo search-hotels \
  --origin-query "wheelchair accessible hotel near West Lake Hangzhou" \
  --place "Hangzhou" \
  --place-type "city" \
  --country-code CN \
  --check-in-date 2026-05-10 \
  --stay-nights 3 \
  --adult-count 2 \
  --size 20 \
  --required-tag "wheelchair-accessible" \
  --preferred-tag "elevator" \
  --format table
```

Extract `hotelId` from results for Step 4.

### Step 4: Build the Accessibility Evidence Matrix

For each candidate hotel, run `hotel-detail` to retrieve room data, then cross-reference with
available tag and review signals to score each of the user's stated requirements.

```bash
npx --yes --package rollinggo@latest rollinggo hotel-detail \
  --hotel-id 1234567 \
  --check-in-date 2026-05-10 \
  --check-out-date 2026-05-13 \
  --adult-count 2 \
  --room-count 1
```

For each user requirement, assign one of four confidence levels:

| Level | Meaning |
|-------|---------|
| ✅ Confirmed | Explicitly stated in official hotel data or room description |
| 💬 Reported | Mentioned in guest reviews by users with similar needs |
| ❓ Unknown | No evidence found in available data |
| ⚠️ Problematic | Reviews indicate the feature exists but has issues in practice |

Build a matrix with hotels as rows and requirements as columns.

### Step 5: Present Results

Present the matrix before listing booking links. Example:

```
Hangzhou Accessibility Match Results
Dates: May 10–13 · 2 adults

Hotel               Ramp  Roll-in shower  Bed height  Elevator  Price/night
─────────────────────────────────────────────────────────────────────────────
Sofitel West Lake   ✅     ✅              💬          ✅        ¥1,280
Hyatt Regency HZ    ✅     ❓              ✅          ✅        ¥980
DoubleTree HZ       💬     ⚠️             ❓          ✅        ¥760

✅ Confirmed  💬 Reported by guests  ❓ Unknown  ⚠️ Reported issues
```

Follow with a brief narrative for the top recommendation and booking URLs from `hotel-detail` output.

## Key Rules

- **Never** surface a hotel as a match based solely on an OTA "accessible" label — at least one
  confirmed or reported signal per stated requirement is required before recommending.
- **Must** display the confidence matrix before booking links; users need evidence, not just rankings.
- **Must** distinguish between roll-in shower (no threshold) and grab-bar-only bathrooms — these are
  not interchangeable and conflating them is a safety issue.
- **Never** relax the accessibility tag filter silently — if no results meet the requirements,
  declare this explicitly and ask the user which requirements can be deprioritized.
- **Prefer** hotels where guest reviews from travelers with mobility aids confirm the features,
  over hotels with official labels but no review corroboration.

## When No Results

If no hotels match the required accessibility tags, follow this loosening order — but only with
explicit user confirmation at each step:

1. Relax `--required-tag` to `--preferred-tag` for secondary requirements (e.g. bed height), while
   keeping entrance ramp and shower type as hard filters
2. Increase `--size` to 30 and expand `--distance-in-meter` to 2000
3. Ask the user which specific requirement they are willing to mark as "nice to have" vs. "must have"
   before removing any further filters

**Never relax without asking:**
- Wheelchair ramp / step-free entrance — removing this makes results useless for wheelchair users
- Roll-in shower — if the user listed this, a grab-bar bathtub is not an acceptable substitute

## References
- Full RollingGo command reference: `references/rollinggo-searchhotel-skill/SKILL.md`
