# VICE COAST

An original, integrated, mobile-oriented 3D open-world prototype. All environment, character and vehicle geometry, facade textures and audio are generated at runtime. The reference images guided the tropical skyline, rounded waterfront towers, causeways and sunset palette; they are not required assets.

## Android build (GitHub Actions)

This repo is wrapped as a Capacitor Android app so it builds into a real `.apk`:

- `www/` holds the five game files plus a locally vendored copy of Three.js 0.128.0 (`www/vendor/three.min.js`), so the app works fully offline in the WebView — no CDN dependency at runtime.
- `android/` is the generated native Capacitor project (package `com.dex.vicecoast`).
- `.github/workflows/android.yml` builds a debug APK on every push to `main`/`master` (or manually via "Run workflow"). It uploads the APK as a downloadable **workflow artifact** named `vice-coast-debug-apk`, and also attaches it to a **draft GitHub Release** for easier download on a phone.

To get the APK after a push: open the **Actions** tab → the latest run → download the `vice-coast-debug-apk` artifact (or grab it from the draft Release on the **Releases** page), then install it on an Android device (enable "install unknown apps" for your browser/file manager first). This is a debug build, so no signing setup is required.

## Claude handoff

**Entry point: `index.html`.** Put these five files together in the repository root and publish that folder with GitHub Pages:

- `index.html`: application shell, loading screen, library loader and HUD markup.
- `style.css`: responsive title screen, menus, HUD and multi-touch controls.
- `world.js`: procedural districts, instanced scenery, road graph, collision grid, ocean, landmarks, characters and vehicle models.
- `game.js`: integrated simulation, player, vehicles, traffic, police, missions, camera, weather, audio, menus, map and saves.
- `README.md`: deployment, controls and verification notes.

No build step, package manager, backend, API key or asset conversion is required. Paths are relative and work under a GitHub Pages repository subpath. Serve the files unchanged over HTTPS with their ordinary HTML/CSS/JavaScript MIME types. A local static server is also suitable. Opening the HTML directly may work, but browser file-origin storage behavior varies; HTTPS hosting is the recommended delivery path.

The only external dependency is **Three.js 0.128.0**, pinned to jsDelivr with an unpkg fallback. Internet access is needed to load it. The project is not an offline PWA. Three.js is MIT-licensed; if vendoring it, retain its license and change the two loader URLs in `index.html`. No external images, fonts, models or audio files are needed.

## Starting state

PLAY starts beside an owned coral sports car in Palm Central. Use E or ENTER near it. JOBS opens five replayable contracts: vehicle delivery, timed travel, evading a police search, collecting parcels and a checkpoint circuit. The delivery job specifically requires the starter sports car; its position is highlighted on the map when you need to retrieve it.

The six connected districts are Palm Central, Glass Key, Afterglow Quay, Tidemarsh, Sunset Trails and Port Meridian. Landmarks include rounded twin towers, a rotating Ferris wheel, an arena, a marina, a seaplane terminal and a tall central skyline. Hidden cash and landmark discovery add exploration rewards.

## Controls

| Action | Desktop | Touch |
|---|---|---|
| Walk / swim | WASD | Left joystick |
| Run | Shift | Hold RUN |
| Jump | Space | JUMP |
| Camera | Drag mouse; wheel to zoom | Drag right-hand area; CAM cycles zoom |
| Enter / exit vehicle | E | ENTER / EXIT |
| Accelerate / reverse | W / S | GAS / BRAKE-REV |
| Steer | A / D | Left / right buttons |
| Handbrake | Space | SLIDE |
| Aircraft climb / descend | R / Q | GAS-RISE / DOWN |
| Action / horn | F | ACT; desktop F for horn while driving |
| Jobs | M | JOBS |
| Map / pause | Tab / Escape | Pause button, then MAP |

Aircraft use arcade flight: steer and accelerate as with other vehicles, then climb. Seaplanes also lift automatically at sufficient speed. Descend to land before exiting. Cars are prevented from driving into deep water; boats remain on water. Entering water on foot starts swimming. ACT near a pedestrian causes a reportable disturbance; there is no firearm or melee-combat simulation.

Touch controls support simultaneous movement, camera and action pointers. Landscape orientation is recommended. Settings can force MOBILE or DESKTOP controls on hybrid devices.

## Settings and saves

LOW / MEDIUM / HIGH alter capped pixel ratio, visibility distance, nearby population, foliage range, ocean detail and shadow availability. LOW disables shadows regardless of the separate shadow preference. Resolution adapts downward when sustained frame rate is poor. Frame limits are 30, 60 or AUTO; AUTO uses 30 on coarse-pointer devices at LOW and 60 otherwise. These are targets, not measured device-performance guarantees.

Settings also include audio, camera sensitivity, clear/cloudy/rain weather, and automatic/day/sunset/night time. The automatic day lasts about 20 minutes of active play. Weather changes periodically. Settings display measured rendering FPS and resolution scale.

Local storage key: `vice-coast-v1`. Money, completed jobs, discovered landmarks, collected cash, position, the selected non-traffic vehicle and settings save periodically and on relevant lifecycle events. Storage failures are caught and reported. Active jobs, heat and ambient traffic positions do not persist across reloads. Saves are local to the browser and hosting origin, not cloud-synchronized.

MAP supports a personal waypoint. Its dotted line indicates direction, not a computed driving route. RETURN TO PALM CENTRAL is a recovery action: it cancels the current job, clears heat, restores health and deducts up to $150. QUIT returns to the title after saving.

## Scope and verification

This is a stylized low-poly prototype, not a recreation of AAA fidelity. Physics, road-node traffic, patrol searches, pedestrian behaviors and flight are lightweight approximations. Traffic follows lane-offset road segments with timed intersection stops. Buildings use collision boxes; there are no explorable interiors or climbable roofs. Waterfront reflection/highlight, wet-road and neon effects are inexpensive approximations rather than ray-traced reflections or postprocessed bloom. Scenery uses instancing and distance visibility rather than an asynchronous asset-streaming system.

Automated checks passed for source execution, startup flow, connected road graph, clear spawn and mission targets, driving, jumping/landing, swimming height, vehicle entry/exit for every type, aircraft ascent, five mission completions, wanted escalation/clearance, and full simulation frames. The generated world contained 6 districts, 75 building/landmark collision proxies, 119 connected road nodes, 40 pooled vehicle objects and 34 pedestrian objects; actual visible counts depend on quality and distance.

**Test limitation:** these checks used lightweight DOM and Three.js stand-ins, not a real browser GPU. Real WebGL rendering, audio playback, multi-touch hardware and phone FPS have not been verified here. During the GitHub handoff, perform a browser smoke test in desktop Chrome/Firefox and physical Android Chrome/iOS Safari: load the title, play, move and drag simultaneously, enter/drive/exit, switch time and weather, complete a job, pause/resume and reload to check persistence. `VC.diagnostics()` provides a read-only integration snapshot in the browser console.

WebGL initialization, CDN failures, unavailable audio, storage errors and graphics-context loss have user-facing or silent-safe handling as appropriate. Following graphics-context loss, reload to restart from saved progress.
