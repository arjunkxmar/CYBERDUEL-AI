# CyberDuel AI — Render Frontend Deployment Guide

This guide details how to deploy the **CyberDuel AI Command Center Dashboard** (`apps/dashboard`) on [Render](https://render.com) easily as a high-performance **Static Site** (100% free with SSL and global CDN).

---

## Option 1: Automatic Blueprint Deployment (Easiest)

This repository includes a pre-configured `render.yaml` file that sets up everything automatically.

1. Go to [Render Dashboard](https://dashboard.render.com/).
2. Click **New +** -> **Blueprint**.
3. Connect your GitHub repository (`CYBERDUEL-AI`).
4. Render will detect `render.yaml` and show:
   - `cyberduel-dashboard` (Static Site)
   - *(Optional)* `cyberduel-api` (Python Web Service)
5. Under Environment Variables for `cyberduel-dashboard`, set:
   - `VITE_API_URL`: Your backend API URL (e.g. `https://cyberduel-api.onrender.com` or Railway URL).
6. Click **Apply**. Render will build and deploy your frontend!

---

## Option 2: Manual Static Site Setup on Render

If you prefer to configure it manually in the Render UI:

### Step 1: Create a Static Site
1. Log in to [dashboard.render.com](https://dashboard.render.com/).
2. Click **New +** -> **Static Site**.
3. Select your repository.

### Step 2: Configure Build Settings
Fill in the following fields:

| Field | Value | Notes |
| :--- | :--- | :--- |
| **Name** | `cyberduel-dashboard` | Any name you prefer |
| **Branch** | `main` | Your active branch |
| **Root Directory** | `apps/dashboard` | **Important:** Tells Render where the Vite app is |
| **Build Command** | `npm install && npm run build` | Builds React app with TypeScript & Vite |
| **Publish Directory** | `dist` | Render serves the compiled assets from here |

### Step 3: Configure Environment Variables
Under the **Environment Variables** section, click **Add Environment Variable**:

- **Key**: `VITE_API_URL`
- **Value**: `https://<your-backend-api-url>` (e.g., `https://cyberduel-api.onrender.com` or Railway URL)

*(Note: If you leave this empty, the frontend defaults to relative paths `/api/v1`, which is ideal if you use a proxy).*

### Step 4: Configure Redirects/Rewrites (Prevents 404 on Refresh)
Because the dashboard is a Single Page Application (React Router):

1. Go to the **Redirects/Rewrites** tab in your Render static site settings.
2. Click **Add Rule**:
   - **Type**: `Rewrite`
   - **Source**: `/*`
   - **Destination**: `/index.html`
3. Click **Save Changes**.

*(This is also pre-configured in `render.yaml` if using Blueprints).*

### Step 5: Deploy
Click **Create Static Site**. Render will run:
```bash
cd apps/dashboard
npm install
npm run build
```
Once deployed, your live URL will look like:
`https://cyberduel-dashboard.onrender.com`

---

## Frontend Environment Variables Reference

| Variable | Required | Description | Example |
| :--- | :---: | :--- | :--- |
| `VITE_API_URL` | Yes (for external API) | Full base URL of your FastAPI backend service | `https://cyberduel-api.onrender.com` |
| `VITE_BACKEND_URL` | Optional | Fallback alternative to `VITE_API_URL` | `https://cyberduel-api.onrender.com` |

---

## Troubleshooting & Verification

1. **Routing & 404 Errors**: If navigating directly to `/lab` or `/system` gives a 404, verify that the Rewrite rule `/*` -> `/index.html` is added under **Redirects/Rewrites**.
2. **CORS Errors**: In your backend environment (on Render or Railway), ensure `CORS_ORIGINS` includes your frontend domain:
   ```env
   CORS_ORIGINS=https://cyberduel-dashboard.onrender.com,http://localhost:5173
   ```
3. **Verify API Connection**: Open DevTools in your browser (`F12` -> Network tab) and verify calls are going to `https://<your-backend-url>/api/v1/health` or `overview`.
