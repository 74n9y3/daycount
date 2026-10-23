# DayCount

**English** | [中文](README.zh-CN.md)

<div align="center">
<h3>☕ Buy me a coffee</h3>
<p>This project is free. If it helped you remember a day that matters, a <strong>USDT</strong> tip on the matching network is welcome.<br/>
<sub>Send on the network below. The wrong chain will not arrive.</sub></p>
<p>
  <img alt="BSC USDT" src="https://img.shields.io/badge/BSC-USDT%20BEP--20-F3BA2F?style=for-the-badge&logo=binance&logoColor=white" />
  <img alt="Solana USDT" src="https://img.shields.io/badge/Solana-USDT-9945FF?style=for-the-badge&logo=solana&logoColor=white" />
  <img alt="Tron USDT" src="https://img.shields.io/badge/Tron-USDT%20TRC--20-FF060A?style=for-the-badge&logo=tron&logoColor=white" />
</p>

| Network | Address |
|:-----|:---------|
| ![BSC](https://img.shields.io/badge/BSC-F3BA2F?style=flat-square&logo=binance&logoColor=white) **USDT · BEP-20** | `0x6E7359A42C466893E9A835fD6aFdb82E0D1Ed32d` |
| ![Solana](https://img.shields.io/badge/Solana-9945FF?style=flat-square&logo=solana&logoColor=white) **USDT** | `Hm7f6wGdvcmLmbcXV7sdLYGJKBdwGoFrJTZPzeN8dpan` |
| ![Tron](https://img.shields.io/badge/Tron-FF060A?style=flat-square&logo=tron&logoColor=white) **USDT · TRC-20** | `TGNrpZBqD8jnfNCb7B9W93qMmVHycoFsKJ` |
</div>

A small Chrome extension for anniversaries, countdown days, and the dates you do not want to miss. You already open the browser every workday, so the important days stay in sight.

[Chrome Web Store](https://chromewebstore.google.com/detail/glilceccaiicdbjgmelhlbdbiebmnmeh)

## Features

- **Countdown vs count-up is automatic**: no mode to pick. A future date counts down (“N days left”); a past date with no repeat counts up (“N days since”) for birthdays, start dates, anniversaries. Repeating events count down to the next occurrence and show which cycle / which anniversary, plus days since the original date
- **Flexible repeats**: every N days / weeks / months / years (every 2 weeks, every 3 months, …). Lunar events follow the lunar month / year. Feb 29, short months, and leap months are handled
- **Display formats**: days; weeks + days; months + days; years + months + days (e.g. “6 years 6 months 15 days”), split on the calendar
- **Solar / lunar**: lunar birthdays and festivals (1900–2100, including leap months), converted to the matching solar date each year
- **Tags**: Anniversary, Life, Work, Study, Holiday, Other, or a custom tag; the list can be filtered by tag
- **Due reminders**: day-based, not a meeting alarm. Countdown events can remind on the day or N days / weeks / months before. Past events can remind every N days / weeks / months / years. Notifications fire at the daily reminder time in Settings; if the browser was closed, they catch up on the next launch
- **Pin and badge**: one pinned event takes the top card and the toolbar icon badge, so the number is visible every day
- **Local only**: data stays in `chrome.storage.local` on this device; no server. Export / import JSON for backup and moving machines
- **Chinese / English UI**: follows the browser by default; switch with **中 / EN** in the popup, or lock it in Settings. Notifications, badge text, and lunar labels switch together (English lunar looks like `Lunar 8/15`, `Bing-Wu (Horse)`)

## Install (unpacked, developer mode)

1. Open Chrome at `chrome://extensions/`
2. Turn on **Developer mode**
3. Click **Load unpacked** and select this folder
4. Pin **DayCount** on the toolbar (puzzle icon → pin) so the badge stays visible

## Usage

- Click the toolbar icon for the list; **New** adds an event; click any event to edit or delete
- Clicking outside the popup closes it (Chrome’s normal popup behavior); click the icon again to reopen
- **中 / EN** in the top-right switches the UI language
- The gear opens **Settings & backup** in a new tab: language, daily reminder time, badge, whether count-up includes the start day, export / import / clear, and optional USDT tip addresses
- Export is JSON you can open in any text editor; import can **merge** or **replace**

## Layout

```
daycount/
├── manifest.json        # MV3 manifest
├── background.js        # Service worker: daily reminders, notifications, badge
├── popup/               # Popup: list + create/edit
├── options/             # Settings and backup
├── src/
│   ├── lunar.js         # Lunar ↔ solar (1900–2100 table)
│   ├── dates.js         # Date helpers, countdown/count-up, repeats, Y/M/D split
│   ├── store.js         # Local storage, model, import/export
│   ├── i18n.js          # UI copy (zh / en)
│   └── theme.css        # Shared styles (dark mode)
├── _locales/            # Store name and description (follows browser language)
└── icons/
```

## Data format

Example export:

```json
{
  "app": "daycount",
  "version": 2,
  "exportedAt": "2026-09-17T01:00:00.000Z",
  "settings": { "language": "auto", "remindTime": "09:00", "includeToday": false, "badge": "top" },
  "events": [
    {
      "id": "…",
      "title": "Started the job",
      "calendar": "solar",
      "solar": { "y": 2020, "m": 3, "d": 2 },
      "date": "2020-03-02",
      "repeat": { "unit": "none", "every": 1 },
      "display": "years",
      "tag": "工作",
      "remind": { "enabled": true, "before": [], "every": [{ "n": 100, "unit": "day" }, { "n": 1, "unit": "year" }] },
      "pinned": true,
      "note": ""
    },
    {
      "id": "…",
      "title": "Mom's birthday",
      "calendar": "lunar",
      "lunar": { "year": 1965, "month": 8, "day": 15, "isLeap": false },
      "repeat": { "unit": "year", "every": 1 },
      "display": "days",
      "tag": "纪念日",
      "remind": { "enabled": true, "before": [{ "n": 0, "unit": "day" }, { "n": 1, "unit": "week" }], "every": [] },
      "pinned": false,
      "note": ""
    }
  ]
}
```

- `repeat.unit`: `none` / `day` / `week` / `month` / `year`; `every` is the interval. Legacy strings `yearly` / `monthly` convert on import
- `display`: `days` / `weeks` / `months` / `years`
- `remind.before`: countdown rules `{ n, unit }` with `day` / `week` / `month`; `n = 0` is the day itself. `remind.every`: count-up “every N units” with `day` / `week` / `month` / `year`. Legacy `days` / `hundreds` and hour/minute/second rules convert on import (h/m/s become “on the day”)
- `calendar`: `solar` uses `solar` (or a `date` string); `lunar` uses `lunar`
- Legacy `type` is ignored (countdown vs count-up is derived from the date and repeat)
- Invalid records are skipped on import

## Permissions

- `storage`: save data on this device
- `alarms`: run the daily reminder check
- `notifications`: system notifications

No site-access permissions. No network.
