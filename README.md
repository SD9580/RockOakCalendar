# Rock Oak Events — TV Schedule

A live weekly schedule for the Rock Oak Events Google Calendar, designed for TV display. Hosted on GitHub Pages, pulls directly from Google Calendar every 15 minutes. No server, no GitHub Actions, no computer needs to stay on.

---

## Setup walkthrough

You'll do five things, in order. Total time: roughly 15 minutes.

1. Make the Rock Oak Events calendar publicly viewable
2. Get the calendar's ID
3. Create a Google Cloud API key
4. Create a GitHub repo and paste the two values into `index.html`
5. Turn on GitHub Pages and point your TV at the URL

---

### 1. Make the calendar public

Open Google Calendar in your browser.

In the left sidebar, find **Rock Oak Events**, hover over it, click the three-dot menu, and choose **Settings and sharing**.

Scroll to **Access permissions for events**. Check **Make available to public**. In the dropdown next to it, leave it set to **See all event details** (the page needs full event details to render titles and times). Google will warn you the calendar will be visible to anyone with the link — that's expected.

Click **OK** on the confirmation. Leave this settings page open; you'll need the next field.

### 2. Copy the calendar ID

Still on the same settings page, scroll down to **Integrate calendar**.

Find **Calendar ID**. It will look like one of these:

- `something-long@group.calendar.google.com` (most common for shared calendars)
- `your-email@gmail.com` (if you're using your primary calendar)

Copy that whole string. Paste it somewhere you won't lose it.

### 3. Create a Google Cloud API key

Go to https://console.cloud.google.com/. Sign in with the same Google account.

If you've never used Google Cloud, it'll ask you to accept the terms — do that. You don't need to enable billing for this; the Calendar API has a generous free tier and will not charge you.

Create a project (top-left dropdown → **New Project**). Name it something like `rock-oak-calendar`. Wait a few seconds for it to be created, then make sure it's selected in that top-left dropdown.

In the search bar at the top, type **Google Calendar API** and click the result. Click **Enable**.

In the left sidebar, click **APIs & Services** → **Credentials**. At the top, click **+ Create credentials** → **API key**. A key will be shown — copy it. Paste it next to your calendar ID from step 2.

**Restrict the key (important).** Click **Edit API key** (or the pencil icon next to the key you just made).

- Under **Application restrictions**, choose **Websites**, then **Add** and enter the GitHub Pages URL you'll create in step 5. (You don't have it yet — for now, set it to `*` and come back to tighten this after step 5. Or skip this and tighten later.)
- Under **API restrictions**, choose **Restrict key**, then in the dropdown select only **Google Calendar API**.

Click **Save**.

### 4. Create the GitHub repo

Go to https://github.com and create a new repository. Name it whatever you like — `rock-oak-schedule` is a sensible default. Make it **Public** (required for free GitHub Pages on a personal account). Don't bother with a README; we have files to upload.

Once the empty repo is created, click **uploading an existing file** on the welcome screen (or **Add file** → **Upload files**). Drag in both `index.html` and this `README.md`.

Before you commit: open `index.html` either locally or via GitHub's web editor and find this block near the top of the `<script>`:

```js
const CONFIG = {
  CALENDAR_ID: 'YOUR_CALENDAR_ID_HERE',
  API_KEY: 'YOUR_API_KEY_HERE',
  ...
};
```

Replace `YOUR_CALENDAR_ID_HERE` with the calendar ID from step 2 and `YOUR_API_KEY_HERE` with the API key from step 3. Keep the quotes around them.

Commit the file ("Commit changes" button at the bottom of GitHub's editor).

> **About the API key being in public source code:** This is fine for this use case because (a) the key is restricted to only the Calendar API, (b) Google's free tier covers far more requests than a TV display will ever make, and (c) the calendar itself is already public. The risk of someone abusing the key is small and capped. If you want, also restrict the key by HTTP referrer to your GitHub Pages URL (step 3 last paragraph).

### 5. Turn on GitHub Pages

In your repo, go to **Settings** → **Pages** (left sidebar).

Under **Source**, choose **Deploy from a branch**. Under **Branch**, pick `main` and `/ (root)`, then click **Save**.

Wait 1–2 minutes. Refresh the Pages settings page — you'll see a green box with your URL, something like:

```
https://YOUR_GITHUB_USERNAME.github.io/rock-oak-schedule/
```

Open that URL. You should see your calendar.

If you see "Failed to load schedule," check the browser console (F12) for the exact error. Almost always it's one of:

- The calendar ID is wrong (typo, trailing whitespace)
- The API key is wrong or not enabled for Calendar API
- The calendar isn't actually public — go back to step 1 and verify
- You restricted the API key to a different domain

Once it works, go back to step 3 and tighten the API key restriction to your real GitHub Pages URL.

### Point your TV at it

On the TV's browser (or whatever device drives the TV — Roku, Fire Stick, Chromecast, an old laptop, whatever), navigate to that GitHub Pages URL and put the browser into fullscreen.

The page auto-refreshes the data every 15 minutes and reloads itself entirely every 15 minutes (via the `<meta http-equiv="refresh">` tag) for safety. If you want a different refresh cadence, edit `REFRESH_INTERVAL` in `index.html`.

---

## Customizing

All visual tweaks live in `index.html`:

- **Time range:** change `HOUR_START` and `HOUR_END` in the CONFIG block (currently 7am–11pm).
- **Refresh interval:** change `REFRESH_INTERVAL` (in milliseconds).
- **Title:** change the `<h1>` text in the header.
- **Subtitle:** change the `<span class="subtitle">` text.
- **Event color:** the warm oak/amber is set as `#d4a574` and `rgba(212, 165, 116, ...)` — search for those values and replace with your preferred color.
- **Today highlight:** also `#d4a574` — same find-and-replace works.

To deploy a change, edit the file (locally or in GitHub's web editor), commit, and GitHub Pages will redeploy in ~30 seconds.
