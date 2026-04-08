# animate-css-skill

A Claude skill that adds [Animate.css](https://github.com/animate-css/animate.css) (v4.1.1) animations to HTML, React/JSX, and WordPress PHP templates — with scroll-trigger support, RTL awareness, and `prefers-reduced-motion` respect baked in.

## What it does

When installed, Claude automatically:

- **Picks the right animation** based on element type and context — no guessing needed
- **Extracts only the CSS you need** from the bundled library instead of loading the full file
- **Applies scroll-triggered animations** via Intersection Observer for below-the-fold elements
- **Flips directional animations for RTL** layouts (Arabic, Urdu, Hebrew, etc.)
- **Respects `prefers-reduced-motion`** in both CSS and JavaScript
- **Tunes the observer threshold** based on element size (badges vs. full sections)

Works across all three environments:

| Environment | Output |
|---|---|
| Raw HTML | `data-animate` attributes + IIFE observer script |
| React / JSX | Reusable `useScrollAnimate` hook + `className` pattern |
| WordPress PHP | `data-*` attributes + enqueueable `scroll-animate.js` + `functions.php` snippet |

---

## Repo structure

```
animate-css-skill/
├── SKILL.md              # Skill instructions (Claude reads this)
└── assets/
    └── animate.min.css   # Bundled Animate.css v4.1.1 (offline, no CDN needed)
```

---

## Installation

### Claude Code (CLI, VS Code, JetBrains, Desktop, Web)

Clone the repo and copy the skill folder into your Claude skills directory:

```bash
git clone https://github.com/msrbuilds/animate-css-skill
```

**Personal install** (available in all projects):

```bash
# macOS / Linux
mkdir -p ~/.claude/skills/animate-css
cp -r animate-css-skill/* ~/.claude/skills/animate-css/

# Windows (PowerShell)
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills\animate-css"
Copy-Item -Recurse animate-css-skill\* "$env:USERPROFILE\.claude\skills\animate-css\"
```

**Project-specific install** (available only in that project):

```bash
mkdir -p .claude/skills/animate-css
cp -r animate-css-skill/* .claude/skills/animate-css/
```

Once installed, use it by typing `/animate-css` in Claude Code or just ask naturally — Claude activates the skill automatically based on context.

---

### Cursor

Cursor uses its own rules system. Copy the skill instructions into a Cursor rule file:

```bash
mkdir -p .cursor/rules
cp animate-css-skill/SKILL.md .cursor/rules/animate-css.md
```

The rule is auto-injected into every AI request in that project. No slash command — Cursor applies it automatically.

---

### Windsurf / Codeium

Windsurf uses a similar rules directory:

```bash
mkdir -p .windsurf/rules
cp animate-css-skill/SKILL.md .windsurf/rules/animate-css.md
```

Like Cursor, rules are always-on and applied automatically per project.

---

### GitHub Copilot

Copilot reads custom instructions from `.github/copilot-instructions.md`. Append the skill content to that file:

```bash
mkdir -p .github
cat animate-css-skill/SKILL.md >> .github/copilot-instructions.md
```

Commit and push — Copilot picks it up automatically for the repo. Note: Copilot's instruction file is a single flat file, so if you already have instructions there, merge manually.

---

### Other AI Editors

For any editor that supports a custom system prompt or rules file, paste the contents of `animate-css-skill/SKILL.md` into the appropriate location. The skill is plain Markdown with YAML frontmatter — most systems can consume it directly.

> **Note:** The bundled `assets/animate.min.css` file is referenced by the skill instructions. For non-Claude-Code editors, ensure the asset file is accessible in your project so the AI can read and extract from it.

---

## Usage examples

Just describe what you want — Claude handles the rest.

> "Add animations to this hero section"

> "Animate these cards as they scroll into view"

> "Make this RTL sidebar slide in from the right"

> "Add a scroll-triggered staggered animation to this product grid"

### Example output — WordPress PHP

```php
<div
  class="home-hero__stat scroll-animate"
  data-animate="fadeInUp"
  data-delay="0"
  data-threshold="0.15"
>
```

```js
// scroll-animate.js — auto-generated, ready to enqueue
(function () {
  var reducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
  var isRtl = document.documentElement.dir === 'rtl';
  // ...observer logic with per-element threshold and RTL flip
})();
```

### Example output — React

```jsx
// hooks/useScrollAnimate.js
export function useScrollAnimate(threshold = 0.15) {
  const ref = useRef(null);
  const [visible, setVisible] = useState(false);
  const reducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
  // ...
  return [ref, visible];
}

// Usage
const [ref, visible] = useScrollAnimate(0.08); // large section
<section ref={ref} className={`animate__animated ${visible ? 'animate__fadeInUp' : ''}`}>
```

---

## Threshold guide

| Element type | Threshold |
|---|---|
| Badges, icons, inline labels | `0.5` |
| Cards, stat blocks (default) | `0.15` |
| Tall feature rows, sections | `0.08` |
| Full-height panels | `0.05` |

---

## RTL support

Directional animations are automatically flipped when `dir="rtl"` is detected on `<html>` or `<body>`:

| LTR | RTL |
|---|---|
| `fadeInLeft` | `fadeInRight` |
| `slideInLeft` | `slideInRight` |
| `bounceInLeft` | `bounceInRight` |
| `lightSpeedInLeft` | `lightSpeedInRight` |

Vertical and attention-seeker animations (`fadeInUp`, `pulse`, `bounce`, etc.) are direction-neutral and need no substitution.

---

## Animate.css version

Bundled: **v4.1.1** — [animate-css/animate.css](https://github.com/animate-css/animate.css) — MIT licensed.

The full library is included in `assets/animate.min.css`. Claude extracts only the `@keyframes` and class blocks actually used in your project, keeping your CSS lean.

---

## License

MIT — see [LICENSE](LICENSE).

Animate.css is licensed separately under the [Hippocratic License](https://github.com/animate-css/animate.css/blob/main/LICENSE).
