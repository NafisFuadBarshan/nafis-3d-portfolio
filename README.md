# Nafis Fuad Barshan | 3D Portfolio

An interactive, scroll-driven 3D portfolio built with **HTML, Tailwind CSS and Three.js**. As you scroll, the camera flies along a curved path through a dark, low-poly scene, stopping at each section: About, Experience, Skills, Projects, Honors and Contact.

The whole site is a single self-contained file: [`index.html`](./index.html). Styles, scripts and images are all inside it, so there is no build step and no `node_modules`.

Live site: <https://nafisfuadbarshan.github.io/nafis-3d-portfolio/>

---

## Features

- **Hero:** floating geometric shapes and a large icosahedron that react to the mouse with depth-based parallax. The camera glides in on load.
- **Scroll-driven camera:** scroll position maps to a position on a curved path. The camera holds near each section while you read, then eases to the next one.
- **Interactive project cards:** the seven projects are 3D cards with the screenshot and title on them. They tilt toward the cursor, lift and glow on hover, and open a modal with a gallery, description and tags on click or tap.
- **Linked hover states:** hovering a skill group, an achievement or an internship highlights its 3D counterpart (orbit ring, gem or tile).
- **Modal viewer:** image gallery, previous/next project, `Esc` to close, `←` / `→` to navigate, focus trap and focus restore.
- **Dark aesthetic:** cyan and ember accents, flat-shaded low-poly materials, fog, a floor grid and soft shadows on desktop.
- **Responsive:** phone layouts get a compact horizontal project list, smaller cards and fewer particles.
- **Graceful fallback:** if WebGL or the Three.js download fails, the site switches to a normal flat layout with a thumbnail grid for projects.
- **Accessibility:** semantic sections and headings, visible keyboard focus, keyboard-operable project list, `aria` labels on controls, and `prefers-reduced-motion` support (idle motion, the intro and pointer parallax are switched off).

## Tech stack

| Piece | Details |
| --- | --- |
| 3D | [Three.js r128](https://threejs.org/) loaded from cdnjs |
| Styling | Tailwind CSS 3, compiled and purged (about 7 KB), inlined in `<style id="tailwind">`, plus a hand-written `<style id="custom">` block |
| Fonts | Bricolage Grotesque (headings) and DM Sans (body) from Google Fonts |
| Images | 13 images converted to WebP and embedded as data URIs (about 1 MB total) |

## Getting started

Open `index.html` in a browser. It works by double-clicking the file, and an internet connection is needed for Three.js and the fonts.

To serve it locally:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Deploying to GitHub Pages

1. Put `index.html` at the root of your `nafis-portfolio` repository (replacing the old `index.html`).
2. Delete the old `style.css` and `script.js`, which are no longer used.
3. Commit and push. In the repository, go to **Settings → Pages** and publish from the main branch.

The site will be served at `https://nafisfuadbarshan.github.io/nafis-portfolio/`.

## Editing your content

Most edits are made directly in `index.html`.

| What | Where |
| --- | --- |
| Bio, education, languages, experience, skills, honors, certifications, contact details | The HTML inside each `<section>` under `<main>` |
| Projects (title, description, tags, images, links) | The `PROJECTS` array near the top of the main `<script>` |
| Images | The `ASSETS` block at the very bottom of the file |

### Adding a link to a project

Each project has a `link` field that is `null` by default. Set it to a URL and a "Visit project" button appears in that project's modal:

```js
{ id:'eduhelp', short:'EduHelp', /* ... */ link:'https://github.com/NafisFuadBarshan/EduHelp' }
```

### Adding or changing a project

Add an object to `PROJECTS`. The list, the 3D cards and the modal are all generated from this array.

```js
{
  id: 'my-project',
  short: 'My Project',                 // shown on the 3D card and in the list
  kind: 'Project',                     // label in the modal (e.g. Project, Thesis)
  period: '2026',                      // optional
  title: 'My Project: Full Title',
  desc: 'One or two sentences.',
  feats: ['Optional bullet', 'Another bullet'],   // optional
  tags: ['Django', 'Python'],
  imgs: [['myKey', 'Alt text describing the image']],
  focus: 0.5,                          // optional: 0 = crop from the top, 1 = from the bottom
  link: null
}
```

If you change the number of projects, the cards are spaced along the path automatically. For a lot more cards, increase the `min-height` of `#projects` in the CSS so the camera has enough scroll distance.

### Replacing images

Images live in the `ASSETS` block as `"key": "data:image/webp;base64,..."`. To replace one, put your new data URI under the same key.

To use ordinary image files instead, replace the values with paths:

```js
window.ASSETS = { eduhelp: 'images/eduhelp.webp', /* ... */ };
```

If you do this, **serve the site over http(s)**. Browsers block WebGL textures loaded from `file://` paths, so the 3D cards would stay blank when opened by double-clicking.

## Customizing the look and motion

- **Colors and spacing:** CSS variables at the top of the `custom` style block (`--neon`, `--ember`, `--lime`, and so on). Matching colors also exist in the scene code (`M = { ... }` for materials).
- **Camera path:** the `CatmullRomCurve3` point list in `init3D()`.
- **Where the camera stops:** the `STATIONS` array (`ta` and `tb` are the start and end positions along the path, from 0 to 1). Each station's section length comes from the `min-height` values in the CSS (`#about`, `#skills`, and so on).
- **Camera easing speed:** the `damp(tSmooth, target, 4.5, dt)` line in `tick()`. Higher is snappier.
- **Where 3D props sit:** each station group is placed with `anchor(group, t, ahead, side, dy)`. `side` moves it left (negative) or right (positive) of the path.
- **Card layout:** `layoutCards()` controls spacing, zig-zag offsets and tilt.

## Editing Tailwind classes

The Tailwind CSS in the file is **pre-compiled and only contains the utility classes the page already uses**. If you add new Tailwind classes in the HTML, they won't have any effect until you either:

- add the Play CDN while developing: `<script src="https://cdn.tailwindcss.com"></script>`, or
- recompile with the Tailwind CLI (v3) against `index.html` and paste the output back into `<style id="tailwind">`:

  ```bash
  npx tailwindcss@3 -c tailwind.config.js -i in.css -o tw.css --minify
  ```

  where `in.css` contains `@tailwind base; @tailwind components; @tailwind utilities;` and the config extends the theme with the colors and fonts listed in the `:root` variables.

Custom classes (`.panel`, `.btn`, `.work`, and so on) are plain CSS and can be edited directly.

## Performance notes

- Pixel ratio is capped (1.75 on desktop, 1.5 on phones).
- Shadow maps and antialiasing are disabled on small or touch screens.
- Each 3D station is hidden while the camera is far from it, and rendering pauses while the project modal is open.
- Star and particle counts are reduced on phones.

## Browser support

Any modern browser with WebGL (current Chrome, Edge, Firefox and Safari). Without WebGL the flat fallback layout is shown.

## Project structure

```
index.html      The whole site (markup, CSS, JS, images)
README.md       This file
```

Inside `index.html`, in order: Tailwind CSS, custom CSS, HTML sections, project modal, Three.js script tag, main script (UI + 3D scene), and the `ASSETS` image block.

## Credits

- [Three.js](https://threejs.org/) (MIT)
- [Tailwind CSS](https://tailwindcss.com/) (MIT)
- Fonts: [Bricolage Grotesque](https://fonts.google.com/specimen/Bricolage+Grotesque) and [DM Sans](https://fonts.google.com/specimen/DM+Sans) (SIL Open Font License)

Portfolio content and images © Nafis Fuad Barshan.
