# RollingGo NPX Reference

> Use this file for npm / npx / Node environments.
> Interaction style and branching logic live in `../SKILL.md`.

## Latest-first prefix

```bash
npx --yes --package rollinggo@latest rollinggo
```

## Three commands this skill uses most

### 1. Find candidate hotels: `search-hotels`

Required:
- `--origin-query`
- `--place`
- `--place-type`

```bash
npx --yes --package rollinggo@latest rollinggo search-hotels \
  --origin-query "high-end hotels near West Lake worth watching for price drops" \
  --place "West Lake Hangzhou" \
  --place-type "<check --help for valid values>" \
  --check-in-date 2026-05-01 \
  --stay-nights 2 \
  --adult-count 2 \
  --size 5
```

### 2. Inspect a specific hotel: `hotel-detail`

Prefer `--hotel-id` and include dates plus occupancy so the room-plan output is meaningful.

```bash
npx --yes --package rollinggo@latest rollinggo hotel-detail \
  --hotel-id 123456 \
  --check-in-date 2026-05-01 \
  --check-out-date 2026-05-03 \
  --adult-count 2 \
  --room-count 1
```

### 3. Translate fuzzy preferences: `hotel-tags`

Use this when the user says things like "design-forward", "family-friendly", "great breakfast", or "brand-heavy".

```bash
npx --yes --package rollinggo@latest rollinggo hotel-tags
```

## Typical order of operations for this skill

### Already booked

1. collect hotel name, dates, occupancy, room type
2. use `hotel-detail` to inspect current pricing and cancellation rules
3. ask for booking platform, exact plan, or booked price only if still needed
4. produce a `Watch Task Summary`

### Not booked yet

1. use `hotel-tags` for fuzzy preferences if needed
2. use `search-hotels` to get 3 to 5 candidates
3. once the user picks one, use `hotel-detail`
4. turn that into a watch task

## Key rules

- `--place-type` must come from `search-hotels --help`
- `hotel-detail` does not support `--format table`
- `--check-out-date` must be later than `--check-in-date`
- prefer `hotelId` whenever you have it
- if there is no real price-history data, do not invent drop percentages

## Troubleshooting

- **Missing API key:** set `RollingGo_API_KEY` or pass `--api-key`
- **No results:** loosen stars, tags, distance, budget, then dates
- **No room plans in detail output:** this is a business result, not necessarily an error; try different dates, occupancy, or hotel
