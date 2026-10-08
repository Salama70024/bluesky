# SPC Baghdad ACC Web Lab — WebATM Spike

This branch is an isolated experiment. `master` is intentionally untouched.

## Goal

Validate the fastest path to a browser-based Baghdad ACC training lab using:

- BlueSky as the headless simulation engine.
- WebATM as the browser HMI.
- SPC Iraq aviation reference data as the authoritative display dataset.
- Simple BlueSky `.scn` files as the first scenario format.

This spike does **not** attempt to reproduce the complete operational TopSky system.

## First scenario

`scenario/SPC/Baghdad_ACC_Test_001.scn`

The scenario creates six aircraft and defines the Iraqi fixes it uses directly with `DEFWPT`. This avoids depending on BlueSky's bundled navdata version for the first integration test.

Aircraft:

- QTR338 — west to east, FL390
- UAE337 — southwest to northeast, FL340
- THY899 — north to south, FL380
- BAW124 — east to west, FL380
- MSR637 — westbound entry toward Baghdad/east, FL350
- ETD843 — southeast to northwest, FL360

The route points are based on Iraq AIP ENR 6-1 reporting-point coordinates used for the SPC reference map. The scenario is for training/simulation only.

## WebATM target architecture

Browser (WebATM / MapLibre)
        |
        | Socket.IO
        v
WebATM proxy
        |
        | BlueSky network protocol
        v
BlueSky headless
        |
        +-- Baghdad_ACC_Test_001.scn

Separately:

SPC Iraq Aviation Reference GeoJSON
        |
        +-- FIR boundary
        +-- AIP reporting points
        +-- ATS routes
        +-- airports / navaids
        +-- selected static circular protected areas

The reference map should remain independent from BlueSky so it can later be reused by WebATM, BluebirdATC, Cesium, Leaflet, OpenLayers, QGIS, or another simulator.

## First acceptance test

Before building controller tools or an instructor console, the spike is successful only if:

1. WebATM connects to this BlueSky branch.
2. `Baghdad_ACC_Test_001.scn` loads.
3. Six aircraft appear and move under LNAV.
4. Start / pause / simulation speed controls work.
5. An aircraft can be selected and its route/label displayed.
6. The view can be centered on Baghdad/Iraq.
7. No dependency on the legacy BlueSky desktop UI is required.

## Next step after runtime validation

Add the SPC Iraq aviation GeoJSON as a dedicated MapLibre layer in WebATM. The base layer must be visually independent of the simulation engine and must default to the reporting points and routes shown on the Iraq AIP ENR 6-1 chart.

Do not add scenario editing, instructor scoring, voice, STCA, or other advanced functions until the runtime + map integration passes.
