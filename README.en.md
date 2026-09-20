# Particle Editor

[繁體中文](README.md) | **English**

Create, preview, bake, and export particle-based skeletal animations for **Spine 3.8.99** directly in your browser.

No installation or account is required. Images, settings, and exported files are processed locally in the user's browser.

This repository is the **introduction, documentation, and issue-reporting page** for Particle Editor. It does not contain the editor source code.

![Particle Editor demo](images/spine錄影17m_gif.gif)

![Particle Editor overview](images/editor-overview.png)

## Try It Online

### [Open Particle Editor](https://particle-editor.kirinlab.workers.dev/)

### Online URL: [https://particle-editor.kirinlab.workers.dev](https://particle-editor.kirinlab.workers.dev/)

### [Watch the YouTube Tutorial](https://youtu.be/a_gSDpEJlk4)

### [Detailed Input/Output Tutorial with Channel Subtitles](https://youtu.be/3RqR93i5vqA)

If you have ideas for future updates, feel free to leave a request in the comments on the YouTube tutorials. The author will review the feedback and consider it as a reference for future development.

The current version:

- Does not collect analytics
- Does not upload project images or settings
- Does not upload exported JSON or ZIP files
- Does not require a name, email address, or GitHub account

**[Support continued development (Ko-fi)](https://ko-fi.com/kirinylab)**

## What Does It Do?

Particle Editor converts particle effects into skeletal animations that can be imported into Spine. Each particle becomes a bone, with position, size, rotation, color, opacity, and attachment visibility baked into animation timelines.

Key features:

- Chinese/English interface switching, with the preference stored only in the local browser
- Live Canvas particle preview
- Baked preview with fixed random results
- Loop preview based on the exported timeline
- Point, directional, circle, ring, box, and box-ring emitters
- Independent X/Y coordinates for each emitter, consistent across preview and Spine export
- Duplicate the selected emitter's settings, curves, and image for color or variation effects
- Directional emitters support total spread angle, emitter width, and even initial positions
- Circle, ring, box, and box-ring dimensions up to 10,000 px
- Outward, inward, and split emission directions
- Evenly distributed initial spawn positions
- Reproducible random seeds: the same seed keeps the same distribution, while a different seed changes it, including the phase of even distributions
- Emission duration, particle lifetime, and random variation
- Up to 300 particles per emitter; higher counts increase Spine bones, JSON size, and preview load
- A separate Motion section with Distance and Initial Speed (physics) modes
- Emission, speed, size, and opacity curves
- Fixed random size per particle using Base Size +/- Random Size
- Editable color timeline with draggable keys
- Original-color and Multiply gradient modes
- Vertical gravity, 360-degree wind direction, wind strength, rotation speed, and direction alignment
- Optional background preview, positioning, and export
- 50%, 60%, and 70% loop cuts
- Repeat and proportionally retime `_repeat` to an exact target frame count
- One-click ZIP export

## How to Use

### Online Version

Open the online editor in a supported browser. No installation or sign-in is required.

### Offline Version

This repository contains only the introduction, documentation, screenshots, and issue-reporting page. It does not include an offline copy of the editor.

The offline test version is currently provided individually by the author. Do not download this repository expecting to find `index.html` or `particle-editor.html`. A download link will be published here if an official offline version becomes available.

## Recommended Workflow

1. Add, duplicate, or select an emitter. Set independent X/Y coordinates under ID / Naming when needed.
2. Adjust its shape, emission, motion, physics, rotation, and visual settings.
3. Use Live Preview to check the overall effect.
4. Select Bake to lock particle timing, lifetime, direction, and random values.
5. Use Baked Preview to review the fixed export result.
6. For a looping animation, choose a 50%, 60%, or 70% loop cut and create the loop.
7. Use Loop Preview to check the `_repeat` animation.
8. For an exact duration, enter a frame count under Target Loop, then create and preview the target animation.
9. Enter a file name and export the bake.

After changing any parameters, create or update the bake again. Existing baked data is not updated automatically.

## Interface and Motion Modes

The emitter list and parameter controls are on the left, with the preview on the right. The first toolbar row contains preview, background, view, loading, and export controls; the second row contains the bake and loop workflow. Background and view settings open as floating panels without reducing preview height.

Each emitter has independent X/Y coordinates under **ID / Naming** and two choices under **Motion**:

- **Distance (legacy mode)**: sets the total distance traveled during the particle lifetime. Increasing lifetime lowers average speed. Older settings continue to use this mode.
- **Initial Speed (physics)**: sets launch speed directly in `px/s`. Lifetime changes only when the particle disappears; velocity, direction, gravity, and wind determine the path.

Older settings without coordinates use `(0,0)`. If initial-speed data is absent, the editor derives a starting value from the existing distance and lifetime.

## Loop Cuts

Creating a loop keeps the original animation and adds:

```text
original_animation_name_repeat
```

For example, an animation named `particle01` produces a loop named `particle01_repeat`.

Available cut ratios:

- **50%**: a shorter loop that may require closer overlap and trajectory inspection
- **60%**: shorter and denser loop timing
- **70%**: the default, compatible with the current loop reference

Frame calculation:

```text
total frames = floor(original duration in seconds x 30)
cut frame = floor(total frames x 0.5, 0.6, or 0.7)
```

The editor moves animation data after the cut back to frame 0, removes keys after the cut from their original positions, and keeps the cut as the final frame of the `_repeat` animation. Attachment visibility is processed as well.

After changing the cut ratio, create the loop again so the preview and export use the same ratio.

### Target Loop Duration

After creating `_repeat`, enter an exact target frame count. The editor chooses the closest whole number of repetitions, then proportionally retimes all bone, slot, color, attachment, scale, and rotation timelines. For example, a 39-frame loop targeting 100 frames uses three complete repetitions and retimes them to 100 frames, producing `particle01_repeat_x3_100`.

The editor analyzes visible lifetimes, abnormal speed, and loop seams. Suspected visible snap-back blocks creation, while substantial speed jumps produce a warning. A target shorter than one loop or requiring more than 120 repetitions is also rejected. These checks reduce risk but do not replace final seam inspection in Spine.

## Export Contents

Supported browsers download a standard ZIP file. For a project named `particles`:

To avoid browser-generated names such as `particles (1).zip`, ZIP names include a bake number and local export time, for example `particles_b3316_260826-143052.zip`. Spaces, parentheses, and unsafe path characters become underscores; another export within the same second receives `_02`.

```text
particles.zip
|- particles.json
|- particles_notforspine.json
`- images/
   |- 1_par_01.png
   |- 2_par_01.png
   `- background.png
```

- `particles.json`: animation data for importing into Spine
- `particles_notforspine.json`: Particle Editor settings for reloading the project; do not import this file into Spine
- `images/`: one particle image per emitter, plus the optional exported background

The loop-cut ratio and target frame count are saved in `_notforspine.json`. Older settings without these fields use 70% and 100 frames.

## Importing into Spine

1. Extract the exported ZIP file.
2. In Spine, select **File -> Import Data**.
3. Select `particles.json`.
4. Point the image path to the extracted `images/` directory.
5. The original animation uses the emitter name. If a loop was created, an additional `_repeat` animation appears under Animations.

Particle Editor does not generate an `.atlas` file. Create one separately in Spine or another atlas tool if your workflow requires it.

## Custom Images and Backgrounds

Visual's Original Image Facing option can be Right, Up, Left, or Down. Angle Fine-tune compensates for artwork that is slightly tilted. Both work together with Rotation's Align to Direction option in live preview, baked preview, and export. Older settings default to Right and preserve the previous correction angle as Angle Fine-tune.

- Each emitter uses a unique image name, such as `1_par_01.png` and `2_par_01.png`.
- Replacing an image after baking updates baked preview, loop preview, and export without resampling particle timing or paths.
- Custom particle images preserve their original aspect ratio and pixel dimensions. Images larger than 2048 px on their longest side are resized proportionally.
- Base Size controls the displayed size in Canvas and Spine, using the image's longest side as the reference.
- Spine attachments use the exported PNG's actual pixel dimensions; display size is represented by bone scale to prevent oversized particles after import.
- Random Size is sampled once when each particle is created. For example, Base Size 5 and Random Size +/-2 produce sizes from 3 to 7 px before applying the size curve.
- Built-in images are exported at sufficient resolution for the base size, maximum random size, and maximum size-curve multiplier.
- Custom images under **2 MB** and no larger than **2048 px** are recommended.
- A background is used only for preview unless Export Background is enabled.
- Background images keep their original aspect ratio and use a dedicated `background` bone below particle slots.
- Fit Background changes only the preview camera. It does not resize the image, move Root, or affect Spine output.
- Reloading `_notforspine.json` restores background coordinates, but the image must be selected again because of browser privacy restrictions.

## Preview Modes

- **Live Preview**: quickly reflects current settings; random details may change
- **Baked Preview**: plays the most recently fixed bake
- **Loop Preview**: samples the timeline that will be exported as `_repeat`

Replay restarts the current result without resampling it. Use Resample Bake to generate a different fixed result.

## Privacy and Network Verification

The current version has undergone static inspection and automated browser testing with synthetic PNG files and particle settings. The tested workflow covers file loading, parameter changes, previewing, baking, looping, and ZIP export.

Results as of **September 16, 2026**:

- No cross-origin requests were detected.
- No POST, PUT, PATCH, DELETE, Fetch/XHR, WebSocket, EventSource, or Beacon activity was detected.
- No third-party analytics, tracking, or telemetry services were detected.
- Only same-origin HTTPS GET requests for the page and default particle image were observed.
- User-loaded images, particle settings, and exported content were not transmitted externally.

These results apply only to the tested version and workflows. Verification is repeated when future versions are updated.

## Privacy Notes

Particle Editor is currently a client-side web tool:

- Project images are processed in browser memory
- Settings files are read only when the user selects them
- JSON and ZIP files are generated locally in the browser
- No analytics API, user tracking, or device fingerprinting is included
- The only `localStorage` item is the Chinese/English interface preference; it stays on the device and contains no personal data

The online version must still be served by website infrastructure, which may generate ordinary connection logs. Particle Editor itself does not actively collect or transmit project content.

## Usage and Licensing

Particle Editor is currently available as a **free public beta**. Its source code is not public.

This repository provides only the introduction, documentation, and issue-reporting page. It does not mean Particle Editor is open source and does not grant permission to copy, modify, redistribute, or sell the editor source code.

Without the author's permission, do not:

- Copy or redistribute the complete Particle Editor application
- Present a modified version as an official Particle Editor release
- Remove or alter author, version, or source attribution
- Sell copied versions or provide unauthorized commercial services based on them

Users may freely create and export their own particle animations through the official online editor. Images, settings, and output created by a user remain the property of that user.

## Compatibility and Limitations

- Target format: Spine 3.8.99
- Recommended browsers: latest Chrome or Edge
- Up to 10 emitters
- Up to 300 particles per emitter
- Each particle adds one Spine bone; choose particle counts according to project performance requirements
- Canvas and Spine are different rendering environments, so blending, curve sampling, and pixel output may differ slightly
- Baked and loop data exist only in the current browser session; loading settings requires baking again
- There is no automatic save; export the ZIP before closing the page
- Interface language affects labels only; it does not change JSON, ZIP, bone, or slot names

## Reporting Issues

Use GitHub **Issues** to report test results, bugs, or feature requests. Users do not need collaborator access or write permission to this repository.

When reporting an issue, include:

- Browser and version
- Spine version
- Steps to reproduce
- A screenshot of the problem
- A shareable `_notforspine.json` example, when appropriate

Remove all company names, project names, confidential images, and other non-public information before submitting an issue.

## Supporting Continued Development

Particle Editor continues to improve through testing and feedback. If you find it useful, you may support continued maintenance through [Ko-fi](https://ko-fi.com/kirinylab). Support is entirely optional and does not affect features, exports, or usage rights.
