# Järjestelmien yleiskuvaus

HYYn vaalijärjestelmä koostuu erillisistä palveluista, joilla on kullakin
oma vastuualueensa vaaliprosessin eri vaiheissa. Tämä sivu kuvaa palvelut
ja niiden väliset tietovirrat.

```
ehdokastiedot ──CSV──> vaalitulostin <──HTTP (äänet)── voting-api
                            │                              ▲
                            │ HTML + JSON                  │ selain
                            ▼                              │
                    S3: vaalitulos.hyy.fi          voting-frontend
                    (vaalitulos-sivusto)           (dist voting-apin
                                                    public/-hakemistossa)
```

## Palvelut

### ehdokastiedot — ehdokasrekisteri

[github.com/hyy-vaalit/ehdokastiedot](https://github.com/hyy-vaalit/ehdokastiedot)

Ehdokasasettelun järjestelmä. Käyttäjärooleja ovat admin, keskusvaalilautakunnan
sihteerit, vaaliliittojen edustajat (asiamiehet) sekä ehdokkaat itse
(itserekisteröityminen Haka-kirjautumisella ja kutsukoodilla).
Vaaliliitot, vaalirenkaat ja ehdokkaat syötetään ja hyväksytään täällä.
Tuottaa CSV-exportit, jotka toimivat Vaalitulostimen ja voting-apin
lähtödatana.

### vaalitulostin — tuloslaskenta

[github.com/hyy-vaalit/vaalitulostin](https://github.com/hyy-vaalit/vaalitulostin)

Laskee vaalituloksen. Tietovirrat:

* Ehdokkaat, vaaliliitot ja vaalirenkaat tuodaan ehdokastiedot-palvelun
  CSV-exporteista (`rake db:seed:edari`).
* Äänet haetaan voting-apista HTTP-rajapinnan yli ServiceUser
  JWT -tunnuksella äänestyksen päätyttyä.
* Arvonnat (tasatilanteet) suoritetaan kolmivaiheisesti: ehdokkaat
  vaaliliiton sisällä, vaaliliittojen vertausluvut renkaan sisällä,
  renkaiden vertausluvut.
* Valmis tulos julkaistaan HTML- ja JSON-muodossa S3:een
  (vaalitulos.hyy.fi). Julkaisu tapahtuu vain tuotantomoodissa.

Vaalitulostimen admin-käyttöliittymää käytetään myös äänestäjien
manuaaliseen lisäämiseen äänestyksen aikana — varsinainen äänestäjän
luonti ja sisäänkirjautumislinkin lähetys tapahtuu voting-apissa
(ks. [Äänestyksen aikana](elections/during.md)).

### voting-api — äänestyspalvelun backend

[github.com/hyy-vaalit/voting-api](https://github.com/hyy-vaalit/voting-api)

Äänestyksen backend:

* Sisäänkirjautuminen Haka SAML -kirjautumisella (ruby-saml) tai
  manuaalisesti lisätyille äänestäjille sähköpostitse lähetettävällä
  sisäänkirjautumislinkillä (AWS SES).
* Äänioikeusrekisteri tuodaan yliopistolta (CSV/teksti/XML-importerit,
  `db:seed:edari:voters`, `bin/seed-edari`).
* Äänet ovat muuttumattomia (immutable). Äänestyksen päättymisen jälkeen
  on armonaika (`VOTING_GRACE_PERIOD_MINUTES`), jonka aikana ääniä ei voi
  viedä ulos.
* Export-rajapinta, josta Vaalitulostin hakee äänet.

### voting-frontend — äänestyksen käyttöliittymä

[github.com/hyy-vaalit/voting-frontend](https://github.com/hyy-vaalit/voting-frontend)

Äänestäjän selaimessa toimiva käyttöliittymä (Vue 3). Buildataan
[voting-frontend-dist](https://github.com/hyy-vaalit/voting-frontend-dist)
-repoon (`bin/distribute.sh`), joka on liitetty voting-apin
`public/`-hakemistoon git-submodulena. Frontend jaellaan siis
voting-apin deployn mukana.

### vaalitulos — staattinen tulossivusto

[github.com/hyy-vaalit/vaalitulos](https://github.com/hyy-vaalit/vaalitulos)

HTML5-sivusto, joka esittää Vaalitulostimen S3:een julkaiseman
JSON-datan osoitteessa http://vaalitulos.hyy.fi/VUOSILUKU. Sivusto
talletetaan samaan S3-buckettiin vuosiluvun hakemistoon; se päivitetään
vuosittain uuden vaalin hakemistoa varten. Ks. [Amazon AWS](aws/README.md).

## Nimeämiskäytäntö

Näissä dokumenteissa:

* **Vaalitulostin** = tuloslaskentapalvelu (vaalitulostin-repo).
* **vaalitulos-sivusto** = staattinen tulossivusto S3:ssä (vaalitulos-repo).
