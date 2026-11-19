## SYSTEM OVERVIEW

| Grid | Code | What It Tracks |
| :--- | :--- | :--- |
| **Minute Grid** | `M` | Minute rounded down to 5 |
| **CODGRID** | `C` | 45-minute intervals |
| **Hour Grid** | `H` | Hour grouped into 6 repeating positions |
| **Arrow Grid** | `A` | Same 6-position hour grouping |

## STRICT FORMAT LOCK
- **If `MODE: SHORT`:** Output **ONLY** the check line(s) (`Time -> Arrow in Mxx, Cd, Hb, Ab (+r mins)`). Do not output the template.
- **If `MODE: FULL`:** Output **1 check line** per input time, then the filled TEMPLATE inside **ONE** ` ```text ` block. Strip the `01|`–`14|` prefixes and keep the exact leading spaces, line count, and left/right layout.
- **3-char boxes (`C`, `H`):** active = `[ X ]`, empty = `[   ]`.
- **4-char boxes (`M`, `A`):** empty = `[    ]`, 1 arrow = `[ ↑  ]`, 2 arrows = `[ ↖↘ ]`, 3 arrows = `[ ←↑→]`, 4 arrows = `[←↑↓→]`.
- **CLEANUP:** Replace all unused `[C...]`, `[H...]`, `[M...]`, `[A...]` placeholders with blank spaces (`[   ]` or `[    ]`).

## MULTIPLE INPUT RULE
1. Treat every input time as a separate, independent calculation.
2. Apply Rules 1–6 to each input time individually.
3. Do not combine or average input times before calculating.
4. After all calculations are complete, place every result into ONE shared template.
5. Preserve every active position from every input time.
6. If multiple inputs activate the same box, display all resulting symbols in that box, following the specified arrow-sorting rules (for `[ X ]` boxes, keep `[ X ]`).
7. Never let a later input overwrite an earlier input.

## RULES (5m x 12 = 60m x 6 = 360m = 72 options; 24-hr H=00–23, M=00–59)
For each input time:
1. **HOUR SLOT (`b` = 1–6, repeating every 6 hrs):**
   `b=1`: 01,07,13,19 (`↖`) | `b=2`: 02,08,14,20 (`↑`) | `b=3`: 03,09,15,21 (`↗`)
   `b=4`: 04,10,16,22 (`↙`) | `b=5`: 05,11,17,23 (`↓`) | `b=6`: 06,12,18,00 (`↘`)
2. **5-MIN GRID: Solve M = 5m + r (m=0–11, leftover r=0–4 mins). Match 5m (00, 05, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55). Place Hour Arrow in matching [Mxx ]; unmatched -> [    ]. Show (+r mins) in the check line. Sort multiple arrows: ↖ ↑ ↗ ↙ ↓ ↘.
Place Hour Arrow in matching `[Mxx ]`; unmatched -> `[ ]`. Show `(-r mins)` in the check line. Sort multiple arrows: `↖ ↑ ↗ ↙ ↓ ↘`
     Each minute value must occupy its exact matching position. Never move a value to another row or column.
3. **2x3 HOUR GRID:** Place `[ X ]` in matching `[Hb ]` using `b` from Rule 1 (others -> `[   ]`).
4. **ARROW GRID:** Determine the box position `[Ab  ]` using the hour slot `b` from Rule 1. Determine the arrow direction independently using the minute range (`00–14`=`↑`, `15–29`=`→`, `30–44`=`↓`, `45–59`=`←`). The hour selects the box; the minutes select the arrow. These two calculations must not be confused. Place the arrow in `[Ab  ]` (others -> `[    ]`). Sort multiple arrows: `← ↑ ↓ →`.
5. **CODGRID:**
   - **CODGRID INDEX RULE:** The 45-minute interval index `d` determines the CODGRID box directly. `d=1` maps to `C1`, `d=2` maps to `C2`, continuing sequentially through `d=8` mapping to `C8`. Never derive the box number by estimating or rounding the clock time.
   - **Equation:** `T = 360(a - 1) + 45(d - 1) + 90 + c` (`a=1–4`, `d=1–8`, `c=00–44`).
   - **Required Calculation Order:**
     1. Calculate `T = (H × 60) + M` (if `T < 90`, add `1440`).
     2. Apply the 90-minute offset (`T - 90`).
     3. Calculate `a` (`1–4`).
     4. Calculate `d` (`1–8`).
     5. Calculate `c` (`00–44`).
     6. Verify the equation.
     7. Place `[ X ]` in `[Cd ]` (others -> `[   ]`).
   - **CODGRID VALIDATION:** Before selecting a box, verify that `0 ≤ c ≤ 44` and that substituting `a`, `d`, and `c` into the original equation reproduces the exact calculated `T`. If verification fails, recalculate. Never output an unverified CODGRID position.
6. **CLOCK:** **NEVER** modify `(00)`, `(45)`, `(15)`, `(30)`. Replace `<input>` with the input time(s).

## TEMPLATE (Strip `01|`–`14|` prefixes; keep exact spaces & left/right layout)
01|You are here and that enough
02|
03|                       CODGRID
04|         (00) 
05|   (45)   |   (15)      [C1 ] | [C2 ] | [C3 ] | [C4 ]
06|         (30)           [C5 ] | [C6 ] | [C7 ] | [C8 ]
07|       <input>
08|5-Minute Grid                 2 by 3 HOUR GRID
09|[M05 ] | [M10 ] | [M15 ]    [H1 ] | [H2 ] | [H3 ]
10|[M20 ] | [M25 ] | [M30 ]    [H4 ] | [H5 ] | [H6 ]
11|[M35 ] | [M40 ] | [M45 ]
12|[M50 ] | [M55 ] | [M00 ]    ARROW GRID
13|                            [A1  ] | [A2  ] | [A3  ]
14|                            [A4  ] | [A5  ] | [A6  ] 

## CURRENT TIME RULE
- **Trigger:** If `INPUT TIME` is `now`, look up the current time in **[Your Time Zone, e.g., Central Time / CST]** on the web and convert it to 24-hour `HH:MM` format before running Rules 1–6.

MODE: SHORT
INPUT TIME: now





////


5-MIN GRID: Solve M = 5m + r (m=0–11, leftover r=0–4 mins). Match 5m (00, 05, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55). Place Hour Arrow in matching [Mxx ]; unmatched -> [    ]. Show (+r mins) in the check line. Sort multiple arrows: ↖ ↑ ↗ ↙ ↓ ↘.


[M05 ]= 00:05+1h. 1h =0≤h≤23.
[M10 ]= 00:05+1h. 1h =0≤h≤23.
[M15 ]= 00:05+1h. 1h =0≤h≤23.
[M20 ]= 00:05+1h. 1h =0≤h≤23.
[M25 ]= 00:05+1h. 1h =0≤h≤23.
[M30 ]= 00:05+1h. 1h =0≤h≤23.
[M35 ]= 00:05+1h. 1h =0≤h≤23.
[M40 ]= 00:05+1h. 1h =0≤h≤23.
[M45 ]= 00:05+1h. 1h =0≤h≤23.
[M50 ]= 00:05+1h. 1h =0≤h≤23.
[M55 ]= 00:05+1h. 1h =0≤h≤23.
[M00 ]= 00:05+1h. 1h =0≤h≤23.
---

[h1 ]=01:xx+6h . h=0-3
[h2 ]=02:xx+6h . h=0-3
[h3 ]=03:xx+6h . h=0-3
[h4 ]=04:xx+6h . h=0-3
[h5 ]=05:xx+6h . h=0-3
[h6 ]=06:xx+6h . h=0-3

24:xx=00:xx
----
[a1 ]=01:xx+6h . h=0-3
[a2 ]=02:xx+6h . h=0-3
[a3 ]=03:xx+6h . h=0-3
[a4 ]=04:xx+6h . h=0-3
[a5 ]=05:xx+6h . h=0-3
[a6 ]=06:xx+6h . h=0-3

24:xx=00:xx


