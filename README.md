# Tracks for Micro.blog

Display GPS tracks from GPX files on interactive Leaflet maps — directly in your Micro.blog posts.

Single-track and multi-track modes, 12 basemap styles, elevation profiles, waypoints with rich media, start/finish markers, and a stats bar. Fully self-contained: Leaflet loads on demand, only once per page, with no head partials or footers to wire up. Safe alongside the [Maps plugin](https://github.com/microdotblog/plugin-maps) — they share the same Leaflet loader.

---

## Installation

1. In Micro.blog, go to **Plug-ins → Find Plug-ins** and search for **Tracks**, or install directly from this repository.
2. That's it. No configuration required.

---

## Quick start

**1. Upload your GPX file** via **Posts → Uploads**. Copy the URL (e.g. `/uploads/2025/w-trek-day1.gpx`).

**2. Create a data file** at `data/tracks/w-trek-day1.yml` in your Hugo site (Micro.blog lets you add these under **Design → Edit Custom Themes → New File**):

```yaml
gpx: "/uploads/2025/w-trek-day1.gpx"
name: "W Trek — Day 1"
note: "Grey Glacier to Paine Grande."
```

**3. Add the shortcode** to any post:

```
{{< track "w-trek-day1" >}}
```

A 500 px interactive map appears with a stats bar showing distance, elevation gain/loss, and moving time.

---

## Shortcode parameters

All parameters are optional except `data` (or the positional argument).

| Parameter | Default | Description |
|-----------|---------|-------------|
| `data` | *(required)* | Name of the YAML file in `data/tracks/` (without `.yml`). Can be passed positionally: `{{< track "my-hike" >}}`. |
| `height` | `500px` | Map height. Accepts any CSS value (`400px`, `60vh`, etc.). |
| `basemap` | `osm` | Map tile style. See [Basemaps](#basemaps) below. |
| `color` | `#E11D48` | Track line color. In multi-track mode, forces **all** tracks to this color. |
| `weight` | `4` | Track line width in pixels. |
| `stats` | `yes` | Show the stats bar (`yes` / `no`). No effect in multi-track mode. |
| `elevation` | `no` | Show an SVG elevation profile chart below the stats bar (`yes` / `no`). Single-track only; requires elevation data in the GPX. |
| `waypoints` | — | Name of a waypoint YAML file in `data/tracks/`. Single-track only. |
| `markers` | `yes` | Show start/finish markers at track endpoints (`yes` / `no`). |
| `legend` | `no` | Show a color-swatch legend bar. Multi-track only. Set to `yes` to enable. |
| `units` | `metric` | Distance and elevation units: `metric` (km / m) or `imperial` (mi / ft). |
| `simplify` | `no` | Reduce GPX point density for faster rendering. Use `yes` for the built-in default tolerance (`≈ 5 m`), or a numeric value in degrees (e.g. `0.00003`). Recommended for tracks longer than ~20 km. |

Any parameter set in the shortcode overrides the same field in the data file.

---

## Data files

Data files live in `data/tracks/` and are YAML. Single-track and multi-track formats are detected automatically.

### Single-track

```yaml
# data/tracks/w-trek-day1.yml
gpx:      "/uploads/2025/w-trek-day1.gpx"   # required
name:     "W Trek — Day 1"                   # shown in stats bar and popup
note:     "Grey Glacier to Paine Grande."    # subtitle / popup note
url:      "/2025/05/w-trek-day1"             # makes the track line clickable
color:    "#E11D48"
weight:   4
basemap:  "topo"
stats:    yes
markers:  yes
elevation: no
simplify: no
units:    "metric"
waypoints: "w-trek-waypoints"                # optional — references another YAML file
```

### Multi-track

A top-level YAML list automatically enables multi-track mode. Each entry is one GPX file drawn on the same map.

```yaml
# data/tracks/patagonia-hikes.yml
- name:   "W Trek — Day 1"
  gpx:    "/uploads/2025/w-trek-day1.gpx"
  color:  "#E11D48"
  url:    "/2025/05/w-trek-day1"
  legend_url: "/2025/05/w-trek-day1"   # optional: makes the legend label a link
  note:   "Grey Glacier to Paine Grande. 11 km, 340 m gain."

- name:   "W Trek — Day 2"
  gpx:    "/uploads/2025/w-trek-day2.gpx"
  color:  "#7C3AED"
  url:    "/2025/05/w-trek-day2"
  note:   "Paine Grande to Italiano. 9 km, 180 m gain."

- name:   "Reference: park boundary"
  gpx:    "/uploads/2025/park-boundary.gpx"
  color:  "#94a3b8"
  popup:   no    # no popup — purely visual
  markers: no    # no start/finish markers
```

**Multi-track notes:**
- The stats bar is never shown. Put condensed stats in each track's `note` field.
- Waypoints are not supported per-track (single-track only).
- If `color` is omitted, a built-in palette cycles automatically.
- Passing `color=` in the shortcode forces **all** tracks to that color.
- In multi-track mode, each track highlights when hovered.

---

## Waypoints

Waypoints are defined in a separate YAML file and referenced from the track data file or the shortcode's `waypoints=` parameter.

```yaml
# data/tracks/w-trek-waypoints.yml
- name:  "Refugio Grey"
  lat:   -50.9876
  lon:   -73.1234
  icon:  "⛺"
  color: "SteelBlue"
  url:   "/2025/05/refugio-grey"
  note:  "Night 1. Book 6+ months ahead."

- name:  "Grey Glacier viewpoint"
  lat:   -50.9912
  lon:   -73.1456
  icon:  "pin"
  note:  "Best views before noon."
```

### Waypoint `url` behaviour

The `url` field of a waypoint is smart about media types:

| File type | Behaviour |
|-----------|-----------|
| Image (`.jpg`, `.png`, `.gif`, `.webp`) | Opens in a full-screen lightbox gallery. Multiple image waypoints share one gallery with prev/next navigation. |
| Video (`.mp4`, `.m4v`, `.mov`, `.webm`, `.ogv`, `.m3u8`) | Opens in a video player overlay. HLS streams use hls.js on non-Safari, loaded on demand. |
| Audio (`.mp3`, `.m4a`, `.aac`, `.ogg`, `.oga`, `.wav`, `.flac`) | Embeds an inline `<audio>` player directly in the waypoint popup. |
| Anything else | Opens as a normal link in a new tab. |

### Waypoint icons

| Category | Values |
|----------|--------|
| Generic | `pin`, `pin-small`, `star`, `heart`, `point`, `disc`, `bullseye`, `flag-checkered`, `camera`, `video`, `image`, `gallery`, `mountain`, `binoculars`, `landmark`, `utensils`, `droplet`, `microphone`, `triangle-exclamation` |
| Circles | `circle-heart`, `circle-star`, `circle-camera`, `circle-video`, `circle-image`, `circle-gallery` |
| Numbered | `circle-0` through `circle-9`; `circle-00` through `circle-99` |
| Emoji | Any emoji character rendered as-is, e.g. `"⛺"`, `"📸"`, `"⚠️"` |

The icon inherits the waypoint's `color` field (or the track color as a fallback).

---

## Basemaps

Set with `basemap=` in the shortcode or the data file's `basemap` field.

### General purpose

| Value | Description |
|-------|-------------|
| `osm` | OpenStreetMap — **default**, worldwide |
| `voyager` | Carto Voyager — clean, colourful |
| `positron` | Carto Positron — minimal, lets track colors pop |

### Outdoor & terrain

| Value | Description |
|-------|-------------|
| `topo` | OpenTopoMap — contours, worldwide |
| `cycle` | CyclOSM — bike lanes, surface types, worldwide |
| `usgs-topo` | USGS Topo — detailed quads, **US only** |
| `usgs-hybrid` | USGS Imagery + Topo overlay, **US only** |

### Satellite & imagery

| Value | Description |
|-------|-------------|
| `satellite` | Esri World Imagery |
| `streets` | Esri World Street Map — urban detail |
| `esri-topo` | Esri World Topo |
| `esri-relief` | Esri Shaded Relief — clean hillshade, no clutter |
| `natgeo` | National Geographic style |

### Activity tips

| Activity | Recommended basemaps |
|----------|----------------------|
| Hiking | `topo`, `esri-topo`, `usgs-topo` |
| Cycling | `cycle`, `topo` |
| Running | `voyager`, `streets`, `osm` |
| Off-road / 4WD | `satellite`, `usgs-hybrid`, `esri-relief` |
| Paddling | `topo`, `satellite`, `osm` |
| Scenic driving | `natgeo`, `esri-relief`, `voyager` |
| Skiing | `topo` |

---

## Elevation profiles

Add `elevation="yes"` to the shortcode (or `elevation: yes` to the data file) to render an SVG elevation chart below the stats bar. The profile is color-matched to the track line and includes a hover tooltip showing distance and elevation at each point.

```
{{< track "w-trek-day1" elevation="yes" >}}
```

Elevation profiles are single-track only and require that the GPX file contains `<ele>` data.

---

## Track simplification

GPX files record a point every 1–5 seconds; a long hike can contain 10,000+ coordinates. Simplification uses the Ramer-Douglas-Peucker algorithm to reduce point count by 60–80% with no visible difference at typical zoom levels.

```
{{< track "w-trek-day1" simplify="yes" >}}
```

| Value | Tolerance | Best for |
|-------|-----------|----------|
| `yes` | ≈ 5 m | Most tracks; good all-round default |
| `0.00003` | ≈ 3 m | Tight switchbacks, technical terrain |
| `0.0001` | ≈ 11 m | Very large files, multi-track maps |

Simplification runs after the GPX loads. The source file on disk is unchanged.

---

## GPX files

Export GPX from **Strava**, **Garmin Connect**, **AllTrails**, **Komoot**, **Apple Fitness**, or any fitness / mapping app that supports GPX export.

Upload the file via **Posts → Uploads** in Micro.blog. The GPX must be hosted on the same domain as your blog — cross-origin (CORS) loading is not supported.

---

## Examples

### A hike with a topo basemap and elevation chart

```
{{< track "w-trek-day1" basemap="topo" elevation="yes" >}}
```

### A cycling route in imperial units

```
{{< track "lake-loop" basemap="cycle" units="imperial" >}}
```

### A trip overview with multiple tracks and a legend

```
{{< track "patagonia-hikes" height="600px" legend="yes" >}}
```

### A track with waypoints, some pointing to photos

```yaml
# data/tracks/coast-walk.yml
gpx: "/uploads/2025/coast-walk.gpx"
name: "Coast Walk"
waypoints: "coast-walk-waypoints"
```

```yaml
# data/tracks/coast-walk-waypoints.yml
- name: "Pelican colony"
  lat: -33.8823
  lon: 151.2644
  icon: "📸"
  url: "/uploads/2025/pelicans.jpg"   # opens in lightbox
  note: "Best at low tide."
```

### Forcing all multi-tracks to one color

```
{{< track "patagonia-hikes" color="#334155" >}}
```

### A reference layer with no popup or markers

```yaml
- name:    "Park boundary"
  gpx:     "/uploads/2025/boundary.gpx"
  color:   "#94a3b8"
  popup:   no
  markers: no
```

---

## Compatibility

- Works alongside the [Maps plugin](https://github.com/microdotblog/plugin-maps). Both share the same Leaflet loader so the library is only downloaded once per page.
- Multiple `{{< track >}}` shortcodes on the same page are fully supported.
- Supports dark mode via `prefers-color-scheme: dark`.
- Graceful no-JavaScript fallback message is shown inside the map container.

---

## Dependencies (loaded on demand, no setup required)

| Library | Version | Purpose |
|---------|---------|---------|
| [Leaflet](https://leafletjs.com) | 1.9.4 | Interactive map |
| [leaflet-gpx](https://github.com/mpetazzoni/leaflet-gpx) | 1.7.0 | GPX parsing and stats |
| [hls.js](https://github.com/video-dev/hls.js) | latest 1.x | HLS video streams (loaded only if needed, only on non-Safari) |
| [Font Awesome Free](https://fontawesome.com) | 6.5.1 | Waypoint icons (loaded only if waypoints are present) |

All libraries are loaded from public CDNs only when first needed.

---

## License

MIT
