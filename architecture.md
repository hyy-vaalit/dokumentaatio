# Arkkitehtuurin yleiskuvaus

HYYn vaalijärjestelmä koostuu neljästä sovelluksesta ja staattisesta
tulossivustosta. Tässä dokumentissa kuvataan järjestelmien roolit ja niiden
väliset tietovirrat. Tekniset yksityiskohdat kuvataan kunkin järjestelmän
omassa README:ssä.

```
 Yliopiston äänioikeus-             Haka / login.helsinki.fi
 rekisteri (CSV)                    (SAML-kirjautuminen)
        │                               │           │
        ▼                               ▼           ▼
 ┌───────────────┐  JWT-rajapinta  ┌──────────────────┐
 │  Voting-api   │◀────────────────│ Voting-frontend  │
 │ (äänestys-    │                 │ (Vue 3 -käyttö-  │
 │  palvelu)     │                 │  liittymä)       │
 └───────────────┘                 └──────────────────┘
    ▲       │
    │       │ äänet CSV:nä
    │       │ grace-periodin jälkeen
    │       ▼
 ┌───────────────┐   CSV-eksportit   ┌────────────────┐
 │ Vaalitulostin │◀──────────────────│ Ehdokastiedot  │
 │ (tulos-       │                   │ (ehdokas-      │
 │  laskenta)    │                   │  asettelu)     │
 └───────────────┘                   └────────────────┘
        │
        │ HTML + JSON
        ▼
 AWS S3 ─ vaalitulos.hyy.fi (staattinen tulossivusto)
```


## Järjestelmät

### Ehdokastiedot — ehdokasasettelu

[hyy-vaalit/ehdokastiedot](https://github.com/hyy-vaalit/ehdokastiedot)

Ehdokasasetteluvaiheen järjestelmä, jolla kerätään vaaliliitot, vaalirenkaat
ja ehdokkaat. Käyttäjäroolit:

* Pääkäyttäjä ja rajoitettu pääkäyttäjä (sihteeri) kirjautuvat salasanalla
  admin-käyttöliittymään.
* Vaaliliiton edustaja kirjautuu Haka-tunnuksella: luo vaaliliiton, liittää
  sen vaalirenkaaseen ja hyväksyy ehdokkaat liittoonsa.
* Ehdokas kirjautuu Haka-tunnuksella ja ilmoittautuu ehdokkaaksi vaaliliiton
  edustajalta saamallaan kutsukoodilla. Ehdokas voi muokata omia tietojaan
  ja perua ehdokkuutensa.

Ehdokasasettelun määräajat asetetaan ympäristömuuttujilla (asettelu →
korjausaika → jäädytys). Kun ehdokasasettelu on päättynyt, pääkäyttäjä antaa
ehdokasnumerot ja järjestelmästä ladataan CSV-eksportit, joilla
Vaalitulostin alustetaan.

### Voting-api — äänestyspalvelun backend

[hyy-vaalit/voting-api](https://github.com/hyy-vaalit/voting-api)

Äänestyksen ydinjärjestelmä. Vastuut:

* Sisäänkirjautuminen: Haka SAML -kirjautuminen (ruby-saml,
  ks [CSC Haka](haka/README.md)) sekä sähköpostitse lähetettävä
  sisäänkirjautumislinkki vaalien aikana lisätyille äänestäjille.
* Äänioikeutettujen rekisteri: yliopistolta saatava äänioikeutettujen
  aineisto tuodaan järjestelmään ennen vaaleja. Äänioikeus tarkistetaan
  kirjautuneen opiskelijanumeroa vasten.
* Äänet talletetaan muuttumattomina (immutable). Äänestysajat, päivittäiset
  aukioloajat ja grace-period määritellään ympäristömuuttujilla.
* Äänet luovutetaan CSV-muodossa export-rajapinnasta vasta äänestyksen ja
  grace-periodin päätyttyä.
* Vaalitulostin käyttää palvelukäyttäjän (ServiceUser) JWT-avainta äänten
  hakuun, tilastoihin sekä äänestäjien lisäämiseen ja
  sisäänkirjautumislinkkien lähettämiseen.
* Julkinen tilannerajapinta kertoo äänestysprosentin ja sen, onko uurnassa
  ääniä (ks [Ennen äänestyksen alkamista](elections/before.md)).

Sähköpostit (mm. sisäänkirjautumislinkit) lähetetään AWS SES:n kautta
worker-prosessista.

### Voting-frontend — äänestäjän käyttöliittymä

[hyy-vaalit/voting-frontend](https://github.com/hyy-vaalit/voting-frontend)

Selaimessa toimiva Vue 3 + TypeScript -sovellus (single-page app), jolla
äänestäjä kirjautuu sisään ja äänestää. Frontend keskustelee voting-apin
kanssa JWT-tunnisteella.

Frontendia ei deployata erikseen: käännetty sovellus julkaistaan
[hyy-vaalit/voting-frontend-dist](https://github.com/hyy-vaalit/voting-frontend-dist)
-repoon, joka on liitetty git-submodulena voting-apin `public/`-hakemistoon.
Voting-api tarjoilee frontendin ja generoi sille ajonaikaisen
konfiguraation (`/_environment.js`), josta frontend saa mm. API-osoitteen
ja äänestysajat. Frontendin päivitys tuotantoon on siten auditoitavissa
oleva submodule-osoittimen muutos voting-apissa.

Vuosittain päivitettävät tekstit (vuosiluku, tuloslinkit, päivämäärät
sivujen teksteissä) muokataan voting-frontendin lähdekoodiin
(lokalisointitiedostot ja `#NOTE`-merkityt kohdat). Äänestyksen
alkamis- ja päättymisajat tulevat voting-apin ympäristömuuttujista.

### Vaalitulostin — tuloslaskenta

[hyy-vaalit/vaalitulostin](https://github.com/hyy-vaalit/vaalitulostin)

Laskee ja julkaisee vaalituloksen. Vastuut:

* Alustetaan ennen vaaleja Ehdokastiedot-järjestelmän CSV-eksporteista
  (ehdokkaat, vaaliliitot, vaalirenkaat).
* Hakee äänestyksen aikana voting-apista tilastoja (mm. äänestysaktiivisuus
  tiedekunnittain ja tunneittain) ja julkaisee ne S3:een.
* Äänestyksen päätyttyä vaalityöntekijä käynnistää äänten haun voting-apista.
  Äänet varmuuskopioidaan heti S3:een yksityisellä tiedostonimellä.
* Laskee tuloksen ja tukee kolmivaiheisia arvontoja tasatuloksille
  (ks [Äänestyksen päättymisen jälkeen](elections/after.md)). Tuloksen tila
  etenee: alustava → jäädytetty (arvontoja varten) → vahvistettu.
* Julkaisee vahvistetun tuloksen HTML- ja JSON-muodossa S3:een, josta se
  näkyy osoitteessa vaalitulos.hyy.fi/VUOSILUKU.
* Admin-käyttöliittymästä voidaan myös lisätä äänestäjiä ja lähettää
  sisäänkirjautumislinkkejä vaalien aikana. Toteutus on voting-apissa;
  Vaalitulostin välittää pyynnöt sinne
  (ks [Äänestyksen aikana](elections/during.md)).

### Vaalitulos-sivusto

[hyy-vaalit/vaalitulos](https://github.com/hyy-vaalit/vaalitulos)

Staattinen HTML5-sivusto, joka on talletettu vaalituloksen S3-buckettiin ja
esittää Vaalitulostimen julkaiseman JSON-datan. Jokaiselle vaalivuodelle on
oma hakemistonsa, jolloin aiempien vuosien tulokset säilyvät muuttumattomina
(ks [Amazon AWS](aws/README.md)).


## Tietovirrat

1. Yliopisto → voting-api: äänioikeutettujen aineisto tuodaan CSV-muodossa
   ennen vaaleja.
2. Ehdokastiedot → Vaalitulostin: ehdokkaat, vaaliliitot ja vaalirenkaat
   siirretään CSV-eksporteilla ennen vaaleja.
3. Äänestäjä → voting-frontend → voting-api: kirjautuminen Hakan kautta tai
   sisäänkirjautumislinkillä, äänestäminen JWT-tunnisteella.
4. Vaalitulostin ↔ voting-api: tilastot äänestyksen aikana; äänet CSV:nä
   grace-periodin päätyttyä; äänestäjien lisäys ja kirjautumislinkit
   vaalien aikana (ServiceUser-JWT).
5. Vaalitulostin → AWS S3: tilastot, äänten varmuuskopio ja vahvistettu
   vaalitulos (HTML + JSON), julkisesti osoitteessa vaalitulos.hyy.fi.


## Vaalin elinkaari järjestelmittäin

1. Ehdokasasettelu: Ehdokastiedot kerää liitot, renkaat ja ehdokkaat.
   Ehdokasnumeroiden annon jälkeen data eksportataan Vaalitulostimeen.
2. Ennen äänestystä: voting-api pystytetään, äänioikeutetut tuodaan,
   frontend jaellaan submodulena, ympäristöt konfiguroidaan
   (ks [Heroku](heroku/README.md)) ja uurna todetaan tyhjäksi
   (ks [Ennen äänestyksen alkamista](elections/before.md)).
3. Äänestyksen aikana: äänestäjät äänestävät; Vaalitulostin julkaisee
   tilastoja; tarvittaessa lisätään äänioikeutettuja käsin
   (ks [Äänestyksen aikana](elections/during.md)).
4. Äänestyksen jälkeen: äänet tuodaan Vaalitulostimeen, arvonnat
   suoritetaan, Keskusvaalilautakunta vahvistaa tuloksen ja tulos
   julkaistaan (ks [Äänestyksen päättymisen jälkeen](elections/after.md)).
5. Vaalien lopuksi: tietokannat ja pääsyavaimet poistetaan
   (ks [Heroku](heroku/removal.md) ja [AWS](aws/removal.md)).


## Yhteinen infrastruktuuri

* Backend-sovellukset (voting-api, Vaalitulostin, Ehdokastiedot) ajetaan
  Herokussa Europe-regioonassa, tietokantana Heroku Postgres
  (ks [Heroku](heroku/README.md)).
* Virhetilanteet raportoidaan Rollbariin.
* Sähköpostit lähetetään AWS SES:llä (voting-api ja Ehdokastiedot).
* Vaalitulos ja tilastot julkaistaan AWS S3:een (ks [AWS](aws/README.md)).
* Kirjautuminen yliopiston tunnuksilla CSC Haka -federaation kautta
  (ks [CSC Haka](haka/README.md)).
