# Muutokset projekteissa

Äänestyspalvelun API ja vaalituloksen laskentapalvelu on auditoitu
syksyllä 2016 Nixun toimesta. Audit on merkitty git historiaan tagilla `X`
(#TODO päivitä taginnimi tähän). Sen lisäksi jokainen tuotantodeploy
saa oman tagin, jolloin git-historiasta voi seurata, mikä kohta
historiaa on deployattu.

Palveluun tehtävät muutokset vertaisarvioidaan vertaamalla edellistä
luotettavaa tilaa uuteen tilaan. Muutokset git-historiasta saa näkyviin
esimerkiksi seuraavasti:

- `git diff --stat FIRST..LAST`
- `git log -p FIRST..LAST`
- Tässä `FIRST` ja `LAST` voivat olla tageja, brancheja tai SHA-tunnisteita.

Tavoite on, että jokaisen deployn jälkeinen tila voidaan kuitata luotettavaksi,
jolloin seuraavan deployn yhteydessä riittää verrata edellisen deploy-tagin
tilaa nykyhetkeen.

## Herokuun deployatun version jäljitys

Kun paikallisessa git-repossa on remotena sekä Github että Heroku, näkee
git-logista, mikä kohta git-historiaa on tällä hetkellä deployattu:
- `git remote add production heroku-url`
- `git fetch -a`
- `git log remotes/production/master`
- tai käytä työkalua kuten `tig`, GitX tai SourceTree.

Esimerkiksi tässä kohdassa Githubin masterissa `origin/master` on uusi commit,
joka ei ole mukana Herokun `qa` tai `production` -ympäristön deployssa:
  ![Track Git remotes](../git/track-remotes.jpg)


## Deploy-tagit

Logi deployn ja ympäristömuuttujien historiasta löytyy komennolla:
- `heroku releases -r production`

Esimerkiksi:
```
=== hyy-vaalit Releases - Current: v195
v195  Deploy f1514ef                                                                           petrus.repo@iki.fi  2016/09/13 16:42:37 +0300 (~ 33s ago)
v194  Remove HEROKU_RELEASE_VERSION, HEROKU_SLUG_COMMIT, HEROKU_SLUG_DESCRIPTION config vars   petrus.repo@iki.fi  2016/09/13 15:46:39 +0300 (~ 56m ago)
v193  Set SAML_IDP_SSO_TARGET_URL, SAML_IDP_ENTITY_ID config vars                              petrus.repo@iki.fi  2016/09/08 14:40:42 +0300
```

Tässä release `v195` ympäristössä `production` on deployattu git-historian
kohdasta `f1514ef`. Lisäksi deploy-skripti `bin/deploy` on myös luonut
tagin `production-195`. Deployn kohteena olevaa versiota voi tutkia esimerkiksi
seuraavasti:
- `git log f1514ef`
- `git diff --stat f1514ef..master`
- `git diff --stat production-195..master`
- `git diff --stat production-194..production-195`

Lisäksi commitin `f1514ef` ja tagin `production-195` voi etsiä graafisella
versionhallintatyökalulla kuten `tig`, GitX tai SourceTree.
