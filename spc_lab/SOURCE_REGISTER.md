# ATC Simulator — Source Register

**Project:** Baghdad ACC / Iraq ATC Training Simulator  
**Register status:** Working source-of-truth index  
**Last updated:** 2026-10-09

## 1. Purpose

This register records the documents used to design, validate, and implement the Baghdad ACC training simulator. It separates:

1. **Current operational/regulatory data** — used for present-day Iraqi airspace, routes, points, frequencies, restrictions and applicable procedures.
2. **Local historical procedure/training baselines** — useful for Baghdad-specific training intent and workflows, but not automatically current.
3. **System/HMI references** — used to reproduce controller workflow, display behaviour and simulator roles without claiming TopSky product equivalence or certification.
4. **Engineering/support references** — map generation, recording/replay, strips, supervision and data-preparation material.

No historical TopSky/Thales or 2015 local document shall override a current Iraq AIP/AIRAC publication or current approved local instruction.

---

## 2. Authority / applicability hierarchy

| Tier | Source class | Use |
|---|---|---|
| A | Current Iraq AIP/AIRAC / ICAA publications | Current airspace, routes, fixes, navaids, frequencies, restrictions, published ATS procedures |
| B | Current approved local instructions / LOAs / unit orders | Current Baghdad ACC local operating procedures and coordination |
| C | UTP / LATSI historical baselines | Training objectives, assessment structure, historical local procedures |
| D | Baghdad-specific Thales system documents | Baghdad delivered-system behaviour, architecture and simulator/HMI reference |
| E | General TopSky product handbooks / course modules | Product workflow/HMI reference where Baghdad-specific material is absent |
| F | Prototype observations / controller screenshots | Visual/interaction evidence; must be labelled as observed and not treated as authoritative logic |

---

## 3. Core Iraq / Baghdad sources

| ID | Document | Revision / date | Authority / scope | Project use | Status / caution |
|---|---|---|---|---|---|
| **REG-01** | **Iraq AIP — ENR compilation (`462.pdf`)** | Mixed page effective dates; pages observed from 2014–2026 | Iraq Civil Aviation Authority; ENR airspace, ATS routes, significant points, navaids, surveillance, restricted/prohibited areas, charts | **Primary source for current Iraq map/navdata baseline**; FIR/sector boundaries, routes, fixes, airports/navaids, ENR 5 areas, published ACC channels | Treat as a compiled snapshot with mixed amendment dates. Before any operational/current claim, verify the specific page against the latest effective AIRAC/AIP. |
| **PROC-01** | **Local Air Traffic Services Instructions (LATSI)** | Version 2.8; Amendment 9; Dec 2015 | ICAA; Baghdad ACC local ATS instructions | Baghdad-specific local procedure baseline, coordination, sector working methods, operational terminology | **Historical baseline**, not current authority by default. Must be reconciled with current AIP/LOA/local instructions. |
| **TRN-01** | **Baghdad ATC Unit Training Plan** | Version 3.0; Amendment 0; 22 Sep 2015 | ICAA; BACC controller unit training, OJT/OJTI, certification and assessment | **Primary training-design baseline**: learning objectives, observable behaviour, assessment and exercise acceptance criteria | Historical training baseline. Use to structure training, then validate against current unit requirements. |
| **SYS-00** | **Overview of TopSky-ATC and Demo V1 / ATC Workshop** | Baghdad, 05 Mar 2024 | TopSky-ATC overview/demo material | Modern high-level product architecture, system concepts, safety nets, flight data, HMI terminology | Overview/demo only; not evidence of Baghdad configuration or certification. |

---

## 4. Baghdad-specific Thales system documents

| ID | Document | Revision / date | Scope | Project use | Priority |
|---|---|---|---|---|---|
| **SYS-01** | **Reference_Baghdad-SSS_20170905_DR.pdf** — System Segment Specification for TopSky-ATC Baghdad | Rev A; Nov 2014; 290 pages | Baghdad-specific system segment specification | **Highest-value architecture/configuration reference**: delivered system functions, interfaces, operational/simulation context, subsystem behaviour | **P0** |
| **SYS-02** | **Baghdad LEADER OH** | Rev -; Sep 2014 | Baghdad-specific Leader Position Operational Handbook | Instructor/training-manager commands, exercise/session control and Baghdad simulator workflow | **P0** |
| **SYS-03** | **Baghdad DATAPREP OH** | Rev -; Sep 2014 | Baghdad-specific Data Preparation | Exercise data preparation and distribution; relationship between fixed configuration and scenario data | P1 |

---

## 5. Controller HMI / surveillance / flight-data references

| ID | Document | Revision / date | What it supports in our simulator | Priority |
|---|---|---|---|---|
| **HMI-01** | **CP OH — Controller Position Operational Handbook** | Rev K; Nov 2013; 290 pages | Main controller position behaviour: track labels, popup menus, ASD functions, flight data, coordination, handoff, alerts, windows and controller workflow | **P0** |
| **HMI-02** | **Mod03 — HMI Presentation** | TopSky operational course, circa 2014 | Menu bar and main HMI layout. Explicit menu order includes **System, ASD Tools, Tracks, ASD Bg, Data Link, Flight Data, Flight Lists, Information, Messages** | **P0** |
| **HMI-03** | **Mod04 — Controller Keyboard** | TopSky operational course, circa 2014 | Keyboard/shortcut semantics. Explicitly documents **HIST**, **SPD**, **DAPs**, LOST, OVR, graphical controls and FPL functions | **P0** |
| **HMI-04** | **Mod05 — HMI Control Functions** | TopSky operational course, circa 2014 | Range/scale, map selection, distance/bearing measurement, filters and other ASD control functions | **P0** |
| **HMI-05** | **Mod06 — Radar Tracks** | TopSky operational course, circa 2014 | **Track symbols, colours, labels, track components and popup menus**. Confirms green designated track, light-blue correlated/controlled, blue correlated/not controlled, white uncorrelated, red safety alerts | **P0** |
| **HMI-06** | **Mod07 — FPL Management** | TopSky operational course, circa 2014 | Flight-plan lifecycle, correlation/de-correlation, active/pre-active/pending/coordinated states, handoff context | **P0** |
| **HMI-07** | **Mod09 — Setup & Operational Supervision** | TopSky operational course, circa 2014 | Controller display configuration. Explicitly supports **1–10 past positions**, speed vectors, label character sizes, label-overlap avoidance and leader-line settings | P1 |
| **HMI-08** | **Mod10 — Planner Functions** | TopSky operational course, circa 2014 | Flight lists, sector inbound/arrival/departure/active lists, handoff list states, planner role | P1 |
| **HMI-09** | **Mod13 — AGDL** | TopSky operational course, circa 2014 | ADS-C / CPDLC / AGDL behaviour if data-link training is later included | P2 |

---

## 6. Safety nets

| ID | Document | Revision / date | Scope | Project use |
|---|---|---|---|---|
| **SAFE-01** | **Mod12 — Safety Nets Alerts** | TopSky operational course, circa 2014 | STCA, MSAW, AFDA, DAIW, AIW, RVSM, CLAM, RNP, NLA, RAM, DUPE, emergency indications and alert HMI | **Primary HMI/workflow reference for safety-net presentation** |
| **SAFE-02** | **CP OH Rev K** | Nov 2013 | Controller interaction with alerts and label indications | Cross-check popup/action behaviour and alert presentation |
| **SAFE-03** | **Reference Baghdad SSS Rev A** | Nov 2014 | Baghdad configuration/system requirements | Check whether a safety-net capability/configuration applies to the Baghdad delivery |
| **REG-01** | **Current Iraq AIP/AIRAC** | Mixed/current pages | Current regulatory/procedural context | Safety-net parameters must **not** be inferred from course screenshots. Operational thresholds require verified local/configuration data. |

**Rule:** STCA and other safety nets are training aids/safety barriers, not substitutes for separation logic. Simulator scoring and separation minima must come from verified procedures, not from alert-trigger assumptions.

---

## 7. Simulator / instructor / pseudo-pilot references

| ID | Document | Revision / date | Scope | Project use | Priority |
|---|---|---|---|---|---|
| **SIM-01** | **Mod20 — Simulation** | TopSky operational course, circa 2014 | Simulator architecture and roles | Confirms training roles: **Training Manager, Trainee, Pilot**; simulated radar/FPL inputs; recording; start/stop/freeze/resume/replay concepts | **P0** |
| **SIM-02** | **Mod21 — Leader Position** | TopSky operational course, circa 2014 | Leader/instructor session management | Session/game/exercise state, start/stop, freeze/resume/replay, exercise speed and radar simulation controls | **P0** |
| **SIM-03** | **Mod22 — Pilot Position** | TopSky operational course, circa 2014 | Pseudo-pilot HMI | Aircraft control by heading, level, speed; traffic/strip/function/report areas; history and speed-vector controls | **P0** |
| **SYS-02** | **Baghdad LEADER OH** | Rev -; Sep 2014 | Baghdad-specific Leader position | Prefer over general course where details conflict | **P0** |
| **TRN-01** | **Baghdad UTP v3.0** | Sep 2015 | Training objectives/assessment | Determines what simulator exercises must teach and what instructors observe | **P0** |

---

## 8. Recording, replay and debrief

| ID | Document | Revision / date | Scope | Project use |
|---|---|---|---|---|
| **REC-01** | **RECREP OH — Recording/Replay Operational Handbook** | Rev J; Nov 2013 | Product recording/replay | Reference for synchronized state capture, replay controls and archive concepts |
| **REC-02** | **Mod18 — Recording/Replay** | TopSky operational course, circa 2014 | Operational/training use of replay | Important design reference for debrief: recorded surveillance, system state, FPL data, controller screen settings; passive vs interactive replay |
| **SIM-01** | **Mod20 — Simulation** | circa 2014 | Training recording | Links recording/replay to assessment and formative training |

**Project direction:** our implementation should record deterministic simulator events/state sufficient to reproduce a run and synchronize controller actions/communications for debrief, without copying legacy storage architecture unnecessarily.

---

## 9. Map / display-generation references

| ID | Document | Revision / date | Scope | Project use |
|---|---|---|---|---|
| **MAP-01** | **MAPGEN OH** | Rev F; Mar 2014 | TopSky map generator | Reference for how operational map entities/layers are organized and generated |
| **MAP-02** | **MOSAICGEN OH** | Rev D; Feb 2013 | Mosaic generation | Reference where mosaic/display background generation is relevant |
| **REG-01** | **Iraq AIP/AIRAC ENR** | mixed/current pages | Authoritative aeronautical geography | **Actual source for simulator airspace/navdata**, not MAPGEN geometry |
| **PROC-01** | **LATSI 2.8** | Dec 2015 | Local sector/procedure context | Historical cross-check only |

**Rule:** use WGS84/versioned simulator data derived from verified AIP/AIRAC coordinates. Do not copy visual geometry from screenshots when coordinate data exists.

---

## 10. Flight strips and support/supervision

| ID | Document | Revision / date | Scope | Project use |
|---|---|---|---|---|
| **STRIP-01** | **STRIPGEN OH** | Rev C; Feb 2013 | Strip Format Generator | Reference if electronic/printed strip training is included |
| **TSUP-01** | **TSUP OH** | Rev J; Nov 2013 | Technical Supervisor Position | Degraded-mode/system supervision concepts; admin/technical role reference |
| **DATA-01** | **DATAGEN OH** | Rev J; Nov 2013 | Data Generator | Offline data/configuration-generation concepts; useful for versioned datasets and configuration tooling |
| **DATA-02** | **Baghdad DATAPREP OH** | Rev -; Sep 2014 | Baghdad data preparation | Scenario/exercise data preparation reference |

---

## 11. Current prototype observations — evidence class F

The following are currently accepted as **observed HMI behaviour** from controller screenshots/user operational observation. They may be implemented in the isolated HMI prototype, but must be cross-checked against the documents above before being treated as system requirements:

- Track/data-block leader dynamically connects to the nearest label edge.
- DAPS controls expanded Mode-S fields; SA remains visible when DAPS is off.
- History/past-position dots behind the track.
- Forward speed/prediction vector.
- Right-click popup menu; field-specific interactions.
- Track designation/selection colour.
- Other-sector tracks shown blue.
- Handover-pending label flashing.
- STCA alert text shown in red above the label.
- Range/bearing/separation information box and draggable display behaviour.

Where the course/handbook confirms an observed behaviour, promote it from “observed” to “documented” in future revisions of this register.

---

## 12. Source-to-feature matrix

| Simulator feature | Primary source(s) | Secondary source(s) |
|---|---|---|
| Iraq FIR / sectors / routes / fixes / navaids / prohibited areas | REG-01 Iraq AIP/AIRAC | MAP-01 for display concepts only |
| Controller main ASD/HMI | HMI-01 CP OH, HMI-02 Mod03, HMI-05 Mod06 | 2024 TopSky overview, screenshots |
| DAPS / Mode-S label behaviour | HMI-03 Mod04, HMI-05 Mod06 | Iraq AIP ENR 1.6 for current Mode-S surveillance capability |
| History trail / speed vector | HMI-03 Mod04, HMI-07 Mod09 | HMI-05 Mod06, screenshots |
| Track colours / ownership indication | HMI-05 Mod06 | HMI-01 CP OH, screenshots |
| Handover / coordination | HMI-01 CP OH, HMI-06 Mod07, HMI-08 Mod10 | LATSI after applicability check |
| Flight plan lifecycle / lists | HMI-06 Mod07, HMI-08 Mod10 | CP OH |
| STCA / safety nets | SAFE-01 Mod12, SAFE-02 CP OH | SYS-01 Baghdad SSS, current local configuration |
| Instructor / exercise control | SYS-02 Baghdad LEADER, SIM-02 Mod21 | SIM-01 Mod20 |
| Pseudo-pilot | SIM-03 Mod22 | SIM-01 Mod20 |
| Recording / replay / debrief | REC-01 RECREP, REC-02 Mod18 | SIM-01 Mod20 |
| Training objectives / assessment | TRN-01 UTP v3.0 | Current unit training requirements when obtained |
| Local Baghdad procedures | Current local instructions/LOAs when obtained | PROC-01 LATSI 2.8 historical baseline |
| Map authoring / data preparation | REG-01 + MAP-01 + DATA-02 | DATA-01 DATAGEN |
| Technical/degraded operations | TSUP-01 | Baghdad SSS |

---

## 13. Documents to keep attached to the ATC Simulator project

### P0 — attach / keep immediately
- UTP Version 3
- LATSI Version 2.8
- Iraq AIP ENR compilation (`462.pdf`)
- Overview of TopSky-ATC and Demo V1
- Baghdad LEADER OH
- Reference_Baghdad-SSS_20170905_DR.pdf
- CP OH (Rev K).pdf
- Mod03 HMI Presentation
- Mod04 Controller Keyboard
- Mod05 HMI Control
- Mod06 Radar Tracks
- Mod07 FPL Management
- Mod12 Safety Nets Alerts
- Mod18 Recording/Replay
- Mod20 Simulation
- Mod21 Leader Position
- Mod22 Pilot Position

### P1 — keep in Drive; attach when phase starts or if project capacity permits
- MAPGEN OH
- MOSAICGEN OH
- RECREP OH
- TSUP OH
- DATAGEN OH
- Baghdad DATAPREP OH
- Mod09 Setup and Operational Supervision
- Mod10 Planner Functions
- Mod13 AGDL
- STRIPGEN OH

### P2 — archive in Drive until needed
- Remaining TopSky course modules and lower-frequency support documents.

---

## 14. Open source gaps

The current library is strong for the 2013–2015 TopSky/Baghdad baseline, but the following must still be obtained/verified for a credible **current** Baghdad ACC simulator:

1. Latest effective Iraq AIP/AIRAC pages/data used by the simulator build.
2. Current Baghdad ACC local instructions and sector operating procedures.
3. Current LOAs with adjacent ATS units/sectors where training scenarios depend on them.
4. Current sectorization/frequencies if different from the effective AIP pages already held.
5. Current training objectives/assessment requirements if the 2015 UTP has been superseded.
6. Any current local safety-net configuration values if exercises are expected to reproduce actual alert thresholds.
7. Current surveillance coverage/update characteristics if sensor-degradation exercises require operational fidelity.

Until those are available, the simulator shall clearly separate:
- **current AIP-derived data**,
- **historical Baghdad/TopSky baseline**, and
- **synthetic training assumptions**.
