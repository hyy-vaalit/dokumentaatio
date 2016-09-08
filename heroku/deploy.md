# Palvelun deploy

## Valmistelut: Rollbar.com

Palvelut raportoivat virhetilanteet Rollbariin.

- Hanki server side access token. Tämä asetetaan jäljempänä ympäristömuuttujaksi.

- Aseta deploy hook
  - Mene https://rollbar.com/hyy/voting-api/deploys/
  - Valitse "Heroku"
  - Etsi copypastettava komento, esimerkiksi:
    `heroku addons:create deployhooks:http --url="https://api.rollbar.com/api/1/deploy/?access_token=SEKRIT&environment=production"`
  - Vaihda parametrin lopusta ympäristön nimi (esim "qa")

## Valmistelut: Loggly

Palvelut lähettävät login Logglyyn.

- Luodaan Heroku Drain:
  - Avaa [Logglyn hallintapaneeli](https://hyy.loggly.com/sources/setup/heroku-app-setup)
  - Etsi copypastettava komento, esimerkiksi:
    `heroku drains:add https://logs-01.loggly.com/bulk/SEKRIT/tag/heroku --app APP_NAME`


## Heroku

- Avaa Heroku.com > Settings.
  - Hanki Git URL palvelun sivulta Settings > Info

- Lisää git remote voting-apiin:
  - `git remote add production https://git.heroku.com/hyy-vaalit.git`

- Aseta ympäristömuuttujat ennen deployta:
  - `cp .env.example .env.deploy`
  - Muokkaa halutut arvot `.env.deploy`
  - Lue asetukset: `bin/read_env_for_heroku_config.sh .env.deploy`
    - skripti tuottaa komennon `heroku config KEY=VALUE`
    - Aseta ympäristömuuttujat Herokuun:
      - `heroku config:set X=x, Y=y -r production`
  - Huomioi talviaikaan siirtyminen, joka todennäköisesti tapahtuu vaalien aikana.
    Aseta äänestämisen alkamisajankohtaan aikavyöhykkeen offsetiksi +0300
    ja loppumisajankohdan offsetiksi +0200.

- Aja Deploy:
  - `git push production master`
  - masterin on syytä vastata aina tuotannossa olevaa versiota. Jos deployaat
    tietyn tagin, käytä `git push production TAG_NAME:master`. Herokun remote
    branchin pitää olla aina `master`.


- Luo tietokannan skeema:
  - `heroku run rake db:schema:load -r production`  

- Siivoa jäljet:
  - git status
  - rm .env.deploy

- Login saa näkyville:
  - `heroku logs --tail -r production`


## Lopuksi

- Heroku.com > Settings:
  - [ ] Maintenance Mode: Off
  - komentoriviltä: `heroku maintenance:off -a PALVELU`
