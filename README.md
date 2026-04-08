# Animate-CSS Skill for AI Agents

<img width="1400" height="600" alt="animate-css-banner" src="https://github.com/user-attachments/assets/4b86817b-e899-41d5-a84a-68be9a35b784" />

An AI agent skill that adds [Animate.css](https://github.com/animate-css/animate.css) (v4.1.1) animations to HTML, React/JSX, and WordPress PHP templates — with scroll-trigger support, RTL awareness, and `prefers-reduced-motion` respect baked in. Works with Claude Code, Cursor, Windsurf, GitHub Copilot, and any AI coding agent that supports custom instructions.

## What it does

When installed, your AI agent automatically:

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
├── SKILL.md              # Skill instructions (your AI agent reads this)
└── assets/
    └── animate.min.css   # Bundled Animate.css v4.1.1 (offline, no CDN needed)
```

---

## Installation

First, clone the repo:

```bash
git clone https://github.com/msrbuilds/animate-css-skill
```

Then follow the instructions for your editor or AI agent below.

---

### Claude Code (CLI, VS Code, JetBrains, Desktop, Web)

**Personal install** (available in all your projects):

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

Once installed, type `/animate-css` in Claude Code or just ask naturally — the skill activates automatically based on context.

---

### Cursor

Copy the skill into a Cursor rule file:

```bash
mkdir -p .cursor/rules
cp animate-css-skill/SKILL.md .cursor/rules/animate-css.md
```

Cursor auto-injects rules into every AI request in that project — no slash command needed.

---

### Windsurf / Codeium

Copy the skill into the Windsurf rules directory:

```bash
mkdir -p .windsurf/rules
cp animate-css-skill/SKILL.md .windsurf/rules/animate-css.md
```

Like Cursor, rules are always-on and applied automatically per project.

---

### GitHub Copilot

Append the skill to Copilot's custom instructions file:

```bash
mkdir -p .github
cat animate-css-skill/SKILL.md >> .github/copilot-instructions.md
```

Commit and push — Copilot picks it up automatically for the repo. If you already have instructions in that file, merge manually to avoid duplicates.

---

### Zed

Copy the skill into Zed's assistant rules directory:

```bash
# macOS / Linux
mkdir -p ~/.config/zed/rules
cp animate-css-skill/SKILL.md ~/.config/zed/rules/animate-css.md
```

Or for project-specific use, add a `.zed/rules/` directory in your project root:

```bash
mkdir -p .zed/rules
cp animate-css-skill/SKILL.md .zed/rules/animate-css.md
```

---

### Aider

Pass the skill as a read-only context file when starting Aider:

```bash
aider --read animate-css-skill/SKILL.md
```

Or add it to your `.aider.conf.yml` for persistent use:

```yaml
read:
  - animate-css-skill/SKILL.md
```

---

### Continue.dev (VS Code / JetBrains)

Add the skill as a context file in your `.continue/config.yaml`:

```yaml
systemMessage: |
  {{file:animate-css-skill/SKILL.md}}
```

Or use the `@file` context provider in chat to reference `SKILL.md` directly.

---

### Other AI Agents

For any agent that supports a custom system prompt, rules file, or context file, point it at `animate-css-skill/SKILL.md`. The skill is plain Markdown with YAML frontmatter — most systems can consume it directly.

> **Note:** The bundled `assets/animate.min.css` is referenced by the skill instructions. Make sure the asset file is accessible in your project so the AI agent can read and extract from it.

---

## Usage

Just describe what you want — your AI agent handles the rest.

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

The full library is included in `assets/animate.min.css`. Your AI agent extracts only the `@keyframes` and class blocks actually used in your project, keeping your CSS lean.

---

## License

MIT — see [LICENSE](LICENSE).

Animate.css is licensed separately under the [Hippocratic License](https://github.com/animate-css/animate.css/blob/main/LICENSE).
