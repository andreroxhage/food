---
name: export-to-notion
description: Exporterar en färdig veckas matplanering till Notion-databasen 💸 Inhandling. Skapar en översiktssida med underliggande sidor för handlingslista, varje recept och meal prep-plan. Använd som sista steg efter Fas 5, eller fristående på en befintlig veckomapp.
argument-hint: "[YYYY-MM-DD]"
---

# Exportera vecka till Notion (Inhandling)

Publicera en färdig veckas planering till Notion-databasen **💸 Inhandling** i en
strukturerad form: en översiktssida med underliggande sidor (subpages) för
handlingslista, varje recept och meal prep-plan.

**Detta körs i huvudkonversationen** (där Notion-MCP finns) — delegera INTE till en
subagent, eftersom subagenter inte garanterat har tillgång till Notion-MCP.

## Fasta värden

- **Inhandling databas-id:** `2ad3a69e-7647-806c-bba0-d503a8f0f2a0`
- **Inhandling data source (förälder för nya sidor):**
  `collection://2ad3a69e-7647-80a2-89f3-000b0dfb831e`
- **Sidnamn:** alltid `Vecka YYYY-MM-DD` (veckans datum).

## Steg

1. **Hitta veckomapp.**
   - Om `$ARGUMENTS[0]` angetts: använd `YYYY-MM-DD/`.
   - Annars: lista alla `YYYY-MM-DD/`-mappar (Glob) och välj den senaste.
   - Kräv att `04-alla-recept.md` finns. Om `03-handlingslista.md` eller
     `05-meal-prep-plan.md` saknas: varna användaren men exportera det som finns.

2. **Läs källfilerna** i veckomappen:
   - `03-handlingslista.md`
   - `04-alla-recept.md`
   - `05-meal-prep-plan.md`

3. **Läs Notion-markdownspecifikationen** via MCP-resursen
   `notion://docs/enhanced-markdown-spec` innan du genererar Notion-innehåll. Gissa aldrig
   syntaxen. (Skicka inte URI:n till `notion-fetch` — läs den som MCP-resurs.)

4. **Bekräfta Inhandling data source.**
   - Använd som standard `collection://2ad3a69e-7647-80a2-89f3-000b0dfb831e`.
   - Om en create/fetch misslyckas: kör `notion-search "Inhandling"`, hämta databasen med
     `notion-fetch` och läs av aktuell data source-URL på nytt (skydd om id:t ändras).

5. **Skapa översiktssidan** med `notion-create-pages`:
   - `parent`: `{ "type": "data_source_id", "data_source_id": "2ad3a69e-7647-80a2-89f3-000b0dfb831e" }`
   - `properties`: `{ "Name": "Vecka YYYY-MM-DD" }`
   - `content`: sammanfattning — veckans datum, antal rätter, totalt antal portioner, samt
     en rättlista (parsad från `## N. Titel`-rubriker och `> Källa … Portioner …`-raderna i
     `04-alla-recept.md`, med portioner + uppskattad tid). Avsluta med en rubrik
     `## Innehåll` — undersidorna som skapas i steg 7 dyker automatiskt upp som klickbara
     länk-block direkt under den rubriken.
   - **Skapa INGEN egen innehållsförteckning** (inga `mention-page`-länkar, inga bullets/
     numrerade länkar till undersidorna). Notion lägger själv till undersidorna som
     länk-block — en manuell lista blir bara en dubblett.
   - Spara den returnerade sid-URL:en / id:t (= `page_id` för subpages).

6. **Dela upp recepten.** Splitta `04-alla-recept.md` på rubriker som matchar `^## \d+\.`
   → ett block per recept (titel + brödtext). Detta ger en subpage per recept.

7. **Skapa subpages** med `notion-create-pages`, `parent` =
   `{ "type": "page_id", "page_id": "<översiktssidans id>" }`, i denna ordning (gärna i en
   batch):
   - `Handlingslista` ← innehåll från `03-handlingslista.md`
   - en sida per recept, `Name` = receptets titel (t.ex. `1. Grillad kyckling …`) ←
     receptblocket
   - `Meal prep-plan` ← innehåll från `05-meal-prep-plan.md`
   - Undersidorna blir automatiskt klickbara länk-block under `## Innehåll` på
     översiktssidan — ingen extra länkning behövs.

8. **Rapportera** översiktssidans URL till användaren. Rör inte de lokala filerna (radera
   eller ändra dem inte).

## Robusthet

- Konvertera markdown så det förblir Notion-kompatibelt enligt specen från steg 3.
- Om recept refererar till lokala filer (t.ex. `recept-…md`): behåll som vanlig text,
  skapa inga trasiga länkar.
- Håll varje create-anrop inom verktygets gränser (dela upp om innehållet är mycket stort).

## Notis om workflow

Detta är det valfria sista steget i matplaneringsworkflowet (efter Fas 5). Det kan också
köras fristående när som helst på en befintlig veckomapp via `/export-to-notion [YYYY-MM-DD]`.
