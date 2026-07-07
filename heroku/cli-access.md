# Pääsyoikeudet Heroku CLI:lle

Heroku poisti SSH-pohjaisen git-deployn käytöstä vuonna 2021. Deploy
tapahtuu HTTPS:llä, ja `heroku`-komentorivityökalu tunnistautuu
API-avaimella (OAuth-tokenilla), jonka `heroku login` luo.

Dual Control -periaate säilyy ennallaan: jokaista sessiota varten
luodaan tuoreet pääsyoikeudet, ja session päätteeksi ne mitätöidään.
Näin varmistetaan, ettei kenelläkään ole sessioiden välillä voimassa
olevaa pääsyä.

## Sisäänkirjautuminen Dual Control -sessiossa

- Kirjaudu komentoriviltä Owner-käyttäjätunnukselle:
  - `heroku login`
  - Kirjautuminen avaa selaimen ja kysyy salasanan sekä kaksivaiheisen
    tunnistautumisen koodin. Salasanan tuntee toinen ATK-vastaava ja
    koodigeneraattori on toisella — kirjautuminen onnistuu vain yhdessä.
  - CLI tallettaa syntyneen tokenin koneen avainnippuun (macOS
    Keychain) tai `~/.netrc`-tiedostoon.

- Tarkista, ettei tunnuksella ole ylimääräisiä valtuutuksia:
  - `heroku authorizations`
  - Listalla saa olla vain juuri luotu CLI-valtuutus.
  - Poista ylimääräiset: `heroku authorizations:revoke ID`

## Käyttäjätunnuksen on pysyttävä Dual Controllissa

Suosituksena on käyttää vain Owner-pääkäyttäjätunnusta.
Owner-käyttäjätunnus on todettu Dual Control -sessiossa puhtaaksi
aiemmin, kun [Heroku-ympäristö on luotu](environment.md).

Jos CLI-pääsy annetaan Collaborator-tunnukselle, sille on tehtävä
Dual Controllissa samat puhdistustoimenpiteet kuin
pääkäyttäjätunnukselle [ympäristön pystytysvaiheessa](environment.md):
salasanan vaihto, valtuutusten poisto ja API-avaimen
uudelleengenerointi. Vähemmän työlästä on käyttää vain
Owner-pääkäyttäjätunnusta.

## Dual Control -session päätteeksi

- Kirjaudu ulos komentoriviltä: `heroku logout`

- Mene heroku.com > [Manage Account](https://dashboard.heroku.com/account)
  - Paina "Regenerate API Key" kohdassa "API Key".
    - Tämä mitätöi kaikki `heroku`-komentorivityökalun sessiot ja
      tokenit — myös muilla koneilla olevat.
  - Tarkista, että "SSH Keys" on tyhjä. Avaimia ei enää käytetä
    deployhin, joten listalla ei kuulu olla mitään.

- Tarkista, että Access-välilehden "Collaborators"-listalla ei ole muita
  kuin Owner-pääkäyttäjätunnus.
