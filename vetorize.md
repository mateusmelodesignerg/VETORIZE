# Rebuild command: Vetorize, a dark design-studio landing page with a yellow accent and 13 scroll-driven sections

Build ONE long, dark landing page for a design studio called **Vetorize**. It opens with a yellow loading curtain that lifts away. Below it, a giant Anton wordmark sits over a black-and-white video of an eye opening, and the wordmark shrinks into the header as you scroll. The sections that follow are:

1. **Hero:** eye video, right-aligned headline, a character blur-in paragraph, a logo marquee and a primary CTA.
2. **About:** a sticky paragraph whose words light up from dark gray to white as you scroll.
3. **Recent work:** a sticky left panel (big index number, nav and title that roll between projects) beside three large images with parallax and clip reveals.
4. **Numbers:** a big title, a tick ruler and four counters that count up, over a fabric texture.
5. **Feature quote:** a quote that lights up word by word, with an image that scales in.
6. **Services:** four rows that expand on hover to show an image, a description and tags.
7. **Process:** a pinned section where the title and four step cards slide horizontally.
8. **Testimonials:** a heading whose characters light up on scroll, plus three cards.
9. **Pricing:** a Monthly/Annual toggle with a sliding pill and slot-machine price digits. Every price reads **$0000,00**.
10. **FAQ:** an accordion over a fabric texture.
11. **Journal:** three post cards with a "Read post" pill that follows the cursor.
12. **Contact:** a hand image drifting up behind a big title.
13. **Footer:** a yellow band whose wordmark letters rise as it is revealed.

Assets live on Cloudflare R2 (**Path A, hosted assets**). Use the exact URLs below. Do not download, rename or re-host them, and do not use placeholders.

---

## 0. Global shell

- Single file `index.html`, no build step, `<html lang="en">`, `<title>Vetorize – Design Studio</title>`, meta description: "Vetorize is an independent studio shaping brands, websites and digital products with focus, precision and heart."
- Libraries (exact versions, in this order, at the end of `<body>`):
  - `https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js`
  - `https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js`
  - `https://cdn.jsdelivr.net/npm/lenis@1.1.13/dist/lenis.min.js`
- Fonts are **self-hosted on R2** (no Google Fonts link). Preload both with `<link rel="preload" as="font" type="font/woff2" crossorigin>`:

```css
@font-face{font-family:"Anton";src:url("https://assets.layerweb.com.br/vetorize/anton-400-latin.woff2") format("woff2");font-weight:400;font-style:normal;font-display:swap}
@font-face{font-family:"Geist";src:url("https://assets.layerweb.com.br/vetorize/geist-var-latin.woff2") format("woff2");font-weight:100 900;font-style:normal;font-display:swap}
```

- Favicon: inline SVG data URI (see section 2).
- Reset: `*{box-sizing:border-box;margin:0;padding:0}`. `body{background:#070707;color:#FFFFFF;font-family:Geist;font-size:1rem;line-height:1.3;letter-spacing:-.02em;font-weight:500;-webkit-font-smoothing:antialiased;overflow-x:hidden}`. While the loader runs, `body.is-loading{overflow:hidden}`.
- Lenis: `new Lenis({lerp:.1, smoothWheel:true})`, `lenis.on('scroll', ScrollTrigger.update)`, `gsap.ticker.add(t=>lenis.raf(t*1000))`, `gsap.ticker.lagSmoothing(0)`. Call `lenis.stop()` until the intro finishes. In-page `#` links scroll with `lenis.scrollTo(target,{duration:1.4})`.

## 1. Design tokens (put these on `:root` first and use them everywhere)

```css
:root{
  --c-black:#070707; --c-white:#FFFFFF; --c-yellow:#FEF83B; --c-red:#FC3C02; --c-gray:#AEAEAE;
  --c-bg:var(--c-black); --c-surface:#141414; --c-surface-2:#1C1C1C; --c-line:#2A2A2A;
  --c-ink:var(--c-white); --c-muted:var(--c-gray); --c-dim:#4A4A4A; --c-ghost:#262626;
  --c-accent:var(--c-yellow); --c-hot:var(--c-red); --c-accent-deep:#1B1A0B; --c-on-accent:var(--c-black);
  --f-display:"Anton",Impact,"Arial Narrow",sans-serif;
  --f-sans:"Geist",ui-sans-serif,system-ui,-apple-system,"Segoe UI",sans-serif;
  --fs-display:8.75rem; --fs-stat:5rem; --fs-h1:3.5rem; --fs-h2:2rem; --fs-h3:1.5rem; --fs-body:1rem; --fs-label:.875rem; --fs-micro:.75rem;
  --ls-h1:-.06em; --ls-h2:-.05em; --ls-h3:-.05em; --ls-btn:-.04em; --ls-body:-.02em; --ls-label:-.04em;
  --gutter:1.5rem; --sec-pad:9.375rem; --r-card:.5rem; --r-lg:1rem; --r-pill:999px;
  --ease-out:cubic-bezier(0,0,0,1); --ease-soft:cubic-bezier(.22,1,.36,1); --ease-inout:cubic-bezier(.76,0,.24,1); --ease-hero:cubic-bezier(.12,.23,0,1);
  --d-fast:.3s; --d-base:.5s; --d-slow:.8s;
}
```

Accent usage rules:
- **Yellow** (`--c-accent`): stat suffixes (+ % M), index dots, pricing "+" bullets, all button labels, the loader curtain, the footer band, the FAQ plus/minus icons and links.
- **Red-orange** (`--c-hot`): quote marks, the rating star, "(Popular)", "-20%" and the menu square marker.
- Text roles: headings `--c-ink`, secondary copy `--c-muted`, unrevealed scroll text `--c-dim`.
- DO NOT use any other hue (no green status dot, no blue links).

Type primitives:

| Class | Font | Size / line-height | Weight | Letter-spacing | Extra |
|---|---|---|---|---|---|
| `.display` | Anton | 140 / 1 | 400 | 0 | uppercase, `--c-ink` |
| `.h1` | Geist | 56 / 1 | 600 | -0.06em | |
| `.h2` | Geist | 32 / 1 | 600 | -0.05em | |
| `.h3` | Geist | 24 / 1.05 | 500 | -0.05em | `--c-muted` |
| `.tag` | Geist | 16 / 1.3 | 500 | -0.02em | `--c-ink` |
| `.label` | Geist | 14 / 1.2 | 400 | -0.04em | uppercase, `--c-muted` |
| `.sup` | Geist | 16 / 1.3 | 500 | -0.02em | uppercase, `align-self:flex-start` (superscript next to display titles) |

## 1.5 Scaling canvas

- Desktop (≥1024 px): `html{font-size:min(calc(100vw / 90), 21.334px)}`, so **1rem = 16 px at 1440 px wide**. Every desktop measurement below is written in px at 1440 and must be authored as `px/16` rem. The page wrapper `.page{max-width:90rem;margin:0 auto}`.
- Tablet (768–1023): `html{font-size:16px}`, with the compact flow layout from section 17.
- Phone (<768): `html{font-size:clamp(14px, calc(100vw / 24.375), 17px)}`, also compact flow.

## 1.6 Absolute coordinate table (desktop, 1440×900 viewport)

Y values are document positions, measured with the page scrolled to the top.

| Element | x | y | w | h | z |
|---|---|---|---|---|---|
| Header wordmark (`.hdr .brand`, fixed) | 24 | 24 | ≈735 | ≈194 (font 224) | 50 |
| Status block | 952 | 26 | auto | 43 | 50 |
| Clock block | 1138 | 26 | auto | 43 | 50 |
| "Say hello" pill (right edge at 1370) | auto | 24 | auto | 43 | 50 |
| Menu button | 1370 | 24 | 46 | 43 | 50 |
| Hero section | 0 | 0 | 1440 | 100vh (min 720) | 1 |
| Hero headline (right-aligned) | right 24 | 150 | auto | 224 | 2 |
| Hero paragraph | 24 | bottom 128 | 428 | auto | 2 |
| Hero bottom row (trust + CTA) | 24 → 1416 | bottom 24 | 1392 | 58 | 2 |
| Hero CTA | 1086 | 818 | 330 | 58 | 2 |
| About section | 0 | 900 | 1440 | 1800 | 1 |
| Work section | 0 | 2700 | 1440 | ≈2860 | 1 |
| Work images | 529 | 3014 / 3814 / 4614 | 869 | 653 | 1 |
| Numbers section | 0 | ≈5560 | 1440 | 854 | 1 |
| Feature section | 0 | ≈6414 | 1440 | 921 | 1 |
| Services section | 0 | ≈7335 | 1440 | ≈1148 | 1 |
| Process section | 0 | ≈8483 | 1440 | 2593 | 1 |
| Testimonials | 0 | ≈11075 | 1440 | 968 | 1 |
| Pricing | 0 | ≈12043 | 1440 | ≈983 | 1 |
| FAQ | 0 | ≈13026 | 1440 | ≈849 | 1 |
| Journal | 0 | ≈13875 | 1440 | ≈992 | 1 |
| Contact | 0 | ≈14867 | 1440 | 700 | 1 |
| Footer main | 0 | ≈15567 | 1440 | 424 | 1 |
| Footer band | 0 | ≈15991 | 1440 | 352 | 1 |
| Menu drawer | 970 | 0 | 470 | 100vh | 80 |
| Loader | 0 | 0 | 100vw | 100vh | 100 |

The total document height at 1440×900 is ≈16 440 px.

## 2. Brand (wordmark only), PIXEL CONTRACT

Markup, used in three places (loader, header, footer band):

```html
<span class="brand"><span class="brand__word">VETORIZE</span></span>
```

```css
.brand{display:inline-flex;align-items:flex-start;font-family:var(--f-display);line-height:.78;color:var(--c-ink);white-space:nowrap}
.brand__word{display:block;font-size:1em;letter-spacing:0;padding-top:.02em}
```

- The brand is the Anton wordmark "VETORIZE" ONLY. There is no icon, symbol or registered mark next to it.
- Header link: `<a class="brand" href="#top" aria-label="Vetorize, back to top">`. The footer band copy uses `aria-label="Vetorize"`.
- Sizes: header 14rem (224 px), footer band 14rem, loader 3.25rem.
- Favicon: inline SVG data URI, a 24×24 `#FEF83B` square with a black "V" path (`M5 4h4l3 11 3-11h4l-5 16h-4z`, fill `#070707`).
- DO NOT add an icon, outline, gradient or second color. DO NOT replace the wordmark with an image.

## 3. Loader + intro timeline, PIXEL CONTRACT

- `.loader{position:fixed;inset:0;z-index:100;background:var(--c-accent);display:grid;place-items:center}`.
- Inside: `.loader__brand{font-size:3.25rem;overflow:hidden;display:flex;padding:.14em .08em .04em}`, holding a black `.brand`.
- Initial states (set with GSAP before the timeline):
  - header brand `{opacity:0, y:63}`
  - `[data-fade]` header items `{opacity:0, y:-12}`
  - hero headline words `{opacity:0, y:20, filter:blur(10px)}`
  - hero paragraph chars `{opacity:0, filter:blur(8px)}`
  - trust row `{opacity:0, y:20}`
  - CTA `{opacity:0, scale:.95}`
  - `.hero__media` `{opacity:0, scale:.9}`
  - loader brand `{yPercent:135}`
- Timeline (`gsap.timeline({delay:.15})`), each step relative to the previous one:
  1. Loader brand to `yPercent:0`, 0.7 s, `power4.out`.
  2. +0.35 s: loader brand to `yPercent:-135`, 0.55 s, `power3.in`.
  3. −0.15 s: `.loader` to `yPercent:-100`, 0.9 s, `power4.inOut`. At +0.3 s into it, remove `body.is-loading`.
  4. +0.2 s: header brand to `{opacity:1, y:0}`, 0.9 s, `power3.out`.
  5. +0.25 s: `.hero__media` to `{opacity:1, scale:1}`, 0.8 s, `power3.out`. On start, `video.play()` (the video plays ONCE and stops on the open eye; no `loop`).
  6. +0.1 s: `[data-fade]` items to `{opacity:1, y:0}`, 0.8 s, stagger 0.06, `power3.out`.
  7. +0.15 s: headline words to `{opacity:1, y:0, blur(0)}`, 1 s, stagger 0.07, `expo.out`.
  8. +0.2 s: paragraph chars to `{opacity:1, blur(0)}`, 0.6 s, stagger 0.012, `power2.out`.
  9. +0.3 s: trust row up, 0.9 s, `expo.out`.
  10. +0.1 s: CTA to `{opacity:1, scale:1}`, 1.2 s, `expo.out`.
  11. Remove `.loader`, `lenis.start()`, `ScrollTrigger.refresh()`.
- DO NOT fade the loader; it slides up as one solid yellow panel.

## 4. Header, PIXEL CONTRACT

- `.hdr{position:fixed;inset:0 0 auto;z-index:50;height:0;pointer-events:none}`, with an inner `max-width:90rem` wrapper. Children use `pointer-events:auto`.
- Shade: `.hdr__shade{position:fixed;top:0;height:7.5rem;background:linear-gradient(#070707 0%, rgba(7,7,7,.85) 45%, rgba(7,7,7,0) 100%)}`. It is `opacity:0`, and becomes 1 (0.5 s) when `body.is-scrolled`, which is toggled by a ScrollTrigger from 80 px to the end of the page.
- Status block at x952 y26:
  - Line 1: "Booking new projects", 16/1.2, weight 700, -0.04em, white. A yellow 6×6 dot sits 12 px to its left and pulses (`box-shadow 0 → 0 0 0 .5rem rgba(254,248,59,0)`, 2 s, infinite).
  - Line 2: "From Jan 2027", 14 px, uppercase.
- Clock at x1138 y26: the live time in `America/Sao_Paulo`, formatted `en-US` `h:mm AM`, refreshed every 15 s. Line 2: "(GMT-3)".
- "Say hello" pill: `.btn.btn--sm`, height 43, padding 0 16, `right:4.375rem` (so its right edge is at x1370), label colour white.
- Menu button at x1370: 46×43 pill, border 1 px `--c-line`, two 18×2 white bars with a 5 px gap. On hover, the second bar shifts 4 px and shrinks to 12 px.
- **Wordmark shrink on scroll:**
  - Desktop: `gsap.fromTo(brand,{scale:1,y:0},{scale:s,y:0, scrollTrigger:{start:0,end:()=>innerHeight*.55,scrub:.35,invalidateOnRefresh:true}})`, with `transform-origin:0 0` and `s = (2.25*rem)/brandWord.offsetHeight` (final cap height 36 px).
  - Compact: final height `1.75rem` and an extra `y` that moves the brand from its compact top (5.25rem tablet / 4.75rem phone) up to 1.15rem / 1.05rem.
- DO NOT render a separate small logo; the same element scales down.

## 5. Buttons, PIXEL CONTRACT

```html
<a class="btn" href="#"><span class="btn__fill"></span><span class="btn__roll"><span>Label</span><span aria-hidden="true">Label</span></span></a>
```

```css
.btn{position:relative;display:inline-flex;align-items:center;justify-content:center;gap:.5rem;white-space:nowrap;height:4rem;padding:0 1.25rem;border:1px solid var(--c-line);border-radius:999px;background:var(--c-bg);color:var(--c-accent);font-size:1rem;font-weight:700;letter-spacing:-.04em;line-height:1.2;text-transform:uppercase;overflow:hidden;isolation:isolate}
.btn__fill{position:absolute;left:-10%;top:100%;width:120%;height:220%;border-radius:50%;background:var(--c-accent);z-index:-1;transition:top .6s var(--ease-soft)}
.btn__roll{position:relative;display:block;height:1.2em;overflow:hidden}
.btn__roll>span{display:block;transition:transform .55s var(--ease-soft)}
.btn__roll>span+span{position:absolute;left:0;top:100%;width:100%;color:var(--c-on-accent)}
.btn:hover{border-color:var(--c-accent)} .btn:hover .btn__fill{top:-60%} .btn:hover .btn__roll>span{transform:translateY(-100%)}
.btn--primary{border-color:var(--c-accent);background:var(--c-accent-deep);height:3.625rem;font-size:1.5rem;font-weight:600}
.btn--primary .btn__roll{height:1em;line-height:1}
```

- An arrow icon (`.btn__ico`, a 16×16 diagonal arrow, stroke 1.6) rotates 45° on hover and turns black.
- DO NOT use glow, shadows or gradients on buttons. DO NOT center the hero CTA label: use `justify-content:space-between` with the arrow on the right.

## 6. Hero

- `.hero{position:relative;height:100svh;min-height:45rem;overflow:hidden}`.
- Video: `<video muted playsinline preload="auto" poster="…/hero-eye-bw-poster.webp">` with the `hero-eye-bw.mp4` source. The file is already **grayscale** (saturation 0, contrast ×1.08); DO NOT add color or CSS filters. It is `position:absolute;inset:0;object-fit:contain`. It plays once, 2 s at 24 fps.
- Headline, top 150, right 24, 56/1, weight 600, -0.05em, right-aligned, four lines: "Sharper" / "Ideas." in `--c-muted`, then "Drawn with" / "Purpose." in white. It is split into words for the intro.
- Paragraph, left 24, bottom 128, width 428, 32/1, weight 600, -0.05em, `text-indent:3rem`: "We shape brands, websites, and digital products" in white, then "with focus, precision and heart." in `--c-dim`. It is split into chars for the intro. **Inline-block word and char spans must set `text-indent:0`.**
- Bottom row (left/right 24, bottom 24, `align-items:flex-end`, gap 32):
  - "Chosen by:" label.
  - Logo marquee: five fictional partner marks drawn as **inline SVG** (no image files), each `height:1.6875rem;width:auto;color:#D6D6D6;opacity:.85;flex:none`, with a 50 px gap. Use exactly these SVGs; in the duplicate set replace `role="img" aria-label="…"` with `aria-hidden="true"`:

```html
<svg viewBox="0 0 96 27" role="img" aria-label="Kalon"><circle cx="13.5" cy="13.5" r="10" fill="none" stroke="currentColor" stroke-width="3"/><text x="31" y="20" font-family="Geist,sans-serif" font-size="19" font-weight="600" letter-spacing="-.6" fill="currentColor">kalon</text></svg>
<svg viewBox="0 0 112 27" role="img" aria-label="Nimbo"><circle cx="9" cy="15" r="7" fill="currentColor"/><circle cx="19" cy="11" r="8" fill="currentColor"/><text x="34" y="20" font-family="Geist,sans-serif" font-size="17" font-weight="700" letter-spacing="2" fill="currentColor">NIMBO</text></svg>
<svg viewBox="0 0 92 27" role="img" aria-label="Atria"><path d="M13 3 24 24H2z" fill="currentColor"/><text x="31" y="20" font-family="Geist,sans-serif" font-size="19" font-weight="600" letter-spacing="-.6" fill="currentColor">Atria</text></svg>
<svg viewBox="0 0 100 27" role="img" aria-label="Orbis"><circle cx="13.5" cy="13.5" r="5" fill="currentColor"/><ellipse cx="13.5" cy="13.5" rx="12" ry="5.5" fill="none" stroke="currentColor" stroke-width="2" transform="rotate(-25 13.5 13.5)"/><text x="33" y="20" font-family="Geist,sans-serif" font-size="19" font-weight="500" letter-spacing="-.4" fill="currentColor">orbis</text></svg>
<svg viewBox="0 0 98 27" role="img" aria-label="Halde"><path d="M2 3h21v21H2z" fill="none" stroke="currentColor" stroke-width="3"/><path d="M2 24 23 3v21z" fill="currentColor"/><text x="31" y="21.5" font-family="Anton,sans-serif" font-size="21" letter-spacing=".5" fill="currentColor">HALDE</text></svg>
```

  - The set is duplicated and animated `translateX(-50%)` over 28 s, linear, infinite. The marquee has `mask-image:linear-gradient(90deg,transparent,#000 12%,#000 88%,transparent)`.
  - CTA `.btn--primary` "Launch a project", 330×58, with the arrow icon.
- Scroll-out: headline and paragraph go to `yPercent:-18, opacity:.2`, and the video to `yPercent:12, scale:1.06`, all scrubbed over the hero height.

## 7. About

- `.about{height:112.5rem}` (1800). Inner `position:sticky;top:0;height:100vh;display:flex;align-items:center`.
- "(About)" `.tag` at x150. Paragraph at x267, width 944, 56/1.1, weight 600, -0.06em, colour `--c-dim`: "We blend a decade of web design and brand strategy to build clear, memorable, story-led experiences."
- Split into words and scrub the colour from `#4A4A4A` to `#FFFFFF`, stagger 0.12, `scrollTrigger:{trigger:'.about',start:'top top',end:'bottom bottom',scrub:.4}`.

## 8. Recent work, PIXEL CONTRACT

- Title row, centred: `.display` "Recent work" + `.sup` "(03)", with a 12 px gap.
- Grid columns: 505 px | 869 px, starting 174 px below the title.
- **Left panel:** `position:sticky;top:calc(50vh - 21.2rem);height:42.4rem;padding-left:24px`, flex column.
  - Index number: a mask 140 px tall holding a stacked list "01." "02." "03." (Anton 140, colour `--c-ghost`, the "." in yellow).
  - Nav, 29 px below: three buttons "Kalon", "Nimbo", "Atria". Inactive: 14 px, weight 500, `--c-dim`. Active: 16 px, weight 700, white, with a 16 px yellow line at `left:-24px`. Inactive buttons show a 10 px line in the current colour. Clicking a nav button scrolls to and centres its image.
  - Titles (pushed to the bottom with `margin-top:auto`): a 56 px mask with stacked "Kalon" / "Nimbo" / "Atria" (56/1, weight 600, -0.06em).
  - Descriptions: 8 px below, a 50.4 px mask, width 330, 24/1.05, `--c-muted`. The three texts are:
    - "Portfolio website for an independent design engineer"
    - "Website and identity for an AI automation startup"
    - "Website and identity for a creative studio"
  - Each stacked list moves with `transform:translateY(-i*33.333%)`, `transition:transform .9s var(--ease-inout)`. The active index `i` comes from a ScrollTrigger on each image (`start:'top 55%', end:'bottom 55%', onToggle: isActive → setWork(i)`).
- **Images:** 869×653, gap 147 px, `overflow:hidden`. The inner img is 118% tall at top −9%, with `object-fit:cover`.
  - Parallax: img `yPercent:-6 → 6`, scrubbed over the viewport pass.
  - Reveal: `clip-path:inset(12% 6% 12% 6%) → inset(0)`, from `top bottom` to `top 35%`, scrubbed.
- Button below, 126 px under the last image: `.btn` 680 wide, centred, "View all projects".
- DO NOT place titles under the images on desktop; they live only in the sticky panel. On compact layouts, each image gets its own caption (`.work__cap`).

## 9. Numbers

- Section 854 tall, `padding-top:150`, `overflow:hidden`. Background: the fabric texture, `position:absolute;inset:-10% 0`, `object-fit:cover`, `opacity:.55`, parallax `yPercent:-8 → 8`.
- "(Impact)" `.tag` at x24, y0. The title has two `.ln` lines, "Results" (padding-left 98) and "on record". Copy `.h3` at left 946, top 204, width 470, `text-indent:51`: "Ten years in the craft taught us one thing: bold brands and sharp digital work move real numbers."
- Stats row, 24 px side margins, four equal columns:
  - A ruler of 59 ticks (1×8, `--c-line`) along the top, revealed with `scaleY 0→1`, stagger 0.012.
  - Each `.stat` is 230 tall, with padding 35/24 and a 1 px left border.
  - Number: 80/1.3, weight 600, -0.07em, white, with the suffix in yellow.
  - Label: 32 px below, width 156.
- Values: 320+ "Projects shipped around the world", 12+ "Years designing for ambitious teams", 98% "Clients who come back for more", 40M "Revenue unlocked for our clients".
- Count-up: from 0, 1.8 s, `power2.out`, `Math.round`, once at `top 90%`.

## 10. Feature quote

- Section 921 tall, `overflow:hidden`.
- Quote: left 24, top 240, width 766, 56/1, weight 600, -0.06em, `text-indent:80`, colour `--c-dim`.
  - The red quote mark is 80 px, absolute at 0,0.
  - Text: "Vetorize treated our brand like their own. Every step was clear, every detail was sharp, and the result just works."
  - The first 7 words start white. The rest scrub to white (`start:'top 80%', end:'bottom 45%'`).
- Author row: top 561, 32 px padding-top, 1 px top border. 88×88 avatar with radius 16, "Lena Moreira" (24 px, white), "Founder at Atria" (16 px, muted).
- Image box: left 857, top 103, 559×715, `overflow:hidden`.
  - Box scale `.8 → 1` from `top 85%` to `center 50%`.
  - Inner img scale `1.25 → 1` over the full section pass.

## 11. Services, PIXEL CONTRACT

- Section `padding:150 0 149`. Title row starts at x187: "What we do best" + "(Services)".
- The list starts 65 px below the title. It has 4 rows, each with `height:10.0625rem` (161) and a 1 px top border; the list has a bottom border.
- Row (closed):
  - Number "01." at left 24, top 64: 32 px, weight 600, `--c-dim`, dot in yellow.
  - Title at left 889, top 64: 32 px, weight 600, white.
  - Image hidden: `opacity:0; transform:scale(.2); transform-origin:1.8rem 1.3rem`.
- Row (open, class `is-open`; on desktop it is set by `mouseenter` and `focus` and cleared on `mouseleave`):
  - `height:27.5625rem` (441), `background:--c-surface`, transition `.7s var(--ease-soft)`.
  - Number scales ×1.75 to `--c-muted`.
  - Image 418×313 at left 372, top 64, radius 16, `opacity:1; scale:1`.
  - Title font-size goes to 56 px (-0.06em).
  - Description `.h3` fades and rises 16 px (delay .1 s).
  - Tag pills (padding 6/12, 1 px border, muted text) at the bottom of the body (delay .18 s). Body: left 889, top 64, bottom 64, width 526.
- After any open or close, debounce `ScrollTrigger.refresh()` by 750 ms.
- Rows:

| # | Title | Description | Tags | Image |
|---|---|---|---|---|
| 01 | Web Design & Development | Fast, responsive sites built to convert and easy to grow. | Responsive Layouts, Interaction Design, CMS Setup, Technical SEO | service-01.webp |
| 02 | Branding | Identities that feel clear, lasting and unmistakably yours. | Logo Systems, Color Palettes, Typography, Brand Guidelines | service-02.webp |
| 03 | Social Media | Templates and content kits that keep every post on-brand and worth the scroll. | Post Design, Story Kits, Content Templates, Visual Rhythm | service-03.webp |
| 04 | Motion Design | Purposeful animation that brings rhythm, clarity and life to every screen. | Micro-interactions, Scroll Effects, Hover States, Page Transitions | service-04.webp |

- DO NOT open rows on click on desktop. On compact layouts every row is shown open, statically.

## 12. Process (pinned horizontal scroll), PIXEL CONTRACT

- `.proc{height:162.0625rem}` (2593). `.proc__pin{position:sticky;top:0;height:100vh;overflow:hidden}`.
- `.proc__track{display:flex;width:max-content;padding:170px 0 0 24px}`.
  - Head block 586 wide: two `.ln` lines "Step by" / "step" + "(Process)".
  - Then 4 cards of 360×560, with top/bottom borders, a 1 px left border on each card and a right border on the last.
- Card content:
  - "STEP n" 32 px muted, with a yellow ".".
  - Title at top 316 (56/1, weight 600).
  - Description `.h3` at top 460, width 312.
  - On hover, a 2 px yellow top line scales X 0→1 (0.8 s).
- Cards: Deep Dive / "We learn your goals, audience, pain points and what makes you different."; Kickoff & Plan / "We lock scope, milestones and timelines, then get straight to work."; Review<br>& Polish / "We share early designs, gather your notes and refine them together."; Launch & Scale / "We ship with confidence and stay close for whatever comes next."
- Desktop only: `gsap.to(track,{x:()=>-(track.scrollWidth-(innerWidth-1.5*rem)),ease:'none',scrollTrigger:{trigger:'.proc',start:'top top',end:'bottom bottom',scrub:.6,invalidateOnRefresh:true}})`. At the end, the last card's right edge sits at x1416.

## 13. Testimonials

- `padding-top:150`, height 968.
- Rating at x24: a 32 px red star, then "4.9" (56 px) with "/5" (24 px, muted), then "280+ verified **client reviews**" (with "client reviews" in white).
- "(Testimonials)" at x416. Heading at x544, width 872, 56/1, weight 600, `text-indent:108`: "Our work is measured by results, not opinions. Here is what our clients have to say."
  - Split into chars; scrub `opacity .14 → 1`, stagger 0.02, `start:'top 82%', end:'bottom 40%'`.
- Cards: three columns, gap 8, 12 px under the 248 px top block. Each card is 420 tall, `--c-surface`, radius 8, padding 24.
  - Avatar 64 with radius 8, name 24/1 weight 600, role 16 muted.
  - Quote at the bottom: 32/1.1, weight 600, -0.06em, `text-indent:33`, with a red “ mark.
  - On hover, a yellow 1 px inner border fades to 0.6.

| Name | Role | Quote | Avatar |
|---|---|---|---|
| Daniel Okafor | CTO at Kalon | Our channels felt random and off-brand. Now every post is consistent, engaging and performs far better. | avatar-01.webp |
| Sofia Lindqvist | Marketing Lead at Nimbo | Brand, website and social landed as one system: strategic, cohesive and made with real care. | avatar-02.webp |
| Priya Raman | Founder at Atria | It felt like adding an in-house team that understood us from day one. | avatar-03.webp |

## 14. Pricing, PIXEL CONTRACT

- Grid `25rem 1fr`, gap 73, padding `150 24 150`.
- Left column:
  - "Pricing" display title.
  - Subcopy `.h3` width 401, `text-indent:32`: "Simple plans that grow with you, from first launch to full scale."
  - Toggle 400×56: 1 px border, pill shape, 8 px padding. It holds the buttons "Monthly" and "Annual" with a red "-20%". A `#2A2A2A` pill (192×40) slides `translateX(11.875rem)` for annual, 0.6 s `--ease-inout`.
  - At the bottom: a "Custom" block (border-top, padding-top 36): `.h2` "Custom", "Tailored for unique scopes or one-off projects.", and a 160×64 "Get a quote" button.
- Plans: two columns, gap 16, each card 683 tall, radius 8, padding `48 24 24`.
  - Starter: `--c-surface`.
  - Growth: `--c-bg` with a `1px solid rgba(254,248,59,.35)` border and a red "(Popular)" label.
  - Inside: name `.h2`, then the description (muted, 40 px bottom padding, 1 px bottom border), then 6 list items (24/1.05, white, yellow "+" bullets, 12 px gap), then the price at the bottom, then the button "Choose this plan" (100% wide, 64 tall, 68 px above).
- **Price:** `$` (24 px muted, top-aligned) + digits `0000,00` (56/1, weight 600, -0.06em) + `/mo` (24 px weight 600 muted). **Every price reads exactly `$0000,00` for both plans and both billing periods.**
- Slot-machine digits: each digit is a 1em-tall mask holding a column of 11 numbers (d … d+10 mod 10). On toggle, animate every column `yPercent:0 → -100*10/11` (duration 0.9 s + (i%6)·0.07 s, `power3.inOut`), then reset to 0. The value does not change.
- Starter list: "1 Senior designer", "72-hour turnaround", "One request at a time", "Pause or cancel anytime", "Up to 40 hours a month", "Async communication".
- Growth list: "2 Senior designers", "48-hour turnaround", "Two requests at a time", "Pause or cancel anytime", "Up to 70 hours a month", "Async communication".
- DO NOT show any price other than `$0000,00`.

## 15. FAQ

- Grid `29.5rem 1fr`, padding `150 24 96`. The fabric texture background is the same as Numbers.
- Left column: "FAQ" display title, then "Still curious?" and a yellow "Talk to us" link (underline grows 0→100% on hover).
- Items: 920 wide, 1 px border, radius 16, gap 16.
  - Question: 32/1, weight 600, padding 32.
  - Icon: 14×14 yellow plus that becomes a minus (the vertical bar rotates 90° → 0°).
  - Answer: `height:0 → auto` with GSAP, 0.6 s `power3.inOut`. Only one item is open at a time, and the first starts open. Answer padding is `0 32 32`, text muted.
- Q/A pairs:
  1. "How does a new project usually start?" — "Every project opens with discovery: your goals, audience and competitors. Then we move through strategy, design and build, with you in the loop at every milestone. Each service has its own checkpoints, and collaboration never stops."
  2. "How long does a typical project take?" — "It depends on scope. A focused website or identity takes 2 to 3 weeks; larger builds with motion or strategy take 4 to 6. You get a clear timeline before we start."
  3. "Do you offer fixed plans or custom quotes?" — "Both. Our plans keep things simple, and custom quotes cover projects with special needs. Tell us what you have in mind and we will shape it around you."
  4. "What comes in a branding package?" — "The essentials: logo, color palette, type pairing and a compact style guide. Social templates or creative direction can be added when needed."
  5. "Can you join our existing dev or marketing team?" — "Absolutely. We plug into development, content or marketing teams with clear communication and shared tools, so everyone moves in the same direction."

## 16. Journal, contact and footer

**Journal**
- Title "Fresh thinking" + "(Journal)". On the right, a "Read all" `.btn` (200×53, label left, arrow right, 87 px from the top).
- Grid `690px 1fr 1fr`, gap 12, cards 520 tall, radius 8.
  - Big card: full-bleed image with the overlay `linear-gradient(#070707 0%, transparent 14%, transparent 62%, #070707 100%)`. White chip "Design Strategy" at 24,24. Date "Nov 4, 2026" and title "Clear Beats Clever in Web Design" (32 px, white) at the bottom.
  - Small cards: 1 px border, 8 px padding, image 212 tall. Chip, date, title (24 px), and a 2-line clamped excerpt.
    - Card 2: "Social Media", "Oct 21, 2026", "Motion That Makes a Website Feel Alive" / "Good motion makes a site feel modern and easy to read. Here is how to use it without overdoing it."
    - Card 3: "Branding", "Oct 8, 2026", "How to Spot a Template Worth Buying" / "Templates are not all equal. These signs separate the solid ones from the rest of the pack."
- Hover: the image scales 1.06 (1 s); the title turns yellow; `cursor:none` on media, and a "Read post ↗" pill (dark glass, `blur(8px)`, 1 px white/22% border) follows the cursor with `gsap.quickTo` (0.45 s) and scales 0→1 with `back.out(2)`.

**Contact**
- Section 700 tall.
- Hand image: absolute, top −100, height 800, full width, `object-fit:contain`, z 0. Parallax `yPercent:18 → -6`.
- Title at x24, top 210: two lines "Start your" / "next move".
- Copy `.h3` at left 1016, top 264, width 400, indent 51: "Have an idea taking shape? Tell us about it and we will help you turn it into something great."
- CTA `.btn--primary` "Contact us", 400×64, at top 372, linking to `mailto:hello@000000.com`.

**Footer**
- Main block, 424 tall:
  - "(Email)" label + yellow "hello@000000.com" (32 px, underline grows on hover).
  - "(Phone)" label + "+00 0000 0000" (`tel:+0000000000`).
  - "(Links)" at x581: Home, Studio, Work, Services, Pricing, Journal, Contact.
  - "(Socials)" at x705: X/Twitter, Instagram, LinkedIn, Behance, each with a ↗ icon.
  - Newsletter at x1046, width 370: "Join our newsletter for fresh insights and studio news.", an underline input with the placeholder "Your email address" (its border turns yellow on focus), and a full-width "Subscribe" button.
  - Legal row at top 361: "©2026 Vetorize. All rights reserved", "Privacy Policy • Terms of Service", and on the right "Designed & built in-house".
- Band: 352 tall, yellow, `overflow:hidden`.
  - Inner row at left/right 24, bottom 36: a black wordmark (14rem) on the left, and on the right the slogan "Sharper Ideas. Drawn with Purpose." (56/1, weight 600, black, right-aligned, width 280).
  - Reveal: the inner row goes `yPercent:-40 → 0` from `top bottom` to `bottom bottom`, scrubbed. The wordmark letters rise `yPercent:100 → 0`, stagger 0.045, 1 s, `expo.out`, starting at `top 75%`.

**Menu drawer**
- Veil `rgba(7,7,7,.62)` with `blur(2px)`.
- Panel 470 wide on the right, 1 px left border, `translateX(100%) → 0` over 0.8 s `--ease-inout`.
- Top bar 88 tall: a red square + "Menu" label on the left, and a round close button on the right that rotates 90° on hover.
- Links: Anton 64 px, padding 25/0, a bottom border on each. Labels rise from 110% with a 0.06 s stagger. "Home" has a yellow 8×8 square after it.
- Below: "(Email)", the yellow email, "(Socials)" in 2 columns.
- Esc, the veil and the close button all close the drawer. Call `lenis.stop()` while it is open.

**Generic reveals**
- `[data-rise]` display titles: every `.ln` (`display:block;overflow:hidden;white-space:nowrap`) is split into chars that go `yPercent:115, rotate:4 → 0`, 1.1 s, stagger 0.028, `expo.out`, at `top 88%`, with `toggleActions:'play none none reverse'`.
- `.sup` labels fade up 10 px.
- `[data-fade-up]`: `y:40`, 1 s, `expo.out`, at `top 90%`.
- `[data-card]`: `ScrollTrigger.batch`, `y:70 → 0`, 1.1 s, stagger 0.1.
- Service rows: batch fade-up 40 px.

## 17. Responsive (compact flow layout, <1024 px)

- Tokens at <1024: `--fs-display:6rem; --fs-stat:3.75rem; --fs-h1:2.5rem; --fs-h2:1.5rem; --fs-h3:1.25rem; --sec-pad:6rem`.
- Tokens at <768: `--fs-display:3.5rem; --fs-stat:2.75rem; --fs-h2:1.375rem; --gutter:1rem; --sec-pad:5rem`.
- Every absolute desktop block becomes `position:static` (or `relative`) in normal flow. `.ln` uses `white-space:normal`. Grids use `minmax(0,1fr)` so nothing overflows (**no horizontal scroll at 390 px**).
- Header:
  - Status and clock are hidden.
  - "Say hello" sits at right 5.125rem (phone 4.25rem), top 1rem.
  - The wordmark starts under the buttons (top 5.25rem, font 7.5rem; on phone, top 4.75rem and font `min(5.6rem, (100vw - 2.5rem)/3.58)`), then shrinks to 1.75rem and moves up.
- Hero:
  - Video uses `object-fit:cover; object-position:42% 50%`.
  - Headline at bottom 16.5rem, 36 px (phone: bottom 15.5rem, 30 px).
  - Paragraph at 24 px (phone 21 px).
  - The bottom row stacks; the CTA goes full width; the "Chosen by" label is hidden on phone.
- About: static, 36 px text (phone 28 px), and the scrub runs on the paragraph itself.
- Work: no sticky panel. Each image has `aspect-ratio:1604/1340`, followed by a caption ("Kalon 01." + description).
- Numbers: 2×2 stats, no ruler.
- Feature: stacked; the image has `aspect-ratio:559/715`, max 512 px.
- Services: every row is open and static, in a 2-column grid (number | body). On phone, a single column. Images use 4:3.
- Process: no pin; cards in a 2-column grid (phone: 1 column), 352 tall (phone 288).
- Testimonials: one column.
- Pricing: one column; plans in 2 columns on tablet and 1 on phone.
- FAQ: one column, question 22 px (phone 18 px).
- Journal: 2 columns on tablet with the big card spanning both; 1 column on phone.
- Contact: in flow with `padding-top:12rem`; the hand image covers the section at `opacity:.6`.
- Footer: a 2-column grid; the band stacks with the wordmark at `min(7.5rem, (100vw - 3rem)/3.6)`.

## 18. Assets (all hosted, base `https://assets.layerweb.com.br/vetorize`)

All photos are free stock photos from Unsplash (Unsplash License), cropped and converted to WebP. The partner logos are inline SVG (section 6), not files.

| File | Used in | Size / notes | Source |
|---|---|---|---|
| https://assets.layerweb.com.br/vetorize/anton-400-latin.woff2 | Display font | woff2, 18.6 KB | Google Fonts (OFL) |
| https://assets.layerweb.com.br/vetorize/geist-var-latin.woff2 | Text font (variable 100–900) | woff2, 29.4 KB | Google Fonts (OFL) |
| https://assets.layerweb.com.br/vetorize/hero-eye-bw.mp4 | Hero video | 1920×1080, 24 fps, 2.0 s, grayscale, no audio, plays once | — |
| https://assets.layerweb.com.br/vetorize/hero-eye-bw-poster.webp | Hero poster / reduced motion | last frame, 1920×1080, grayscale | — |
| https://assets.layerweb.com.br/vetorize/stock-work-01-v2.webp | Work, Kalon (laptop with a portfolio site) | 1604×1340 | Unsplash photo-1707836885254-79b6e3d7b18d |
| https://assets.layerweb.com.br/vetorize/stock-work-02-v2.webp | Work, Nimbo (printed brand identity) | 1604×1340 | Unsplash photo-1636247499734-893da2bcfc1c |
| https://assets.layerweb.com.br/vetorize/stock-work-03-v2.webp | Work, Atria (laptop with a yellow site) | 1604×1340 | Unsplash photo-1558174685-430919a96c8d |
| https://assets.layerweb.com.br/vetorize/stock-texture-fabric.webp | Numbers + FAQ background | 2400×1488 | Unsplash photo-1618022325802-7e5e732d97a1 |
| https://assets.layerweb.com.br/vetorize/stock-feature-image-v2.webp | Feature quote image (phone with a site) | 1118×1430 | Unsplash photo-1707836916010-3c4ad261936c |
| https://assets.layerweb.com.br/vetorize/stock-feature-avatar.webp | Feature author (Lena Moreira) | 192×192 | Unsplash photo-1506863530036-1efeddceb993 |
| https://assets.layerweb.com.br/vetorize/stock-service-01.webp | Service 01, Web Design | 836×626 | Unsplash photo-1558655146-d09347e92766 |
| https://assets.layerweb.com.br/vetorize/stock-service-02.webp | Service 02, Branding | 836×626 | Unsplash photo-1617661338085-d1ec6a89a6d8 |
| https://assets.layerweb.com.br/vetorize/stock-service-03.webp | Service 03, Social Media | 836×626 | Unsplash photo-1754821130727-685b74ac81fc |
| https://assets.layerweb.com.br/vetorize/stock-service-04.webp | Service 04, Motion Design | 836×626 | Unsplash photo-1622737133809-d95047b9e673 |
| https://assets.layerweb.com.br/vetorize/stock-avatar-01.webp | Testimonial, Daniel Okafor | 192×192 | Unsplash photo-1614010966237-74489a16848b |
| https://assets.layerweb.com.br/vetorize/stock-avatar-02.webp | Testimonial, Sofia Lindqvist | 192×192 | Unsplash photo-1601412436009-d964bd02edbc |
| https://assets.layerweb.com.br/vetorize/stock-avatar-03.webp | Testimonial, Priya Raman | 192×192 | Unsplash photo-1532170579297-281918c8ae72 |
| https://assets.layerweb.com.br/vetorize/stock-journal-01.webp | Journal, big card | 1380×1040 | Unsplash photo-1616588589676-62b3bd4ff6d2 |
| https://assets.layerweb.com.br/vetorize/stock-journal-02.webp | Journal, card 2 | 900×600 | Unsplash photo-1550275994-f0ada0c3db31 |
| https://assets.layerweb.com.br/vetorize/stock-journal-03.webp | Journal, card 3 | 900×600 | Unsplash photo-1522542550221-31fd19575a2d |
| https://assets.layerweb.com.br/vetorize/stock-contact-hand.webp | Contact background (red-lit hand) | 2000×985 | Unsplash photo-1559913929-08b6989ec45e |

- 21 files in total; all respond 200 with `Access-Control-Allow-Origin: *`.
- Exact `alt` texts: `stock-work-01-v2.webp` → "Laptop on a wooden desk showing a bold portfolio website, with a phone beside it"; `stock-work-02-v2.webp` → "Printed brand identity pieces: brochures, cards and letterhead in blue, orange and black"; `stock-work-03-v2.webp` → "Laptop by a sunny window showing a yellow and blue website"; `stock-feature-image-v2.webp` → "Phone showing a blue website, resting on a wooden desk"; `stock-service-01.webp` → "Design interface on a desktop monitor beside a plant"; `stock-service-02.webp` → "Glass bottle with orange liquid against a warm gradient"; `stock-service-03.webp` → "Hand holding a smartphone against a yellow background"; `stock-service-04.webp` → "Floating 3D cubes and yellow spheres"; `stock-journal-01.webp` → "Room lit with purple neon and a hello sign"; `stock-journal-02.webp` → "Colorful light trails in motion"; `stock-journal-03.webp` → "Watercolor sketches of website layouts". Avatars use "Portrait of <name>"; the texture and the contact hand are decorative (`alt=""`).
- Every `<img>` has `width`/`height` matching the file and `loading="lazy"` below the fold.
- Fallback: on `error`, add `.broken{opacity:0}` (no layout shift).

## 19. Accessibility

- `prefers-reduced-motion: reduce`:
  - No Lenis, no loader (it is removed immediately), no scrubs, no intro.
  - All CSS transitions and animations are set to 0.001 ms, and the marquee stops.
  - The video jumps to its last frame.
  - Scroll-reveal words are white, and counters show their final values.
  - The header wordmark snaps to header size once `scrollY > 30vh` (it does not stay huge).
- One `<h1 class="sr">` in the hero: "Vetorize, a design studio for brands, websites and digital products".
- Buttons have `aria-label`s, the menu toggles `aria-expanded`/`aria-hidden`, FAQ questions toggle `aria-expanded`, and the pricing buttons toggle `aria-pressed`.
- `:focus-visible{outline:2px solid #FEF83B; outline-offset:3px}`. Service rows are focusable (`tabindex="0"`) and open on focus.

## 20. Final self-check (run before answering)

1. Fonts: `document.fonts` reports `Anton:loaded` and `Geist:loaded`.
2. Assets: 18 `<img>` elements loaded (`naturalWidth > 0`) and 10 inline logo SVGs in the marquee. The grayscale video reaches `readyState 4`. No 404s, no console errors.
3. At 1440×900 the document height is ≈16 440 px ±150.
4. The loader is yellow and slides up. The wordmark lands at x24 y24 and shrinks to a ≈36 px cap height by 55% of the viewport scrolled.
5. About words go from gray to white across the 1800 px sticky section.
6. The work panel index reads 01 → 02 → 03 as each image crosses 55% of the viewport.
7. The four counters end at 320 / 12 / 98 / 40.
8. Hovering a service row expands it to 441 px and shows its image (418×313), description and 4 tags.
9. The process track ends with the last card's right edge at x1416.
10. Both prices read `$0000,00` in Monthly AND Annual. The toggle runs the digit roll.
11. Exactly one FAQ item is open at a time.
12. The journal pill follows the cursor over the images.
13. The footer band is yellow with a black wordmark and a 4-line slogan.
14. No horizontal scroll at 390 px or 820 px.
15. No brand name other than "Vetorize" (the fictional partner marks Kalon, Nimbo, Atria, Orbis and Halde are allowed). No colours outside the token list.
16. Contact details are placeholders: email `hello@000000.com`, phone `+00 0000 0000`.
