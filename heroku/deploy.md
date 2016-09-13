# Palvelun deploy

Palvelun deploy tarkoittaa palvelun koodin, ympäristön ja asetusten luomista
palveluntarjoajalle. Deployn suorittavat atk-vastaavat yhdessä noudattaen [Dual
Control](dual-control.md) -periaatetta.

Ensimmäinen deploy kannattaa harjoitella tuotantoa vastaavassa
Staging-ympäristössä. Staging-ympäristöön voidaan luoda testikäyttäjiä,
joilla vaalin vaiheet voidaan kuivaharjoitella vielä kertaalleen juuri ennen
tuotantoon siirtymistä. Staging-ympäristö kannattaa pystyttää samalla tavalla
kuin tuotantoympäristö. Tällöin myös [ympäristön pystyttäminen](environment.md)
voidaan kuivaharjoitella.


## Valmistelut: Rollbar.com

Palvelut raportoivat virhetilanteet Rollbariin.

- Hanki server side access token. Tämä asetetaan jäljempänä ympäristömuuttujaksi.

- Aseta deploy hook
  - Mene https://rollbar.com/hyy/voting-api/deploys/
  - Valitse "Heroku"
  - Etsi copypastettava komento, esimerkiksi:
    `heroku addons:create deployhooks:http --url="https://api.rollbar.com/api/1/deploy/?access_token=SEKRIT&environment=production"`
  - Vaihda parametrin lopusta ympäristön nimi (esim `qa`)


## Valmistelut: Loggly

Palvelut lähettävät login Logglyyn.

- Luodaan Heroku Drain:
  - Avaa [Logglyn hallintapaneeli](https://hyy.loggly.com/sources/setup/heroku-app-setup)
  - Etsi copypastettava komento, esimerkiksi:
    `heroku drains:add https://logs-01.loggly.com/bulk/SEKRIT/tag/heroku -a PALVELU`


## Valmistelut: asennettavan ohjelmistoversion varmistaminen

Palvelun git `master` branchin on syytä vastata tuotannossa olevaa versiota.
Jokainen deploy luo uuden git tagin, jolloin git historiasta pystyy näkemään
tuotannon deploy-historian.

Palvelun deploy suoritetaan komennolla `bin/deploy`, joka luo tarvittavat git
tagit.

Deploy on suoritettava siten, ettei se ole ristiriidassa [dual
control](dual-control.md) -periaatteen suhteen. Kaksi atk-vastaavaa yhdessä
suorittavat deployn ja poistavat lopuksi itseltänsä deployn ajaksi annetut
ylläpidon pääsyoikeudet.

Ennen deployn suorittamista,
[varmistetaan vertaisarvioiden git-historiasta](../git/changes.md)
muutokset aiempaan turvalliseksi todettuun versioon nähden.

## Heroku: Ensimmäinen deploy

Suoritetaan palvelun ensimmäinen deploy Herokuun.

- Avaa Heroku.com > Settings.
  - Hanki Git URL palvelun sivulta Settings > Info

- Lisää Herokun git remote:
  - `git remote add production https://git.heroku.com/PALVELU.git`

- Aseta ympäristömuuttujat ennen deployta:
  - `cp .env.example .env.deploy`
  - Muokkaa halutut arvot `.env.deploy`
  - Lue asetukset: `bin/env_for_heroku_config.sh .env.deploy`
    - skripti tuottaa komennon `heroku config KEY=VALUE`
    - Aseta ympäristömuuttujat Herokuun:
      - `heroku config:set KEY_1="VALUE_1" [..] KEY_N="VALUE_N" -r production`
  - Huomioi talviaikaan siirtyminen, joka todennäköisesti tapahtuu vaalien aikana.
    Esimerkiksi: Aseta äänestämisen alkamisajankohtaan aikavyöhykkeen
    offsetiksi +0300 ja loppumisajankohdan offsetiksi +0200.

- Suorita deploy:
  - `bin/deploy production master`
  - Palvelun masterin on syytä vastata aina tuotannossa olevaa versiota.

- Luo tietokannan skeema:
  - `heroku run rake db:schema:load -r production`  

- Syötä palvelun seed-data:
  - `heroku run rake db:seed:TASK`,
    jossa vaadittu `TASK` on kuvattu kunkin palvelun README:ssa.

- Siivoa jäljet:
  - `git status`
  - `rm .env.deploy`

- Login saa näkyville:
  - `heroku logs --tail -r production`

- Herokun ympäristömuuttujat saa näkyville:
  - `heroku config -r production`

- Jos joudut käyttämään ylläpitokonsolia, jokin on mennyt pieleen. Korjaa deploy
  sellaiseksi, että se voidaan suorittaa ilman konsolin avaamista. Konsolin saa
  tarvittaessa auki:
  - `heroku run console -r production`

- Logi deployn ja ympäristömuuttujien historiasta:
  - `heroku releases -r production`

## Lopuksi

- Ota maintenance mode pois päältä.
  - Mene Heroku.com > Settings:
    - [ ] Maintenance Mode: Off
  - Komentoriviltä: `heroku maintenance:off -a PALVELU`
- Poista ylimääräiset pääsyoikeudet.
- Allekirjoita vaalipöytäkirja.
