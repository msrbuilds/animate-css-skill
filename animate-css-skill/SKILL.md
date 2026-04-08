---
name: animate-css
description: >
  Add CSS animations to HTML elements, React/JSX components, or WordPress PHP templates using
  Animate.css (v4.1.1). Use this skill whenever the user wants to animate UI elements, add
  entrance/exit effects, attention-seeker animations, or any CSS-based motion to their markup.
  Trigger on phrases like "add animation", "animate this", "make it bounce/fade/slide/zoom",
  "add transitions", "CSS animations", or any request to bring elements to life visually.
  Works with raw HTML snippets, full HTML files, React JSX, and WordPress PHP templates.
  Claude picks the most contextually appropriate animation — no guesswork needed from the user.
---

# Animate.css Skill

Adds Animate.css (v4.1.1) animations to HTML, React, or WordPress PHP code. Claude selects animations based on element type and context, then delivers the modified code plus only the required CSS extracted from the bundled library.

**Bundled asset:** `assets/animate.min.css` — the full Animate.css v4.1.1 library. Read this file when you need to extract specific `@keyframes` blocks and class definitions to copy into the user's CSS file. Do NOT ask the user to link a CDN unless they explicitly prefer it.

---

## Animation Selection Guide

Choose animations based on element type and purpose. Prefer subtle, purposeful choices over flashy ones.

| Element type | Recommended animations |
|---|---|
| Hero headings / H1 | `fadeInDown`, `backInDown`, `slideInDown` |
| Body headings H2–H4 | `fadeInLeft`, `fadeInUp`, `slideInLeft` |
| Paragraphs / body text | `fadeIn`, `fadeInUp` |
| Buttons / CTAs | `pulse` (hover), `bounceIn` (entrance), `tada` (attention) |
| Cards / panels | `zoomIn`, `fadeInUp`, `backInUp` |
| Navigation / menus | `slideInDown`, `fadeInDown` |
| Modal / dialog entrance | `zoomIn`, `bounceIn`, `flipInX` |
| Modal / dialog exit | `zoomOut`, `bounceOut`, `flipOutX` |
| Alert / notification | `slideInRight`, `bounceInRight`, `shakeX` (error) |
| Images / media | `zoomIn`, `fadeIn`, `rollIn` |
| List items | `fadeInLeft` with staggered `animate__delay-*` |
| Icons / badges | `heartBeat`, `tada`, `rubberBand` |
| Form success state | `bounceIn`, `jackInTheBox` |
| Form error state | `shakeX`, `headShake` |
| Page/section entrance | `fadeIn`, `backInUp` |
| Sidebar | `slideInLeft`, `fadeInLeft` |
| Footer | `fadeInUp` |
| Loading / spinner | `pulse animate__infinite`, `flash animate__infinite` |
| Tooltips / popovers | `fadeIn animate__faster` |
| Dropdown menus | `fadeInDown animate__faster` |

### Speed modifiers
- Default: 1s
- `animate__faster` (500ms) — tooltips, dropdowns, small UI elements
- `animate__fast` (800ms) — cards, modals
- `animate__slow` (2s) — hero sections, dramatic entrances
- `animate__slower` (3s) — ambient / background elements

### Delay for staggered lists
Use `animate__delay-1s` through `animate__delay-5s` on list children to stagger them naturally.

### Scroll-trigger threshold tuning
The `threshold` value controls how much of an element must be visible before it animates. Choose based on element size:

| Element size / type | Threshold | Rationale |
|---|---|---|
| Small — badges, icons, inline labels | `0.5` | Must be mostly visible to feel intentional |
| Medium — cards, stat blocks, buttons | `0.15` | Default; fires as soon as the element peeks in |
| Large — tall sections, feature rows | `0.08` | Fires early so animation completes before user scrolls past |
| Full-height panels / hero-style sections | `0.05` | Fires almost immediately on entry |

Pass `data-threshold` on the element (HTML/WP) or pass a value to `useScrollAnimate(threshold)` (React) to override per element.

### RTL support
For RTL layouts (Arabic, Urdu, Hebrew, etc.), directional animations must be flipped. Apply these substitutions automatically when the document or a parent container has `dir="rtl"`:

| LTR animation | RTL equivalent |
|---|---|
| `fadeInLeft` | `fadeInRight` |
| `fadeInRight` | `fadeInLeft` |
| `slideInLeft` | `slideInRight` |
| `slideInRight` | `slideInLeft` |
| `backInLeft` | `backInRight` |
| `backInRight` | `backInLeft` |
| `bounceInLeft` | `bounceInRight` |
| `bounceInRight` | `bounceInLeft` |
| `lightSpeedInLeft` | `lightSpeedInRight` |
| `lightSpeedInRight` | `lightSpeedInLeft` |

Vertical animations (`fadeInUp`, `fadeInDown`, `zoomIn`, etc.) and attention seekers (`pulse`, `bounce`, `shakeX`, etc.) are direction-neutral — no substitution needed.

**Detection:** Check for `dir="rtl"` on `<html>`, `<body>`, or any ancestor. In WordPress use `is_rtl()`. In React check `document.documentElement.dir === 'rtl'` or pass an `isRtl` prop. Always detect at runtime — never hardcode RTL assumptions.

---

## Output Format

### Step 1 — Annotate and modify the user's markup

**Scroll-trigger vs page-load:** Use scroll-triggered animations for any element that may appear below the fold (cards, stats, sections, lists). Use page-load animations only for above-the-fold elements like hero headings and nav. When in doubt, default to scroll-triggered — it's always the safer choice.

---

#### HTML (vanilla)

For above-the-fold elements, add classes directly:
```html
<h1 class="animate__animated animate__fadeInDown">Welcome</h1>
```

For scroll-triggered elements, start hidden and add a JS observer. Add `data-animate="fadeInUp"` as the trigger attribute, then include this script once per page:

```html
<!-- Mark elements for scroll animation -->
<div class="home-hero__stat scroll-animate" data-animate="fadeInUp" data-delay="0">...</div>
<div class="home-hero__stat scroll-animate" data-animate="fadeInUp" data-delay="150">...</div>
<div class="home-hero__stat scroll-animate" data-animate="fadeInUp" data-delay="300">...</div>

<script>
(function () {
  var reducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
  var isRtl = document.documentElement.dir === 'rtl' || document.body.dir === 'rtl';
  var rtlFlip = {
    fadeInLeft: 'fadeInRight', fadeInRight: 'fadeInLeft',
    slideInLeft: 'slideInRight', slideInRight: 'slideInLeft',
    backInLeft: 'backInRight', backInRight: 'backInLeft',
    bounceInLeft: 'bounceInRight', bounceInRight: 'bounceInLeft',
    lightSpeedInLeft: 'lightSpeedInRight', lightSpeedInRight: 'lightSpeedInLeft'
  };

  document.querySelectorAll('.scroll-animate').forEach(function (el) {
    var threshold = parseFloat(el.dataset.threshold || 0.15);
    var observer = new IntersectionObserver(function (entries) {
      entries.forEach(function (entry) {
        if (!entry.isIntersecting) return;
        var anim = el.dataset.animate;
        if (isRtl && rtlFlip[anim]) anim = rtlFlip[anim];
        if (reducedMotion) {
          el.style.opacity = 1;
        } else {
          setTimeout(function () {
            el.classList.add('animate__animated', 'animate__' + anim, 'animate__fast');
          }, parseInt(el.dataset.delay || 0));
        }
        observer.unobserve(el);
      });
    }, { threshold: threshold });
    observer.observe(el);
  });
})();
</script>
```

Add this CSS to keep elements invisible until the observer fires:
```css
.scroll-animate { opacity: 0; }
.scroll-animate.animate__animated { opacity: 1; }
```

---

#### React / JSX

For above-the-fold elements, apply classes directly with `className`:
```jsx
<h1 className="animate__animated animate__fadeInDown">Welcome</h1>
```

For scroll-triggered elements, use a reusable `useScrollAnimate` hook:

```jsx
// hooks/useScrollAnimate.js
import { useEffect, useRef, useState } from 'react';

export function useScrollAnimate(threshold = 0.15) {
  const ref = useRef(null);
  const [visible, setVisible] = useState(false);
  const reducedMotion = typeof window !== 'undefined'
    && window.matchMedia('(prefers-reduced-motion: reduce)').matches;

  useEffect(() => {
    if (reducedMotion) { setVisible(true); return; }
    const observer = new IntersectionObserver(
      ([entry]) => { if (entry.isIntersecting) { setVisible(true); observer.disconnect(); } },
      { threshold }
    );
    if (ref.current) observer.observe(ref.current);
    return () => observer.disconnect();
  }, [threshold, reducedMotion]);

  return [ref, visible];
}
```

Usage in a component — RTL-aware, stagger via inline `animationDelay`:
```jsx
import { useScrollAnimate } from './hooks/useScrollAnimate';

function HeroStats() {
  const [ref, visible] = useScrollAnimate(0.15);
  const isRtl = document.documentElement.dir === 'rtl';
  const enterAnim = isRtl ? 'animate__fadeInRight' : 'animate__fadeInLeft';
  const items = ['NEXT.JS', 'REACT 19', '< 10 MIN', '95/100'];

  return (
    <div ref={ref} className="home-hero__meta">
      {items.map((item, i) => (
        <div
          key={item}
          className={`home-hero__stat animate__animated animate__fast ${visible ? enterAnim : ''}`}
          style={{ opacity: visible ? 1 : 0, animationDelay: `${i * 150}ms` }}
        >
          {item}
        </div>
      ))}
    </div>
  );
}
```

---

#### WordPress PHP

For above-the-fold elements, add classes directly in the template:
```php
<h1 class="animate__animated animate__fadeInDown">
  <?php esc_html_e( 'Welcome', 'your-theme' ); ?>
</h1>
```

For scroll-triggered elements, use `data-animate` attributes and enqueue the observer script via `functions.php`:

**In the PHP template:**
```php
<div class="home-hero__meta">
  <?php
  $stats = [
    ['value' => 'NEXT.JS',   'label' => 'App Router & ISR'],
    ['value' => 'REACT 19',  'label' => 'Server Components'],
    ['value' => '< 10 MIN',  'label' => 'Setup to Deploy'],
    ['value' => '95/100',    'label' => 'Google Speed Scores'],
  ];
  foreach ( $stats as $i => $stat ) : ?>
    <div
      class="home-hero__stat scroll-animate"
      data-animate="fadeInUp"
      data-delay="<?php echo esc_attr( $i * 150 ); ?>"
    >
      <span class="home-hero__stat-value"><?php echo esc_html( $stat['value'] ); ?></span>
      <span class="home-hero__stat-label"><?php echo esc_html( $stat['label'] ); ?></span>
    </div>
  <?php endforeach; ?>
</div>
```

**In `functions.php`:**
```php
function theme_enqueue_animations() {
  wp_enqueue_style(
    'animate-css',
    get_template_directory_uri() . '/assets/css/animations.css',
    [],
    '4.1.1'
  );
  wp_enqueue_script(
    'scroll-animate',
    get_template_directory_uri() . '/assets/js/scroll-animate.js',
    [],
    '1.0.0',
    true
  );
}
add_action( 'wp_enqueue_scripts', 'theme_enqueue_animations' );

// Or for small extractions, inline the CSS to avoid an extra request:
function theme_inline_animation_css() {
  $css = '.scroll-animate{opacity:0}.scroll-animate.animate__animated{opacity:1}';
  wp_add_inline_style( 'animate-css', $css );
}
add_action( 'wp_enqueue_scripts', 'theme_inline_animation_css' );
```

**`assets/js/scroll-animate.js`** (enqueue this file):
```js
(function () {
  var reducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
  var isRtl = document.documentElement.dir === 'rtl' || document.body.dir === 'rtl';
  var rtlFlip = {
    fadeInLeft: 'fadeInRight', fadeInRight: 'fadeInLeft',
    slideInLeft: 'slideInRight', slideInRight: 'slideInLeft',
    backInLeft: 'backInRight', backInRight: 'backInLeft',
    bounceInLeft: 'bounceInRight', bounceInRight: 'bounceInLeft',
    lightSpeedInLeft: 'lightSpeedInRight', lightSpeedInRight: 'lightSpeedInLeft'
  };

  document.querySelectorAll('.scroll-animate').forEach(function (el) {
    var threshold = parseFloat(el.dataset.threshold || 0.15);
    var observer = new IntersectionObserver(function (entries) {
      entries.forEach(function (entry) {
        if (!entry.isIntersecting) return;
        var anim = el.dataset.animate;
        if (isRtl && rtlFlip[anim]) anim = rtlFlip[anim];
        if (reducedMotion) {
          el.style.opacity = 1;
        } else {
          setTimeout(function () {
            el.classList.add('animate__animated', 'animate__' + anim, 'animate__fast');
          }, parseInt(el.dataset.delay || 0));
        }
        observer.unobserve(el);
      });
    }, { threshold: threshold });
    observer.observe(el);
  });
})();
```

In WordPress, detect RTL server-side and pass it to the script:
```php
function theme_enqueue_animations() {
  wp_enqueue_style( 'animate-css', get_template_directory_uri() . '/assets/css/animations.css', [], '4.1.1' );
  wp_enqueue_script( 'scroll-animate', get_template_directory_uri() . '/assets/js/scroll-animate.js', [], '1.0.0', true );
}
add_action( 'wp_enqueue_scripts', 'theme_enqueue_animations' );
```

Note: The script auto-detects `dir="rtl"` from `<html>` — WordPress sets this automatically via `language_attributes()` in the theme header, so no extra PHP needed.

### Step 2 — Extract and deliver only the needed CSS

Read `assets/animate.min.css`. Extract:
1. The `:root` CSS variables block (always include — needed for `--animate-duration`, `--animate-delay`, `--animate-repeat`)
2. The `.animate__animated` base class block (always include)
3. The `@media (prefers-reduced-motion)` block (always include — accessibility)
4. Only the `@-webkit-keyframes` + `@keyframes` + `.animate__*` class blocks for the animations actually used

Deliver this as a code block the user can paste into their CSS file. Label it clearly:

```css
/* === Animate.css (extracted) — paste into your project CSS === */

:root { --animate-duration: 1s; --animate-delay: 1s; --animate-repeat: 1; }

.animate__animated { /* ... */ }

@media (prefers-reduced-motion: reduce) { /* ... */ }

/* Animations used: fadeInDown, fadeInUp, bounceIn */
@keyframes fadeInDown { /* ... */ }
.animate__fadeInDown { /* ... */ }
/* etc. */
```

If the user has a Tailwind or CSS Modules setup, note they can also `npm install animate.css` and `import 'animate.css'` instead.

### Step 3 — Setup instructions

Always include a brief note covering:
- Where to paste the CSS (or how to import via npm)
- For **HTML**: add the CDN link OR paste the extracted CSS into their stylesheet
- For **React**: `npm install animate.css` then `import 'animate.css'` in `main.jsx` / `index.js`, or paste the extracted CSS into a global stylesheet
- For **WordPress**: paste extracted CSS into the theme's `style.css` or enqueue a separate `animations.css` file via `functions.php`
- The `prefers-reduced-motion` media query is already included in the CSS — no extra work needed for accessibility

---

## Best Practices (apply automatically)

- **Never animate `<html>` or `<body>`** — wrap in a `<main>` or container div instead
- **Avoid `animate__infinite`** unless it's clearly a loader/spinner context
- **Use `overflow: hidden`** on the parent of sliding/bouncing elements to prevent horizontal scrollbars
- **Inline elements can't be animated** — check if the target element is `display: inline` and note if the user needs to make it `inline-block` or `block`
- **Don't pile on animations** — one entrance animation per element; use attention seekers sparingly
- **Stagger list items** with delay classes; don't apply the same delay to everything
- **Always flip directional animations for RTL** — check the RTL substitution table above before applying any left/right animation
- **Respect reduced motion in JS, not just CSS** — the bundled CSS handles `prefers-reduced-motion` for class-based animations, but the scroll observer script must also check it and skip animations (just show the element immediately) when the user prefers reduced motion

---

## Quick Reference — All Available Animations

**Attention seekers:** bounce, flash, pulse, rubberBand, shakeX, shakeY, headShake, swing, tada, wobble, jello, heartBeat

**Back entrances/exits:** backInDown, backInLeft, backInRight, backInUp / backOutDown, backOutLeft, backOutRight, backOutUp

**Bouncing entrances/exits:** bounceIn, bounceInDown, bounceInLeft, bounceInRight, bounceInUp / bounceOut, bounceOutDown, bounceOutLeft, bounceOutRight, bounceOutUp

**Fading entrances/exits:** fadeIn, fadeInDown, fadeInDownBig, fadeInLeft, fadeInLeftBig, fadeInRight, fadeInRightBig, fadeInUp, fadeInUpBig, fadeInTopLeft, fadeInTopRight, fadeInBottomLeft, fadeInBottomRight / (same pattern for fadeOut)

**Flippers:** flip, flipInX, flipInY, flipOutX, flipOutY

**Lightspeed:** lightSpeedInRight, lightSpeedInLeft, lightSpeedOutRight, lightSpeedOutLeft

**Rotating entrances/exits:** rotateIn, rotateInDownLeft, rotateInDownRight, rotateInUpLeft, rotateInUpRight / (same pattern for rotateOut)

**Specials:** hinge, jackInTheBox, rollIn, rollOut

**Zooming entrances/exits:** zoomIn, zoomInDown, zoomInLeft, zoomInRight, zoomInUp / zoomOut, zoomOutDown, zoomOutLeft, zoomOutRight, zoomOutUp

**Sliding entrances/exits:** slideInDown, slideInLeft, slideInRight, slideInUp / slideOutDown, slideOutLeft, slideOutRight, slideOutUp

**Utility classes:** animate__delay-1s through animate__delay-5s, animate__slow, animate__slower, animate__fast, animate__faster, animate__repeat-1/2/3, animate__infinite
