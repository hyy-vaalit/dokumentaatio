# CSC Haka Knowledge Base

Vaalijärjestelmä
[rekisteröidään](https://wiki.eduuni.fi/display/CSCHAKA/Usein+kysytyt+kysymykset)
Service Providerina (SP) CSC Hakan [resurssirekisteriin](https://rr.funet.fi/rr/).

Vaalijärjestelmä ohjaa käyttäjän suoraan Helsingin yliopiston
sisäänkirjautumissivulle (Identity Provider, IdP).

Tekninen toteutus käyttää sovelluskerroksen
[SAML2-kirjastoa](https://github.com/onelogin/ruby-saml), mutta *ei* käytä
Shibbolethia. Shibboleth on web-applikaatioserverikerroksen toteutus, josta ei
v2016 löytynyt kypsännäköistä toteutusta Rubyn
Puma/Unicorn-applikaatioserverille. Vaalijärjestelmällä ei ole käytettävissä
Apachea.


## Sisäänkirjautuminen

* Käyttäjä ohjataan suoraan Helsingin yliopiston kirjautumissivulle.

* Käyttäjän ei pidä joutua valitsemaan kotiorganisaatiotansa CSC Hakan
  WAYF-välisivun listasta.

* Sisäänkirjautumisessa kelpuutetaan ainoastaan Helsingin yliopiston
  käyttäjätunnukset (entityID=xyz.helsinki.fi).

* IdP välittää sisäänkirjautumisen tiedoissa opiskelijanumeron.

* Sisäänkirjautuneen käyttäjän äänioikeus tarkistetaan opiskelijanumeroa vasten
  paikallisesta (omasta) äänioikeutettujen rekisteristä (`Voters` taulu).


## Attribuutit

* Hakan opiskelijanumero muotoa: [schacPersonalUniqueCode](https://wiki.eduuni.fi/display/CSCHAKA/funetEduPersonSchema2dot2#funetEduPersonSchema2dot2-schacPersonalUniqueCode)

* Helsingin yliopiston opiskelijanumero muotoa: [urn:mace:terena.org:schac:personalUniqueCode:int:studentID:helsinki.fi:165934](http://www.helsinki.fi/atk/luvat/ldap/doc/index.html#henkiloluokat_schacPersonalUniqueCode)

* [Helsingin yliopiston attribuutit](http://www.helsinki.fi/atk/urn/)


## Metadata

https://wiki.eduuni.fi/display/CSCHAKA/Metadata

> Haka-operaattori julkaisee uuden metadatan keskimäärin kerran kahden viikon
> kuluessa. Metadatassa ilmoitetaan validuntil-attribuutti, joka ilmaisee
> metadatan vanhenemisen.
>
> Hakassa metadata on voimassa maksimissaan neljä viikkoa julkaisusta.
>
> Ilmoitteassasi Haka-operointiin entiteettisi metatiedon muutoksesta huomoi,
> että muutos tulee voimaan vasta seuraavan metadatajulkaisun yhteydessä.
> Julkaisuhetkestä kuluu vielä vuorkausi ennen kuin kaikki muut entiteetit ovat
> noutaneet uuden metadatan.


## SAML-profiili

[HAKA SAML-profiili](https://wiki.eduuni.fi/display/CSCHAKA/SAML-profiili)

[Sertifikaatin vaihtaminen](https://confluence.csc.fi/display/HAKA/SAML-varmenteen+vaihtaminen)
* "SAML-palvelun (SP) tai tunnistuslähteen (IdP) varmenteen vaihtaminen vaatii
  kaksi metadatan julkaisusykliä. Hakassa normaali metadatan uudistamissykli on
  n. 2 viikkoa, joten varaa varmenteen vaihtamiseen aikaa vähintään kuukausi.
* [Varmennekäytäntö](https://wiki.eduuni.fi/pages/viewpage.action?pageId=27297727)

[SAML-varmenteet](https://confluence.csc.fi/display/HAKA/Usein+kysytyt+kysymykset#Useinkysytytkysymykset-Varmenteet)
* Hakassa käytettävän SAML-viestien vaihdon suojaamiseen tarkoitetun varmenteen
  uusimiseen on varattava aikaa n. kuukausi.

* SAML-varmenne julkaistaan muille verkoston entiteeteille Haka-metadatassa.
  Hakassa metadatan normaali julkaisusykli on noin kerran kahden viikon aikana.

* Huomaa, että käyttäjälle näkyvän HTTP-liikenteen suojaamiseen käytettävän
  SSL-varmenteen ei tarvitse olla sama, jota käytetään Hakassa SAML-viestien
  suojaamiseen.


## CSC Hakan Testipalvelu

* [Hakan ohjeet Testipalvelusta](https://wiki.eduuni.fi/display/CSCHAKA/Testipalvelimet)

* [Hakan Test metadata](https://haka.funet.fi/metadata/haka_test_metadata_signed.xml)
  * Testipalvelun sertifikaatti on todennäköisesti päivittynyt edellisten
    vaalien jälkeen. Sertifikaatti on attribuutin `X509Certificate` arvo
    Kohdassa `<EntityDescriptor .. entityID="https://testidp.funet.fi/idp/shibboleth">`
  * Kopioi Hakan testipalvelun päivitetyt sertifikaatit hakemistoon
    voting-apin hakemistoon `doc/example_certificates`.

* Vaalijärjestelmän Hakaan rekisteröity testi-SP:
  * localhost.enemy.fi on 127.0.0.1, Hakaan ei voi rekisteröidä pelkkää `localhost`
  * SP Login page URL: http://localhost.enemy.fi:3000/haka/auth/new
  * SP Consumer URL: http://localhost.enemy.fi:3000/haka/auth/consume
  * Issuer: hyy.voting.test.local
  * Name identifier format: urn:oasis:names:tc:SAML:2.0:nameid-format:persistent
  * Sertifikaatit: kysy Petrus Repo
  * [Resurssirekisterissä id=773](https://rr.funet.fi/rr/sp_view.php?id=773)

* Asenna esim `npm install -g local-ssl-proxy` ja ohjaa `localhost:3001`
  Rails-serverin porttiin PORT=3000:
  - `local-ssl-proxy --source 3001 --target 3000`


## Sertifikaatti Hakan resurssirekisteriin

Generoitu self-signed cert:
`openssl req -x509 -nodes -newkey rsa:2048 -keyout key.pem -out cert.pem -days 3650`

* Hakan vaatima minimipituus 2048 bits

* `DirName:/C=FI/ST=ESL/L=Helsinki/O=Helsingin yliopiston ylioppilaskunta/CN=vaalit.hyy.fi`

* Jätä email tyhjäksi

Näytä sertifikaatin tiedot:
`openssl x509 -in cert.pem -text -noout`


# Issues:
* [SAML2 metadata is not found from EntitiesDescriptor (plural) with idp_metadata_parser()](https://github.com/onelogin/ruby-saml/issues/346)
