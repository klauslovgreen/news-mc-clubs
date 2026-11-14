# news-mc-clubs

Monaco clubs & associations: membership, fees and upcoming events, read automatically by [news.mc](https://news.mc).

This repository holds one data file, **`clubs.json`**. NEWS.MC reads it by itself, the same way the Emergency box works out the duty pharmacy every day. You don't need to paste anything into WordPress when the club list changes. You only change this file.

Everything in it is already public on the clubs page, so the repository is public. **There are no logins, passwords or keys here, and there never should be.** NEWS.MC logins (WordPress, Kinsta, Mailchimp, GitHub) live in Klaus's password vault.

## How it works

1. `clubs.json` lives here, on the `main` branch.
2. On news.mc, the Code Snippets snippet **"Monaco clubs"** (version v004 or later) downloads the raw file:
   `https://raw.githubusercontent.com/klauslovgreen/news-mc-clubs/main/clubs.json`
3. It keeps that copy for **12 hours**, then downloads it again.
4. It only accepts the file if GitHub answers normally, the JSON is valid, `schema_version` is `1` and the file has a real club list (at least 10 clubs). Otherwise it ignores the file and uses:
   - first, the **last good copy** it downloaded before (saved in WordPress), and
   - if there isn't one, the **built-in copy** inside the snippet itself.
   After a failed download it tries again 30 minutes later.

So a typo or a GitHub outage can never blank the clubs page. The worst case is that the site keeps showing the previous list.

Past events are hidden automatically every day (on the server and in the visitor's browser), so an event never needs to be deleted just because its date has passed.

## What on news.mc uses this file

| Where | What |
|---|---|
| [Clubs page](https://news.mc/monaco-clubs-associations/) | Shortcode `[nmc_clubs]`: the whole page body plus the FAQ data for Google. |
| Homepage sidebar, widget `custom_html-23` | Shortcode `[nmc_club_events]`, the orange "Upcoming Club Events" box (next 7 days). The clubs page uses the same sidebar and moves the box under the top banner. |
| Article sidebar, widget `custom_html-24` "Upcoming Club Events" | Same `[nmc_club_events]` box. |
| [/events/](https://news.mc/events/) page 1 | "Club & association events" cards under UPCOMING EVENTS (`[nmc_club_events_block]`, next 30 days). |
| [/feed/club-events/](https://news.mc/feed/club-events/) | RSS feed of upcoming club events (`?days=8&max=5&pick=1` = the newsletter picks). |
| Sunday Mailchimp email "Sunday Newsletter" | The "Upcoming Club Events" box added to `https://news.mc/?feed=sunday-events`, after "More events on NEWS.MC". 5 picks from the next 8 days. **In a week with no club events the box is left out entirely.** |

When there are no events in the window, the widget, the /events/ block and the Sunday box all disappear on their own.

## Who updates it

- **NEWS.MC SEO Desk** checks the clubs' websites every **Monday at 09:00 (Monaco time)** and, when something changed (a new event, a new fee, a new link), commits the change here with a short message saying what changed.
- **Klaus** can edit it by hand at any time (below).

## Edit by hand on GitHub

1. Open [`clubs.json`](https://github.com/klauslovgreen/news-mc-clubs/blob/main/clubs.json) and click the pencil icon (Edit).
2. Find the club (Ctrl/Cmd + F on its name) and change the text between the quotes. Keep the quotes, commas and brackets exactly as they are.
3. Click **Commit changes**, write what you changed (e.g. "CREM: December event"), and commit to `main`.
4. The site picks it up within 12 hours. To see it straight away, log in to WordPress as admin and open
   `https://news.mc/monaco-clubs-associations/?nmc_clubs_refresh=1`, then clear the **Kinsta site cache** and the **CDN cache** so visitors get the new page.
5. To check which copy the site is using, view the page source of the clubs page and search for `nmc_clubs source=`:
   `remote` or `remote-cache` = this file, `last-good` = an earlier download, `bundled` = the copy built into the snippet (GitHub was unreachable or the file was rejected).

If you break the JSON (a missing comma, say), nothing bad happens on the site: it keeps the last good copy. Fix it or roll back (below). You can paste the file into any JSON validator (e.g. jsonlint.com) before committing.

### What a club looks like

```json
{
  "name": "Rotary Club de Monaco",
  "short_name": "",
  "description": "Chartered in 1937; …",
  "url": "https://www.rotary.mc/",
  "url_label": "www.rotary.mc",
  "next_event": {
    "title": "Statutory meeting and dinner",
    "date": "2026-11-17",
    "end_date": "2026-11-17",
    "date_text": "17 November 2026",
    "time": "20:00–22:30",
    "venue": "Méridien Beach Plaza",
    "note": "",
    "url": "https://www.rotary.mc/evenement/…"
  },
  "fee": { "text": "€25 a year", "url": "https://…" },
  "membership_url": "https://www.rotary.mc/devenir-membre/",
  "notes": "",
  "status": "active"
}
```

- `next_event`: one upcoming event per club, or `null` when there is none. `date` (and the optional `end_date` for multi-day events) must be `YYYY-MM-DD`; `date_text` is what readers see. `time`, `venue` and `note` may be empty `""`.
- `fee`: `null` when no fee is published, otherwise the text shown and a link to the source.
- `status`: `active` or `listed` (only in the official Monaco directory).
- Clubs sit inside `sections` → `subsections` → `blocks` (type `clubs`); blocks of type `p` are plain paragraphs on the page. `faqs` are the questions at the bottom of the page.
- Top level: `schema_version` (leave it at `1`), `updated` (the date of the last change), `source` (a note), and `meta.checked_date` / `meta.events_checked_date` (shown on the page as "checked on"). Never set `meta.checked_date` earlier than `2026-11-14`.

## Roll back

- **The data**: on GitHub open **History** for `clubs.json` (or the repository's Commits), pick the commit that broke things and use **Revert** (or open the earlier version, copy it and commit it as the new content). Then refresh as in step 4 above.
- **The code**: paste the previous snippet version back into the "Monaco clubs" snippet (without its first `<?php` line), then clear the Kinsta and CDN caches. v003e uses only its built-in copy and never contacts GitHub.

The snippet versions and their checksums are kept by the NEWS.MC SEO Desk:

- current: `/workspace/seo/snippets/clubs/nmc-clubs-shortcode-v004-20261114.php` (reads this repository)
- rollback: `/workspace/seo/snippets/clubs/nmc-clubs-shortcode-v003e-20261114.php` (built-in copy only)
- version list: `/workspace/seo/snippets/MANIFEST.md` and `/workspace/seo/snippets/clubs/README.md`
- tests: `/workspace/seo/snippets/clubs/tests/` (`t-v004-check.py`)

(These are paths on the SEO Desk computer, not on the website.)
