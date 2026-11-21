# TABEWISE remote config (banners and app version)

This repository holds the remote content for the TABEWISE app. The app fetches it over HTTPS, so changes go live without releasing a new APK.

| File | Purpose |
|---|---|
| `promotion.json` | List of Home screen banners (the swipeable carousel) |
| `offer/` | Banner images referenced by `promotion.json` |
| `version.json` | App version and update-prompt information |

The repository must stay **public** and the content must stay on the **`main`** branch of `tabewise/tabewise-ad`. The app reads the raw files:

- `https://raw.githubusercontent.com/tabewise/tabewise-ad/refs/heads/main/promotion.json`
- `https://raw.githubusercontent.com/tabewise/tabewise-ad/refs/heads/main/version.json`

## 1. Banners (`promotion.json`)

The Home screen shows a carousel built from the entries in `promotion.json`. The app does **not** list the `offer/` folder: an image that is not referenced in `promotion.json` is never shown. To add, change or remove a banner, edit `promotion.json` (and upload the image to `offer/`).

### Schema

```json
[
  {
    "name": "Banner 1",
    "ads": false,
    "banner_url": "https://raw.githubusercontent.com/tabewise/tabewise-ad/refs/heads/main/offer/banner-1-sakura.png",
    "link_url": "https://tabewise.github.io/",
    "start_datetime": "2026-10-01 09:00",
    "expire_datetime": "2027-10-01 00:00"
  },
  {
    "name": "Banner 2",
    "ads": false,
    "banner_url": "https://raw.githubusercontent.com/tabewise/tabewise-ad/refs/heads/main/offer/banner-2-sky.png",
    "link_url": "",
    "start_datetime": "2026-10-01 09:00",
    "expire_datetime": "2027-10-01 00:00"
  }
]
```

| Field | Meaning |
|---|---|
| `name` | Internal label for the banner |
| `ads` | `true` shows a small "Ad" label on the banner; `false` shows none. It does **not** enable or disable the banner |
| `banner_url` | Public HTTPS URL of the image (normally a raw file in `offer/`) |
| `link_url` | Where a tap goes (see below) |
| `start_datetime` | When the banner starts showing, format `YYYY-MM-DD HH:mm` |
| `expire_datetime` | When the banner stops showing, same format |

The array can hold any number of entries and is shown in array order. Keep it to a few banners so the carousel stays quick to browse.

Do not hardcode banner names, image URLs, destinations or dates in the app.

### Banner rules (app behaviour)

**`link_url`**
- Valid non-empty HTTPS URL: the banner is tappable and opens the destination.
- Empty `link_url`: the banner is shown but is not tappable.
- Invalid URL: the banner is shown but is not tappable.
- Only safe HTTP/HTTPS destinations are allowed. Unsupported schemes such as `javascript:` or `file:` are rejected.
- An invalid destination must never stop the image, or other valid banners, from displaying.

**`ads`**
- `false`: no label. `true`: show a small "Ad" label.
- If a banner is paid advertising, set `ads` to `true`. Do not rely on wording drawn inside the image.

**No banners**
- If no banner is active (empty list, or all outside their dates), the carousel does not appear and takes no space.

### Image specification (for designers)

| Item | Value |
|---|---|
| **Canvas** | **1200 x 600 px, ratio 2:1** (the app draws the banner at exactly 2:1) |
| Smaller / larger | Minimum 960 x 480. Larger than 1200 wide gives no benefit: the app decodes at most 1080 px wide |
| Formats | PNG, JPG/JPEG, WebP. Use JPG or WebP (quality 80 to 85) for photos, PNG for flat graphics. No animation (GIF/animated WebP shows one frame) |
| File size | Under **2.5 MB** each; aim for **under 300 KB** so it loads fast on mobile data |
| Colour | sRGB. No transparency needed: a transparent area shows a pale background |
| Phone orientation | The app is portrait only |

The two current banners (`banner-1-sakura.png`, `banner-2-sky.png`) are about 0.8 MB each: within the hard limit, but above the 300 KB target.

#### How big it appears on a phone

The banner is as wide as the screen minus 20 dp of margin on each side, and half as tall as it is wide.

| Phone width | Banner on screen | Pixels on a 3x screen |
|---|---|---|
| 360 dp (small) | 320 x 160 dp | 960 x 480 |
| 393 dp (common) | 353 x 177 dp | 1059 x 530 |
| 412 dp (large) | 372 x 186 dp | 1116 x 558 |

A 1200 x 600 image is shown at roughly 3.0 to 3.75 pixels per dp: sharp on every phone.

#### Safe area (measured on the 1200 x 600 canvas)
- **Corners are rounded** (18 dp, about 70 px): keep important content at least **60 px from the left and right edges and 50 px from the top and bottom** (a centred **1080 x 500 px** safe area).
- **Text size:** use **40 px or larger** for the main message (about 11 dp on a phone) and **28 px or larger** for the second line. Use strong contrast (dark on light, or the reverse).
- **Image shape:** the image is scaled to fill the banner (cropped from the centre if the ratio is not 2:1), so export at exactly 2:1.
- Do not put the app's own buttons, fake "OK / close" icons or anything that looks like a scan result inside a banner.

### How to add or change a banner

1. Export the image at 1200 x 600 and upload it to `offer/` on `main`.
2. Add or edit an entry in `promotion.json` with its raw URL (`.../refs/heads/main/offer/<file>`), link, `ads` flag and dates.
3. Commit to `main`. The app picks it up on its next fetch.

## 2. App version (`version.json`)

Used by the app to decide whether to show an update prompt.

| Field | Meaning |
|---|---|
| `latest_version` | Newest released app version |
| `minimum_supported_version` | Oldest version still allowed to run |
| `update_available` | `true` to show the update prompt |
| `force_update` | `true` to block use until the user updates |
| `release_date` | Release date of `latest_version` |
| `update_title` | Title of the update prompt |
| `update_message` / `update_message_ja` | Prompt text in English / Japanese |
| `android_store_url` / `ios_store_url` | Store pages the update button opens |

Before setting `update_available` or `force_update` to `true`, fill in the store URLs; otherwise the prompt has nowhere to send users.
