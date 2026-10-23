# Předání: Plánování FTE z volume – Planá

Stav k 20. 10. 2026. Pokračování v Claude Code.

## Cíl
Každý týden před tvorbou rozpisu směn importovat nové odvolávky od logistiky. Aplikace spočítá potřebu lidí po linkách a doporučí přesuny, přesčas/víkend nebo agenturu. Řetězec: odvolávky → volume po lince → směny → OP → porovnání s lidmi k dispozici → doporučení → (později) DLE a € dopad.

## Soubory
- **Sdílená aplikace (claude.ai, databáze pro všechny): https://claude.ai/artifact/ET3UZWtGNRiDiX32ysucdw** – publikuje se z `index.html` (bez obalu `<html>/<head>/<body>`), capabilities `db` + `downloads`. Databáze: `app/master`, `app/roster`, `app/dle`, `app/monitor` (sazby a seznamy 12h/víkend), `exports/<rok-týden>`, `people/<id>`, `plans/<rok-týden>`, `h12/<rok-týden>` (řádky 12h směn), `weekend/<datum soboty>` (rozpis, potvrzení oddělení, neplánovaní).
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
- Import rozpisů: OP za den = unikátní jména bez TL/TR; „A + B“ = 2 lidé, „A/B“ = 1 sdílená pozice; jména bez malých písmen (QS, TL IMM) se nepočítají; nepřítomnosti se jen zobrazí, neodečítají. Barvy buněk (zaměstnavatel) neodpovídají legendě → nečtou se.
- Lidé ve více rozpisech: Tsiple Maryna (OV51/64, SK336, MFA2), Biro Kristina (OV51/64, MBEAM), Virich Oleksandr (G463 Assy, Prefix) → podklad pro „Sdílí lidi s“, ověřit.
- Normy ostatních linek chybí – owner David Varhan. Nic nevymýšlet.
- W520 „bez kategorie“ (2 díly, 6 ks za celý horizont CW43) – nejasné, kam patří. Záměrně nenamapováno, v aplikaci zůstává mezi nenamapovanými.

## Pravidla (skill provozni-aplikace)
Single-file HTML, cdnjs jen, detekce sloupců podle hlavičky, čeština s diakritikou, navy #003B7A, RAG W_n vs W_n−1 (±5 %), G463 L1/L2 zvlášť, localStorage v try/catch, žádná vymyšlená data.

## Další kroky
1. Doplnit normy + OP standard (Slush, MBEAM, G463 Assembly, SK336/W206 první).
2. Ověřit lidi k dispozici (Gemba / potvrzení TL) a „Sdílí lidi s“.
3. ~~Import rozpisů směn přímo do aplikace~~ – hotovo (Import → Import rozpisů směn, jednotná šablona PL-SM-FR-G09-09). Zbývá: Prefix L1/L2 (rozpis nemá rozdělení, posádky A/B) a SK336 (nový rozpis 9/6/0 = 15 OP vs. dřív 9/2/9 = 20 – ověřit).
4. Napojení Summary PLANA1 a DLE (skutečnost vs. plán, gap v €), sazby DL/IDL/agentura.
5. ~~Skill matrix~~ – hotovo (pohled Skill matrix: z rozpisů SKILL LEVEL Z/P/T/N + ručně doplněné kvalifikace na jiných linkách, v Doporučení se u přesunu vypíšou kvalifikovaní Z/P). IMM, OV51/64 a Prefix nemají v rozpisu vyplněný SKILL LEVEL → doplnit v rozpisech. Zbývá víkendový rozpis.
6. ~~Sdílení~~ – hotovo (claude.ai artefakt se sdílenou databází). TL sdílet jako Contributor (mohou měnit rozpis, lidi, nepřítomnosti).
7. Rozpis týdne v aplikaci: lidé jsou záznamy (domovská linka, směna, pozice, SKILL LEVEL, nepřítomnosti); rozpis = stálé obsazení − nepřítomní, díry z přebytků jen Z/P; ruční změny zamčené. Potřeba na směnu: norma → ručně → obsazení z rozpisu CW43. Otevřené: Prefix (80 lidí, posádky A/B) není přiřazený k L1/L2 – L1/L2 mají normu, ale nikoho → chybí 120 OP.
8. Rozpis týdne jako tabulka pozice × směny (jako Excel). Linka může mít seznam pozic / strojů (Kmenová data → Pozice / stroje); IMM: IMM001–004, IMM006–012, materialista, laser. Rozpis IMM nemá u operátorů vyplněný stroj → přiřadit v aplikaci (klik na „neobsazeno“). Cycle time (s/ks) dopočte normu, když chybí.
9. 12h směny a víkendy v aplikaci (náhrada sešitů `Kontrola_12h_smen_Plana.xlsx` a `Vikendovy_rozpis_Plana.xlsx`): pohledy 12h směny a Víkendy, import obou sešitů (jednorázově historie + Nastavení), dál zadávání v aplikaci. Výpočty ověřené proti Přehledu sešitů (12h CW42: 189 lidí, 587 směn, 61 148 €; víkend CW42: 18/22/1 lidí, 26 242,94 €, 0,37 nepřímých na 1 DL). Lidé s 12 h v Rozpisu týdne se převezmou do monitoringu jedním klikem. Otevřené: v sešitu mají Výroba a Logistika prázdný Režim (72 řádků CW42) → nepočítají se do lidí na místě, v aplikaci je tlačítko „Nastavit Na místě“; v sešitu je zastaralá hodnota CW41 (Celý den 2,1 h místo 23,5 h) a ručně přepsané náklady 212 € u 4 řádků Q support CW43.
10. Jednotné rozpisy směn pro výrobu i ostatní oddělení (Logistika, Q support, Maintenance – stejná šablona PL-SM-FR-G09-09): import přes Import rozpisů směn, v Rozpisu týdne přepínač oddělení + přehled závodu, plánování FTE dál jen z výroby. CW43: Logistika 52 + 4 vedení, Q support 4 (vedení neobsazené – 6 míst), Maintenance jen 2 vedení (technici v rozpisu nevyplnění, 10 neobsazených míst vedení).
11. Sekce Náklady (12h + víkendy po týdnech a odděleních, graf, trend, kumulativně, ×52) + dlaždice a list v exportu. CW42: 61 148 € + 26 243 € = 87 391 €.
