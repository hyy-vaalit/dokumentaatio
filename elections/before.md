# Ennen äänestyksen alkamista

## Todetaan vaaliuurna tyhjäksi

Keskusvaalilautakunnan puheenjohtaja avaa
[äänestyksen tilannesivun](https://vaalit.hyy.fi/api/public/elections/1/voting_percentage)

Sivu on julkinen ja palauttaa vaalien aikana äänten lukumäärän yhden
desimaalin tarkkuudella. Vaaliuurna on tyhjä kun `has_votes` on `false`.
```json
{
  "voting_percentage": 0,
  "voter_count": 28000,
  "has_votes": false
}
```
