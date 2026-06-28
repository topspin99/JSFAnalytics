# Site Dashboard

A two-site Google Analytics (GA4) dashboard. Sign in with Google, see active
users, sessions, bounce rate, top channels/pages/devices for both sites side
by side.

## 1. Local setup

```bash
npm install
cp .env.local.example .env.local
```

Open `.env.local` and fill in:

- `GOOGLE_CLIENT_ID` / `GOOGLE_CLIENT_SECRET` — from Google Cloud Console →
  APIs & Services → Credentials.
- `NEXTAUTH_SECRET` — generate one with `openssl rand -base64 32`.
- `GA_PROPERTY_ID_1` / `GA_PROPERTY_ID_2` — already filled in with your two
  property IDs (383895604, 504003265). Edit the labels if you want nicer
  names than "Site One" / "Site Two".

Also add these two **public** copies so the frontend knows which property
IDs to request (safe to expose — they're just numeric IDs, not secrets):

```
NEXT_PUBLIC_GA_PROPERTY_ID_1=383895604
NEXT_PUBLIC_GA_PROPERTY_ID_2=504003265
```

Then run:

```bash
npm run dev
```

Visit `http://localhost:3000`.

## 2. Google Cloud checklist

Make sure in Google Cloud Console:

- **Google Analytics Data API** is enabled on your project.
- OAuth consent screen has your email added as a **test user** (while the
  app is unpublished/in testing mode).
- OAuth client's **Authorized redirect URIs** includes:
  `http://localhost:3000/api/auth/callback/google`

The Google account you sign in with in the app must itself have **Viewer**
access (or higher) on both GA4 properties — this app uses your own login's
permissions, it doesn't use a separate service account.

## 3. Deploying to Vercel

1. Push this folder to a GitHub repo.
2. Import it in Vercel.
3. Add all the same environment variables from `.env.local` in the Vercel
   project settings (Settings → Environment Variables).
4. Set `NEXTAUTH_URL` to your real deployed URL, e.g.
   `https://your-app.vercel.app`.
5. Back in Google Cloud Console, add a second Authorized redirect URI:
   `https://your-app.vercel.app/api/auth/callback/google`
6. Redeploy.

## Notes

- Nothing here uses a Google service account — it reads Analytics data
  using the signed-in user's own OAuth token, scoped to read-only access
  (`analytics.readonly`).
- Property IDs are not secret. Client ID/Secret and NEXTAUTH_SECRET are —
  keep them only in `.env.local` / your hosting provider's env settings,
  never in source control or chat.
