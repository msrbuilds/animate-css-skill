# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

This is a **Claude Code skill** (not a traditional app). It teaches Claude how to add Animate.css v4.1.1 animations to user code across three environments: raw HTML, React/JSX, and WordPress PHP.

The skill is defined entirely in two files:
- `animate-css-skill/SKILL.md` — the skill instructions (frontmatter + rules Claude follows)
- `animate-css-skill/assets/animate.min.css` — the bundled Animate.css v4.1.1 library (offline, no CDN)

There is no build system, no dependencies, and no tests. This repo has no `package.json`, `Makefile`, or CI pipeline.

## How the Skill Works

When installed, Claude reads `SKILL.md` on activation. The skill's job is to:

1. **Annotate the user's markup** with animation classes/attributes (format depends on environment)
2. **Extract only the needed CSS** (`@keyframes` + class blocks) from the bundled `animate.min.css` — never deliver the whole file
3. **Provide setup instructions** for the user's specific environment

Key behaviors baked into the skill:
- Scroll-triggered animations use Intersection Observer with `data-animate` attributes and per-element `data-threshold`
- RTL layouts get automatic directional animation flipping (e.g., `fadeInLeft` → `fadeInRight`)
- `prefers-reduced-motion` is respected in both CSS and JS (observer shows elements immediately instead of animating)

## Editing Guidelines

- `SKILL.md` frontmatter (`name`, `description`) controls when Claude activates this skill — keep the description trigger-phrase-rich
- The animation selection table, RTL flip map, and threshold guide in `SKILL.md` are the core decision logic — changes here directly affect what animations Claude picks
- The three output format sections (HTML, React, WordPress PHP) are templates Claude follows verbatim — keep them copy-paste ready and consistent with each other
- `animate.min.css` should only be replaced when upgrading Animate.css versions — update the version references in both `SKILL.md` and `README.md` if you do
