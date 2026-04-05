# Skywatcher

A self-contained, single-file astronomy planning dashboard for amateur observers. No dependencies, no server, no internet connection required — open the HTML file in any modern browser.

Fully configurable for any location via ZIP code or the ⌖ Locate button. Default location is San Francisco CA.

---

## Quick Start

1. Open `astronomy_dashboard.html` in Chrome, Firefox, or Safari
2. Optionally enter your ZIP code in Settings (gear icon) to set your location
3. Use the four tabs to explore the year, plan a specific night, or check Jupiter and Saturn

---

## Tabs

### Year View

An annual Gantt-style timeline showing every tracked object's visibility across the full calendar year. The horizontal axis is the year; each row is one object. Bars represent nights when the object rises above your effective horizon during your observing window.

**Bar encoding:**
- Bar length = observable season
- Bar opacity = peak altitude quality (brighter = higher culmination)
- Colored dot = object color

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
- Hover any bar for altitude, rise/set times, phase/illumination (where applicable)
- Right-click any event marker to jump to that date in Single Night view
- Click any object label to open the detail panel
- Use `‹ Year ›` arrows to navigate years

---

### Single Night View

An altitude-vs-time chart for any chosen date, covering dusk through dawn. Each tracked object gets a curve showing its altitude through the night.

**Visual encoding:**
- Solid line = above your effective horizon limit
- Dashed line = below limit (still plotted for context)
- Line weight: Moon 4px, planets 2.5px
- Colors match the Year View

**Jupiter overlay** (when Jupiter is visible):
- **Orange stripe** on Jupiter's curve = Great Red Spot within ±35° of central meridian
- **Silver stripe** = Galilean moon shadow transit in progress, labeled with moon initial (I/E/G/C)

**Conjunction annotations:**
- Bracket markers with separation label when two objects are within 5° of each other

**Interactions:**
- Hover anywhere on the canvas for a tooltip showing time, altitude, azimuth, and phase
- The date picker accepts any date; click Update to redraw

---

### Jupiter Tab

A monthly event timeline for the Galilean moon system. Designed to identify high-value observing nights — specifically shadow transits, the Great Red Spot, and rare simultaneous events.

**Event rows:**
- **Io, Europa, Ganymede, Callisto** — horizontal bars for shadow transits and moon transits
- **GRS** — orange bars when the Great Red Spot is within ±35° of the central meridian (System II longitude)

**Visual hierarchy:**
- Shadow transits: full-height silver/white bars (highest priority)
- Moon transits: shorter, dimmer bars (secondary)
- GRS: orange bars
- Rare overlaps (double shadow, shadow + GRS): pulsing gold highlight

**Best Nights chips** — scored and ranked:
- Shadow in observing window: +3 pts
- GRS in window: +2 pts
- Rare overlap: +4 pts
- Top 8 nights shown; click any chip to jump to Single Night view

**Column date alignment:**
- All bars display under their **local evening date** (not UT date). Events occurring in the early morning UT hours (e.g., 02:00 UT on the 23rd = 7pm PDT on the 22nd) correctly appear under the local date column.

**Navigation:**
- `‹ Month Year ›` arrows step through months
- Right-click any event bar to jump to that night in Single Night view
- GRS longitude field is adjustable (default 295°, System II; drifts ~1°/month)

---

### Saturn Tab

An annual context panel answering: *"Is Saturn worth observing right now, and what makes it interesting?"*

**Left panel — current context:**
- **SVG ring diagram** — dynamically drawn at the current ring tilt angle; thicker = more open
- **Quality rating** — Excellent / Very Good / Good / Fair / Poor, based on ring tilt B
- **Ring tilt (B)** — sub-Earth latitude in degrees. 0° = edge-on (rings invisible), 27° = maximum
- **Trend** — whether rings are opening or closing over the next 30 days
- **Elongation** — current angular separation from the Sun
- **Distance** — Earth-Saturn distance in AU
- **Opposition date** — next or most recent opposition for the selected year

Hover any stat row for a tooltip explaining what the value means and its observational implications.

**Right panel — annual timeline (three rows):**

| Row | What it shows |
|-----|--------------|
| Ring tilt | Band height proportional to \|B\|. Thin = edge-on, thick = wide open. Shaded region = observable |
| Visibility | Bar brightness proportional to elongation. Dark = near conjunction, bright = near opposition |
| Ring shadow | Blue highlight when shadow geometry is favorable (elongation 55–125°, B > 2°) |

Vertical markers: opposition (orange), quadratures (blue), today (gold).

Hover any track for a date-interpolated tooltip showing ring tilt, elongation, distance, and shadow quality at that point.

**Notable Events callouts:**
- Opposition date and ring tilt at that moment
- Quadrature window and shadow arc visibility note
- Titan transit status (context-aware by year — see Titan note below)

**Year navigation:** `‹ Year ›` arrows; shares the `ST.year` state with Year View.

---

## Astronomical Methods

### Coordinate System

All positions computed in the J2000.0 ecliptic frame and converted to geocentric equatorial (RA/Dec) for alt-az projection. The observer's local sidereal time drives the hour angle used in the alt-az transformation.

### Core Ephemerides

**Sun** — Meeus *Astronomical Algorithms* Ch.25. Mean longitude + equation of center (3 terms). Accurate to ~0.01°.

**Moon** — Meeus Ch.47 simplified series. Longitude (60 terms), latitude (5 terms), distance. Accurate to ~0.1° for planning purposes.

**Planets** — Meeus Table 31.a orbital elements (L, e, i, Ω, ω) with secular rates. Full Keplerian orbit solution via iterative Kepler equation. Converted to geocentric equatorial via heliocentric rectangular → geocentric rectangular → equatorial rotation.

> **Critical fix applied:** The argument of latitude `u = v + ω − Ω` must be computed entirely in radians. An earlier version mixed radians (true anomaly v) with degrees (ω, Ω), producing ~90° errors in planet RA and corrupting elongation and shadow phase calculations for all months.

**Rise/set/transit** — Meeus Ch.15 iterative method. Converges to within ~1 minute for all objects.

### Jupiter — Galilean Moon System

**Algorithm:** Meeus Ch.44 simplified series. Each moon's mean longitude is perturbed by mutual interaction terms (largest: Io/Europa resonance).

**Sky-plane projection:**
```
x =  -a · sin(λ)                    [east-west, + = west]
y =   a · cos(λ) · sin(B₀)          [north-south]
z =   a · cos(λ) · cos(B₀)          [depth; z < 0 = in front of Jupiter]
```

where B₀ is Jupiter's sub-Earth latitude (replaces the previous fixed DE = 3.1°, which made Callisto transits geometrically impossible when |B₀| was small).

**Sub-Earth latitude B₀** — computed from Jupiter's heliocentric ecliptic longitude and the node/obliquity of Jupiter's equatorial plane:
```
B₀ = arcsin(−sin(3.117°) · sin(λ_J − 99.44°))
```

**Transit detection:** `z < 0` AND `x² + (y/0.935)² < 1` (disk ellipse: equatorial radius 1 Rj, polar 0.935 Rj)

**Shadow displacement:**
```
shadow_x = moon_x + shadowPhase · a
shadowPhase = ±sin(phase_angle_at_Jupiter)
```
Negative post-opposition (shadow trails east of moon). Phase angle derived from Jupiter's geocentric elongation and heliocentric distance.

**Great Red Spot** — System II central meridian longitude via:
```
CM_II = 181.62° + 870.5366° · (JD − 2443000.5)
```
GRS visible when |CM_II − GRS_longitude| < 35°. Default GRS longitude 295° (System II, 2026); adjustable in the UI.

**Calibrated orbital elements** (empirically matched to *Sky & Telescope* predictions, Apr–May 2026):

| Moon | l₀ (°) | n (°/day) | Transit accuracy |
|------|--------|-----------|-----------------|
| Io | 355.50 | 203.48895579 | ±6 min |
| Europa | 64.80 | 101.37472473 | ±18 min |
| Ganymede | 64.716 | 50.17586719 | ±30 min |
| Callisto | 289.963 | 21.43479135 | ±20 min |

Ganymede and Callisto were calibrated from two transit midpoints each (Apr 10 and May 23 for Ganymede; Apr 20 and May 7 for Callisto), giving consistent period and initial longitude simultaneously.

### Saturn — Ring System

**Ring tilt B** — sub-Earth latitude on Saturn, computed from geocentric ecliptic longitude and latitude of Saturn and Saturn's pole orientation (Meeus Ch.45):
```
B = arcsin(−sin(28.048°) · cos(β) · sin(λ − 169.53°) + cos(28.048°) · sin(β))
```
where λ, β are Saturn's geocentric ecliptic longitude and latitude.

**Titan transits** — geometrically possible only when |B| < 2.83° (= arcsin(1/20.27), Titan's orbital radius in Saturn radii). The last transit window was mid-2024 through early February 2026. The next window opens around 2038–2040.

### Eclipses and Occultations

**Lunar eclipses** — detected by checking Moon–antisun angular separation at full moon. Umbral magnitude from shadow geometry. Penumbral eclipses (umbral magnitude < 0) are noted separately.

**Solar eclipses** — detected at new moon by checking Moon-Sun angular separation against the sum of apparent radii. Coverage percentage computed for the observer's location.

**Planetary occultations** — Moon-planet angular separation at new/full moon phases checked against the Moon's apparent radius.

### Conjunctions

Scanned at 12-hour intervals throughout the year for all pairs of tracked objects. Tiered by separation:

| Tier | Threshold | Display |
|------|-----------|---------|
| Close | < 1.5° | Gold ⟡ |
| Moderate | < 5° | White ⟡ |
| Wide | < 10° | Blue ⟡ |

Daytime conjunctions (both objects below horizon during night hours) are filtered out.

---

## Observer Settings

| Setting | Default | Notes |
|---------|---------|-------|
| ZIP code | 94102 (San Francisco CA) | Looks up lat/lon from built-in table |
| East horizon limit | 5° | Objects below this altitude at eastern azimuths are suppressed |
| West horizon limit | 5° | Same for western azimuths |
| Late night cutoff | 1:00 AM | Observing window ends at this local time |

**Supported ZIP codes (built-in):**
94102 San Francisco CA · 95630 Folsom CA · 95621 Citrus Heights CA · 95814 Sacramento CA · 90210 Beverly Hills CA · 92101 San Diego CA · 91101 Pasadena CA · 96001 Redding CA · 89101 Las Vegas NV · 97201 Portland OR · 98101 Seattle WA

---

## Tracked Objects

**Solar system:** Moon, Mercury, Venus, Mars, Jupiter, Saturn, Uranus, Neptune

**Deep sky (optional, disabled by default):**
Pleiades M45, Orion Nebula M42, Andromeda Galaxy M31, Hercules Cluster M13, Beehive M44, Crab Nebula M1, M35 Gemini, Lagoon Nebula M8

---

## Code Structure

The entire application is ~2,400 lines of vanilla HTML/CSS/JavaScript in a single file. No build step, no framework, no external dependencies.

```
astronomy_dashboard.html
│
├── <style>          CSS variables, layout, tab system, component styles
│
└── <script>
    ├── DOM CACHE    Cached element references ($tooltip, $nightDate, $nightCanvas)
    ├── CONSTANTS    J2000, JD_UNIX, TITAN_EPOCH
    ├── CORE MATH    JD conversion, date formatting, time helpers
    ├── ASTRONOMY    Sun, Moon, planets, alt/az, rise/set/transit
    ├── OBJECTS & STATE  Tracked objects, observer settings (ST), ZIP lookup
    ├── YEAR VIEW    Annual timeline rendering, visibility bar computation
    ├── CONJUNCTIONS Separation scanning, tier classification, marker injection
    ├── ECLIPSES     Lunar/solar eclipse detection, occultation scanning
    ├── PLANET EVENTS Opposition, quadrature, elongation marker computation
    ├── NIGHT VIEW   Canvas altitude curves, Jupiter overlays, annotations
    ├── UI HELPERS   Tooltips, detail panel, options, tab switching
    ├── JUPITER TAB  Monthly Galilean moon shadow/transit timeline + scoring
    └── SATURN TAB   Annual ring context, shadow geometry, hover tooltips
```

**Key constants:**
```javascript
const J2000      = 2451545.0;  // JD of J2000.0 epoch
const JD_UNIX    = 2440587.5;  // JD of Unix epoch
const TITAN_EPOCH = 2443000.5; // Reference epoch for Galilean moon elements
const DEG = Math.PI / 180;     // degrees → radians
const RAD = 180 / Math.PI;     // radians → degrees
```

---

## Limitations and Known Accuracy

- **Planetary positions:** ~0.1° (sufficient for naked-eye and telescope planning)
- **Conjunction timing:** ±12 hours (12-hour scan step + hourly refinement)
- **Eclipse detection:** umbral magnitude within ~5%; type (total/partial/penumbral) correct
- **Galilean moon transits:** see calibration table above
- **Saturn ring tilt:** ~0.1° accuracy
- **No light-time correction** — positions are geometric, not apparent
- **No atmospheric refraction** — altitudes are geometric; add ~0.5° near horizon
- **No precession beyond J2000** — adequate for ±10 year range around 2026

---

## Reference

Meeus, J. *Astronomical Algorithms*, 2nd ed. Willmann-Bell, 1998.

Calibration data: *Sky & Telescope* planet and satellite event tables, April–May 2026.
