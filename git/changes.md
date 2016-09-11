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

Kun olet lisännyt git remoteksi Herokun, näet git logista mikä kohta
git-historiaa on tällä hetkellä deployattu:
  - `git remote add production heroku-url`
  - `git fetch -a`
  - katso missä kohtaa `production` menee:
    ![Track Git remotes](../git/track-remotes.jpg)
