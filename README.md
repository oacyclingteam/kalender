# OA Kalender — clubwebsite

Een eenvoudige, mobielvriendelijke kalender voor de club. De website leest de
ritten automatisch uit de Google Sheet en koppelt ze aan de GPX-bestanden in
deze map.

## Hoe werkt het?

1. **De kalender** staat in Google Sheets. Kolommen: `Datum | Vertrek | Rit | Rit B`.
   - `Datum` in het formaat `01-Mar-2026`
   - `Vertrek` in het formaat `09h00`
   - `Rit` = naam van de rit voor **Groep A** (bv. `TISSELT`)
   - `Rit B` = naam van de rit voor **Groep B** — optioneel. Leeg laten (of
     dezelfde naam als `Rit` invullen) als er die dag geen aparte
     B-rit is.
2. **De GPX-bestanden** staan in `gpx/2027/`. Bestandsnaam volgt dit patroon:

   ```
   oa27-NaamVanDeRit-AfstandKm-HoogtemeterHm.gpx
   ```
   Voorbeeld: `oa27-tisselt-85km450hm.gpx`

3. De website zoekt zelf naar een bestand in `gpx/2027/` waarvan de naam de
   `Rit`-naam (en, indien ingevuld, de `Rit B`-naam) uit de Google Sheet
   bevat. Vindt ze een match, dan toont ze automatisch de afstand, de
   hoogtemeters en de tag **Vlak** (< 500 Hm) of **Heuvel** (> 500 Hm) — en de
   kaart wordt klikbaar naar het GPX-bestand. Geen match? Dan toont de kaart
   "GPX volgt".

**Belangrijk:** de naam in de Google Sheet (`Rit` / `Rit B`) en de naam in het
GPX-bestand moeten overeenkomen (spelling maakt niet uit voor hoofdletters,
spaties of streepjes — maar wél voor typfouten, bv. `TISSELT` vs `TISSELS`
matcht niet).

## Groep A & B

Voor ritten waar de groep zich splitst in een langere/moeilijkere (A) en een
kortere/rustigere (B) variant, toont de kaart twee GPX-knoppen met een
label **A** en **B**.

- Bovenaan de kalender staan drie filterknoppen: **Alle**, **Groep A**,
  **Groep B**. Hiermee kan een lid enkel zijn/haar groep tonen.
- Is `Rit B` leeg of identiek aan `Rit`, dan toont de website gewoon één
  kaart zonder A/B-label (zoals vroeger).
- Groep A en Groep B kunnen elk naar een ander GPX-bestand wijzen; is er
  geen apart bestand voor B, dan valt de website terug op het bestand van A.

## GPX-lijst in Google Sheets

Naast de kalendertab bevat de Google Sheet ook een tab met **alle**
GPX-bestanden uit deze repo (`gpx/2025/`, `gpx/2026/`, `gpx/2027/`,
`gpx/_Nieuw/`) — map, bestandsnaam, routenaam, km, hm en een link naar het
bestand op GitHub.

**Deze lijst wordt niet manueel bijgehouden.** Een Google Apps Script
(gekoppeld aan de club-Gmail) leest periodiek de GitHub-repo uit via de
GitHub API en vult deze tab automatisch aan/bij. De datum/tijd bovenaan de
tab ("Laatst bijgewerkt: …") toont wanneer het script voor het laatst gelopen
heeft. Nieuwe of hernoemde GPX-bestanden in de repo verschijnen dus
vanzelf in deze tab na de eerstvolgende scriptrun — je hoeft de tab zelf
niet aan te vullen.

## Een nieuwe rit toevoegen

1. Voeg een rij toe in de Google Sheet (Datum, Vertrek, Rit, en optioneel
   Rit B).
2. Upload het/de GPX-bestand(en) naar `gpx/2027/` met de juiste naamgeving.
3. Klaar — de website toont het automatisch. Geen herpublicatie nodig.

## Bestandsstructuur

```
kalender/
├── index.html          ← zomerkalender (1 bestand, geen build nodig)
├── winter/
│   └── index.html      ← winterkalender (apart bestand, eigen URL)
├── gpx/
│   ├── 2025/            ← archief
│   ├── 2026/            ← archief
│   ├── 2027/            ← actief seizoen (zomer ÉN winter gebruiken deze map)
│   │   ├── OA27-Tisselt-85km450hm.gpx
│   │   └── ...
│   └── _Nieuw/          ← nog te verwerken routes
└── README.md
```

Voor een volgend zomerseizoen kan je een map `gpx/2028/` toevoegen; pas dan in
`index.html` de regels `GPX_API_URL` en `GPX_RAW_BASE` bovenaan het script
aan van `gpx/2027` naar `gpx/2028`.

## Winterkalender

Voor de winterperiode (**zondag 22 november 2026 t.e.m. 28 februari 2027**)
is er een aparte, eenvoudigere kalender. Die staat los van de zomerkalender
en heeft een eigen link:

`https://oacyclingteam.github.io/kalender/winter/`

De zomer- en winterkalender zijn twee afzonderlijke pagina's en verwijzen
bewust **niet** naar elkaar.

**Gegevens**

- De ritten staan in een eigen tabblad van dezelfde Google Sheet: `2026-winter`
  (kolommen `Datum | Vertrek | Rit`). Er is hier **geen Groep A/B**: per
  zondag is er één rit.
- Dit tabblad is apart gepubliceerd als CSV; de link staat in
  `winter/index.html` bij `SHEET_CSV_URL`.
- De winterritten gebruiken de routes uit `gpx/2027/`. Koppeling en
  naamgeving zijn identiek aan de zomerkalender (naam in de Sheet moet in de
  bestandsnaam voorkomen; km/Hm komen uit de bestandsnaam).

**Wat toont de pagina?**

- Bovenaan één grote kaart met de **eerstvolgende rit**: datum, vertrekuur,
  routenaam, afstand, hoogtemeters, de tag Vlak/Heuvel en de GPX-download.
  Daaronder staan de andere ritten in een lijst.
- Op rit-zondag schuift de grote kaart na 14u door naar de volgende rit.
- **Weersvoorspelling** (Open-Meteo): een uurstrook rond het vertrekuur.
  Die verschijnt pas vanaf 7 dagen voor de rit, omdat de voorspelling
  daarvoor te onbetrouwbaar is.
- **Wind t.o.v. de route**: een kaartje van de route met per heen- en
  terugweg de aanduiding Tegenwind, Rugwind of Zijwind. Dit wordt berekend
  uit de GPX (startpunt = Café De Kiek) en de voorspelde windrichting.
- Links naar de windkaart op **Windy** en naar de **KMI**-voorspelling voor
  Kortenberg.
- Een donker thema ("Sneeuwnacht") met amberkleurig accent, goed leesbaar
  op donkere winterochtenden.

**Een winterrit toevoegen of wijzigen**

1. Pas een rij aan in het tabblad `2026-winter` van de Google Sheet.
2. Zet (indien nodig) het GPX-bestand in `gpx/2027/`.
3. Klaar — geen herpublicatie nodig. De pagina onthoudt de laatste gegevens
   en haalt bij elk bezoek de nieuwste versie op; de weerdata wordt 1 uur
   bewaard.

**Volgende winter (2027-2028)**

Maak een nieuw tabblad (bv. `2027-winter`), publiceer dat als CSV en vervang
de link bij `SHEET_CSV_URL` in `winter/index.html`. Verwijs bij
`GPX_API_URL` naar de juiste `gpx/`-map en pas de periode in deze README aan.

## Hosting: GitHub Pages

1. Maak (indien nog niet gedaan) een repository `kalender` op GitHub onder
   `oacyclingteam`.
2. Upload deze bestanden (via de GitHub-website: **Add file → Upload files**
   werkt prima, geen command line nodig).
3. Ga naar **Settings → Pages**, kies branch `main`, map `/ (root)`, **Save**.
4. Na 1–2 minuten is de site live op:
   `https://oacyclingteam.github.io/kalender/`

## De Google Sheet vernieuwen

De site haalt bij elk bezoek automatisch de laatste versie van de Google
Sheet op (en toont ondertussen de vorig bewaarde versie, zodat het altijd snel
laadt). Er is ook een vernieuw-knop rechtsboven voor een directe update.

Als je ooit de Google Sheet vervangt door een nieuwe: **File → Share →
Publish to web**, tabblad kiezen, formaat **CSV**, publiceren, en de nieuwe
link in `index.html` invullen bij `SHEET_CSV_URL`. Zorg dat de kolomvolgorde
`Datum | Vertrek | Rit | Rit B` blijft — anders leest de website de
groepsritten fout in.
