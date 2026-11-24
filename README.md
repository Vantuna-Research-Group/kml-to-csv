# KML to CSV

A single-page converter that turns Google Earth KML and KMZ files into CSV. It was built for Vantuna Research Group field data as Google Earth Pro for desktop is retired (no new downloads after June 25, 2027).

**Open it:** https://vantuna-research-group.github.io/kml-to-csv/

Files are read in your browser and never uploaded. The page also works offline: download `index.html` and open it in Chrome, Edge, Firefox or Safari.

## What it does

- Reads `.kml` and `.kmz` files. Drop several at once to get one combined CSV with a `source_file` column.
- One row per placemark, or one row per vertex for GPS tracks and transects.
- Columns: source file, folder path, name, description, geometry type, latitude, longitude, altitude, vertex count, length (m), area (m²), time stamps, style, and every attribute field.
- Attribute fields come from ExtendedData (`Data` and `SchemaData`). Two-column tables inside descriptions, which ArcGIS and other tools use for attributes, can be turned into columns too.
- Optional WKT geometry column for QGIS or ArcGIS.
- Tick or untick each column heading in the preview to choose what gets exported. Choices are remembered in that browser.
- A map of all features on a satellite, ocean or street basemap (Esri and OpenStreetMap tiles), so you can check nothing was dropped before exporting. Hover a feature for its name. Without internet the page shows a plain plot instead.

## Limits

- Coordinates are written exactly as stored in the KML (decimal degrees, WGS84).
- `length_m` and `area_m2` are geodesic estimates. Polygon holes are not subtracted.
- Network links, image overlays and 3D models are not converted.
- KMZ reading needs a browser from 2023 or later (it uses `DecompressionStream`).

## Updating

Everything is in `index.html`: HTML, CSS and JavaScript, with no build step. It loads Leaflet 1.9.4 from cdnjs for the map and Google Fonts for type; both are optional and the page still works without them. Edit it, commit to `main`, and GitHub Pages republishes the site within a minute or two.

To test changes locally, open `index.html` in a browser. It loads a small made-up sample of San Diego Bay seine stations so there is always something to look at.
