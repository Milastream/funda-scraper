[Funda Scraper](https://apify.com/alwaysprimedev/funda-scraper?fpr=data)

# 🏠 Funda Scraper — The Dutch Real Estate Goldmine 🇳🇱

> **Turn funda.nl into a clean, structured dataset in minutes.**
> Built for analysts, investors, real-estate platforms, ML teams and anyone who wants Dutch property data on tap.

[![Made for Apify](https://img.shields.io/badge/Made%20for-Apify-ff9012)](https://apify.com/)
[![Python 3.11](https://img.shields.io/badge/Python-3.11+-blue)](https://www.python.org/)

---

## ✨ Why this scraper?

🚀 **Blazing fast** — uses TLS-impersonated HTTP, **no headless browser**, no GBs of RAM. Pages return in **~0.5s**.
🧠 **Smart parsing** — pulls every meaningful field: price, m², rooms, energy label, year built, photos, location and more.
🧱 **Battle-tested** — rotates browser TLS fingerprints (Chrome / Firefox / Safari) so requests look 100% human.
🎯 **Filter to the listings you want** — by city, price range, sort order, sale or rent.
📦 **Plug-and-play output** — clean JSON ready for BI dashboards, BigQuery, Postgres or Pandas.
💰 **Pay-per-result friendly** — efficient enough to run cheap. Designed for monetization on the Apify platform.

---

## 🎬 What it does

Give it a city. Get a structured catalog of every property currently listed there:

```
amsterdam ➜ 🏠 1,200+ listings  ➜  📊 30+ fields each  ➜  ☁️ JSON / CSV / XLSX
```

Run it once. Run it daily. Run it across 50 cities in parallel. The shape of the output never changes.

---

## ⚡ Quick start

1. Hit **Try for free** on the actor page.
2. Pick your areas (e.g. `amsterdam`, `rotterdam`, `utrecht`).
3. Choose **For sale** or **For rent**, set price range and how many listings you want.
4. Click **Start** ▶️ — your dataset is ready in a few minutes.

---

## 🛠️ Input

| Field | Type | Description | Example |
| --- | --- | --- | --- |
| `searchType` | enum | `koop` (sale) or `huur` (rent) | `"koop"` |
| `areas` | string[] | City / region slugs | `["amsterdam", "utrecht"]` |
| `minPrice` | int | Minimum asking price (EUR) | `300000` |
| `maxPrice` | int | Maximum asking price (EUR) | `750000` |
| `sortBy` | enum | Order of results | `"price_up"` |
| `maxItems` | int | Stop after N listings (`0` = no limit) | `100` |
| `scrapeDetails` | bool | Full details per listing (slower) or URL-only (lightning fast) | `true` |
| `concurrency` | int | Parallel requests (1–25) | `5` |
| `startUrls` | string[] | Advanced — raw Funda URLs to scrape directly | – |

### Example input

```
{
  "searchType": "koop",
  "areas": ["amsterdam", "haarlem"],
  "minPrice": 400000,
  "maxPrice": 900000,
  "sortBy": "date_down",
  "maxItems": 200,
  "scrapeDetails": true,
  "concurrency": 5
}
```

---

## 📦 Output — beautifully structured

Every listing comes back as a flat, BI-friendly JSON record:

```
{
  "url": "https://www.funda.nl/detail/koop/amsterdam/appartement-van-nijenrodeweg-488/43331703/",
  "id": "43331703",
  "scraped_at": "2026-05-04T23:19:39+00:00",
  "listing_type": "sale",

  "address": {
    "street": "Van Nijenrodeweg 488",
    "postal_code": "1082 HN",
    "city": "Amsterdam",
    "region": "Noord-Holland",
    "neighborhood": "Buitenveldert-Zuid-Midden"
  },

  "price_eur": 495000,
  "price_per_m2_eur": 5690,
  "service_costs_eur": 70,
  "vve_costs_eur": 238,
  "status": "Beschikbaar",
  "acceptance": "In overleg",

  "property_kind": "Appartement",
  "property_type": "Portiekwoning (appartement)",
  "build_type": "Bestaande bouw",
  "year_built": 1965,
  "roof_type": "Plat dak bedekt met bitumineuze dakbedekking",

  "living_area_m2": 87,
  "exterior_area_m2": 6,
  "external_storage_m2": 6,
  "plot_area_m2": null,
  "volume_m3": 277,

  "rooms": 5,
  "bedrooms": 4,
  "bathrooms": 1,
  "floors": 1,

  "energy_label": "A",
  "insulation": "Dubbel glas, HR-glas en vloerisolatie",
  "heating": "Blokverwarming",
  "hot_water": "Elektrische boiler",

  "garden": null,
  "balcony_or_terrace": "In woonwijk en vrij uitzicht",
  "parking": "Betaald parkeren en parkeervergunningen",

  "description": "In Buitenveldert-West ligt dit lichte en praktisch ingedeelde appartement...",
  "main_photo": "https://cloud.funda.nl/.../868_1440x960.jpg",
  "photos": [
    "https://cloud.funda.nl/.../868_1440x960.jpg",
    "https://cloud.funda.nl/.../913_1440x960.jpg",
    "..."
  ],
  "breadcrumbs": ["Home", "Amsterdam", "Buitenveldert-Zuid-Midden", "Van Nijenrodeweg 488"]
}
```

Choose your format on Apify: **JSON · JSONL · CSV · XLSX · HTML · XML · RSS**.

---

## 💡 Use cases

| Who | What they get |
| --- | --- |
| 🏢 **Real-estate platforms** | Backfill catalogs, monitor new listings, enrich your own data |
| 📈 **Investors & funds** | Track price/m² trends across neighborhoods over time |
| 🏘️ **Buyers / agents** | Daily alerts for new listings matching your criteria |
| 🤖 **ML / AI teams** | Training data for valuation models, image generation, NLP on descriptions |
| 📊 **Market analysts** | Build dashboards on supply, energy labels, build year, regional pricing |
| 🌍 **PropTech startups** | Skip months of scraper R&D and ship your product |

---

## 🧰 Tips & tricks

- **Want everything?** Set `maxItems: 0` and let it run.
- **Need a daily delta?** Use `sortBy: "date_down"` with a small `maxItems` and schedule the actor.
- **Targeting a neighborhood?** Use slugs like `amsterdam/centrum` or `rotterdam/kralingen`.
- **Specific pages only?** Drop them into `startUrls`.
- **Speed vs. depth:** `scrapeDetails: false` returns URLs only, perfect for diff-style daily monitoring.

---

## ❓ FAQ

**How many listings can I scrape?**
There is no hard limit. The actor handles pagination automatically. Apify usage costs scale linearly.

**How fresh is the data?**
Real-time. Each run hits Funda's live pages.

**Can I run it on a schedule?**
Yes — use Apify Schedules. Daily, hourly, anything.

**What about rate limiting?**
The actor uses TLS-impersonated requests and a configurable concurrency limit. The default (5) is gentle and safe.

**Does it scrape rentals too?**
Absolutely. Set `searchType: "huur"`.

---

## 📜 Legal

This actor is provided for personal, research and analytical use. You are responsible for ensuring your use complies with Funda's Terms of Service, the GDPR, and any applicable local laws. Scrape only what you need, respect the site, and don't republish copyrighted content.

---

Built with ❤️ for the Apify marketplace. Happy scraping! 🚀