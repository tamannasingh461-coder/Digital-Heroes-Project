# Site Audit Tool

A small web tool that audits any URL: fetches the page and reports HTTP
status, response time, title, meta description, H1 count, images missing
`alt` text, and an approximate word count.

- **Backend:** Node.js + Express + Cheerio (`server.js`), single endpoint
  `POST /api/audit`.
- **Frontend:** one static page (`public/index.html`), vanilla JS, no
  build step, styled like a paper inspection report.
- **Failure handling:** invalid URLs, timeouts (10s), non-HTML responses,
  and unreachable hosts all return a clean JSON error with an appropriate
  HTTP status — the server never crashes.

## Run it locally

Requires Node 18+ (uses the built-in global `fetch`).

```bash
npm install
npm start
```

Then open **http://localhost:3000** and enter a URL.

## API

`POST /api/audit`

```json
{ "url": "https://example.com" }
```

**Success (200):**

```json
{
  "url": "https://example.com",
  "httpStatus": 200,
  "responseTimeMs": 184,
  "title": "Example Domain",
  "metaDescription": null,
  "h1Count": 1,
  "imagesTotal": 0,
  "imagesMissingAlt": 0,
  "wordCount": 28
}
```

**Error responses** (all return `{ "error": "..." }`):

| Situation                        | Status |
|-----------------------------------|--------|
| Missing/empty `url`               | 400    |
| Malformed URL or non-http(s)      | 400    |
| Fetch timed out (>10s)            | 504    |
| DNS/connection failure            | 502    |
| Response isn't `text/html`        | 415    |
| Body unreadable / unparsable HTML | 500/502|

## Deploying (free tier)

The app is a single Node service that serves both the API and the static
frontend, so any Node-friendly free host works. **Render** is the
simplest:

1. Push this repo to GitHub (see below).
2. Go to [render.com](https://render.com) → **New +** → **Web Service** →
   connect your GitHub repo.
3. Settings:
   - **Build command:** `npm install`
   - **Start command:** `npm start`
   - **Instance type:** Free
4. Deploy. Render gives you a public URL like
   `https://your-app-name.onrender.com` once the build finishes.

Railway, Fly.io, and Cyclic work the same way (connect repo, build with
`npm install`, run `npm start`). No environment variables are required.

## Publishing the GitHub repo

From inside this project folder:

```bash
git init
git add .
git commit -m "Site audit tool"
git branch -M main
git remote add origin https://github.com/<your-username>/site-audit-tool.git
git push -u origin main
```

(Create the empty repo on GitHub first — `github.com/new` — without a
README, since one is already included here.)

## Notes on testing

This project's core logic (URL validation and the HTML-parsing/report
calculations) was unit-tested against a range of sample pages and edge
cases (missing title, missing meta description, zero/duplicate H1s,
missing `alt` attributes, empty pages). Full end-to-end testing against
live internet URLs should be done after `npm install`, since it pulls in
`express` and `cheerio` from npm.
# Digital-Heroes-Project
