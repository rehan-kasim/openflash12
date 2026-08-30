# OpenFlash 

A paper-crafted interactive creation portal — vector games, interactive stories and chiptune experiments, built with React, TypeScript and HTML5 Canvas. Create in the Studio, ship to the Arcade, and get paid in Bitcoin, Ethereum or Solana.

![theme](https://img.shields.io/badge/theme-paper%20blue%20%26%20amber-FEA619)
![react](https://img.shields.io/badge/react-18-blue)
![vite](https://img.shields.io/badge/vite-5-blue)
![deploy](https://img.shields.io/badge/deploy-Netlify%20one--click-brightgreen)

## What's inside

- **Studio** — vector drawing tools, multi-layer timeline animation, a TypeScript sandbox runtime with physics, particles and collision, and a chiptune synth. Run your code live, export offline HTML, and publish to the arcade.
- **Arcade** — three original playable games seeded as the OpenFlash catalog:
  - **Grid Runner** — gravity, coyote time and dash chaining.
  - **Nova Drift** — endless drift with mines and a score multiplier.
  - **Orbit Painter** — paint the grid by orbiting.
- **Crypto checkout** — sell and buy games with BTC / ETH / SOL. Payments are monitored directly on-chain (Blockstream Esplora, Etherscan v2, Solana RPC), amounts are quoted from live rates with a configurable tolerance, and every paid order settles idempotently into creator revenue. No third-party payment processor.
- **Creator monetization** — Beta / Sigma / Alpha plans with revenue splits, an earnings ledger, withdrawals, and a leaderboard per game.
- **Security** — untrusted game code runs in an opaque-origin sandboxed iframe (`sandbox="allow-scripts"`, no same-origin privileges), the studio sandbox bounds execution time, and production ships a strict CSP without inline scripts.

## Quick start

```bash
git clone https://github.com/Unseen094/OpenFlash.git
cd OpenFlash
npm install
npm run dev
# → http://localhost:3000
```

Out of the box the app runs in guest mode — sign-in, Studio and Hub all work locally without any configuration.

### Production build

```bash
npm run build     # type-check + bundle → dist/
npm run preview
```

## Deploy to Netlify

The repo ships with `netlify.toml` (SPA redirects, strict security headers, immutable asset caching). Deploy in two ways:

1. **Netlify CLI**

   ```bash
   npm install -g netlify-cli
   netlify deploy --prod --dir=dist
   ```

2. **Dashboard** — connect the GitHub repo in Netlify and set:
   - Build command: `npm run build`
   - Publish directory: `dist`

### Environment variables

Copy `.env.example` and fill in what you need. All variables are optional — the app **fails closed** when payment configuration is missing.

| Variable | Purpose |
| --- | --- |
| `VITE_FIREBASE_*` | Firebase auth (blank = guest-only mode) |
| `VITE_BTC_ADDRESS` / `VITE_ETH_ADDRESS` / `VITE_SOL_ADDRESS` | Self-custody wallet addresses |
| `VITE_ETHERSCAN_KEY` | Required for ETH payment monitoring |
| `VITE_SOL_RPC_URL` | Required for SOL payment monitoring |
| `VITE_MONITOR_MODE` | `real` (prod default) · `simulated` (dev default) · `off` |
| `VITE_AMOUNT_TOLERANCE_PCT` | Payment amount tolerance, default `0.5` |
| `VITE_POLL_INTERVAL_MS` | On-chain poll interval, default `15000` |

> In production, checkout is disabled unless at least one wallet address is configured (ETH also needs the Etherscan key, SOL needs an RPC). This guarantees no one can pay into an unverified wallet.

## How payments work

1. A buyer picks a coin; the checkout quotes a live USD→crypto amount and renders a QR (EIP-681 for ETH with `value` in wei, memo-tagged for SOL).
2. The monitor polls the chain for the address. Any transaction within the tolerance window is claimed (a tx-hash registry prevents double-spend across orders) and the order advances `awaiting → detecting → confirming → paid`.
3. On `paid`, the order settles exactly once through an idempotent ledger: downloads and revenue are booked, and the buyer's entitlement (game unlock / plan) is derived from the paid order itself — never from a client flag.

## Scripts

| Script | Description |
| --- | --- |
| `npm run dev` | Dev server on :3000 |
| `npm run build` | Type-check + production build |
| `npm run preview` | Preview the production build |
| `npm run type-check` | TypeScript only |
| `npm run lint` / `lint:fix` | ESLint |
| `npm run test:run` | Vitest suite |
| `npm run ci` | Type-check + lint + coverage |

## Docs & credits

- `/docs` in-app: full API reference for the OpenFlash runtime.
- Fonts: [Bricolage Grotesque](https://fonts.google.com/specimen/Bricolage+Grotesque) + [Courier Prime](https://fonts.google.com/specimen/Courier+Prime) (Google Fonts).
