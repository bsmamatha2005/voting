# Fix Render deploy errors (CODEBASE_LEARN / any Node repo)

## Error: `Cannot find module '/opt/render/project/src/src/server.js'`

That path means **Render’s “Root Directory” is already `src`**, but your **Start Command** is still `node src/server.js`.  
Render runs the start command **relative to the root directory**, so it becomes: `src` + `src/server.js` → **`src/src/server.js`** (wrong).

### Fix (pick one)

| Your real file on disk | Render **Root Directory** | Render **Start Command** |
|-------------------------|---------------------------|---------------------------|
| `server.js` at repo root | *(empty / repo root)* | `node server.js` |
| `src/server.js` | *(empty / repo root)* | `node src/server.js` |
| `src/server.js` | `src` | **`node server.js`** ← most common fix for your error |
| `voting_system/server.js` | `voting_system` | `node server.js` |

After changing, **Manual Deploy → Clear build cache & deploy** once.

---

## Error: Node `24.x` (you want LTS)

In Render → **Environment**:

- Add **`NODE_VERSION`** = **`20.18.1`** (or `20`)

Or add a **`.nvmrc`** file at the **repository root** (same folder as `package.json` Render uses) containing:

```text
20.18.1
```

Docs: https://render.com/docs/node-version

---

## Error: `npm install` only installs a few packages / wrong folder

Set **Root Directory** to the folder that contains the **`package.json`** you actually use for this service, then:

- **Build Command:** `npm install` (or `npm ci` if you commit `package-lock.json`)
- **Start Command:** must point at the **entry file inside that same root**

---

## “GitHub project folder not upload” — how to get fixes onto GitHub

1. On your PC, open the project in **Git** (GitHub Desktop, VS Code Source Control, or terminal).
2. **Commit** the changed files, then **Push** to `main` (or your deploy branch).
3. Render will **auto-deploy** from the new commit.

If you only have the ZIP from classmate:

- Create a **new repo** on GitHub → upload files (drag-drop on github.com) **or** use GitHub Desktop “Add existing repository”.

---

## Quick checklist before clicking Deploy

- [ ] **Root Directory** matches where `package.json` lives for this web service  
- [ ] **Start Command** has **no extra** `src/` if Root is already `src`  
- [ ] **`NODE_VERSION`** or **`.nvmrc`** pins Node **20 LTS** (avoid random 24 defaults unless you need it)  
- [ ] If you use **SQLite native** (`sqlite3`), Linux hosts need a **from-source rebuild** or **Docker** (see `README.md` in this workspace for `sqlite3` / GLIBC notes)
