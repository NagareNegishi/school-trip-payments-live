# Render deploy guide

The database is on Neon; Render hosts the backend and the frontend. Do the steps in order.

## Before you start

1. Push this code to a GitHub repo, branch `main`.
2. Sign in at dashboard.render.com and connect GitHub (Account Settings > Account Security > Git Deployment Credentials). Grant access to the repo.
3. Create a Neon account at console.neon.tech/signup. The Free plan needs no card.

## 1. Database (Neon)

1. Click **New Project**. Name it `school-trips`, pick a region, click **Create project**.
2. Click **Connect**, select the branch, database and role, and turn **Connection pooling** off to get the direct string (no `-pooler` in the host). The backend runs its migrations on start, and Neon says to use a direct connection for migrations.
3. Copy the string. It looks like `postgresql://USER:PASSWORD@HOST/DATABASE?sslmode=require&channel_binding=require`.
4. Edit it: remove `&channel_binding=require`, then change `sslmode=require` to `ssl=require`. asyncpg does not accept the other two options, and the backend fails to start with them.
5. Keep the string out of chat, docs and git. It only goes into Render's `DATABASE_URL`.
6. Create no tables. The backend creates them on first start.

## 2. Backend (web service)

1. Click **+ New** > **Web Service**. Select the repo and click **Connect**.
2. Fill in the form:
   - **Name**: `school-trips-api`
   - **Language**: Python
   - **Branch**: `main`
   - **Root Directory**: `backend`
   - **Build Command**: `uv sync`
   - **Start Command**: `uv run uvicorn app.main:app --host 0.0.0.0 --port $PORT`
   - **Compute**: **Free**
3. Under **Advanced**, add these environment variables:

   | Key | Value |
   |---|---|
   | `PYTHON_VERSION` | `3.14.8` |
   | `DATABASE_URL` | the edited Neon URL from step 1 |
   | `FRONTEND_ORIGIN` | `https://placeholder.invalid` (replaced in step 4) |
   | `DEMO_RESET_ENABLED` | `true` only to keep `POST /demo/reset` (no authentication); otherwise leave it out |

4. Create the service and wait for **Live** on the **Deploys** page. If it fails, read the log.
5. Copy the service's `onrender.com` URL.
6. Open `https://<backend-url>/parents`. It must return three parents. The first request can take a minute while the service wakes.

## 3. Frontend (static site)

1. Click **+ New** > **Static Site**. Select the same repo and click **Connect**.
2. Fill in the form:
   - **Name**: `school-trips-web`
   - **Branch**: `main`
   - **Root Directory**: `frontend`, if the form shows it
   - **Build Command**: `npm install && npm run build`
   - **Publish Directory**: `dist`
3. Add the environment variable `VITE_API_URL` = the backend URL, no trailing slash.
4. Create the site, wait for **Live**, and copy its URL.
5. If the form had no Root Directory field, the first build fails. Open **Settings** > **Build & Deploy** > **Root Directory**, enter `frontend`, save, then run **Manual Deploy** > **Deploy latest commit**.

## 4. Connect them

1. Open the backend service > **Environment**.
2. Set `FRONTEND_ORIGIN` to the static site URL, no trailing slash.
3. Click **Save and deploy** and wait for **Live**.

## 5. Check it works

1. Open the static site. The trip loads and the parent dropdown has three names.
2. Pick a parent, register a child and pay. About 10% of payments are declined at random, so retry.
3. A successful payment ends in a receipt, and the registration leaves the unpaid list.
4. In the backend **Logs**, each attempt shows as an `app.payments` line with no card number, expiry or CVV.
5. A wrong card number shows an error on the field.

## 6. Before a demo

1. The free backend sleeps after 15 minutes idle. Open `https://<backend-url>/parents` a minute or two before you start.
2. When no trips show, call `POST /demo/reset` (`<backend-url>/docs`, expand it, **Try it out**, **Execute**). It returns 404 unless `DEMO_RESET_ENABLED` is set.

## Changing `VITE_API_URL`

Open the static site > **Environment**, edit the value and click **Save, rebuild, and deploy**. The value is baked in at build time.
