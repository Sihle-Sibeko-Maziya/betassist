# BetAssist Frontend

Next.js 14 (App Router) + TypeScript + Tailwind + Framer Motion + Recharts.
Bloomberg-Terminal-for-sports aesthetic. Dark only.

> Analytics only. Not betting advice.

## Quick start
```bash
pnpm install   # or npm install / bun install
cp .env.example .env.local
pnpm dev
```
Open http://localhost:3000 → redirects to `/login` → use the prefilled demo creds → land on `/dashboard`.

`NEXT_PUBLIC_USE_MOCK=true` keeps everything client-side mock so you can demo without the NestJS backend.

## Structure
```
src/
  app/
    layout.tsx              # Root layout, fonts, providers
    providers.tsx           # React Query + Auth providers
    globals.css             # Tailwind + terminal grid bg
    page.tsx                # → redirect /dashboard
    login/page.tsx          # Auth screen
    dashboard/
      layout.tsx            # Sidebar + Header + Ticker shell
      page.tsx              # Main analysis dashboard
  components/
    layout/                 # Sidebar, Header, Ticker
    ui/                     # Card, StatBadge, ProbabilityGauge, DataTable
    dashboard/              # Confidence, EV, Risk, AI, LegIndicators, LegsTable
    charts/                 # Recharts wrappers
  contexts/auth-context.tsx # JWT-in-localStorage auth + protected routes
  hooks/use-analysis.ts     # React Query hooks (slip analysis, history)
  lib/
    api-client.ts           # axios instance w/ JWT interceptor
    mock-data.ts            # demo slip + 30-day history
    utils.ts                # cn, fmtPct, fmtBps, fmtMoney, fmtOdds
  types/index.ts            # SlipAnalysis, SlipLeg, HistoricalPoint, User
tailwind.config.ts          # terminal palette + ticker/pulse keyframes
```

## Dashboard widgets
- **ProbabilityGauge** — animated 270° SVG arc, model vs market overlay, edge in pp.
- **EvCard** — expected value per unit, edge in bps, Kelly fraction.
- **ConfidenceCard** — σ-weighted confidence with animated bar.
- **RiskCard** — 20-segment risk meter (green→amber→red), variance.
- **LegIndicators** — strongest / weakest leg callouts.
- **AiPanel** — model-generated explanation + mandatory disclaimer.
- **HistoryChart** — Recharts ComposedChart, model area + market line.
- **LegsTable** — terminal-style table with edge coloring + signal badge.

## Wiring to the real backend
Set `NEXT_PUBLIC_USE_MOCK=false` and point `NEXT_PUBLIC_API_URL` at the NestJS gateway from the architecture doc.
The hooks in `src/hooks/use-analysis.ts` already call `/slips/:id/analysis` and `/analytics/history`.
WebSocket wiring (live slip updates via `slip.analysis.completed`) is left as a follow-up — drop a `socket.io-client` listener into `dashboard/layout.tsx` and `queryClient.invalidateQueries(['slip', id])` on event.
