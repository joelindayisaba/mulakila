# MuLaKiLa Project — Activities Webmap

An interactive web map showing project activities for the MuLaKiLa landscape restoration project in **Ngororero and Rutsiro Districts, Rwanda**. Built with [Leaflet](https://leafletjs.com/) — no server or database required.

**Live demo:** _add your hosted URL here once deployed, e.g. `https://your-username.github.io/mulakila-map/`_

---

## What's on the map

| Layer | Source | Description |
|---|---|---|
| Project area | `cell_boundary` | Administrative cell boundaries covering the project footprint |
| Eligible sites | `best_site boundary` | Sites assessed as eligible for project activities ("BEST sites") |
| Terraces | `terraces_boundary` | Radical/progressive terracing works, by year and type |
| Planted plots | `planted_plots` | Individual planted plot boundaries (47,848 polygons — large layer, loads on demand) |
| Nurseries | `tree_nursery_location` | Established tree nurseries from field survey |
| Field-added nurseries | — | Nurseries added directly on the map by field staff (see below) |

> **Note:** if `cell_boundary`/`best_site boundary` roles turn out to be reversed from what's listed above, swap the two `fetch(...)` lines near the top of the `<script>` block in `index.html` (search for `project_area.geojson` and `eligible.geojson`).

Click any feature to see its attributes in a popup. Use the layer control (top-right) to toggle layers on/off, and switch between street map and satellite basemaps.

## Adding nurseries from the field

Tap **"+ Add Nursery"** (bottom-right) to:
- Drop a pin by tapping the map, or use **"Use my location"** (GPS) on a phone
- Fill in name, village/cell/sector, status, and notes
- Save — it appears as a red pin, stored in that device's browser (`localStorage`)

**Sharing additions between devices:** since this is a static site with no backend, new nursery points don't sync live across devices. Use the **Export** button (bottom-left) to download a small `.geojson` file of a device's new nurseries, share it with the team (email/WhatsApp), and have others **Import** it into their own browser — duplicates are skipped automatically by ID.

If you need real-time shared sync across the whole team, that requires adding a small backend (e.g. a Google Sheet or database as the data store) — this repo doesn't include one, but it's a straightforward addition on request.

## Project structure

```
├── index.html          # the entire app: map, layers, add-nursery tool
├── vendor/
│   └── leaflet/         # Leaflet library, bundled locally (no CDN dependency)
└── data/
    ├── project_area.geojson
    ├── eligible.geojson
    ├── terraces.geojson
    ├── planted_plots.geojson   # ~24MB, lazy-loaded only when toggled on
    └── nurseries.geojson
```

> **Important when uploading to GitHub/any host:** all three — `index.html`, the `vendor` folder, and the `data` folder — must sit at the same level (the repo root). If they end up nested inside an extra folder, or if `vendor`/`data` are missing, the map won't load.

All geometry was converted from the original shapefiles and reprojected to WGS84 (EPSG:4326). `planted_plots` and `terraces` were lightly simplified (≤5m tolerance, imperceptible at normal zoom) to keep the map responsive — the originals had over 1.2 million vertices combined.

## Hosting it

This is a static site — any static host works. Two free options:

**GitHub Pages** (this repo)
1. Settings → Pages → Source: Deploy from a branch → branch `main`, folder `/ (root)` → Save
2. Live in 1–2 minutes at `https://<username>.github.io/<repo-name>/`

**Netlify Drop**
1. Go to https://app.netlify.com/drop
2. Drag the whole project folder onto the page — live instantly

## Running locally

Because the app `fetch()`es the `data/*.geojson` files, opening `index.html` directly (double-click) will fail due to browser local-file security rules. Serve it instead:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Limitations

- **Requires internet to load fully**: Leaflet's library and map tiles (OpenStreetMap/Esri satellite) are loaded from CDNs, and the basemap won't appear offline. Your own data layers are self-contained in the `data/` folder.
- **Planted plots** is a large layer (~24MB) — it's off by default and only downloads when switched on, to keep initial load fast, especially on mobile data.
- **Field-added nurseries** are per-device (browser `localStorage`), not centrally synced — see "Adding nurseries from the field" above.

## Credits

Built for the MuLaKiLa 2026 project. Map library: [Leaflet](https://leafletjs.com/). Basemaps: OpenStreetMap contributors, Esri World Imagery.
