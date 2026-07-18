# Recomp

A hypertrophy logbook with a built-in double-progression engine. 12-week block, 6-day PPL split, offline-first PWA. No build step, no dependencies, no backend.

---

## Deploy (GitHub Pages, ~5 min)

1. On GitHub: **New repository** → name it `recomp` → **Public** → Create.
2. Click **uploading an existing file**.
3. Drag in **every file and the `icons` folder** from this bundle.
4. **Commit changes**.
5. Repo → **Settings** → **Pages** (left sidebar).
6. Under *Build and deployment* → Source: **Deploy from a branch** → Branch: **main** → Folder: **/ (root)** → **Save**.
7. Wait ~60 seconds. Your URL appears at the top:
   `https://YOURNAME.github.io/recomp/`

### Install on iPhone
Open that URL in **Safari** (must be Safari — Chrome can't install PWAs on iOS) → **Share** (□↑) → **Add to Home Screen**.

### Install on Android
Open in Chrome → menu (⋮) → **Install app**.

You now have a real app icon. Fullscreen, no browser bars, works with zero signal.

**Verify it worked:** open the app → **Track** tab → scroll to the bottom. If it says *"Preview mode"*, storage isn't persisting. If that text is gone, you're live. Log a set, force-close, reopen — the number should still be there.

---

## Files

| File | Purpose |
|---|---|
| `index.html` | The entire app — markup, styles, program data, logic. Everything is here. |
| `manifest.webmanifest` | Tells the OS it's an installable app (name, icon, colours, fullscreen). |
| `sw.js` | Service worker. Caches everything so the app opens offline and instantly. |
| `icons/` | App icons: 192, 512, maskable (Android adaptive), and Apple touch icon. |

---

## Making changes

Everything lives in `index.html`. Edit it, commit, and GitHub Pages redeploys in about a minute.

### ⚠️ The one gotcha: bump the cache version

The service worker caches aggressively — that's what makes it work offline. But it means **installed phones will keep serving the old version** unless you tell them something changed.

**Every time you deploy a change, open `sw.js` and increment the version:**

```js
const CACHE = 'recomp-v1';   // → 'recomp-v2', 'recomp-v3', ...
```

Forget this and you'll swear your changes didn't deploy. They did — your phone is just serving cache.

### Changing the program

The program is plain data near the top of the `<script>` block. No framework, no build.

**Change sets, reps, or rest on an exercise:**
```js
{n:'Barbell Flat Bench Press', s:4, r:[5,7], rpe:'8', rest:180, inc:2.5, sub:[...]}
//                              │      │              │        │
//                          sets  rep range      rest(sec)  weight jump when you overload
```

**Add an exercise** — drop a new object into the `ex:[...]` array for that day.

**Add a whole training day** — copy a `{id, label, focus, day, ex:[...]}` block into `DAYS`. The tabs, logging, and progression engine all pick it up automatically.

**Change the phases** — edit `PHASES`. Week ranges, RPE targets, and the banner text.

**Change deload volume** — `setsFor()`. Currently `base * 0.5` on weeks 6 and 12.

**Change the progression rule** — `verdict()`. Currently: every set at the top of the rep range → fire "add weight."

**Change the calorie/macro targets** — `FUEL`.
**Change the fat-loss decision rules** — `viewTrack()`.

---

## Roadmap to the App Store

Right now this is a **PWA** — a web app that installs like a native one. For a personal tool that is genuinely the correct answer, and it's what most fitness apps should have been.

If you later want it in the actual App Store / Play Store:

### Step 1 — Wrap it with Capacitor
[Capacitor](https://capacitorjs.com) takes a web app and produces real native iOS and Android projects.

```bash
npm install @capacitor/core @capacitor/cli
npx cap init Recomp com.yourname.recomp
npx cap add ios
npx cap add android
# put index.html + assets in the `www` folder
npx cap sync
npx cap open ios      # opens Xcode
npx cap open android  # opens Android Studio
```

### Step 2 — What you'll need
| | Requirement | Cost |
|---|---|---|
| **iOS** | A Mac + Xcode. No way around the Mac. | Apple Developer: **$99/year** |
| **Android** | Android Studio (Windows/Mac/Linux). | Google Play: **$25 once** |

### Step 3 — Be ready for this
Apple **rejects apps that are thin wrappers around a website** (App Store Guideline 4.2, "Minimum Functionality"). To pass review you'll want genuinely native touches — local notifications for rest timers, HealthKit integration to pull body weight and steps, haptics, widgets. Android is far more permissive.

**Honest advice:** ship the PWA, use it for the full 12 weeks, and let real use tell you what's missing. Most feature ideas die on contact with a squat rack. Wrap it natively once you know what it actually needs — and once you have twelve weeks of your own data proving it works.

---

## Data & privacy

Everything is stored in your browser's `localStorage`, on your device only. Nothing is uploaded anywhere. There is no server, no analytics, no account.

**This means the data is only as safe as the browser.** Clearing site data wipes it. Use **Track → Export logbook** every few weeks — it saves a JSON backup to your phone.

If you ever want cross-device sync, that's the point where you'd need a backend (Supabase or Firebase would each take an afternoon).

---

## Program summary

**Split:** Push A / Pull A / Legs A / Push B / Pull B / Legs B / Rest — every muscle 2×/week.
**Volume:** ~149 hard sets/week. Deload weeks 6 and 12 at 60%.
**Progression:** double progression. Hit the top of the rep range on *every* set → add weight.
**Cardio:** Zone 2 only (110–129 bpm). Never a stepper on or before a leg day.
**Nutrition:** calorie and macro targets configurable in the `FUEL` block.

---

## Privacy

**No personal data is stored in this repository.** All logged sets, body weights, and measurements live in `localStorage` on the device only. There is no server, no account, no analytics, and no network request of any kind.

Before making this repo public, check that you haven't hardcoded personal details (body weight, body-fat %, calorie targets) into `index.html`. The `FUEL` block and header are the places to check.
