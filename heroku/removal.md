# Heroku: Vaalien lopuksi

Vaalien päätyttyä jokaiseen Heroku-palveluun liitetty tietokanta poistetaan.
Ehdokastietojärjestelmä ja vaalituloksen laskentapalvelu kytketään pois
käytöstä. Äänestyspalvelu vaalit.hyy.fi voidaan jättää eloon, mutta tällöin
tietokanta on ensin poistettava ja sen jälkeen luotava tyhjästä uudelleen.
Äänestyspalvelu näyttää vaalien jälkeen infosivun ja linkin vaalitulokseen.


## Heroku

- Poista jokaisen palvelun tietokanta
  - [ ] `heroku pg:reset DATABASE -a PALVELU`

- Poista kaikki mahdolliset varmuuskopiot, jotka henkilötietoja sisältävistä
  tietokannoista on otettu vaalien aikana.

- Avaa Overview > Dyno formation
  - Tarkista, että web ja worker ovat "off"-tilassa.
  - Tarkista, että palvelun kustannusarvio on $0.

- Avaa Resources
  - Poista AddOnit.

- Luo viimeiselle deploy-tagille tagi, josta näkee seuraavissa vaaleissa, mikä
  oli viimeinen tuotannossa ollut versio. Esimerkiksi `vuosiluku-final`.
  - `git tag 2016-final && git push --tags`
  - Muista pushata tagit.
