---
name: league-config
description: League registry — ESPN endpoints, Kalshi series tickers, team alias maps, season windows, polling ramp rules
leagues: [nfl, nba, mlb]
last_updated: 2026-07-17
---

# League Configuration

This file is the single source of truth for league identity, entity matching, and
season scheduling. The `espn-data`, `league-matching`, and orchestrator polling-ramp
logic all read from here (via the vault skill — never directly on a live cycle).

**Verification flags:** Kalshi ticker abbreviations below are best-effort drafts.
Rows marked ⚠ must be verified against live Kalshi market tickers (via
`GET /trade-api/v2/markets?series_ticker=...`) during Phase 3 before the
league-matching skill treats this table as authoritative. ESPN abbreviations are
stable and well-documented but should be spot-checked once against a live
scoreboard response per league.

---

## NFL

| Key | Value |
|---|---|
| League ID | `nfl` |
| ESPN sport/league slug | `football/nfl` |
| ESPN scoreboard | `https://site.api.espn.com/apis/site/v2/sports/football/nfl/scoreboard` |
| ESPN summary (win prob) | `https://site.api.espn.com/apis/site/v2/sports/football/nfl/summary?event={event_id}` (win probability in `winprobability[]`) |
| Kalshi game series ticker | `KXNFLGAME` ⚠ verify at runtime |
| Season window | Preseason: early Aug – late Aug. Regular season: first Thu after Labor Day – early Jan. Playoffs: mid Jan – Super Bowl (2nd Sun of Feb). **Off-season: mid-Feb – July.** |
| Game days | Primarily Thu/Sun/Mon (regular season); Sat late-season |
| Typical game length | ~3h 10m real time |
| 2026 season notes | 2026 regular season expected to open Thu Sep 10, 2026. Currently OFF-SEASON as of 2026-07-17. |

### NFL team alias map

Columns: Kalshi ticker abbr (⚠ = unverified guess) | ESPN abbr | ESPN display name | Common names/aliases

| Kalshi | ESPN | ESPN display name | Common names |
|---|---|---|---|
| ARI ⚠ | ARI | Arizona Cardinals | Cardinals, Cards, Arizona |
| ATL ⚠ | ATL | Atlanta Falcons | Falcons, Atlanta |
| BAL ⚠ | BAL | Baltimore Ravens | Ravens, Baltimore |
| BUF ⚠ | BUF | Buffalo Bills | Bills, Buffalo |
| CAR ⚠ | CAR | Carolina Panthers | Panthers, Carolina |
| CHI ⚠ | CHI | Chicago Bears | Bears, Chicago |
| CIN ⚠ | CIN | Cincinnati Bengals | Bengals, Cincy, Cincinnati |
| CLE ⚠ | CLE | Cleveland Browns | Browns, Cleveland |
| DAL ⚠ | DAL | Dallas Cowboys | Cowboys, Dallas |
| DEN ⚠ | DEN | Denver Broncos | Broncos, Denver |
| DET ⚠ | DET | Detroit Lions | Lions, Detroit |
| GB ⚠ | GB | Green Bay Packers | Packers, Green Bay, Pack |
| HOU ⚠ | HOU | Houston Texans | Texans, Houston |
| IND ⚠ | IND | Indianapolis Colts | Colts, Indy, Indianapolis |
| JAX ⚠ | JAX | Jacksonville Jaguars | Jaguars, Jags, Jacksonville (alt abbr: JAC) |
| KC ⚠ | KC | Kansas City Chiefs | Chiefs, Kansas City, KC |
| LAC ⚠ | LAC | Los Angeles Chargers | Chargers, LA Chargers, Bolts |
| LAR ⚠ | LAR | Los Angeles Rams | Rams, LA Rams (alt abbr: LA) |
| LV ⚠ | LV | Las Vegas Raiders | Raiders, Vegas, Las Vegas (alt abbr: LVR) |
| MIA ⚠ | MIA | Miami Dolphins | Dolphins, Fins, Miami |
| MIN ⚠ | MIN | Minnesota Vikings | Vikings, Vikes, Minnesota |
| NE ⚠ | NE | New England Patriots | Patriots, Pats, New England |
| NO ⚠ | NO | New Orleans Saints | Saints, New Orleans |
| NYG ⚠ | NYG | New York Giants | Giants, NY Giants |
| NYJ ⚠ | NYJ | New York Jets | Jets, NY Jets |
| PHI ⚠ | PHI | Philadelphia Eagles | Eagles, Philly, Philadelphia |
| PIT ⚠ | PIT | Pittsburgh Steelers | Steelers, Pittsburgh |
| SEA ⚠ | SEA | Seattle Seahawks | Seahawks, Hawks, Seattle |
| SF ⚠ | SF | San Francisco 49ers | 49ers, Niners, San Francisco |
| TB ⚠ | TB | Tampa Bay Buccaneers | Buccaneers, Bucs, Tampa Bay, Tampa |
| TEN ⚠ | TEN | Tennessee Titans | Titans, Tennessee |
| WSH ⚠ | WSH | Washington Commanders | Commanders, Washington (MLB verification 2026-07-17 showed Kalshi uses WSH there — WSH is now the best guess here too, but verify NFL live before trusting) |

---

## NBA

| Key | Value |
|---|---|
| League ID | `nba` |
| ESPN sport/league slug | `basketball/nba` |
| ESPN scoreboard | `https://site.api.espn.com/apis/site/v2/sports/basketball/nba/scoreboard` |
| ESPN summary (win prob) | `https://site.api.espn.com/apis/site/v2/sports/basketball/nba/summary?event={event_id}` |
| Kalshi game series ticker | `KXNBAGAME` ⚠ verify at runtime (may be `KXNBA`) |
| Season window | Preseason: early Oct. Regular season: ~Oct 21 – mid Apr. Play-in/Playoffs: mid Apr – Finals (mid Jun). **Off-season: late Jun – Sep.** |
| Game days | Daily during season, heavy Tue/Wed/Fri/Sat slates |
| Typical game length | ~2h 20m real time |
| 2026 season notes | 2026-27 season expected to tip ~Oct 20, 2026. Currently OFF-SEASON as of 2026-07-17 (Summer League games exist but are NOT tradeable slate — exclude). |

### NBA team alias map

**Important:** the NBA is where ESPN's abbreviations diverge most from the standard/
Kalshi-style ones. Divergent rows are bolded. Matching on abbreviation alone WILL
mis-match here — this table is mandatory.

| Kalshi | ESPN | ESPN display name | Common names |
|---|---|---|---|
| ATL ⚠ | ATL | Atlanta Hawks | Hawks, Atlanta |
| BOS ⚠ | BOS | Boston Celtics | Celtics, Boston, C's |
| BKN ⚠ | BKN | Brooklyn Nets | Nets, Brooklyn |
| CHA ⚠ | CHA | Charlotte Hornets | Hornets, Charlotte |
| CHI ⚠ | CHI | Chicago Bulls | Bulls, Chicago |
| CLE ⚠ | CLE | Cleveland Cavaliers | Cavaliers, Cavs, Cleveland |
| DAL ⚠ | DAL | Dallas Mavericks | Mavericks, Mavs, Dallas |
| DEN ⚠ | DEN | Denver Nuggets | Nuggets, Denver |
| DET ⚠ | DET | Detroit Pistons | Pistons, Detroit |
| **GSW ⚠** | **GS** | Golden State Warriors | Warriors, Golden State, Dubs |
| HOU ⚠ | HOU | Houston Rockets | Rockets, Houston |
| IND ⚠ | IND | Indiana Pacers | Pacers, Indiana |
| LAC ⚠ | LAC | LA Clippers | Clippers, LA Clippers |
| LAL ⚠ | LAL | Los Angeles Lakers | Lakers, LA Lakers |
| MEM ⚠ | MEM | Memphis Grizzlies | Grizzlies, Grizz, Memphis |
| MIA ⚠ | MIA | Miami Heat | Heat, Miami |
| MIL ⚠ | MIL | Milwaukee Bucks | Bucks, Milwaukee |
| MIN ⚠ | MIN | Minnesota Timberwolves | Timberwolves, Wolves, Minnesota |
| **NOP ⚠** | **NO** | New Orleans Pelicans | Pelicans, Pels, New Orleans |
| **NYK ⚠** | **NY** | New York Knicks | Knicks, New York |
| OKC ⚠ | OKC | Oklahoma City Thunder | Thunder, OKC |
| ORL ⚠ | ORL | Orlando Magic | Magic, Orlando |
| PHI ⚠ | PHI | Philadelphia 76ers | 76ers, Sixers, Philly |
| PHX ⚠ | PHX | Phoenix Suns | Suns, Phoenix |
| POR ⚠ | POR | Portland Trail Blazers | Trail Blazers, Blazers, Portland |
| SAC ⚠ | SAC | Sacramento Kings | Kings, Sacramento |
| **SAS ⚠** | **SA** | San Antonio Spurs | Spurs, San Antonio |
| TOR ⚠ | TOR | Toronto Raptors | Raptors, Raps, Toronto |
| **UTA ⚠** | **UTAH** | Utah Jazz | Jazz, Utah |
| **WAS ⚠** | **WSH** | Washington Wizards | Wizards, Washington |

---

## MLB

| Key | Value |
|---|---|
| League ID | `mlb` |
| ESPN sport/league slug | `baseball/mlb` |
| ESPN scoreboard | `https://site.api.espn.com/apis/site/v2/sports/baseball/mlb/scoreboard` |
| ESPN summary (win prob) | `https://site.api.espn.com/apis/site/v2/sports/baseball/mlb/summary?event={event_id}` |
| Kalshi game series ticker | `KXMLBGAME` ✅ verified live 2026-07-17 |
| Kalshi ticker grammar | ✅ verified live 2026-07-17: event ticker `KXMLBGAME-{YY}{MON}{DD}{HHMM}{AWAY}{HOME}` where `{HHMM}` is scheduled start in ET (e.g. `KXMLBGAME-26JUL191920LADNYY` = LAD @ NYY, Jul 19 2026, 7:20 PM ET); market ticker appends `-{TEAM}` for the YES side (`...-NYY`). The embedded start time disambiguates doubleheaders directly. Market titles are truncated ("New York Y") — never match on title text, only ticker abbreviations. |
| Season window | Spring training: late Feb – late Mar. Regular season: late Mar – late Sep. Playoffs: Oct – World Series (late Oct/early Nov). **Off-season: Nov – mid-Feb.** |
| Game days | Daily, typically 8–15 games/day; All-Star break ~mid-July (4 quiet days) |
| Typical game length | ~2h 40m real time (pitch clock era) |
| 2026 season notes | **IN-SEASON as of 2026-07-17** (just past All-Star break — this is the live-testing league for the build). |

### MLB team alias map

| Kalshi | ESPN | ESPN display name | Common names |
|---|---|---|---|
| ARI ⚠ | ARI | Arizona Diamondbacks | Diamondbacks, D-backs, Arizona |
| ATL ⚠ | ATL | Atlanta Braves | Braves, Atlanta |
| BAL ⚠ | BAL | Baltimore Orioles | Orioles, O's, Baltimore |
| BOS ⚠ | BOS | Boston Red Sox | Red Sox, Sox (ambiguous — see CHW), Boston |
| **CHC ⚠** | **CHC** | Chicago Cubs | Cubs, Cubbies, Chicago Cubs |
| **CHW ⚠** | **CHW** | Chicago White Sox | White Sox, Chicago White Sox (alt abbr: CWS — ⚠ known divergence risk) |
| CIN ⚠ | CIN | Cincinnati Reds | Reds, Cincinnati |
| CLE ⚠ | CLE | Cleveland Guardians | Guardians, Cleveland |
| COL ⚠ | COL | Colorado Rockies | Rockies, Colorado |
| DET ⚠ | DET | Detroit Tigers | Tigers, Detroit |
| HOU ⚠ | HOU | Houston Astros | Astros, Houston |
| KC ⚠ | KC | Kansas City Royals | Royals, Kansas City (alt abbr: KCR) |
| LAA ⚠ | LAA | Los Angeles Angels | Angels, Halos, LA Angels |
| LAD ⚠ | LAD | Los Angeles Dodgers | Dodgers, LA Dodgers |
| MIA ⚠ | MIA | Miami Marlins | Marlins, Miami |
| MIL ⚠ | MIL | Milwaukee Brewers | Brewers, Brew Crew, Milwaukee |
| MIN ⚠ | MIN | Minnesota Twins | Twins, Minnesota |
| NYM ⚠ | NYM | New York Mets | Mets, NY Mets |
| NYY ⚠ | NYY | New York Yankees | Yankees, Yanks, NY Yankees |
| **ATH ✅** | **ATH** | Athletics | A's, Athletics, Sacramento Athletics (✅ Kalshi ATH verified live 2026-07-17; franchise relocated 2025 — legacy abbr OAK may still appear in some data sources) |
| PHI ⚠ | PHI | Philadelphia Phillies | Phillies, Phils, Philadelphia |
| PIT ⚠ | PIT | Pittsburgh Pirates | Pirates, Bucs (ambiguous — see TB NFL), Pittsburgh |
| SD ⚠ | SD | San Diego Padres | Padres, San Diego (alt abbr: SDP) |
| SEA ⚠ | SEA | Seattle Mariners | Mariners, M's, Seattle |
| SF ⚠ | SF | San Francisco Giants | Giants (ambiguous — see NYG NFL), San Francisco (alt abbr: SFG) |
| STL ⚠ | STL | St. Louis Cardinals | Cardinals (ambiguous — see ARI NFL), Cards, St. Louis |
| TB ⚠ | TB | Tampa Bay Rays | Rays, Tampa Bay (alt abbr: TBR) |
| TEX ⚠ | TEX | Texas Rangers | Rangers (ambiguous — NY Rangers NHL), Texas |
| TOR ⚠ | TOR | Toronto Blue Jays | Blue Jays, Jays, Toronto |
| WSH ✅ | WSH | Washington Nationals | Nationals, Nats, Washington (✅ verified live 2026-07-17: Kalshi uses WSH, matching ESPN) |

**Ambiguity rule:** nickname-only matches ("Giants", "Cardinals", "Rangers", "Bucs",
"Sox") are NEVER sufficient across leagues. The league-matching skill must always
resolve within a single league context, and cross-league nickname collisions listed
above must fall through to abbreviation + start-time matching.

---

## Polling ramp rules (consumed by orchestrator in Phase 3)

| Phase | Condition | Poll cadence |
|---|---|---|
| Off-season | Outside league season window | Once daily (slate check only, cheap) |
| In-season, no games today | Season window active, empty slate | Every 6h |
| Pregame | Game today, T-minus > 60 min | Every 15 min |
| Pregame ramp | T-minus ≤ 60 min | Every 5 min |
| **Live** | Game in progress | **Every 20s** (scoreboard); win-prob/summary every 60s; tighten to 10s in final 5 min of NFL/NBA game or 8th inning onward in MLB |
| Final | ESPN status `STATUS_FINAL` | Stop; trigger postmortem skill once |

Cadence numbers are Category A drafts (rate-limit-driven, not money-driven) — tune
freely in Phase 3 against observed ESPN throttling.
