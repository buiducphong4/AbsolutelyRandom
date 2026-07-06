# FORTUNA — Wheel of Names
## Project Specification Document

> A luxury-aesthetic random name picker wheel web application with a hidden "rigged winner" feature for controlled outcomes.

---

## 1. Project Overview

**Product Name:** Fortuna & Fate — Wheel of Names
**Type:** Single-page web application (static HTML/CSS/JS, no backend)
**Primary Use Case:** Random name selection for events, team activities, giveaways, classroom picks
**Secret Use Case:** Host-controlled outcomes when a specific winner is required

**Design Philosophy:** Editorial / luxury / casino-classic aesthetic — dark jewel tones, gold accents, serif italic typography. NOT a toy-like or neon design.

---

## 2. Tech Stack Requirements

- **Pure HTML + CSS + Vanilla JavaScript** — single file, no frameworks
- **No backend, no database** — all state in-memory
- **No build step** — must work by opening the `.html` file directly in a browser
- **No browser storage APIs** (localStorage, sessionStorage) — use in-memory state only
- **Fonts:** Google Fonts
  - Display: `Cormorant Garamond` (italic, 500 weight) — for wheel text, title, winner names
  - Body: `Space Grotesk` (300–700) — for general UI
  - Mono: `JetBrains Mono` — for labels, counts, timestamps

---

## 3. Design System

### 3.1 Color Palette (CSS Variables)

```css
--bg-0: #0b0a0e;          /* Deepest background */
--bg-1: #13121a;          /* Panel background */
--bg-2: #1c1a26;          /* Panel gradient bottom */
--ink: #f5f1e8;           /* Primary text (warm off-white) */
--ink-dim: #8a8596;       /* Secondary text */
--ink-faint: #4a4656;     /* Tertiary/faint text */
--gold: #d4a945;          /* Primary accent */
--gold-bright: #f0c85a;   /* Gold highlight */
--crimson: #c73e3a;       /* Destructive/warning */
--emerald: #3a7d5c;       /* Success/active indicator */
--line: rgba(245, 241, 232, 0.08);         /* Subtle dividers */
--line-strong: rgba(245, 241, 232, 0.18);  /* Stronger dividers */
```

### 3.2 Wheel Slice Colors (rotate through these)
Deep jewel tones — rich reds, golds, emeralds, sapphires, plums:
```
#c73e3a, #d4a945, #3a7d5c, #2e5c8a,
#8b4789, #b85d3a, #5c7a3d, #3d6b7a,
#a83c5c, #c4923a, #4a6b4a, #5a4a7a
```

### 3.3 Visual Atmosphere

- **Film grain overlay:** SVG noise texture at 3.5% opacity, `mix-blend-mode: overlay`
- **Radial gradient glows:** 3 colored radial gradients at different corners (gold at top, crimson bottom-right, emerald bottom-left)
- **Grain texture** uses inline SVG data URI with `feTurbulence`

---

## 4. Layout Structure

### 4.1 Header
- **Left side:**
  - Brand mark: "Est. MMXXV" in a gold-bordered pill, plus "№ 001"
  - Title: "Fortuna **& **Fate" — ampersand in gold, italic serif, 2.2–3.8rem responsive
- **Right side:**
  - Green pulsing dot + "SYSTEM READY" status
  - Live clock showing `HH:MM:SS LOCAL` (updates every second)
- Bottom border: subtle line divider

### 4.2 Main Grid (2-column, 1400px max-width)
- **Left column (flex):** The Wheel + Spin Button
- **Right column (420px fixed):** Input panel + Winners Log panel
- **Responsive:** Below 960px, stack vertically

### 4.3 The Wheel Stage
- Container: `min(560px, 90vw)` square
- **Decorative elements around the wheel:**
  - Outer ring: 1px solid line at `-40px` inset, with 2 inner dashed/solid rings
  - Rotating tick marks: 72 ticks (every 5°), major ticks every 30° in gold; rotates 360° every 60s
  - 4 corner ornaments: SVG line-art in gold, 80x80px, mirrored per corner
  - Stage labels (top-left / top-right) in mono font: "◆ The Wheel" / "N entries"
- **The wheel itself (SVG):**
  - viewBox 400x400
  - Each slice: radial gradient (lighter center, darker edge)
  - Text: Cormorant Garamond italic, with dark stroke/paint-order for readability
  - Outer gold ring + inner accent ring
  - Drop shadow for depth
- **Pointer (at top):** Gold teardrop shape with circular pivot, SVG gradient fill, drop shadow
- **Hub (center):** 72px radial gradient circle with gold border, inner ring, and small gold dot center
- **Spin animation:** `transition: transform 5s cubic-bezier(0.17, 0.67, 0.12, 0.99)`

### 4.4 Spin Button
- Font: Cormorant Garamond italic 1.6rem, uppercase, letter-spacing 0.2em
- Label: "◆ Spin ◆"
- Background: gold gradient `linear-gradient(180deg, --gold-bright, --gold)`
- Text color: dark bg-0
- Secondary outer border at `-6px` inset with 0.4 opacity, expands to `-10px` + 0.8 opacity on hover
- Box shadow: gold glow + inset highlight lines for 3D feel
- Hover: translateY(-2px) + stronger shadow
- Disabled: 0.3 opacity + grayscale

### 4.5 Side Panels
Each panel:
- Gradient bg (bg-1 → bg-2), 1px line border, 4px radius
- **Top accent line:** gradient `transparent → gold → transparent` at 0.4 opacity
- Header: italic serif title with mono "№" prefix + mono sub-label on right
- 28px padding

**Panel 01 — Participants:**
- `<textarea>` for names (min-height 220px), one per line
- Meta row: gold count "N / entries" + hint "↵ new line"
- Button row: "⟲ Shuffle" + "✕ Clear" (ghost buttons, danger variant for Clear)

**Panel 02 — Winners Log:**
- Scrollable list (max-height 280px)
- Each entry: gold number + italic serif name + mono timestamp
- Dashed line dividers
- Slide-in animation on new entry
- Empty state: "No draws yet — the wheel awaits" (italic, faint)

### 4.6 Footer
- "© FORTUNA · Wheel of Fate" on left
- "v 2.0 ◆ SPACE = SPIN" on right
- Mono font, faint color, small size

---

## 5. Winner Modal

Triggered after each spin completes.

- **Overlay:** `rgba(11, 10, 14, 0.92)` with `backdrop-filter: blur(20px)`
- **Modal box:**
  - Gradient bg, 1px gold border, 4px radius
  - 60px × 70px padding
  - **Corner brackets:** Gold L-shaped brackets at top-left and bottom-right (via `::before` and `::after`)
  - Reveal animation: scale 0.9 → 1 with cubic-bezier ease
- **Content:**
  - Mono label: "◆ THE WHEEL HAS SPOKEN ◆" in gold
  - Serif flourish: "~" large italic gold
  - Winner name: Cormorant Garamond italic, 2.4–4rem responsive
  - Two buttons:
    - Secondary: "Remove Winner" (ghost, turns crimson on hover)
    - Primary: "Continue" (solid gold)
- Click outside to close

**Celebration effect:** 40 gold "sparks" (4px dots) fly out in staggered sequence from center — fade + scale + random horizontal drift.

---

## 6. HIDDEN SETTINGS PANEL (Critical Feature)

### 6.1 The Hidden Trigger (CRITICAL — must be undetectable)

**Location:** Fixed position at bottom-right corner of viewport (16px from each edge)

**Appearance:** A 16×16px transparent container with:
- Inner 6×6 gray dot (`--ink-faint`) at 25% opacity
- Outer thin border circle at 12% opacity
- Looks like a decorative UI element or a status indicator

**CRITICAL BEHAVIOR:**
- `cursor: default` — **DO NOT use `cursor: pointer`**. The cursor must NOT change to a hand when hovering. This is the single most important detail. If the cursor changes, users will discover the feature.
- No tooltip, no hover effect, no visual feedback whatsoever
- No `title` attribute
- No aria-label that reveals function

**Activation:** Triple-click within 1.5 seconds to open the settings panel. A single or double click does nothing visible.

**Backup keyboard shortcut:** `Ctrl + Alt + .` (period) toggles the panel.

### 6.2 Settings Panel UI

**Panel:**
- Fixed, right side, 420px wide (95vw max on mobile)
- Full viewport height
- Background: `--bg-1` with gold left border
- Heavy shadow: `-30px 0 80px rgba(0, 0, 0, 0.6)`
- Slide-in from right: `transform: translateX(100%)` → `translateX(0)` with 500ms cubic-bezier transition
- Top gold gradient accent line

**Header:**
- Title: "Sub Rosa" (italic serif, gold, 1.8rem) — Latin for "under the rose" (= in secret)
- Subtitle: "◆ Hidden Configuration ◆" (mono, uppercase, dim)
- Close button (✕) on right

**Warning box:**
- Gold border + 4% gold background
- Text: "⚠ This panel is hidden from regular users. Close before anyone else views the screen."

### 6.3 Settings Groups (each with dashed bottom divider)

**Group 1: Outcome Control**
- Toggle: "Predetermined winner" (serif italic text)
- Status indicator below:
  - Inactive state: gray dot + "Inactive — random mode" (crimson color)
  - Enabled but no target: gray dot + "Enabled — select a target"
  - Fully active: pulsing emerald dot + `Active — "NAME" will win` (emerald color)

**Group 2: Target Winner**
- `<select>` dropdown populated dynamically from the participants list
- Auto-updates when names input changes
- Placeholder option: "— Select from entries —"
- Hint: "The wheel will always land on this name while control is active."

**Group 3: Spin Duration**
- `<select>` with 4 options:
  - "Brisk · 3s" → 3 second spin
  - "Standard · 5s" → 5 second spin (default)
  - "Suspenseful · 7s" → 7 second spin
  - "Random (3–7s) · recommended" → random duration each spin
- Hint: "Varying duration avoids suspicion. Random is most natural."

**Group 4: Auto-remove Winner**
- Toggle: "Remove name after each draw"
- Hint: "Automatically excludes winners from the next spin."

**Group 5: Trigger Methods (info only)**
- Label + hint:
  - "◆ Triple-click the dot at bottom-right corner"
  - "◆ Or press **Ctrl + Alt + .**"

### 6.4 Toggle Switch Styling

- 48×26px rounded pill
- Default: dark bg, gray knob
- Active: gold-tinted bg, gold knob with glow shadow
- Smooth 0.3s transition

---

## 7. Core Logic

### 7.1 State Object

```javascript
const state = {
  names: [],              // Array of trimmed name strings
  rotation: 0,            // Cumulative rotation in degrees
  spinning: false,        // Prevents concurrent spins
  lastWinner: null,       // { name, index } of most recent winner
  history: [],            // Array of { name, time } — newest first
  rigged: {
    enabled: false,
    target: ''            // Name to always win
  },
  delayMode: 'normal',    // 'short' | 'normal' | 'long' | 'random'
  autoRemove: false,
  secretClicks: 0,        // Counter for triple-click detection
  secretTimer: null       // Timeout reference for click reset
};
```

### 7.2 Name Parsing
- Split textarea value by `\n`
- Trim each line
- Filter out empty strings
- Re-render wheel + update rigged dropdown on every input event

### 7.3 Wheel Drawing (SVG)

- Calculate `anglePerSlice = 360 / n`
- Center (200, 200), radius 195
- For each name:
  - Compute start/end angles (offset by -90° so first slice starts at top)
  - Draw pie-slice path: `M cx cy L x1 y1 A r r 0 largeArc 1 x2 y2 Z`
  - Fill with radial gradient (cycle through 12 colors, lightened 15% at center)
  - Add inner arc line at 82% radius for visual detail
  - Place text at 62% radius, rotated to align radially, with dark text stroke for legibility
  - Truncate names > 14 chars (fewer if many names); scale font size by count

**Empty state:** Dashed gold circle + "Awaiting entries" italic + "ADD NAMES TO BEGIN" mono caption

### 7.4 Spin Algorithm (CRITICAL MATH)

```javascript
function spin() {
  if (state.spinning || state.names.length < 2) return;
  state.spinning = true;

  const n = state.names.length;
  const anglePerSlice = 360 / n;

  // Determine target
  let targetIndex;
  if (state.rigged.enabled && state.rigged.target) {
    const idx = state.names.indexOf(state.rigged.target);
    targetIndex = idx >= 0 ? idx : Math.floor(Math.random() * n);
  } else {
    targetIndex = Math.floor(Math.random() * n);
  }

  // Compute final angle such that middle of target slice ends up at top (0°)
  const sliceMidAngle = targetIndex * anglePerSlice + anglePerSlice / 2;
  // Natural jitter within slice (±35% of slice width) — avoids always landing dead center
  const jitter = (Math.random() - 0.5) * anglePerSlice * 0.7;
  // 5–8 full rotations for drama
  const extraRotations = 5 + Math.floor(Math.random() * 4);

  const currentMod = state.rotation % 360;
  const targetMod = (360 - sliceMidAngle - jitter + 360) % 360;
  let delta = targetMod - currentMod;
  if (delta < 0) delta += 360;
  state.rotation += extraRotations * 360 + delta;

  // Duration based on setting
  let duration;
  if (state.delayMode === 'random') {
    duration = 3 + Math.random() * 4;
  } else {
    duration = state.delayMode === 'long' ? 7 : state.delayMode === 'short' ? 3 : 5;
  }

  wheel.style.transitionDuration = `${duration}s`;
  wheel.style.transform = `rotate(${state.rotation}deg)`;

  setTimeout(() => {
    showWinner(state.names[targetIndex], targetIndex);
    state.spinning = false;
  }, duration * 1000 + 100);
}
```

**Why the jitter matters:** Without it, the pointer always stops at the exact center of a slice, which looks computer-generated. The ±35% random offset makes each spin look organic.

### 7.5 Winner Handling

- Show modal with name + gold spark effect (40 particles, staggered 25ms apart)
- Add entry to `state.history` (unshift = newest first)
- Render history list (show up to 20 most recent)
- If `autoRemove` is enabled: hide "Remove Winner" button and call remove function after 300ms
- "Remove Winner" button: splice name from textarea, rebuild state, close modal

### 7.6 Clock
- Update every 1000ms with `HH:MM:SS LOCAL` format

### 7.7 Rigged Dropdown Sync
- Every time `updateNames()` runs, rebuild the `<select>` options from current names
- Preserve current selection if the name still exists
- Update status indicator text/color based on enabled + target state

---

## 8. Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `Space` | Trigger spin (when textarea is not focused) |
| `Escape` | Close modal or settings panel |
| `Ctrl + Alt + .` | Toggle hidden settings panel |

---

## 9. Interaction Details (polish)

- **Textarea focus:** gold border + soft gold glow ring
- **Ghost buttons hover:** border + text turn gold, subtle gold background tint
- **Danger ghost button hover:** turns crimson instead
- **Green status dot:** pulses 1 → 0.5 opacity every 2s
- **Ring around wheel:** slowly rotates (60s full revolution) — gives a "live" feeling
- **Winner sparks:** gold dots fly from center area to random destinations, fading out
- **Modal corner brackets:** just decorative L-shapes, reinforce the luxury feel
- **No emojis in the UI** except the diamond bullet `◆` and arrow `✕` symbols
- **Initial sample names:** pre-fill textarea with "An, Bình, Chi, Dung, Em, Phúc, Giang, Hà" (one per line)

---

## 10. Responsive Behavior

- **< 960px:** Grid stacks to single column, wheel above panels
- **Wheel container:** `min(560px, 90vw)` always fits
- **Font sizes:** clamp() on title and winner name for smooth scaling
- **Settings panel:** caps at 95vw on small screens
- **Ornaments:** stay in place; may clip on very small screens (acceptable)

---

## 11. Anti-Detection Checklist (for the hidden feature)

When implementing the secret trigger, the following MUST be true:

- [ ] `cursor: default` on `.hidden-trigger` (not pointer, not help, not any custom cursor)
- [ ] No `title` attribute, no tooltip
- [ ] No hover visual change — same appearance before and after mouse-over
- [ ] No console.log that reveals the feature
- [ ] The trigger element has no descriptive id or class name that gives it away (use "hidden-trigger" only in code, looks like generic decoration)
- [ ] Triple-click required (single accidental clicks do nothing)
- [ ] 1.5s timeout resets click count (prevents accumulating clicks over time)
- [ ] Settings panel fully hidden off-screen (`translateX(100%)`) when inactive — not just `display: none`, so no flash if toggled
- [ ] Spin duration should default to "Random" in production for natural feel when rigged
- [ ] Wheel rotation uses jitter so landing position looks organic, not mechanical

---

## 12. File Structure

Single file: `wheel-of-names.html`
- `<head>`: meta, Google Fonts preconnect + link, all `<style>` inline
- `<body>`: full markup + all `<script>` inline at end
- No external dependencies beyond Google Fonts

---

## 13. Sample Names for Testing

Pre-populate the textarea on load with:
```
An
Bình
Chi
Dung
Em
Phúc
Giang
Hà
```

---

## 14. Acceptance Criteria

- [ ] Wheel renders correctly for 0, 1, 2, 3, 10, 20+ names
- [ ] Spin button is disabled when spinning or when < 2 names
- [ ] Space key triggers spin (except when typing in textarea)
- [ ] Winner modal appears with correct name after spin animation completes
- [ ] Gold spark particles animate on win
- [ ] History log records every draw with timestamp
- [ ] "Remove Winner" button removes the name from textarea and re-renders wheel
- [ ] Hidden trigger at bottom-right does NOT show pointer cursor on hover
- [ ] Triple-clicking the hidden dot within 1.5s opens the settings panel
- [ ] `Ctrl+Alt+.` also opens settings
- [ ] When "Predetermined winner" is enabled with a target selected, every spin lands on that name
- [ ] Jitter ensures the pointer doesn't always land dead-center on the target slice
- [ ] "Auto-remove Winner" removes winner automatically without user clicking the button
- [ ] "Random" spin duration produces different spin times each time
- [ ] Clock updates in real-time
- [ ] Responsive layout works on mobile (< 960px) and desktop
- [ ] All animations feel smooth and intentional, not janky

---

## 15. Design Intent Reminder

> This is NOT a colorful, playful, cartoon wheel-of-fortune app. It is a **refined, dark, editorial product** — think Rolex catalog meets vintage European casino meets luxury magazine. Every detail (corner brackets, serif italic, gold accents, tick marks, grain texture) contributes to an atmosphere of elegance and mystery. The hidden feature is meant to feel like a secret compartment in a finely-crafted object — discoverable only by those who know.

---

**END OF SPECIFICATION**
