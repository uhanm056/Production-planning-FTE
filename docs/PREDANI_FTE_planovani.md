# Předání: Plánování FTE z volume – Planá

Stav k 20. 10. 2026. Pokračování v Claude Code.

## Cíl
Každý týden před tvorbou rozpisu směn importovat nové odvolávky od logistiky. Aplikace spočítá potřebu lidí po linkách a doporučí přesuny, přesčas/víkend nebo agenturu. Řetězec: odvolávky → volume po lince → směny → OP → porovnání s lidmi k dispozici → doporučení → (později) DLE a € dopad.

## Soubory
- `FTE_Planovani_Plana.html` – hlavní aplikace (single-file, localStorage, SheetJS z cdnjs lazy). Pohledy: Plán týdne, Doporučení, Výhled 12 t., Kmenová data, Import, Metodika. Export do Excelu.
- `FTE_model_Plana_CW43.xlsx` – Excel verze modelu (Převodník, Normy, Parametry, Volume po lince, Potřeba FTE, Linky z rozpisů, Kontrola jmen).
- Návrh postupu (dokument): https://claude.ai/code/artifact/c12d9868-8f67-452e-bb26-527073730bbf
- Vstupy: `odvolavky_prehled_CW43-2026.xlsx` (list Díly), rozpisy CW43 po linkách (OV51/64, IMM, SK336, Slush, MFA2, Mbeam, G463 aktual, GBDP1, Foam, Prefix).

## Rozhodnutí a fakta
- „Bez projektu“ v odvolávkách = HOP díly a nakupovaný materiál → do volume se nepočítá.
- Projekty a kategorie jsou přiřazené; převodník projekt × kategorie → linka doplnil Milan (je v aplikaci jako výchozí kmenová data).
- G463 Prefix L1 a L2 vždy zvlášť: L1 = DP díly s „LH“ v popisu, L2 = „RH“ (předpoklad, ověřit). Norma 140 ks/8h, 20 OP/linka/směnu, OEE nastaveno 100 % (140 = cílový výstup – ověřit).
- Počty lidí = OP za den z rozpisů CW43 (jen přítomní, bez TL/TR). Milan si není jistý, že sedí → doplní/ověří později, model běží bez nich.
- Foam: K2 (3 směny, 25 OP/den + Scoring 6), K1 (jen ranní 12h, 7 OP). Bez vazby na odvolávky.
- Lidé ve více rozpisech: Tsiple Maryna (OV51/64, SK336, MFA2), Biro Kristina (OV51/64, MBEAM), Virich Oleksandr (G463 Assy, Prefix) → podklad pro „Sdílí lidi s“, ověřit.
- Normy ostatních linek chybí – owner David Varhan. Nic nevymýšlet.
- W520 „bez kategorie“ (2 díly, 6 ks za celý horizont CW43) – nejasné, kam patří. Záměrně nenamapováno, v aplikaci zůstává mezi nenamapovanými.

## Pravidla (skill provozni-aplikace)
Single-file HTML, cdnjs jen, detekce sloupců podle hlavičky, čeština s diakritikou, navy #003B7A, RAG W_n vs W_n−1 (±5 %), G463 L1/L2 zvlášť, localStorage v try/catch, žádná vymyšlená data.

## Další kroky
1. Doplnit normy + OP standard (Slush, MBEAM, G463 Assembly, SK336/W206 první).
2. Ověřit lidi k dispozici (Gemba / potvrzení TL) a „Sdílí lidi s“.
3. Import rozpisů směn přímo do aplikace (lidé k dispozici automaticky místo ručně).
4. Napojení Summary PLANA1 a DLE (skutečnost vs. plán, gap v €), sazby DL/IDL/agentura.
5. Skill matrix pro přesuny (kdo může na kterou linku), víkendový rozpis.
6. Případně sdílení přes SharePoint / Firebase RTDB.
