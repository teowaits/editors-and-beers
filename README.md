# &Beer &Coffee &Editors

A single-page conference travel planning tool. Enter a venue or city, set a radius,
and see nearby breweries, specialty coffee shops, and researchers on an interactive map.

**Live:** [teowaits.github.io/editors-and-beers](https://teowaits.github.io/editors-and-beers)

---

## What it does

1. Type a venue or city name — autocomplete resolves it to coordinates via Nominatim
2. Select a search radius from the dropdown (2 / 5 / 10 / 20 / 50 km, default 5 km)
3. Click **Find** — three data layers load in parallel:
   * **🍺 Breweries** — from [Open Brewery DB](https://www.openbrewerydb.org), with
     automatic fallback to OpenStreetMap for international results
   * **☕ Coffee** — specialty cafés and roasters shown by default (🫘); click
     **+ More cafes** to reveal all cafés ranked by listing completeness
   * **🎓 Editors** — upload a CSV of editorial board members; locations are geocoded
     and filtered to your radius
4. Toggle each layer on/off with the pill buttons
5. Hover the **(i)** icons next to the data disclaimers for source notes

---

## Coffee layer

The coffee layer prioritises quality over quantity:

* **Default view** shows only venues explicitly tagged as specialty coffee
  (`specialty_coffee=yes`), micro-roasters, dedicated coffee shops, and coffee
  roasters in OpenStreetMap. These appear with a 🫘 map pin.
* **+ More cafes** reveals all remaining cafés, ranked by how complete their OSM
  listing is (website, opening hours, phone, address). These appear with a ☕ map pin.

If no specialty results are found in your radius, click **+ More cafes** or select
a larger radius.

---

## Editors CSV format

The editors layer is optional. Upload a CSV to add researcher/editor pins to the map.

### Required columns

Only three columns are strictly required:

| Column      | Notes                                              |
|-------------|----------------------------------------------------|
| Editor      | Full name — displayed in the popup                 |
| Country     | Used to validate and constrain geocoding results   |
| Affiliation | Institution name — primary geocoding input         |

**Minimum working CSV:**
```csv
Editor,Country,Affiliation
Jane Smith,Germany,Max Planck Institute
Li Wei,China,Peking University
```

### Optional columns

These columns enhance functionality when present:

| Column  | Notes                                                        |
|---------|--------------------------------------------------------------|
| Journal | Enables the journal filter dropdown when present             |
| Role    | Enables the role filter dropdown (e.g. Associate Editor)     |
| City    | Improves geocoding accuracy for ambiguous institution names  |
| Email   | Used for precise institution matching via email domain hints |
| Website | Shown as a link in the editor popup                          |

Column order does not matter. Column names are case-insensitive. Empty optional
fields are fine — leave them blank or omit the column entirely.

### Geocoding methods

Two methods are available, selectable before uploading:

* **Online** (recommended, default) — queries Nominatim for any institution
  worldwide; approximately 1 second per editor
* **Built-in database** — instant; covers ~200 major research institutions;
  unrecognised institutions fall back to the country centroid

**Tip:** increase the radius to 20–50 km when searching for editors — research
institutions are often outside city centres.

---

## Data sources

| Layer        | Source                                                                                                        |
|--------------|---------------------------------------------------------------------------------------------------------------|
| Breweries    | [Open Brewery DB](https://www.openbrewerydb.org) (US/Canada) · [OpenStreetMap](https://www.openstreetmap.org) via Overpass (international) |
| Coffee       | [OpenStreetMap](https://www.openstreetmap.org) via [Overpass API](https://overpass-api.de)                   |
| Venue search | [Nominatim](https://nominatim.org) / OpenStreetMap                                                           |
| Editors      | User-supplied CSV + Nominatim geocoding                                                                       |

Brewery data is strongest in the US and Canada. Outside North America, results come
from OpenStreetMap and may include pubs and venues serving beer rather than dedicated
breweries. Coffee coverage is generally solid in cities worldwide but varies by region.

---

## Technical notes

* Single `index.html` — no build tools, no server, no dependencies to install
* All computation runs in the browser; nothing is stored or transmitted beyond the
  API calls listed above
* No analytics, no cookies, no persistent storage — refreshing clears all state
* [Leaflet](https://leafletjs.com) for the map, [PapaParse](https://www.papaparse.com)
  for CSV parsing, [MarkerCluster](https://github.com/Leaflet/Leaflet.markercluster)
  for the editors layer only (brewery and coffee markers are not clustered)
* Overpass requests use exponential backoff (up to 3 attempts, 2 s → 4 s) and degrade
  gracefully on persistent server errors

---

## Known limitations

* **Brewery coverage outside US/Canada is qualitatively different.** Open Brewery DB
  focuses on North America. International results fall back to OpenStreetMap, which
  includes generic pubs and bars alongside dedicated breweries. Results are filtered
  to exclude obviously non-brewery types, but some noise is unavoidable.

* **Coffee results depend on OSM tagging quality.** Specialty coffee tags
  (`specialty_coffee=yes`, `craft=coffee_roaster`) are applied inconsistently across
  regions. In some cities the default view returns few or no results even where
  specialty coffee exists — use **+ More cafes** as a fallback.

* **Editor geocoding accuracy varies.** The built-in database covers ~200 major
  research institutions. Any institution not in the database falls back to the country
  centroid, placing the marker in the geographic centre of the country rather than the
  actual city. Online geocoding via Nominatim is more accurate but slower (~1 s/editor).

* **No deduplication of editor markers.** If two editors share the same institution,
  their markers overlap at the same coordinates. MarkerCluster groups them visually —
  click the cluster number to expand.

* **Overpass can be slow on first load.** The Overpass API applies a cold-cache
  penalty on the first query after a period of inactivity. The coffee layer has a
  25-second timeout to accommodate this; a brief loading delay on first use is normal.

---

## Contributing

### Adding institutions to the built-in geocoding database

The built-in database is the `LOCATION_DATABASE` object near the top of `index.html`.
Each entry maps a normalised institution name to coordinates:

```javascript
'university of example': { lat: 51.5074, lon: -0.1278, city: 'London' },
```

To add an institution:
1. Find accurate coordinates (e.g. from Google Maps or OpenStreetMap)
2. Add an entry to `LOCATION_DATABASE` using the institution's common name in
   lowercase (punctuation stripped)
3. If the institution has a distinctive email domain, add a matching entry to
   `EMAIL_DOMAIN_HINTS`:
   ```javascript
   'example.ac.uk': { lat: 51.5074, lon: -0.1278, name: 'University of Example' },
   ```
4. Test with a CSV row using that affiliation
5. Submit a pull request

### Related project

This tool is a companion to
[wherearetheeditors](https://github.com/teowaits/wherearetheeditors), which takes
the opposite approach: the editorial board CSV is the primary input, and location
search is secondary. If you need to visualise a full editorial board globally rather
than find researchers near a specific venue, `wherearetheeditors` is the better
starting point. Both apps share the same geocoding engine and CSV format.

---

## Changelog

### v1.2

* Brewery popups now include a **🍻 Search on Untappd ↗** deep-link — opens a
  pre-filled Untappd search for that brewery in a new tab, no API key required

### v1.1

* Coffee layer redesigned: specialty cafés and roasters shown by default (🫘 pin);
  **+ More cafes** reveals broader results ranked by OSM listing completeness
* (i) tooltips added to data source disclaimers
* Overpass resilience: exponential backoff retry (2 s → 4 s), graceful degradation
  on persistent 504s
* First-search coffee bug fixed (Overpass cold-cache timeout raised to 25 s)
* OSM element deduplication corrected (node/way ID collision)

### v1.0

* Initial release: venue autocomplete, breweries (OBDB + OSM fallback), coffee (OSM),
  editors CSV layer with geocoding pipeline

---

## Credits

Ideated and planned by [teowaits](https://github.com/teowaits), with coding assistance
from [Claude Sonnet 4.6](https://www.anthropic.com/claude) (Anthropic).

---

## License

MIT — see [LICENSE](https://github.com/teowaits/editors-and-beers/blob/main/LICENSE).
