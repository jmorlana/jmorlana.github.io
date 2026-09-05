# jmorlana.github.io

Source of Javier Morlana's personal website: https://jmorlana.github.io/

Plain HTML and CSS, served by GitHub Pages. No build step: edit, commit, push.
The only JavaScript on the page highlights the current section in the top bar
and fades the name in once you scroll; everything works without it.

## Files

- `index.html` — the main page: intro, Now, News, Publications, Experience.
- `blog/` — the blog: `index.html` (list of cards) and one HTML file per post, from `_template.html`.
- `stylesheet.css` — all the styles. Colours, fonts and the column width live in
  the `:root` block at the top.
- `images/` — original figures at full resolution.
- `images/web/` — downscaled copies actually used by the page (1400 px wide, JPEG).
- `images/logos/` — 144 px square logos for News and the Experience timeline.
- `images/profile.jpg` — the portrait at full size; `images/web/profile.jpg` is the 320 px copy the page uses.
- `cv.pdf` — linked from the CV button in the intro.
- `images/og-card.png` — 1200×630 preview card for LinkedIn, X and messaging apps.

## The mark

`images/logo.svg` is the JM signature, a single stroke. It is also embedded in each
page's sprite as `#i-jm` (top bar and footer). `images/favicon/favicon.svg` is the
same stroke inside a circle; the PNG and ICO files there are rendered from it.

## Icons

The social icons are inline SVG in a hidden `<svg class="sprite">` at the top of
`<body>`, used through `<use href="#i-github">`. No icon font, no extra request.
There is no box around them: the logo itself is the button. "CV" has no logo, so it is drawn as one.
The brand marks come from [Simple Icons](https://simpleicons.org) (CC0) and the
envelope from [Bootstrap Icons](https://icons.getbootstrap.com) (MIT). To add one,
copy the `<path>` out of the library's SVG into a new `<symbol>`.

## Adding a news item

Add an `<li>` at the top of the `<ul class="news">` list. The logo is a 144 px
square in `images/logos/` (see below); title, one line of detail with the date,
and optional button links:

```html
<li>
  <img class="mark" src="images/logos/acme.png" width="144" height="144" alt="">
  <div>
    <p class="title">Something happened</p>
    <p class="meta">One line of detail. January 2027.</p>
    <p class="links"><a href="https://…" target="_blank" rel="noopener">Site</a></p>
  </div>
</li>
```

## Adding a publication

Copy an `<article class="card">` block and change the figure, title, authors,
venue and links. Then add the web-sized figure:

```bash
python3 - <<'EOF'
from PIL import Image
im = Image.open("images/newfigure.png").convert("RGB")
w = 1400; h = round(im.height * w / im.width)
im.resize((w, h), Image.LANCZOS).save(
    "images/web/newfigure.jpg", "JPEG", quality=82, optimize=True, progressive=True)
print(w, h)  # use these in the <img> width/height attributes
EOF
```

## Adding an experience entry

Copy an `<article class="step">` inside `.timeline`. The date is optional:

```html
<article class="step">
  <img class="mark" src="images/logos/acme.svg" width="36" height="36" alt="">
  <div class="entry">
    <p class="when">2027 — present</p>
    <h3>Role</h3>
    <p class="org">Organisation</p>
    <p class="where">City, Country</p>
  </div>
</article>
```

Logos are 144 px squares with a transparent background, the mark centred.
Until one exists, `<span class="mark" style="--mark:#1F5F8B">E</span>` draws a
coloured circle with initials instead.

The line draws itself and the entries fade in as they scroll into view. The CSS
keeps everything visible by default and only hides it under a `.js` class that
the page sets when `IntersectionObserver` exists, so without JavaScript — or with
"reduce motion" turned on — the timeline is simply there, static. To drop the
animation entirely, delete the `.js .timeline` / `.js .step` rules in
`stylesheet.css`; no HTML change needed.

## The CV

The `CV` button in the intro links to `cv.pdf` in the repo root. Replace the file
to update it.

## Replacing the photo

Put a square photo at `images/profile.jpg`, then make the 320 px copy the page uses:

```bash
python3 - <<'EOF'
from PIL import Image
im = Image.open("images/profile.jpg").convert("RGB")
im.resize((320, 320), Image.LANCZOS).save("images/web/profile.jpg", "JPEG", quality=88, optimize=True, progressive=True)
EOF
```

The page shows it as a 136 px square with a thin frame (`.portrait` in the CSS).

`images/og-card.png` repeats the name, the headline and the photo; regenerate it
by hand if any of those change.

## The blog

`blog/index.html` is the list of posts, one card each, newest first; the card
markup is in a comment inside the file. The whole card is a link: a tag pill
(`research`, `code`, `company`, or none), the month, the title, one line of text
and an arrow. Add `has-image` and an `<img class="post-thumb">` for a 96 px
thumbnail beside the text. Remove the "No keyframes yet" placeholder card with the
first real post.

A post is a copy of `blog/_template.html` saved as `blog/my-post.html`: fill the
TITLE / SUMMARY / DATE / IMAGE marks, write inside `<div class="prose">`, delete
the cover `<figure>` if there is no image. Post images go in `images/blog/`.
Blog pages have no footer, on purpose.

If the blog grows past a handful of posts, moving it to Jekyll (Markdown posts,
GitHub builds the list) reuses this same CSS; nothing here would change visually.
