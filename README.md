# app-ads.txt Auto-Updater

Automatically keeps your `app-ads.txt` up to date by fetching the latest
entries from Unity, Mintegral, AppLovin, and Yandex on a schedule.
AdMob and Facebook entries are constant and never auto-modified.

## Setup

### 1. Add your publisher IDs

Open `scripts/update-app-ads.js` and replace the placeholder IDs:

```js
const CONSTANT_ENTRIES = {
  admob: [
    "google.com, pub-XXXXXXXXXXXXXXXX, DIRECT, f08c47fec0942fa0",
  ],
  facebook: [
    "facebook.com, XXXXXXXXXXXXXXXXXX, DIRECT",
  ],
};
```

### 2. Configure your refresh interval

Open `.github/workflows/update-app-ads.yml` and edit the cron schedule:

```yaml
- cron: "0 3 * * 1"   # every Monday at 03:00 UTC
```

Common intervals:
| Interval    | Cron expression  |
|-------------|-----------------|
| Every 7 days  | `0 3 * * 1`     |
| Every 3 days  | `0 3 */3 * *`   |
| Every 14 days | `0 3 */14 * *`  |
| Every 30 days | `0 3 1 * *`     |

Use [crontab.guru](https://crontab.guru) to build custom expressions.

### 3. Enable GitHub Pages

In your repo settings → Pages → set source to your main branch, root (`/`).
Your `app-ads.txt` will then be served at `https://yourdomain.com/app-ads.txt`.

### 4. Run it for the first time

Go to **Actions → Update app-ads.txt → Run workflow** to trigger it manually
and verify everything works before the first scheduled run.

## File structure

```
├── .github/
│   └── workflows/
│       └── update-app-ads.yml   # scheduled GitHub Actions workflow
├── scripts/
│   └── update-app-ads.js        # fetcher + merger script (edit this)
├── app-ads.txt                  # auto-generated output (do not edit manually)
└── README.md
```

## How it works

1. GitHub Actions runs on your chosen schedule
2. `update-app-ads.js` fetches the public `app-ads.txt` from each live network
3. Entries are merged with your constant AdMob + Facebook lines
4. The result is committed back to the repo as `app-ads.txt`
5. GitHub Pages serves it at your domain root — no extra deploy step needed

If a network fetch fails, that section is left empty and the workflow logs a warning.
If **all** live networks fail, the workflow exits with an error so you get notified.
