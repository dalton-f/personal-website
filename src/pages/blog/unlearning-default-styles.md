---
layout: ../../layouts/MarkdownPostLayout.astro
title: "Unlearning Default Styles"
publicationDate: "2026-04-03"
description: "Browsers come with opinions. They aren't always great."
---

Browsers come with opinions.

Every browser comes with its own set of default styles, adding margins, padding, and font sizes that you didn’t ask for. Most of the time, you don’t even notice them until something starts to feel slightly off and you can’t explain why. These built-in styles might seem helpful at first, but they often lead to inconsistent layouts and frustrating bugs. That’s where CSS resets come in.

A CSS reset removes these default styles, giving you a clean, predictable starting point so you can design your layout exactly the way you want, without fighting the browser.

It's not often that I use a personalised reset for my projects, but a recent Kevin Powell video on some newer CSS properties got me thinking about my own reset - what I include, what I leave out, and why.

## What Tailwind gets right with Preflight

Most of my projects these days use Tailwind, which means I’m usually working with Preflight by default. Preflight is Tailwind's own opinionated set of base styles that automatically injected into the base layer of any project.

Preflight, as the documentation states, is built on top of modern-normalize and represents a set of base styles "that are designed to smooth over cross-browser inconsistencies and make it easier for you to work within the constraints of your design system".

In summary, Preflight does what most good resets do, it removes all of the default margins from all elements, resets border styles, unstyles headings/lists, and constrains images to the block level. The only potentially confusing thing about this reset is the unstyling of all heading tags - but to me it's always made sense. In UI development, headings should often be visually de-emphasized. Making headings unstyled by default means any styling you apply to headings happens consciously and deliberately.

I find that Preflight strikes a good balance, fixing the small inconsistencies that crop up in browsers like Firefox, but it’s not quite the full picture for me.

## Filling in the gaps

When working on non-Tailwind projects, it’s important to understand what each part of a reset is doing, and to be able to build a personalised one that fits the project. Preflight does a lot of the heavy lifting (like most resets you can find online), but over time I’ve built up a small set of additions that I tend to reach for. They’re less about resetting everything, and more about smoothing out the edges.

For a long time, I used Eric Meyer's famous CSS Reset. It's a solid chunk of CSS, but it hasn't been updated in more than a decade, and a lot has changed since then! These days, if I'm working on a small, neatly scoped project, I'll use Josh Comeau's version of a modern CSS reset, which has a lot of small, important details that I find other resets miss out on. Rather than stripping everything to zero (which isn't as necessary these days, browsers don't have massive discrepancies anymore), it focuses on sensible defaults and little snippets I've picked up along the way.

## The CSS Reset

```css
/* 1. Use a more-intuitive box-sizing model */
/* 2. Remove default margins and padding */
/* 3. Reset all borders */
*,
::after,
::before,
::backdrop,
::file-selector-button {
  box-sizing: border-box; /* 1 */
  margin: 0; /* 2 */
  padding: 0; /* 2 */
  border: 0 solid; /* 3 */
}

/* 1. Set the line-height to be consistent and more accessible across all browsers */
/* 2. Preserve space on the left side of a page that doesn't have a scrollbar, to maintain consistency between component positions */
/* 3. Improve text rendering consistency */
html {
  line-height: 1.5; /* 1 */
  scrollbar-gutter: stable; /* 2 */
  -webkit-font-smoothing: antialiased; /* 3 */
  -moz-osx-font-smoothing: grayscale; /* 3 */
}

/* 
Use the logical variant of min-height to set a constraint to page height 
(this is an easy way to force the footer to the bottom of a page regardless of content amount,
the grid properties could be excluded)
*/
body {
  display: grid;
  grid-template-rows: auto 1fr auto;
  min-block-size: 100svb;
}

/* 1. Remove the default font size and weight for headings */
/* 2. Avoid text overflows */
/* 3. Improve line wrapping */
h1,
h2,
h3,
h4,
h5,
h6 {
  font-size: inherit; /* 1 */
  font-weight: inherit; /* 1 */
  overflow-wrap: break-word; /* 2 */
  text-wrap: balance; /* 3 */
}

/* Improve media defaults to avoid unexpected alignment issues */
img,
svg,
video,
canvas,
audio,
iframe,
embed,
object {
  display: block;
  vertical-align: middle;
}

/* Contrain media elements to the parent width and preserve their instrinsic aspect ratio */
img,
video {
  max-width: 100%;
  height: auto;
}

/* Inherit font styles in all browsers. */
button,
input,
select,
optgroup,
textarea,
::file-selector-button {
  font: inherit;
}

/* Enforces that elements with a "hidden" attribute stay invisible on all browsers */
[hidden]:where(:not([hidden="until-found"])) {
  display: none !important;
}

/* 1. Enable keyword animations (can be helpful when applying animations to details summary elements) */
/* 2. Make scrolling smoother by using an easing function */
@media (prefers-reduced-motion: no-preference) {
  html {
    interpolate-size: allow-keywords; /* 1 */
    scroll-behavior: smooth; /* 2 */
  }
}
```

## Final Thoughts

CSS resets have quietly shifted from being a “wipe everything clean” tool to something more nuanced and intentional. Being able to shape a predictable baseline that still respects how the browser wants to behave is invaluable.

It’s also worth noting that no CSS reset is ever truly finished. CSS continues to evolve, introducing new logical properties and improved responsive design capabilities every day. Staying aware of these changes means that you can continue to build on your own reset to keep a project consistent.

Unlearning the defaults that the browser stylesheets provide us isn't a one time thing, but part of an ongoing practice that keeps your code consistent. CSS resets don’t have to sit silently in the background; understanding how they work can help you write code more deliberately, with accessibility and better semantics in mind.

## Acknowledgements + Further Reading

Kevin Powell's recent video about unique CSS reset properties: https://youtu.be/qI5rXLJnxco?si=M-V6fST8wkAaCsz6

Eric Meyer's CSS reset: https://meyerweb.com/eric/tools/css/reset/

Josh Comeau's blog post about modern CSS resets: https://www.joshwcomeau.com/css/custom-css-reset/

Tailwind's Preflight documentation: https://tailwindcss.com/docs/preflight
