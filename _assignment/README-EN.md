# Mobile App Development — Assignment 2026-27

[Ελληνική έκδοση](README.md)

## Purpose

The purpose of the assignment is to build a cross-platform mobile app that finds points of interest (POIs) around the user and presents them with content from Wikimedia's open data (Wikidata, Wikipedia, Wikimedia Commons).

Unlike previous years, the app **has no backend of its own**. All data is fetched directly from the device through Wikimedia's public APIs and cached on the device. Users sign in with their Wikimedia account through OAuth 2.0.

The app includes gamification: the user "collects" POIs by visiting them, answers quizzes generated automatically from Wikidata's data and, optionally, contributes to the Wikimedia projects by filling in what is missing.

## Procedure

- The assignment is **mandatory**, counts for **50%** of the final grade and is a **team project (up to 2 people)**. Only students beyond their 4th year may submit individually.
- The code is hosted in a **private GitHub repo**. In team projects, the number of commits per member is a criterion of contribution.
- The app must run through **Expo Go** on Android and iOS devices and be fully functional during the examination. No RESTful API, Docker or Swagger is required.
- During the examination you will be asked to demonstrate the app, explain how you implemented selected features and implement a small change in a short time (e.g. "add a new category" or "make a quiz question from P84"), as an oral examination.

## Supporting labs

Each part of the assignment builds on a lab of the course. You can (and should) start from the labs' code.

| Lab | What it covers for the assignment |
|---|---|
| [Lab 04](../lab04/README-EN.md) | Components, lists, `fetch`, loading / error states |
| [Lab 05](../lab05/README-EN.md) | React Navigation (stack, tabs), Context & `useReducer` |
| [Lab 06](../lab06/README-EN.md) | User location, map, pins, distance, check-in ≤100 m, location simulation |
| [Lab 07](../lab07/README-EN.md) | Queries A & B (SPARQL), `wbgetentities`, Wikipedia, Commons, User-Agent, HTTP 429, cache, offline |
| [Lab 08](../lab08/README-EN.md) | Sign in with Wikimedia (OAuth 2.0 + PKCE), secure token storage & refresh, progress sync |

## Assignment Scope

You are asked to build an app in **React Native (Expo)** that communicates directly with the Wikimedia APIs. The minimum technical requirements are as follows.

### Authentication with Wikimedia OAuth 2.0

The user signs in with their Wikimedia account (**Authorization Code flow with PKCE, no client secret**).

- Each team registers **its own** app (OAuth 2.0 consumer) at [meta.wikimedia.org](https://meta.wikimedia.org/wiki/Special:OAuthConsumerRegistration/propose/oauth2): **non-confidential** (Client is confidential: no), **not** owner-only, with **minimal** rights. Instructions: [Lab 08, Exercise 1](../lab08/README-EN.md).
- The course's **bridge page** is registered as the callback URL, `https://riggas-ionio.github.io/mobile-apps/oauth/`, so that sign-in works from Expo Go.
- Endpoints:
  - Authorize: `https://meta.wikimedia.org/w/rest.php/oauth2/authorize`
  - Token: `https://meta.wikimedia.org/w/rest.php/oauth2/access_token`
  - User info: `https://meta.wikimedia.org/w/rest.php/oauth2/resource/profile`
- Tokens are stored securely (`expo-secure-store`) and refreshed with the refresh token when they expire.
- Browsing POIs is also allowed for **guests**. Signing in is required for the profile, progress sync and contribution missions.

### User location

The app asks for location permission (`expo-location`). If the user doesn't grant it, they can pick a point on the map (default: the centre of Corfu town). The app **always** shows on screen whether the location comes from GPS or is picked / simulated.

### Cache and respect for the infrastructure

- Every request (and every image) sends a **`User-Agent`** header (and `Api-User-Agent`) that identifies the app and the team, e.g. `IonioPOI-team07/1.0 (https://github.com/…)`. Without it Wikimedia answers HTTP 403 (e.g. for images on Android).
- Results are stored locally (`AsyncStorage` or `expo-sqlite`) keyed by "location cell + radius + category" (lat/lon rounded to 2 decimals) with a **24-hour TTL**.
- Repeated queries on every move of the map or the location are not allowed: a new query happens only when the cell changes.
- The app shows the stored POIs **without a connection** too (offline), with an indicator.

### Required screens

The app must provide at least the following six screens.

1. **Sign-in and team.** A "Sign in with Wikimedia" button, a "Continue as guest" option and, below them, the full names and student IDs of the team members.
2. **Home: "What would you like to see nearby?"** The user's location, a radius choice (1 / 3 / 5 km) and 4–6 category buttons with their number of POIs. Buttons appear only for categories with results (Query A).
3. **Category map.** A full-screen map with the POIs (Query B) and a list sorted by distance. Pins have a different colour for collected, not collected and "needs a photo". Tapping a pin shows a preview (tooltip or card) with the title, a small photo and the distance.
4. **Full POI view** (`wbgetentities`), with:
   - a slider of ≥3 photos, the first from P18 (the rest from the Commons category, P373), where available
   - the category as a tag, title, short description and coordinates from Wikidata
   - longer text from Wikipedia (REST summary) and a link to the article
   - an "I'm here" (check-in) button, enabled only within ≤100 m
5. **Quiz.** One to three multiple-choice questions after the check-in, with immediate feedback and points.
6. **Profile.** Collections per category, badges, total points and active missions.

### Map

Use `react-native-maps` (it works in Expo Go without an API key) or, alternatively, Leaflet + OpenStreetMap in a `WebView` ([Lab 06](../lab06/README-EN.md)). If you build your own APK with `react-native-maps`, you will need your own Google Maps API key — but the demo is done in Expo Go.

## POI categories and Wikidata queries

### Where the data comes from: SPARQL or HTTP API?

Both, for different jobs. Both are HTTP calls made with `fetch` that return JSON. **SPARQL** queries to the Wikidata Query Service (`https://query.wikidata.org/sparql?query=…&format=json`) are used for **discovery**: which POIs exist around the location, per category and including subclasses. Wikimedia's **HTTP APIs** are used for the **details** of one POI. SPARQL is slower and has stricter limits (HTTP 429), so it is called once per location cell and category and the result is cached.

| What the app needs | Where from | Screen |
|---|---|---|
| POIs around me per category, and counts per category | **SPARQL** (Queries A and B) | Home, Map |
| Everything about one POI (labels, statements, sitelinks) | Wikidata Action API, `wbgetentities` | POI |
| Article text | Wikipedia REST, `page/summary` | POI |
| Photos, author and licence | Commons: `Special:FilePath`, `prop=imageinfo` | POI, preview |
| Wrong quiz answers from nearby POIs | **SPARQL** (like the P571 example) | Quiz |
| Sign-in and user details | OAuth REST on meta.wikimedia.org | Sign-in, Profile |

Discovery can also be done without SPARQL, with the Action API (see the end of this section).

POIs are not fixed, but are fetched dynamically around the user's location. Each team picks **4–6 categories**. Indicative classes (with the count within 3 km of Corfu town centre, November 2026):

| Button | Wikidata class | Corfu, 3 km |
|---|---|---|
| Monuments | `wd:Q4989906` (monument) | 35 |
| Churches | `wd:Q16970` (church building) | 31 |
| Museums | `wd:Q33506` (museum) | 13 |
| Archaeological sites | `wd:Q839954` (archaeological site) | 7 |
| Squares | `wd:Q174782` (square) | 6 |
| Fortifications | `wd:Q57821` (fortification) | 4 |

`wdt:P31/wdt:P279*` also catches subclasses (e.g. an archaeological museum counts as a museum). The "castle" class (`wd:Q23413`) gives 0 in Corfu — the Old Fortress is a fortification. In WKT the order is `Point(lon lat)` and the radius is given in km.

### Query A: number of POIs per category (home screen)

```sparql
SELECT ?cat (COUNT(DISTINCT ?item) AS ?n) WHERE {
  VALUES ?cat { wd:Q33506 wd:Q16970 wd:Q4989906 wd:Q174782 wd:Q57821 wd:Q839954 }
  SERVICE wikibase:around {
    ?item wdt:P625 ?coord .
    bd:serviceParam wikibase:center "Point(19.92 39.62)"^^geo:wktLiteral ;
                    wikibase:radius "3" .
  }
  ?item wdt:P31/wdt:P279* ?cat .
}
GROUP BY ?cat
ORDER BY DESC(?n)
```

### Query B: the POIs of a category (map)

```sparql
SELECT DISTINCT ?item ?label ?desc ?coord ?dist ?image ?elwiki ?enwiki WHERE {
  SERVICE wikibase:around {
    ?item wdt:P625 ?coord .
    bd:serviceParam wikibase:center "Point(19.92 39.62)"^^geo:wktLiteral ;
                    wikibase:radius "3" ;
                    wikibase:distance ?dist .
  }
  ?item wdt:P31/wdt:P279* wd:Q33506 .
  OPTIONAL { ?item wdt:P18 ?image }
  OPTIONAL { ?elwiki schema:about ?item ; schema:isPartOf <https://el.wikipedia.org/> }
  OPTIONAL { ?enwiki schema:about ?item ; schema:isPartOf <https://en.wikipedia.org/> }
  SERVICE wikibase:label {
    bd:serviceParam wikibase:language "en,el,mul" .
    ?item rdfs:label ?label ; schema:description ?desc .
  }
}
ORDER BY ?dist
LIMIT 50
```

An item with two photos or two coordinates returns **two rows** — keep one POI per Q-ID.

### Wikidata Query Service backend change

The Wikidata Query Service is moving to a new engine (QLever), which does not support `wikibase:around` and `wikibase:label`. The app must keep the **endpoint and query format in one place** in the code (e.g. `api/wikidata.js`), so that the change can be made without touching the screens. The v2 form of Query B (only for the new endpoint — on the current one it is very slow):

```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX schema: <http://schema.org/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX geof: <http://www.opengis.net/def/function/geosparql/>

SELECT DISTINCT ?item ?label ?desc ?coord ?dist ?image ?enwiki WHERE {
  ?item wdt:P625 ?coord .
  BIND(geof:distance("Point(19.92 39.62)"^^geo:wktLiteral, ?coord) AS ?dist)
  FILTER(?dist <= 3)
  ?item wdt:P31/wdt:P279* wd:Q33506 .
  OPTIONAL { ?item rdfs:label ?lEn  FILTER(LANG(?lEn)  = "en") }
  OPTIONAL { ?item rdfs:label ?lEl  FILTER(LANG(?lEl)  = "el") }
  OPTIONAL { ?item rdfs:label ?lMul FILTER(LANG(?lMul) = "mul") }
  BIND(COALESCE(?lEn, ?lEl, ?lMul) AS ?label)
  OPTIONAL { ?item schema:description ?desc FILTER(LANG(?desc) = "en") }
  OPTIONAL { ?item wdt:P18 ?image }
  OPTIONAL { ?enwiki schema:about ?item ; schema:isPartOf <https://en.wikipedia.org/> }
}
ORDER BY ?dist
LIMIT 50
```

### POI details, text and images

- Item data: `https://www.wikidata.org/w/api.php?action=wbgetentities&ids=${id}&props=labels|descriptions|claims|sitelinks&languages=en|el&format=json`
- Article summary: `https://en.wikipedia.org/api/rest_v1/page/summary/${title}`
- Image from P18: `https://commons.wikimedia.org/wiki/Special:FilePath/${photoname}?width=1000`. It replaces last year's `/thumb/4/4a/…` style URL, because the `4/4a` path differs per file (the MD5 of the name).
- Photo author & licence: `https://commons.wikimedia.org/w/api.php?action=query&prop=imageinfo&iiprop=url|extmetadata&titles=File:…`

Alternatively, teams can use the Action API without SPARQL: `list=geosearch` on `wikidata.org` (`gscoord=39.62|19.92&gsradius=3000&gslimit=100`), then `wbgetentities` in batches of 50 ids and filtering by P31 in code. In that case subclasses are not caught, so each category needs a list of Q-IDs.

## Gamification

Levels 1 and 2 are mandatory. Progress sync and Level 3 are optional and give a bonus.

| Level | What the user does | Points (indicative) | Where they are stored |
|---|---|---|---|
| 1. Explorer | Check-in at a POI (≤100 m) | 10 per POI | Locally |
| 2. Quiz | Correct answer after the check-in | 5 per question | Locally |
| Bonus: Sync | Progress in the user's account | — | `action=options` (`userjs-…`) |
| 3. Contribution missions (bonus) | A photo of a POI without P18, uploaded | 30 per file | On the wikis themselves (verifiable) |

### Level 1: Explorer

- Check-in is allowed only when the distance from P625 is ≤100 m (haversine formula in code).
- A collected POI becomes a "card" with its P18 photo, and cards are grouped into collections per category.
- Each team defines at least **3 badges** (e.g. "First museum", "5 squares", "3 categories in one day").
- Progress is stored on the device and is **not lost** when the app closes.

### Bonus: Progress sync

Progress is synced to the user's account with `action=options` (key `userjs-<app>`, a small JSON), so it is kept on another device without a backend ([Lab 08, Exercise 7](../lab08/README-EN.md)). It needs the `editmyoptions` right, so the consumer needs approval by the OAuth admins — until then it works for the account that created it, which is enough for the demo.

### Level 2: Quizzes from linked data

After the check-in the app automatically generates a question from one of the item's statements. The correct answer is the POI's value and the wrong answers come from nearby POIs. At least **2 question types** are required, from the following properties:

- **P571** (inception / year built): "When was it built?"
- **P84** (architect): "Who designed it?"
- **P138** (named after): "Whom does the name honour?"
- **P1435** (heritage designation): "How is it protected?"

Example query for year questions:

```sparql
SELECT ?item ?label ?year WHERE {
  SERVICE wikibase:around {
    ?item wdt:P625 ?c .
    bd:serviceParam wikibase:center "Point(19.92 39.62)"^^geo:wktLiteral ;
                    wikibase:radius "3" .
  }
  ?item wdt:P571 ?inception .
  BIND(YEAR(?inception) AS ?year)
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en,el,mul" .
                           ?item rdfs:label ?label }
}
LIMIT 30
```

If the POI has none of the properties, the app shows no quiz and tells the user that data is missing on Wikidata.

### Level 3: Contribution missions (optional, bonus)

- The app shows nearby items without a photo: in Query B, `OPTIONAL { ?item wdt:P18 ?image }` becomes `FILTER NOT EXISTS { ?item wdt:P18 [] }`.
- The user takes a photo on site and uploads it with their OAuth token (`action=upload`), under a CC BY-SA 4.0 licence and with a caption the user fills in.
- During development and the demo, uploads and edits are made **only on the test wikis** (test.wikipedia.org, test.wikidata.org).
- Level 3 points are computed from the actual contributions (`list=allimages&aiuser=…` or `list=usercontribs`); they are not stored on the device.

## Good practices towards the Wikimedia community

Your app uses shared infrastructure and content created by volunteers. The following rules are **mandatory** and are checked during grading.

- **Respect for the infrastructure**: a User-Agent that identifies the app, a cache, no parallel queries to WDQS, correct handling of HTTP 429 (waiting as long as `Retry-After` says).
- **Attribution and licences**: every photo shows its author and licence (from `prop=imageinfo&iiprop=extmetadata` on Commons) and Wikipedia text comes with a link and the CC BY-SA licence.
- **No edits to the real wikis without an explicit user action**: Level 3 asks for confirmation before every upload, and only the test wikis are used during development.
- **Don't alter the data to make the app "work"**: if a POI has wrong or incomplete data, the app handles it quietly. Corrections on Wikidata are made only if documented, with a source.
- **Privacy**: the user's location is sent nowhere except the queries to Wikimedia (rounded to 2 decimals), and no route history is stored.
- **Secrets**: no token, password or client secret in the code or on GitHub.

## Grading criteria

| Criterion | Points |
|---|---|
| OAuth 2.0 PKCE authentication, secure storage and token refresh | 15 |
| User location and dynamic category buttons (Query A) | 10 |
| Map, pins and preview (Query B) | 15 |
| Full POI view (slider, text, attribution) | 15 |
| Cache, offline operation, User-Agent and error handling | 10 |
| Level 1: check-in, collections, badges | 15 |
| Level 2: quizzes from at least 2 properties | 10 |
| Code quality, commits, README | 10 |
| **Total** | **100** |
| Bonus: progress sync with `action=options` | +5 |
| Bonus: Level 3 (contribution missions on the test wikis) | +10 |

The oral examination and the code change during the presentation act as a multiplier of individual contribution.

## Final deliverables and dates

The presentation takes place in person in the last week of classes (for students in their 4th year or beyond, on the exam day). During the demo, the check-in may be done with a **simulated location**, as long as the app shows on screen that the location is simulated. Before the presentation, all teams submit on opencourses:

1. A **README.md** with the full names and student IDs of the members, the categories they chose with their Q-IDs, the app's User-Agent, the OAuth consumer's client ID and the gamification rules (points, badges).
2. A **recorded demo** (screen capture with voice over) as an MS Stream link, with access rights for the instructors. The demo covers sign-in, coordinates, check-in (with a real or simulated location), quizzes and offline operation.
3. **All the source code** (not the `node_modules` folder) **in a zip**. The app must start with `npm install` and `npx expo start`, without any other services.
4. **Screenshots from your own devices** (PNG or JPG, in a separate zip): at least one for each of the 6 main screens (Sign-in, Home, Category map with preview, Full POI view, Quiz, Profile), named like `01-login.png`, `02-home.png` etc. The screenshots come from the app **actually running on a phone** (not from the browser, nor Figma mockups), with real Wikimedia data. Each team member takes at least one on their own device; if the team has both iOS and Android, show both. Optionally, add one of offline mode or of an error message.

> **A note on appearance**: the screens described in this assignment define functionality, not style. A **visual identity of your own** will be especially appreciated: colour palette, typography, icons, logo, cards and micro-animations, designed by the team (e.g. in Figma, as in Lab 9). Apps that keep the default look of an AI tool or of the lab material will not stand out; we want to recognise your app from a single screenshot.

The exact dates will be announced together with the exam schedule.

-- Good luck --
