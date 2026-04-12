# Vibe Coding Workshop — od PRD k deploynuté appce

**Konference:** Hack Your Way 2026, Brno  
**Datum:** 28. dubna 2026 (úterý, workshopový den)  
**Délka:** 3 hodiny  
**Formát:** Hands-on, účastníci pracují na vlastních notebookách  
**Nástroje:** Claude Code  
**Stack:** Next.js (App Router) + Supabase + Tailwind CSS + Vercel

---

## Prerekvizity (email účastníkům min. týden předem)

### Povinné

- Node.js 18+ nainstalovaný (`node -v` funguje)
- Git nainstalovaný
- GitHub účet
- Supabase účet (free tier) — https://supabase.com
- Vercel účet (free tier, propojený s GitHubem) — https://vercel.com
- Claude Code nainstalovaný a funkční (vyžaduje Claude Pro/Max subscription) — https://docs.claude.com/en/docs/claude-code

### Ověření před workshopem

Pošli účastníkům checklist:

1. `node -v` → vidíš verzi 18+
2. `git --version` → funguje
3. Na supabase.com se přihlásíš a vidíš dashboard
4. Na vercel.com se přihlásíš a vidíš dashboard
5. Claude Code funguje (`claude` v terminálu → reaguje na prompty)

### Nice to have

- VS Code (jako fallback editor)
- Základní znalost terminálu (cd, ls, npm)

---

## Workshop Kit (distribuce materiálů)

Účastníci na začátku workshopu naklonují připravený `workshop-kit` repozitář:

```bash
git clone https://github.com/[TBD]/workshop-kit.git moje-appka
cd moje-appka
claude
```

Kit obsahuje:

```
workshop-kit/
├── CLAUDE.md                        # Projektový kontext pro Claude Code
├── README.md                        # Quick start pro účastníky
├── .gitignore                       # Připravený gitignore
└── .claude/
    └── commands/
        ├── prd.md                   # /project:prd — PRD agent
        ├── scaffold.md              # /project:scaffold — Scaffold agent
        ├── deploy.md                # /project:deploy — Deploy agent
        └── feature.md              # /project:feature — Feature agent
```

Agenti jsou implementovaní jako Claude Code custom commands (`.claude/commands/`).
Účastník je spouští přes `/project:prd`, `/project:scaffold` atd.
Každý agent navazuje na předchozí — PRD agent na konci řekne "spusť /project:scaffold",
scaffold agent na konci řekne "spusť /project:deploy" atd.

---

## Struktura workshopu

### Blok 1: Intro (10 min)

**Cíl:** Účastníci chápou, co budou dělat a proč.

**Co říct:**

- Co je vibe coding — místo psaní kódu popisuješ, co chceš, a AI to generuje
- Proč product engineering — nezačínáme kódem, začínáme produktem. Nejdřív CO a PROČ, pak JAK
- Co dnes postavíme — každý si vybere svou CRUD webovou appku, napíše si PRD, vygeneruje kód, a deployne na internet
- Stack: Next.js + Supabase + Tailwind + Vercel — proč tohle combo (managed, free tier, AI to zná dobře)

**Demo (2 min):**

- Ukázat hotovou appku (připravit předem) — "tohle vzniklo za 30 minut čistě přes prompty"
- Ukázat živou URL na Vercelu — "a běží to na internetu"

---

### Blok 2: Product Discovery & PRD (20 min)

**Cíl:** Každý účastník má mini PRD pro svůj projekt.

**Co říct (3 min):**

- Než začneme kódovat, musíme vědět CO stavíme
- PRD nemusí být dokument na 10 stran — stačí jedna stránka, která jasně říká: problém, uživatel, co je in scope, datový model
- AI nám pomůže i s tímhle krokem

**Příklady projektů (ukázat na slide/projektoru):**

- Todo list s kategoriemi a prioritami
- Rezervační systém (místnosti, časy, jména)
- Tracker výdajů (částka, kategorie, datum)
- Habit tracker (návyky, denní check-in)
- Jednoduchý CRM (kontakty, poznámky, status)
- Knihovna receptů (ingredience, postup, tagy)

**PRD agent:**

Účastníci nemusí sami vymýšlet prompt. Místo toho použijí připraveného PRD agenta
(viz `prd-agent-prompt.md`), který je provede celým procesem krok po kroku —
ptá se jednu otázku po druhé a na konci vygeneruje hotové PRD včetně SQL.

Na projektoru ukázat, jak vypadá konverzace s agentem (připravit demo předem).

**Aktivita (15 min):**

1. Účastníci spustí Claude Code ve svém `workshop-kit` adresáři
2. Zadají příkaz `/project:prd`
3. PRD agent se jich bude ptát krok po kroku — problém, uživatel, akce, scope, datový model
4. Na konci dostanou hotové PRD včetně SQL a Mermaid ER diagramu (uloží se jako `PRD.md`)

**Checkpoint:** Zeptat se — "má každý PRD s datovým modelem? Kdo potřebuje help?"

---

### Blok 3: Init prompt & scaffold (25 min)

**Cíl:** Každý má lokálně běžící appku.

**Co říct (2 min):**

- Teď vezmeme PRD a přeměníme ho na fungující kód
- Dva kroky: (1) založíme databázi v Supabase, (2) vygenerujeme Next.js appku

**Krok 1 — Supabase setup (8 min):**

Na projektoru ukázat:

1. V Supabase dashboardu → New Project → pojmenovat, vybrat region (eu-central-1), nastavit heslo
2. Počkat než se projekt vytvoří
3. Vzít SQL z PRD a pustit ho v SQL Editoru (Supabase dashboard → SQL Editor)
4. Settings → API → zkopírovat `Project URL` a `anon/public key`

**Krok 2 — Init prompt (15 min):**

Init prompt šablona:

```
Vytvoř mi novou webovou aplikaci podle tohoto PRD:

[VLOŽIT CELÉ PRD]

Technické požadavky:
- Next.js 14+ s App Routerem
- TypeScript
- Tailwind CSS pro styling
- Supabase jako databáze (tabulky už existují v Supabase)
- Použij @supabase/ssr pro klienta
- Všechny CRUD operace přes Supabase client (ne custom API routes)

Začni tím, že:
1. Inicializuješ Next.js projekt (npx create-next-app@latest)
2. Nainstaluješ závislosti (@supabase/supabase-js, @supabase/ssr)
3. Vytvoříš .env.local šablonu s NEXT_PUBLIC_SUPABASE_URL a NEXT_PUBLIC_SUPABASE_ANON_KEY
4. Vytvoříš Supabase client utility
5. Implementuješ základní CRUD UI — seznam položek, formulář pro přidání, možnost smazat

Udělej to jednoduché a funkční. Žádné over-engineering.
```

**Aktivita:**

1. Účastníci vloží své PRD do init promptu a pustí
2. AI vygeneruje projekt
3. Vyplní `.env.local` svými Supabase klíči
4. `npm install && npm run dev`
5. Otevřou localhost:3000 — vidí svou appku

**Troubleshooting (mít připravené):**

- "npm run dev nefunguje" → zkontrolovat Node verzi, smazat node_modules a znovu install
- "Supabase connection error" → zkontrolovat .env.local, ověřit že klíče jsou správné
- "Tabulka neexistuje" → zkontrolovat že SQL proběhl v Supabase SQL Editoru
- "RLS policy error / prázdná data" → v Supabase dočasně vypnout RLS (pro workshop stačí)

**Checkpoint:** "Vidí každý svou appku v prohlížeči? Kdo potřebuje help?"

---

### Blok 4: První deploy (20 min)

**Cíl:** Každý má svou appku živě na internetu — i když je zatím ošklivá a basic.

**Co říct (2 min):**

- Deployujeme hned teď — nečekáme až bude appka "hotová"
- Chci, abyste viděli, jak jednoduché je dostat kód na internet
- A hlavně — až budeme dál vylepšovat, uvidíte jak jednoduchý je redeploy

**Postup (ukázat na projektoru):**

1. Git init + commit:
```bash
git init && git add . && git commit -m "initial version"
```

2. Vytvořit GitHub repo a pushnout:
```bash
gh repo create my-app --public --source=. --push
```
(nebo přes GitHub web, pokud nemají `gh` CLI)

3. Na vercel.com → New Project → Import z GitHubu
4. Přidat environment variables: `NEXT_PUBLIC_SUPABASE_URL` a `NEXT_PUBLIC_SUPABASE_ANON_KEY`
5. Deploy

**Alternativa přes Vercel CLI:**
```bash
npx vercel --yes
# přidat env variables přes Vercel dashboard
npx vercel --prod
```

**Checkpoint:** "Má každý živou URL? Kdo potřebuje help?"

Moment oslavy — každý má appku na internetu. Může si ji otevřít na mobilu, poslat kamarádovi.

---

### Přestávka (10 min)

---

### Blok 5: Build & iterate (50 min)

**Cíl:** Účastníci iterativně vylepšují svou appku a průběžně redeployují.

**Co říct (3 min):**

- Tohle je hlavní blok — teď budete stavět
- Já budu obcházet a pomáhat
- Průběžně budu na projektoru ukazovat zajímavé situace
- Pokaždé když máte něco nového hotového: commit + push → Vercel automaticky redeployuje
- Cíl: na konci máte appku, kterou chcete ukázat ostatním

**Milníky (napsat na tabuli/slide):**

1. ~~Fungující CRUD~~ ✓ (máte z předchozího bloku)
2. ~~Živá URL~~ ✓ (máte z deploye)
3. Hezčí UI (lepší layout, barvy, spacing)
4. Extra feature (filtrování, řazení, kategorie, vyhledávání...)
5. Auth (login/signup)
6. UX polish (loading states, prázdné stavy, validace)

**Redeploy workflow (ukázat na projektoru):**

```bash
git add . && git commit -m "popis zmeny" && git push
```
To je všechno. Vercel automaticky buildne a deployuje novou verzi. Ukázat v Vercel dashboardu jak se deployment spustí.

**Promptovací vzory (ukázat průběžně na projektoru):**

Přidání nové feature:
```
Přidej možnost filtrovat [položky] podle [kritérium].
Přidej dropdown/select s možnostmi [A, B, C] a po výběru se seznam přefiltruje.
```

Vylepšení UI:
```
Vylepši design celé appky. Chci moderní, čistý look:
- Card-based layout pro jednotlivé položky
- Lepší typografie a spacing
- Hover efekty na interaktivních prvcích
- Konzistentní barevná paleta
```

Auth:
```
Přidej do appky autentizaci přes Supabase Auth:
1. Login/signup stránka s emailem a heslem
2. Chráněné routes — nepřihlášený uživatel vidí jen login
3. Logout tlačítko v navigaci
4. Data jsou filtrovaná podle přihlášeného uživatele (přidej user_id sloupec pokud chybí)
```

Debugging:
```
Tohle nefunguje: [popis problému]
Tady je error z konzole: [vložit error]
Oprav to.
```

Když AI bloudí:
```
Stop. Tohle je špatný směr. Vrať se k předchozí fungující verzi a místo toho [jednodušší přístup].
```

**Co dělat jako lektor:**

- Obcházet, dívat se lidem přes rameno
- Pomáhat s konkrétními problémy
- Když vidíš zajímavý pattern (dobrý prompt, typický fail), ukázat na projektoru
- Každých ~15 min krátce zastavit a ukázat tip nebo řešení častého problému
- Připomínat: "commitněte a pushněte, ať se vám to deployuje"
- Povzbuzovat experimentování — "zkuste to, nejhorší co se stane je že to zrevertujete"

**Typické problémy a jak je řešit:**

| Problém | Řešení |
|---------|--------|
| AI generuje příliš komplexní kód | "Zjednoduš to. Chci nejjednodušší možné řešení." |
| Appka se rozbila po posledním promptu | `git diff` → vrátit změny → prompt jinak |
| Supabase RLS blokuje requesty | Vypnout RLS pro workshop (ukázat v dashboardu) |
| Styling je rozbitý | "Oprav layout. Tady je screenshot jak to vypadá: [popis]" |
| TypeScript errory | "Oprav TypeScript errory. Tady je výstup z npm run dev: [error]" |
| Vercel build failed | Zkontrolovat build log ve Vercel dashboardu, fix lokálně, push znovu |

---

### Přestávka (10 min)

---

### Blok 6: Finální sprint + show & tell (15 min)

**Finální push (5 min):**

- "Poslední commit a push — ať máte finální verzi deploynutou"
- Kdo ještě nemá auth, ať zkusí přidat alespoň to
- Kdo má všechno, ať zkusí poslední vylepšení

**Show & tell (5 min):**

- Kdo chce, ukáže svou appku na projektoru (sdílí živou Vercel URL)
- Max 1 minuta na osobu — co to je, co bylo nejtěžší, kolik deployů stihli

**Reflexe (5 min):**

Otázky pro diskuzi:

- Co vás překvapilo? (pozitivně i negativně)
- Kde AI pomohlo nejvíc? Kde překáželo?
- Jak se lišil první deploy od posledního? Co se změnilo?
- Použili byste tohle v práci? Na co?

**Key takeaways (říct na závěr):**

- Vibe coding je silný pro prototypování a MVP — od nápadu k běžící appce za hodiny, ne týdny
- Začněte produktem (PRD), ne kódem — AI potřebuje jasné zadání stejně jako lidský vývojář
- Deployujte brzo a často — netlačte na "hotovo", netlačte na dokonalost
- Iterujte v malých krocích — jeden prompt = jedna věc, jeden commit = jedna změna
- Nebojte se experimentovat — git vám vždycky umožní vrátit se zpět
- Znát limity — vibe coding není náhrada za software engineering u komplexních systémů

---

## Materiály k přípravě

### Před workshopem

- [ ] Připravit demo appku (hotovou, deploynutou na Vercelu)
- [ ] Připravit slides: intro, příklady projektů, stack diagram, milníky
- [ ] Připravit cheat sheet s prompt šablonami (vytisknout nebo sdílet jako PDF)
- [ ] Připravit troubleshooting guide
- [ ] Otestovat celý flow end-to-end na čistém stroji
- [ ] Připravit fallback Supabase projekt (pro účastníky, kterým nefunguje registrace)
- [ ] Poslat email s prerekvizitami min. 7 dní předem
- [ ] Poslat reminder email 2 dny předem

### Cheat sheet pro účastníky (vytisknout)

```
VIBE CODING WORKSHOP — CHEAT SHEET

== SUPABASE ==
Dashboard:        https://supabase.com/dashboard
SQL Editor:       Dashboard → SQL Editor → New Query
API klíče:        Settings → API → Project URL + anon key
Auth:             Authentication → Users

== PŘÍKAZY ==
Nový projekt:     npx create-next-app@latest my-app
Spustit:          npm run dev
Deploy:           npx vercel --prod

== ENV PROMĚNNÉ (.env.local) ==
NEXT_PUBLIC_SUPABASE_URL=https://xxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJ...

== PROMPTOVACÍ TIPY ==
Buď konkrétní:    "Přidej tlačítko pro smazání u každé položky v seznamu"
Dej kontext:      "Mám tabulku 'todos' se sloupci id, title, done. Přidej..."
Malé kroky:       Jeden prompt = jedna feature/fix
Když bloudí:      "Stop. Vrať se k jednoduššímu řešení."
Debug:            "Nefunguje X. Error: [vlož error]. Oprav to."
```

### Záložní plán

- Pokud Wi-Fi nefunguje → mít mobilní hotspot
- Pokud Supabase/Vercel má výpadek → mít lokální SQLite alternativu připravenou
- Pokud někdo nemá Claude Code → ChatGPT web + manuální copy-paste kódu (funguje, jen je pomalejší)
- Pokud někdo má Windows bez Nodu → Replit nebo StackBlitz jako fallback
