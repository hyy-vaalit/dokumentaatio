# Heroku-ympäristön pystyttäminen

Luodaan ja konfiguroidaan Herokuun vaaleissa käytettävä palvelu.
Palveluna voidaan käyttää edellisissä vaaleissa luotua palvelua.
Heroku-ympäristöä pystytettäessä kriittiset kohdat, kuten pääsyoikeudet ja
kaksivaiheinen tunnistautuminen, luodaan varmuuden vuoksi
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

- Avaa valikko oikeassa yläkulmassa > Account settings
  - [ ] Tarkista, että sähköpostiosoitteen verkkotunnus kohdassa "Email address" päättyy @ hyy.fi
    - Tällöin vain hyy.fi-verkkotunnuksen ylläpito voi muuttaa sitä,
      kuka saa postiosoitteeseen lähetettävän password recovery -viestin.
  - [ ] Sähköpostiosoitteeseen lähetetty sähköpostiviesti saapuu molemmille ATK-vastaaville.

  - [ ] Tarkista, että "Registered ssh keys" on tyhjä.

  - [ ] Vaihda käyttäjätunnuksen salasana.
    - Generoi vahva salasana esimerkiksi 1Passwordilla.

  - [ ] Varmistu, että salasana ei ole muiden kuin vaalien atk-vastaavien tiedossa.

  - [ ] Valitse Regenerate API key.
    - Tällöin Heroku CLI -komentorivityökalun kaikki aiemmat käyttäjät joutuvat
      kirjautumaan uudelleen sisään.


## Kaksivaiheinen tunnistautuminen (Two factor authentication)

#TODO: Poista pääsiheeri

Herokun kaksivaiheista tunnistautumista käytetään varmistamaan, että kukaan
yksittäinen ylläpitäjä ei pääse sisään Herokun hallintakonsoliin. Vaalien
ATK-vastaavien hallussa on ainoastaan Heroku-tunnuksen salasana. Kaksivaiheisen
tunnistautumisen generoitu osa on vain sellaisella henkilöllä, jolla ei ole
tiedossaan Heroku.comin hallintatunnuksen pysyvää salasanaa (esimerkiksi
pääsihteerillä).

Kaksivaiheinen tunnistautuminen suojaa ainoastaan Heroku.comin webissä olevan
hallintakonsolin. Jos kaksivaiheisen tunnistautumisen jälkeen lisätään
pääsyoikeuksia tai SSH-avaimia, kaikki lisätyt pääsyoikeudet on lopuksi
poistettava.

Viimeistään tuotannon asennusvaiheessa koodigeneraattorin syötekoodi luodaan
uusiksi, jotta varmistutaan siitä, ettei kenelläkään muulla kuin esim.
pääsihteerillä ole pääsyä kaksivaiheisen tunnistautumisen koodeihin.

Aina, kun kaksivaiheisen tunnistautumisen koodi syötetään, siitä täytyy tehdä
merkintä vaalipöytäkirjaan. Merkinnästä on käytävä ilmi:
- mihin kellonaikaan kirjauduttiin sisään
- mitä kirjautumisen aikana tehtiin
- mihin kellonaikaan kirjauduttiin ulos
- miten varmistuttiin, ettei kaksivaiheisella tunnistautumisella avattu
- selainsessio jäänyt auki tai selaimen välimuistiin

### Overview:
- Asennetaan Google Authenticator koodigeneraattori pääsihteerin puhelimeen.
- Käytetään SMS Recoveryssä pääsihteerin puhelinnumeroa.
- SMS Recoveryssä ei saa käyttää muun kuin pääsihteerin puhelinnumeroa.
- Pääsihteeri ei saa tuntea Heroku.comin hallintasalasanaa.
- ATK-vastaavat eivät saa nähdä koodigeneraatorin asennusvaiheessa näkyvää QR-koodia.
- ATK-vastaavat eivät saa nähdä tulostettavia Recovery Codes -koodeja.


### Checklist:

Varmistetaan, ettei kenelläkään muulla ole pääsyä two factor authin edellyttämiin koodeihin luomalla two factor authin syöte uudelleen.

Seuraavat toimenpiteet suoritetaan pääsihteerin toimesta pääsihteerin tietokoneelta.

- [ ] Tietokonetta käyttää ATK-vastaava, joka ei tunne Heroku-tunnuksen salasanaa.  
- [ ] Varmistu, että olet omalla tietokoneellasi.  
- [ ] Avaa selain private browsing modeen
- [ ] Avaa Heroku.com
- Avaa Settings > Two factor authentication.
- [ ] Valitse `Disable` kohdassa "Two factor authentication is enabled"
- [ ] Varmistu ettei koneen äärellä ole ylimääräisiä henkilöitä, jotka voivat ottaa QR-koodin tai varmistuskoodit itsellensä talteen.
- [ ] Valitse "Set Up Two-factor Authentication"
  - [ ] Asenna pääsihteerin puhelimeen Google Authenticator
  - [ ] Skannaa Google Authenticatorilla Herokun antama two factor authin koodien siemenenä toimiva QR-koodi.
  - [ ] Syötä koodigeneraattorin antama koodi kohdassa "Verify your app"
  - [ ] Paina Enable two factor authentication
- Two factor auth on nyt enabled.
  - [ ] Ennen Recovery Options -vaiheen suorittamista, varmista että muut kuin pääsihteeri eivät näe tietokoneen ruutua.
  - Paina Set up recovery options:
    - [ ] Syötä pääsihteerin numero SMS recoveryyn
    - [ ] Syötä tekstiviestinä saapunut vahvistuskoodi
    - *Tärkeä*: SMS recovery saa olla vain sellaisella henkilöllä, joka ei tunne ensisijaista salasanaa.
    - Ota talteen tekstimuodossa olevat palautuskoodit (Recovery Codes).
      - [ ] Tulosta koodit paperille
      - [ ] Talleta paperi esimerkiksi kassakaappiin, tai muuhun turvalliseen paikkaan.
    - [ ] Varmistu, että kukaan muu ei näe palautuskoodeja eikä koodigeneraattorin syötteenä toiminutta QR-koodia.
    - [ ] Varmistu, ettei tulostetuista palautuskoodeista syntynyt tiedostoa esimerkiksi Downloads-hakemistoon.
- [ ] Tarkista, että sivulla lukee: Two factor authentication is enabled
- [ ] Tarkista, että backup phone number sisältää ainoastaan pääsihteerin puhelinnumeron.
- [ ] Sulje selain. Private browsing moden ansiosta selain tyhjentää sulkemisen yhteydessä historiansa ja välimuistinsa.
- Tarkista, että asetukset ovat kunnossa:
  - [ ] Avaa uusi selainikkuna private browsing modeen
  - [ ] Varmistu, että Heroku.com kysyy salasanan syöttämisen jälkeen kaksivaiheisen tunnistautumisen koodia.


## Palvelun luominen Herokuun

Luodaan vaalipalvelulle Heroku-ympäristön perusasetukset.

- [ ] Selain on private browsing modessa.
- Avaa Heroku.com > hyy-vaalit
- Avaa Access.
  - Tarkista Collaborators-listan pääsyoikeudet:
    - [ ] vaalit-admin@hyy.fi: Owner
    - [ ] vaalityöntekijät: Collaborator
- Avaa Settings
  - [ ] Aseta palvelu huoltotilaan: Maintenance Mode: On
  - Kohta Config variables
    - Jätä seuraavat ympäristömuuttujat (Config Vars),
      - HEROKU_*
      - SENDGRID_*
      - NEWRELIC_*
    - Poista muut ympäristömuuttujat.
  - Kohta SSL Certificate
    [ ] Tarkista, että SSL-sertifikaatin voimassaoloaika riittää vähintään vaalien keston ajan.
  - Kohta Domains
    [ ] Heroku domain hyy-vaalit.herokuapp.com
    [ ] Custom domains: vaalit.hyy.fi (vaalit.hyy.fi.herokudns.com)
    [ ] Ei muita domaineja

- Avaa Resources
  - Lisää AddOnit:
    - [ ] Heroku Postgres, [Hobby Basic](https://elements.heroku.com/addons/heroku-postgresql), $9/kk
    - [ ] [Sendgrid](https://elements.heroku.com/addons/sendgrid)
    - [ ] New Relic

- Ensimmäinen Deploy
  - Lisää Herokun git-remote, URL näkyy sivulla Settings:
    - [ ] git remote add production https://git.heroku.com/PALVELU.git
  - Lisää ympäristömuuttujat
    - Lue malli tiedostosta `cat .env.example`
    - Lisää jokainen muuttuja `heroku config:set KEY=VALUE -r production`
  - Deploy:
    - [ ] git push production master

  - Nosta Heroku-palvelutaso ilmaisesta maksulliseen
    - Overview > Configure Dynos
    - [ ] Upgrade to Hobby $7 /dyno/kk
  - Lisää [SSL-sertifikaatti](heroku-ssl-cert.md)
    - [ ] heroku _certs:add server.crt server.key -a hyy-ehdokastiedot
  - Lisää domain (jos ei vielä ole)
    - [ ] heroku domains:add palvelu.fi -a PALVELU
