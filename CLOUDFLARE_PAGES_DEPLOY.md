# Cloudflare Pages Deployment Guide

This guide walks you through deploying the Aura-ai Next.js application to Cloudflare Pages using the dashboard.

## Prerequisites

1. **Cloudflare Account** - Create a free account at https://dash.cloudflare.com
2. **GitHub Repository** - Code is already pushed to `Sonnetel/Aura-ai`
3. **Gemini API Key** - `AIzaSyBaM9kAEGqrC-u2BYnSkBYG_V2R7K56z3M` (store this securely)

## Step-by-Step Deployment

### 1. Connect GitHub to Cloudflare
- Visit https://dash.cloudflare.com/pages
- Click **"Create a project"**
- Select **"Connect to Git"**
- Authorize Cloudflare to access your GitHub account
- Select the `Sonnetel/Aura-ai` repository

### 2. Configure Build Settings
When prompted, Cloudflare will auto-detect Next.js as the framework.

- **Production branch**: `main`
- **Build command**: `npm run build`
- **Build output directory**: `.next/standalone`
- **Node.js version**: 20 (or default)

### 3. Set Environment Variables
Add the following environment variables in the Cloudflare Pages project settings:

#### Secrets (hidden from logs):
- `GEMINI_API_KEY`: `AIzaSyBaM9kAEGqrC-u2BYnSkBYG_V2R7K56z3M`

#### Public Variables (Next.js public keys):
- `NEXT_PUBLIC_FIREBASE_API_KEY`
- `NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN`
- `NEXT_PUBLIC_FIREBASE_PROJECT_ID`
- `NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET`
- `NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID`
- `NEXT_PUBLIC_FIREBASE_APP_ID`

(Get these from Firebase console → Project Settings)

### 4. Deploy
Click **"Save and Deploy"**. Cloudflare will:
1. Clone the repository
2. Run `npm install`
3. Run `npm run build`
4. Deploy the `.next/standalone` output
5. Provide a live URL (e.g., `aura-ai-xxx.pages.dev`)

Deployment typically takes 2-5 minutes.

## Testing the Deployment

Once live, test the Gemini API endpoint:

```bash
curl -X POST https://<your-cloudflare-domain>/api/gemini \
  -H "Content-Type: application/json" \
  -d '{"prompt":"Hello, how are you?"}'
```

Expected response:
```json
{
  "text": "Hello! I'm doing well, thank you for asking..."
}
```

## GitHub Actions Auto-Deploy (Optional)

For automatic deployments on every push to `main`:

1. Add Cloudflare API credentials as GitHub Secrets:
   - `CLOUDFLARE_API_TOKEN` - Create at https://dash.cloudflare.com/profile/api-tokens
   - `CLOUDFLARE_ACCOUNT_ID` - Found in Cloudflare dashboard

2. The workflow file `.github/workflows/deploy-cloudflare.yml` will trigger automatically.

## Troubleshooting

### Deployment Fails
- Check build logs in Cloudflare Pages dashboard
- Ensure `npm run build` succeeds locally: `npm run build`
- Verify all environment variables are set

### API Endpoint Returns 500
- Verify `GEMINI_API_KEY` is set in environment variables
- Check Cloudflare Functions logs for errors

### Domain Configuration
To use a custom domain (e.g., `aura.example.com`):
1. In Cloudflare Pages project settings, go to **Custom domains**
2. Add your domain
3. Point DNS records to Cloudflare as instructed

## Environment Variables Reference

```env
# Gemini API (required for /api/gemini endpoint)
GEMINI_API_KEY=AIzaSyBaM9kAEGqrC-u2BYnSkBYG_V2R7K56z3M

# Firebase Configuration (public - safe to commit as NEXT_PUBLIC_*)
NEXT_PUBLIC_FIREBASE_API_KEY=<your-key>
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=<your-domain>
NEXT_PUBLIC_FIREBASE_PROJECT_ID=<your-project>
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=<your-bucket>
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=<your-id>
NEXT_PUBLIC_FIREBASE_APP_ID=<your-app>
```
