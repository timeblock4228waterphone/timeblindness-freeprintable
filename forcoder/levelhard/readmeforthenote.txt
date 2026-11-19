
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

INPUT TIME: now


///

[M05 ]=
