# Ennen äänestyksen alkamista

## Ehdokasasettelu

Ehdokasasettelu tehdään ehdokastiedot-palvelussa ennen äänestystä:
ks. [Ehdokastiedot](../ehdokastiedot/README.md).

## Ehdokkaiden vieminen järjestelmiin

Ehdokkaat, vaaliliitot ja vaalirenkaat viedään ehdokastiedot-palvelun
CSV-exporteista sekä Vaalitulostimeen että voting-apiin:

- Vaalitulostin: `rake db:seed:edari`
- voting-api: `bin/seed-edari` (paikallisesti `bin/seed-edari local`)

## Äänioikeusrekisterin tuominen

Äänioikeutetut tuodaan yliopistolta saatavasta aineistosta voting-apiin.
Importereita on eri tiedostomuodoille (CSV eri vuosien formaateissa,
teksti, XML). Äänestäjät ja äänioikeudet syötetään rake-taskeilla
(`db:seed:edari:voters_and_voting_rights`, ks. voting-apin README) tai
`bin/seed-edari`-skriptillä.

## Todetaan vaaliuurna tyhjäksi

Keskusvaalilautakunnan puheenjohtaja avaa
[äänestyksen tilannesivun](https://vaalit.hyy.fi/api/public/elections/1/voting_percentage)

Sivu on julkinen ja palauttaa äänestysprosentin yhden desimaalin
tarkkuudella. Tarkkuus on tarkoituksella karkea, jotta yksittäisen äänen
antamista ei voi päätellä luvusta. Äänten lukumäärää ei kerrota —
`has_votes` on ainoa signaali siitä, onko uurnassa ääniä.
Vaaliuurna on tyhjä kun `has_votes` on `false`.
```json
{
  "voting_percentage": 0,
  "voter_count": 28000,
  "has_votes": false
}
```
