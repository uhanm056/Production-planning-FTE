# Plánování FTE z volume – Planá

Týdenní plánování potřeby lidí po linkách z odvolávek logistiky:
odvolávky → volume po lince → směny → OP → porovnání s lidmi k dispozici → doporučení (přesuny, přesčas/víkend, agentura).

## Obsah
- `index.html` – hlavní aplikace (single-file, data v localStorage, SheetJS z cdnjs). Stačí otevřít v prohlížeči. Importuje přehled odvolávek (list Díly) a rozpisy směn (šablona `CW_xx_<linka>.xlsx`, list CWxx) → lidé k dispozici po linkách.
- `model/FTE_model_Plana_CW43.xlsx` – Excel verze modelu (Převodník, Normy, Parametry, Volume po lince, Potřeba FTE, Linky z rozpisů, Kontrola jmen).
- `docs/PREDANI_FTE_planovani.md` – předávací dokument: rozhodnutí, otevřené body a další kroky.

## Pravidla
Vizuál podle MEC Analyzer Planá (navy header s logem, úvodní dlaždice, pilulková navigace). Single-file HTML, knihovny jen z cdnjs, detekce sloupců podle hlavičky, čeština s diakritikou, navy `#003B7A`, RAG W_n vs W_n−1 (±5 %), G463 Prefix L1/L2 vždy zvlášť, localStorage v try/catch, žádná vymyšlená data (chybějící normy zůstávají prázdné).
