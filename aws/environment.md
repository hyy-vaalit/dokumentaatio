# Amazon AWS -ympäristön määrittäminen

Jokaiselle palvelinympäristölle (tuotanto, qa, test) on omat asetuksensa.

Hallintapaneeli löytyy osoitteesta https://hyy-vaalit.signin.aws.amazon.com/console

Seuraavat stepit on tehtävä jokaiseen käytettävään S3-bucketiin.

* Luo S3:een uusi hakemisto, johon kirjoitetaan vaalitulos.
  - Avaa Services > S3 > BUCKET
  - BUCKET: vaalitulos.hyy.fi (tuotanto), vaalitulostin-qa, vaalitulostin-test
  - Bucketin nimi asetetaan Vaalitulostimen ympäristömuuttujaan
    `AWS_S3_BUCKET_NAME` (koodissa `Vaalit::Results::AWS_S3_BUCKET_NAME`).
  - [ ] Luo buckettiin vuosiluvulle uusi hakemisto.
  - Hakemiston nimi on kuluva vuosi: Vaalitulostin päättelee sen
    automaattisesti (`Vaalit::Results::DIRECTORY = Time.now.year`),
    eli sitä ei konfiguroida erikseen.

* Anna kirjoitusoikeus edellä luotuun hakemistoon.
  - Avaa Services > IAM > Users
  - Valitse palvelun käyttäjätunnus (esim vaalitulostin-qa)
  - Valitse Permissions > Inline Policies > nimi > Edit Policy
  - Muokkaa vuosiluku Inline Policyn kohtaan:
    `"Resource": [ "arn:aws:s3:::vaalitulostin-qa/2026/*" ]`

* Tarkista, ettei millään AWS IAM -käyttäjällä ole kirjoitusoikeutta
  aiempien vaalien hakemistoihin. Näin vanhat tulokset ovat turvassa kämmäilyltä.

* Testaa Vaalitulostimen AWS-kirjoitusoikeus (`rails console`):
```ruby
S3Publisher.new.test_write
```

Huom: Vaalitulostin kirjoittaa S3:een vain tuotantomoodissa
(`Rails.env.production?`). Kehitys- ja QA-ympäristössä lokaalisti ajettuna
S3-kirjoitus ohitetaan, mikä näkyy lokissa "Not storing to S3" -viestinä.
