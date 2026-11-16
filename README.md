# RayanOS live MVP

Browser (index.html, css/, js/) → `/api/*` (FastAPI in `api/index.py`, Vercel Function) → Supabase (bron van waarheid, met RLS).
Python rekent (totalen, projectgezondheid) en bewaart niets. `static-fallback/` is de oude statische demo.

## Lokaal testen (Windows PowerShell)
```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt -r requirements-dev.txt
Copy-Item .env.example .env        # vul SUPABASE_URL en SUPABASE_ANON_KEY in
Get-Content .env | ForEach-Object { if ($_ -match '^([^#=]+)=(.*)$') { Set-Item "env:$($Matches[1])" $Matches[2] } }
pytest
npm i -g vercel; vercel dev        # serveert frontend + API op http://localhost:3000
```
Controleer: `http://localhost:3000/api/health`, open daarna `http://localhost:3000`.

## Vercel
1. Zet de map in een Git-repo en importeer die in Vercel (framework: Other).
2. Settings → Environment Variables: `SUPABASE_URL`, `SUPABASE_ANON_KEY`.
3. Deploy. Controleer `/api/health`.

## Supabase
Voer `supabase/migrations/001_rayanos_mvp.sql` zelf uit in de SQL Editor (niets wordt automatisch uitgevoerd; geen DROP/DELETE). Het maakt: projects, tasks, purchase_transactions, asset_groups, assets, purchase_needs, decisions, en voegt `user_id` toe aan `domains`. Alle tabellen krijgen RLS: alleen eigen rijen.

## Beveiliging (verplicht vóór echte gegevens)
- API-routes vragen een Supabase-token (`Authorization: Bearer`); zonder token: 401. RLS beperkt data per gebruiker.
- De knop **Token** in de zijbalk is tijdelijk. Voeg eerst Supabase Auth toe (e-mail/wachtwoord of magic link, `supabase-js` in de browser) en maak een Supabase-gebruiker aan, voordat je echte cijfers invoert.
- Geen service-role key gebruikt. Nooit in frontend of Git zetten.

## Wat is nog demo
Inventaris, Persoonlijk, School, Focus, weekdoelen en de chatsimulatie. Alleen de bedragen op Vandaag (en het aandachtsgetal) komen live uit `/api/dashboard/summary`, als de status "Live data" toont. Endpoints voor projecten, taken, assets en aankoopbehoeften bestaan, maar de schermen gebruiken ze nog niet.

## Handmatige checks
Elke knop in de zijbalk (Vernieuwen, Token), euro-notatie nl-NL, geen NaN/undefined, 320 px breed, tab verbergen stopt het vernieuwen.
