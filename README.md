# OA Kalender — clubwebsite

Een eenvoudige, mobielvriendelijke kalender voor de club. De website leest de
ritten automatisch uit de Google Sheet en koppelt ze aan de GPX-bestanden in
deze map.

## Hoe werkt het?

1. **De kalender** staat in Google Sheets. Kolommen: `Datum | Vertrek | Rit`.
   - `Datum` in het formaat `01-Mar-2026`
   - `Vertrek` in het formaat `09h00`
   - `Rit` = naam van de rit (bv. `TISSELT`)
2. **De GPX-bestanden** staan in `gpx/2026/`. Bestandsnaam volgt dit patroon:

   ```
   oa26-NaamVanDeRit-AfstandKm-HoogtemeterHm.gpx
   ```
   Voorbeeld: `oa26-tisselt-85km450hm.gpx`

3. De website zoekt zelf naar een bestand in `gpx/2026/` waarvan de naam de
   `Rit`-naam uit de Google Sheet bevat. Vindt ze een match, dan toont ze
   automatisch de afstand, hoogtemeters en de tag **Vlak** (< 500 Hm) of
   **Heuvel** (> 500 Hm) — en de kaart wordt klikbaar naar het GPX-bestand.
   Geen match? Dan toont de kaart "GPX volgt".

**Belangrijk:** de naam in de Google Sheet (`Rit`) en de naam in het
GPX-bestand moeten overeenkomen (spelling maakt niet uit voor hoofdletters,
spaties of streepjes — maar wél voor typfouten, bv. `TISSELT` vs `TISSELS`
matcht niet).

## Een nieuwe rit toevoegen

1. Voeg een rij toe in de Google Sheet (Datum, Vertrek, Rit).
2. Upload het GPX-bestand naar `gpx/2026/` met de juiste naamgeving.
3. Klaar — de website toont het automatisch. Geen herpublicatie nodig.

## Bestandsstructuur

```
oact/
├── index.html          ← de volledige website (1 bestand, geen build nodig)
├── gpx/
│   └── 2026/
│       ├── oa26-tisselt-85km450hm.gpx
│       └── ...
└── README.md
```

Voor 2027 kan je een map `gpx/2027/` toevoegen; pas dan in `index.html` de
regel `gpx/2026` aan naar `gpx/2027` (zoek `GPX_API_URL` en `GPX_RAW_BASE`
bovenaan het script).

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
link in `index.html` invullen bij `SHEET_CSV_URL`.
