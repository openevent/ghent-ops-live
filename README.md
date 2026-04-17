# Ghent · Ops — deploy guide

A real-time civic dashboard for Ghent, powered by `data.stad.gent`.

## 🚀 Publish in ~5 minutes (no terminal)

### Step 1 — Get the code into StackBlitz

1. Go to **https://stackblitz.com** and click **Sign in** (use GitHub — easiest).
2. Click **"+ Create"** → **"Import from folder"** OR just drag the unzipped `ghent-ops-deploy` folder onto the page.
   - Alternative: click **"+ Create"** → **"Vite + React"**, then replace the files with the ones in this folder.
3. StackBlitz auto-installs and boots. You'll see the dashboard running in the right pane.

### Step 2 — Push it to GitHub (one click)

1. In StackBlitz, click the **GitHub icon** in the left sidebar.
2. Click **"Connect to GitHub"**, then **"Create repository"**.
3. Name it `ghent-ops` and hit create. Done — your code is on GitHub.

### Step 3 — Deploy with Vercel (one click)

1. Go to **https://vercel.com/new** and sign in with the same GitHub account.
2. You'll see your `ghent-ops` repo in the list. Click **"Import"**.
3. Leave every setting as default. Click **"Deploy"**.
4. Wait ~60 seconds. You'll get a live URL like `ghent-ops-yourname.vercel.app`.

**That's it. Share the link.**

Every time you push a change from StackBlitz to GitHub, Vercel redeploys automatically.

---

## What's inside

```
ghent-ops-deploy/
├── index.html              ← page shell
├── package.json            ← dependencies
├── vite.config.js          ← build config
├── public/favicon.svg      ← browser tab icon
├── src/
│   ├── main.jsx            ← React entry
│   └── GhentOps.jsx        ← the dashboard component
└── api/
    └── gent.js             ← serverless CORS proxy (see below)
```

## Why the proxy matters

The dashboard fetches live data from `data.stad.gent`. That API doesn't send
CORS headers, so a browser calling it directly from `ghent-ops.vercel.app`
would get blocked and the dashboard would fall back to sample data.

`api/gent.js` is a tiny serverless function (an "Edge Function") that Vercel
runs for free. When the dashboard requests `/api/gent?dataset=...`, Vercel
proxies it to the real API and sends the JSON back with the right headers.

Bonus: it caches responses at Vercel's edge for 60 seconds, so the upstream
API stays happy even under viral traffic.

## Customising

- **Add a dataset**: add its ID to `ALLOWED_DATASETS` in `api/gent.js`, then
  fetch it from the component with `fetch('/api/gent?dataset=your-id')`.
- **Change the refresh interval**: edit the `5 * 60 * 1000` in the `useEffect`
  inside `GhentOps.jsx` (milliseconds).
- **Custom domain**: in Vercel → Project → Settings → Domains, add your own.

## Local development (optional — needs Node.js)

```bash
npm install
npm run dev       # http://localhost:5173  (proxy won't work locally — use Vercel CLI)
npm run build     # production build
```

For local proxy testing: `npx vercel dev` (requires `npm i -g vercel` first).

---

Built on open data from the city of Ghent · data.stad.gent
