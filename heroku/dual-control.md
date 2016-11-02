# Ylläpito noudattaa Dual Control -periaatetta

HYYn vaaleissa ylläpidon toteuttavat kaksi ATK-vastaavaa yhdessä. Kukaan
yksittäinen henkilö ei saa päästä käyttämään ylläpito-oikeuksia ilman toisen
henkilön läsnäoloa samassa *fyysisessä* tilassa. Ylläpitotoiminnoilla
tarkoitetaan kaikkia niitä toimintoja, joiden kautta pääsee vaalien aikana
käsiksi Heroku.com-hallintapaneeliin tai `heroku`-komentorivityökaluun.

Vaalien tuotantodeployn jälkeen Heroku-palvelulta poistetaan kaikki
Collaborator-oikeudet ja SSH-avaimet. Toisella ATK-vastaavalla on Herokun
hallintakonsolin salasana ja toisella henkilöllä on hallintakonsolin
kaksivaiheisen tunnistautumisen koodit (generaattori ja palautuskoodit). Vaalien
aikana Collaborator-oikeudet tai SSH-avain voidaan tarvittaessa kytkeä palveluun
jonkin kriittisen ylläpitotoiminnon suorittamiseksi. Kaikki ylimääräiset
pääsyoikeudet on kuitenkin poistettava välittömästi ylläpitotoiminnon
suorittamisen jälkeen.

Kukaan yksittäinen henkilö ei saa tuntea *sekä* Heroku-hallintakonsolin
Owner-käyttäjätunnuksen salasanaa *että* hallintakonsolin kaksivaiheisen
tunnistautumisen koodia. Molempien ATK-vastaavien on oltava fyysisesti läsnä
samassa tilassa kaikkina niinä hetkinä, kun palveluun on kytkettynä
Owner-käyttäjätunnuksen lisäksi muita toimijoita Collaborator-oikeuksin tai
SSH-avaimien kautta.

Kun kirjaudutaan Herokun hallintakonsoliin, selaimen on aina oltava Private
Browsing -tilassa. Istunnon päätyttyä varmistetaan, ettei palvelussa ole
Owner-käyttäjätunnuksen lisäksi muita pääsyoikeuksia. Lopuksi generoidaan
Herokun API-avain uudelleen, mikä kirjaa ulos kaikki aiemmat
`heroku`-komentorivityökalun sessiot. API-avaimen uudelleengenerointi ei
keskeytä auki olevia istuntoja (esim `heroku console` tai `heroku logs`).

Viimeisenä vaiheena suljetaan Private Browsing -tilassa oleva selain, mikä
poistaa selaimen välimuistiin jääneet sessiotiedot. Lisäksi suljetaan kaikki
terminaali-ikkunat ja varmistutaan, ettei historiatietoihin jäänyt
API-avaimia ja että avatut `heroku`-sessiot on suljettu.

Jokaisesta vaalien aikana ylläpito-oikeuksin suoritettavasta tapahtumasta
on laadittava pöytäkirja, jonka allekirjoittavat ylläpito-oikeuksien molemmat
haltijat. Pöytäkirjasta on käytävä ilmi, että molemmat ylläpito-oikeuksien
haltijat olivat läsnä samassa fyysisessä tilassa koko ylläpito-oikeuksien käytön
ajan siihen asti, että ylimääräiset pääsyoikeudet poistettiin.


## Linkit:
* [Esimerkki pöytäkirjasta](https://docs.google.com/document/d/1oaHR-T-TdU2MLTGLZgPicL3je6p6uHKu7C1qMfbNe0M/edit?usp=sharing)
