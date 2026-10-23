# Plánování FTE z volume – Planá

Týdenní plánování potřeby lidí po linkách z odvolávek logistiky:
odvolávky → volume po lince → směny → OP → porovnání s lidmi k dispozici → doporučení (přesuny, přesčas/víkend, agentura).

## Obsah
- **Sdílená verze pro tým:** https://claude.ai/artifact/ET3UZWtGNRiDiX32ysucdw (stejná data pro všechny, rozpis týdne se dělá jen tam).
- `index.html` – hlavní aplikace (single-file, data v localStorage, SheetJS z cdnjs). Stačí otevřít v prohlížeči. Importuje přehled odvolávek (list Díly) a rozpisy směn (šablona `CW_xx_<linka>.xlsx`, list CWxx) → lidé k dispozici po linkách. Monitoring 12h směn a víkendové práce (náhrada sešitů Kontrola 12h směn a Víkendový rozpis).
- `model/FTE_model_Plana_CW43.xlsx` – Excel verze modelu (Převodník, Normy, Parametry, Volume po lince, Potřeba FTE, Linky z rozpisů, Kontrola jmen).
- `docs/PREDANI_FTE_planovani.md` – předávací dokument: rozhodnutí, otevřené body a další kroky.

## Pravidla
Vizuál podle MEC Analyzer Planá (navy header s logem, úvodní dlaždice, pilulková navigace). Single-file HTML, knihovny jen z cdnjs, detekce sloupců podle hlavičky, čeština s diakritikou, navy `#003B7A`, RAG W_n vs W_n−1 (±5 %), G463 Prefix L1/L2 vždy zvlášť, localStorage v try/catch, žádná vymyšlená data (chybějící normy zůstávají prázdné).

## Online verze (Firebase – stejný projekt jako MEC Analyzer)

Web: **https://fte-plana.web.app** – přihlášení stejným účtem jako do MEC (e-mail + heslo, nebo účet Microsoft).
Po každém začlenění do `main` workflow `.github/workflows/firebase-deploy.yml` zkopíruje `index.html` do `site/`
a nasadí ho na Firebase Hosting spolu s pravidly `firestore.rules`.

- Data jsou ve **Firestore** (`fte/main/{kolekce}/{id}`, dokument = `{j: JSON, at, by}`); MEC používá Realtime Database,
  takže nasazení FTE nemění jeho data ani pravidla.
- Přístupy: `fte_acl/{email malými písmeny}` s polem `role` = `admin` (správce), `edit` (manažeři, TL), `view` (jen čtení).
  Správce přidává `edit` / `view` přímo v aplikaci (Import → Online verze → Přístupy); `admin` jen ve Firebase konzoli.
- Web neobsahuje žádná data – jména a čísla jsou jen ve Firestore za přihlášením.
- Verze na claude.ai (artefakt) a soubor HTML bez přihlášení fungují dál; po přihlášení soubor pracuje se stejnými online daty.

### Jednorázové zprovoznění (Firebase konzole projektu `mec---action-plan`)

1. **Build → Firestore Database → Create database** → umístění `eur3 (europe-west)` → *production mode*.
2. **Build → Hosting → Add another site** → ID webu `fte-plana` (když je obsazené, zvolte jiné a změňte `site` ve `firebase.json`).
3. **Authentication → Settings → Authorized domains → Add domain** → `fte-plana.web.app` (kvůli přihlášení účtem Microsoft).
4. **Firestore → Data → Start collection** `fte_acl` → ID dokumentu = váš e-mail z MEC malými písmeny → pole `role` (string) = `admin`.
5. GitHub → tento repozitář → **Settings → Secrets and variables → Actions → New repository secret**:
   `FIREBASE_SERVICE_ACCOUNT` = stejný JSON klíč servisního účtu jako v repozitáři MEC
   (nemáte ho: Firebase konzole → Project settings → Service accounts → Generate new private key; účet má roli Firebase Admin).
6. GitHub → **Actions → Nasazení na Firebase → Run workflow**.
7. Otevřete předvyplněný soubor `FTE_Planovani_Plana_CW43.html`, přihlaste se jako správce → **Import → Online verze →
   Nahrát data z tohoto souboru online** (jen když je online databáze prázdná). Pak v **Přístupy** přidejte manažery a TL.
