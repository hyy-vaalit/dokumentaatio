# Äänestyksen päättymisen jälkeen

## Äänten tuominen Vaalitulostimeen

Äänestyksen päätyttyä vaalityöntekijä käynnistää Vaalitulostimesta äänten
hakemisen. Äänestyspalvelu luovuttaa äänet vasta äänestyksen grace-periodin
päättymisen jälkeen. Äänten tuomisen jälkeen alustava vaalitulos on saatavilla.
Alustavaa vaalitulosta ei julkaista tai näytetä vaalivalvojaisissa.

Teknisiä yksityiskohtia:

- Äänten haku edellyttää, että Vaalitulostimessa on täsmälleen yksi
  äänestysalue (VotingArea) — muuten haku keskeytyy virheeseen.
- Heti tuonnin jälkeen äänet varmuuskopioidaan S3:een. Tiedosto saa
  yksityisen satunnaisen tiedostonimen, jottei ääniä voi lukea ennen
  lopullisen tuloksen julkaisua.
- Tuloksella on kolme vaihetta: alustava tulos → jäädytetty tulos
  (arvontoja varten) → lopullinen vahvistettu tulos.


## Arvonnat

Tasatuloksille päätyneiden ehdokkaiden keskinäinen järjestys arvotaan. Arvonnan
teknisenä kirjurina toimii esimerkiksi vaalityöntekijä. Perinteisesti
Keskusvaalilautakunta on arponut käsin varsinaisten ja varaedustajien
paikkajakoon vaikuttavat tasatilanteet. Koneella on arvottu paikkajakoon
vaikuttamattomat tasatilanteet.

Arvonnat löytyvät Vaalitulostimesta. Ensin suoritetaan
ehdokkaiden tasaäänimäärien arvonnat vaaliliiton sisällä. Toiseksi suoritetaan
liittovertailulukujen tasatilanteiden arvonnat vaalirenkaan sisällä.
Kolmanneksi suoritetaan rengasvertailulukujen tasatilanteiden arvonnat
vaalirenkaiden kesken.

Arvontojen suorittamisen jälkeen Keskusvaalilautakunta vahvistaa vaalituloksen.
Vaalitulostin julkaisee vahvistetun vaalituloksen osoitteeseen
vaalitulos.hyy.fi.
