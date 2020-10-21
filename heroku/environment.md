# Heroku-ympäristön pystyttäminen

Luodaan ja konfiguroidaan Herokuun vaaleissa käytettävä palvelu.
Heroku-ympäristöä pystytettäessä kriittiset kohdat, kuten pääsyoikeudet ja
kaksivaiheinen tunnistautuminen, luodaan varmuuden vuoksi
uudelleen.

Herokun appsia, johon dynot kytketään, ei tarvitse luoda uudelleen. Deploy
voidaan tehdä edellisissä vaaleissa käytettyyn Heroku-appsiin. Edellisen vaalin
Heroku-appsia käyttämällä esimerkiksi domain-endpointteja ei tarvitse luoda
uudelleen. Ympäristön pystyttäminen aloitetaan varmistamalla, että luotava Heroku-app on tyhjä, eikä
siihen ole ylimääräisiä pääsyoikeuksia. Palvelun ympäristömuuttujiin määritetyt pääsyavaimet luodaan
uudelleen.


## Dual Control -periaatteen noudattaminen

Tuotantojärjestelmien ylläpidossa noudatetaan
[Dual Control -periaatetta](dual-control.md). Kukaan yksittäinen henkilö ei saa
päästä käyttämään ylläpito-oikeuksia ilman toisen henkilön läsnäoloa samassa
*fyysisessä* tilassa.

Rajoitus koskee erityisesti:
- Pääsyä Herokun hallintakonsoliin Owner-käyttäjätunnuksella.
- Tilanteita, joissa palveuun on Collaborator-oikeudet muilla kuin
  Owner-käyttäjätunnuksella tai palveluun on liitetty SSH-avain.
- Heroku CLI -komentorivityökalun käyttöä (`heroku`-komento).


## Heroku-tunnuksen pääsyoikeuksien tarkistaminen

Varmistutaan, että Heroku-pääkäyttäjätunnukseen ei ole ylimääräisiä
pääsyoikeuksia.

- [ ] Selain on Private Browsing -tilassa

- Avaa Heroku.com > Personal apps > hyy-vaalit > Access
  - [ ] Poista Collaborators-listasta muut käyttäjät paitsi owner.
    - Owner on sama käyttäjätunnus, jolla olet kirjautuneena Heroku.comiin.
    - ATK-vastaavat ja pääsihteeri saavat kaikki tunnuksen sähköpostit.

- Avaa valikko oikeassa yläkulmassa > Account settings
  - [ ] Tarkista, että sähköpostiosoitteen verkkotunnus kohdassa "Email address" päättyy @ hyy.fi
    - Tällöin vain hyy.fi-verkkotunnuksen ylläpito voi muuttaa sitä,
      kuka saa postiosoitteeseen lähetettävän password recovery -viestin.
  - [ ] Sähköpostiosoitteeseen lähetetty sähköpostiviesti saapuu molemmille ATK-vastaaville.

  - [ ] Tarkista, että "Registered ssh keys" on tyhjä.

  - [ ] Vaihda käyttäjätunnuksen salasana.
    - Generoi vahva salasana esimerkiksi 1Passwordilla.

  - [ ] Varmistu, että uusi salasana on ainoastaan yhden atk-vastaavan tiedossa.

  - [ ] Valitse Regenerate API key.
    - Tällöin `heroku`-komentorivityökalun kaikki aiemmat sessiot vanhenevat.

  - Avaa Applications-välilehti
    - [ ] Tarkista, että Third-party Services on tyhjä.
    - [ ] Tarkista, että Authorized Application on tyhjä.
    - [ ] Tarkista, että API Clients on tyhjä.


## Kaksivaiheinen tunnistautuminen (Two factor authentication)

Herokun kaksivaiheista tunnistautumista käytetään varmistamaan, että kukaan
ylläpitäjä ei yksin pääse sisään Herokun hallintakonsoliin. Vaalissa on kaksi
ATK-vastaavaa, joista toisen hallussa on Heroku-tunnuksen salasana  ja toisella
henkilöllä on kaksivaiheisen tunnistautumisen koodit.

Kaksivaiheinen tunnistautuminen suojaa ainoastaan Heroku.comin webissä olevan
hallintakonsolin. Jos kaksivaiheisen tunnistautumisen jälkeen lisätään
pääsyoikeuksia tai SSH-avaimia, jokainen käytettävä SSH-avain on generoitava
tyhjästä Dual Control -session aikana. Lopuksi kaikki lisätyt pääsyoikeudet on
poistettava. Molempien ylläpitäjien on yhdessä pysyttävä samassa fyysisessä
tilassa niin pitkään kuin ylläpitotoimet kestävät.

Viimeistään tuotannon asennusvaiheessa koodigeneraattorin syötekoodi luodaan
uusiksi. Tällä varmistutaan siitä, ettei kenelläkään muulla ole pääsyä
kaksivaiheisen tunnistautumisen koodeihin. Owner-käyttäjätunnuksella
sisäänkirjautuminen vaatii sekä generoidun koodin että ennalta tunnetun
salasanan syöttämistä.

### Ylläpitotoimista laaditaan pöytäkirja

Aina, kun kaksivaiheisen tunnistautumisen koodi syötetään, siitä täytyy tehdä
merkintä vaalipöytäkirjaan. Merkinnästä on käytävä ilmi:
- Mihin kellonaikaan kirjauduttiin sisään.
- Mitä kirjautumisen aikana tehtiin.
- Mihin kellonaikaan kirjauduttiin ulos.
- Miten varmistuttiin, ettei kaksivaiheisella tunnistautumisella avattu
  selainsessio jäänyt auki tai selaimen välimuistiin.
- Todetaan, että molemmat ATK-vastaavat olivat samassa fyysisessä tilassa
  koko sen ajan, kun ylläpito-oikeudet olivat voimassa.
- Todetaan, että ylläpitotoimien ajaksi annetut uudet pääsyoikeudet on
  poistettu.


### Yleiskuvaus:

- Asennetaan Google Authenticator -koodigeneraattori.
- Käytetään SMS Recoveryssä sen henkilön puhelinnumeroa, jolla on
  koodigeneraattori. Tämä henkilö ei saa tuntea Owner-käyttäjätunnuksen
  salasanaa.
- SMS Recoveryssä ei saa käyttää sen henkliön puhelinnumeroa, joka tuntee
  Owner-käyttäjätunnuksen salasanan.
- Salasanan tunteva henkilö ei saa nähdä koodigeneraatorin asennusvaiheessa
  näkyvää QR-koodia tai tulostettavia Recovery Codes -koodeja.


### Checklist:

Varmistetaan, ettei kenelläkään muulla ole pääsyä kaksivaiheisen
tunnistautumisen koodeihin luomalla koodigeneraattorin syöte uudelleen.

Seuraavat toimenpiteet suoritetaan sen ATK-vastaavan toimesta, joka ei tunne
Owner-käyttäjätunnuksen salasanaa. Henkilö, joka tuntee salasanan, ei saa nähdä
jäljempänä syntyvää QR-koodia tai palautuskoodeja.

- [ ] Tietokonetta käyttää ATK-vastaava, joka ei tunne Owner-käyttäjätunnuksen salasanaa.
- [ ] Varmistu, että olet omalla tietokoneellasi.
- [ ] Avaa selain Private Browsing -tilaan.

- Avaa Avaa Heroku.com > Settings > Two factor authentication.
  - [ ] Valitse `Disable` kohdassa "Two factor authentication is enabled"
  - [ ] Varmistu ettei koneen äärellä ole ylimääräisiä henkilöitä, jotka voivat ottaa QR-koodin tai varmistuskoodit itsellensä talteen.
  - Valitse "Set Up Two-factor Authentication"
    - [ ] Asenna puhelimeesi Google Authenticator
    - [ ] Skannaa Google Authenticatorilla Herokun antama two factor authin koodien siemenenä toimiva QR-koodi.
    - [ ] Syötä koodigeneraattorin antama koodi kohdassa "Verify your app"
    - [ ] Paina Enable two factor authentication

- Two factor auth on nyt enabled.
  - Ennen Recovery Options -vaiheen suorittamista, varmista että muut eivät
    näe tietokoneen ruutua.
  - Paina Set up recovery options:
    - [ ] Syötä numerosi SMS recoveryyn
    - [ ] Syötä tekstiviestinä saapunut vahvistuskoodi
    - SMS recovery saa olla vain sellaisella henkilöllä, joka ei tunne ensisijaista salasanaa.
    - Ota talteen tekstimuodossa olevat palautuskoodit (Recovery Codes).
      - [ ] Tulosta koodit paperille tai 1Passwordiin
      - [ ] Talleta paperi esimerkiksi kassakaappiin, tai muuhun turvalliseen paikkaan.
    - [ ] Varmistu, että kukaan muu ei nähnyt palautuskoodeja eikä koodigeneraattorin syötteenä toiminutta QR-koodia.
    - [ ] Varmistu, ettei tulostetuista palautuskoodeista syntynyt tiedostoa esimerkiksi Downloads-hakemistoon.
- [ ] Tarkista, että sivulla lukee: Two factor authentication is enabled
- [ ] Tarkista, että backup phone number sisältää ainoastaan oman puhelinnumerosi.
- [ ] Sulje selain. Private browsing moden ansiosta selain tyhjentää sulkemisen yhteydessä historiansa ja välimuistinsa.
- Tarkista, että asetukset ovat kunnossa:
  - [ ] Avaa uusi selainikkuna private browsing modeen
  - [ ] Varmistu, että Heroku.com kysyy salasanan syöttämisen jälkeen kaksivaiheisen tunnistautumisen koodia.


## Palvelun luominen Herokuun

Luodaan vaalipalvelulle Heroku-ympäristön perusasetukset.

- [ ] Selain on private browsing modessa.
- Avaa Heroku.com > HEROKU APPSIN NIMI
- Avaa Access.
  - Tarkista Collaborators-listan pääsyoikeudet:
    - [ ] vaalit-admin@hyy.fi: Owner
    - [ ] Ei muita collaboratoreita.
  - Luo uusi SSH-avain deployn suorittavalle henkilölle.
  - Lisää edellä luotu SSH-avain sivulla Account Settings > SSH-key.

- Poista aiempi Postgres-tietokanta.
  - Avaa Resources
  - Valitse Heroku Postgres > Remove

- Avaa Settings
  - [ ] Aseta palvelu huoltotilaan: Maintenance Mode: On
  - Kohta Config variables
    - Jätä seuraavat ympäristömuuttujat (Config Vars),
      - HEROKU_*
    - Poista muut ympäristömuuttujat.
  - Kohta SSL Certificate
    - [ ] Tarkista, että SSL-sertifikaatin voimassaoloaika riittää vähintään vaalien keston ajan.
  - Kohta Domains
    - [ ] Heroku domain PALVELU.herokuapp.com (esim. hyy-vaalit.herokuapp.com)
    - [ ] Custom domains: PALVELU.hyy.fi (esim. vaalit.hyy.fi: vaalit.hyy.fi.herokudns.com)
    - [ ] Ei muita domaineja

- Avaa Resources
  - Lisää AddOnit:
    - [ ] Heroku Postgres, [Hobby Basic](https://elements.heroku.com/addons/heroku-postgresql), $9/kk


- Nosta Herokun palvelutaso ilmaisesta maksulliseen.
  - Overview > Configure Dynos
  - [ ] Upgrade to Hobby $7 /dyno/kk
- Lisää [SSL-sertifikaatti](heroku-ssl-cert.md)
  - [ ] heroku _certs:add server.crt server.key -r production
- Lisää domain (jos ei vielä ole)
  - [ ] heroku domains:add PALVELU.hyy.fi -r production
