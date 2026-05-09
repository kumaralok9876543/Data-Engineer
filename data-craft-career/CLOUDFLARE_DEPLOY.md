# Cloudflare Deployment Guide

## Quick Start - Deploy from GitHub

### Step 1: Connect GitHub to Cloudflare (One-time Setup)

1. Go to **Cloudflare Dashboard**: https://dash.cloudflare.com
2. Select **Workers & Pages**
3. Click **Create → Pages**
4. Click **Connect to Git**
5. Authorize Cloudflare to access your GitHub
6. Select your GitHub repository: `kumaralok9876543/data-craft-career`
7. Click **Begin setup**

### Step 2: Configure Build Settings

In the Cloudflare Pages setup:

```
Production Branch: main
Build Command: npm install && npm run build
Build Output Directory: dist/server
Root Directory: /
```

### Step 3: Set Environment Variables

In your Cloudflare Pages project settings → Environment variables, add:

```
SUPABASE_URL=<your-supabase-url>
SUPABASE_PUBLISHABLE_KEY=<your-public-key>
VITE_SUPABASE_URL=<your-supabase-url>
VITE_SUPABASE_PUBLISHABLE_KEY=<your-public-key>
```

⚠️ **IMPORTANT**: These are PRODUCTION secrets - use production Supabase project keys

### Step 4: Save & Deploy

- Click **Save and Deploy**
- Cloudflare automatically deploys on every push to `main` branch
- Your app will be live at: `https://<project-name>.pages.dev`

---

## Automated Deployment with GitHub Actions (Optional)

If you want more control, use GitHub Actions:

### Create `.github/workflows/deploy.yml`

```yaml
name: Deploy to Cloudflare

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build
        run: npm run build
      
      - name: Deploy to Cloudflare
        uses: cloudflare/wrangler-action@v3
        with:
          apiToken: ${{ secrets.CLOUDFLARE_API_TOKEN }}
          accountId: ${{ secrets.CLOUDFLARE_ACCOUNT_ID }}
          secrets: |
            SUPABASE_URL
            SUPABASE_PUBLISHABLE_KEY
            VITE_SUPABASE_URL
            VITE_SUPABASE_PUBLISHABLE_KEY
```

### Add GitHub Secrets

Go to GitHub repo → Settings → Secrets and variables → Actions:

- `CLOUDFLARE_API_TOKEN`: Get from https://dash.cloudflare.com/profile/api-tokens
- `CLOUDFLARE_ACCOUNT_ID`: From Cloudflare Dashboard
- `SUPABASE_URL`: Your Supabase URL
- `SUPABASE_PUBLISHABLE_KEY`: Your Supabase public key
- `VITE_SUPABASE_URL`: Same as SUPABASE_URL
- `VITE_SUPABASE_PUBLISHABLE_KEY`: Same as SUPABASE_PUBLISHABLE_KEY

---

## Monitoring & Logs

View your deployments:
1. Go to Cloudflare Dashboard → Workers & Pages
2. Select your project
3. View real-time logs and deployment status
4. Check Tail logs for errors

---

## Custom Domain (Optional)

1. In Cloudflare Pages project settings
2. Go to **Custom domain**
3. Add your domain (e.g., data-craft-career.com)
4. Follow DNS instructions

---

## Troubleshooting

### Build fails: "Cannot find wrangler"
```bash
npm install -g wrangler
```

### "SUPABASE_URL not found"
Verify environment variables are set in Cloudflare dashboard.

### Deploy fails after push
Check GitHub Actions logs in your repo → Actions tab

### Site not loading
Check Cloudflare Tail logs for errors during runtime
