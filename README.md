# Bandcamp Collection Tag Scraper + Embed Skimmer

This notebook has two parts:

1) Scrape + filter your Bandcamp collection into a .txt list (by tags)

2) Build a skimmable HTML page that embeds each album/track player + lets you filter/search

---

## PART 1 - Collection → filtered .txt

This notebook uses the bandcamp-fetch Node.js library to read Bandcamp collections and filter releases by tag. It includes an HTML fallback for albums or tracks whose tags are not exposed via metadata.

---

## CONFIGURATION

### 1) Bandcamp session cookies (optional)

Most users do NOT need cookies for bandcamp-fetch (public collections work fine without logging in).

Only add cookies if:

your collection includes private / “fans only” items,

the collection returns empty even though it has items,

or you’re seeing errors that go away when logged in.

To add cookies (OPTIONAL), paste your Bandcamp cookies into the script:

bcfetch.setCookie('PASTE_COOKIE_STRING_HERE');

How to get cookies:

Log into https://bandcamp.com

Open DevTools → Console

Run: document.cookie

Copy/paste the full output

Notes:
Cookies expire (sometimes within hours or a day)
Never share or commit cookies (treat them like a password)

### 2) Bandcamp username

const USERNAME = 'bandcamp_username';

### 3) Target tags

const TARGET_TAGS = [ 'breaks', 'breakbeat'];

Matching behavior:

Case-insensitive

Partial matches are allowed (e.g. "garage" matches "uk garage")

Album metadata is checked first

HTML tag scraping is used as a fallback when metadata is missing

---

## OUTPUT

Writes bandcamp_filtered.txt as repeated blocks:

Title
URL
Tags: tag1, tag2, tag3

---

## RUN ORDER (GOOGLE COLAB)

Install Node.js and dependencies

Edit USERNAME / TARGET_TAGS

Run bandcamp_scraper.js

Confirm bandcamp_filtered.txt exists

If output is empty:

If you enabled cookies, check they’re still valid

Confirm the collection contains releases with relevant tags

Try adding a broad tag (e.g. "electronic") to sanity-check matching

---

## PART 2 - .txt → Embed Skimmer HTML

This step converts your .txt list into a single HTML page of embedded Bandcamp players.

### What it does

Reads bandcamp_filtered.txt

For each URL, extracts the Bandcamp embed id (album or track)

Writes:

bandcamp_items.json (everything we could build an embed for)

bandcamp_failures.json (everything that failed, with a reason)

bandcamp_skimmer.html (the UI you open in your browser)

---

## IMPORTANT NOTES

The skimmer does not need cookies.

Bandcamp will rate-limit if you fetch lots of pages quickly, so the builder uses:

Network limiter (slow, steady requests)

Global cooldown on any HTTP 429 (rate limit)

Retry pass at the end for rate-limited items (after a bigger cooldown)

---

## Using the skimmer page

Open bandcamp_skimmer.html

Scroll down: it will load more players automatically as you scroll (no “Load more” button).

Use the Tag Filter box to show only items that contain a tag string.

Example: type dubstep or uk garage

Failures are shown too (title + URL + reason), so nothing “disappears.”

---

## Troubleshooting

“Some embeds won’t load”: a small % of Bandcamp pages are un-embeddable or removed/expired.

“Many failures after X items”: you’re being rate-limited → increase cooldown + reduce concurrency.

“Last items don’t show”: this is usually a UI batching/render edge case; all links should still appear.

---

## Quick checklist

✅ Have bandcamp_filtered.txt

✅ Run the embed builder

✅ Download/open bandcamp_skimmer.html

✅ Use search + tag filtering
