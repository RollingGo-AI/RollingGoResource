---
name: hotel-booking-hk-midweek-luxury
description: 香港五星级酒店周中抄底助手。帮助用户在周一至周四入住香港五星酒店，利用工作日低价窗口以三四星的预算享受五星体验。触发词——"香港五星周中""香港酒店抄底""Hong Kong luxury midweek""周中五星捡漏"。
homepage: https://mcp.agentichotel.cn
metadata:
  {
    "openclaw": {
      "emoji": "🏨",
      "primaryEnv": "RollingGo_API_KEY",
      "requires": {
        "anyBins": ["rollinggo", "npx", "node", "uvx", "uv"],
        "env": ["RollingGo_API_KEY"]
      }
    }
  }
---

# 香港五星级酒店的周中抄底计

## When to Use

✅ **适用场景：**
- 计划周一至周四前往香港，想用 1000-1200 港币/晚的预算住进五星级酒店（同酒店周末常价 1800-2500+）
- 出差/自由行日期灵活，愿意把行程调整到周中以换取五星体验的大幅折扣
- 想在尖沙咀、中环等核心商圈之外 3-5km 找到交通便利但价格骤降的五星替代方案（如沙田、荃湾、东涌等）
- 关注含早/泳池/健身房等权益，希望在低价的同时不牺牲五星配套

❌ **不适用场景：**
- 必须在周五至周日入住——周末五星价格回归高位，本 Skill 的抄底逻辑不适用
- 预算无上限、不关注性价比——直接搜索即可，无需抄底策略
- 需要预订香港以外地区的酒店
- 用户询问非酒店类预订（如机票、火车票、租车等）

## API Key

解析顺序：`--api-key` 参数 → `RollingGo_API_KEY` 环境变量。

还没有 Key？前往申请：https://mcp.agentichotel.cn/apply

## Runtime

根据用户环境选择，加载对应参考文件并在会话中保持：

- **npm/npx/Node 环境：** 加载 [references/rollinggo-npx.md](references/rollinggo-npx.md)
- **uv/uvx/Python 环境：** 加载 [references/rollinggo-uv.md](references/rollinggo-uv.md)

未指定时默认 → **npm/npx**（兼容性更广）。

## Primary Workflow

1. **确认关键信息：** 目的地商圈（默认香港全域）、可选的周中日期范围、每晚预算上限（建议 1200 以内）、是否需要含早/泳池等权益
2. **获取合法 tag：** 运行 `hotel-tags` 获取可用标签字符串（如 `"breakfast included"`、`"swimming pool"`、`"gym"`）
3. **核心区基准搜索：** 以尖沙咀或中环为 `--place`，搜索五星酒店，用 `--format table` 拉取 10 条结果，**记录核心区均价作为基准线**
4. **外围抄底搜索：** 将 `--distance-in-meter` 设为 3000-5000，搜索核心商圈外围的五星酒店，同样拉取 10 条结果
5. **周中 vs 周末对比（关键决策步骤）：**
   - 如果 Step 3/4 的周中价格已在预算内 → 直接进入 Step 6
   - 如果周中价格仍偏高 → 尝试放宽 `--max-price-per-night` 50-100 元，或切换到非核心商圈（沙田、荃湾、东涌）重新搜索
   - 如果想验证「周中到底便宜多少」→ 用同一参数分别搜索周二和周六的价格，直观对比差额
6. **锁定目标：** 对候选酒店运行 `hotel-detail` 确认具体房型、取消政策和含税总价
7. **无结果 → 按 Filter Loosening 策略降级处理**

## Expert Logic

本场景的核心洞察：**香港五星酒店的定价是「双曲线」结构——周末被旅客/会展需求推高 50-100%，而周中（尤其周二、周三）因商务客退潮出现价格真空。**

三个维度的叠加逻辑：

1. **时间维度（错峰抄底）：** 香港五星酒店周二至周四的挂牌价通常比周五至周日低 30-50%。例如同一家尖沙咀五星，周六 2200 港币/晚 → 周三可能仅 1100 港币/晚。这是因为周末被内地游客和会展参会者推高，而周中商务客减少形成供给过剩。

2. **空间维度（外围替代）：** 尖沙咀/中环的五星酒店即使在周中仍有品牌溢价。但沙田、荃湾、东涌等地的五星酒店（如万豪、喜来登系列），因不在核心旅游区，周中价格可再低 20-30%。这些区域地铁直达中环仅 20-30 分钟，交通成本几乎可以忽略。

3. **价格天花板（极端限制）：** 设定 `--max-price-per-night 1200` 作为硬上限，在周中+外围的双重折扣下，这个价位足以触达多数五星酒店。如果同时锁定含早（`--preferred-tag "breakfast included"`），则实际日均总成本（住宿+早餐）比核心区四星+自费早餐更低。

**量化省钱逻辑：** 以两晚行程计算，核心区周末五星均价 2200×2=4400，本策略（外围周中）预计 1000×2=2000，节省约 2400 港币（55%），且酒店星级、房间面积和设施配套完全一致。

## Commands Quick Reference

```bash
# Step 1: Get valid tag strings
rollinggo hotel-tags

# Step 2: Search core area (Tsim Sha Tsui) midweek 5-star as price baseline
rollinggo search-hotels \
  --origin-query "Find 5-star hotels in Tsim Sha Tsui Hong Kong for midweek stay" \
  --place "Tsim Sha Tsui" \
  --place-type "<value from --help>" \
  --country-code "HK" \
  --check-in-date "2026-05-12" \
  --stay-nights 2 \
  --star-ratings "5.0,5.0" \
  --max-price-per-night 1500 \
  --format table \
  --size 10

# Step 3: Search periphery (Sha Tin) for price-drop comparison
rollinggo search-hotels \
  --origin-query "Find 5-star hotels near Sha Tin Hong Kong midweek bargain" \
  --place "Sha Tin" \
  --place-type "<value from --help>" \
  --country-code "HK" \
  --check-in-date "2026-05-12" \
  --stay-nights 2 \
  --star-ratings "5.0,5.0" \
  --max-price-per-night 1200 \
  --preferred-tag "breakfast included" \
  --preferred-tag "swimming pool" \
  --format table \
  --size 10

# Step 4: Weekend comparison (same params, Saturday check-in) to quantify savings
rollinggo search-hotels \
  --origin-query "Find 5-star hotels near Sha Tin Hong Kong weekend rate comparison" \
  --place "Sha Tin" \
  --place-type "<value from --help>" \
  --country-code "HK" \
  --check-in-date "2026-05-16" \
  --stay-nights 2 \
  --star-ratings "5.0,5.0" \
  --format table \
  --size 10

# Step 5: Deep dive - check room types and cancellation policy for target hotel
rollinggo hotel-detail \
  --hotel-id <hotelId> \
  --check-in-date 2026-05-12 \
  --check-out-date 2026-05-14 \
  --adult-count 2 --room-count 1
```

## Key Rules

- `--place-type` 必须使用 `rollinggo search-hotels --help` 中的精确值
- `--star-ratings` 格式：`min,max`，如 `5.0,5.0` 表示仅搜索五星
- `--format table` **仅**可用于 `search-hotels`，`hotel-detail` 和 `hotel-tags` 不支持
- `--child-count` 的数值必须与 `--child-age` 的出现次数完全一致
- `--check-out-date` 必须晚于 `--check-in-date`（仅 `hotel-detail` 使用）
- tag 字符串须先通过 `hotel-tags` 命令获取，不得自行编造
- 本场景特有：`--check-in-date` 务必选择周一至周四，周五至周日不属于本 Skill 的抄底窗口
- 优先使用 `--hotel-id` 而非 `--name` 查询酒店详情

## Filter Loosening（无结果时降级策略）

按顺序逐步尝试：

1. **放宽预算：** 将 `--max-price-per-night` 从 1200 提升至 1500
2. **扩大搜索量：** 增大 `--size` 至 15-20，获取更多候选
3. **扩大搜索半径：** 将 `--distance-in-meter` 从 3000 增至 5000-8000
4. **移除 tag 过滤：** 去掉 `--preferred-tag` 中的泳池/含早等非必要标签
5. **放宽星级：** 将 `--star-ratings` 从 `5.0,5.0` 调整为 `4.5,5.0`（部分四星半酒店品质接近五星）
6. **调整日期：** 如果当前周中日期无结果，尝试前后一周的周中日期
