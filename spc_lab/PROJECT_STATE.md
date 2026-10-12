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


## 19. Track history, speed vector and leader-line geometry — 2026-10-09

Additional controller HMI reference behavior:
- Show a short sequence of history dots behind each aircraft track symbol to indicate recent past positions. In the isolated prototype these may be synthetic from current track/heading; production must use recorded surveillance/track history samples.
- Add a short forward speed/vector line from the aircraft track symbol. It is toggled from the aircraft right-click context menu via the `Speed` item. Prototype behavior is visual only until the operational vector time/scale is verified.
- Track-to-label leader line must terminate very close to both objects: from the nearest edge of the track symbol to the nearest edge of the data-block rectangle, with only a minimal 0–2 px visual gap. It must not start from the track center or leave a large blank gap at either end.
- Leader, history dots, and speed vector should remain separate visual primitives and preserve data-block drag behavior.


## 20. Selected-track color, history spacing and Flight Data menu — 2026-10-09

Further controller-HMI prototype refinements from user observation:
- Reduce history-dot spacing/overall trail length; keep the same short recent-history concept but make the dots more compact behind the track.
- Right-click selection should highlight the **entire aircraft data block** in green, not only the track/leader. For safety-priority indications, STCA should remain red unless later reference evidence shows otherwise.
- Add a top-menu item named **Flight Data** positioned between **ASD Bg** and **Flight Lists**. The menu can remain a placeholder shell until its operational contents are documented.


## 21. Handover / sector ownership HMI behavior — 2026-10-09

Observed controller HMI behavior from user-provided operational screenshots/experience:
- Handover is initiated from the aircraft right-click context menu by choosing the target sector/controller position.
- During a pending handover, the aircraft data block flashes between gray and white until the transfer is accepted or cancelled/accepted again through the context menu.
- The `Accept` action is available through the aircraft right-click context menu; acceptance may be performed from either the initiating or receiving position according to the observed workflow.
- Aircraft controlled by another sector are displayed in blue, including both the data block and its leader line.
- These are prototype HMI/coordination-state observations and are not yet backed by an authoritative LEADER/TopSky handover-state specification. Do not encode operational transfer rules beyond the observed UI behavior until source verification is available.
- Iraq AIP confirms the current ACC North / ACC South control organization and published control channels, but not the specific label colors/flashing behavior used by the HMI.

Recommended prototype state model:
`owned_local`, `handover_pending_out`, `handover_pending_in`, `owned_other`, with explicit target/source sector identifiers and a separate `selected` UI state.


## 22. Thales System source library strategy — 2026-10-09

A Google Drive folder named **Thales System** is available as the long-form historical/product reference library for the ATC simulator. It contains Baghdad-specific Thales documentation, general TopSky-ATC operational handbooks, and training-course modules.

### Keep in Google Drive as the complete archive/library
The full folder should remain in Drive. It includes, among others:
- Reference_Baghdad-SSS_20170905_DR.pdf — TopSky-ATC Baghdad System Segment Specification, Rev A, Nov 2014, 290 pages.
- CP OH (Rev K).pdf — Controller Position Operational Handbook, Rev K, Nov 2013, 290 pages.
- DATAGEN OH (Rev J).pdf.
- Baghdad DATAPREP OH (Rev -).pdf.
- Baghdad LEADER OH (Rev -).pdf.
- MAPGEN / MOSAICGEN / STRIPGEN / RECREP / TSUP handbooks.
- TopSky operational-course modules including HMI Presentation, Controller Keyboard, HMI Control, Radar Tracks, FPL Management, Setup/Operational Supervision, Planner Functions, Safety Nets, Recording/Replay, Simulation, Leader Position, and Pilot Position.

### Project-source curation rule
Keep the Drive folder as the full archive, but attach a curated subset to the ATC Simulator project for high-frequency design/implementation work and durable cross-conversation access.

Highest-priority missing project sources:
1. Reference_Baghdad-SSS_20170905_DR.pdf
2. CP OH (Rev K).pdf
3. Mod03_HMI Presentation
4. Mod04_Controller Keyboard
5. Mod05_HMI Control
6. Mod06_Radar tracks
7. Mod07_FPL management
8. Mod12_Safety Nets Alerts
9. Mod18_Recordingreplay
10. Mod20_Simulation
11. Mod21_Leader position
12. Mod22_Pilot position

Second-tier sources for later phases:
- DATAGEN OH (Rev J)
- Baghdad DATAPREP OH (Rev -)
- Mod09 Setup and Operational Supervision
- Mod10 Planner functions
- Mod13 AGDL
- remaining course modules.

Already present as project sources and therefore do not need duplicate upload merely for access: Baghdad LEADER OH, MAPGEN, MOSAICGEN, STRIPGEN, RECREP, TSUP.

### Scope/applicability caution
These Thales materials are historical/product/configuration references (mainly 2013–2014 Baghdad delivery/product material). Use them to reproduce HMI/workflow/training behavior and understand system architecture, but not as current regulatory truth. Current Iraqi AIP/AIRAC and applicable ICAO/local instructions govern current operational data and requirements.


## 23. HMI-02 menu/setup foundation — 2026-10-11

The isolated controller HMI prototype reached the **HMI-02 menu/setup foundation** milestone in the local WebATM working copy.

Local branch:
- `prototype/controller-hmi-menus`

Commit:
- `f803f4fb2ba710e19d6be217328a633f4ab4e19f` — HMI-02 menu/setup foundation

Prototype-only files changed:
- `prototypes/controller-hmi/app.js`
- `prototypes/controller-hmi/index.html`
- `prototypes/controller-hmi/styles.css`
- `prototypes/controller-hmi/mock-aircraft.json`
- `prototypes/controller-hmi/README.md`

Implemented:
- data-driven menu configuration for top menu bar and aircraft/context/CFL menus;
- full controller menu bar shell with Data Link configurable but hidden;
- one-open dropdown behavior, click-outside/Escape close, disabled states and cascade menus;
- separate Track Label and Track Symbol context menus;
- dedicated CFL selector that updates controller-cleared level only, leaving actual level/SA unchanged;
- reusable window manager;
- Setup window with functional Track/Marker/Bright tabs and Centre/Sensor/Superv shells;
- previous radar/HMI prototype behaviors preserved: draggable labels/leaders, measurement vectors, DAPS, STCA visual state, handover/ownership, history dots and speed vectors.

Validated in browser:
- top menus and nested menus;
- CFL changes clearedLevel only;
- aircraft context menus;
- Setup immediate history changes;
- DAPS behavior;
- no JavaScript errors except an unrelated favicon 404.

Deferred/placeholders remain:
- SSR Find/zoom/filter/weather operational behavior;
- DCT/Hold/AIREP and other operational commands;
- CPDLC/AIDC/OLDI;
- real STCA logic;
- BlueSky wiring;
- overlap-avoidance behavior;
- Centre/Sensor/Superv operational behavior.

Recommended next milestone after visual acceptance of HMI-02: **HMI-03 Flight Data / Sector Inbound / Planning workflow**, still in the isolated prototype before BlueSky integration.


## 24. HMI-02 corrections: handover timing/state and map aspect ratio — 2026-10-11

Post-HMI-02 visual review identified two prototype defects:

1. **Handover pending indication**
   - Current flash cadence is too fast for the intended Baghdad controller HMI reference.
   - Initiating handover to another sector must enter a real pending state and flash until explicit acceptance; it must not jump directly to `owned_other`.
   - Proposed prototype cadence: configurable ~900 ms per phase (white/gray), subject to further controller validation.
   - Completion via `Accept` transitions ownership and stops flashing. STCA remains red and higher priority.

2. **Map aspect-ratio distortion**
   - The standalone SVG prototype currently stretches the Iraq aviation geometry when viewport aspect ratio changes: wide windows make the map wider; narrow windows make it taller.
   - Correct behavior is aspect-preserving fit: uniform X/Y scale, centered within the available radar viewport, with the same neutral radar background filling unused space.
   - Use an SVG/viewBox or equivalent projection transform with `preserveAspectRatio` / uniform scale; do not scale longitude and latitude axes independently.
   - This correction applies only to the isolated SVG HMI prototype. The production MapLibre Iraq map already uses geographic projection and should not be altered by this fix.


## 25. HMI-02 correction verified — 2026-10-11

The isolated controller HMI prototype correction pass was verified locally (not yet committed at the time of this note).

Corrections verified:
- outgoing handover now remains in `handover_pending_out` until explicit `Accept`;
- outgoing handover no longer auto-completes and is disabled for `owned_other` / incoming states;
- handover flashing uses a slower configurable cadence: 900 ms white + 900 ms gray;
- STCA remains red during handover flashing;
- standalone SVG map no longer distorts with viewport aspect ratio;
- map now uses a stable logical viewBox with uniform scaling and `preserveAspectRatio="xMidYMid meet"`;
- aircraft logical coordinates remain stable across wide, square and portrait viewports;
- production MapLibre map was not modified.

Visual review note: preserving the full Iraq/FIR aspect ratio on a wide monitor naturally leaves unused lateral radar background. This is expected geometry, not distortion. Later controller-workspace view/scale policy should decide whether to show full FIR, a sector-centered default view, or a user-controlled radar scale; do not solve this by non-uniform stretching.

Recommended action: commit this correction as the HMI-02 accepted baseline, then proceed to HMI-03 (Flight Data / Sector Inbound / Planning workflow) in the isolated prototype.


## 26. HMI-02 accepted baseline commit — 2026-10-11

The verified HMI-02 correction pass was committed locally in the WebATM working copy.

Branch:
- `prototype/controller-hmi-menus`

Commit:
- `40f6d33` — `Fix handover state and preserve HMI map aspect ratio`

This commit fixes the handover pending/accept state flow and preserves the standalone SVG map aspect ratio across viewport sizes. It is the accepted HMI-02 baseline before starting HMI-03.


## 27. HMI-03 Flight Data / Sector Inbound / Planning — 2026-10-11

The isolated controller HMI prototype reached the **HMI-03** milestone on local WebATM branch:

- `prototype/controller-hmi-flightdata`
- commit `0b186ad` — `Add Flight Data and sector inbound HMI workflow`

Prototype-only changes:
- new `flight-data.js` domain/config module;
- new `mock-flight-plans.json` with 14 deterministic FPLs;
- explicit aircraft ↔ flight-plan correlation;
- reusable configured flight-list windows for Incoming, Arrival, Departure, Active, Non-Active, FIR Entry and FIR Exit;
- Sector Inbound List filtering/sorting and auto-open/auto-close behavior;
- reusable Planning window accessible from Flight Data, Track Label Plan action, and flight-list double-click;
- separate aircraft actual level from controller/FPL cleared level;
- mapped handover ownership state to FPL substates HANDOVER_OUT / HANDOVER_IN / CONTROLLED;
- controller viewpoint (ACC NORTH / ACC SOUTH) affects SIL/controller context without rewriting underlying aircraft/FPL data.

Validation reported:
- syntax checks passed;
- SIL and Active-list logic passed;
- browser smoke covered correlation, uncorrelated FPL, track-without-FPL, CFL vs AFL separation, Accept→CONTROLLED, SIL auto close/reopen;
- no JavaScript errors except favicon 404.

Deferred:
- automatic FPL lifecycle;
- FDP route/ETO processing;
- OLDI/AIDC;
- BlueSky command wiring;
- real SIL sector definitions/look-ahead;
- Flow/STATS/electronic strips.

Recommended next milestone: stop expanding presentation-only prototype and perform a **controlled controller-to-simulation integration spike**. Define a small, explicit adapter contract between the accepted HMI state model and BlueSky for read-only aircraft truth first, then a minimal set of validated controller commands. Keep controller clearance state separate from aircraft truth and retain the prototype as the HMI reference baseline.


## 28. Integration Spike 01 design — read-only BlueSky truth — 2026-10-12

After HMI-03, the next phase is a controlled **read-only live integration**. Do not add more presentation-only HMI before validating the state architecture against live BlueSky traffic.

### Chosen integration path
Reuse WebATM's existing BlueSky data path rather than building a second backend:

BlueSky → WebATM BlueSkyClient/ZMQ → BlueSkyProxy → Flask-Socket.IO → controller-HMI prototype adapter.

The standalone prototype (served from repo root on port 8765) may connect to the already running WebATM Socket.IO endpoint (normally port 8082). WebATM currently allows cross-origin Socket.IO clients, so the spike should not require a duplicate ZMQ client or new proxy service.

### Read-only contract
Consume only:
- `initial_data.traffic_data`
- live `acdata`
- optionally `siminfo` / connection status for diagnostics.

Do **not** emit `command` from the prototype in this spike.

Map BlueSky aircraft truth into a separate adapter model:
- id/callsign
- lat/lon
- track
- actual altitude
- TAS (and GS/CAS if present)
- vertical speed
- aircraft type if present
- conflict flag only as raw simulator truth/debug data, not as Baghdad STCA semantics.

BlueSky WebATM wire units are SI: altitude metres, speed m/s, vertical speed m/s. Convert explicitly at the adapter boundary to the HMI display units (FL/ft, knots, ft/min).

### State-separation rule
Live BlueSky updates may change aircraft truth only. They must not overwrite:
- controller `clearedLevel`;
- local FlightPlanState;
- handover/ownership state;
- controller selection/UI state;
- DAPS/Mode-S fields that are not present in the live feed.

Correlate live aircraft to mock/prototype FPL only by explicit callsign/association. A live track without FPL remains valid; an FPL without live track remains valid. Never fabricate either side.

### History
In live mode, use received position samples for track history dots instead of synthetic heading-derived dots. Keep a small bounded per-aircraft history buffer. Synthetic history remains acceptable only in mock mode.

### Fallback / testability
Keep mock mode intact and selectable. Live-mode failure must not corrupt mock data. Provide visible source/connection state (MOCK / LIVE CONNECTING / LIVE / STALE / DISCONNECTED).

### Success criteria
Run the existing Baghdad scenario in BlueSky and verify that live aircraft move in the HMI prototype while local FPL/CFL/handover state remains stable and separate. No controller command path is enabled yet.


## 29. Integration Spike 01 live truth adapter completed — 2026-10-12

Cursor reports the isolated controller HMI prototype read-only BlueSky integration finished on the **local** WebATM working copy:

- Branch: `prototype/controller-hmi-live-truth`
- Commit: `27fc43d` — `Add read-only BlueSky truth adapter to controller HMI`
- Changed files **only** inside `prototypes/controller-hmi/`: new `live-bluesky.js`; modified `app.js`, `index.html`, `styles.css`, `mock-flight-plans.json`, `README.md`.
- Source: existing WebATM Socket.IO endpoint at `http://localhost:8082`, connected from the prototype served at `http://localhost:8765/prototypes/controller-hmi/`.
- Protocol: reads `initial_data`, `acdata`, `siminfo`, connection status; no prototype `command` emission and no second BlueSky proxy/backend.
- Units: SI altitude/speeds converted centrally to FL/knots/ft-per-minute; no fabricated Mode-S data.
- MOCK and LIVE modes selectable; status distinguishes CONNECTING, LIVE, STALE, DISCONNECTED; paused simulation can be stale without socket disconnect.
- In LIVE, track history derives from received positions rather than synthetic dots.
- Live aircraft truth does not overwrite local CFL, FPL, handover, selection or dragged label positions.

Cursor reports a full 24-step integration acceptance test passed using the Baghdad scenario: 6 live aircraft, observed 7 acdata/s with ~5 fresh frames/s, update/render capped near 2 fps, FPL correlation/no-FPL cases, handover/CFL state persistence, pause/stop/disconnect, and mock/live switching. A screenshot independently shows the LIVE mode and six aircraft in the prototype; the remaining test outcomes are **Cursor-reported**, not independently retested in this chat.

### Follow-up defects / engineering cautions before write-enabled integration

1. **Mock/live isolation:** Editing or handing over a flight in LIVE currently can leave its mock FPL substate changed after switching back to MOCK. Establish separate cloned/session-scoped controller/FPL state for modes or an explicit state reset/preservation policy.
2. **Correlation ≠ ownership:** A live aircraft with no FPL correlation must not automatically be shown as owned by another sector. Distinguish uncorrelated surveillance tracks from tracks owned by another controller. The appropriate color must follow a verified local configuration, not an invented fallback.
3. **Input test gap:** Label dragging in LIVE was tested using synthetic pointer events, not an actual mouse gesture. Test pointer capture, coordinate mapping, drag/click interactions in real browser.
4. **Startup error test gap:** Browser error capture was installed after page load; startup errors must be observed during a fresh load.
5. **UI quirks:** Debug FPL dropdown does not refresh after handover; auto-open SIL obscures source-mode debug controls.
6. **Track lifecycle:** Disappearing track is currently removed immediately (no coast); acceptable as a labelled spike limitation, not as validated surveillance behavior.
7. **Scenario calibration:** Reported high groundspeeds at cruise need separate scenario/performance review; not proof of an adapter defect.
8. **Rendering:** Approx. 2 Hz radar redraw remains an initial prototype compromise; tune only after establishing training frame/update requirements.

Recommended next step: small **Integration Spike 01.1 stabilization** confined to the isolated prototype, then decide controlled command interface design (not yet enable BlueSky commands).


## 30. Verified Baghdad ACC North/South divider and sector ownership — 2026-10-12

**Source verification:** Iraq AIP ENR 2.1-2 (28 NOV 2024, AIRAC AIP AMDT 05/24 in the provided mixed-date AIP PDF `462.pdf`) formally defines Baghdad ACC North/South sectors, both H24 and FL235–FL460. The shared internal lateral boundary, in west→east order, is:

`312325N 0412439E → ANBAR → BABIL → DEBNI → SEPTU → VAXEN → 332610N 0460200E`.

The reverse is shown in the North-sector definition. WGS84 coordinates of intermediate AIP fixes are also listed in the same 2026 AIP compilation:
- ANBAR N320649 E0422453
- BABIL N323447 E0430436
- DEBNI N331014 E0441703
- SEPTU N331300 E0444400
- VAXEN N331800 E0451500

Do not treat a hand-drawn yellow screenshot annotation as source geometry. Use the published coordinates and versioned data, rechecking the current effective ENR 2.1 page before claiming present operational validity.

**LATSI 2.8 (Dec 2015), Section III/Chapter 1** states explicitly: Baghdad ACC is split into North and South sectors, lateral boundaries as per Iraq AIP ENR, vertical FL235–FL460, H24. Section III/Chapter 2 states transfer of control coincides with the airspace boundary and transfer of communication should occur before it. Historical procedural baseline only, validate current local rules later.

**Baghdad SSS Rev A (Nov 2014) §§3.5.8.6–3.5.8.6.1, pp.130–131** establishes asymmetric sector-local handover views:
- upstream controller offers handover of a locally controlled flight;
- sender views HANDOVER-OUT, receiver views HANDOVER-IN, with visual blinking;
- receiver's Accept completes transfer; sender may cancel its outstanding offer before acceptance (using Accept again in documented configuration);
- upon completion the flight becomes controlled in receiving sector and other-sector traffic from sender viewpoint.
**CP OH Rev K (Nov 2013), §5.2.2.2 pp.58–59** documents default handover flashing for both positions and controlled/uncontrolled colour distinctions. Note that controller-observed white-for-local and blue-for-other is a **local visual reference** and must be validated separately from generic product colour palette/configuration.

**Important architectural rule:** one controller session operates a single assigned sector (ACC NORTH or ACC SOUTH), per project training brief. The same aircraft has one shared `ownerSector` and at most one pending `handoverOffer`, while each client derives its own presentation from `viewpointSector`. Do **not** store independent contradictory ownership values in two browser clients; do not infer control merely from the aircraft's geographic side of the divider. Use geographic sector location to assist exercises, boundary-crossing prompts and instructor validation, not to silently transfer control. Also distinguish UI handover acceptance from procedural transfer-of-control at the airspace boundary.

**Next isolated-prototype task:** restore/display the authentic divider from the AIP vertices, validate it against the chart, use sector-specific label/leader colours and pending handover visualization without any BlueSky write commands. Defer synchronized 2-controller handover until a shared authority/coordination service exists.

**UI note:** `AIRCRAFT SOURCE (DEBUG — READ-ONLY)`, `PROTOTYPE STATE`, and `FLIGHT DATA DEBUG` are temporary developer/instructor tools. They must not permanently obscure the controller radar map. Move to a collapsible debug drawer/panel or hide by default behind `?debug=1` while preserving MOCK/LIVE selection and test harness usability. Final controller workspace must not expose debug controls.


## 31. Sector Foundation 01 implementation — reported 2026-10-12

Cursor reports isolated WebATM controller-HMI prototype milestone:
- Local branch: `prototype/controller-hmi-live-truth`
- Local commit: `e79eba0` — `Add Baghdad ACC North/South sector foundation`
- Modified only `prototypes/controller-hmi/`: new `baghdad-acc-sectors.json`; `app.js`, `index.html`, `styles.css`, `regression-harness.js`, `README.md`.
- North/South divider from Iraq AIP ENR 2.1-2 (28 NOV 2024), endpoints `312325N 0412439E` and `332610N 0460200E`, following ANBAR → BABIL → DEBNI → SEPTU → VAXEN. Intermediate fixes cross-checked against AIP ENR 4.4-derived aviation GeoJSON, reportedly 0 mismatches.
- Shared map geographic projection used for divider; source metadata preserved.
- Sector viewpoint / owner sector / handover source-target / correlation / aircraft location kept distinct; no automatic ownership from location.
- Controller colors: local white, other-sector blue, pending handover flashing white/gray, uncorrelated neutral; STCA red preserved.
- Prototype debug tools hidden by default, enabled via `?debug=1`, draggable/collapsible; viewpoint stays on controller chrome.
- Cursor reports regression suite 10/10 passing and no JavaScript errors. These are Cursor-reported local checks; this chat has not independently inspected the unpublished local commit.
- Remaining limits: line only (no sector polygons); no shared cross-browser handover state; APP/KIRKUK/ALI sector view options lack complete AIP boundaries; no BlueSky writes.

### Next recommended increment: cross-tab handover interaction spike

Test two simultaneous controller viewpoints on a single machine, each controlling just ACC NORTH or ACC SOUTH, with shared HMI-only sector ownership and pending handover. Keep BlueSky live truth read-only. A lightweight browser BroadcastChannel transport can be used as an **explicitly temporary same-origin/same-browser-profile prototype transport**, not as a distributed training-system authority. Avoid changing production WebATM. Later replace transport with authoritative server-side coordination for multiple training workstations. Validate manual offer/accept/cancel, both views updating coherently, one owner per aircraft and no automatic transfer merely from geographic boundary crossing. Keep controller visual states context-specific and avoid cross-mode (MOCK/LIVE) data leakage.


## 32. Two-controller handover prototype and APP sector boundary distinction — 2026-10-12

User-provided Cursor report: two-controller handover spike is implemented locally in the WebATM prototype branch `prototype/controller-hmi-live-truth`, commit `e817c6e`. Added `handover-coord.js` BroadcastChannel transport; changed `app.js`, `index.html`, `styles.css`, `README.md`. Two tabs (NORTH `?role=north&coord=1`, SOUTH `?role=south&coord=1`) synchronize one owner sector and one pending offer per callsign; sender offer, receiver accept, cancel, reverse transfer, duplicate handling and peer-lost/unsynchronized indication. Reported two-tab tests and baseline regression 10/10 passed. BlueSky read path still read-only. BroadcastChannel is temporary, same origin/browser profile only; no multi-machine server authority yet. Independently verify local code/tests when that commit becomes accessible; do not claim remote review.

**Documented airspace scope** from `462.pdf` (provided Iraq AIP compilation):
- AIP **ENR 2.1-1 dated 23 FEB 2023**, PDF p.47, defines historical published **KIRKUK Sector ↔ BAGHDAD Sector** shared lower-airspace boundary: `344655N 0411325E → 343054N 0414705E → 342936N 0453144E`.
- That boundary is **APP sectorization below FL235**, not the high-level ACC NORTH/SOUTH divider. The ENR entry lists both as under Baghdad APP, H24, with differing variable lower limits and upper limit FL235.
- AIP **ENR 2.1-2 dated 28 NOV 2024**, PDF p.48, defines ACC NORTH/SOUTH sector divider at FL235–FL460.
- AIP **ENR 2.1-3 through 2.1-5 dated 19 MAR 2026**, PDF pp.49–51, lists more detailed KIRKUK TMA 1–9 boundaries below FL235, showing that APP area and TMA subdivisions are not interchangeable and published page revision dates differ across the compilation.
- Recommended map model: separate versioned `ACC Sector Boundaries` (NORTH/SOUTH) and `APP Sector Boundaries` (KIRKUK/BAGHDAD/ALI) datasets/layers, with vertical limit/effective date/provenance and layer toggles. Do not make ACC ownership depend on crossing APP divider; no automatic transfer merely from geometry. Re-check current effective AIP before operational claims.
- The APP BAGHDAD↔ALI boundary is separately documented in ENR 2.1-1/-2 (read when implementing), and should not be presented as an ACC boundary.

**Screenshot/UI issue (2026-10-12):** user image shows app restricted to about 730×600 at upper left of a much larger browser window with grey unused area. URL contains `?debug=1&runRegression=1`, tab title `PASS 10 - HMI regression`. This suggests a regression resize test may leave the SVG/stage container artificially sized after tests. Diagnosis is unverified until local DOM/CSS/tests inspected. First compare clean controller URL without `runRegression=1`; if only test mode is affected, restore dimensions/styles and resize callbacks in harness cleanup. Normal controller must fill available viewport while preserving projected map aspect ratio with uniform scaling. Never solve by stretching the geography; test 16:9, square, portrait, fullscreen, and post-regression restoration.


## 33. Radar camera requirement after two-controller screenshot — 2026-10-12

New screenshot at normal controller URL (no regression parameter) confirms the controller menu bar and radar stage fill the browser width and height; the earlier small stage seen with `?runRegression=1` was not reproduced. Cursor code review reports `regression-harness.js` does not itself alter the viewport, so prior suspicion that it fails to restore dimensions was unverified and should not drive code changes.

**Actual identified gap:** the standalone controller HMI's aspect-preserving SVG mock has no usable operational radar zoom/pan interaction. Current static logical viewBox / `preserveAspectRatio="xMidYMid meet"` correctly prevents distortion but is not an interactive camera. A centered complete-FIR map can look relatively small in the workspace. Add a focused, self-contained `RadarCamera` behavior to the **prototype only**, preserving geographic coordinates, context menus, measurement, dragging, FPL, DAPS, STCA and synchronized two-controller handover.

Desired interaction: wheel-to-zoom around cursor, zoom in/out buttons, Fit FIR, sector-centered optional fit, background drag pan; prevent accidental map panning from left-dragging aircraft data-block labels; retain middle-click measurement and right-click menus; ensure consistent screen↔world coordinate conversion for accurate hit tests, leader lines and measurements after transformations, full-screen and resize. Avoid non-uniform stretching and keep operational aircraft data-block text legible at different map zoom levels (ideally constant screen pixel size). Do not require BlueSky writes or change existing Iraq GeoJSON. Add meaningful tests for wide/narrow screens, anchors, label drags, measurement, camera limits and two-controller sync (camera state should remain local to each controller).

After camera zoom/pan is tested, add **display-only APP boundary overlays** from historical published AIP ENR 2.1-1 (23 FEB 2023) and review revised Kirkuk TMA publications in ENR 2.1-3..5 (19 MAR 2026) as separate scope; never conflate them with ACC North/South ownership, and no geographic automatic transfer.
