# TRADING DASHBOARD - KOMPLETNA SPECIFIKACIJA

**Verzija:** 3.0  
**Datum:** Svibanj 2026  
**Status:** U RAZVOJU  
**GitHub:** https://github.com/CryptoKyl/Aplikacija-za-trejdanje  
**Supabase:** https://cpuhymueefwzozimqdjj.supabase.co  

---

## SADRZAJ

1. Overview
2. Tech Stack
3. Dizajn & UI/UX
4. Arhitektura Navigacije
5. Autentifikacija & Korisnici
6. Watchlist Management
7. Asset Kartice
8. Asset Detail View + Risk Calculator
9. Risk Engine Tab
10. Fundamentalne Metrike
11. AI Analiza Financijskih Izvjestaja
12. Search
13. Favorites & Pinning
14. Bulk Actions
15. Watchlist Sharing & Import
16. Admin Panel
17. Notifikacije - Arhitektura
18. Offline Support
19. Dark/Light Mode
20. Charts & Grafici
21. Baza Podataka - Supabase
22. Struktura Projekta
23. Inicijalni Testni Podaci
24. Trgovacka Pravila & Logika
25. Price API - Strategija
26. Roadmap - Sve Faze

---

## 1. OVERVIEW

### Sto je aplikacija?
Mobilna Trading Dashboard aplikacija za tradere koji trebaju brzu analizu asset-a sa jasnim signalima iz Hurst ciklusa i Supply/Demand zona. Aplikacija automatski trazi signale, prati fundamentalne metrike i koristi AI za analizu financijskih izvjestaja.

### Cilj
Izgraditi profesionalni trading tool koji pokriva:
- Tehnicku analizu (Hurst ciklusi, S&D zone)
- Fundamentalnu analizu (50+ metrika)
- AI analizu financijskih izvjestaja
- Live cijene za US, EU i australske dionice
- Notifikacije u realnom vremenu

### Korisnik
- Primarily mobitel (iOS/Android)
- US dionice, EU dionice, Australske dionice (ASX)
- Osobna upotreba + potencijalno vise korisnika

### Radni Principi
- Radimo iterativno, fazu po fazu
- Mobile-first dizajn (390px base)
- Kod se razvija u Cursor editoru s Claude AI pomocju

---

## 2. TECH STACK

| Tehnologija | Svrha | Status |
|---|---|---|
| Next.js 14+ (App Router) | Frontend framework | ✅ Implementirano |
| TypeScript | Type-safety | ✅ Implementirano |
| Tailwind CSS | Styling | ✅ Implementirano |
| shadcn/ui | UI Komponente | ✅ Implementirano |
| Supabase PostgreSQL | Baza podataka | ✅ Implementirano |
| Supabase Auth | Autentifikacija via PIN | ⬜ Faza 4 |
| Yahoo Finance (direktni fetch) | Live cijene (privremeno) | ✅ Implementirano |
| Polygon.io | Live cijene US + EU (profesionalno) | ⬜ Faza 5 |
| Financial Modeling Prep | Fundamentalne metrike + vijesti | ⬜ Faza 8 |
| Claude API | AI analiza izvjestaja | ⬜ Faza 9 |
| Vercel | Hosting/Deploy | ⬜ Faza 3 |
| TradingView Lightweight Chart | Grafici | ⬜ Faza 4 |
| Firebase Cloud Messaging | Push notifikacije | ⬜ Faza 5 |
| SendGrid | Email notifikacije | ⬜ Faza 5 |
| IndexedDB | Offline cache | ⬜ Faza 6 |
| Capacitor.js | Native app (App Store) | ⬜ Faza 10+ |

### Smart Refresh
- 10 sekundi za assets u ACTION ZONE ili DVOSTRUKI SIGNAL
- 60 sekundi za ostale assets

---

## 3. DIZAJN & UI/UX

### Tema
- Dark Mode (primary) + Light Mode opcija
- Glassmorphism efekt na svim karticama
- Neonske zelene akcente (#00ff88)
- Font: Space Grotesk (ne Inter)

### Pozadina
```css
background: #0a0e1a;
background-image: 
  linear-gradient(rgba(0,255,136,0.03) 1px, transparent 1px),
  linear-gradient(90deg, rgba(0,255,136,0.03) 1px, transparent 1px);
background-size: 24px 24px;
```

### Glassmorphism Kartice
```css
background: linear-gradient(135deg, rgba(255,255,255,0.06) 0%, rgba(255,255,255,0.02) 100%);
backdrop-filter: blur(12px);
border: 1px solid rgba(255, 255, 255, 0.08);
box-shadow: 0 4px 24px rgba(0,0,0,0.4), inset 0 1px 0 rgba(255,255,255,0.06);
```

### Status Boje

| Status | Hex | Efekt |
|---|---|---|
| DVOSTRUKI SIGNAL | #00ff88 | Glow pulsira beskonacno |
| ACTION ZONE | #3b82f6 | Staticno |
| CIKLUS AKTIVAN | #f59e0b | Staticno |
| STOP LOSS | #ef4444 | Staticno |

### Animacije

| Tip | Trajanje |
|---|---|
| Jednostavne (hover, click) | 150ms |
| Kompleksne (modal, tranzicije) | 250ms |
| Smooth (bar fill) | 600ms |
| Glow pulsiranje (DVOSTRUKI SIGNAL) | 2s infinite |

### Mobile-First
- Base width: 390px
- Max width: 430px
- Centriran na vecim ekranima
- Bottom nav: fiksna, 64px visina

---

## 4. ARHITEKTURA NAVIGACIJE

### Layout Ekrana
```
+---------------------------------------+
|  Hamburger        Title    Search     |  <- Header (fiksan)
+---------------------------------------+
|                                       |
|  [Sadrzaj ovisno o tab-u]             |
|                                       |
+---------------------------------------+
|  Watchlist | Risk Engine | Organizacija|  <- Bottom Nav (fiksan)
+---------------------------------------+
```

### Bottom Navigation
- Watchlist - glavni tab
- Risk Engine - kalkulator pozicija
- Organizacija - upravljanje assetima
- Active indicator: Tockica ispod aktivnog taba + neon zelena boja

### Hamburger Menu
Slide-out panel, glassmorphism pozadina.

```
+----------------------------------+
|  X  POSTAVKE                     |
+----------------------------------+
|  Watchlist Management            |
|    -> Kreiraj novu               |
|    -> Import watchlist           |
|                                  |
|  Displej                         |
|    -> Sort by: Status            |
|    -> Dark Mode  [toggle]        |
|    -> Light Mode [toggle]        |
|                                  |
|  Profil                          |
|    -> email (read-only)          |
|                                  |
|  Admin Panel (samo admin)        |
|  Odjava (crvena)                 |
+----------------------------------+
```

---

## 5. AUTENTIFIKACIJA & KORISNICI

### Login Flow
```
Email + 6-znamenkasti PIN
        |
Supabase Auth provjeri
        |
OK  -> JWT token -> Dashboard
FAIL -> "Pogresni podaci"
```

### Admin Korisnik
- Email: marko31071985@gmail.com
- Pristup: Sve + Admin Panel

### Obicni Korisnici
- Dodaje ih SAMO Admin
- PIN mijenjanje: Faza 4+

---

## 6. WATCHLIST MANAGEMENT

### Vrste Watchlist-a

#### Default Watchlist (Admin Postavlja)
- Samo admin moze mijenjati
- Korisnici samo gledaju i filtriraju

#### Custom Watchlist (Korisnik Kreira)
- Neogranicen broj
- Dodavanje assets iz default liste ili bilo kojeg asset-a
- Korisnik definira koje parametre vidi na kartici

### Dropdown
```
Default Watchlist (v)
  -> Default Watchlist
  -> My Metals
  -> Energy Plays
  ─────────────────
  -> + Nova Watchlist
```

---

## 7. ASSET KARTICE

### Pulse Metrike (Vrh Stranice)
```
+--------+-----------+---------+----------+----------+
|  [12]  |   [3]     |   [5]   |   [2]    |   [1]    |
| Ukupno | Dvostruki | Action  |  Ciklus  |   Stop   |
| asseta |  Signal   |  Zone   | Aktivan  |   Loss   |
+--------+-----------+---------+----------+----------+
```

### Filter Pills
```
[SVE] [DVOSTRUKI SIGNAL] [ACTION ZONE] [CIKLUS AKTIVAN] [STOP LOSS]
```

### Izgled Kartice

```
+===================================================+
|  NICK H  Nickel Futures    [DVOSTRUKI SIGNAL]     |
|  $26.23  ▲ +1.50%                                |
|                                                   |
|  [================================] Unutar zone   |
|  (proximity bar)                                  |
|                                                   |
|  💰 Demand: $26.00-$28.00  🛡 Stop Loss: $25.50  |
|  ⚠️ SFP Invalid: $25.20   🔄 3 dana              |
+===================================================+
```

### Swipe to Delete
- Swipe lijevo -> pojavi se "Obriši" gumb

### Status Badge

| Status | Trigger |
|---|---|
| DVOSTRUKI SIGNAL | Cijena u zoni + Hurst ciklus aktivan |
| ACTION ZONE | Cijena unutar demand zone |
| CIKLUS AKTIVAN | Hurst ciklus aktivan, ceka zonu |
| STOP LOSS | Cijena ispod stop loss razine |

---

## 8. ASSET DETAIL VIEW + RISK CALCULATOR

### Otvaranje
Klik na karticu -> Slide-up sheet (85% visine ekrana)

### Tabovi u Detail View
```
[Tehnicki] [Fundamentali] [Vijesti]
```

### Layout - Tehnicki Tab
```
+------------------------------------------+
|  <- NICK H                [DVOSTRUKI]  X |
|  $26.23  ▲ +1.50%                        |
|                                          |
|  BLIZINA CIJENE / PROXIMITY BAR          |
|  ┌────────────────────────────────────┐  |
|  │         $26.23                     │  |
|  │            ↓                       │  |
|  │ [DZ][=====|========-------][SL]    │  |
|  │ $26.00  zeleno->zuto->crveno $25.50│  |
|  │ "Cijena unutar Demand Zone.        │  |
|  │  Blizina Stop Loss-a je 2.86%"     │  |
|  └────────────────────────────────────┘  |
|                                          |
|  PARAMETRI                               |
|  Demand: $26.00-$28.00                   |
|  Supply: $30.00-$31.50                   |
|  Stop Loss: $25.50                       |
|  SFP Invalid: $25.20                     |
|  Timing: 3 dana                          |
|  Hurst Bias: Bullish Correction          |
|                                          |
|  MINI CHART 24h                          |
|  [Linijski grafik - interaktivan]        |
|  [Otvori TradingView]                    |
|                                          |
|  RISK CALCULATOR                         |
|  Capital:    [5000]  Risk%: [2]          |
|  Risk iznos:  $100 (auto)                |
|  Entry:      [26.50]                     |
|  Stop Loss:  [25.50] (auto-fill)         |
|  Target:     [30.00]                     |
|                                          |
|  Position Size:    200 akcija            |
|  Potential Profit: $700                  |
|  Risk/Reward:      1:7                   |
|                                          |
|  [Izracunaj]        [Reset]              |
+------------------------------------------+
```

### Layout - Fundamentalni Tab
```
+------------------------------------------+
|  VALUACIJA                               |
|  P/E:        28.5   Sektor avg: 22.1    |
|  Forward P/E: 24.2                       |
|  P/B:        45.2   🟢 Odlicno          |
|  EV/EBITDA:  22.1                        |
|                                          |
|  PROFITABILNOST                          |
|  ROE:        147%   🟢 Odlicno          |
|  Net Margin:  25%   🟢 Odlicno          |
|  Debt/Eq:    1.8x   🟡 Srednje          |
|                                          |
|  EARNINGS                                |
|  EPS Actual:    $2.18                    |
|  EPS Estimate:  $2.10                    |
|  EPS Surprise:  +3.8% ✅ BEAT           |
|  Sljedeci izvjestaj: 15.08.2026          |
|                                          |
|  ANALYST                                 |
|  Rating: BUY (18/24 analitičara)        |
|  Price Target: $220 (avg)               |
+------------------------------------------+
```

### Layout - Vijesti Tab
```
+------------------------------------------+
|  🤖 AI SAZETAK (najnoviji izvjestaj)     |
|  ┌────────────────────────────────────┐  |
|  │ Q4 2024: BEAT EPS +3.8%           │  |
|  │ Services +16% YoY, iPhone -3%     │  |
|  │ Outlook: Pozitivan za Q1 2025     │  |
|  │ Sentiment: 🟡 NEUTRALNO           │  |
|  └────────────────────────────────────┘  |
|                                          |
|  VIJESTI                                 |
|  • Apple najavljuje novi iPhone...       |
|    Reuters - prije 2 sata               |
|  • Goldman Sachs podigao target...      |
|    Bloomberg - prije 5 sati             |
+------------------------------------------+
```

### Risk Calculator Formule
```
Position Size   = Risk Amount / (Entry - Stop Loss)
Potential Profit = (Target - Entry) x Position Size
Risk/Reward      = Profit / Risk
```

---

## 9. RISK ENGINE TAB

### Tabovi
```
[Osnovni] [Ladder (Skaliranje)]
```

### Osnovni Kalkulator
```
Kapital:        [5000]
Risk %:         [2]
Risk iznos:     $100 (auto)
Cijena ulaza:   [26.50]
Stop Loss:      [25.50]
Target cijena:  [30.00]

--- REZULTATI ---
Velicina pozicije: 200 akcija
Potencijalni profit: $700
Risk/Reward: 1:7

[Izracunaj] [Reset]
```

### Ladder (Fibonacci Skaliranje)
```
Rasporedivanje naloga kroz zonu:

Fib 0.618:  $27.27  → 40% kapitala  → 80 akcija
Fib 0.702:  $26.90  → 35% kapitala  → 70 akcija
Fib 0.786:  $26.50  → 25% kapitala  → 50 akcija

Ukupno: 200 akcija, $100 risk
```

### Compounding Kalkulator (Faza 7)
```
Original Entry:  $26.50  (100 akcija)
New Entry:       $27.00  (50 akcija)
New Stop Loss:   $26.20

Novi prosjek ulaza: $26.67
Add size: 33% dodatka
```

---

## 10. FUNDAMENTALNE METRIKE

### 50+ Metrika po Kategorijama

#### VALUACIJA
```
P/E Ratio, Forward P/E, P/B Ratio, P/S Ratio,
P/FCF, EV/EBITDA, EV/Revenue, PEG Ratio,
Dividend Yield, Enterprise Value
```

#### PROFITABILNOST
```
ROE, ROA, ROIC, Gross Margin %, Operating Margin %,
Net Margin %, EBITDA Margin, FCF Margin,
Return on Capital Employed
```

#### RAST
```
Revenue Growth YoY, Revenue Growth QoQ,
EPS Growth YoY, EPS Growth QoQ,
FCF Growth, EBITDA Growth,
User/Subscriber Growth (za tech)
```

#### FINANCIJSKA SNAGA
```
Debt/Equity, Current Ratio, Quick Ratio,
Interest Coverage, Debt/EBITDA,
Cash per Share, Net Cash/Debt,
Working Capital
```

#### EARNINGS
```
EPS Actual, EPS Estimate, EPS Surprise %,
Revenue Actual, Revenue Estimate, Revenue Surprise %,
Next Earnings Date, Earnings Calendar
```

#### DIVIDENDE
```
Dividend per Share, Payout Ratio,
Dividend Growth 5Y, Ex-Dividend Date,
Dividend History
```

#### INSIDER & INSTITUCIJE
```
Insider Ownership %, Institutional Ownership %,
Short Interest %, Insider Buying/Selling,
52W High/Low, Float
```

#### ANALYST
```
Analyst Rating (Buy/Hold/Sell), 
Price Target (avg/high/low),
Number of Analysts, Upgrades/Downgrades
```

### Prikazivanje Metrika

#### Na Kartici (2-3 metrike po izboru)
```
Korisnik definira koje 2-3 metrike vidi direktno na kartici.
Npr: P/E | ROE | Next Earnings
```

#### U Detail View - Fundamentalni Tab
```
Sve metrike grupirane po kategorijama.
Color coding:
🟢 Zeleno = odlicno (gornji kvartil sektora)
🟡 Zuto   = srednje
🔴 Crveno = losije od prosjeka sektora
```

#### Usporedba sa Sektorom
```
P/E: 28.5  |  Sektor prosjek: 22.1  |  +29% iznad prosjeka
```

### API za Fundamentale
- **Financial Modeling Prep (FMP)**: $19/mj
- Pokriva: US dionice, EU dionice, ASX dionice
- Podaci: Svi financijski izvjestaji, ratios, earnings, vijesti

---

## 11. AI ANALIZA FINANCIJSKIH IZVJESTAJA

### Koncept
```
1. FMP API detektira novi earnings izvjestaj
         ↓
2. Webhook triggera nasu aplikaciju
         ↓
3. Dohvacamo puni izvjestaj (PDF/JSON)
         ↓
4. Saljemo Claude API-ju na analizu
         ↓
5. Claude vraca strukturirani sazetak
         ↓
6. Prikazujemo u aplikaciji + saljem notifikacije
```

### Claude API Prompt Struktura
```
"Analiziraj ovaj earnings izvjestaj i vrati JSON s:
- sazetak (3-5 recenica)
- kljucni_brojevi (EPS beat/miss, Revenue beat/miss)
- menadžment_komentar (sto su rekli o buducnosti)
- rizici (sto su naveli kao rizike)
- sentiment (Bullish/Bearish/Neutral)
- ocjena (1-10)"
```

### Primjer AI Sazetka
```
📊 APPLE (AAPL) — Q4 2024 Earnings

✅ BEAT EPS: $2.18 vs $2.10 (+3.8%)
❌ MISS Revenue: $89.5B vs $90.1B (-0.7%)

Sazetak: Solidni rezultati uz snazni rast Services 
segmenta (+16% YoY) koji kompenzira slabiji iPhone 
(-3% YoY). Menadžment dao pozitivan Q1 2025 outlook.

Rizici: Kina tržiste (-8% YoY), EU regulacija App Store.

Sentiment: 🟡 NEUTRALNO

Goldman Sachs: Maintain BUY $220
Morgan Stanley: Maintain OW $230
```

### Earnings Calendar Widget
```
Ovaj tjedan:
├── Pon: AAPL (after market) ← sutra!
├── Sri: MSFT (after market)
└── Pet: HAL (before market)
```

### Notifikacija pri Novom Izvjestaju
```
Push: "📊 AAPL upravo objavio Q4! EPS BEAT +3.8%"
       Klikni za AI sazetak →
```

### Trosak
- Claude API: ~$0.01 po izvjestaju
- Za 50 asseta godisnje = ~$2-5/god

---

## 12. SEARCH

### Logika
```
PRIORITET 1 - U korisnikovim watchlistama:
  Default Watchlist -> NICK
  My Metals -> NICK

PRIORITET 2 - Dostupni assets:
  Svi assets koje platforma podrzava
```

### UI
```
[NICK_____________] [X]

U TVOJIM WATCHLISTAMA:
  NICK H | $26.23 | +1.5%     [Otvori →]

DOSTUPNI ZA DODAVANJE:
  NICK | Nickel | $26.23      [+ Dodaj]
```

---

## 13. FAVORITES & PINNING

- Zvjezdica = favorit
- Pin = na vrhu liste
- Pin-ani assets prikazuju se PRVI
- Redoslijed = pin_order u bazi

---

## 14. BULK ACTIONS

- Long press -> checkbox mode
- Akcije: Delete, Add to watchlist, Pin, Cancel

---

## 15. WATCHLIST SHARING & IMPORT

### Link Format
```
https://app.com/import/{shareId}/{targetUserId}
```

### Pravila
- Samo primatelj moze importirati
- Direktna kopija (bez sinkronizacije)
- Link ne istice

---

## 16. ADMIN PANEL

### Sekcije
1. User Management (dodaj/obrisi korisnike)
2. Assets Management (dodaj/uredi/obrisi)
3. Parameter Definitions (koje metrike postoje)
4. Default Watchlist Management
5. AI Import (Faza 9)

---

## 17. NOTIFIKACIJE - ARHITEKTURA

### 3 Tipa

#### TIP 1: IN-APP (Faza 4)
```
Toast: "NICK upravo usao u ACTION ZONE!"
Sprema se u notifications tablicu
```

#### TIP 2: PUSH NOTIFICATION (Faza 5)
```
Firebase Cloud Messaging (FCM)
"NICK - ACTION ZONE!"
```

#### TIP 3: EMAIL (Faza 5)
```
SendGrid
"NICK - ACTION ZONE Alert"
```

### Triggeri
- Asset ulazi u ACTION ZONE
- DVOSTRUKI SIGNAL aktivan
- STOP LOSS probijen
- Novi earnings izvjestaj + AI sazetak
- Insider kupovina/prodaja
- Analyst upgrade/downgrade

---

## 18. OFFLINE SUPPORT (Faza 6)

```
ONLINE:  Supabase -> IndexedDB -> Prikaz
OFFLINE: IndexedDB -> Prikaz + "Offline - podaci stari"
POVRATAK: Automatski refresh iz Supabase
```

Cache: Assets, Watchliste, Preferences
Ne cache: Live cijene, Chart podaci

---

## 19. DARK/LIGHT MODE (Faza 4)

- Toggle u Hamburger Meniju
- Sprema se u user_preferences
- Primjenjuje odmah

---

## 20. CHARTS & GRAFICI

### Mini Chart na Karticama
- Mala linijska krivulja u donjem dijelu kartice
- Boja prati status
- Faza 4: Stvarni podaci

### Mini Chart 24h (Detail View)
- TradingView Lightweight Chart
- Interaktivan (zoom, hover)
- Faza 4: Stvarni podaci

### TradingView Full Chart
- Gumb [Otvori TradingView]
- Fullscreen ili nova kartica

---

## 21. BAZA PODATAKA - SUPABASE

### Trenutne Tablice (Implementirano)
```sql
-- profiles, watchlists, trading_assets
-- (detalji u schema.sql)
```

### Nove Tablice (Faza 8+)
```sql
-- Fundamentalne metrike
CREATE TABLE asset_fundamentals (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  asset_id UUID REFERENCES trading_assets(id),
  metric_key VARCHAR NOT NULL,
  metric_value DECIMAL,
  metric_text VARCHAR,
  period VARCHAR,  -- 'TTM', 'Q4 2024', 'FY 2024'
  fetched_at TIMESTAMPTZ DEFAULT NOW()
);

-- AI sazetci izvjestaja
CREATE TABLE earnings_summaries (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  asset_id UUID REFERENCES trading_assets(id),
  period VARCHAR NOT NULL,  -- 'Q4 2024'
  report_date DATE,
  eps_actual DECIMAL,
  eps_estimate DECIMAL,
  eps_surprise_pct DECIMAL,
  revenue_actual DECIMAL,
  revenue_estimate DECIMAL,
  revenue_surprise_pct DECIMAL,
  ai_summary TEXT,
  ai_sentiment VARCHAR,  -- 'Bullish', 'Bearish', 'Neutral'
  ai_score INTEGER,  -- 1-10
  key_points JSONB,
  risks JSONB,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Vijesti
CREATE TABLE asset_news (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  asset_id UUID REFERENCES trading_assets(id),
  title VARCHAR NOT NULL,
  summary TEXT,
  source VARCHAR,
  url VARCHAR,
  published_at TIMESTAMPTZ,
  sentiment VARCHAR,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Insider trading
CREATE TABLE insider_transactions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  asset_id UUID REFERENCES trading_assets(id),
  insider_name VARCHAR,
  insider_role VARCHAR,
  transaction_type VARCHAR,  -- 'BUY', 'SELL'
  shares INTEGER,
  price DECIMAL,
  value DECIMAL,
  transaction_date DATE,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Korisnicke preference za metrike
CREATE TABLE user_metric_preferences (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES profiles(id),
  asset_id UUID REFERENCES trading_assets(id),
  card_metrics JSONB,  -- koje metrike na kartici
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

---

## 22. STRUKTURA PROJEKTA

```
trading-dashboard/
├── .cursorrules
├── .env.local (NIKAD na GitHub!)
├── .env.local.example
├── package.json
├── tailwind.config.ts
├── tsconfig.json
├── next.config.js
├── postcss.config.js
│
├── app/
│   ├── globals.css
│   ├── layout.tsx
│   ├── page.tsx
│   ├── (auth)/
│   │   └── login/page.tsx
│   ├── (dashboard)/
│   │   ├── layout.tsx
│   │   ├── watchlist/page.tsx
│   │   ├── risk-engine/page.tsx
│   │   ├── organizacija/page.tsx
│   │   └── admin/
│   │       ├── page.tsx
│   │       ├── users/page.tsx
│   │       ├── assets/page.tsx
│   │       └── watchlist/page.tsx
│   └── api/
│       ├── prices/route.ts
│       ├── fundamentals/route.ts (Faza 8)
│       ├── news/route.ts (Faza 8)
│       ├── earnings/route.ts (Faza 8)
│       └── ai-analysis/route.ts (Faza 9)
│
├── components/
│   ├── layout/
│   │   ├── BottomNav.tsx
│   │   ├── Header.tsx
│   │   └── HamburgerMenu.tsx
│   ├── watchlist/
│   │   ├── AssetCard.tsx
│   │   ├── StatusBadge.tsx
│   │   ├── ProximityBar.tsx
│   │   ├── FilterPills.tsx
│   │   └── StatsBar.tsx
│   ├── modals/
│   │   ├── AssetDetailModal.tsx
│   │   └── HamburgerMenu.tsx
│   ├── fundamentals/ (Faza 8)
│   │   ├── FundamentalsTab.tsx
│   │   ├── MetricCard.tsx
│   │   └── SectorComparison.tsx
│   ├── news/ (Faza 8)
│   │   ├── NewsTab.tsx
│   │   └── AISummaryCard.tsx
│   ├── charts/ (Faza 4)
│   │   ├── MiniChart.tsx
│   │   └── Chart24h.tsx
│   └── risk/
│       └── RiskCalculator.tsx
│
├── lib/
│   ├── types.ts
│   ├── supabase.ts
│   ├── trading-logic.ts
│   ├── utils.ts
│   ├── fundamentals.ts (Faza 8)
│   └── ai-analysis.ts (Faza 9)
│
├── hooks/
│   ├── useAssets.ts
│   ├── usePrices.ts
│   ├── useFundamentals.ts (Faza 8)
│   └── useRiskEngine.ts
│
└── supabase/
    └── schema.sql
```

---

## 23. INICIJALNI TESTNI PODACI

### Trenutno u Bazi
- NICK H - Nickel Futures (ROBA)
- HAL M - Halliburton (ENERGIJA)
- BTC-USD - Bitcoin (KRIPTO)
- VALE - Vale S.A. (ROBA)
- GLD - SPDR Gold Shares (ETF)

### Planirani Asseti (US Dionice)
```
Energija: HAL, SLB, XOM, CVX, OXY
Metali:   NICK, VALE, BHP, RIO
Tech:     AAPL, MSFT, NVDA, GOOGL
Finance:  JPM, BAC, GS
ETF:      GLD, SLV, XLE, SPY
Kripto:   BTC-USD, ETH-USD
```

### EU Dionice (Faza 5+)
```
Frankfurt:  SAP, BASF, BMW, Volkswagen
LSE:        Shell, HSBC, BP, Unilever
Euronext:   LVMH, ASML, TotalEnergies
```

### ASX Dionice (Faza 5+)
```
BHP, CBA, NAB, WBC, ANZ,
RIO, FMG, WES, CSL, MQG
```

---

## 24. TRGOVACKA PRAVILA & LOGIKA

### Status Logika
```
DVOSTRUKI SIGNAL = cijena u demand zoni + Hurst ciklus aktivan
ACTION ZONE      = cijena unutar demand zone (bez Hurst)
CIKLUS AKTIVAN   = Hurst ciklus aktivan, cijena ceka zonu
STOP LOSS        = cijena ispod stop loss razine
WATCH            = nista od gore
```

### Proximity Racunanje
```
Cijena IZNAD zone:
  proximity = ((cijena - demand_high) / demand_high) * 100

Cijena UNUTAR zone:
  proximity = 0%

Cijena ISPOD zone:
  proximity = 100%
```

### Laddering (Fibonacci)
```
Fib 0.618: 40% kapitala (konzervativniji ulaz)
Fib 0.702: 35% kapitala (sredina)
Fib 0.786: 25% kapitala (agresivniji ulaz)
```

### SFP (Swing Failure Pattern)
```
Cijena probije vrh/dno
Pokuplja likvidnost
NE ZATVORI izvan tog nivoa
Stop Loss: tik iznad/ispod fitilja
```

---

## 25. PRICE API - STRATEGIJA

### Trenutno (MVP)
```
Yahoo Finance (direktni fetch)
├── Besplatno ✅
├── Kašnjenje: 15-20 minuta ⚠️
└── Neoficijalni API (moze prestati) ⚠️
```

### Faza 5 - Profesionalni Setup

#### US Dionice + Kripto + Forex
```
Polygon.io - $29/mj
├── Real-time (<1 sekunda) ✅
├── NYSE, NASDAQ ✅
├── Kripto ✅
├── Forex ✅
└── Opcije, Futures ✅
```

#### EU Dionice
```
Istraziti (trenutno nejasno pokriće):
├── Polygon.io (provjeriti EU coverage)
├── EODHD ($19/mj - dobro EU pokriće)
└── Quandl/Nasdaq Data Link
```

#### ASX Dionice (Australija)
```
Opcije za istraziti:
├── EODHD ($19/mj - ima ASX) ✅
├── MarketStack ($9/mj - ima ASX)
└── ASX direktni feed (skuplje)
```

#### Preporucena Kombinacija
```
Polygon.io:  $29/mj  (US + Kripto + Forex)
EODHD:       $19/mj  (EU + ASX + historical)
FMP:         $19/mj  (Fundamentali + Vijesti)
─────────────────────────────────────────
UKUPNO:      $67/mj  za profesionalni setup
```

#### Kripto Besplatno
```
Binance API (besplatno, real-time)
├── BTC, ETH, i sve major altcoins
└── <100ms kašnjenje
```

### Migracija
```
Migracija na novi API = promjena SAMO u:
app/api/prices/route.ts
└── ~1-2 sata rada
```

---

## 26. ROADMAP - SVE FAZE

### ✅ FAZA 1 — MVP Struktura
```
✅ Projekt struktura (.cursorrules, package.json...)
✅ Dizajn sistem (dark glassmorphism, Space Grotesk)
✅ Login stranica (6-znamenkasti PIN UI)
✅ Watchlist s karticama (mock podaci)
✅ Status logika (DVOSTRUKI SIGNAL, ACTION ZONE...)
✅ Proximity bar (kompaktni + prosireni)
✅ Risk kalkulator (osnovni)
✅ Organizacija tab
✅ Bottom navigation
✅ Supabase schema (SQL)
```

### ✅ FAZA 2 — Live Podaci
```
✅ Supabase baza postavljena
✅ Test podaci u bazi
✅ Zamijena mock podataka sa Supabase upitima
✅ Yahoo Finance live cijene (direktni fetch)
✅ Popravak % promjene (chartPreviousClose)
✅ Auto-refresh svake 30 sekundi
✅ "Azurirano: HH:MM:SS" timestamp
```

### ⬜ FAZA 3 — Deploy (SUTRA)
```
⬜ Vercel account + deploy
⬜ Environment varijable na Vercel
⬜ Javni link (https://trading-dashboard-xxx.vercel.app)
⬜ Testiranje na mobitelu bez lokalnog servera
⬜ Custom domena (opcionalno)
```

### ⬜ FAZA 4 — Polish & UX
```
⬜ Login s PIN-om (pravi Supabase Auth)
⬜ Hamburger menu (funkcionalan)
⬜ Asset Detail Modal poboljsanja:
   ⬜ Mini chart 24h (TradingView)
   ⬜ Proximity bar s labelama (DZ/SL)
   ⬜ SFP Invalid highlight
   ⬜ Cycle days countdown (pravi datumi)
⬜ Dark/Light mode toggle
⬜ Swipe to delete (poboljsano)
⬜ Favoriti i pinning
⬜ Sort by Status funkcionalan
⬜ Skeleton loading poboljsan
```

### ⬜ FAZA 5 — Notifikacije & Pravi Price API
```
⬜ Polygon.io integracija (US + Kripto)
⬜ EODHD integracija (EU + ASX)
⬜ Smart Refresh (10s aktivne zone, 60s ostale)
⬜ Push notifikacije (Firebase Cloud Messaging)
⬜ Email notifikacije (SendGrid)
⬜ Triggeri: ACTION ZONE, DVOSTRUKI SIGNAL, STOP LOSS
⬜ Notification Center u aplikaciji
⬜ Notification preferences po korisniku
```

### ⬜ FAZA 6 — Offline & PWA
```
⬜ IndexedDB cache
⬜ Offline indikator
⬜ PWA manifest
⬜ "Instaliraj na home screen" prompt
⬜ Service Worker
```

### ⬜ FAZA 7 — Risk Engine 2.0
```
⬜ Ladder kalkulator (Fibonacci skaliranje)
⬜ Compounding kalkulator
⬜ Trade journal (zapis ulaza/izlaza)
⬜ Portfolio tracker (ukupni P&L)
⬜ Win rate statistike
⬜ Export (PDF, CSV)
```

### ⬜ FAZA 8 — Fundamentalne Metrike
```
⬜ Financial Modeling Prep API ($19/mj)
⬜ 50+ metrika po assetu
⬜ Fundamentalni tab u Asset Detail View
⬜ Color coding (zeleno/zuto/crveno vs sektor)
⬜ Usporedba sa sektorskim prosjekom
⬜ Earnings Calendar widget
⬜ Insider trading prikaz
⬜ Analyst ratings & price targets
⬜ 2-3 metrike po izboru na kartici
⬜ Vijesti tab po assetu
⬜ Korisnik bira koje metrike vidi na kartici
```

### ⬜ FAZA 9 — AI Analiza
```
⬜ Claude API integracija
⬜ Automatska analiza earnings izvjestaja
⬜ AI sazetak (3-5 recenica)
⬜ Beat/Miss detekcija (EPS, Revenue)
⬜ Rizici izvuceni iz izvjestaja
⬜ Sentiment analiza (Bullish/Bearish/Neutral)
⬜ Conference call transcript analiza
⬜ AI Score (1-10) za svaki asset
⬜ Notifikacija s AI sazetkom
⬜ Komparativna analiza (vs konkurencija)
⬜ Screening po fundamentalima + tehnickim
```

### ⬜ FAZA 10 — Admin & Multi-User
```
⬜ Admin Panel (user management)
⬜ Bulk import CSV
⬜ Watchlist sharing (QR kod)
⬜ Multi-user podrska
⬜ PIN mijenjanje od strane korisnika
⬜ Role-based access control
```

### ⬜ FAZA 11 — Skaliranje (Dugorocno)
```
⬜ Tablet/Desktop responzivni layout
⬜ Capacitor.js (pakiranje u native app)
⬜ Google Play Store deploy
⬜ Apple App Store deploy
⬜ Subscription model (opcionalno)
⬜ Community watchliste
⬜ API za vanjske integracije
```

---

## TROSKOVNIK (Kad sve bude gotovo)

```
Vercel Pro:              $20/mj
Supabase Pro:            $25/mj
Polygon.io Professional: $29/mj
EODHD:                   $19/mj
Financial Modeling Prep: $19/mj
Claude API:              ~$5/mj
──────────────────────────────
UKUPNO:                 ~$117/mj

vs Bloomberg Terminal:  $2,000/mj
Usteda:                 ~$1,883/mj 😄
```

---

## VAZNE NAPOMENE

1. `.env.local` NIKAD na GitHub!
2. Supabase RLS je UKLJUCEN
3. Admin email: marko31071985@gmail.com
4. Risk Calculator NE SPREMA rezultate (za sada)
5. Yahoo Finance je privremeno rjesenje
6. Sve boje definirane u `tailwind.config.ts`
7. Sve poslovne logike u `lib/trading-logic.ts`
8. Migracija price API-ja = samo `app/api/prices/route.ts`

---

**Dokument azuriran:** Svibanj 2026  
**Verzija:** 3.0  
**Autor:** Marko + Claude AI  
**Sljedeci korak:** Faza 3 - Vercel Deploy
