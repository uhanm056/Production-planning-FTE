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

## Online verze (vlastní Firebase projekt, zabezpečení jako MEC Analyzer)

Samostatný Firebase projekt (odděleně od MEC), stejná metoda přihlášení a zabezpečení jako MEC:
e-mail + heslo s ověřením e-mailu nebo účet Microsoft, Realtime Database, seznam přístupů a správců v databázi.
Po každém začlenění do `main` workflow `.github/workflows/firebase-deploy.yml` zkopíruje `index.html` do `site/`
a nasadí ho na Firebase Hosting (`https://<projekt>.web.app`) spolu s pravidly `database.rules.json`.

- Data: `fte/data/{kolekce}/{id}` = `{j: JSON, at, by}` (`by` musí být e-mail přihlášeného).
- Přístupy: `fte/allow/{email}` = `"edit"` (manažeři, TL) nebo `"view"` (jen čtení); správci `fte/admin/{email}` = `true`
  (e-mail malými písmeny, tečky nahrazené čárkou: `jan,novak@yanfeng,com`). Správce přidává `allow` přímo v aplikaci
  (Import → Online verze → Přístupy); `admin` jen ve Firebase konzoli, aplikace do něj zapsat nemůže.
- Číst smí ověřený e-mail ze seznamu (nebo účet Microsoft) a správce, zapisovat `edit` a správce. Web neobsahuje žádná data.
- Verze na claude.ai (artefakt) a soubor HTML bez přihlášení fungují dál; po přihlášení soubor pracuje se stejnými online daty.
- Konfigurace projektu je v `index.html` (`FIREBASE_CONFIG`); do té doby jde vložit v Import → Online verze (uloží se v prohlížeči).

### Jednorázové zprovoznění

1. https://console.firebase.google.com → **Add project** → název `fte-plana` (ID projektu = adresa webu `fte-plana.web.app`), Analytics vypnout.
2. **Build → Realtime Database → Create database** → umístění `europe-west1`, *locked mode*.
3. **Build → Authentication → Get started → Email/Password** zapnout (volitelně **Microsoft** jako v MEC);
   **Settings → Authorized domains** obsahuje `<projekt>.web.app` automaticky.
4. **Realtime Database → Data**: přidat `fte` → `admin` → `vas,email@yanfeng,com` = `true` (malými písmeny, tečky → čárky).
5. **Build → Hosting → Get started** (jen proklikat).
6. **Project settings → General → Your apps → Web app (</>)** → zkopírovat objekt `firebaseConfig` (s `databaseURL`)
   a poslat ho do `index.html` jako `FIREBASE_CONFIG`.
7. **Project settings → Service accounts → Generate new private key** → GitHub → tento repozitář → **Settings → Secrets and
   variables → Actions** → secret `FIREBASE_SERVICE_ACCOUNT` = celý JSON (soubor pak smazat). Jiné ID projektu než `fte-plana`
   → proměnná repozitáře `FIREBASE_PROJECT`.
8. GitHub → **Actions → Nasazení na Firebase → Run workflow** (nebo začlenit do `main`).
9. Otevřít předvyplněný soubor `FTE_Planovani_Plana_CW43.html`, založit si účet / přihlásit se jako správce → **Import → Online verze →
   Nahrát data z tohoto souboru online** (jen když je online databáze prázdná). Pak v **Přístupy** přidat manažery a TL.
