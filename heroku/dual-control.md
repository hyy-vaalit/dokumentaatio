# Ylläpito noudattaa Dual Control -periaatetta

HYYn vaaleissa ylläpidon toteuttavat kaksi ATK-vastaavaa yhdessä. Kukaan
yksittäinen henkilö ei saa päästä käyttämään ylläpito-oikeuksia ilman toisen
henkilön läsnäoloa samassa *fyysisessä* tilassa.

Vaalien tuotantodeployn jälkeen Heroku-palvelulta poistetaan kaikki
Collaborator-oikeudet ja SSH-avaimet. Toisella ATK-vastaavalla on Herokun
hallintakonsolin salasana ja toisella henkilöllä on hallintakonsolin
kaksivaiheisen tunnistautumisen koodit (generaattori ja palautuskoodit).

Kukaan yksittäinen henkilö ei saa tuntea *sekä* hallintakonsolin
Owner-käyttäjätunnuksen salasanaa *että* hallintakonsolin kaksivaiheisen
tunnistautumisen koodia. Molempien ATK-vastaavien on oltava fyysisesti läsnä
samassa tilassa kaikkina niinä hetkinä, kun palveluun on kytkettynä
Owner-käyttäjätunnuksen lisäksi muita toimijoita Collaborator-oikeuksin tai
SSH-avaimien kautta.

Kun kirjaudutaan Herokun hallintakonsoliin, selaimen on aina oltava Private
Browsing -tilassa. Istunnon päätyttyä varmistetaan, ettei palvelussa ole
Owner-käyttäjätunnuksen lisäksi muita pääsyoikeuksia. Lopuksi generoidaan
Herokun API-avain uudelleen, mikä kirjaa ulos kaikki aiemmat Heroku CLI
-sessiot. Viimeisenä vaiheena suljetaan Private Browsing -tilassa oleva selain,
mikä poistaa selaimen välimuistiin jääneet sessiotiedot.
