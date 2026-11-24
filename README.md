# Map file converter

A single-page tool for converting field and GIS map files between formats, built for Vantuna Research Group as Google Earth Pro for desktop is retired (no new downloads after June 25, 2027).

**Open it:** https://vantuna-research-group.github.io/map-converter/

Files are read in your browser and never uploaded. The page also works offline: download `index.html` and open it in Chrome, Edge, Firefox or Safari.

## Formats

| Format | Read | Write | Notes |
|---|---|---|---|
| KML / KMZ | yes | KML | Folders, ExtendedData, TimeStamp/TimeSpan and gx:Track are kept. Original styles are replaced with standard colours. |
| GPX | yes | yes | Waypoints, routes and tracks (multi-segment included). GPX has no polygons or attribute table: polygons become closed tracks and attributes go into each item's `cmt`. |
| GeoJSON | yes | yes | Coordinates must be WGS84 longitude/latitude (RFC 7946). The tool warns when a file looks projected. |
| CSV / TSV | yes | yes | Reads lat/long columns found by name or chosen from lists, decimal degrees or degrees-minutes(-seconds) such as `32°42.69'N`, or a WKT column for lines and polygons. Comma, tab and semicolon delimiters. |
| Shapefile | planned | planned | Next step, with reprojection from UTM, State Plane and other projected systems. |

## Features

- Drop several files at once to combine them. Each feature keeps its `source_file`.
- A map of all features on a satellite, ocean or street basemap (Esri and OpenStreetMap tiles). Without internet the page shows a plain plot instead.
- A preview table of every field. Untick a column heading to leave it out of the export, in any format. Choices are remembered in that browser.
- CSV output options: one row per feature or per vertex, first-vertex or centre coordinates for lines and polygons, a WKT geometry column, Excel-friendly encoding.
- Attribute tables inside KML descriptions (how ArcGIS and other tools export attributes) can be turned into fields.
- `length_m` and `area_m2` are geodesic estimates for lines, tracks and polygons, with holes subtracted from area.
- A notes box lists anything a conversion could not carry over, so nothing is dropped silently.

## Limits

- KML network links, image overlays and 3D models are not converted.
- A CSV exported with one row per vertex re-imports as separate points. Export with the WKT column to round-trip lines and polygons.
- KMZ reading needs a browser from 2023 or later (it uses `DecompressionStream`).

## Updating

Everything is in `index.html`: HTML, CSS and JavaScript, with no build step. It loads Leaflet 1.9.4 from cdnjs for the map and Google Fonts for type; both are optional and the page still works without them. Edit it, commit to `main`, and GitHub Pages republishes the site within a minute or two.

The code is organised as readers (one per input format) that produce a shared feature list, and writers (one per output format) that consume it. Adding a format means adding one reader and/or one writer.

To test changes locally, open `index.html` in a browser. It loads a small made-up sample of San Diego Bay seine stations so there is always something to look at.
