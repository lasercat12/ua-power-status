# ua-power-status

Стан енергосистеми України по регіонах у форматі JSON: 🟢 норма · 🟡 дефіцит · 🔴 графіки відключень · 🚨 аварійні відключення.

> ⚠️ **Неофіційні дані без жодних гарантій.** Проєкт не пов'язаний з НЕК «Укренерго».
> Дані можуть запізнюватися, бути неповними або помилковими. Не використовуйте їх
> як єдине джерело для рішень, де важлива безпека. Офіційне джерело — застосунок і сайт «Укренерго».

## Файли

| Файл | Що всередині |
|---|---|
| [`data/regions.json`](data/regions.json) | поточний стан усіх регіонів |
| [`data/history.json`](data/history.json) | останні 200 змін стану |
| [`schema/regions.schema.json`](schema/regions.schema.json) | JSON Schema для `regions.json` |

Пряме посилання для програм:

```
https://raw.githubusercontent.com/lasercat12/ua-power-status/main/data/regions.json
```

## Оновлення

- Стан перевіряється щохвилини.
- Файли оновлюються одразу після зміни стану будь-якого регіону і не рідше ніж раз на годину.
- `generated_at` — час останньої успішної перевірки. **Якщо він старший за 2 години, вважайте дані неактуальними.**

## Формат `regions.json`

```json
{
  "schema_version": 1,
  "generated_at": "2026-10-09T08:34:32Z",
  "changed_at": "2026-10-09T07:09:47Z",
  "source": "НЕК «Укренерго» — неофіційно, без гарантій",
  "statuses": { "outage_schedules": { "code": 10, "emoji": "🔴", "color": "#e54825", "title_uk": "…", "title_en": "…" } },
  "regions": [
    {
      "id": 31,
      "slug": "kyiv",
      "name_uk": "м. Київ",
      "name_en": "Kyiv",
      "status": "outage_schedules",
      "status_code": 10,
      "emoji": "🔴",
      "color": "#e54825",
      "title_uk": "Електроенергії значно не вистачає",
      "title_en": "Significant electricity shortages",
      "since": "2026-10-09T07:08:37Z",
      "period": { "start": "2026-10-09T07:00:00Z", "end": "2026-10-09T09:00:00Z" },
      "next_change": null
    }
  ]
}
```

| Поле | Опис |
|---|---|
| `id` | ідентифікатор регіону (стабільний) |
| `slug`, `name_uk`, `name_en` | назва регіону; `null`, поки відповідність не перевірена |
| `status` | `normal` · `shortage` · `outage_schedules` · `emergency_outages` · `unknown` |
| `status_code` | числовий код стану з джерела |
| `emoji`, `color` | значок і колір стану (як в офіційному застосунку) |
| `title_uk`, `title_en` | назва стану |
| `since` | з якого моменту діє поточний стан (UTC) |
| `period` | `{ "start", "end" }` поточного періоду обмежень (UTC) або `null` |
| `next_change` | `{ "at", "status", "status_code" }` найближчої запланованої зміни або `null` |

Усі часи — UTC у форматі ISO 8601 (`…Z`).

## Приклади

**curl + jq**
```bash
curl -s https://raw.githubusercontent.com/lasercat12/ua-power-status/main/data/regions.json \
  | jq '.regions[] | select(.slug == "kyiv") | {emoji, title_uk, since, period}'
```

**JavaScript**
```js
const r = await fetch("https://raw.githubusercontent.com/lasercat12/ua-power-status/main/data/regions.json");
const { regions } = await r.json();
const kyiv = regions.find(x => x.slug === "kyiv");
console.log(kyiv.emoji, kyiv.title_uk);
```

**Python**
```python
import json, urllib.request
url = "https://raw.githubusercontent.com/lasercat12/ua-power-status/main/data/regions.json"
regions = json.load(urllib.request.urlopen(url))["regions"]
print([r["name_uk"] for r in regions if r["status"] != "normal"])
```

Будь ласка, не опитуйте файл частіше ніж раз на хвилину: частіше він однаково не оновлюється.

---

## English

Unofficial, machine-readable status of Ukraine's power grid by region (JSON). **No guarantees**, not affiliated with NPC Ukrenergo; data may be delayed, incomplete or wrong — do not use it as the only source for safety-critical decisions. Checked every minute; files are updated on every change and at least hourly. If `generated_at` is older than 2 hours, treat the data as stale. All times are UTC (ISO 8601).
