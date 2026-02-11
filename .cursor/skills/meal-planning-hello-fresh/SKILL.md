---
name: meal-planning-hello-fresh
description: Skapar HelloFresh-liknande matplanering för veckan: brainstorming av måltider, receptval med svenska länkar, poolad handlingslista skalad till portioner, och optimerad meal prep-plan. Använd när användaren nämner matplan/veckomeny/handlingslista/meal prep/HelloFresh eller vill planera mat för veckan. Måste stanna och invänta användarens OK mellan faser.
---

# Matplanering (HelloFresh-stil)

## Quick start

När användaren vill planera mat för kommande vecka:

1. Skapa/uppdatera en datum-mapp i projektroten: `YYYY-MM-DD/` (idag: `2026-01-24/`).
2. Följ faserna nedan och skriv output i respektive markdown-fil.
3. **Gå aldrig vidare till handlingslista eller meal prep-plan utan tydligt “kör vidare”.**

## Antaganden (default)

- **Språk**: svenska
- **Vecka**: lunch + middag
- **Standardportioner**: 6 portioner per recept (om inget annat anges)
- **Preferens**: högre protein, men varierat för hushållet (inte low carb, inte veganskt)
- **Receptkällor**: prioritera svenska sajter (ICA först), men använd internationella källor när det ger bättre kvalitet/autenticitet (t.ex. phở)

## Fas 1 — Brainstorming (måltider)

### Input att samla (kort, men konkret)

- antal dagar/måltider (lunch + middag)
- proteinfokus och variation (kyckling/nöt/fisk/veg etc.)
- allergier/preferenser i hushållet (om angivet)
- tid/energi: “vardag snabbt” vs “helgprojekt”
- ev. utrustning (ugn, airfryer, slow cooker)

### Output

Skriv i `01-brainstorming.md`:
- preferenser + constraints
- 10–20 kandidatmåltider med kort motivering (t.ex. “hög protein”, “snabb”, “bra matlåda”)
- markera vilka som är bra för batch-cook / frys

### STOPP

Be användaren:
- välja X rätter
- och/eller klistra in egna recept / länkar

## Fas 2 — Receptval (länkar + portioner)

### Regler

- Ge 1–3 länkar per rätt (svenska sajter först, internationellt om det blir bättre).
- Om användaren redan har recept: använd det som källa och länka bara om det efterfrågas.
- Dokumentera portioner och ev. skalningsfaktor.
- Om du skriver ett eget recept (helt eller anpassat): spara som `YYYY-MM-DD/recept-<slug>.md` och använd “eget recept (fil)” som källa i tabellen.

### Output

Skriv i `02-receptval.md`:
- tabell: rätt, länk/källa, originalportioner, målportioner, skalningsfaktor, taggar (snabb/batch/frys)
- lista “att dubbelkolla” (t.ex. kryddor, specialingredienser)

### STOPP (obligatorisk)

Fråga: **“Vill du att jag skapar handlingslista nu?”**

## Fas 3 — Handlingslista (poolad)

### Regler

- Sammanfoga ingredienser över alla valda recept.
- Normalisera enheter (t.ex. `1000 g` → `1 kg`, `10 dl` → `1 l` när det blir tydligare).
- Håll ingrediensnamn “butiksvänliga” (svenska).
- Strukturera i kategorier (Grönsaker, Frukt, Mejeri & Ägg, Kött/Fisk, Skafferi, Kryddor & Såser, Fryst, Bröd, Övrigt).
- Markera osäkerheter med “(verifiera)” istället för att gissa.

### Output

Skriv i `03-handlingslista.md`:
- poolad lista per kategori
- separat sektion för “skafferi-antaganden” (t.ex. salt, peppar, olja) som användaren kan välja att inkludera/ignorera

### STOPP (obligatorisk)

Fråga: **“Vill du att jag skapar meal prep-plan nu?”**

## Fas 4 — Meal prep-plan (optimerad)

### Mål

Minimera total tid genom att:
- gruppera moment (hacka allt, koka allt ris/pasta, ugnsplåt samtidigt)
- parallellisera (ugn + spis + kall-prep)
- återanvända såsbaser/kryddmixar när det passar utan att offra variation

### Output

Skriv i `04-meal-prep-plan.md`:
- “innan du börjar” (utrustning, lådor, etiketter)
- tidslinje i block (t.ex. 0–15, 15–35, 35–60…)
- per recept: korta steg + kritiska temperaturer/tider
- förvaring: kyl/frys + hållbarhet (konservativt)

## Referens

För källor och konverteringar, se [reference.md](reference.md).
För exempel på format, se [examples.md](examples.md).

