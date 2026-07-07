# Ennen äänestyksen alkamista

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
