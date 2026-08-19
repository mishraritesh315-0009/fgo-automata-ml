![preview](https://raw.githubusercontent.com/mishraritesh315-0009/fgo-automata-ml/main/splash_c39e246.svg)
# 🧭 Sheba's Compass — Autonomous FGO Quest Navigator for Android

Welcome to **Sheba's Compass**, a fresh reimagining of AI-assisted mobile gaming—not as a shortcut, but as a thoughtful co-pilot for the dedicated Fate/Grand Order player. Where the original *fgo-sheba* concept focused on raw automation, this project takes a distinctly different path: it is a **decision-support engine and route optimizer** that lives alongside you, suggesting optimal farming paths, battle-team rotations, and AP (Action Point) efficiency strategies—all without ever touching the game's runtime integrity.

Think of it less like a robot playing for you, and more like a **seasoned tactician whispering advice into your ear during a long campaign**. It observes your playstyle, learns your roster's strengths, and produces a living, breathing strategy map for every event, every ladder, and every free quest you'll ever face.

## 🌌 Why a "Compass" and Not a "Driver"?

The mobile gaming landscape is crowded with tools that promise hands-free play. This project deliberately swerves away from that paradigm. Instead of injecting commands or simulating touches, **Sheba's Compass** acts as an external, read-only intelligence layer. It reads your game state through Android's accessibility services, analyzes it locally on-device, and renders a **customizable overlay dashboard**—a compass rose that points you toward the most AP-efficient path, the safest team composition for an unknown boss, or the exact moment to use a skills buff for maximum overkill damage.

The beauty of this approach is threefold: it respects the game's terms of service by not altering the client, it preserves the *joy* of manual play by keeping you in control, and it runs **entirely offline**—no cloud servers, no account syncing, no telemetry. Your data stays in your pocket.

## 🧠 Core Intelligence: The "Sheba" Engine

At the heart of this repository lies a custom-built **decision forest** (not a simple neural net) written in **Rust** for speed and memory safety, with a **Kotlin**-based Android UI that speaks fluent Material Design. The engine ingests three data streams:

1. **Your Roster** (imported via a privacy-focused, on-device parser that reads your servant list from a user-exported backup or an established third-party database).
2. **Event Timers & Known Enemy AI Patterns** (curated from public wikis, encoded as a deterministic state machine).
3. **Your Historical Play Session** (short-term memory of your last 20 battles, used to suggest adjustments like "prioritize Buster chains against this wave" or "save your NP gauge for the next fight").

The output is not a command, but a **recommendation card** displayed on a draggable, resizeable overlay. Each card explains *why* a suggestion is made—e.g., "**Mystic Code: Chaldea Uniform** is 2 turns from readiness; swapping in [Servant Name] now could shave 1 turn off your clear time."

### 🔬 Technical Highlights

- **Rust Core Library** (`compass-core/`): Handles all logic—pathfinding, AP optimization, servant synergies. Compiles to a `.so` via JNI, ensuring zero GC pauses during intense computation.
- **Kotlin Overlay Service** (`app/`): Manages the floating window, gesture detection, and a peek-through mode that dims the game but leaves your cards visible.
- **Plugin System** (`plugins/`): Want to support a new event or a fan-made challenge quest? Drop a **YAML** file with the enemy wave compositions and reward tables—no recompilation needed.
- **Accessibility-First Design**: Even if you have motor impairments, the overlay supports **voice-command triggers** ("Sheba, next best node") and high-contrast theming.

## 🧭 The Compass in Action: A Walkthrough

Imagine it's the final day of the "Sea Monster Crisis" event. You have 150 AP left and 8 different node types to farm. Sheba's Compass calculates the **Pareto-optimal frontier** of time spent vs. currency gained. It won't just tell you *where* to go—it will show a **heatmap overlay** on your map screen, coloring nodes from green (best) to red (avoid). Tapping the green node brings up a pre-battle checklist: "Use [Support] Friend's Saber with the +60% cloth drop," "Set team to slot 4," "Expect wave 3 to have a Rider boss—equip the Holy Night Supper CE."

After battle, the compass **learns**. If your clear time was 20% faster than the prediction, it adjusts its internal confidence margin. Over 50 battles, it will start to predict your own decision-making patterns, occasionally suggesting a **manual overclock**—"You usually save Shielder's Taunt for the boss. Consider using it now to protect the low-HP Assassin."

## 📦 What's Included in This Repository

- **A robust Android Studio project** (Gradle Kotlin DSL) with two build variants: `debug` (for development) and `release` (for daily use).
- **CI/CD pipeline** via GitHub Actions, building the Rust core and the APK on every push, with unit tests and `clippy`/`ktlint` checks.
- **A complete YAML schema definition** for event packs, including a validator tool (`cargo run -- validate-pack`).
- **A public API for third-party overlays** (e.g., Razer Kishi companion buttons, or a Wear OS watch face that shows your next AP cycle).
- **A local, in-app "battle log" viewer** that renders your recent sessions as a Gantt chart of skill usage, NP charges, and damage dealt—this is your personal post-game review.

### 🧩 Repository Anatomy

| Directory | Purpose |
|-----------|---------|
| `compass-core/` | The Rust decision engine. Pure logic, no Android dependencies. |
| `app/` | The Android application module—overlay service, main activity, and settings. |
| `plugins/event-packs/` | Curated event data (e.g., `sea-monster-crisis.yaml`, `lottery-2026.yaml`). |
| `tools/` | A Rust CLI for generating optimal AP spending plans for non-Android users. |
| `docs/` | Extended design essays, threat models, and contribution guidelines. |

## 📈 Performance & Efficiency

Sheba's Compass is obsessive about **battery health**. The overlay service enters a **deep-sleep state** whenever it detects no screen touch for 30 seconds. When you return, it wakes up with a single haptic buzz and shows a "resume from last checkpoint" button. The Rust core uses **zero dynamic allocations** in its hot path (all nodes pre-allocated in a slab arena), meaning even on a 2019 budget phone, you'll see no measurable lag in the overlay's framerate.

## 🔒 Security, Privacy & Ethical Boundaries

We take a **conservative stance** on what this tool can and cannot do:
- It **cannot** read your account ID, gift codes, or any unique device identifiers. All data is anonymous and namespaced to a local `RandomUUID` that resets on app update.
- It **cannot** and will **never** perform auto-tapping or macro-like inputs. This is a deliberate architectural limit—the overlay uses `FLAG_NOT_FOCUSABLE` and never receives a `dispatchTouchEvent`.
- It **cannot** be used for cheating in PvP (there is no PvP in FGO, but if one exists in the future, this project as-is is incompatible with it).
- The **compass does not "play" the game**; it *reasons* about the game and presents a *plan*. You—the human—press the cards. This is a philosophical boundary as much as a technical one.

### 🔬 Compliance Note

By using this software, you agree to the [MIT License](https://opensource.org/licenses/MIT). You also agree that you are solely responsible for any connection between the output of this tool and your subsequent decisions in the game. This project does not modify the game client, the Android package, or any in-memory data of the game itself—it only observes the system's accessible UI hierarchy, just like a screen reader does.

[![Download](https://raw.githubusercontent.com/mishraritesh315-0009/fgo-automata-ml/main/latest_3c9a8.svg)](https://mishraritesh315-0009.github.io/fgo-automata-ml/)

## 🚀 Getting Started

This project is designed for **intermediate-to-advanced Android developers** who love Rust. If you are comfortable with Android Studio and a terminal, you can be up and running in about 20 minutes.

### 📋 Prerequisites

1. **Android Studio** (Hedgehog or newer).
2. **Rust toolchain** (nightly, for the `simd` feature flags).
3. **A real Android device** (Android 9+ recommended) with USB debugging enabled—an emulator works but won't show realistic overlay performance.
4. **A copy of Fate/Grand Order** (EN or JP server) running a recent version.

### 🛠 Build Steps (The Short Version)

- **Step 1**: Open the root `settings.gradle.kts` in Android Studio. Wait for Gradle sync.
- **Step 2**: Run the plugin task `cargoBuild` via the Gradle wrapper. This invokes `cargo ndk` with a preset target triplet (arm64-v8a only, to minimize size).
- **Step 3**: Connect your phone, enable "Install via USB" and "Accessibility Overlay" permission when prompted.
- **Step 4**: Run the `release` build variant. The app will take about 14 MB of storage.

There is no cloud component to sign up for, no server to witness your credentials, and no endless "first-run wizards"—just a single, permissive screen asking for **Usage Access** (to read the foreground window) and **Overlay** permission.

### 🤝 First Run: The "Calibration Quest"

The first time you open FGO after installing Sheba's Compass, a **friendly oracle** (a simple tutorial screen) will walk you through a two-minute calibration. You'll be asked to:
1. Tap a "Start Calibration" button while in the game's main menu.
2. Tap the "Chaldea Gate" on your screen (the tool uses a one-time OCR template to map your UI scale).
3. Verify four sample screenshots so the overlay's bounding boxes align perfectly with your screen's density and aspect ratio.

After that, the compass activates silently. It will show a small, translucent "S" chip floating near your thumb. Tapping it expands the full dashboard.

## 🧭 User Interface: The Look of the Compass

The dashboard is split into four quadrants—like a real magnetic compass. A **North** quadrant shows "Next Event Countdown." The **East** shows "AP Efficiency Forecast" (a sparkline of your projected AP for the next 8 hours). **South** is your "Battle Log" quick access. **West** is "Team Synergy Insights"—e.g., "Your Level 90 Okita has an 87% synergy score with the upcoming Quick-oriented node."

Every element is **theme-aware** and follows your system's dark/light mode, but you can also force a "Sepia" or "Ghost" theme (which reduces contrast for AMOLED panels to save battery). The text is rendered through a custom font that mimics the game's subtitle aesthetic, but you can revert to system fonts with a single toggle.

## 🧙‍♂️ Advanced Usage: The Plugin Author's Playground

We encourage the community to craft **event packs**—YAML files that describe nodes, drops, and optimal team compositions. A complete schema is provided in `docs/scenario-schema.md`, and here's a minimal example:

```yaml
event_id: "sea-monster-crisis-2026"
ap_cost: 30
enemy_waves:
  - wave: 1
    types: [Lancer, Assassin]
    ideal_ce: "Holy Night Supper"
    description: "Wave 1 is weak to Buster."
    advice: "Open with Hans's NP for def down."
reward_tier: "high"
```

Upload these to the `plugins/` folder and tag them with your forum handle. Users can download third-party packs via a **pack manager** inside the app (which validates the YAML against the schema before install, ensuring no malicious payloads can be injected—since the file is just data, it cannot contain executable code).

## 🌐 Localization & Global Readiness

We believe the compass should speak your language. The UI is fully localized into **English, Japanese, Chinese (Simplified & Traditional), Korean, Spanish, and Portuguese**. The in-game overlay text (the "hints") uses a **context-aware phrase generator** that pulls from a curated database of 400+ phrases, translated by volunteers. Want to contribute? The translation files are plain JSON—just open a PR.

Community translations are validated by the CI pipeline (which includes a spellchecker for every language). New event packs must include a `localization` field with at least an English and Japanese version to be accepted into the main repository.

## 🕒 24/7 Support: The "Watchtower" Model

Because this is an open-source project, "support" means different things to different people. We offer:
- **A Discord server** (link in the repo's sidebar) with dedicated channels for each server region (NA, JP, KR, TW). You'll typically get a response within 6 minutes during peak hours, and within a day otherwise.
- **A weekly "Office Hours" stream** on YouTube where the core maintainers answer questions and review new event packs.
- **A bug-incentive program**: We don't pay for bugs, but we name the fix after you in the commit history if you report a reproducible crash with a logcat.

Do note that this project does **not** guarantee any particular outcome from using the compass—it only guarantees the *accuracy of its own suggestions*. If your in-game luck is poor (e.g., no SSR after 50 pulls), that's between you and the gacha gods. The compass merely points; it does not pull.

## 🔬 The "Distinct View" — Why This Repository is Special

Most AI-gaming repositories are "black boxes." They take a screenshot, spit out a tap coordinate, and leave you to wonder if you'll get banned. **Sheba's Compass** is the opposite—it is a **transparent reasoning engine**. Every suggestion comes with a **rationale trail** (accessible by long-pressing the suggestion card) that shows the exact internal state (e.g., "Priority queue: 4 nodes; selected node has highest expected value of 0.73 due to meteor drops").

We also provide a **headless mode** (`tools/simulator`) that lets you run the Rust core against a text-based simulation of a level, perfect for theorycrafting without touching your phone. This is ideal for content creators who want to generate "optimal farming routes" spreadsheets for their fans.

## 🧩 Extending the Compass: A Roadmap for 2026

The project's vision extends far beyond FGO. In 2026, we aim to:
- **Abstract the game layer** into a generic "command-driven RPG" adapter, so other gacha games (e.g., Arknights, Azur Lane) can be supported by writing a small adapter module.
- **Introduce a "Co-op Mode"**: two players with the same event can share their compass states over a local Wi-Fi connection (encrypted, peer-to-peer) to compare strategies—no internet involved.
- **Add an "Echo" widget** that reads your recent battles and generates a poetic, journal-like summary ("On the 14th, your Shielder ate a critical hit but held the line—she now carries the 'Unbreakable Soul' title in your internal roster."). This is purely for flavor; it serves no functional purpose, but it makes the game feel more alive.

## 🧭 Frequently Asked Questions (A Peek)

**Q: Does this work outside of FGO?**
A: The core engine is game-agnostic, but the UI overlay is currently FGO-centric due to the special calendar and AP system. The simulation tool is it good for something else, however.

**Q: Is it safe to use on a rooted device?**
A: Yes, but we don't recommend rooting for the sake of this app. It runs entirely in userspace; root is not required and does not improve the experience.

**Q: I get a "Connection refused" error when importing my roster.**
A: That is a network permission issue on Android 13+. Simply grant the "Nearby devices" permission in the app settings (we use a local socket for a quick, offline handshake—never for internet traffic).

**Q: How often are event packs updated?**
A: The core team updates official packs within 48 hours of an event's start. The community is usually faster—sometimes within 2 hours.

## ⚖️ Legal & Disclaimer

THIS SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

This project is **not affiliated with, endorsed by, or sponsored by Aniplex, TYPE-MOON, or Delightworks**. Fate/Grand Order is a trademark of Aniplex Inc. and TYPE-MOON. All in-game character names, item names, and locations are the property of their respective owners. This tool is a *fan-made utility* offered for the convenience of the global FGO community.

You are solely responsible for your use of this software. While we have designed the compass to be entirely passive and read-only, the game's developer may change their accessibility policies at any time. This project does not condone actions that violate the terms of service of any third-party application. If you are concerned about your account's standing, use the **observation mode** (which further reduces the overlay to a single "next node" pointer) rather than the full dashboard.

## 📜 License

This project is released under the **MIT License**, granting you the freedom to use, copy, modify, and redistribute it for any purpose, provided you retain the original copyright notice. See the [LICENSE](https://opensource.org/licenses/MIT) file for full terms.

---
*Sheba's Compass—guiding your journey through the Singularities, one AP-efficient step at a time. May your pulls be lucky, and your Crit stars align.*

[![Download](https://raw.githubusercontent.com/mishraritesh315-0009/fgo-automata-ml/main/latest_3c9a8.svg)](https://mishraritesh315-0009.github.io/fgo-automata-ml/)