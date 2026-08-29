# PlanetScale provisioning and import steps for STOKKO

Follow these steps locally to create a PlanetScale database and import the existing MySQL schema `backend/db/stokko_db.sql`.

1) Install PlanetScale CLI (macOS / Homebrew)

```bash
brew install planetscale/tap/pscale
pscale auth login
```

2) Create the database (you can also use the PlanetScale UI)

```bash
# in UI: create database named `stokko`
# or via CLI (if supported):
# pscale database create stokko
```

3) Create a development branch and a password for remote imports

```bash
pscale branch create stokko main
pscale password create stokko import-user
# copy the generated username and password shown by the CLI
```

4) Open a local tunnel and import the SQL schema

```bash
# opens a local tunnel to PlanetScale on port 3306 (leave running)
pscale connect stokko main --port 3306

# in another terminal, import the SQL file
mysql -u <USERNAME_FROM_PASSWORD_CREATE> -p -h 127.0.0.1 -P 3306 < backend/db/stokko_db.sql
# enter the password you copied when prompted
```

Notes:
- For serverless deployments (Vercel) use PlanetScale Data Proxy (recommended) to avoid connection limits.
- To use Data Proxy: enable it in the PlanetScale UI for your database and note the provided connection string.

5) Environment variables to set in Vercel (Production)

Set these in the Vercel Dashboard for your project (Settings → Environment Variables):

- `DB_HOST` — when using Data Proxy use the proxy host; otherwise use the host value from pscale connect or PlanetScale UI
- `DB_PORT` — usually `3306`
- `DB_NAME` — `stokko`
- `DB_USER` — the username created by `pscale password create`
- `DB_PASS` — the password created by `pscale password create`
- `DB_SSL` — `true` (use TLS for PlanetScale)
- `JWT_SECRET`, `SESSION_SECRET`, `CLOUDINARY_CLOUD_NAME`, `CLOUDINARY_API_KEY`, `CLOUDINARY_API_SECRET`, `CLIENT_URL`

6) Quick verification

- Health endpoint: `https://<your-vercel-domain>/api/health`
- Products: `https://<your-vercel-domain>/api/products`

If you want, I can add a small README section into `README.md` or commit these steps into the repo as I did here. Run the commands locally — I can't create the PlanetScale account or run `pscale auth login` for you.
