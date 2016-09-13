# Git-commit-viestin muotoilu

Commit-viesti on kirjoitettava huolellisesti, jotta git-logista saa
mahdollisimman hyvin selvää. Tavoite on, ettei erillistä changelogia
tarvitse kirjoittaa, vaan oleelliset muutokset pystyy silmäilemään git-logista
commit-viestin avainsanojen perusteella. Esimerkiksi uudet toiminnallisuudet
voi hakea git-logista filtteröimällä commit-viestit, jotka alkavat `feat`.

Viestin jokaisen rivin maksimipituus on 80 merkkiä, jotta rivi mahtuu kokonaan
esimerkiksi Githubin historianäkymään, kapeaan terminaali-ikkunaan ja useimpiin
git-historiatyökaluihin. Jos asia ei mahdu yhdelle riville, ensimmäiselle
riville kirjoitetaan oleellinen ja viestiä jatketaan yhden tyhjän rivin jälkeen
alempana.

Commit-viestien formaatti on mukautettu
[Angularin git commit -ohjeistuksesta](https://github.com/angular/angular.js/blob/d14c7f3c31deb098bf8f1c50ea6d00af758dbdcb/CONTRIBUTING.md#commit):
```
avainsana (aihealue) Verbillä alkava kuvaus, 70-80 merkkiä.
<tyhjä rivi>
Jos viesti on pitkä, jätä ensimmäisen rivin väliin tyhjä rivi
ja jatka kirjoittamista sen alle.
```

Avainsana on yksi seuraavista:
* feat: uusi bisnesarvoa tuottava toiminnallisuus
* chore: tekninen muutos, joka ei tuota uutta bisnesarvoa
* fix: korjaa virheellisen toiminnallisuuden
* refact: refaktorointi, joka ei oleellisesti muuta aiempaa toiminallisuutta
* doc: dokumentointiin tai kommentointiin liittyvä muutos
* test: testaamiseen liittyvät muutokset
* style: muutokset jotka eivät vaikuta toiminnallisuuteen (esim sisennys)
