---
name: hotel-booking-pet-friendly
description: 涓烘惡甯﹀疇鐗╁嚭琛岀殑鏃呭鎼滅储鍏佽瀹犵墿鍏ヤ綇鐨勯厭搴楋紝鏀寔鎸夎窛绂诲拰璁炬柦绛涢€夈€傚綋鐢ㄦ埛鎻愬埌甯﹀疇鐗╂梾琛屻€佸疇鐗╁弸濂介厭搴椼€佹惡瀹犲叆浣忋€乸et friendly hotel绛夋弿杩版椂瑙﹀彂銆?
homepage: https://mcp.agentichotel.cn
metadata:
  {
    "openclaw": {
      "emoji": "馃彣",
      "primaryEnv": "RollingGo_API_KEY",
      "requires": {
        "anyBins": ["rollinggo", "npx", "node", "uvx", "uv"],
        "env": ["RollingGo_API_KEY"]
      }
    }
  }
---

# 鎼哄疇鍏ヤ綇閰掑簵鎼滅储

甯姪鎼哄甫瀹犵墿鍑鸿鐨勬梾瀹㈠揩閫熸壘鍒板厑璁稿疇鐗╁叆浣忕殑閰掑簵锛岄€氳繃鏍囩寮哄埗绛涢€?+ 璁炬柦鎺掗櫎 + 浠锋牸瀵规瘮锛岄伩鍏嶅埌搴楄鎷掓垨浜х敓楂橀瀹犵墿闄勫姞璐广€?

## When to Use

鉁?**閫傜敤鍦烘櫙锛?*

- 鐢ㄦ埛鎻愬埌鎼哄甫瀹犵墿锛堢尗銆佺嫍绛夛級鏃呰锛岄渶瑕佹壘鍏佽瀹犵墿鍏ヤ綇鐨勯厭搴椻€斺€斾緥濡?甯︾嫍鍘讳笁浜氾紝鎵句釜鑳藉甫瀹犵墿鐨勯厭搴?
- 鐢ㄦ埛璇㈤棶鏌愪釜鐩殑鍦伴檮杩戝摢浜涢厭搴楁槸瀹犵墿鍙嬪ソ鐨勶紙pet friendly锛夛紝涓旀媴蹇冨埌搴楄鎷掆€斺€斾緥濡?涓婃甯︾尗鍘婚厭搴楄鎷掍簡锛屽府鎴戞壘纭畾鑳藉甫瀹犵墿鐨?
- 鐢ㄦ埛闇€瑕佸姣斿瀹跺疇鐗╁弸濂介厭搴楃殑浠锋牸銆佽窛绂绘垨鎴垮瀷锛岄€夋嫨鎬т环姣旀渶楂樼殑鈥斺€斾緥濡?鏉窞瑗挎箹闄勮繎瀹犵墿閰掑簵鍝渚垮疁鍙堝ソ"
- 鐢ㄦ埛闇€瑕佹壘鏈夊疇鐗╂椿鍔ㄧ┖闂达紙鑺卞洯銆佽崏鍧級鎴栧疇鐗╁瘎瀛樻湇鍔＄殑閰掑簵

鉂?**涓嶉€傜敤鍦烘櫙锛?*

- 鐢ㄦ埛鎼滅储閰掑簵浣嗘湭娑夊強瀹犵墿闇€姹傦紙浣跨敤閫氱敤閰掑簵鎼滅储 Skill锛?
- 鐢ㄦ埛璇㈤棶闈為厭搴楃被浣忓鐨勫疇鐗╂斂绛栵紙濡傛皯瀹裤€丄irbnb锛?
- 鐢ㄦ埛璇㈤棶闈為厭搴楃被棰勮锛堝鏈虹エ銆佺伀杞︾エ銆佺杞︾瓑锛?

## API Key

瑙ｆ瀽椤哄簭锛歚--api-key` 鍙傛暟 鈫?`RollingGo_API_KEY` 鐜鍙橀噺銆?

杩樻病鏈?Key锛熷墠寰€鐢宠锛歨ttps://mcp.agentichotel.cn/apply

## Runtime

鏍规嵁鐢ㄦ埛鐜閫夋嫨锛?
- **npm/npx/Node 鐜锛?* 鍔犺浇 [references/rollinggo-npx.md](references/rollinggo-npx.md)
- **uv/uvx/Python 鐜锛?* 鍔犺浇 [references/rollinggo-uv.md](references/rollinggo-uv.md)

鏈寚瀹氭椂榛樿 鈫?npm/npx銆?

## Expert Logic

> 本场景策略：**宠物准入筛选 × 设施排除 × 价格梯度对比**

**为什么携宠出行必须用专属搜索策略？**

这里最重要的不是“宠物友好”这四个字本身，而是把可住性和真实成本一起确认。`--required-tag "pet friendly"` 解决的是准入问题，距离梯度对比解决的是价格问题，`hotel-detail` 则负责补上宠物附加费和房型限制这些隐藏条件。

这样搜索出来的结果才不是“理论上能带宠物”，而是“实际住起来也不会翻车”的方案。

在常见城市场景里，这种做法既能避免到店被拒，也经常能帮用户省下 100–300 元每晚的宠物相关隐性成本。

---
## Primary Workflow

### Step 1锛氱‘璁ゆ惡瀹犲弬鏁?

鍚戠敤鎴风‘璁や互涓嬩俊鎭細
- 鐩殑鍦板煄甯傛垨鏅尯
- 鍏ヤ綇鏃ユ湡鍜屼綇瀹挎櫄鏁?
- 鎴愪汉/鍎跨浜烘暟
- 瀹犵墿绫诲瀷鍜屾暟閲忥紙閮ㄥ垎閰掑簵瀵瑰ぇ鍨嬬姮鏈夐檺鍒讹級
- 姣忔櫄棰勭畻涓婇檺

---

### Step 2锛氳幏鍙栨湁鏁堟爣绛?

```bash
rollinggo hotel-tags
```

> 浠庤繑鍥炵粨鏋滀腑纭 `pet friendly`锛堟垨 `pets allowed` 绛夊彉浣擄級鍜?`adults only` 鐨勭簿纭瓧绗︿覆銆?

---

### Step 3锛氱涓€杞悳绱⑩€斺€旂洰鐨勫湴闄勮繎瀹犵墿鍙嬪ソ閰掑簵

```bash
rollinggo search-hotels \
  --origin-query "Pet-friendly hotels in downtown Shanghai" \
  --place "涓婃捣" \
  --place-type city \
  --check-in-date 2026-04-10 \
  --stay-nights 2 \
  --adult-count 2 \
  --room-count 1 \
  --required-tag "pet friendly" \
  --excluded-tag "adults only" \
  --max-price-per-night 600 \
  --star-ratings 3.0,5.0 \
  --size 10 \
  --format table
```

---

### Step 4锛氬垎鏀喅绛栤€斺€旂粨鏋滄槸鍚﹀厖瓒筹紵

瀵规瘮 Step 3 鐨勬悳绱㈢粨鏋滐細

- **濡傛灉缁撴灉 鈮?5 鏉′笖鏈?鈮?500 鍏冪殑閫夐」** 鈫?鐩存帴杩涘叆 Step 6 鏌ョ湅璇︽儏
- **濡傛灉缁撴灉鍋忓皯锛? 3 鏉★級鎴栦环鏍兼櫘閬嶅亸楂?* 鈫?杩涘叆 Step 5 鏀惧璺濈鎼滅储
- **濡傛灉缁撴灉涓洪浂** 鈫?杩涘叆 Filter Loosening 娴佺▼

---

### Step 5锛氱浜岃疆鎼滅储鈥斺€旀墿澶ц窛绂绘壘鎬т环姣?

```bash
rollinggo search-hotels \
  --origin-query "Pet-friendly hotels in Shanghai with a wider radius" \
  --place "涓婃捣" \
  --place-type city \
  --check-in-date 2026-04-10 \
  --stay-nights 2 \
  --adult-count 2 \
  --room-count 1 \
  --required-tag "pet friendly" \
  --excluded-tag "adults only" \
  --max-price-per-night 600 \
  --star-ratings 3.0,5.0 \
  --distance-in-meter 5000 \
  --size 10 \
  --format table
```

> 瀵规瘮涓よ疆缁撴灉鐨勪环宸紝鍛婄煡鐢ㄦ埛"鎵╁ぇ鑼冨洿鍚庢瘡鏅氬彲鐪?楼XXX"銆?

---

### Step 6锛氱‘璁ょ洰鏍囬厭搴楄鎯?

```bash
rollinggo hotel-detail \
  --hotel-id <浠庢悳绱㈢粨鏋滆幏鍙栫殑hotel-id> \
  --check-in-date 2026-04-10 \
  --check-out-date 2026-04-12 \
  --adult-count 2 \
  --room-count 1
```

> 閲嶇偣纭锛?1) 鏄惁鏀跺彇瀹犵墿闄勫姞璐癸紱(2) 瀹犵墿浣撻噸/鍝佺闄愬埗锛?3) 鍙栨秷鏀跨瓥鈥斺€旀惡瀹犲嚭琛屽彉鏁版洿澶э紝浼樺厛閫夊彲鍏嶈垂鍙栨秷鐨勬埧鍨嬨€?

---

### Step 7锛氬悜鐢ㄦ埛鍛堢幇缁撴灉

缁煎悎灞曠ず浠ヤ笅淇℃伅锛岃緟鍔╁喅绛栵細
- 閰掑簵鍚嶇О銆佹槦绾с€佽窛鐩殑鍦拌窛绂?
- 姣忔櫄浠锋牸鍙婂惈绋庢€讳环
- 瀹犵墿鏀跨瓥锛氭槸鍚﹀厑璁搞€侀檮鍔犺垂閲戦銆佷綋閲嶉檺鍒?
- 鍙栨秷鏀跨瓥
- 鎺ㄨ崘鐞嗙敱锛堢患鍚堝疇鐗╁弸濂藉害銆佷环鏍笺€佷綅缃級

---

## Commands Quick Reference

```bash
rollinggo hotel-tags

rollinggo search-hotels \
  --origin-query "Pet-friendly hotels in downtown Shanghai" \
  --place "涓婃捣" \
  --place-type city \
  --check-in-date 2026-04-10 \
  --stay-nights 2 \
  --adult-count 2 \
  --room-count 1 \
  --required-tag "pet friendly" \
  --excluded-tag "adults only" \
  --max-price-per-night 600 \
  --star-ratings 3.0,5.0 \
  --size 10 \
  --format table

rollinggo search-hotels \
  --origin-query "Pet-friendly hotels near West Lake Hangzhou" \
  --place "瑗挎箹" \
  --place-type landmark \
  --check-in-date 2026-04-10 \
  --stay-nights 2 \
  --adult-count 2 \
  --room-count 1 \
  --required-tag "pet friendly" \
  --excluded-tag "adults only" \
  --max-price-per-night 500 \
  --star-ratings 3.0,5.0 \
  --distance-in-meter 5000 \
  --size 10 \
  --format table

rollinggo hotel-detail \
  --hotel-id <hotelId> \
  --check-in-date 2026-04-10 \
  --check-out-date 2026-04-12 \
  --adult-count 2 \
  --room-count 1
```

---

## Key Rules

- `--place-type` 蹇呴』浣跨敤 `rollinggo search-hotels --help` 杩斿洖鐨勭簿纭灇涓惧€?
- `--star-ratings` 鏍煎紡涓?`min,max`锛屼緥濡?`3.0,5.0`
- `--format table` **浠?* `search-hotels` 鏀寔锛沗hotel-detail` 鍜?`hotel-tags` **涓嶅彲浣跨敤**
- `--child-count` 蹇呴』涓?`--child-age` 鍑虹幇娆℃暟瀹屽叏涓€鑷?
- `--check-out-date` 蹇呴』鏅氫簬 `--check-in-date`
- `search-hotels` 浣跨敤 `--stay-nights`锛宍hotel-detail` 浣跨敤 `--check-out-date`鈥斺€斾笉鍙贩鐢?
- 宸茬煡 `hotel-id` 鏃讹紝浼樺厛浣跨敤 `--hotel-id` 鑰岄潪 `--name`
- tag 瀛楃涓插繀椤诲厛閫氳繃 `hotel-tags` 鍛戒护鑾峰彇锛屼笉寰楄嚜琛岀紪閫狅紙瀹犵墿鐩稿叧 tag 鍙兘鍖呮嫭 `pet friendly`銆乣pets allowed` 绛夊彉浣擄紝浠ュ疄闄呰繑鍥炲€间负鍑嗭級
- 鎼滅储鏃?*蹇呴』**鍚屾椂浣跨敤 `--excluded-tag "adults only"` 鎺掗櫎浠呴檺鎴愪汉鐨勯厭搴?
- 灞曠ず缁撴灉鏃堕』**閲嶇偣鏍囨敞瀹犵墿闄勫姞璐瑰拰浣撻噸闄愬埗**锛岄伩鍏嶇敤鎴峰埌搴楀悗浜х敓棰濆璐圭敤
- 涓嶅瓨鍦?`--tag` 鍙傛暟锛屾爣绛捐繃婊ゅ彧鑳戒娇鐢?`--required-tag`銆乣--preferred-tag`銆乣--excluded-tag`

---

## Filter Loosening

缁撴灉涓嶈冻鏃讹紝鎸変互涓嬮『搴忛€愭鏀惧绛涢€夋潯浠讹細

1. 灏?`--required-tag "pet friendly"` 闄嶇骇涓?`--preferred-tag`锛堝厑璁搁潪瀹犵墿鍙嬪ソ閰掑簵娣峰叆锛屼絾浼樺厛鎺掑簭瀹犵墿鍙嬪ソ鐨勶級
2. 绉婚櫎 `--star-ratings` 闄愬埗锛堜笉闄愭槦绾э級
3. 澧炲ぇ `--size` 杩斿洖鏁伴噺涓婇檺锛?0 鈫?20锛?
4. 澧炲ぇ `--distance-in-meter`锛?000 鈫?5000 鈫?10000锛?
5. 鏀惧 `--max-price-per-night` 棰勭畻涓婇檺锛屽苟鎻愮ず鐢ㄦ埛浠锋牸鍙樺寲
6. 绉婚櫎 `--excluded-tag "adults only"`锛屽湪 `hotel-detail` 闃舵閫愪竴纭瀹犵墿鏀跨瓥
