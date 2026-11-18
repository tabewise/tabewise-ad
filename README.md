# Home banners (advertising carousel)

The Home screen shows a swipeable carousel of banners. Banners are **not inside the app**: the app lists the `offer/` folder of the GitHub repository `Shahidrox/Add-Halal-Lance` and shows every image in it. To change the banners, upload to GitHub; no new APK is needed.

## What to upload

Folder: **`offer/` on the `master` branch** of `Shahidrox/Add-Halal-Lance` (https://github.com/Shahidrox/Add-Halal-Lance/tree/master/offer). The app reads it through
`https://api.github.com/repos/Shahidrox/Add-Halal-Lance/contents/offer?ref=master`. The repository must stay **public**.

| | |
|---|---|
| Images | `.png`, `.jpg`, `.jpeg` or `.webp`, **1200 x 600 pixels (2:1)**, under **2.5 MB** each |
| Which are shown | **Every image in the folder**, in file-name order (`01-…`, `02-…` to control the order), at most **6** |
| No images | The carousel does not appear and takes no space |
| Starter files | `docs/banners/samples/banner-1-sakura.png` and `banner-2-sky.png` (already uploaded to `offer/`) |

The app does **not** add an "Ad" label. If a banner is paid advertising, put the wording (for example **広告** or **PR**) in the image itself.

## Design specification (for designers)

| Item | Value |
|---|---|
| **Canvas** | **1200 x 600 px, ratio 2:1** (the app draws the banner at exactly 2:1) |
| Smaller / larger | Minimum 960 x 480. Larger than 1200 wide gives no benefit: the app decodes at most 1080 px wide |
| Formats | PNG, JPG/JPEG, WebP. Use JPG or WebP (quality 80 to 85) for photos, PNG for flat graphics. No animation (GIF/animated WebP shows one frame) |
| File size | Under **2.5 MB** each; aim for **under 300 KB** so it loads fast on mobile data |
| Colour | sRGB. No transparency needed: a transparent area shows a pale background |
| Count and order | Up to 6; shown in file-name order (`01-…`, `02-…`) unless `banners.json` sets an order |
| Phone orientation | The app is portrait only |

### How big it appears on a phone
The banner is as wide as the screen minus 20 dp of margin on each side, and half as tall as it is wide.

| Phone width | Banner on screen | Pixels on a 3x screen |
|---|---|---|
| 360 dp (small) | 320 x 160 dp | 960 x 480 |
| 393 dp (common) | 353 x 177 dp | 1059 x 530 |
| 412 dp (large) | 372 x 186 dp | 1116 x 558 |

So a 1200 x 600 image is shown at roughly 3.0 to 3.75 pixels per dp: sharp on every phone.

### Safe area (measured on the 1200 x 600 canvas)
- **Corners are rounded** (18 dp, about 70 px): keep important content at least **60 px from the left and right edges and 50 px from the top and bottom** (a centred **1080 x 500 px** safe area).
- **Text size:** at 1200 px wide, use **40 px or larger** for the main message (about 11 dp on a phone) and **28 px or larger** for the second line. Dark text on a light background (or the reverse) with strong contrast.
- **Image shape:** the image is scaled to fill the banner (cropped from the centre if the ratio is not 2:1), so export at exactly 2:1.
- Do not put the app's own buttons, fake "OK / close" icons or anything that looks like a scan result inside a banner.

The two samples in `docs/banners/samples/` follow this layout: text on the left, a round mark on the right, accent bar at the bottom left.

## Optional: `banners.json` (links, text, dates, order)

Without it, banners are shown without a link. To add a link, a description for screen readers, show dates or an order, put a `banners.json` next to the images:

```json
{
  "version": 1,
  "banners": [
    {
      "image": "banner-1-sakura.png",
      "link": "https://example.com/offer",
      "alt": { "en": "Spring offer", "ja": "春のキャンペーン" },
      "start": "2026-12-01",
      "end": "2026-12-31"
    },
    { "image": "banner-2-sky.png", "enabled": false }
  ]
}
```

- `image`: the file name in the same folder (required).
- `link`: must start with `https://`; it opens inside the app. Any other kind of link is ignored.
- `alt`: text read by screen readers, in `en` and `ja` (optional).
- `start` / `end`: `YYYY-MM-DD`, both inclusive (optional).
- `enabled`: `false` hides an image without deleting it.
- Images listed in `banners.json` come first, in its order; other images follow by name.

## How it behaves

- On start the app shows the banners it saved last time, then updates from GitHub in the background. Without a connection it keeps showing the saved banners.
- Only `https` addresses are used. Files that are not real png/jpg/webp images, or are over 2.5 MB, are skipped.
- GitHub's public listing allows about 60 requests per hour per network address; the app asks once per start, so this is not a practical limit.
- The request tells GitHub the phone's network address, as any web request does; the app sends no personal data. The Privacy text in the app says so.
- Banners are advertising. Anything shown must follow the same rule as the rest of the app: **never say or imply that a product is halal or verified**.

## Regenerating the starter banners

`flutter test tool/render_banners_test.dart` (from `app/`, on a Mac with Hiragino fonts) redraws the two samples from the app's own logo code.
