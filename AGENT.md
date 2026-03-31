# AI Glasses Developer Rules

You are an expert AI assistant helping build applications for Android XR and AI Glasses. This project uses the `androidx.xr` Jetpack libraries. You must adhere to the following rules at all times when writing or refactoring code:

## 1. UI Components (Jetpack Glimmer)
- **ALWAYS** use `GlimmerTheme` for the base application theme.
- **NEVER** use standard Android Phone Compose elements (like standard `material3.Text` or `material3.Button`) for the core visual components unless specifically requested to do so for an edge case.
- **ALWAYS** prefer importing from `androidx.xr.glimmer.*` (e.g., `androidx.xr.glimmer.Button`, `androidx.xr.glimmer.Card`, `androidx.xr.glimmer.Text`, `androidx.xr.glimmer.TitleChip`).
- Use transparent background shapes and borderless modifiers to ensure the UI blends seamlessly into the spatial environment.

## 2. Dual Activity Architecture (Phone vs. Glasses)
- AI Glasses projects use a tethered companion architecture consisting of two main entry points:
  1. `MainActivity.kt`: Runs on the **Host Mobile Phone**. This should ONLY use standard Android Jetpack Compose (`androidx.compose.material3`). It handles settings, permissions, background audio, and host configuration.
  2. `GlassesMainActivity.kt`: Runs on the **AI Glasses**. This must exclusively use Jetpack XR Glimmer (`androidx.xr.glimmer`) and never standard `material3`.
- **IMPORTANT**: If the user asks for a "settings screen" or "companion menu," build it in `MainActivity`. If the user asks for a "HUD widget," build it in `GlassesMainActivity`. If it is ever unclear which Activity the user intends to modify, **ALWAYS pause and ask for clarification** before scaffolding any new UI code.

## 3. Layouts and Window Configuration
- Do **NOT** remove or modify the `ProjectedDisplayController` setup in `onCreate()`. It is specifically injected to provide `FLAG_KEEP_SCREEN_ON` to prevent the device from sleeping.
- Do **NOT** alter the `<activity>` tag in `AndroidManifest.xml` that declares `android:requiredDisplayCategory="xr_projected"`.

## 4. Handling User Input (Mudra / HID)
- XR Glasses interactions are primarily driven by connected HID peripherals (like the Mudra band).
- Interactions should be mapped to standard D-Pad (Directional Pad) key events.
- To listen for these inputs, use `dispatchKeyEvent` or `onKeyDown`/`onKeyUp` inside the `ComponentActivity`, specifically checking for:
  - `KeyEvent.KEYCODE_DPAD_UP`, `DOWN`, `LEFT`, `RIGHT`
  - `KeyEvent.KEYCODE_ENTER` or `KeyEvent.KEYCODE_DPAD_CENTER` (for selection/clicking)
  - `KeyEvent.KEYCODE_V` (for secondary interactions like Volume).

## 5. Design & Usability Guidelines
- **CRITICAL:** This is an **AI Glasses** project, NOT a generic XR Headset VR app! 
- The user's real-world vision must remain unobstructed. 
- ALWAYS render your UI components anchored to the **bottom third of the screen** (e.g., using `Alignment.BottomCenter` inside a `Box(Modifier.fillMaxSize())`).
- Keep components compact, glanceable, and minimal. Do not center massive UI panels that block the user's field of view.

## 6. Dependencies & Updates
- **ALWAYS** manage dependencies in `gradle/libs.versions.toml`. Do not hardcode version strings in the `build.gradle.kts` files.

## 7. Web Search & Documentation Retrieval
- **IMPORTANT:** Do NOT automatically search the web for Jetpack XR / Glimmer API updates on every prompt. This wastes time.
- **ONLY** perform web searches or retrieve official Jetpack XR documentation if:
  1. **(First Prompt Only):** It is the very first setup prompt in a new project. Briefly verify if the Canary versions inside `libs.versions.toml` are still the latest before scaffolding.
  2. The user explicitly asks you to "check for updates" or "find the latest XR docs".
  3. A specific `androidx.xr` library or syntax explicitly fails to compile, and you need to troubleshoot a breaking Canary update.
