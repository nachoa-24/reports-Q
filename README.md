# Quantia Market Reports

A premium prediction-market research terminal for Polymarket. Next.js 14 + Tailwind. Fully client-side state (localStorage) with **server-side API routes** for Polymarket — no CORS issues.

## ✨ Features

- 🔐 Auth: admin login fixed (`nachoajag@gmail.com` / `.Quantia2002.`), user register/login, simulated Google sign-in
- 📊 Dashboard with KPIs, Trading at, Unrealized ROI, Edge, Kelly sizing
- 📈 Polymarket integration via internal API routes (`/api/polymarket`, `/api/clob`) — frontend never touches `gamma-api.polymarket.com` directly
- ⏱️ Live price refresh every 60 s
- 🧮 Side-aware financial logic (NO 0.62 / FV 0.82 → +20% edge)
- 🎨 Light mode by default + dark mode toggle, premium investment platform aesthetic
- 👤 User profile (name + avatar)
- 📰 Admin Updates feed (publish/read)
- 🥇 Premium / Free reports with gold accents
- 🖼 Image upload via drag, paste, file + 16:9 cropper, stored as base64
- 🎯 Risk profiles (Conservative / Balanced / Aggressive) with Kelly settings editable by admin

## 📁 Structure

```
quantia-market-reports/
├── app/
│   ├── api/
│   │   ├── polymarket/route.ts   # Gamma proxy (server-side)
│   │   └── clob/route.ts         # CLOB price proxy
│   ├── page.tsx                  # main client app
│   ├── layout.tsx
│   └── globals.css
├── components/
│   └── ImageCropper.tsx
├── lib/
│   ├── kelly.ts
│   ├── polymarket.ts
│   ├── storage.ts
│   ├── types.ts
│   └── utils.ts
├── package.json
├── tailwind.config.ts
├── postcss.config.js
├── next.config.js
└── tsconfig.json
```

## 🚀 Deploy to Vercel

### Option A — drag & drop the ZIP

1. Unzip the project locally.
2. Push it to a GitHub repo (or use the Vercel CLI / Vercel UI's "Import Project" → "Upload").
3. On vercel.com → **Add New… → Project** → Import the repo or upload the folder.
4. Framework preset: **Next.js** (auto-detected). No env vars needed.
5. Click **Deploy**.

### Option B — Vercel CLI (fastest)

```bash
npm i -g vercel
cd quantia-market-reports
vercel
```

Follow the prompts. Subsequent deploys: `vercel --prod`.

## 🛠 Local development

```bash
npm install
npm run dev
# open http://localhost:3000
```

## 🧠 How the Polymarket integration works

1. Admin pastes a URL like `https://polymarket.com/event/<event>/<market>`.
2. Browser calls `/api/polymarket?url=...` → **server-side** Edge function.
3. The route extracts both `eventSlug` and `marketSlug`, then cascades through Gamma:
   - `GET /markets/slug/{marketSlug}`
   - `GET /markets?slug={marketSlug}`
   - `GET /events/slug/{eventSlug}`
   - `GET /events?slug={eventSlug}`
4. Returns normalized market + YES/NO `tokenId` + Gamma fallback prices.
5. Frontend then calls `/api/clob?token_yes=...&token_no=...&side=BUY` for live CLOB prices.
6. Every 60 s, the app refreshes all reports' YES/NO live prices.

Because the proxy lives in `/api`, there is **no CORS issue** in the browser.

## 📝 Notes

- Persistence is `localStorage` only for now (per spec). To migrate to a DB, swap `lib/storage.ts`.
- Premium reports show gold styling but are not yet access-controlled — placeholders are in place.
- Admin login is hardcoded; for production replace with real auth.
