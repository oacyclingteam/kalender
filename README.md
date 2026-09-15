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
oact/
├── index.html          ← de volledige website (1 bestand, geen build nodig)
├── gpx/
│   ├── 2025/            ← archief
│   ├── 2026/            ← archief
│   ├── 2027/             ← actief seizoen
│   │   ├── OA27-Tisselt-85km450hm.gpx
│   │   └── ...
│   └── _Nieuw/          ← nog te verwerken routes
└── README.md
```

Voor een volgend seizoen kan je een map `gpx/2028/` toevoegen; pas dan in
`index.html` de regels `GPX_API_URL` en `GPX_RAW_BASE` bovenaan het script
aan van `gpx/2027` naar `gpx/2028`.

## Hosting: GitHub Pages

1. Maak (indien nog niet gedaan) een repository `oact` op GitHub onder
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
