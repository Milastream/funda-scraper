[Funda Scraper](https://apify.com/memo23/funda-scraper?fpr=data)

# Overview

The **Funda.nl Scraper** efficiently extracts property listings and detailed information from the Funda.nl website, a popular real estate platform in the Netherlands. It provides structured data for analysis, reporting, and business needs and is a powerful tool designed to efficiently extract real estate data from the Funda.nl platform. It enables you to gather property information from various cities in the Netherlands, including key details such as pricing, location, and property features. Ideal for real estate professionals, market researchers, and data enthusiasts.

---

## Important: Funda.nl vs Fundainbusiness.nl

> **This actor is specifically designed for [Funda.nl](https://www.funda.nl) (residential properties).**

| Website | Description | Actor to Use |
| --- | --- | --- |
| **funda.nl** | Residential properties (apartments, houses) | **This actor** |
| **fundainbusiness.nl** | Commercial properties (offices, shops, warehouses) | [Fundainbusiness Scraper](https://apify.com/memo23/fundainbusiness-scraper) |

### Example URLs

**Funda.nl (use THIS actor):**

- `https://www.funda.nl/detail/koop/amsterdam/appartement-wildeman-97/43279645/`
- `https://www.funda.nl/zoeken/koop?selected_area=["amsterdam"]`

**Fundainbusiness.nl (use [Fundainbusiness Scraper](https://apify.com/memo23/fundainbusiness-scraper) instead):**

- `https://www.fundainbusiness.nl/alle-bedrijfsaanbod/amsterdam/koop/30-dagen/`
- `https://www.fundainbusiness.nl/winkel/zierikzee/object-89532846-kerkhof-28-30/`

If you provide a fundainbusiness.nl URL to this actor, it will fail with an error message directing you to use the correct actor.

---

## Features

- **City-Wide Data Extraction**:
Select any city in the Netherlands or scrape data from all available cities.
- **Neighborhood Insights** 🆕:
Optionally enrich your property data with comprehensive neighborhood statistics including:

- Total inhabitants in the neighborhood
- Percentage of families with children
- Average asking price per m² for the area
- Official neighborhood and city names
- **Customizable Concurrency Settings**:
Control the number of simultaneous requests for optimized performance.
- **Retry Mechanism**:
Ensure reliable data collection with robust retry settings for failed requests.
- **Data Deduplication**:
Use custom storage names for persistent data storage and duplication prevention.
- **Proxy Support**:
Includes proxy configuration for anonymous and uninterrupted scraping.
- **Start URLs or search filters (dual mode)**:
Either paste one or more **Funda search or listing URLs** into `startUrls`, **or** leave `startUrls` empty and fill the **Search by filters** fields in the Apify input form (`searchKeywords`, `searchLocation`, `searchTransactionType`, `searchPriceMin`, `searchPriceMax`). The actor builds the canonical Funda URL for you. If any start URL is non-empty, **filter fields are ignored** for that run (URLs always win). Nationwide **buy** is the default when all filter fields are empty.

---

## How to Use

1. **Set up** an Apify account and open this actor on the Apify platform.
2. **Choose how to define the search:**

- **URLs (recommended for exact reproduction):** On [funda.nl](https://www.funda.nl), set filters, copy the URL from the address bar (or use a `/detail/…` listing URL). Add it under **Start by URLs** (Add / Bulk edit / text file).
- **Filters (no URL):** Clear all start URLs and use **Search by filters** — e.g. keywords only (`searchKeywords`: `gracht`), or add `searchLocation`, prices, and `searchTransactionType` (`buy` / `rent`).

1. **Set run limits** (`maxItems`, concurrency, retries) and optional **named store** for deduplication.
2. **Listing & crawl options:** `contentLanguage` (`nl` / `en`) for listing text; optional **neighborhood insights** via `includeNeighborhoodData`.
3. **Proxy:** Residential proxy is recommended for stable access to Funda.
4. **Run** the actor; results go to the default dataset (and optional KV store when `storeName` is set).

Same JSON examples also appear in the actor **input schema** description in the Apify Console.

---

## Input configuration

### What it is

This actor scrapes **residential listings** from **[funda.nl](https://www.funda.nl)** — either a **search** (many homes) or a **single listing** (`/detail/…` URL).

### How to use

| Mode | What you do |
| --- | --- |
| **URLs** | Add one or more funda.nl links in `startUrls` (search page or detail page). |
| **Filters** | Do **not** use any start URL: use `startUrls: []` or remove all URL rows in the UI. Then optionally fill the filter fields. |

If **any** `startUrls` entry has a non-empty `url`, **filter fields are ignored** when building the search (the URL wins).

### Filter mode — mandatory fields

- **Required:** only that **no non-empty start URL** exists. In JSON use `"startUrls": []`. None of `searchKeywords`, `searchLocation`, `searchPriceMin`, `searchPriceMax`, or `searchTransactionType` are required.
- **Optional refinements:** keywords, location, min/max price (EUR), `searchTransactionType` `buy` / `rent` (defaults to **buy**). If you leave everything blank, you still get a valid run: **nationwide buy**.

- *Examples:* `searchKeywords` → `gracht`, `tuin`; `searchLocation` → `amsterdam`, `rotterdam`, `den-haag, 5km`; `searchPriceMin` / `searchPriceMax` → `200000` / `450000`.

### Minimal JSON

**URL mode**

```
{
    "startUrls": [{ "url": "https://www.funda.nl/zoeken/koop?selected_area=[%22amsterdam%22]" }],
    "maxItems": 50
}
```

**Filter mode** (keyword example; `startUrls` empty is the important part)

```
{
    "startUrls": [],
    "searchKeywords": "gracht",
    "maxItems": 25
}
```

### Other inputs

`maxItems`, `maxConcurrency`, `minConcurrency`, `maxRequestRetries`, `storeName`, `includeNeighborhoodData`, `contentLanguage` (`nl` / `en`), and `proxy` behave the same in both modes. Prefer **RESIDENTIAL** proxy for Funda. Older API payloads may still send a nested `searchFilters` object; the actor still accepts that for compatibility.

For the general “URLs vs filters” pattern, see ./GUIDE_DUAL_MODE_INPUT_FOR_SCRAPER_ACTORS.md.

---

## Neighborhood Insights Feature 🆕

Optional enrichment from Funda’s Market Insights API. Set **`includeNeighborhoodData`** to **`true`** in the input (works with URL or filter mode).

### What data is included

| Field | Description | Example |
| --- | --- | --- |
| `city` | Official city name | Rotterdam |
| `neighbourhood` | Official neighborhood name | Het Lage Land |
| `inhabitants` | Population in the area | 11400 |
| `familiesWithChildren` | % families with children | 21.79 |
| `averageAskingPricePerM2` | Average €/m² | 3782 |

If data is missing for an area, that listing is still scraped; the extra fields may be omitted.

---

## Output Structure

The scraper produces a list of listings, with each listing containing fields such as:

```
{
    "_id": "7717991",
    "Price": {
        "SellingPrice": "€ 495.000 k.k.",
        "NumericSellingPrice": 495000,
        "IsAuction": false
    },
    "KenmerkSections": [
        {
            "Index": 1,
            "Id": "overdracht",
            "Title": "Overdracht",
            "KenmerkenList": [
                {
                    "Index": 1,
                    "Id": "overdracht-vraagprijs",
                    "Label": "Vraagprijs",
                    "Value": "€ 495.000 kosten koper",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "PriceTooltip": {
                        "Price": "€ 495.000",
                        "LivingSpace": "59 m²",
                        "PriceIcon": "Information"
                    },
                    "Index": 2,
                    "Id": "overdracht-vraagprijsperm2",
                    "Label": "Vraagprijs per m²",
                    "Value": "€ 8.390",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 0,
                    "Id": "overdracht-aangeboden-sinds",
                    "Label": "Aangeboden sinds",
                    "Value": "Vandaag",
                    "LabelStyle": "None"
                },
                {
                    "Index": 5,
                    "Id": "overdracht-status",
                    "Label": "Status",
                    "Value": "Beschikbaar",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 0,
                    "Label": "Aanvaarding",
                    "Value": "Per direct beschikbaar",
                    "LabelStyle": "None"
                },
                {
                    "Index": 8,
                    "Id": "overdracht-bijdragevve",
                    "Label": "Bijdrage VvE",
                    "Value": "€ 164,54 per maand",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                }
            ]
        },
        {
            "Index": 2,
            "Id": "bouw",
            "Title": "Bouw",
            "KenmerkenList": [
                {
                    "Index": 1,
                    "Id": "bouw-soortobject",
                    "Label": "Soort appartement",
                    "Value": "Benedenwoning (appartement)",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 2,
                    "Id": "bouw-soortbouw",
                    "Label": "Soort bouw",
                    "Value": "Bestaande bouw",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 3,
                    "Id": "bouw-bouwjaar",
                    "Label": "Bouwjaar",
                    "Value": "1922",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 5,
                    "Id": "bouw-bijzonderheden",
                    "Label": "Specifiek",
                    "Value": "Beschermd stads- of dorpsgezicht en monumentaal pand",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 6,
                    "Id": "bouw-dak",
                    "Label": "Soort dak",
                    "Value": "Samengesteld dak bedekt met bitumineuze dakbedekking en pannen",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                }
            ]
        },
        {
            "Index": 3,
            "Id": "afmetingen",
            "Title": "Oppervlakten en inhoud",
            "KenmerkenList": [
                {
                    "Index": 1,
                    "Label": "Gebruiksoppervlakten",
                    "LabelStyle": "Title",
                    "KenmerkenList": [
                        {
                            "Index": 1,
                            "Id": "afmetingen-gebruiksoppervlakten-wonen",
                            "Label": "Wonen",
                            "Value": "59 m²",
                            "LabelStyle": "None",
                            "KenmerkenList": []
                        },
                        {
                            "Index": 4,
                            "Id": "afmetingen-gebruiksoppervlakte-externebergruimte",
                            "Label": "Externe bergruimte",
                            "Value": "4 m²",
                            "LabelStyle": "None",
                            "KenmerkenList": []
                        }
                    ]
                },
                {
                    "Index": 3,
                    "Id": "afmetingen-inhoud",
                    "Label": "Inhoud",
                    "Value": "218 m³",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                }
            ]
        },
        {
            "Index": 4,
            "Id": "indeling",
            "Title": "Indeling",
            "KenmerkenList": [
                {
                    "Index": 1,
                    "Id": "indeling-totalrooms",
                    "Label": "Aantal kamers",
                    "Value": "3 kamers (2 slaapkamers)",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 2,
                    "Id": "indeling-totalbathroom",
                    "Label": "Aantal badkamers",
                    "Value": "1 badkamer en 1 apart toilet",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 4,
                    "Id": "indeling-totalstories",
                    "Label": "Aantal woonlagen",
                    "Value": "1 woonlaag",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 6,
                    "Id": "indeling-locatedat",
                    "Label": "Gelegen op",
                    "Value": "Begane grond",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                }
            ]
        },
        {
            "Index": 5,
            "Id": "energie",
            "Title": "Energie",
            "KenmerkenList": [
                {
                    "EnergyData": {
                        "EnergyLabel": "D"
                    },
                    "EnergyLabel": "D",
                    "Index": 1,
                    "Id": "energie-energielabel",
                    "Label": "Energielabel",
                    "Value": "Wat betekent dit?",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 2,
                    "Id": "energie-isolatie",
                    "Label": "Isolatie",
                    "Value": "Gedeeltelijk dubbel glas en muurisolatie",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 3,
                    "Id": "energie-verwarming",
                    "Label": "Verwarming",
                    "Value": "Cv-ketel",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 4,
                    "Id": "energie-warmwater",
                    "Label": "Warm water",
                    "Value": "Cv-ketel",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 5,
                    "Id": "energie-cv",
                    "Label": "Cv-ketel",
                    "Value": "Ferroli BlueSense 4 32 (gas gestookt combiketel uit 2021, eigendom)",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                }
            ]
        },
        {
            "Index": 6,
            "Id": "cadastral",
            "Title": "Kadastrale gegevens",
            "KenmerkenList": [
                {
                    "Index": 1,
                    "Id": "cadastral-parcel",
                    "Label": "AMSTERDAM V 10963",
                    "LabelStyle": "Title",
                    "KenmerkenList": [
                        {
                            "Index": 3,
                            "Id": "cadastral-ownershipsituation",
                            "Label": "Eigendomssituatie",
                            "Value": "Gemeentelijke erfpacht",
                            "LabelStyle": "None",
                            "KenmerkenList": []
                        },
                        {
                            "Index": 4,
                            "Id": "cadastral-fees",
                            "Label": "Lasten",
                            "Value": "Afgekocht tot 30-09-2054",
                            "LabelStyle": "None",
                            "KenmerkenList": []
                        }
                    ]
                }
            ]
        },
        {
            "Index": 7,
            "Id": "buitenruimte",
            "Title": "Buitenruimte",
            "KenmerkenList": [
                {
                    "Index": 1,
                    "Id": "buitenruimte-ligging",
                    "Label": "Ligging",
                    "Value": "Aan rustige weg en in woonwijk",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 2,
                    "Id": "buitenruimte-tuin",
                    "Label": "Tuin",
                    "Value": "Achtertuin",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 3,
                    "Id": "buitenruimte-hoofdtuin",
                    "Label": "Achtertuin",
                    "Value": "52 m² (8,25 meter diep en 6,30 meter breed)",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                },
                {
                    "Index": 4,
                    "Id": "buitenruimte-liggingtuin",
                    "Label": "Ligging tuin",
                    "Value": "Gelegen op het zuiden",
                    "LabelStyle": "None",
                    "KenmerkenList": []
                }
            ]
        },
        {
            "Index": 8,
            "Id": "bergruimte",
            "Title": "Bergruimte",
            "KenmerkenList": [
                {
                    "Index": 0,
                    "Label": "Schuur/berging",
                    "Value": "Vrijstaande stenen berging",
                    "LabelStyle": "None"
                },
                {
                    "Index": 0,
                    "Label": "Voorzieningen",
                    "Value": "Elektra",
                    "LabelStyle": "None"
                }
            ]
        },
        {
            "Index": 10,
            "Id": "parkeergelegenheid",
            "Title": "Parkeergelegenheid",
            "KenmerkenList": [
                {
                    "Index": 0,
                    "Label": "Soort parkeergelegenheid",
                    "Value": "Betaald parkeren en parkeervergunningen",
                    "LabelStyle": "None"
                }
            ]
        },
        {
            "Index": 11,
            "Id": "vvechecklist",
            "Title": "VvE checklist",
            "KenmerkenList": [
                {
                    "Index": 0,
                    "Label": "Inschrijving KvK",
                    "Value": "Ja",
                    "LabelStyle": "None"
                },
                {
                    "Index": 0,
                    "Label": "Jaarlijkse vergadering",
                    "Value": "Ja",
                    "LabelStyle": "None"
                },
                {
                    "Index": 0,
                    "Label": "Periodieke bijdrage",
                    "Value": "Ja (€ 164,54 per maand)",
                    "LabelStyle": "None"
                },
                {
                    "Index": 0,
                    "Label": "Reservefonds aanwezig",
                    "Value": "Ja",
                    "LabelStyle": "None"
                },
                {
                    "Index": 0,
                    "Label": "Onderhoudsplan",
                    "Value": "Ja",
                    "LabelStyle": "None"
                },
                {
                    "Index": 0,
                    "Label": "Opstalverzekering",
                    "Value": "Ja",
                    "LabelStyle": "None"
                }
            ]
        }
    ],
    "Labels": [
        {
            "Text": "Nieuw",
            "Type": "New"
        }
    ],
    "Aanbiedingstekst": "Sfeervol benedenhuis van ca. 59 m² met twee slaapkamers, een zonnige tuin van ca. 52 m² op het zuiden.\nGelegen in de karaktervolle Van Hilligaertstraat in de Nieuwe Pijp, in een gemeentelijk monument uit 1922, onderdeel van het beroemde Plan Zuid van Berlage. De woning is goed onderhouden met een recent vernieuwde badkamer en toilet en heeft een uitbouw aan de achterzijde uit 2014. Dankzij de tuin op het zuiden en de rustige straat woon je hier comfortabel en toch midden in de stad, op loopafstand van winkels, cafés en openbaar vervoer.\n\nIndeling\nVia de gemeenschappelijke hal bereik je de entree en kom je in de hal met een ruime kast met garderobe en wasmachineaansluiting. Vanuit hier bereik je de recent gerenoveerde badkamer en het separate toilet, de eerste slaapkamer en de keuken aan de achterzijde, en de woonkamer aan de voorzijde. Achter de slaapkamer is een uitbouw gemaakt (2014) waarin de tweede slaapkamer is gerealiseerd. De keuken én de tweede slaapkamer bieden toegang tot de zonnige achtertuin van ca. 52 m². Achterin de tuin staat een schuur van 4,4 m² met elektra, ideaal voor fietsen en extra bergruimte.\n\nErfpacht\nDe erfpacht is reeds omgezet naar eeuwigdurende erfpacht. De te betalen erfpachtcanon afgekocht tot 30 september 2054. En voor de periode ná 2054 is gekozen voor een jaarlijkse canon, die zal vanaf 1 oktober 2054 € 1.160,90 per jaar bedragen, te corrigeren voor inflatie.\n\nVereniging van Eigenaren\nDe Vereniging van Eigenaars \"De Vincent III\" omvat het gehele huizenblok en wordt professioneel beheerd (beheer: VvE Beheer Amsterdam). Het is een gezonde en actieve VvE: er wordt jaarlijkse vergaderd en er is een MJOP aanwezig. De te betalen servicekosten zijn € 164,54 per maand.\n\nParkeren\nParkeren kan via betaald parkeren of met een parkeervergunning. In de straat zelf is vaak plek beschikbaar.\n\nOmgeving & bereikbaarheid\nDe Van Hilligaertstraat ligt in de Nieuwe Pijp, een geliefde buurt in Amsterdam-Zuid. De Ferdinand Bolstraat, de Albert Cuypmarkt en talloze restaurants, cafés en winkels liggen om de hoek. Voor ontspanning zijn het Sarphatipark en de Amstel dichtbij. Met de fiets sta je binnen enkele minuten in het centrum. Tram- en buslijnen zijn op loopafstand en via de nabijgelegen A2 en A10 is de rest van de stad eenvoudig bereikbaar.\n\nBijzonderheden\n-\tBouwjaar 1922\n-\tBenedenhuis van 59 m² (meetrapport conform BBMI aanwezig)\n-\tTwee slaapkamers\n-\tTuin gelegen op het zuiden\n- \tErfpacht afgekocht tot 2054, daarna eeuwigdurend vastgeklikt \n- \tVerwarming en warm water via CV-ketel (Ferroli BlueSense 4 32, 2021)\n-\tEnergielabel D\n-\tGelegen in karakteristiek complex in Plan Zuid\n- \tGemeentelijk monument\n-\tProfessioneel beheerde VvE, servicekosten ca. € 164,54 per maand\n-\tVaste notaris: KB notarissen in Amsterdam\n-\tOplevering kan snel\n\nDe verkoop geschiedt onder de voorwaarde dat een overeenkomst pas tot stand komt op het moment dat alle betrokken partijen het koopcontract hebben ondertekend (model van de KNB ring Amsterdam of soortgelijk).\n\nDisclaimer: \nDeze advertentie is met zorg samengesteld en gebaseerd op informatie die ten tijde van het opmaken van deze tekst bij de makelaar beschikbaar was. Desalniettemin kunnen er onbedoelde onjuistheden in staan en kunnen de opgegeven maten (marginaal) afwijken. Aan de inhoud van deze advertentie kunnen geen rechten worden ontleend. \nDe koper dient zelf onderzoek te (laten) doen naar de privaat- en publiekrechtelijke aspecten en (on)mogelijkheden van onder meer het door de koper beoogde gebruik, de staat waarin het object verkeert, milieu en (ver)bouwplannen.",
    "PerceelOppervlakteSubTitle": "",
    "WoonOppervlakteSubTitle": "59 m²",
    "KoopprijsSubTitle": "€ 495.000 k.k.",
    "IsAuction": false,
    "AddressTitle": "Van Hilligaertstraat 16-A",
    "AddressSubTitle": "1072 JZ Amsterdam",
    "IsInternational": false,
    "NumberOfBedrooms": "2",
    "ListingUrl": "https://www.funda.nl/detail/43162354",
    "ShareListingText": "Gevonden via #funda",
    "ReportErrorUrl": "https://help.funda.nl:443/s/mef?globalID=7717991&returnURL=https%3a%2f%2fwww.funda.nl%2fdetail%2fkoop%2famsterdam%2fappartement-van-hilligaertstraat-16-a%2f43162354%2f&brokerID=24818",
    "BuurtName": "Cornelis Troostbuurt",
    "BuurtIdentifier": "amsterdam/cornelis-troostbuurt",
    "Coordinates": {
        "Latitude": 52.349583,
        "Longitude": 4.8874354
    },
    "Media": {
        "HoofdfotoUrl": "https://cloud.funda.nl/valentina_media/217/267/106_360.jpg",
        "Photos": [
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/106_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/106_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/109_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/109_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/111_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/111_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/113_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/113_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/116_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/116_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/119_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/119_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/122_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/122_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/126_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/126_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/128_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/128_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/131_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/131_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/134_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/134_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/137_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/137_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/140_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/140_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/141_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/141_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/142_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/142_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/143_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/143_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/144_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/144_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/145_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/145_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/146_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/146_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/147_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/147_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/148_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/148_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/149_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/149_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/150_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/150_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/151_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/151_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/152_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/152_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/153_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/153_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/154_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/154_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/155_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/155_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/156_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/156_360.jpg"
            },
            {
                "PhotoUrl": "https://cloud.funda.nl/valentina_media/217/267/157_360.jpg",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/157_360.jpg"
            }
        ],
        "Photos360": [
            {
                "Name": "Achtertuin",
                "Url": "https://www.funda.nl/koop/amsterdam/appartement-43162354-van-hilligaertstraat-16-a/360-foto-achtertuin1/",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/362_groot.jpg?bypass_transform=true"
            },
            {
                "Name": "Achtertuin",
                "Url": "https://www.funda.nl/koop/amsterdam/appartement-43162354-van-hilligaertstraat-16-a/360-foto-achtertuin2/",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/361_groot.jpg?bypass_transform=true"
            },
            {
                "Name": "Woonkamer",
                "Url": "https://www.funda.nl/koop/amsterdam/appartement-43162354-van-hilligaertstraat-16-a/360-foto-woonkamer/",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/366_groot.jpg?bypass_transform=true"
            },
            {
                "Name": "Slaapkamer",
                "Url": "https://www.funda.nl/koop/amsterdam/appartement-43162354-van-hilligaertstraat-16-a/360-foto-slaapkamer1/",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/365_groot.jpg?bypass_transform=true"
            },
            {
                "Name": "Slaapkamer",
                "Url": "https://www.funda.nl/koop/amsterdam/appartement-43162354-van-hilligaertstraat-16-a/360-foto-slaapkamer2/",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/364_groot.jpg?bypass_transform=true"
            },
            {
                "Name": "Slaapkamer",
                "Url": "https://www.funda.nl/koop/amsterdam/appartement-43162354-van-hilligaertstraat-16-a/360-foto-slaapkamer3/",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/363_groot.jpg?bypass_transform=true"
            },
            {
                "Name": "Keuken",
                "Url": "https://www.funda.nl/koop/amsterdam/appartement-43162354-van-hilligaertstraat-16-a/360-foto-keuken/",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/368_groot.jpg?bypass_transform=true"
            },
            {
                "Name": "Hal",
                "Url": "https://www.funda.nl/koop/amsterdam/appartement-43162354-van-hilligaertstraat-16-a/360-foto-hal/",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/367_groot.jpg?bypass_transform=true"
            },
            {
                "Name": "Badkamer",
                "Url": "https://www.funda.nl/koop/amsterdam/appartement-43162354-van-hilligaertstraat-16-a/360-foto-badkamer/",
                "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/369_groot.jpg?bypass_transform=true"
            }
        ],
        "FloorPlan": {
            "ProjectId": "176942233",
            "Floors": [
                {
                    "Id": 271698865,
                    "Title": "Begane_grond",
                    "ThumbnailUrl": "https://cloud.funda.nl/valentina_media/217/267/371_plattegrond_600_450.png"
                }
            ]
        }
    },
    "Advertising": {
        "OfferType": "Koop",
        "ContentUrl": "https://www.funda.nl/koop/amsterdam/appartement-43162354-van-hilligaertstraat-16-a/",
        "TargetingOptions": {
            "taal": "nl",
            "cat42": "0",
            "soortaanbod": "koop",
            "globalid": "7717991",
            "cat1": "noordholland",
            "cat2": "amsterdam",
            "cat3": "1072jz",
            "cat4": "16",
            "cat5": "a",
            "cat6": "495000",
            "cat7": "appartement",
            "cat8": "3",
            "cat9": "59",
            "cat10": "d",
            "cat11": "24818",
            "cat13": "bestaandebouw",
            "cat20": "nieuw",
            "cat24": "0",
            "cat26": "nvm",
            "cat28": "1",
            "cat29": "1922",
            "cat30": "450000",
            "cat33": "koop",
            "cat35": "43162354",
            "cat36": "0",
            "cat38": "gemeenteamsterdam",
            "cat39": "cornelistroostbuurt",
            "cat43": "2",
            "cat44": "0",
            "postcode": "1072jz",
            "plaats": "amsterdam",
            "provincie": "noordholland",
            "huisnummer": "16",
            "huisnummertoevoeging": "a",
            "woonoppervlakte": "59",
            "vraagprijs": "495000",
            "aantalkamers": "3",
            "soortobject": "appartement",
            "energieklasse": "d",
            "hoofdaanbieder": "24818",
            "bouwvorm": "bestaandebouw",
            "bedrijfsruimte": "false",
            "cvketel": "nieuw",
            "branchevereniging": "nvm",
            "dakterras": "false",
            "tuin": "true",
            "balkon": "false",
            "soortaanbieding": "koop",
            "tinyid": "43162354",
            "vraagprijsrange": "450000",
            "bouwjaar": "1922",
            "openhuis": "false",
            "gemeente": "amsterdam",
            "buurt": "cornelistroostbuurt",
            "monumentalestatus": "true",
            "rijksmonument": "false",
            "energiezuinig": "false",
            "kluswoning": "false",
            "adgroup": "b",
            "status": "beschikbaar",
            "zonnepanelen": "false",
            "parkeergelegenheidopafgeslotenterrein": "false",
            "parkeergelegenheidopeigenterrein": "false",
            "warmtepomp": "false"
        }
    },
    "Tracking": {
        "Values": {
            "global_id": 7717991,
            "listing_askingprice": 495000,
            "listing_offering_type": "koop",
            "listing_type": "apartment",
            "listing_status": "beschikbaar",
            "listing_postal_code": "1072JZ",
            "listing_house_no": 16,
            "listing_house_no_addition": "A",
            "listing_place": "Amsterdam",
            "brokers": [
                {
                    "broker_id": 24818,
                    "broker_association": "nvm"
                }
            ],
            "broker_id": 24818,
            "broker_association": "nvm",
            "main_broker_id": 24818,
            "main_broker_association": "nvm"
        }
    },
    "Promo": {
        "Blikvanger": {
            "Text": "Benedenhuis in Zuid: Zonnige tuin - 2 Slaapkamers - Schuur met stroom"
        }
    },
    "FastView": {
        "LivingArea": "59 m²",
        "PlotArea": "",
        "NumberOfBedrooms": "2",
        "EnergyLabel": "D"
    },
    "Share": {
        "Url": "https://www.funda.nl/detail/43162354",
        "Text": "Gevonden via #funda"
    },
    "IsSoldOrRented": false,
    "basicInfo": {
        "_index": "search-listings-prod-20250903133449",
        "_id": "7717991",
        "_score": null,
        "_source": {
            "agent": [
                {
                    "logo_type": "new",
                    "relative_url": "/makelaar/24818-edens-makelaardij/",
                    "is_primary": true,
                    "logo_id": 169314558,
                    "name": "Edens Makelaardij",
                    "association": "NVM",
                    "id": 24818
                }
            ],
            "number_of_bedrooms": 2,
            "address": {
                "country": "NL",
                "province": "Noord-Holland",
                "wijk": "Nieuwe Pijp",
                "city": "Amsterdam",
                "neighbourhood": "Cornelis Troostbuurt",
                "identifiers": [
                    "nl",
                    "amsterdam/straat-van-hilligaertstraat",
                    "amsterdam",
                    "gemeente-amsterdam",
                    "provincie-noord-holland",
                    "amsterdam/cornelis-troostbuurt",
                    "amsterdam/wijk-nieuwe-pijp",
                    "1072jz",
                    "1072",
                    "regio-groot-amsterdam"
                ],
                "house_number_suffix": "A",
                "municipality": "Amsterdam",
                "is_bag_address": true,
                "house_number": "16",
                "postal_code": "1072JZ",
                "street_name": "Van Hilligaertstraat"
            },
            "plot_area_range": {
                "gte": 0,
                "lte": 0
            },
            "blikvanger": {
                "text": "Benedenhuis in Zuid: Zonnige tuin - 2 Slaapkamers - Schuur met stroom",
                "enabled": true
            },
            "object_type": "apartment",
            "energy_label": "D",
            "floor_area": [
                59
            ],
            "floor_area_range": {
                "gte": 59,
                "lte": 59
            },
            "type": "single",
            "construction_type": "resale",
            "thumbnail_id": [
                217267106,
                217267109,
                217267111,
                217267113,
                217267116,
                217267119,
                217267122,
                217267126,
                217267128,
                217267131,
                217267134,
                217267137,
                217267140,
                217267141,
                217267142,
                217267143,
                217267144,
                217267145,
                217267146,
                217267147,
                217267148,
                217267149,
                217267150,
                217267151,
                217267152,
                217267153,
                217267154,
                217267155,
                217267156,
                217267157
            ],
            "offering_type": [
                "buy"
            ],
            "price": {
                "selling_price": [
                    495000
                ],
                "selling_price_range": {
                    "gte": 495000,
                    "lte": 495000
                },
                "selling_price_type": "regular",
                "selling_price_condition": "kosten_koper"
            },
            "zoning": "residential",
            "plot_area": [
                0
            ],
            "id": 7717991,
            "available_media_types": [
                "floor_plan",
                "photo_360",
                "video"
            ],
            "publish_date": "2025-09-17T00:15:01.9130000+02:00",
            "object_detail_page_relative_url": "/detail/koop/amsterdam/appartement-van-hilligaertstraat-16-a/43162354/",
            "status": "none",
            "number_of_rooms": 3
        },
        "sort": [
            "basis",
            59000,
            7717991
        ]
    }
}
```

### Property Details

- **_id**: Unique identifier for the listing (e.g., `7717991`).
- **Price**: Property pricing information:

- **SellingPrice**: Formatted price (e.g., `€ 495.000 k.k.`).
- **NumericSellingPrice**: Numeric price (e.g., `495000`).
- **IsAuction**: Boolean indicating if the property is sold via auction.
- **KenmerkSections**: Detailed property characteristics organized in sections:

- **Overdracht**: Transfer details (price, status, offered since, VvE contribution).
- **Bouw**: Construction details (type, year, roof type, special features).
- **Afmetingen**: Dimensions and volume (living area, storage area, total volume).
- **Indeling**: Layout (rooms, bathrooms, facilities, floor location).
- **Energie**: Energy features (energy label, heating type, insulation, CV boiler details).
- **Kadastrale gegevens**: Ownership and cadastral details (leasehold information).
- **Buitenruimte**: Outdoor space (garden size, orientation, location type).
- **Bergruimte**: Storage space type and facilities.
- **Parkeergelegenheid**: Parking availability and type.
- **VvE checklist**: Homeowners association details (if applicable).
- **Labels**: Property status labels (e.g., "Nieuw" for new listings).
- **Aanbiedingstekst**: Full property description text.
- **AddressTitle**: Full property address (e.g., `Van Hilligaertstraat 16-A`).
- **AddressSubTitle**: Postal code and city (e.g., `1072 JZ Amsterdam`).
- **NumberOfBedrooms**: Number of bedrooms as string.
- **BuurtName**: Neighborhood name (e.g., `Cornelis Troostbuurt`).
- **BuurtIdentifier**: Neighborhood URL identifier.

### Basic Search Information

- **basicInfo**: Contains the original search result data:

- **_id**: Property ID from search results.
- **_source**: Original search data including:

- **agent**: Real estate agent information (name, association, logo).
- **address**: Complete address details with identifiers.
- **number_of_bedrooms**: Number of bedrooms.
- **object_type**: Property type (e.g., `apartment`).
- **energy_label**: Energy efficiency rating.
- **floor_area**: Living area in square meters.
- **price**: Price information from search results.
- **publish_date**: When the listing was published.
- **object_detail_page_relative_url**: Link to detailed property page.

### Coordinates

- **Coordinates**:

- **Latitude**: Latitude of the property.
- **Longitude**: Longitude of the property.

### Neighborhood Insights 🆕

When `includeNeighborhoodData` is enabled, each property listing includes a `NeighborhoodInsights` object with comprehensive area statistics:

```
"NeighborhoodInsights": {
    "city": "Rotterdam",
    "neighbourhood": "Het Lage Land",
    "inhabitants": 11400,
    "familiesWithChildren": 21.79,
    "averageAskingPricePerM2": 3782
}
```

**Field Descriptions:**

- **city** (string): Official city name for the neighborhood
- **neighbourhood** (string): Official neighborhood name as recognized by Funda
- **inhabitants** (number): Total population living in the neighborhood
- **familiesWithChildren** (number): Percentage of households with children (e.g., 21.79 = 21.79%)
- **averageAskingPricePerM2** (number): Average asking price per square meter in euros for properties in this neighborhood

**Example Use Cases:**

1. **Price Comparison**: Compare a property's price per m² against the neighborhood average to identify good deals
2. **Demographic Analysis**: Filter properties by family-friendly neighborhoods (high `familiesWithChildren` percentage)
3. **Market Research**: Analyze price trends across different neighborhoods in a city
4. **Investment Decisions**: Identify up-and-coming areas by comparing inhabitant density and pricing

**Data Availability:**

- Neighborhood data is sourced from Funda's Market Insights API
- Coverage includes most major cities and neighborhoods in the Netherlands
- If data is unavailable for a specific neighborhood, the `NeighborhoodInsights` field will be omitted
- Data is updated regularly by Funda based on market activity

### Media and Additional Information

- **Media**: Rich media content for the property:

- **HoofdfotoUrl**: URL of the main property photo.
- **Photos**: Array of property images with thumbnail and full-size URLs.
- **Photos360**: Interactive 360-degree photos with room names and URLs.
- **FloorPlan**: Floor plan details:

- **ProjectId**: Unique project ID.
- **Floors**: Array of floor plans with IDs, titles, and thumbnail URLs.
- **Advertising**: Marketing and targeting information for the listing.
- **Tracking**: Analytics data including broker information and property characteristics.
- **Promo**: Promotional content like "Blikvanger" (eye-catcher) text.
- **FastView**: Quick overview data (living area, plot area, bedrooms, energy label).
- **Share**: Social sharing information (URL and text).
- **IsSoldOrRented**: Boolean indicating if the property is still available.

---

## Benefits of the Funda.nl Scraper

- **Automates Data Collection**: Saves hours of manual effort by automatically gathering real estate data.
- **Detailed Property Insights**: Provides comprehensive information about each listing for informed decision-making.
- **Neighborhood Context** 🆕: Enriches properties with demographic and market statistics for better investment analysis.
- **Customizable Settings**: Allows targeted scraping for specific cities or all of the Netherlands.
- **Proxy Support**: Ensures anonymity and uninterrupted data collection.
- **Data Deduplication**: Prevents duplicate entries with persistent storage options.

---

## Why Choose the Funda.nl Scraper?

- **Comprehensive Data**: Covers every field listed on Funda.nl for in-depth analysis.
- **Market Intelligence** 🆕: Optional neighborhood insights provide demographic and pricing context for smarter decisions.
- **High-Quality Media**: Fetch multimedia assets including photos, 360° views, and floor plans for presentations or reports.
- **Versatility**: Perfect for market research, investment analysis, demographic studies, or property tracking.
- **Reliable**: Robust proxy support and retry mechanisms ensure uninterrupted and anonymous scraping.
- **Production-Ready**: Battle-tested with automatic error handling and performance optimization.

---

## Explore More Scrapers

If you found this Apify Smartbuyglasses Scraper useful, be sure to check out our other powerful scrapers and actors at [memo23's Apify profile](https://apify.com/memo23). We offer a wide range of tools to enhance your web scraping and automation needs across various platforms and use cases.

## Support

- For issues or feature requests, please use the [Issues](https://console.apify.com/actors/isEqQn5XKtr3D3fRW/issues) section of this actor.
- If you need customization or have questions, feel free to contact the author:

- Author's website: [https://muhamed-didovic.github.io/](https://muhamed-didovic.github.io/)
- Email: [muhamed.didovic@gmail.com](mailto:muhamed.didovic@gmail.com)

## Additional Services

- Request customization or whole dataset: [muhamed.didovic@gmail.com](mailto:muhamed.didovic@gmail.com)
- If you need anything else scraped, or this actor customized, email: [muhamed.didovic@gmail.com](mailto:muhamed.didovic@gmail.com)
- For API services of this scraper (no Apify fee, just usage fee for the API), contact: [muhamed.didovic@gmail.com](mailto:muhamed.didovic@gmail.com)
- Email: [muhamed.didovic@gmail.com](mailto:muhamed.didovic@gmail.com)