## SYSTEM OVERVIEW

| Grid | Code | Modular Cycle |
| :--- | :--- | :--- |
| **Minute Grid** | `M` | `00:05m + 1h` (`m=0–11`, `h=0–23`, `24:xx = 00:xx`) |
| **CODGRID** | `C` | `T = 90 + 360q + 45(d - 1) + c` (`d=1–8`, `q=0–3`, `c=00–44`) |
| **Hour Grid** | `H` | `0b:xx + 6h` (`b=1–6`, `h=0–3`) |
| **Arrow Grid** | `A` | `0b:xx + 6h` (`b=1–6`, `h=0–3`) |

## STRICT FORMAT LOCK
- **Speed Bump First:** Always output the 1-line `Calc:` scratchpad before any check line or template. Never skip it.
- **If `MODE: SHORT`:** Output **ONLY** the `Calc:` line(s) and the check line(s) (`Time -> HourArrow in Mxx, Cd, Hb, Ab (MinArrow) (+r mins)`). Do not output the template.
- **If `MODE: FULL`:** Output the `Calc:` line(s) and **1 check line** per input time, then the filled TEMPLATE inside **ONE** ```` ```text ```` block. Strip the `01|`–`14|` prefixes and keep the exact leading spaces, line count, and left/right layout.
- **3-char boxes (`C`, `H`):** active = `[ X ]`, empty = `[   ]`.
- **4-char boxes (`M`, `A`):** empty = `[    ]`, 1 arrow = `[ ↑  ]`, 2 arrows = `[ ↖↘ ]`, 3 arrows = `[ ←↑→]`, 4 arrows = `[←↑↓→]`.
- **CLEANUP:** Never leave an inactive placeholder containing its original label (`[C...]`, `[H...]`, `[M...]`, `[A...]`). Replace all unused boxes with blank spaces (`[   ]` or `[    ]`) while preserving exact brackets and spacing.

## MULTIPLE INPUT RULE
1. Treat every input time (`H=00–23`, `M=00–59`) as an independent calculation; never combine or average inputs.
2. Place all results into ONE shared template, preserving every active position without overwriting earlier inputs.
3. If multiple inputs share a box, keep `[ X ]` for `C`/`H`. For `M` and `A`, deduplicate identical arrows (each unique arrow appears only once), then sort `M` (`↖ ↑ ↗ ↙ ↓ ↘`) and `A` (`← ↑ ↓ →`).

## COMPACT MODULAR RULES
For each input time (`24:xx = 00:xx`, note `H=00` solves with `b=6, h=-1` or wraps to `24`):

1. **HOUR SLOT (`b` = 1–6):** Solve `H = b + 6h` (`h=0–3`; exception: `H=00` uses `b=6, h=-1`).
   - `b=1` (`↖`) | `b=2` (`↑`) | `b=3` (`↗`) | `b=4` (`↙`) | `b=5` (`↓`) | `b=6` (`↘`)
2. **5-MIN GRID (`Mxx`):** Solve `M = 5m + r` (`m=0–11`, `r=0–4`).
   - Explicit mapping: `0→M00`, `1→M05`, `2→M10`, `3→M15`, `4→M20`, `5→M25`, `6→M30`, `7→M35`, `8→M40`, `9→M45`, `10→M50`, `11→M55`.
   - Locked Grid Layout: `R1: M05, M10, M15` | `R2: M20, M25, M30` | `R3: M35, M40, M45` | `R4: M50, M55, M00`
   - **Rule:** Always place the **hour arrow** (`↖ ↑ ↗ ↙ ↓ ↘`) in `[Mxx ]`, never the minute-range arrow. Show `(+r mins)` in the check line.
3. **2x3 HOUR GRID (`Hb`):** Show `H[b]` separately from `A[b]` and place `[ X ]` in `[Hb ]`.
4. **ARROW GRID (`Ab`):** Select box `[Ab  ]` using `b`, and arrow using `M` (`00–14`=`↑`, `15–29`=`→`, `30–44`=`↓`, `45–59`=`←`). Never place the hour arrow in `[Ab  ]`.
5. **CODGRID (`Cd`):** Let `T = 60H + M` (if `T < 90`, add `1440`), then let `U = T - 90`.
   - Extract variables directly:
     - `q = floor(U / 360)`
     - `d = floor((U mod 360) / 45) + 1`
     - `c = U mod 45`
   - Confirm `0 ≤ c ≤ 44` and explicitly verify that `90 + 360q + 45(d - 1) + c` equals the original `T`, then place `[ X ]` in `[Cd ]`.
6. **CLOCK:** Never modify `(00)`, `(45)`, `(15)`, `(30)`. Replace `<input>` with the input time(s).

## VERIFICATION LOCK (MANDATORY SPEED BUMP)
Slow down and write a 1-line scratchpad (`Calc: ...`) before every check line to force step-by-step math:
- **Format:** `Calc: H=[H] -> b=[b] (HourArrow=[arrow]), M=[M] -> 5([m])+[r] (M[xx], MinArrow=[arrow]), T=[T], U=[U] -> C=[Cd] (q=[q], d=[d], c=[c] -> 90+360([q])+45([d-1])+[c]=[T])`
- **Example (`23:08`):** `Calc: H=23 -> b=5 (HourArrow=↓), M=08 -> 5(1)+3 (M05, MinArrow=↑), T=1388, U=1298 -> C=C5 (q=3, d=5, c=38 -> 90+360(3)+45(4)+38=1388)`
- **Final Check:**
  - **MINUTE GRID:** Always place the hour arrow, never the minute-range arrow. Confirm `M[xx]` matches `5×m`.
  - **HOUR GRID:** Show `H[b]` separately from `A[b]`.
  - **ARROW GRID:** Show `A[b]` and its minute-range direction.

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
- **Trigger:** If `INPUT TIME` is `now`, use the user's current local time in 24-hour `HH:MM` format before running Rules 1–6 (if local time is unavailable, ask the user for their current time).

MODE: FULL
INPUT TIME: now
version 5
///

## SYSTEM OVERVIEW

| Grid | Code | Modular Cycle |
| :--- | :--- | :--- |
| **Minute Grid** | `M` | `00:05m + 1h` (`m=0–11`, `h=0–23`, `24:xx = 00:xx`) |
| **CODGRID** | `C` | `T = 90 + 360q + 45(d - 1) + c` (`d=1–8`, `q=0–3`, `c=00–44`) |
| **Hour Grid** | `H` | `0b:xx + 6h` (`b=1–6`, `h=0–3`) |
| **Arrow Grid** | `A` | `0b:xx + 6h` (`b=1–6`, `h=0–3`) |

## STRICT FORMAT LOCK
- **Speed Bump First:** Always output the 1-line `Calc:` scratchpad before any check line or template. Never skip it.
- **If `MODE: SHORT`:** Output **ONLY** the `Calc:` line(s) and the check line(s) (`Time -> HourArrow in Mxx, Cd, Hb, Ab (MinArrow) (+r mins)`). Do not output the template.
- **If `MODE: FULL`:** Output the `Calc:` line(s) and **1 check line** per input time, then the filled TEMPLATE inside **ONE** ```` ```text ```` block. Strip the `01|`–`14|` prefixes and keep the exact leading spaces, line count, and left/right layout.
- **3-char boxes (`C`, `H`):** active = `[ X ]`, empty = `[   ]`.
- **4-char boxes (`M`, `A`):** empty = `[    ]`, 1 arrow = `[ ↑  ]`, 2 arrows = `[ ↖↘ ]`, 3 arrows = `[ ←↑→]`, 4 arrows = `[←↑↓→]`.
- **CLEANUP:** Never leave an inactive placeholder containing its original label (`[C...]`, `[H...]`, `[M...]`, `[A...]`). Replace all unused boxes with blank spaces (`[   ]` or `[    ]`) while preserving exact brackets and spacing.

## MULTIPLE INPUT RULE
1. Treat every input time (`H=00–23`, `M=00–59`) as an independent calculation; never combine or average inputs.
2. Place all results into ONE shared template, preserving every active position without overwriting earlier inputs.
3. If multiple inputs share a box, keep `[ X ]` for `C`/`H`. For `M` and `A`, deduplicate identical arrows (each unique arrow appears only once), then sort `M` (`↖ ↑ ↗ ↙ ↓ ↘`) and `A` (`← ↑ ↓ →`).

## COMPACT MODULAR RULES
For each input time (`24:xx = 00:xx`, note `H=00` solves with `b=6, h=-1` or wraps to `24`):

1. **HOUR SLOT (`b` = 1–6):** Solve `H = b + 6h` (`h=0–3`; exception: `H=00` uses `b=6, h=-1`).
   - `b=1` (`↖`) | `b=2` (`↑`) | `b=3` (`↗`) | `b=4` (`↙`) | `b=5` (`↓`) | `b=6` (`↘`)
2. **5-MIN GRID (`Mxx`):** Solve `M = 5m + r` (`m=0–11`, `r=0–4`).
   - Explicit mapping: `0→M00`, `1→M05`, `2→M10`, `3→M15`, `4→M20`, `5→M25`, `6→M30`, `7→M35`, `8→M40`, `9→M45`, `10→M50`, `11→M55`.
   - Locked Grid Layout: `R1: M05, M10, M15` | `R2: M20, M25, M30` | `R3: M35, M40, M45` | `R4: M50, M55, M00`
   - **Rule:** Always place the **hour arrow** (`↖ ↑ ↗ ↙ ↓ ↘`) in `[Mxx ]`, never the minute-range arrow. Show `(+r mins)` in the check line.
3. **2x3 HOUR GRID (`Hb`):** Show `H[b]` separately from `A[b]` and place `[ X ]` in `[Hb ]`.
4. **ARROW GRID (`Ab`):** Select box `[Ab  ]` using `b`, and arrow using `M` (`00–14`=`↑`, `15–29`=`→`, `30–44`=`↓`, `45–59`=`←`). Never place the hour arrow in `[Ab  ]`.
5. **CODGRID (`Cd`):** Let `T = 60H + M` (if `T < 90`, add `1440`), then let `U = T - 90`.
   - Extract variables directly:
     - `q = floor(U / 360)`
     - `d = floor((U mod 360) / 45) + 1`
     - `c = U mod 45`
   - Verify `90 + 360q + 45(d - 1) + c = T` and `0 ≤ c ≤ 44`, then place `[ X ]` in `[Cd ]`.
6. **CLOCK:** Never modify `(00)`, `(45)`, `(15)`, `(30)`. Replace `<input>` with the input time(s).

## VERIFICATION LOCK (MANDATORY SPEED BUMP)
Slow down and write a 1-line scratchpad (`Calc: ...`) before every check line to force step-by-step math:
- **Format:** `Calc: H=[H] -> b=[b] (HourArrow=[arrow]), M=[M] -> 5([m])+[r] (M[xx], MinArrow=[arrow]), T=[T], U=[U] -> C=[Cd] (q=[q], d=[d], c=[c])`
- **Example (`23:08`):** `Calc: H=23 -> b=5 (HourArrow=↓), M=08 -> 5(1)+3 (M05, MinArrow=↑), T=1388, U=1298 -> C=C5 (q=3, d=5, c=38)`
- **Final Check:**
  - **MINUTE GRID:** Always place the hour arrow, never the minute-range arrow. Confirm `M[xx]` matches `5×m`.
  - **HOUR GRID:** Show `H[b]` separately from `A[b]`.
  - **ARROW GRID:** Show `A[b]` and its minute-range direction.

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
- **Trigger:** If `INPUT TIME` is `now`, use the user's current local time in 24-hour `HH:MM` format before running Rules 1–6 (if local time is unavailable, ask the user for their current time).

MODE: FULL
INPUT TIME: now
vo4?


## SYSTEM OVERVIEW

| Grid | Code | Modular Cycle |
| :--- | :--- | :--- |
| **Minute Grid** | `M` | `00:05m + 1h` (`m=0–11`, `h=0–23`, `24:xx = 00:xx`) |
| **CODGRID** | `C` | `T = 90 + 360q + 45(d - 1) + c` (`d=1–8`, `q=0–3`, `c=00–44`) |
| **Hour Grid** | `H` | `0b:xx + 6h` (`b=1–6`, `h=0–3`) |
| **Arrow Grid** | `A` | `0b:xx + 6h` (`b=1–6`, `h=0–3`) |

## STRICT FORMAT LOCK
- **Speed Bump First:** Always output the 1-line `Calc:` scratchpad before any check line or template. Never skip it.
- **If `MODE: SHORT`:** Output **ONLY** the `Calc:` line(s) and the check line(s) (`Time -> HourArrow in Mxx, Cd, Hb, Ab (MinArrow) (+r mins)`). Do not output the template.
- **If `MODE: FULL`:** Output the `Calc:` line(s) and **1 check line** per input time, then the filled TEMPLATE inside **ONE** ```` ```text ```` block. Strip the `01|`–`14|` prefixes and keep the exact leading spaces, line count, and left/right layout.
- **3-char boxes (`C`, `H`):** active = `[ X ]`, empty = `[   ]`.
- **4-char boxes (`M`, `A`):** empty = `[    ]`, 1 arrow = `[ ↑  ]`, 2 arrows = `[ ↖↘ ]`, 3 arrows = `[ ←↑→]`, 4 arrows = `[←↑↓→]`.
- **CLEANUP:** Never leave an inactive placeholder containing its original label (`[C...]`, `[H...]`, `[M...]`, `[A...]`). Replace all unused boxes with blank spaces (`[   ]` or `[    ]`) while preserving exact brackets and spacing.

## MULTIPLE INPUT RULE
1. Treat every input time (`H=00–23`, `M=00–59`) as an independent calculation; never combine or average inputs.
2. Place all results into ONE shared template, preserving every active position without overwriting earlier inputs.
3. If multiple inputs share a box, keep `[ X ]` for `C`/`H`. For `M` and `A`, deduplicate identical arrows (each unique arrow appears only once), then sort `M` (`↖ ↑ ↗ ↙ ↓ ↘`) and `A` (`← ↑ ↓ →`).

## COMPACT MODULAR RULES
For each input time (`24:xx = 00:xx`, note `H=00` solves with `b=6, h=-1` or wraps to `24`):

1. **HOUR SLOT (`b` = 1–6):** Solve `H = b + 6h` (`h=0–3`; exception: `H=00` uses `b=6, h=-1`).
   - `b=1` (`↖`) | `b=2` (`↑`) | `b=3` (`↗`) | `b=4` (`↙`) | `b=5` (`↓`) | `b=6` (`↘`)
2. **5-MIN GRID (`Mxx`):** Solve `M = 5m + r` (`m=0–11`, `r=0–4`).
   - Explicit mapping: `0→M00`, `1→M05`, `2→M10`, `3→M15`, `4→M20`, `5→M25`, `6→M30`, `7→M35`, `8→M40`, `9→M45`, `10→M50`, `11→M55`.
   - Locked Grid Layout: `R1: M05, M10, M15` | `R2: M20, M25, M30` | `R3: M35, M40, M45` | `R4: M50, M55, M00`
   - **Rule:** Always place the **hour arrow** (`↖ ↑ ↗ ↙ ↓ ↘`) in `[Mxx ]`, never the minute-range arrow. Show `(+r mins)` in the check line.
3. **2x3 HOUR GRID (`Hb`):** Show `H[b]` separately from `A[b]` and place `[ X ]` in `[Hb ]`.
4. **ARROW GRID (`Ab`):** Select box `[Ab  ]` using `b`, and arrow using `M` (`00–14`=`↑`, `15–29`=`→`, `30–44`=`↓`, `45–59`=`←`). Never place the hour arrow in `[Ab  ]`.
5. **CODGRID (`Cd`):** Let `T = 60H + M` (if `T < 90`, add `1440`).
   - Solve `T = 90 + 360q + 45(d - 1) + c` (`q=0–3`, `d=1–8`, `c=00–44`).
   - Verify `0 ≤ c ≤ 44` reproduces `T`, then place `[ X ]` in `[Cd ]`.
6. **CLOCK:** Never modify `(00)`, `(45)`, `(15)`, `(30)`. Replace `<input>` with the input time(s).

## VERIFICATION LOCK (MANDATORY SPEED BUMP)
Slow down and write a 1-line scratchpad (`Calc: ...`) before every check line to force step-by-step math:
- **Format:** `Calc: H=[H] -> b=[b] (HourArrow=[arrow]), M=[M] -> 5([m])+[r] (M[xx], MinArrow=[arrow]), T=[T] -> C=[Cd] (90+360([q])+45([d-1])+[c])`
- **Example (`23:08`):** `Calc: H=23 -> b=5 (HourArrow=↓), M=08 -> 5(1)+3 (M05, MinArrow=↑), T=1388 -> C=C5 (90+360(3)+45(4)+38)`
- **Final Check:**
  - **MINUTE GRID:** Always place the hour arrow, never the minute-range arrow. Confirm `M[xx]` matches `5×m`.
  - **HOUR GRID:** Show `H[b]` separately from `A[b]`.
  - **ARROW GRID:** Show `A[b]` and its minute-range direction.

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
- **Trigger:** If `INPUT TIME` is `now`, use the user's current local time in 24-hour `HH:MM` format before running Rules 1–6 (if local time is unavailable, ask the user for their current time).

MODE: FULL
INPUT TIME: now
version 3.
//

## SYSTEM OVERVIEW

| Grid | Code | Modular Cycle |
| :--- | :--- | :--- |
| **Minute Grid** | `M` | `00:05m + 1h` (`m=0–11`, `h=0–23`, `24:xx = 00:xx`) |
| **CODGRID** | `C` | `Start(Cd) + (6q hours) + (c mins)` (`q=0–3`, `c=00–44`) |
| **Hour Grid** | `H` | `0b:xx + 6h` (`b=1–6`, `h=0–3`) |
| **Arrow Grid** | `A` | `0b:xx + 6h` (`b=1–6`, `h=0–3`) |

## STRICT FORMAT LOCK
- **Speed Bump First:** Always output the 1-line `Calc:` scratchpad before any check line or template. Never skip it.
- **If `MODE: SHORT`:** Output **ONLY** the `Calc:` line(s) and the check line(s) (`Time -> HourArrow in Mxx, Cd, Hb, Ab (MinArrow) (+r mins)`). Do not output the template.
- **If `MODE: FULL`:** Output the `Calc:` line(s) and **1 check line** per input time, then the filled TEMPLATE inside **ONE** ```` ```text ```` block. Strip the `01|`–`14|` prefixes and keep the exact leading spaces, line count, and left/right layout.
- **3-char boxes (`C`, `H`):** active = `[ X ]`, empty = `[   ]`.
- **4-char boxes (`M`, `A`):** empty = `[    ]`, 1 arrow = `[ ↑  ]`, 2 arrows = `[ ↖↘ ]`, 3 arrows = `[ ←↑→]`, 4 arrows = `[←↑↓→]`.
- **CLEANUP:** Never leave an inactive placeholder containing its original label (`[C...]`, `[H...]`, `[M...]`, `[A...]`). Replace all unused boxes with blank spaces (`[   ]` or `[    ]`) while preserving exact brackets and spacing.

## MULTIPLE INPUT RULE
1. Treat every input time (`H=00–23`, `M=00–59`) as an independent calculation; never combine or average inputs.
2. Place all results into ONE shared template, preserving every active position without overwriting earlier inputs.
3. If multiple inputs share a box, keep `[ X ]` for `C`/`H`. For `M` and `A`, deduplicate identical arrows (each unique arrow appears only once), then sort `M` (`↖ ↑ ↗ ↙ ↓ ↘`) and `A` (`← ↑ ↓ →`).

## COMPACT MODULAR RULES
For each input time (`24:xx = 00:xx`):
1. **HOUR SLOT (`b` = 1–6):** Solve `H = b + 6h` (`h=0–3`; exception: `H=00` uses `b=6, h=-1`).
   - `b=1` (`↖`) | `b=2` (`↑`) | `b=3` (`↗`) | `b=4` (`↙`) | `b=5` (`↓`) | `b=6` (`↘`)
2. **MINUTE GRID (`Mxx`):** Solve `M = 5m + r` (`m=0–11`, `r=0–4`).
   - Map: `0→M00`, `1→M05`, `2→M10`, `3→M15`, `4→M20`, `5→M25`, `6→M30`, `7→M35`, `8→M40`, `9→M45`, `10→M50`, `11→M55`.
   - Locked Grid Layout: `R1: M05, M10, M15` | `R2: M20, M25, M30` | `R3: M35, M40, M45` | `R4: M50, M55, M00`
   - **Rule:** Always place the **hour arrow** (`↖ ↑ ↗ ↙ ↓ ↘`) in `[Mxx ]`, never the minute-range arrow. Show `(+r mins)` in the check line.
3. **HOUR GRID (`Hb`):** Show `H[b]` separately from `A[b]` and place `[ X ]` in `[Hb ]`.
4. **ARROW GRID (`Ab`):** Show `A[b]` and its **minute-range direction** (`00–14`=`↑`, `15–29`=`→`, `30–44`=`↓`, `45–59`=`←`). Never place the hour arrow in `[Ab ]`.
5. **CODGRID (`Cd`):** Match time to `Start(Cd) + (6q hours) + (c mins)` (`q=0–3`, `0 ≤ c ≤ 44` mins; if total ≥ `24:00`, subtract `24` hours):
   - `C1`: `01:30` | `C2`: `02:15` | `C3`: `03:00` | `C4`: `03:45`
   - `C5`: `04:30` | `C6`: `05:15` | `C7`: `06:00` | `C8`: `06:45`
   - Verify `0 ≤ c ≤ 44`, then place `[ X ]` in `[Cd ]`.
6. **CLOCK:** Never modify `(00)`, `(45)`, `(15)`, `(30)`. Replace `<input>` with the input time(s).

## VERIFICATION LOCK (MANDATORY SPEED BUMP)
Slow down and write a 1-line scratchpad (`Calc: ...`) before every check line to force step-by-step math:
- **Format:** `Calc: H=[H] -> b=[b] (HourArrow=[arrow]), M=[M] -> 5([m])+[r] (M[xx], MinArrow=[arrow]), C=[Cd] ([Start]+[6q]h+[c]m)`
- **Example (`23:08`):** `Calc: H=23 -> b=5 (HourArrow=↓), M=08 -> 5(1)+3 (M05, MinArrow=↑), C=C5 (04:30+18h+38m)`
- **Final Check:**
  - **MINUTE GRID:** Always place the hour arrow, never the minute-range arrow. Confirm `M[xx]` matches `5×m`.
  - **HOUR GRID:** Show `H[b]` separately from `A[b]`.
  - **ARROW GRID:** Show `A[b]` and its minute-range direction.

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
13|                            [A1 ] | [A2 ] | [A3 ]
14|                            [A4 ] | [A5 ] | [A6 ]

## CURRENT TIME RULE
- **Trigger:** If `INPUT TIME` is `now`, use the user's current local time in 24-hour `HH:MM` format before running Rules 1–6 (if local time is unavailable, ask the user for their current time).

MODE: FULL
INPUT TIME: now

version 1







[C1 ] | [C2 ] | [C3 ] | [C4 ]
      [C5 ] | [C6 ] | [C7 ] | [C8 ]

Row 1: 1:30 | 2:15 | 3:00 | 3:45
Row 2: 4:30 | 5:15 | 6:00 | 6:45

1=7=13=19
2=8=14=20
3=9=15=21
4=10=16=22
5=11=17=23
6=12=18=00

[C1 ]=1:30
[C2 ]=2:15
[C3 ]= 3:00
[C4 ]= 3:45
[C5 ]=4:30
[C6 ]= 5:15 
[C7 ]= 6:00 
[C8 ]= 6:45

hours
1=7=13=19
2=8=14=20
3=9=15=21
4=10=16=22
5=11=17=23
6=12=18=00
////


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


