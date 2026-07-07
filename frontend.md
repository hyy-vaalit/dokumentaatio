# Äänestyksen käyttöliittymän jakelu

Äänestäjän selaimessa toimiva käyttöliittymä kehitetään repossa
[voting-frontend](https://github.com/hyy-vaalit/voting-frontend) (Vue 3).
Buildattu versio julkaistaan repoon
[voting-frontend-dist](https://github.com/hyy-vaalit/voting-frontend-dist),
joka on liitetty voting-apin `public/`-hakemistoon git-submodulena.
Frontend päätyy siis äänestäjille voting-apin deployn mukana.

## Vuosittaiset muutokset

Dist-repoa **ei muokata käsin**. Vuosittaiset päivitykset tehdään kahteen
paikkaan:

1. **voting-frontend-lähdekoodi:**
   - Käännöstekstit: `src/locales/locale-fi.json`, `locale-en.json`,
     `locale-se.json`.
   - `#NOTE`-kommentilla merkityt kohdat komponenteissa
     (`InfoPartial.vue`, `ContactPartial.vue`, `SignUpView.vue`,
     `MainView.vue`): otsikon vuosiluku, linkki vaalitulokseen,
     päivämäärät teksteissä.

2. **voting-apin ympäristömuuttujat** (Heroku config vars):
   - `VOTE_SIGNIN_STARTS_AT`, `VOTE_SIGNIN_ENDS_AT` — äänestysaika
   - `ELECTION_TERMINATES_AT` — vaalien päättymishetki
   - `VOTE_SIGNIN_DAILY_OPENING_TIME`, `VOTE_SIGNIN_DAILY_CLOSING_TIME`
     — päivittäiset aukioloajat
   - `VOTING_GRACE_PERIOD_MINUTES` — armonaika äänestää sulkemisajan
     jälkeen, jos kirjautui sisään ennen sitä

## Julkaisupolku

1. Aja voting-frontendissä `bin/distribute.sh`: skripti buildaa
   sovelluksen ja committaa + pushaa tuloksen voting-frontend-dist
   -repoon.
2. Päivitä voting-apin `public/`-submodulen osoitin uuteen
   dist-committiin ja committaa se voting-apiin.
3. Deployaa voting-api normaalisti ([deploy-ohje](heroku/deploy.md)).

Submodule-osoittimen päivitys voting-apissa on auditoitava
deploy-yksikkö: git-historiasta näkyy täsmälleen, mikä frontend-versio
on ollut jaossa minäkin hetkenä.

> **Huom (tilanne 07/2026):** voting-frontend-dist-repon HEAD on yhä
> vanha AngularJS-build, eikä Vue 3 -versiota ole vielä jaeltu.
> Varmista ennen seuraavaa vaalia, että distribuutio ajetaan Vue 3
> -versiosta, jottei vanha frontend päädy tuotantoon.
