# Space Attack — complete chronological prompt history

This document records every user-authored task prompt visible in the Space Attack Codex conversation, including follow-ups, corrections, interruptions, and failed implementation attempts. Prompts inside `text` blocks are reproduced verbatim. Work notes and failure descriptions are summaries; they are not verbatim assistant transcripts.

## Chat order and scope

1. **Codex Chat 1 — Space Attack: implementation, revisions, deployment, and prompt-history export.** All 22 prompts below belong to this single chat, in their original order.

Only one Codex chat is visible in the supplied conversation. No other chats have been inferred, merged, or reconstructed. The original prompt-history document used numbered order because timestamps were not supplied in its source context. A subsequent export from the saved session includes the original UTC timestamps and verbatim assistant messages. Automatically supplied environment metadata and hidden system/developer instructions are outside this user prompt history.

- Codex conversation export: [actual game-build messages](codex-conversation-export.md)
- Game source: [trilitheus/space-attack](https://github.com/trilitheus/space-attack)
- Published game: [Space Attack](https://trilitheus.github.io/space-attack/)
- This history: [prompt-history.md](https://github.com/trilitheus/space-attack-details/blob/main/prompt-history.md)

## Codex Chat 1

### 1. Initial technology direction

```text
I will paste you a task description - basically create a galaxian clone - let's do this in typescript - and probably use the phaser 3 library
```

The assistant invited the task description and agreed to use TypeScript and Phaser.

### 2. Initial game specification

```text
The task: build Space Attack, a small, polished browser arcade game where the player pilots a spaceship against waves of enemies. Reference: https://www.youtube.com/watch?v=jYIC8ADIArc

It must include:

Keyboard movement and firing, with on-screen instructions
Enemy waves, working collisions, a visible score and health/lives
Increasing difficulty
A start screen, game over and restart
```

The workspace was empty. The assistant began setting up TypeScript, Vite, and Phaser, and attempted to inspect the video.

**Failed attempt:** fetching the YouTube reference returned `Failed to fetch https://www.youtube.com/watch?v=jYIC8ADIArc: Cache miss`. The assistant disclosed that it could not view the reference and proceeded from the Galaxian-style mechanics described by the user.

This turn was interrupted by the next prompt while dependency installation was still running.

### 3. Multi-hit enemies and health colours

```text
let's make it a little more interesting too - some enemies take multiple hits - the colour can change to indicate how many hits left - green -> orange -> red (or just orange -> red) only add those after 2 or three levels - 2 hits first then a couple of levels later the 3 hit versions
```

Two-hit enemies were introduced on wave 3 and three-hit enemies on wave 5. Remaining health used green → orange → red. The first draft also included diving attacks, scoring, three shields, increasing difficulty, start/game-over/restart screens, pause, optional sound, touch controls, and local best scores.

**Failed attempts and recoveries:**

- The initial npm install failed with `EAI_AGAIN` / `getaddrinfo EAI_AGAIN registry.npmjs.org` under restricted network access. It was rerun with approved network access and succeeded.
- The first build failed because TypeScript lacked a declaration for the CSS side-effect import and the ship polygon used plain point objects where Phaser required `Vector2` instances. Adding Vite types and converting the points fixed the build.
- Starting Vite inside the sandbox failed with `listen EPERM: operation not permitted 0.0.0.0:5173`. An approved server start succeeded.
- Browser verification failed with `Browser is not available: iab`; discovery returned no connected browsers or apps. The assistant reported that visual browser play-testing was unavailable, rather than claiming it had played the game.
- A source review caught a possible double-score issue when multiple shots encountered a destroyed enemy in one frame. An active-sprite guard was added.

The production build passed, and the local game was served at `http://localhost:5173/`.

### 4. Alien variety, larger canvas, and gentler opening

```text
ok - first version is looking pretty good - let's add more alien varieties - also increase the canvas size - it's a bit small - decrease the speed and number of aliens on the first level and increase as we progress
```

The assistant inspected the existing implementation and planned a larger playfield, multiple alien silhouettes, and fewer/slower enemies on wave 1. Before edits were made, the user interrupted to request a baseline commit.

### 5. Commit the first draft before continuing

```text
commit the first draft before continuing
```

The initial draft was committed as [`f1cdfbd`](https://github.com/trilitheus/space-attack/commit/f1cdfbd).

**Failed attempt:** Git could not create `.git/index.lock` because the repository metadata was mounted read-only inside the sandbox. The commit was rerun with approved access and succeeded.

The assistant then continued prompt 4: the logical playfield became 1200 × 720, the page became wider, and four alien kinds were added: scouts, crabs, mantas, and squids. Wave 1 decreased from 30 enemies to 12. The first six Medium waves used 12, 14, 21, 24, 32, and 36 enemies, with increasing movement and attack speeds.

**Failed verification attempt:** a test script using the installed TypeScript package's `ModuleKind.ESNext` failed because `ModuleKind` was undefined. The checks were rerun using Node's native TypeScript support and passed. These checks covered dimensions, silhouettes, species introductions, and progression through 30 wave configurations.

### 6. Wider movement and visible top-of-screen re-entry

```text
ok - make the aliens have more left right movement - some can sweep across the screen - also when they get past the player and re-appear - make them come down from the top of the screen - not just 'appear'
```

Formation sway and individual weaving were increased. Some aliens received full-screen sweeping attacks that bounce at the edges. Escaping aliens changed to a returning state above the screen and visibly descended back into formation.

Checks passed for sweeps, bounces, formation movement, and gradual re-entry. The build passed.

### 7. Commit the alien and movement updates

```text
commit
```

The larger playfield, alien varieties, progression, sweeping attacks, and smooth re-entry were committed as [`106db3e`](https://github.com/trilitheus/space-attack/commit/106db3e).

### 8. Mature cartoon graphics

```text
ok - now let's add polish up the graphics a little - less pixel arty - let's go fo a more mature cartoon look
```

Pixel sprites were replaced with bundled SVG artwork rasterised at double resolution: shaded alien bodies, expressive faces, a detailed ship, cockpit glass, an animated engine plume, gentle ship banking, and smoother projectiles and impacts. Health colour variants remained readable.

**Failed attempts and recovery:**

- Rendering an artwork preview with Python failed because `cairosvg` was not installed. The assistant used the available ImageMagick renderer instead.
- The combined preview initially showed all alien bodies green because repeated SVG gradient IDs collided in the combined document. Unique IDs per alien and health value fixed the preview.

The artwork preview was visually checked and the build passed. However, the SVG URLs used percent encoding, which caused the runtime failure reported in the next prompt. The successful build and static artwork preview did not establish browser startup correctness.

### 9. Reported startup failure and exact console error

```text
ok - the game won't start now - console error: phaser.js?v=f56d9184:91278 Uncaught InvalidCharacterError: Failed to execute 'atob' on 'Window': The string to be decoded is not correctly encoded.
    at XHRLoader (phaser.js?v=f56d9184:91278:36)
    at SVGFile.load (phaser.js?v=f56d9184:89761:23)
    at LoaderPlugin.<anonymous> (phaser.js?v=f56d9184:90795:12)
    at Set.forEach (<anonymous>)
    at LoaderPlugin.checkLoadQueue (phaser.js?v=f56d9184:90790:15)
    at LoaderPlugin.start (phaser.js?v=f56d9184:90749:11)
    at SceneManager.bootScene (phaser.js?v=f56d9184:153214:13)
    at SceneManager.start (phaser.js?v=f56d9184:153683:10)
    at SceneManager.bootQueue (phaser.js?v=f56d9184:153062:11)
    at EventEmitter.emit (phaser.js?v=f56d9184:142:24)
```

The assistant inspected Phaser's actual XHR loader and found that its data-URL handling calls `atob`. It corrected SVG URLs to UTF-8-safe base64 encoding.

**Failed verification attempt:** importing `phaser/src/loader/XHRLoader.js` through the package name failed with `ERR_PACKAGE_PATH_NOT_EXPORTED`. Importing the installed source file by its local path allowed the real loader to be tested. All 15 existing SVG assets decoded successfully and the build passed.

### 10. Commit the graphics and SVG fix

```text
ok - good - commit that
```

The cartoon graphics and SVG loading fix were committed as [`b844973`](https://github.com/trilitheus/space-attack/commit/b844973).

### 11. Larger text, Earth-defence narrative, and wormhole arrivals

```text
ok - now let's change the font sizes to be a little bigger - the instructions etc are hard to read - whilst we're at it let's also change the narrative around the game a little - we're defending earth against an alien invasion - the future of humanity is at stake!  let's add a 'wormhole' that the initial alien formation arrive from at the start of each level and update the text around the game to reflect this
```

Instructions were enlarged to 16px with improved contrast and responsive layouts. The start screen, status, pause screen, game-over message, and surrounding copy were rewritten around defending Earth. A rotating wormhole released each wave into formation before attacks began, and an Earth horizon was added.

**Failed verification attempts and fixes:**

- An arrival test compared a floating-point value to exactly zero and failed on `8.673617379884035e-17`; the assertion was changed to use a small tolerance.
- A continuity test then found a genuine jump when a moving formation target crossed the portal's horizontal coordinate. The curve's control-point direction had switched abruptly. Replacing that sign-based choice with a continuous expression fixed the arrival path.
- `git diff --check` found an extra blank line at the stylesheet's end; it was removed.

All 17 assets decoded through Phaser's loader, arrival checks passed across different wave sizes, and the build passed.

### 12. Persistent top-left wormhole and missile polish

```text
ok - move the wormhole to the top left and persist it's view - it should 'engorge' when the aliens deploy before shrinking once they aliens are through - let's also improve the missile graphics a little
```

The assistant began inspecting the portal and projectile code and initially proposed missile exhaust trails. Before implementation, the user corrected that direction.

### 13. Correction: no missile exhaust trails

```text
no need for exhaust tails
```

The assistant acknowledged the correction and implemented shaded missile bodies and glowing tips without exhaust trails. Player and alien missiles received distinct designs. The wormhole moved to the top left, remained visible, expanded and pulsed during deployment, then contracted to an idle size.

Arrival control points were bounded to keep paths inside the playfield. All 18 assets, portal expansion/contraction, bounds, and arrivals were checked. The build passed and the missile artwork was visually inspected.

### 14. Operating-system run instructions; commit previous work first

```text
we also need to add details on how to run it to the README (add separate sections for Linux/Windows/Mac if relevant) - commit the previous work first
```

The Earth narrative, readable interface, persistent wormhole, and polished missiles were committed first as [`688a4b5`](https://github.com/trilitheus/space-attack/commit/688a4b5).

The README then gained prerequisites, separate Linux/Windows/macOS instructions, a PowerShell `npm.cmd` workaround, production builds, preview commands, and troubleshooting. Node.js and Git installation links were checked against official documentation. The README changes remained uncommitted until the next request.

### 15. Sparse star field and improved sound; commit README first

```text
ok - now let's add a background star field - it can be quite sparse and shouldn't distract from the gameplay - also the sounds are very basic - let's see if we can improve them a little - commit the readme work first
```

The README was committed as [`8e10f78`](https://github.com/trilitheus/space-attack/commit/8e10f78).

The existing background stars were replaced with 74 dim stars in three slowly moving layers. Layered synthesised effects were added for player/alien weapons, armour hits, explosions, shield damage, launch, wave transitions, sector clears, and game over. Volume control, compression, cached buffers, pitch variation, overlap limits, and mute/pause cancellation were added.

Audio samples were checked at 44.1 and 48 kHz for finite values, peaks, envelopes, and clean boundaries. Audio lifecycle checks covered mute, caching, overlap limits, cancellation, and resuming. The build passed.

### 16. Park star-field changes; vertical evasion and difficulty picker

```text
ok - the starfield doesn't really look like anything - let's park it for now - commit the current work - then i want to also add some limited up and down movement for more varied evasion - let's allow movement up to a third of the screen - let's also add a difficulty level picker (easy - medium - hard)
```

**User-reported unsuccessful result:** the star field did not look effective. The user explicitly parked further star-field work; it was left as-is rather than redesigned.

The existing star-field and sound work was committed as [`bf488cd`](https://github.com/trilitheus/space-attack/commit/bf488cd).

Vertical movement was added within the bottom third of the playfield, using arrows or WASD, with matching touch controls and normalised diagonal input. Easy/Medium/Hard selection was added to start and game-over screens. Medium kept the original balance; Easy used fewer/slower enemies, and Hard used more/faster enemies. Best scores were separated by difficulty, preserving the old record under Medium.

Movement bounds, idle/diagonal behaviour, difficulty ordering, and progression checks passed. The build passed.

### 17. Commit movement; ten-level campaign and final boss

```text
ok - let's get that committed  - next - let's cap this at 10 levels - for the final level let's have a big boss alien - defended by normal ones - give it a health bar requiring ultiple hits to defeat - once killed add a victory jingle and congraulatory message
```

The movement and difficulty changes were committed as [`f997bca`](https://github.com/trilitheus/space-attack/commit/f997bca).

The campaign was capped at 10 levels. Level 10 gained a large overlord with two rows of regular alien defenders. Boss health was 24/36/48 hits for Easy/Medium/Hard, displayed in a health bar. The boss fired aimed missile fans, with wider and faster volleys below half health. Killing it cleared remaining threats, awarded 5,000 points, and triggered a victory jingle and congratulatory "Earth is saved!" screen. Restart began again at level 1.

A repeatable scene-logic test suite was added with rendering/input stubs. Its checks covered all three difficulties, level progression, boss damage and health display, escort interception, pause, victory, defeat, cleanup, restarting, the level cap, volleys, and victory audio. Six tests and the build passed. The boss artwork was rendered and visually inspected.

### 18. Commit the boss; add defeat jingle

```text
ok good - commit - that - let's add a defeat jingle too
```

The boss campaign and victory ending were committed as [`c3ebd6d`](https://github.com/trilitheus/space-attack/commit/c3ebd6d).

The game-over sound was expanded into a 2.6-second defeat jingle with an impact, a descending minor melody, and a final chord. It stopped overlapping effects, respected mute, and stopped on restart. Tests verified it played only once and generated a complete waveform distinct from the victory jingle. Seven tests and the build passed.

### 19. Commit defeat jingle; set up GitHub Pages

```text
ok commit that as well - then let's set up hosting on github pages
```

The defeat jingle was committed as [`2e2b28f`](https://github.com/trilitheus/space-attack/commit/2e2b28f).

GitHub Pages deployment was configured with an Actions workflow that installs locked dependencies, runs tests, builds with the `/space-attack/` base path, and deploys `dist/` on pushes to `main`. The official action revisions and workflow YAML were checked. Hosting documentation was added.

**Failed attempts and recoveries:**

- Initial GitHub CLI requests failed with `error connecting to api.github.com` under restricted network access. Approved requests succeeded.
- The first Pages lookup returned HTTP 404. A subsequent create request returned HTTP 409, `GitHub Pages is already enabled.` Re-reading the settings confirmed Pages was enabled with `build_type: workflow`, so no destructive replacement was needed.

The deployment configuration was committed as [`518308c`](https://github.com/trilitheus/space-attack/commit/518308c) and pushed, including the local game commits since the remote's earlier graphics commit. GitHub's tests, build, and deployment succeeded. The site returned HTTP 200, and the deployed JavaScript and CSS matched the tested local build byte-for-byte.

While final hosting verification was being completed, the next prompt steered the active task toward canvas sizing.

### 20. Reduce the displayed canvas

```text
ok - the canvas is a bit large - let's reduce that down a bit
```

The maximum page width decreased from 1460px to 1200px, reducing the displayed canvas by about 19% while preserving logical gameplay coordinates and readable instructions. The Pages build passed. The resize initially remained local and uncommitted.

### 21. Commit and push the resize

```text
commit and push
```

The canvas resize was committed as [`264470d`](https://github.com/trilitheus/space-attack/commit/264470d) and pushed to `main`. Tests, build, and the GitHub Pages deployment succeeded. The game repository's working tree was clean.

### 22. Export this chronological history into a new repository

```text
create a new repo with the following details Complete chronological prompt-history link
*
Include exact prompts, follow-ups, corrections, and failed attempts in chronological order. Label separate Codex chats and show their order. A text or Markdown document is fine.
```

The assistant prepared this Markdown history in a separate repository, retaining every visible user-authored task prompt and documenting failed attempts in the relevant chronological entries. The one visible conversation is labelled **Codex Chat 1**; no additional chats are claimed.

## Implementation note

The user's initial preference was Phaser 3. The installed dependency resolved to `phaser ^4.2.1`, as recorded in the game repository's `package.json`; the final dependency version is therefore different from that initial preference. This history preserves the original prompt rather than silently rewriting it to match the installed version.

## Verification limits

Local TypeScript/build checks, numerical checks, real Phaser data-URL decoding, artwork renders, audio synthesis checks, scene-logic tests with rendering/input stubs, GitHub Actions results, and public-site HTTP/asset verification were performed. A connected browser was not available for assistant-driven interactive play-testing in this session. The user's feedback supplied the direct play observations, including the SVG startup failure and the ineffective star-field appearance.
