# SPEC.md — The Kitchen App
### Master build specification for Claude Code

---

## WHAT TO BUILD

A single-file HTML web application called **"The Kitchen"** — a cinematic, interactive video-style recipe guide. Multiple dishes are organized by food category in a left sidebar. Each recipe plays as a dark, auto-advancing fullscreen slideshow. Ingredient quantities scale dynamically based on number of people. Stock food photography from Unsplash appears on every slide.

**Single deliverable: one file named `index.html`** — all CSS and JavaScript inline. No frameworks. No audio of any kind.

---

## VISUAL DESIGN SYSTEM

### Fonts (load from Google Fonts)
- **Cormorant Garamond** — display headings, dish names, large titles
- **Outfit** — body text, UI labels, buttons
- **DM Mono** — monospace labels, amounts, codes, eyebrows

### Color Palette
```css
--ember:  #D94F00;
--gold:   #E8A020;
--jade:   #2D7A4F;
--deep:   #0C0804;
--panel:  rgba(255,255,255,0.04);
--border: rgba(255,255,255,0.08);
--bright: #FFF3E0;
--dim:    rgba(255,243,224,0.50);
--dimmer: rgba(255,243,224,0.28);
```

### Atmosphere Effects (all CSS, no JS libraries)
- **Film grain overlay** — fixed position, SVG fractalNoise, opacity ~0.032, 8-frame animation loop
- **Radial gradient backgrounds** — each slide has a unique dark ember/gold radial glow
- **Floating particles** — 20 small colored dots per slide, rise upward via CSS animation, random size/speed/drift
- **Heat shimmer** — subtle vertical breathing gradient at bottom of each slide
- **Custom cursor** — small ember-colored dot + larger lagging gold ring, both `position:fixed`
- **Ken Burns effect** — slide background images slowly scale from 100% to 107% over the slide duration

---

## LAYOUT

### Left Sidebar — 260px wide, fixed height, independent scroll
```
┌──────────────────────────────┐
│  🍽️  The Kitchen             │
│  Restaurant recipes,          │
│  step by step                │
├──────────────────────────────┤
│  ▼ 🛺 Indian Street Food     │  ← expanded category
│      • Pav Bhaji  ◀ active   │  ← active recipe (ember highlight)
│      • Vada Pav              │
│  ▶ 🍛 Indian Curries         │  ← collapsed category
│  ▶ 🍚 Rice Dishes            │
│  ▶ 🥣 Soups & Dals           │
│  ▶ 🍮 Desserts               │
├──────────────────────────────┤
│  SERVES                      │
│  [–]   4   [+]              │
│  Scaled for 4 people         │
└──────────────────────────────┘
```

**Sidebar behavior:**
- Category rows click to expand/collapse — show/hide recipe links underneath
- Active recipe: left border in `--ember`, text in `--ember`
- Inactive recipes: `--dim` text, hover to `--bright`
- Background: `#080502`, border-right: `var(--border)`
- Collapses to hamburger icon on screens narrower than 768px

### Main Area — remaining width
- When no recipe selected: **Welcome screen** (see below)
- When recipe selected: **Cinematic slideshow** (see below)

### Top Bar — above main area
- Left: current recipe name in Cormorant Garamond
- Right: **"☰ All Steps"** button — opens full-screen step menu overlay

---

## WELCOME SCREEN

Shown when the app first loads, before any recipe is selected.

- Large italic `"The Kitchen"` in Cormorant Garamond, ~8rem, ember gradient fill
- Subtitle: *"Select a recipe from the sidebar to begin"* in Outfit, dimmer color
- Row of large category tile buttons — clicking one expands that category in the sidebar
- Ambient particle animation running in background
- Film grain overlay active

---

## SLIDESHOW SYSTEM

Each recipe is a sequence of slides that auto-advance. Every slide has:

### Slide Structure
- **Background:** Unsplash image + dark overlay `rgba(0,0,0,0.62)` + Ken Burns scale animation
- **Particles:** 20 rising dots, re-spawned on each slide transition
- **Content:** Centered, max-width 920px, padding 2.5rem

### Slide Transition
- Exiting slide: opacity 1→0, scale 1→0.97 over 650ms
- Entering slide: opacity 0→1, scale 1.05→1 over 850ms, `cubic-bezier(.16,1,.3,1)`

### Slide Types (in this order for every recipe)

**1. Intro Slide**
- Eyebrow label: cuisine name in DM Mono, gold color
- Dish name: Cormorant Garamond, ~8–11rem, bright color, italic accent word
- Subtitle: description line
- Stats row: Time · Base Servings · Step count — each as a small badge

**2. Ingredients Slide(s)**
- Eyebrow: "What You Need"
- Heading: "The Ingredients" with italic accent
- Dark grid (2 columns) — each column is an ingredient group
- Each row: ingredient name (left) + scaled amount in gold pill (right)
- Tip box below if relevant

**3. Step Slides** (one per cooking step)
- Phase tag: small jade-colored DM Mono label (e.g. "Phase 1 — Masala")
- Step badge: circle with step number in ember
- Step count: "of N steps" next to badge
- Title: Cormorant Garamond display heading with italic accent word
- Body: Outfit, font-weight 300, 1.78 line-height — key values in `<strong>`
- Timer pill (optional): ember background, shows duration and heat level
- Tip box (optional): gold left border, chef insight

**4. Chef Tips Slide**
- 3-column grid of tip cards
- Each card: emoji icon, bold title, short paragraph
- Cards: dark panel background, border, border-radius 12px

**5. Serving Slide**
- 2×2 grid of serving cards
- Cards: best bread pairings, rice options, garnish ideas, storage tips

**6. End Slide**
- Giant "Enjoy" — Cormorant Garamond, ~13rem, gradient text (ember→gold→bright)
- Subtitle: "[Dish name] is ready"
- Credit line: "Recipe by Hebbar's Kitchen · Presented by The Kitchen App"

### Controls Bar (fixed, bottom center)
- Background: dark panel, border, border-radius 100px, backdrop-filter blur
- Buttons: `←` prev, `⏸/▶` play-pause, `→` next
- Dot row: one dot per slide, active dot widens and turns ember
- Slide label: current slide name in DM Mono

### Arc Timer (bottom right, fixed)
- SVG circle, 44×44px
- Outer track: faint white circle
- Inner fill: gold stroke, animates around the circle over the slide duration
- Center label: countdown in seconds

### Keyboard Shortcuts
- `→` or `Space` — next slide
- `←` — previous slide
- `P` — pause / resume
- `M` — open step menu overlay
- `Escape` — close overlay

### Click Zones
- Click right half of slide → next
- Click left half of slide → previous
- Clicking controls bar does not advance slide

### Step Menu Overlay
- Full screen, dark background, backdrop blur
- Lists all slides for current recipe with number + label
- Click any item to jump to that slide
- Current slide highlighted in ember

---

## SERVING SIZE SCALER

Located at the bottom of the left sidebar.

- Label: "SERVES" in DM Mono uppercase, dimmer color
- `[–]` button · number display · `[+]` button
- Default: 4 people. Range: 1 to 20.
- Below: "Scaled for X people" in dimmer text

**When serving count changes:**
All ingredient amounts across all slides of the active recipe update instantly.

**Scaling logic:**
```javascript
function scaleAmount(base, baseServings, currentServings) {
  return toFraction((base / baseServings) * currentServings);
}

function toFraction(n) {
  const whole = Math.floor(n);
  const rem = n - whole;
  let frac = '';
  if      (rem < 0.10) frac = '';
  else if (rem < 0.30) frac = '¼';
  else if (rem < 0.45) frac = '⅓';
  else if (rem < 0.60) frac = '½';
  else if (rem < 0.70) frac = '⅔';
  else if (rem < 0.90) frac = '¾';
  else                 frac = '1';
  if (whole === 0) return frac || '¼';
  return frac ? `${whole}${frac}` : `${whole}`;
}
```

Scaled amounts display in `--gold` color to visually indicate they have changed.

---

## FOOD CATEGORIES

```javascript
const categories = [
  { id: 'indian-street-food', label: 'Indian Street Food', icon: '🛺' },
  { id: 'indian-curries',     label: 'Indian Curries',     icon: '🍛' },
  { id: 'indian-breads',      label: 'Indian Breads',      icon: '🫓' },
  { id: 'rice-dishes',        label: 'Rice Dishes',        icon: '🍚' },
  { id: 'soups-and-dals',     label: 'Soups & Dals',       icon: '🥣' },
  { id: 'desserts',           label: 'Desserts',           icon: '🍮' },
];
```

---

## RECIPE DATA STRUCTURE

```javascript
{
  id: 'kadai-paneer',
  name: 'Kadai Paneer',
  category: 'indian-curries',
  cuisine: 'North Indian',
  time: '45 mins',
  baseServings: 4,
  heroImage: 'UNSPLASH_PHOTO_ID',

  ingredients: [
    {
      group: '🌶️ Kadai Masala',
      items: [
        { name: 'Coriander seeds', amount: 2,   unit: 'tbsp' },
        { name: 'Cumin seeds',     amount: 2,   unit: 'tsp'  },
        { name: 'Fennel seeds',    amount: 2,   unit: 'tsp'  },
        { name: 'Dried red chilli',amount: 4,   unit: ''     },
        { name: 'Black pepper',    amount: 2,   unit: 'tsp'  },
      ]
    },
    {
      group: '🧅 Sabzi',
      items: [
        { name: 'Paneer, cubed',   amount: 250, unit: 'g'    },
        { name: 'Onions',          amount: 2,   unit: ''     },
        { name: 'Tomatoes',        amount: 4,   unit: ''     },
        { name: 'Green capsicum',  amount: 1,   unit: ''     },
        { name: 'Garlic cloves',   amount: 5,   unit: ''     },
        { name: 'Ginger',          amount: 1,   unit: 'inch' },
      ]
    }
  ],

  steps: [
    {
      phase: 'Phase 1 — Kadai Masala',
      title: 'Dry Roast the Spices',
      body: 'In a dry pan over <strong>low flame</strong>, add the coriander seeds, cumin, fennel, dried chillies and black pepper. Stir continuously until aromatic.',
      timer: '3–4 minutes · Low flame',
      tip: 'The kitchen will fill with a toasty fragrance. The coriander will deepen in colour — that is your signal.',
      image: 'UNSPLASH_PHOTO_ID'
    }
  ],

  tips: [
    { icon: '🌶️', title: 'Fresh Masala Only', body: 'Grind your kadai masala fresh each time. Pre-ground spices lose their punch within hours.' },
  ],

  serving: {
    bread:   ['Garlic naan', 'Tandoori roti', 'Butter roti'],
    rice:    ['Jeera rice', 'Plain basmati'],
    garnish: ['Fresh coriander', 'Swirl of cream', 'Ginger juliennes'],
    storage: ['Refrigerate up to 2 days', 'Reheat with a splash of water', 'Do not freeze — paneer turns grainy']
  }
}
```

---

## SEED RECIPES — BUILD ALL 5 FULLY

Write complete, accurate recipe data for each. Do not leave any fields as placeholders.

| # | Recipe | Category | Steps | Base Servings |
|---|--------|----------|-------|---------------|
| 1 | Pav Bhaji | indian-street-food | 8 | 4 |
| 2 | Kadai Paneer | indian-curries | 10 | 4 |
| 3 | Paneer Butter Masala | indian-curries | 9 | 4 |
| 4 | Dal Makhani | soups-and-dals | 8 | 4 |
| 5 | Vegetable Biryani | rice-dishes | 10 | 4 |

For each recipe include:
- Full ingredient list with accurate gram/tsp/tbsp amounts
- 6–10 detailed step objects with body text, timers, and tips
- 5–6 chef tip cards
- Complete serving object (bread, rice, garnish, storage)
- Real Unsplash photo IDs for heroImage and each step image

---

## UNSPLASH IMAGE USAGE

Format:
```
https://images.unsplash.com/photo-{PHOTO_ID}?w=1400&q=80&fit=crop&auto=format
```

Use **real, verified Unsplash photo IDs** for food photography. Find IDs by searching unsplash.com for each dish/step and copying the ID from the URL. Do not invent IDs.

Suggested search terms per recipe:
- Pav Bhaji: "pav bhaji", "indian street food", "mumbai food"
- Kadai Paneer: "kadai paneer", "paneer curry", "indian curry"
- Paneer Butter Masala: "butter paneer", "orange curry", "indian gravy"
- Dal Makhani: "dal makhani", "black lentil curry", "indian dal"
- Vegetable Biryani: "vegetable biryani", "indian rice", "biryani"

Step image suggestions: "chopping onions", "sizzling pan", "spices mortar", "curry cooking", "plating food", "steam rising pan"

---

## SWITCHING RECIPES

When a user clicks a different recipe in the sidebar:
- Slideshow resets to slide 0 of the new recipe
- Serving count stays the same (re-scales new recipe to current count)
- Arc timer resets
- Step menu updates to new recipe's slides
- Active recipe highlight updates in sidebar

---

## NO AUDIO

Do not implement any of the following:
- Web Speech API
- Web Audio API  
- SpeechSynthesisUtterance
- AudioContext
- Any sound effects
- Any narration
- Any background music

---

## TECHNICAL RULES

- Single file: `index.html` only
- All CSS in a `<style>` tag in `<head>`
- All JavaScript in a `<script>` tag before `</body>`
- Google Fonts loaded via `<link>` in `<head>`
- Unsplash images referenced via `<img>` or CSS `background-image`
- No npm packages, no CDN JS libraries, no frameworks
- No localStorage, no cookies, no backend
- Must open directly in Chrome/Safari/Edge with no server
- Responsive: sidebar collapses on screens < 768px wide
