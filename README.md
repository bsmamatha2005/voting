# Secure Blockchain Voting System (Demo)

This project is inside the `voting_system/` folder.

## Run (recommended)

```bash
cd voting_system
npm install
npm start
```

Open `http://localhost:3000`.

## Run from the workspace root (optional)

If you are in the workspace root (`voting_system1/`), you can run:

```bash
npm run install:app
npm start
```

## Common fix: "Request failed (405)"

This happens when you open `public/index.html` directly or run a static server (Live Server).

Always run the Node server and open `http://localhost:3000` instead.

## Deploy on Render (fix `GLIBC_2.38` / `node_sqlite3.node` crash)

Render’s runtime often has an **older glibc** than the **prebuilt** `sqlite3` binary. This repo runs `scripts/postinstall.js` on **Linux** (and when `RENDER=true` / `CI=true`) to **`npm rebuild sqlite3 --build-from-source`** so the addon matches the host.

**In the Render dashboard**

1. Set **Node version** to **20.x** (recommended). You can add a Root **Environment** variable:
   - `NODE_VERSION` = `20.18.1`  
   or rely on the included `voting_system/.nvmrc`.
2. Keep the **build** command as `npm install` (postinstall runs automatically).
3. Keep the **start** command as `node server.js` (or `npm start` from `voting_system` if that is your root).

If the rebuild ever fails in the build logs, set **`FORCE_SQLITE3_REBUILD=1`** and redeploy, or switch the service to a **Docker** deploy with `build-essential` installed.

### Monorepo on Render (this repo: `voting_system1/` + `voting_system/`)

- **Option A (recommended):** In the Render service settings, set **Root Directory** to `voting_system`, then **Build** = `npm install`, **Start** = `node server.js`.
- **Option B:** Commit `render.yaml` from this repo and use a **Blueprint** deploy — it already sets `rootDir: voting_system` and `NODE_VERSION=20.18.1`.
- **Option C:** Use **Docker** and point Render at `voting_system/Dockerfile`.

**Do not** add `sqlite3` to the parent `package.json` — the app must install dependencies inside `voting_system/` so `scripts/postinstall.js` runs and rebuilds the native module there.

## Render: `Cannot find module .../src/src/server.js`

Your **Root Directory** and **Start Command** are misaligned. See **[RENDER_DEPLOY.md](./RENDER_DEPLOY.md)** for the exact table and fix.

