# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

BurgerBlast is a fictional fast-food chain website. The entire site is a single self-contained file: [index.html](index.html) (~1,400 lines, ~60KB) with all HTML, CSS, and JavaScript inline.

## Development

No build step, no dependencies, no package manager. Open [index.html](index.html) directly in a browser or serve it with any static file server:

```
npx serve .
```

## Architecture

Everything lives in [index.html](index.html), structured in this order:

1. **CSS** (`<style>` block) — CSS custom properties for brand colors, then sections in document order: reset/vars → nav → hero → menu tabs → meal cards → deals → locations → order form → cart sidebar → footer → responsive breakpoints.
2. **HTML** — Sections: `#navbar`, `#hero`, `#menu` (tabbed: Burgers/Chicken/Sides/Drinks/Desserts), Signature/Value/Kids meal cards, `#deals`, `#locations`, `#order` (contact/order form), `#footer`, and `#cart-sidebar`.
3. **JavaScript** (`<script>` block at end of body) — Cart logic (add/remove/update quantity, localStorage persistence), tab switching, mobile menu toggle, scroll-spy on navbar, and form submission via `fetch` to formsubmit.co/ajax.

## Brand Tokens

| Variable | Hex | Usage |
|---|---|---|
| `--red` | `#E8161B` | Primary CTA buttons, accents |
| `--yellow` | `#FFC72C` | Logo, highlights, nav border |
| `--dark` | `#1A1A1A` | Page background |
| `--orange` | `#FF6B1A` | Hover states |

## Form Submission

The order/contact form POSTs JSON to `formsubmit.co/ajax/verwertung777@gmail.com` via `fetch`. No server-side code exists; submissions go directly to that email address via FormSubmit's service.
