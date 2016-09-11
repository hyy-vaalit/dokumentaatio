# Heroku

Vaalin backend-palvelut tuotetaan Heroku-pilvipalvelussa siten, että
henkilötiedot sijaitsevat pelkästään EU-alueella henkilötietolain määrittämässä
laajudessa. Esimerkiksi palveluun talletettava äänioikeutettujen opiskelijoiden
tietokanta on henkilörekisteri. Sen sijaan esimerkiksi palvelun generoimat
logiviestit eivät kuitenkaan sisällä henkilötietoja eikä niistä voi
jälkikäteenkään muodostaa henkilörekisteriä.

Sen lisäksi, että Herokun palvelut luodaan Europe-alueelle, myös Heroku Add-Ons
-lisäpalvelujen valinnassa on oltavata tarkkana. Esimerkiksi Herokun PG Backups
-varmuuskopiointi ei tarjoa palvelua erikseen Euroopan alueella. Kuitenkin
[Postgres Continuous
Protection](https://devcenter.heroku.com/articles/heroku-postgres-data-safety-and-continuous-protection)
säilyttää tietokannan varmuuskopion ja logit samalla alueella jolla
tietokantakin on.


Linkkejä:
* [Heroku Regions](https://devcenter.heroku.com/articles/regions)
* [Data residency](https://devcenter.heroku.com/articles/regions#data-residency)


## Sisällys

* [Dual Control -yleiskuvaus](dual-control.md)
* [Heroku-ympäristön pystyttäminen](environment.md)
  - [SSL-sertifikaatin asentaminen](ssl-cert.md)
* [Palvelun deployn yleiskuvaus](deploy.md)
* [Vaalien lopuksi](removal.md)
