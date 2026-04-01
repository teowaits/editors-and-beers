# &Beer &Coffee &Editors

A single-page conference travel planning tool. Enter a venue or city, set a radius, and see nearby breweries, specialty coffee shops, and researchers on an interactive map.

**Live:** [teowaits.github.io/editors-and-beers](https://teowaits.github.io/editors-and-beers)

---

## What it does

1. Type a venue or city name — autocomplete resolves it to coordinates via Nominatim
2. Set a search radius (default 5 km, up to 50 km)
3. Click **Find** — three data layers load in parallel:
   - **Breweries** — from [Open Brewery DB](https://www.openbrewerydb.org), with automatic fallback to OpenStreetMap for international results
   - **Coffee** — specialty cafés and roasters shown by default (🫘); tap **+ More cafes** to reveal all cafés ranked by listing completeness
   - **Editors** — upload a CSV of editorial board members; locations are geocoded and filtered to your radius
4. Toggle each layer on/off with the pill buttons
5. Hover the **(i)** icons next to the data disclaimers for source notes

---

## Coffee layer

The coffee layer prioritises quality over quantity:

- **Default view** shows only venues explicitly tagged as specialty coffee (`specialty_coffee=yes`), micro-roasters, dedicated coffee shops, and coffee roasters in OpenStreetMap. These appear with a 🫘 map pin.
- **+ More cafes** reveals all remaining cafés, ranked by how complete their OSM listing is (website, opening hours, phone, address). These appear with a ☕ map pin.

If no specialty results are found in your radius, tap **+ More cafes** or increase the radius.

---

## Editors CSV format

Upload a CSV with the following columns:

| Column | Required | Notes |
|---|---|---|
| Editor | Yes | Full name |
| Journal | Yes | |
| Role | Yes | e.g. Associate Editor |
| Country | Yes | Used to resolve institution location |
| Affiliation | Yes | Institution name |
| City | No | Improves geocoding accuracy |
| Email | No | Used for precise institution matching |
| Website | No | Shown in popup |

**Tip:** set the radius to 50 km to find more editors.

Two geocoding methods are available:
- **Online** (recommended) — queries Nominatim for any institution worldwide; ~1 second per editor
- **Built-in database** — instant, covers ~200 major research institutions; others fall back to country centroid

---

## Data sources

| Layer | Source |
|---|---|
| Breweries | [Open Brewery DB](https://www.openbrewerydb.org) (US/Canada) · [OpenStreetMap](https://www.openstreetmap.org) via Overpass (international) |
| Coffee | [OpenStreetMap](https://www.openstreetmap.org) via [Overpass API](https://overpass-api.de) |
| Venue search | [Nominatim](https://nominatim.org) / OpenStreetMap |
| Editors | User-supplied CSV + Nominatim geocoding |

Brewery data is strongest in the US and Canada. Outside North America, results come from OpenStreetMap and may include pubs and venues serving beer. Coffee coverage varies by region.

---

## Technical notes

- Single `index.html` — no build tools, no server, no dependencies to install
- All computation runs in the browser; nothing is stored or transmitted beyond the API calls above
- No analytics, no cookies, no persistent storage — refreshing clears all state
- Leaflet for the map, PapaParse for CSV parsing, MarkerCluster for the editors layer
- Overpass requests use exponential backoff (up to 3 attempts) and degrade gracefully on server errors

---

## Changelog

### v1.2
- Brewery popups now include a **🍻 Search on Untappd ↗** deep-link — opens a pre-filled Untappd search for that brewery in a new tab, no API key required

### v1.1
- Coffee layer redesigned: specialty cafés and roasters shown by default (🫘 pin); **+ More cafes** reveals broader results ranked by OSM listing completeness
- (i) tooltips added to data source disclaimers
- Overpass resilience: exponential backoff retry (2 s → 4 s), graceful degradation on persistent 504s
- First-search coffee bug fixed (Overpass cold-cache timeout raised to 25 s)
- OSM element deduplication corrected (node/way ID collision)

### v1.0
- Initial release: venue autocomplete, breweries (OBDB + OSM fallback), coffee (OSM), editors CSV layer with geocoding pipeline

---

## License

MIT — see [LICENSE](LICENSE).
