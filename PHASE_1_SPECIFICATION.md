# TRADING DASHBOARD - PHASE 1 SPECIFICATION

**Verzija:** 2.0
**Datum:** Svibanj 2026
**Status:** FINALIZIRAN
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
10. Search
11. Favorites & Pinning
12. Bulk Actions
13. Watchlist Sharing & Import
14. Admin Panel
15. Notifikacije - Arhitektura
16. Offline Support
17. Dark/Light Mode
18. Charts & Grafici
19. Baza Podataka - Supabase
20. Struktura Projekta
21. Inicijalni Testni Podaci
22. Trgovacka Pravila & Logika
23. Roadmap - Faza 2+

---

## 1. OVERVIEW

### Sto je aplikacija?
Mobilna Trading Dashboard aplikacija za tradere koji trebaju brzu analizu asset-a sa jasnim signalima iz Hurst ciklusa i Supply/Demand zona.

### Cilj Faze 1
Izgraditi vizualni kostur i core funkcionalnosti sa dummy podacima. Bez live API integracije - samo testiranje UI-ja i logike.

### MVP - Minimalni Pocetak
```
KORAK 1: Setup projekta
KORAK 2: Login stranica (vizualno)
KORAK 3: Watchlist sa dummy karticama
KORAK 4: Asset Detail View + Risk Calculator
KORAK 5: Testiranje na mobu
KORAK 6: Spajanje sa Supabase
KORAK 7: Login funkcionalan (pravi PIN)
KORAK 8: Podaci iz baze (ne dummy)
--- MVP GOTOV ---
KORAK 9+: Dodajemo ostale funkcije
```

### Radni Principi
- Radimo iterativno, fazu po fazu
- Prvo vizualni kostur, onda baza i API-ji
- Kod se direktno upisuje na GitHub web interface (copy/paste)
- Korisnik nema iskustva s programiranjem

---

## 2. TECH STACK

| Tehnologija | Svrha |
|---|---|
| Next.js 14+ (App Router) | Frontend framework |
| TypeScript | Type-safety |
| Tailwind CSS | Styling |
| shadcn/ui | UI Komponente |
| Supabase PostgreSQL | Baza podataka |
| Supabase Auth | Autentifikacija via PIN |
| yfinance | Live cijene (Faza 2+) |
| TradingView Lightweight Chart | Grafici (gotova libraru) |
| IndexedDB | Offline cache |
| QR Code Generator | Share watchlist |

### Smart Refresh (Faza 2+)
- 10 sekundi za assets u ACTION ZONE ili DVOSTRUKI SIGNAL
- 60 sekundi za ostale assets

---

## 3. DIZAJN & UI/UX

### Tema
- Dark Mode (primary) + Light Mode opcija
- Glassmorphism efekt na svim karticama
- Neonske zelene akcente

### Pozadina
```
background: linear-gradient(135deg, #0a0e27 0%, #1a1f3a 100%);
```

### Glassmorphism Kartice
```
background: rgba(255, 255, 255, 0.05);
backdrop-filter: blur(20px);
border: 1px solid rgba(255, 255, 255, 0.1);
```

### Font
```
font-family: 'Inter', sans-serif;
font-variant-numeric: tabular-nums;
letter-spacing: -0.02em;
```

### Status Boje

| Status | Hex | Efekt |
|---|---|---|
| DVOSTRUKI SIGNAL | #10B981 | Glow pulsira beskonacno |
| ACTION ZONE | #22C55E | Staticno |
| CIKLUS AKTIVAN | #F59E0B | Staticno |
| STOP LOSS | #EF4444 | Staticno |

### Animacije

| Tip | Trajanje |
|---|---|
| Jednostavne (hover, click) | 150ms |
| Kompleksne (modal, tranzicije) | 250ms |
| Smooth (bar fill) | 600ms |
| Glow pulsiranje (DVOSTRUKI SIGNAL) | 2s infinite |

### Hover Efekt na Karticama
```
transform: translateY(-4px);
box-shadow: 0 12px 40px rgba(0, 0, 0, 0.3);
border-color: rgba(255, 255, 255, 0.2);
transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
```

### Active State
```
transform: scale(0.98);
```

### Mobile-First Breakpoints
- Mobile: default
- Tablet: 640px+
- Desktop: 1024px+

---

## 4. ARHITEKTURA NAVIGACIJE

### Layout Ekrana
```
+---------------------------------------+
|  Hamburger (/)       Search (Lupa)    |  <- Gornji red (fiksan)
+---------------------------------------+
|                                       |
|  [Sadrzaj ovisno o tab-u]             |
|                                       |
+---------------------------------------+
|  Watchlist  |  Risk Engine            |  <- Bottom Nav (fiksan)
|      (dot)                            |
+---------------------------------------+
```

### Bottom Navigation
- Watchlist - glavni tab
- Risk Engine - kalkulator pozicija
- Active indicator: Tockica ispod aktivnog taba
- Stil: Glassmorphism pozadina, rounded-t-3xl, floating shadow sa sjenom

### Hamburger Menu - Gornji Lijevi Kut
Dostupan na SVIM tab-ovima. Slide-out panel s lijeva.
Pozadina se zamucuje (blur) kada je menu otvoren.

```
+----------------------------------+
|  X  POSTAVKE                     |
+----------------------------------+
|                                  |
|  Watchlist Management            |
|    -> Kreiraj novu               |
|    -> Import watchlist           |
|                                  |
|  Displej                         |
|    -> Sort by: Status            |
|    -> Dark Mode  [toggle ON]     |
|    -> Light Mode [toggle OFF]    |
|                                  |
|  Profil                          |
|    -> marko@gmail.com (read-only)|
|                                  |
|  Admin Panel  <- SAMO ZA ADMINA  |
|  (posebna zlatna/narancasta boja)|
|                                  |
|  Odjava (crvena boja)            |
|                                  |
+----------------------------------+
```

### Search - Gornji Desni Kut
Globalni search kroz sve dostupne assets.

---

## 5. AUTENTIFIKACIJA & KORISNICI

### Login Flow
```
Email + 6-znamenkasti PIN
        |
Supabase provjeri
        |
OK  -> JWT token -> Dashboard
FAIL -> "Pogresni podaci"
```

### Admin Korisnik
- Email: marko31071985@gmail.com
- Uloga: Automatski admin (definirano u bazi)
- Pristup: Watchlist + Risk Engine + Admin Panel

### Obicni Korisnici
- Dodavanje: SAMO Admin rucno dodaje (kopira email, upise PIN)
- Brisanje: SAMO Admin moze obrisati
- PIN mijenjanje: Planira se za Fazu 2+

### Kako Admin Dodaje Korisnika
```
1. Korisnik posalje email Adminu
2. Admin ide Admin Panel -> User Management
3. Unese email + PIN
4. Klikne Dodaj korisnika
5. Korisnik se prijavljuje sa email + PIN
```

---

## 6. WATCHLIST MANAGEMENT

### Vrste Watchlist-a

#### Default Watchlist (Admin Postavlja)
- SAMO admin moze dodavati/uklanjati assets i parametre
- Korisnici mogu samo gledati i filtrirati
- Moze biti vise default watchlista
- Admin NE MOZE mijenjati custom watchliste korisnika

#### Custom Watchlist (Korisnik Kreira)

Korisnik MOZE:
- Kreirati neogranicen broj custom watchlista
- Dodavati assets iz default liste ILI bilo koji asset koji platforma podrzava
- Uklanjati assets iz svoje watchliste
- Uredivati parametre za asset u SVOJOJ watchlisti
- Preuzeti parametre iz default liste pri dodavanju
- Mijenjati preuzete parametre
- Dodavati nove parametre (npr. P/E ratio)
- Filtrirati koje parametre vidi na kartici (checkbox)
- Preimenovati i brisati watchlistu
- Dijeliti watchlistu s drugim korisnicima

Korisnik NE MOZE:
- Mijenjati tude watchliste
- Mijenjati default watchlistu
- Imati dvije watchliste s istim imenom

### Dropdown za Prebacivanje
```
Default Watchlist (v)
  -> Default Watchlist (samo view)
  -> My Metals (edit)
  -> Energy Plays (edit)
  -------------------------
  -> + Nova Watchlist
  -> Postavke Watchliste
```

---

## 7. ASSET KARTICE

### Pulse Metrike (Vrh Watchlist Stranice)
```
+--------+-----------------+-----------+-----------------+----------+
|  [12]  |    [3]          |   [5]     |    [2]          |  [1]     |
| Ukupno | Dvostruki Signal| Action    | Ciklus Aktivan  | Stop Loss|
| asseta | (zeleno pulsira)| Zone      | (naranzasta)    | (crvena) |
+--------+-----------------+-----------+-----------------+----------+
```

Svaka metrika je u zasebnoj okrugloj kartici sa odgovarajucom bojom.

### Filter Radio Buttons
```
( ) SVE | ( ) DVOSTRUKI SIGNAL | ( ) ACTION ZONE | ( ) CIKLUS AKTIVAN | ( ) STOP LOSS
```

### Izgled Kartice (AssetCardMedium) - NOVI DIZAJN

VAZNO: Svaka kartica ima MINI CHART u pozadini/donjem dijelu!

```
+===================================================+
|  NICK  [H]        $26.23        +1.5%            |
|  Nickel Futures                                   |
|                                                   |
|  [DVOSTRUKI SIGNAL badge]                         |
|                                                   |
|  [===========================---] Proximity Bar   |
|  (zelena/naranzasta/crvena ovisno o statusu)      |
|                                                   |
|  Demand: $26.00 - $28.00                         |
|  Stop Loss: $25.50                               |
|  SFP Invalid: $25.20                             |
|                                                   |
|  [Mini Chart 24h - linijski, suptilan]           |
|                                                   |
|  [Ciklus za 3 dana chip]                         |
+===================================================+
```

### Mini Chart NA Karticama (NOVO!)
- Mala linijska krivulja u donjem dijelu kartice
- Suptilna, ne zauzima previse prostora
- Boja prati status (zelena za signal, narancasta za ciklus, crvena za stop loss)
- Faza 1: Dummy podaci (sinusni val)
- Faza 2+: Stvarni podaci iz yfinance

### Proximity Bar na Karticama (MINI verzija)
- Tanka linija (4-6px visina)
- Boja prati status kartice
- Prikazuje postotak udaljenosti od demand zone

### Status Badge

| Status | Trigger | Boja | Efekt |
|---|---|---|---|
| DVOSTRUKI SIGNAL | Cijena u zoni + Hurst ciklus aktivan | #10B981 | Glow pulsira |
| ACTION ZONE | Cijena unutar demand zone | #22C55E | Staticno |
| CIKLUS AKTIVAN | Hurst ciklus aktivan, ceka zonu | #F59E0B | Staticno |
| STOP LOSS | Cijena ispod stop lossa | #EF4444 | Staticno |

### Confidence Badge
- H (High) - visoka vjerojatnost setup-a
- L (Low) - niza vjerojatnost
- M (Medium) - srednja vjerojatnost

---

## 8. ASSET DETAIL VIEW + RISK CALCULATOR

### Otvaranje
Korisnik klikne na asset karticu -> Slide-up sheet od dna ekrana (85% visine ekrana)

### Layout
```
+------------------------------------------+
|  <- NICK H                           X   |
+------------------------------------------+
|                                          |
|  $26.23  +1.5%   [DVOSTRUKI SIGNAL]     |
|                                          |
|  == BLIZINA CIJENE / PROXIMITY BAR ======|
|                                          |
|           $26.23 (marker)               |
|               |                         |
|  [DZ] [=======|=========-------] [SL]   |
|  $26.00   (zeleno -> crveno)   $25.50   |
|  Demand Zone              Stop Loss     |
|                                          |
|  "Cijena je unutar Demand Zone.         |
|   Blizina Stop Loss-a je 2.86%"         |
|                                          |
|  == PARAMETRI ===========================|
|                                          |
|  zones:                                  |
|    Demand Zone:  $26.00 - $28.00        |
|    Supply Zone:  $30.00 - $31.50        |
|                                          |
|  risk:                                   |
|    Stop Loss:    $25.50                 |
|    SFP Invalid:  $25.20                 |
|                                          |
|  timing:                                 |
|    Cycle:        3 dana                 |
|    Hurst Bias:   Bullish Correction     |
|                                          |
|  == MINI CHART 24h ======================|
|  +--------------------------------------+|
|  |  [Linijski grafik 24h - interaktivan]||
|  +--------------------------------------+|
|  [Otvori TradingView]                   |
|                                          |
|  == RISK CALCULATOR =====================|
|                                          |
|  Capital:      [5000    ]               |
|  Risk %:       [2       ]               |
|  Risk Amount:   $100 (auto-calc)        |
|                                          |
|  Entry Price:  [26.50   ]               |
|  Stop Loss:    [25.50   ] (auto-fill)   |
|  Target Price: [30.00   ]               |
|                                          |
|  -- REZULTATI ---------------------------+
|  Position Size:       200 akcija        |
|  Potential Profit:    $700              |
|  Risk/Reward Ratio:   1:7              |
|                                          |
|  [Izracunaj]        [Reset]             |
|                                          |
+------------------------------------------+
```

### NOVI DIZAJN - Full Proximity Visual (NOVO!)

Umjesto jednostavnog progress bara, u Asset Detail View prikazujemo:

```
BLIZINA CIJENE / PROXIMITY BAR
(glassmorphism kartica)

         $26.23
            |
[DZ] [======|==========----------] [SL]
$26.00   Zeleno -> Zuto -> Crveno   $25.50
Demand Zone                    Stop Loss

Tekst ispod: "Cijena je unutar Demand Zone.
              Blizina Stop Loss-a je 2.86%"
```

Karakteristike Full Proximity Visual-a:
- Gradijent boja: zelena (demand zona) -> zuta (sredina) -> crvena (stop loss)
- Marker/pointer koji pokazuje tocnu poziciju cijene
- DZ oznaka lijevo (Demand Zone)
- SL oznaka desno (Stop Loss)
- Tekst objasnjenje ispod (dinamicki)
- Glassmorphism kartica oko cijelog elementa

### Parametri - Dinamicka Arhitektura
```typescript
interface AssetParameter {
  id: string;
  label: string;
  value: string | number;
  icon?: string;
  category?: 'zones' | 'risk' | 'timing' | 'market' | 'fundamentals';
  format?: 'currency' | 'percentage' | 'text';
}
```

Dodavanje novog parametra = samo dodaj novi objekt u niz. Nema mijenjanja koda!

### Risk Calculator Formule
```
Position Size   = Risk Amount / (Entry - Stop Loss)
                  Npr: $100 / ($26.50 - $25.50) = 100 akcija

Potential Profit = (Target - Entry) x Position Size
                   Npr: ($30 - $26.50) x 100 = $350

Risk/Reward      = Profit / Risk
                   Npr: $350 / $100 = 3.5:1
```

VAZNO: Faza 1 - NEMA SPREMA rezultata. Samo pregled.

---

## 9. RISK ENGINE TAB

### Layout
```
+------------------------------------------+
|  Risk Engine                             |
+------------------------------------------+
|                                          |
|  Odaberi Asset: [dropdown ili search]    |
|                                          |
|  == INPUTS ==============================|
|  Capital:      [5000    ]               |
|  Risk %:       [2       ]               |
|  Risk Amount:   $100 (auto-calc)        |
|                                          |
|  Entry Price:  [26.50   ]               |
|  Stop Loss:    [25.50   ]               |
|  Target Price: [30.00   ]               |
|                                          |
|  == REZULTATI ===========================|
|  Position Size:       200 akcija        |
|  Potential Profit:    $700              |
|  Risk/Reward Ratio:   1:7              |
|                                          |
|  [Izracunaj]        [Reset]             |
|                                          |
+------------------------------------------+
```

VAZNO: Faza 1 - NEMA SPREMA trade setup-a. Planira se Faza 2+.

---

## 10. SEARCH

### Gdje
Ikona lupe u gornjem desnom kutu - dostupna na svim tab-ovima.

### Logika Pretrazivanja
```
Korisnik unese: "NICK" ili "Nickel"

PRIORITET 1 - U korisnikovim watchlistama:
  Default Watchlist -> NICK (prikazi prvi)
  My Metals -> NICK
  Energy Plays -> NICK

PRIORITET 2 - Dostupni (nisu u nijednoj watchlisti):
  Svi assets koje platforma podrzava
```

### UI Rezultati
```
+-------------------------------------+
|  [NICK_____________]    [X]         |
+-------------------------------------+
|  U TVOJIM WATCHLISTAMA:             |
|                                     |
|  Default Watchlist                  |
|  NICK H | $26.23 | +1.5%           |
|                    [Otvori ->]      |
|                                     |
|  DOSTUPNI ZA DODAVANJE:             |
|  NICK | Nickel Futures | $26.23     |
|             [+ Dodaj u watchlistu]  |
+-------------------------------------+
```

### Akcije iz Searcha
- Otvori -> Otvori Asset Detail View
- Dodaj -> Modal: "Dodaj u koju watchlistu?" -> Odabir -> Dodaj

---

## 11. FAVORITES & PINNING

- Favorit - oznaci asset zvjezdicom
- Pin - pinnati asset na vrh liste
- Pin-ani assets prikazuju se PRVI u watchlisti
- Redoslijed pin-anih = pin_order broj u bazi

---

## 12. BULK ACTIONS

### Aktivacija
- Long press na karticu -> pojavljuje se checkbox
- ILI [Select Mode] gumb

### Dostupne Akcije
- Delete selected - obriši iz trenutne watchliste
- Add to watchlist - dodaj u drugu watchlistu
- Pin selected - pinaj na vrh
- Cancel - izadi iz select mode-a

---

## 13. WATCHLIST SHARING & IMPORT

### Koncept
- Korisnik 1 zeli watchlistu od Korisnika 2
- Korisnik 1 daje svoj User ID Korisniku 2
- Korisnik 2 generira Link + QR za Korisnika 1
- Korisnik 1 importira watchlistu

### Pravila Sharinga

| Pravilo | Detalj |
|---|---|
| Tko moze importirati | SAMO korisnik na ciji je ID link kreiran |
| Koliko puta | Multi-import - link ostaje validan |
| Tip kopije | Direktna kopija - bez sinkronizacije |
| Nakon importa | Nova lista ima vlastiti share_id |
| Duplo ime | Vec postoji lista s istim imenom -> STOP |
| Preimenuj | Korisnik moze promijeniti naziv pri importu |
| Revoke | Nema mogucnosti ponistavanja linka |
| Expiration | Nema - link ne istice |

### Link Format
```
https://app.com/import/{shareId}/{targetUserId}
```

---

## 14. ADMIN PANEL

### Pristup
- SAMO korisnik s role = 'admin' (marko31071985@gmail.com)
- Dostupan: Hamburger Menu -> Admin Panel
- Admin NE MOZE pregledavati/mijenjati custom watchliste korisnika

### Sekcija 1: User Management
- Dodaj korisnika (email + PIN)
- Obrisi korisnika
- Pregled svih korisnika

### Sekcija 2: Assets Management
- Dodaj asset rucno
- Uredi asset i parametre
- Obrisi asset
- Bulk import CSV (Faza 2+)
- AI import (Faza 2+)

### Sekcija 3: Parameter Definitions
Admin definira koje parametre korisnici mogu koristiti:
- Kljuc (npr. p_e_ratio)
- Label (npr. P/E Ratio)
- Tip podatka (decimal, integer, string)
- Ikona i kategorija

### Sekcija 4: Default Watchlist Management
- Odaberi koji assets idu u default listu
- Postavi parametre za svaki asset
- Moze biti vise default watchlista

---

## 15. NOTIFIKACIJE - ARHITEKTURA

### 3 Tipa

#### TIP 1: IN-APP
```
Korisnik je u aplikaciji
        |
Toast: "NICK upravo usao u ACTION ZONE!"
        |
Sprema se u notifications tablicu
        |
Notification badge u gornjem kutu
```

#### TIP 2: PUSH NOTIFICATION
```
Korisnik je zatvorio app
        |
Mobilna notifikacija na vrhu ekrana
"NICK - ACTION ZONE!"
        |
Trebam: Firebase Cloud Messaging (FCM)
```

#### TIP 3: EMAIL
```
Korisnik ima email notifikacije ukljucene
        |
Email: "NICK - ACTION ZONE Alert"
        |
Trebam: SendGrid ili Resend + Supabase Triggers
```

Faza 1: Tablice kreirane, arhitektura postavljena, BEZ aktivne implementacije.
Faza 2+: Implementacija Push i Email.

---

## 16. OFFLINE SUPPORT

### Jednostavna Verzija (IndexedDB)

```
ONLINE:
1. Dohvati iz Supabase-a
2. Spremi u IndexedDB
3. Prikazi korisniku

OFFLINE:
1. Nema interneta -> citaj iz IndexedDB
2. Prikazi zadnje poznate podatke
3. Prikazi: "Offline - podaci mogu biti stari"

POVRATAK ONLINE:
1. Automatski azuriraj iz Supabase-a
2. Makni offline indikator
```

Sto se cache-uje: Assets, Watchliste, parametri, User preferences
Sto se NE cache-uje: Live cijene, Chart podaci

---

## 17. DARK/LIGHT MODE

- Korisnik bira KLIKOM (nije automatski)
- Dostupno: Hamburger Menu -> Displej
- Toggle: Dark Mode ON/OFF | Light Mode ON/OFF
- Preference se sprema u user_preferences tablicu
- Primjenjuje se odmah pri kliku

```
Dark Mode  [toggle ON]
Light Mode [toggle OFF]
```

Tailwind config: darkMode: ['class']

---

## 18. CHARTS & GRAFICI

### Mini Chart NA Karticama (NOVO - DODANO!)
- Mala linijska krivulja u donjem dijelu svake asset kartice
- Suptilna, ne zauzima previse prostora
- Boja prati status kartice
- Faza 1: Dummy podaci (sinusni val)
- Faza 2+: Stvarni podaci iz yfinance

### Mini Chart 24h (u Asset Detail View)
- TradingView Lightweight Chart libraru
- Linijski grafik zadnjih 24 sata
- Interaktivan (zoom, hover)
- Faza 1: Dummy podaci
- Faza 2+: Stvarni podaci

### TradingView Full Chart
- Gumb [Otvori TradingView] u Asset Detail View
- Otvara se fullscreen ili nova kartica
- Candlestick, volume, sve mogucnosti

### Zasto Libraru (ne custom)?
- Profesionalan prikaz
- Sve mogucnosti ukljucene
- Testiran od milijuna korisnika
- Brze za razvoj
- Besplatno za osnovnu upotrebu

---

## 19. BAZA PODATAKA - SUPABASE

Pristupni podaci:
- URL: https://cpuhymueefwzozimqdjj.supabase.co
- Anon Key: (u .env.local datoteci - NIKAD na GitHub!)
- RLS: Ukljucen (Enable automatic RLS)

### Strategija: JSONB za Fleksibilnost
Parametri se cuvaju kao JSONB umjesto fiksnih kolona.
Prednost: Dodavanje novih parametara bez mijenjanja strukture baze!

### Sve Tablice

```sql
-- 1. USERS
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR UNIQUE NOT NULL,
  pin_hash VARCHAR NOT NULL,
  role VARCHAR DEFAULT 'user',
  is_active BOOLEAN DEFAULT true,
  created_by UUID REFERENCES users(id),
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- 2. ASSETS
CREATE TABLE assets (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  ticker VARCHAR UNIQUE NOT NULL,
  full_name VARCHAR NOT NULL,
  sector VARCHAR,
  created_by UUID REFERENCES users(id),
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- 3. PARAMETER DEFINITIONS
CREATE TABLE parameter_definitions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  key VARCHAR UNIQUE NOT NULL,
  label VARCHAR NOT NULL,
  description TEXT,
  data_type VARCHAR DEFAULT 'decimal',
  icon VARCHAR,
  category VARCHAR,
  is_default BOOLEAN DEFAULT false,
  created_by UUID REFERENCES users(id),
  created_at TIMESTAMP DEFAULT NOW()
);

-- 4. DEFAULT WATCHLISTS
CREATE TABLE default_watchlists (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR NOT NULL,
  description TEXT,
  created_by UUID REFERENCES users(id),
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- 5. DEFAULT WATCHLIST ASSETS
-- asset_parameters primjer:
-- {
--   "demand_zone_low": 26.00,
--   "demand_zone_high": 28.00,
--   "stop_loss": 25.50,
--   "sfp_invalid": 25.20,
--   "cycle_countdown_days": 3,
--   "hurst_bias": "Bullish Correction",
--   "supply_zone_low": 30.00,
--   "supply_zone_high": 31.50
-- }
CREATE TABLE default_watchlist_assets (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  default_watchlist_id UUID REFERENCES default_watchlists(id) ON DELETE CASCADE,
  asset_id UUID REFERENCES assets(id) ON DELETE CASCADE,
  asset_parameters JSONB DEFAULT '{}',
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- 6. USER CUSTOM WATCHLISTS
CREATE TABLE user_watchlists (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  name VARCHAR NOT NULL,
  description TEXT,
  share_id VARCHAR UNIQUE DEFAULT gen_random_uuid()::text,
  is_shareable BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  CONSTRAINT unique_name_per_user UNIQUE(user_id, name)
);

-- 7. USER WATCHLIST ASSETS
CREATE TABLE user_watchlist_assets (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_watchlist_id UUID REFERENCES user_watchlists(id) ON DELETE CASCADE,
  asset_id UUID REFERENCES assets(id) ON DELETE CASCADE,
  asset_parameters JSONB DEFAULT '{}',
  inherited_from_default_id UUID REFERENCES default_watchlist_assets(id) NULL,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- 8. WATCHLIST SHARES
CREATE TABLE watchlist_shares (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  watchlist_id UUID REFERENCES user_watchlists(id) ON DELETE CASCADE,
  shared_by_user_id UUID REFERENCES users(id),
  shared_to_user_id UUID REFERENCES users(id),
  share_link VARCHAR UNIQUE NOT NULL,
  can_import BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT NOW()
);

-- 9. WATCHLIST IMPORTS
CREATE TABLE watchlist_imports (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  original_watchlist_id UUID REFERENCES user_watchlists(id),
  shared_by_user_id UUID REFERENCES users(id),
  imported_by_user_id UUID REFERENCES users(id),
  new_watchlist_id UUID REFERENCES user_watchlists(id),
  import_date TIMESTAMP DEFAULT NOW()
);

-- 10. USER ASSET PREFERENCES
CREATE TABLE user_asset_preferences (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  asset_id UUID REFERENCES assets(id) ON DELETE CASCADE,
  is_favorite BOOLEAN DEFAULT false,
  is_pinned BOOLEAN DEFAULT false,
  pin_order INTEGER,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  CONSTRAINT unique_user_asset UNIQUE(user_id, asset_id)
);

-- 11. NOTIFICATION PREFERENCES (Arhitektura)
CREATE TABLE notification_preferences (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  in_app_enabled BOOLEAN DEFAULT true,
  push_enabled BOOLEAN DEFAULT false,
  email_enabled BOOLEAN DEFAULT false,
  notify_action_zone BOOLEAN DEFAULT true,
  notify_double_signal BOOLEAN DEFAULT true,
  notify_cycle_complete BOOLEAN DEFAULT true,
  notify_custom_price BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- 12. NOTIFICATIONS (Arhitektura)
CREATE TABLE notifications (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  type VARCHAR NOT NULL,
  title VARCHAR NOT NULL,
  message TEXT,
  asset_id UUID REFERENCES assets(id),
  sent_via_in_app BOOLEAN DEFAULT false,
  sent_via_push BOOLEAN DEFAULT false,
  sent_via_email BOOLEAN DEFAULT false,
  read BOOLEAN DEFAULT false,
  read_at TIMESTAMP NULL,
  created_at TIMESTAMP DEFAULT NOW(),
  expires_at TIMESTAMP NULL
);

-- 13. DEVICE TOKENS (Push - Arhitektura)
CREATE TABLE device_tokens (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  device_id VARCHAR NOT NULL,
  fcm_token VARCHAR NOT NULL,
  platform VARCHAR,
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- 14. USER PREFERENCES
CREATE TABLE user_preferences (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  theme VARCHAR DEFAULT 'dark',
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

---

## 20. STRUKTURA PROJEKTA

```
Aplikacija-za-trejdanje/
|
+-- app/
|   +-- (auth)/
|   |   +-- login/
|   |   |   +-- page.tsx
|   |   +-- layout.tsx
|   |
|   +-- (dashboard)/
|   |   +-- watchlist/
|   |   |   +-- page.tsx
|   |   +-- risk-engine/
|   |   |   +-- page.tsx
|   |   +-- admin/
|   |   |   +-- page.tsx
|   |   |   +-- users/page.tsx
|   |   |   +-- assets/page.tsx
|   |   |   +-- default-watchlist/page.tsx
|   |   +-- layout.tsx
|   |
|   +-- api/
|   |   +-- notifications/route.ts
|   |   +-- search/route.ts
|   |   +-- sync/route.ts
|   |   +-- charts/route.ts
|   |
|   +-- import/
|   |   +-- [shareId]/
|   |       +-- [targetUserId]/
|   |           +-- page.tsx
|   |
|   +-- layout.tsx
|   +-- page.tsx
|   +-- globals.css
|
+-- components/
|   +-- navigation/
|   |   +-- BottomNav.tsx
|   |   +-- HamburgerMenu.tsx
|   |   +-- SettingsPanel.tsx
|   |
|   +-- cards/
|   |   +-- AssetCardMedium.tsx
|   |   +-- AssetCardSmall.tsx
|   |   +-- AssetDetailView.tsx
|   |
|   +-- watchlist/
|   |   +-- WatchlistDropdown.tsx
|   |   +-- PulseMetrics.tsx
|   |   +-- FilterButtons.tsx
|   |   +-- AssetDetailParams.tsx
|   |   +-- ShareWatchlist.tsx
|   |
|   +-- risk/
|   |   +-- RiskCalculator.tsx
|   |
|   +-- search/
|   |   +-- GlobalSearch.tsx
|   |
|   +-- charts/
|   |   +-- MiniChartCard.tsx       <- NOVO (mini chart na karticama)
|   |   +-- MiniChart24h.tsx        <- (u Asset Detail View)
|   |   +-- TradingViewChart.tsx
|   |   +-- ProximityVisual.tsx     <- NOVO (full proximity visual)
|   |
|   +-- notifications/
|   |   +-- NotificationCenter.tsx
|   |   +-- NotificationBadge.tsx
|   |   +-- NotificationPreferences.tsx
|   |
|   +-- offline/
|   |   +-- OfflineIndicator.tsx
|   |
|   +-- theme/
|   |   +-- ThemeToggle.tsx
|   |
|   +-- admin/
|   |   +-- UserManagement.tsx
|   |   +-- AssetManagement.tsx
|   |   +-- ParameterDefinitions.tsx
|   |   +-- DefaultWatchlistManager.tsx
|   |
|   +-- ui/
|       +-- ProximityBar.tsx        <- mini verzija (na karticama)
|       +-- ProximityVisual.tsx     <- full verzija (u detail view) NOVO
|       +-- StatusBadge.tsx
|       +-- Button.tsx
|       +-- Modal.tsx
|       +-- BottomSheet.tsx
|       +-- QRCodeDisplay.tsx
|
+-- lib/
|   +-- supabase.ts
|   +-- auth.ts
|   +-- notifications.ts
|   +-- offline.ts
|   +-- cache.ts
|   +-- theme.ts
|   +-- designTokens.ts
|   +-- validators.ts
|
+-- hooks/
|   +-- useWatchlist.ts
|   +-- useAssets.ts
|   +-- useSearch.ts
|   +-- useTheme.ts
|   +-- useOffline.ts
|
+-- types/
|   +-- asset.ts
|   +-- watchlist.ts
|   +-- user.ts
|   +-- notifications.ts
|
+-- docs/
|   +-- PHASE_1_SPECIFICATION.md
|
+-- public/
|   +-- icons/
|
+-- package.json
+-- tsconfig.json
+-- tailwind.config.ts
+-- next.config.ts
+-- postcss.config.js
+-- .env.local  <- NIKAD na GitHub!
```

---

## 21. INICIJALNI TESTNI PODACI

### Asset 1: NICK
```json
{
  "ticker": "NICK",
  "full_name": "Nickel Futures",
  "sector": "Energija",
  "status": "DVOSTRUKI SIGNAL",
  "confidence": "H",
  "current_price": 26.23,
  "price_change_percent": 1.5,
  "parameters": {
    "demand_zone_low": 26.00,
    "demand_zone_high": 28.00,
    "stop_loss": 25.50,
    "sfp_invalid": 25.20,
    "cycle_countdown_days": 3,
    "hurst_bias": "Bullish Correction",
    "supply_zone_low": 30.00,
    "supply_zone_high": 31.50
  }
}
```

### Asset 2: HAL
```json
{
  "ticker": "HAL",
  "full_name": "Halliburton",
  "sector": "Energija",
  "status": "ACTION ZONE",
  "confidence": "M",
  "current_price": 18.50,
  "price_change_percent": 2.3,
  "parameters": {
    "demand_zone_low": 18.00,
    "demand_zone_high": 19.50,
    "stop_loss": 17.00,
    "sfp_invalid": 16.80,
    "cycle_countdown_days": 7,
    "hurst_bias": "Bullish",
    "supply_zone_low": 20.00,
    "supply_zone_high": 21.00
  }
}
```

---

## 22. TRGOVACKA PRAVILA & LOGIKA

### Detekcija Zona (Supply/Demand)
- Cilj: Traziti ulaze ISKLJUCIVO izvan "Fair Value" zona
- Visoko vjerojatne zone: brz odlazak cijene, malo vremena na nivou, netaknuti nivoi
- Oscilatori (RSI, MACD): relevantni ISKLJUCIVO kada je cijena vec unutar kljucnih nivoa

### Swing Failure Pattern (SFP) & Invalidation
- SFP = cijena probije vrh/dno, pokupli likvidnost, ali NE ZATVORI izvan tog nivoa
- Stop Loss tik iznad/ispod fitilja
- SFP Invalidation Point = tocka ponistenja teze

### Status Logika
```
DVOSTRUKI SIGNAL = cijena u demand zoni + Hurst ciklus aktivan
ACTION ZONE      = cijena unutar demand zone
CIKLUS AKTIVAN   = Hurst ciklus aktivan, ceka zonu
STOP LOSS        = cijena ispod stop loss razine
```

### Taktike (Faza 2+)
- Laddering: Fibonacci 0.618 i 0.786 rasporedivanje naloga
- Compounding: Dodavanje na dobitni trade sa novim S/D set-upom
- Averaging Down: Visoko rizicno - samo za masivni support

---

## 23. ROADMAP - FAZA 2+

### Faza 2
- [ ] Live cijene (yfinance) - Smart Refresh 10s/60s
- [ ] Push notifikacije (Firebase)
- [ ] Email notifikacije (SendGrid)
- [ ] PIN mijenjanje od strane korisnika
- [ ] Bulk import CSV
- [ ] Laddering kalkulator
- [ ] Sprema trade setup-a
- [ ] Price alerts

### Faza 3+
- [ ] AI import assets
- [ ] Compounding kalkulator
- [ ] Community watchliste
- [ ] Multi-admin podrska
- [ ] Export (PDF/CSV)
- [ ] Sync importane watchliste

---

## VAZNE NAPOMENE

1. .env.local NIKAD na GitHub!
2. Supabase RLS je UKLJUCEN
3. Admin email definiran u bazi
4. Risk Calculator NE SPREMA rezultate u Fazi 1
5. Sharing link - primatelj mora biti LOGIRAN
6. Sve boje u lib/designTokens.ts
7. Parametri su data-driven - dodaj u parameter_definitions bez mijenjanja koda

## NOVI DIZAJN ELEMENTI (Verzija 2.0)

### Dodano u ovoj verziji:
1. MINI CHART NA KARTICAMA - svaka asset kartica ima mali linijski grafik
2. FULL PROXIMITY VISUAL - u Asset Detail View:
   - Gradijent linija (zelena -> zuta -> crvena)
   - Marker/pointer za trenutnu cijenu
   - DZ oznaka (Demand Zone)
   - SL oznaka (Stop Loss)
   - Tekst objasnjenje ("Cijena je unutar Demand Zone. Blizina SL je 2.86%")
   - Glassmorphism kartica oko cijelog elementa
3. HAMBURGER MENU - toggle za Dark/Light mode sa vizualnim toggle switchevima

---

Dokument azuriran: Svibanj 2026
Verzija: 2.0 - Dodani novi dizajn elementi
Sljedeci korak: MVP kodiranje - Faza 1
