# ATC Simulator — Project State

**Last updated:** 2026-10-08  
**Project:** Baghdad ACC training simulator / Iraq aviation reference map  
**Purpose:** Durable project-state register for future conversations and implementation sessions.

## 1. Current architecture decision

Use a modular stack:

- **BlueSky** = headless simulation engine / aircraft truth and movement.
- **WebATM** = browser HMI / MapLibre / interaction layer.
- **Iraq Aviation Reference data** = independent versioned GeoJSON, not owned by BlueSky.
- **Controller / Instructor workspaces** = same application and state/backend, different presentation and allowed controls.

Do not return to the legacy BlueSky desktop UI as the primary controller HMI unless a future validation experiment shows a compelling reason.

## 2. Repositories / working branches

### BlueSky
Repository: `Salama70024/bluesky`  
Branch: `spike/webatm-iraq-lab`

Implemented:
- `scenario/SPC/Baghdad_ACC_Test_001.scn`
- six-aircraft Baghdad ACC test scenario
- WebATM integrated Docker quick-start
- spike documentation

Validated at runtime:
- WebATM ↔ BlueSky connection works.
- scenario loads.
- six aircraft move under LNAV.
- aircraft selection works.
- selected route display works.

### WebATM
Local working copy: `C:\CM\webatm-spc`  
Local branch: `spc-iraq-map`

Important: the latest WebATM changes described below are **local/uncommitted** unless explicitly pushed later.

## 3. Iraq aviation reference map baseline

The earlier standalone map **v0.2.2** is the visual/data baseline.

Core principles:
- purpose-built Iraq/Baghdad FIR display; not a world basemap with masks.
- WGS84 / EPSG:4326.
- aviation data independent from simulation engine.
- military / variable-use airspace kept separate from stable civil reference data.

Current reference data includes:
- Baghdad FIR boundary based on Iraq AIP ENR chart work.
- ENR 6-1 reporting points as the primary displayed FIX layer.
- ATS airways/routes.
- airports.
- navaids.
- three selected permanent circular prohibited/protected areas used in the project baseline: OR/P101, OR/P201, OR/P401.
- geographic context separated from aviation data.

Current data files used in WebATM:
- `WebATM/static/map/iraq-aviation-base.geojson`
- `WebATM/static/map/iraq-geographic-context.geojson`

Geographic context currently contains:
- 1 country-boundary polygon.
- 161 river LineStrings.

The Iraq aviation dataset remains the source of truth for the custom aviation overlay.

## 4. WebATM map integration completed

Custom components:
- `IraqAviationOverlay.ts`
- `IraqGeographicContextOverlay.ts`

Rendering concept:
1. plain training background
2. geographic context + WGS84 grid
3. Iraq aviation reference
4. BlueSky route/aircraft overlays

Global OpenFreeMap is **not** the SPC controller default anymore.
It may remain available only as a development/debug map style.

The SPC default map uses:
- simple blank/light background
- Iraq geographic context
- 1-degree WGS84 lat/lon grid
- Baghdad FIR / ATS network / AIP features

Camera:
- 2D Mercator only.
- no FIR-related minZoom lock.
- no SPC maxBounds zoom constraint.
- `Fit FIR` uses `fitBounds([[38.3,28.7],[48.9,37.8]])` with normal padding.
- normal manual zoom-in and zoom-out work.

## 5. Display-layer controls completed

Controller Display panel now controls custom Iraq layers.

### Geographic Context
- Iraq Boundary
- Rivers
- Lat/Lon Grid

### Aviation Reference — Iraq AIP
- Baghdad FIR
- ATS Airways
- Reporting Points / FIXes
- Airports (Iraq AIP)
- Navaids
- Permanent Prohibited Areas

Visibility affects both symbols and associated labels where applicable.
Visibility state survives MapLibre style reload.

Generic WebATM Airports/Waypoints controls were removed from the SPC display panel to avoid conflict with the Iraq AIP reference layers. Generic navdata remains in state but is off by default.

## 6. Controller / Instructor workspace separation

A presentation-level workspace architecture has been implemented locally in WebATM.

Detection:
- `?workspace=controller`
- `?workspace=instructor`
- default SPC mode = `controller`

New:
- `frontend/src/core/WorkspaceMode.ts`
- tests for workspace detection/application.

### Controller workspace
Header: **BAGHDAD ACC — CONTROLLER**

Visible:
- main map/radar area
- Traffic list
- Aircraft Info
- Conflicts panel
- Fit FIR / zoom controls
- compact/collapsible Iraq display controls
- minimal connection/status information

Hidden:
- Simulation Nodes / Add Node
- Create Aircraft
- Draw Shape / Draw Route
- Play / Pause / Reset / rate controls
- Command Console and debug logs
- upload/developer controls
- command palette

### Instructor workspace
Header badge: **BAGHDAD ACC — INSTRUCTOR**

For now it intentionally preserves the existing WebATM interface and simulator controls. Instructor redesign is deferred.

Both workspaces share the same:
- SocketManager
- StateManager
- MapDisplay
- Iraq overlays
- BlueSky connection
- aircraft state

Do not fork separate applications.

## 7. Runtime validation achieved

Validated:
- Docker Desktop + integrated WebATM image works.
- BlueSky auto-start and proxy connection works after removing the read-only nested BlueSky volume mount.
- six-aircraft Baghdad scenario loads.
- aircraft move.
- LNAV works.
- aircraft selection and route display work.
- Iraq aviation map renders inside WebATM.
- custom layer visibility controls work.
- free zoom and Fit FIR work.

## 8. Important design decision: simulator roles

The current direction is role-separated:

- **Controller**: operational radar/HMI, traffic labels, flight data, clearances, ownership/handover, safety-net indications.
- **Instructor**: scenario load/control, pause/resume/rate, traffic injection, failures/events, interventions, recording/replay and assessment.
- **Pseudo-pilot**: later; execution/readback/voice path.
- **Supervisor/Admin**: later if required.

Do not expose instructor simulation controls in the controller workspace.

## 9. Next recommended engineering phase

### NEXT: Controller Data Block + Cleared State

Do **not** jump immediately to voice, STCA, scenario editor redesign or scoring.

The next core model should separate:

**Aircraft truth / observed state**
- actual position
- actual altitude / FL
- actual track/heading
- actual speed
- vertical speed

from

**Controller-cleared state**
- cleared flight level
- cleared heading
- cleared speed
- route / direct-to state
- sector ownership / handover state later

Reason: this is the boundary between “aircraft moving on a map” and a credible ATC training simulator.

Initial implementation target:
- controller-oriented aircraft label/data block
- selected aircraft detail
- controlled clearance commands
- visible difference between actual and cleared state
- deterministic state/event recording

Safety-net logic, separation assessment, handover and scoring should build on this state model later.

## 10. Operational-reference notes

Use project source documents according to scope:

- **Iraq AIP / 462.pdf**: current/public airspace, routes, fixes, radar procedures, restrictions and current applicable data by effective date.
- **UTP**: learning objectives, trainee behaviors and assessment.
- **LATSI**: local documented procedures where applicable.
- **TopSky-ATC materials**: workflow/HMI behavior reference only; do not claim product equivalence or certification.
- **Baghdad LEADER / MAPGEN / MOSAICGEN / STRIPGEN / RECREP / TSUP manuals**: specialist/historical system and training references; confirm revision/applicability before treating as current requirement.

Do not invent frequencies, separation minima, coordinates, procedures or operational capability.
Training validation is not operational approval.

## 11. Known issues / deferred items

- latest WebATM work is local and not yet committed/pushed.
- Windows npm script `build:integrated` uses Unix-style env assignment; PowerShell-compatible build has been used successfully.
- instructor UI is not redesigned yet.
- controller aircraft data blocks are still WebATM generic.
- cleared-state model not implemented yet.
- sector ownership/handover not implemented yet.
- safety nets/STCA not implemented yet.
- voice/pseudo-pilot not implemented yet.
- scenario authoring, replay and scoring are later phases.

## 12. Update rule

After every material implementation milestone, update this file with:
- date
- branch/commit status
- what changed
- runtime test result
- architecture decisions
- known limitations
- next recommended step

Future project conversations should read this state file before making major recommendations or continuing implementation.


## 13. HMI prototyping decision — 2026-10-08

Before replacing the generic WebATM controller UI with a production-like Baghdad ACC HMI, build an **isolated Controller HMI Lab** inside the WebATM working copy.

Purpose:
- enable fast visual/interaction iteration without touching the working simulator path;
- use the current reference screenshots as visual targets;
- validate controller interaction patterns before integration;
- keep the production controller workspace stable while HMI ideas change rapidly.

Recommended prototype location in the WebATM repo:
- `prototypes/controller-hmi/`

Prototype characteristics:
- standalone HTML/CSS/JavaScript (or TypeScript if trivial to wire), served locally;
- no BlueSky dependency initially;
- no Docker rebuild required for each visual change;
- reuse the real Iraq reference GeoJSON files from `WebATM/static/map/` rather than duplicating aviation data;
- mock aircraft fixtures for deterministic visual states;
- fullscreen browser presentation as the target usage mode.

First HMI lab scope:
- radar-first fullscreen shell;
- top menu bar inspired by the current Baghdad controller system references;
- dropdown menu shell only, not full function implementation;
- aircraft track symbol;
- controller data block / label;
- draggable label with leader line;
- selection states;
- range/bearing/separation measurement graphic;
- mock traffic states for normal/selected/owned/handover/warning/conflict/emergency.

The lab is a design/interaction sandbox, not a second application. Once a component is accepted, port/reuse its styling and behavior in the real `?workspace=controller` implementation.

Figma may be used only as a supporting visual/specification tool; interactive behavior should be validated in the HMI Lab.


## 14. Controller HMI iteration workflow — 2026-10-08

The isolated HMI prototype is now running under `prototypes/controller-hmi/` and is the preferred place for rapid controller-interface experiments.

### Working method
Use ChatGPT as the design/specification/acceptance layer and Cursor as the implementation agent:
1. User describes desired controller behavior here, preferably with screenshots.
2. Convert the request into a small interaction contract: trigger, state change, visual response, persistence/reset behavior, and acceptance criteria.
3. Send one tightly scoped implementation task to Cursor in `webatm-spc`.
4. User refreshes the standalone prototype immediately; no Docker rebuild required.
5. Review screenshot/behavior here and iterate.
6. Only accepted HMI behaviors are later ported into the real `?workspace=controller`.

Avoid large visual rewrites or direct edits to production controller code while the interaction design is still changing.

### Immediate HMI behavior target
Current preferred interaction conventions for the prototype:
- normal aircraft/data-block text: white (with secondary/clearance fields allowed to use configured accent colors);
- right-click aircraft/label: select the aircraft and change its selected-state presentation to green/highlighted;
- middle-click aircraft: start range/bearing measurement anchored to that aircraft;
- second middle-click on another aircraft/its label: complete aircraft-to-aircraft measurement;
- second middle-click on empty map space: complete aircraft-to-point measurement;
- measurement line should update visually and report NM + bearing; aircraft-to-aircraft may additionally show vertical difference, but this is display-only until operational semantics are verified.

These conventions are prototype design decisions, not claims about certified/operational TopSky behavior.


## 15. HMI reference interactions from controller screenshots — 2026-10-08

New controller-screen references clarify the intended HMI interaction model for the prototype. These are visual/interaction targets derived from screenshots and user observation; field semantics not yet verified in manuals must remain provisional.

Observed/desired behavior:
- Aircraft track symbol is connected to a compact multi-line data block by a leader line.
- Primary label text is cyan/white with selected/clearance-related fields using contrasting amber/orange in the reference screenshots.
- Left mouse drag on the data block moves the label while the leader line remains anchored to the track.
- Right-click on the aircraft/data block opens an aircraft action context menu.
- Right-click specifically on the level/altitude field opens a dedicated flight-level selector instead of the generic context menu.
- Flight-level selector uses flight-level hundreds (e.g. 100 = FL100/10,000 ft, 110 = FL110/11,000 ft) in 10-FL / 1,000-ft increments and should support wheel/keyboard navigation.
- Range/bearing/conflict-prediction tool displays a compact bordered result box visually similar to the reference, with fields labelled `d`, `b`, and `Sep`; exact operational semantics of all values remain to be verified before production logic.
- Measurement line can connect aircraft-to-aircraft or aircraft-to-map-point depending on the second selection.

Implementation rule: prototype these interactions first in `prototypes/controller-hmi/`; do not wire flight-level selection to BlueSky or claim operational clearance semantics until the cleared-state model and source verification are complete.


## 16. HMI prototype interaction corrections — 2026-10-09

Controller HMI prototype behavior was refined from direct user observation of the Baghdad ACC interface.

Confirmed prototype targets:
- Radar/map background should be a **uniform neutral dark gray** inside and outside the aviation boundary; do not tint the FIR interior differently.
- Hide/remove the prototype geographic/country-boundary context for now. Its geometry is not trusted for controller-HMI fidelity and is visually distracting. Use the aviation reference map layers as the displayed reference until geographic context is deliberately reintroduced with verified geometry.
- Range/bearing measurement starts with **middle click**.
- Measurement is **confirmed/completed with left click**, not a second middle click.
- If the confirmation target is another aircraft or its data block, complete aircraft-to-aircraft measurement; otherwise complete aircraft-to-map-point measurement.
- The generated measurement information box is **draggable** after creation; moving the box must not move the measurement endpoints.
- Keep the result box visually compact and ATC-like. Exact collision/separation-prediction semantics remain provisional until verified from system documentation or controller explanation.

These are HMI prototype decisions only and must be implemented first under `prototypes/controller-hmi/` without modifying production WebATM/BlueSky behavior.


## 17. Data-block DAPS and STCA visual reference — 2026-10-09

New controller screenshots and user observation define the next HMI prototype target.

### Data-block visual model
- Normal/primary label information is white in the operational reference.
- Orange fields represent Mode-S-derived supplementary information in the observed configuration.
- A DAPS display toggle controls whether the supplementary orange Mode-S fields are expanded/shown.
- When DAPS is off, primary white fields remain and `SA` remains visible in orange; user identifies `SA` as the aircraft-selected altitude.
- Keep each field as an independently addressable DOM/HMI field rather than a preformatted text blob.

### STCA visual indication
- When an STCA condition exists, the reference shows a red `*STCA` alert above the affected aircraft label.
- For the isolated HMI prototype, implement the visual/state behavior only. Do not assign an operational distance/time threshold or claim certified STCA logic until the safety-net model is specified and verified.

AIP evidence confirms Mode-S capability exists in Baghdad FIR surveillance infrastructure, but the specific `DAPS` UI control and exact label-field mapping above are based on controller screenshots/user observation and remain HMI configuration knowledge rather than an AIP requirement.


## 18. Controller HMI prototype baseline commit — 2026-10-09

The isolated Baghdad ACC controller HMI prototype was committed locally in the WebATM working copy on branch:

- `prototype/controller-hmi-review`
- local commit: `e80ca9c` — `Add Baghdad ACC controller HMI prototype`

Committed prototype files only:
- `prototypes/controller-hmi/README.md`
- `prototypes/controller-hmi/app.js`
- `prototypes/controller-hmi/index.html`
- `prototypes/controller-hmi/mock-aircraft.json`
- `prototypes/controller-hmi/styles.css`

The existing production WebATM modifications remain outside this prototype commit. The HMI prototype remains the rapid-iteration sandbox for controller display behavior before accepted components are ported into the real controller workspace.
