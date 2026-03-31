# &Beer &Coffee &Editors

A single-page conference travel planning tool. Enter a venue or city, set a radius, and see nearby breweries, specialty coffee shops, and researchers on an interactive map.

**Live:** [teowaits.github.io/editors-and-beers](https://teowaits.github.io/editors-and-beers)

---

## What it does

1. Type a venue or city name — autocomplete resolves it to coordinates via Nominatim
2. Set a search radius (default 5 km, up to 50 km)
3. Click **Find** — three data layers load in parallel:
   - **Breweries** — from [Open Brewery DB](https://www.openbrewerydb.org), with automatic fallback to OpenStreetMap for international results
   - **Coffee** — specialty cafes and roasters from OpenStreetMap via Overpass
   - **Editors** — upload a CSV of editorial board members; locations are geocoded and filtered to your radius
4. Toggle each layer on/off with the pill buttons

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

Brewery data is strongest in the US and Canada. Coffee coverage varies by region.

---

## Technical notes

- Single `index.html` — no build tools, no server, no dependencies to install
- All computation runs in the browser; nothing is stored or transmitted beyond the API calls above
- No analytics, no cookies, no persistent storage — refreshing clears all state
- Leaflet for the map, PapaParse for CSV parsing, MarkerCluster for the editors layer

---

## License

MIT — see [LICENSE](LICENSE).
