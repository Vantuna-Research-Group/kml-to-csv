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
| Shapefile | yes (zipped, or the loose .shp/.dbf/.prj/.cpg files) | yes (zip) | Reprojected using the .prj file. Output in WGS 84 lat/long, UTM 10N/11N (WGS 84 or NAD83), or the same system as the input. Mixed geometry becomes separate point, line and polygon shapefiles. |

## Coordinate systems

Everything is converted to WGS 84 latitude/longitude internally, using [proj4js](https://github.com/proj4js/proj4js) 2.22.0.

- **Shapefiles** are read using their `.prj` file. Without one, latitude/longitude-range numbers are read as WGS 84, and UTM-looking numbers are read as UTM zone 11N, WGS 84 (a note says so).
- **CSV** files with easting/northing columns (`easting`, `northing`, `UTM_E`, `UTM_N`, `x`, `y`) are detected the same way.
- **Source coordinate system** overrides the automatic choice for every shapefile, CSV and GeoJSON input. Presets: WGS 84, UTM 10N/11N (WGS 84 and NAD83), California State Plane zones 5 and 6 (NAD83, US feet), California Albers, Web Mercator. **Other** takes any proj4 string or WKT.
- **Shapefile output** can be written in WGS 84, UTM 10N/11N (WGS 84 or NAD83), or the same system as an input shapefile's `.prj`. The `.prj` uses Esri WKT, so ArcGIS Pro recognises it.
- NAD83 and WGS 84 are treated as identical (no datum grid shift). They differ by about 1 to 2 m in Southern California.

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
- KMZ and zipped-shapefile reading needs a browser from 2023 or later (it uses `DecompressionStream`).
- Shapefile output drops line and polygon altitudes and track times. Points keep altitude in an `altitude` field. Field names are cut to 10 characters and text to 254 bytes; the notes box lists every renamed field.
- Reprojection needs the proj4js library from jsDelivr, so projected files need an internet connection the first time.

## Updating

Everything is in `index.html`: HTML, CSS and JavaScript, with no build step. It loads Leaflet 1.9.4 from cdnjs for the map, proj4js 2.22.0 from jsDelivr for reprojection, and Google Fonts for type. The page still works without them, except that projected coordinates cannot be converted. Edit it, commit to `main`, and GitHub Pages republishes the site within a minute or two.

The code is organised as readers (one per input format) that produce a shared feature list, and writers (one per output format) that consume it. Adding a format means adding one reader and/or one writer.

To test changes locally, open `index.html` in a browser. It loads a small made-up sample of San Diego Bay seine stations so there is always something to look at.
