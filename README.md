# Skywatcher

A self-contained, single-file astronomy planning dashboard for amateur observers. No dependencies, no server, no internet connection required — open the HTML file in any modern browser.

Fully configurable for any location via ZIP code or the ⌖ Locate button. Default location is San Francisco CA.

---

## Quick Start

1. Open `index.html` in Chrome, Firefox, or Safari
2. Optionally enter your ZIP code in Settings and click Apply, or click **⌖ Locate** to use your browser's geolocation
3. Use the five tabs to explore the year, plan a specific night, or check Jupiter, Saturn, and deep sky objects

Your location is saved in the URL hash (e.g. `#94102` or `#37.77,-122.41`) so it persists across reloads and can be bookmarked or shared.

---

## Tabs

### Year View

An annual Gantt-style timeline showing every tracked object's visibility across the full calendar year. Each row is one object; bars represent nights when the object rises above your effective horizon during your observing window.

**Bar encoding:**
- Bar length = observable season
- Bar opacity = peak altitude quality (brighter = higher culmination)
- Phase/illumination dot on Moon and inner planet bars

**Overlaid event markers** (each toggleable via checkboxes):

| Symbol | Event |
|--------|-------|
| ⊕ | Opposition |
| ◧ | Western quadrature |
| ◁ | Greatest eastern elongation (inner planets) |
| ▷ | Greatest western elongation (inner planets) |
| ● | Conjunction (near sun) |
| ⟡ | Planetary conjunction (gold <1.5°, white <5°, blue <10°) |
| 🌑🌕 | New/full moon with eclipse potential |
| ☀ | Solar eclipse |
| ⊙ | Lunar occultation of a planet |

**Interactions:**
- Hover any bar for altitude, rise/set times, phase/illumination
- Right-click any event marker to jump to that date in Single Night view
- Click any object label to open the detail panel (shows magnitude, position, rise/set times)
- Use `‹ Year ›` arrows to navigate years

---

### Single Night View

An altitude-vs-time chart for any chosen date, covering dusk through dawn. Each tracked object gets a curve showing its altitude through the night.

**Visual encoding:**

| Object | Line style |
|--------|-----------|
| Moon | Solid, 4px |
| Planets | Solid, 2.5px |
| Deep sky objects | Dashed, 1.5px, type color |

- Solid line = above effective horizon
- Dashed ghost = below limit (plotted for context)
- Colors match the Year View and Deep Sky tab type colors

**Jupiter overlay** (when Jupiter is visible):
- **Orange stripe** on Jupiter's curve = Great Red Spot within ±35° of central meridian
- **Silver stripe** = Galilean moon shadow transit in progress, labeled with moon initial (I/E/G/C)

**Conjunction annotations:**
- Bracket markers with separation label when two objects are within 5° of each other

**Interactions:**
- Hover anywhere on the canvas for a tooltip: time, altitude, azimuth, phase/distance for planets and Moon; description for DSOs
- Date picker accepts any date

---

### Jupiter Tab

A monthly event timeline for the Galilean moon system, designed to identify high-value observing nights.

**Event rows:** Io, Europa, Ganymede, Callisto (shadow transits and disk transits), plus GRS (Great Red Spot).

**Visual hierarchy:**
- Shadow transits: full-height silver/white bars (highest priority)
- Moon transits: shorter, dimmer bars
- GRS: orange bars when within ±35° of central meridian
- Rare overlaps (double shadow, shadow + GRS): pulsing gold highlight

**Best Nights chips** — scored and ranked by shadow transit (+3), GRS (+2), rare overlap (+4). Top 8 shown; click any chip to jump to Single Night.

**Column date alignment:** All bars display under their **local evening date** (not UT date). Events in the early morning UT hours correctly appear under the previous local date.

**Callisto transit gap note:** When Callisto transits are geometrically impossible (B₀ too large), an info banner explains why and gives the next expected window.

**Navigation:** `‹ Month Year ›` arrows, right-click any bar to jump to Single Night, GRS longitude field (default 295°, System II).

---

### Saturn Tab

An annual context panel answering: *"Is Saturn worth observing right now, and what makes it interesting?"*

**Left panel:**
- **SVG ring diagram** — drawn at the current ring tilt; thicker ellipse = more open
- **Quality rating** — Excellent / Very Good / Good / Fair / Poor (based on ring tilt B)
- **Ring tilt (B)** — 0° = edge-on, 27° = maximum
- **Trend** — rings opening or closing over the next 30 days
- **Elongation, distance, opposition date**

Hover any stat row for a tooltip explaining the value.

**Right panel — annual timeline (three rows):**

| Row | Encoding |
|-----|---------|
| Ring tilt | Band height ∝ \|B\|. Thin = edge-on, thick = wide open |
| Visibility | Bar brightness ∝ elongation |
| Ring shadow | Blue window when shadow geometry is favorable (elong 55–125°, B > 2°) |

Hover any track for a date-interpolated tooltip with ring tilt, elongation, distance, and shadow quality.

**Notable Events callouts:** opposition date, quadrature shadow note, Titan transit status (context-aware by year — last window closed early 2026, next ~2038–2040).

**Year navigation:** `‹ Year ›` arrows, shared with Year View.

---

### Deep Sky Tab

An annual visibility timeline for 15 common deep sky objects, with Bortle sky condition context.

**Objects included:**

| Type | Objects |
|------|---------|
| Open cluster (blue) | Pleiades M45, Beehive M44, Wild Duck M11, Double Cluster NGC 869/884, M35 |
| Globular cluster (gold) | Hercules M13, M3 |
| Emission nebula (pink) | Orion Nebula M42, Lagoon Nebula M8, Crab Nebula M1 |
| Planetary nebula (teal) | Ring Nebula M57, Dumbbell Nebula M27 |
| Galaxy (purple) | Andromeda M31, Bode's Galaxy M81, Cigar Galaxy M82 |

**Timeline:** Each row shows a visibility bar whose height encodes peak altitude through the night (taller = object higher in sky = more favorable). Grouped by observing season: Autumn/Winter, Spring, Summer/Autumn.

**Hover any track:** Shows the date at cursor, peak altitude, and the full three-tier Bortle context (Urban/Suburban 7–9, Suburban/Rural 4–6, Dark Skies 1–3) from the reference table.

**Click any object label:** Opens the detail panel with the SVG diagram, magnitude with telescope requirement hint, coordinates, and Bortle notes.

**Year navigation:** `‹ Year ›` arrows.

---

## Object Details Panel

Click any object label in Year View or Deep Sky tab to open the detail panel.

**Planets:** Magnitude range, current RA/Dec, distance, altitude/azimuth now, today's rise/transit/set times.

**Moon:** Phase name and icon, illumination %, distance, altitude now, today's rise/transit/set.

**Deep sky objects:** Catalog magnitude with telescope requirement hint (e.g. "Binoculars or small telescope"), description, RA/Dec, altitude now, today's rise/transit/set.

All panels include a **View Tonight's Chart** button that jumps to the Single Night tab for the current date.

---

## Astronomical Methods

### Coordinate System

All positions computed in the J2000.0 ecliptic frame and converted to geocentric equatorial (RA/Dec) for alt-az projection. Observer's local sidereal time drives the hour angle.

### Core Ephemerides

**Sun** — Meeus Ch.25. Mean longitude + equation of center (3 terms). ~0.01° accuracy.

**Moon** — Meeus Ch.47 simplified series. Longitude (60 terms), latitude (5 terms), distance. ~0.1° accuracy.

**Planets** — Meeus Table 31.a orbital elements with secular rates. Full Keplerian orbit via iterative Kepler equation. Geocentric RA/Dec via heliocentric → geocentric rectangular → equatorial rotation.

> **Note:** The argument of latitude `u = v + ω − Ω` must be computed entirely in radians. An earlier version mixed radians and degrees here, producing ~90° errors in planet RA.

**Rise/set/transit** — Meeus Ch.15 iterative method. Converges to within ~1 minute.

### Jupiter — Galilean Moon System

**Algorithm:** Meeus Ch.44 simplified series with mutual interaction perturbations.

**Sky-plane projection:**
```
x = -a · sin(λ)                  [east-west, + = west]
y =  a · cos(λ) · sin(B₀)        [north-south]
z =  a · cos(λ) · cos(B₀)        [depth; z < 0 = in front of Jupiter]
```

**Sub-Earth latitude B₀** — computed dynamically from Jupiter's heliocentric ecliptic longitude (replaces old fixed DE = 3.1°, which made Callisto transits geometrically undetectable):
```
B₀ = arcsin(−sin(3.117°) · sin(λ_J − 99.44°))
```

**Transit detection:** `z < 0` AND `x² + (y/0.935)² < 1`

**Shadow displacement:**
```
shadow_x = moon_x + shadowPhase · a
shadowPhase = ±sin(phase_angle_at_Jupiter)   [negative post-opposition]
```

**GRS:** System II central meridian via `CM_II = 181.62° + 870.5366° · (JD − TITAN_EPOCH)`

**Calibrated orbital elements** (matched to Sky & Telescope, Apr–May 2026):

| Moon | l₀ (°) | n (°/day) | Accuracy |
|------|--------|-----------|---------|
| Io | 355.50 | 203.48895579 | ±6 min |
| Europa | 64.80 | 101.37472473 | ±18 min |
| Ganymede | 64.716 | 50.17586719 | ±30 min |
| Callisto | 289.963 | 21.43479135 | ±20 min |

**Callisto transit window:** Requires |B₀| < 2.03° (= arcsin(1/26.36 Rj)). Last window closed ~Jan 2027; next opens ~late 2030.

### Saturn — Ring System

**Ring tilt B** — Meeus Ch.45:
```
B = arcsin(−sin(28.048°) · cos(β) · sin(λ − 169.53°) + cos(28.048°) · sin(β))
```

**Titan transits** — possible only when |B| < 2.83° (= arcsin(1/20.27 Rs)). Last window: mid-2024 through early Feb 2026. Next: ~2038–2040.

### Eclipses and Occultations

**Lunar eclipses** — Moon–antisun separation at full moon; umbral magnitude from shadow geometry.

**Solar eclipses** — Moon–Sun separation at new moon vs sum of apparent radii; coverage % for observer's location.

**Planetary occultations** — Moon-planet separation at new/full moon vs Moon's apparent radius.

### Conjunctions

Scanned at 12-hour intervals for all pairs of tracked objects. Daytime conjunctions filtered out.

| Tier | Threshold | Display |
|------|-----------|---------|
| Close | < 1.5° | Gold ⟡ |
| Moderate | < 5° | White ⟡ |
| Wide | < 10° | Blue ⟡ |

---

## Observer Settings

| Setting | Default | Notes |
|---------|---------|-------|
| ZIP code | 94102 (San Francisco CA) | Looks up lat/lon from built-in table |
| ⌖ Locate | — | Uses browser Geolocation API (requires permission) |
| East horizon | 5° | Objects below this altitude at eastern azimuths are suppressed |
| West horizon | 5° | Same for western azimuths |
| Late night cutoff | 1:00 AM | Observing window ends at this local time |

**URL hash persistence:** After applying a ZIP or using Locate, the location is saved to the URL hash (`#94102` or `#37.77,-122.41`). Reloading the page restores the location automatically. The hash can be bookmarked or shared.

**Supported ZIP codes (built-in):**
94102 San Francisco CA · 95630 Folsom CA · 95621 Citrus Heights CA · 95814 Sacramento CA · 90210 Beverly Hills CA · 92101 San Diego CA · 91101 Pasadena CA · 96001 Redding CA · 89101 Las Vegas NV · 97201 Portland OR · 98101 Seattle WA

---

## Tracked Objects

**Solar system:** Moon, Mercury, Venus, Mars, Jupiter, Saturn, Uranus, Neptune

**Deep sky (optional, disabled by default):**

| Type | Objects |
|------|---------|
| Open clusters | Pleiades M45, Beehive M44, Wild Duck M11, Double Cluster, M35 |
| Globular clusters | Hercules M13, M3 |
| Emission nebulae | Orion Nebula M42, Lagoon Nebula M8, Crab Nebula M1 |
| Planetary nebulae | Ring Nebula M57, Dumbbell Nebula M27 |
| Galaxies | Andromeda M31, Bode's Galaxy M81, Cigar Galaxy M82 |

Enable DSOs via the ⚙ Objects panel. Enabled DSOs appear as **dashed colored curves** in Single Night view alongside planets, with type-matched colors.

---

## Code Structure

Single file, ~3,150 lines of vanilla HTML/CSS/JavaScript. No build step, no framework, no external dependencies.

```
index.html
│
├── <style>          CSS variables, layout, tab system, component styles
│
└── <script>
    ├── DOM CACHE    $tooltip, $nightDate, $nightCanvas
    ├── CONSTANTS    J2000, JD_UNIX, TITAN_EPOCH, DEG, RAD
    ├── CORE MATH    JD conversion, time formatting, local noon
    ├── ASTRONOMY    Sun, Moon, planets, alt/az, rise/set/transit
    ├── OBJECTS & STATE  DSO array (15 objects), OBJECTS, ST, ZIP_DB
    ├── YEAR VIEW    Annual timeline, visibility bars, event markers
    ├── CONJUNCTIONS Separation scanning, tier classification
    ├── ECLIPSES     Lunar/solar eclipse detection, occultation scanning
    ├── PLANET EVENTS Opposition, quadrature, elongation markers
    ├── NIGHT VIEW   Canvas altitude curves, DSO dashed lines, Jupiter overlays
    ├── UI HELPERS   Tooltips, detail panel (with magnitude), tabs, settings
    ├── JUPITER TAB  Galilean moon timeline, nightWindow(), scoring
    ├── SATURN TAB   Ring tilt context, shadow geometry, Titan note
    └── DEEP SKY TAB DSO_CATALOG (13 entries), dsoSVG(), Bortle tooltips
```

**Key constants:**
```javascript
const J2000       = 2451545.0;  // JD of J2000.0 epoch
const JD_UNIX     = 2440587.5;  // JD of Unix epoch
const TITAN_EPOCH = 2443000.5;  // Reference epoch for Galilean moon elements
const DEG = Math.PI / 180;      // degrees → radians
const RAD = 180 / Math.PI;      // radians → degrees
```

---

## Limitations and Known Accuracy

- **Planetary positions:** ~0.1° (sufficient for naked-eye and telescope planning)
- **Conjunction timing:** ±12 hours
- **Eclipse detection:** umbral magnitude within ~5%; type correct
- **Galilean moon transits:** see calibration table above
- **Saturn ring tilt:** ~0.1° accuracy
- **No light-time correction** — geometric positions only
- **No atmospheric refraction** — add ~0.5° near horizon
- **No precession beyond J2000** — adequate for ±10 years around 2026

---

## Reference

Meeus, J. *Astronomical Algorithms*, 2nd ed. Willmann-Bell, 1998.

Calibration data: *Sky & Telescope* planet and satellite event tables, April–May 2026.

DSO descriptions and Bortle context adapted from standard amateur astronomy references.
