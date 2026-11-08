# CLAUDE.md

Static one-page placeholder ("parking") site for **uniware.ca**, served by GitHub Pages from `main` (repo `uniware/parking`). Pushing to `main` deploys it.

## Layout

- `index.html` — the entire site: inline `<style>`, markup, and a small inline slideshow `<script>`. No build step, no dependencies, no framework.
- `images/hero/*.jpg` — hero slideshow photos.
- `CNAME` — custom domain for GitHub Pages. Do not remove or change it.
- `README.md` — release log of `# release @HH:MM Mon D, YYYY` lines.

## Working on it

- Preview locally: `python3 -m http.server` in the repo root, then open http://localhost:8000. Image paths are relative (`./images/...`), so opening the file directly also works.
- Keep everything self-contained in `index.html`. Don't add a build tool, package manager, or external JS.
- The CSS class names (`custom-header-media`, `site-branding`, `post-14`, etc.) come from the WordPress Twenty Seventeen theme the page was copied from. Some rules (nav, search form, entry title) are currently unused.

## Hero slideshow

- Each slide is a `<div class="slide">` inside `.custom-header-media`. The first slide sets its image with inline `background-image`, carries `is-active`, and is also `<link rel="preload">`ed in `<head>` (keep those two in sync if it changes).
- Every other slide is lazy-loaded: its image goes in `data-bg`, and the script sets it one slide ahead of being shown. Don't put `background-image` on those slides or all photos download at page load again.
- Adjust cropping per slide with an inline `background-position` (e.g. `center 40%`).
- The script rotates `is-active` every 6000 ms. The fade is a 1.5 s CSS opacity transition, disabled under `prefers-reduced-motion`.
- To add a photo: process it (see Images), put it in `images/hero/`, add a `.slide` div with `data-bg`, and tune `background-position`.

## Images

- Prefer landscape photos. Phone photos are often Display P3; convert to sRGB, or colours shift once the profile is dropped.
- Processing recipe (macOS; converts to sRGB, caps width at 1920 px, strips all metadata including EXIF/GPS, quality 75 progressive JPEG):
  ```sh
  sips -m "/System/Library/ColorSync/Profiles/sRGB Profile.icc" -s format tiff in.jpg --out /tmp/x.tif
  ffmpeg -v error -i /tmp/x.tif -vf "scale='min(1920,iw)':-2:flags=lanczos,format=rgb24" -f image2pipe -vcodec ppm - \
    | cjpeg -quality 75 -progressive -optimize > images/hero/out.jpg
  ```
- The current photos are highly detailed, so they land around 100–900 KB each at this setting.

## Fonts

- Libre Franklin is loaded from Google Fonts (weights 300/400/600/700/800). If CSS starts using another weight, add it to the `<link>` URL in `<head>`.

## Commits

- Short imperative messages, e.g. "Add tunnel photo to hero slideshow".
