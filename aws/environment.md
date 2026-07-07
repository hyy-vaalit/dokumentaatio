# Amazon AWS -ympäristön määrittäminen

Jokaiselle palvelinympäristölle (tuotanto, qa) on omat asetuksensa.

Hallintapaneeli löytyy osoitteesta https://hyy-vaalit.signin.aws.amazon.com/console

Seuraavat stepit on tehtävä jokaiseen käytettävään S3-bucketiin.

* Luo S3:een uusi hakemisto, johon kirjoitetaan vaalitulos.
  - Avaa Services > S3 > BUCKET
  - BUCKET: vaalitulos.hyy.fi, vaalitulostin-qa
  - Bucketin nimi asetetaan Vaalitulostimen ympäristömuuttujaan
    `AWS_S3_BUCKET_NAME` (koodissa `Vaalit::Results::AWS_S3_BUCKET_NAME`).
  - [ ] Luo buckettiin vuosiluvulle uusi hakemisto.

* Anna kirjoitusoikeus edellä luotuun hakemistoon.
  - Avaa Services > IAM > Users
  - Valitse palvelun käyttäjätunnus (esim vaalitulostin-qa)
  - Valitse Permissions > Inline Policies > nimi > Edit Policy
  - Muokkaa vuosiluku Inline Policyn kohtaan:
    `"Resource": [ "arn:aws:s3:::vaalitulostin-qa/2016/*" ]`
  - Vaalitulostin kirjoittaa vuosiluvun mukaiseen hakemistoon:
    `Vaalit::Results::DIRECTORY` johdetaan automaattisesti kuluvasta
    vuodesta, sitä ei konfiguroida.

* Tarkista, ettei millään AWS IAM -käyttäjällä ole kirjoitusoikeutta
  aiempien vaalien hakemistoihin. Näin vanhat tulokset ovat turvassa kämmäilyltä.

* Testaa Vaalitulostimen AWS-kirjoitusoikeus (`rails console`):
```ruby
S3Publisher.new.test_write
```
  - Huomaa, että Vaalitulostin kirjoittaa S3:een ainoastaan
    production-ympäristössä. Muissa ympäristöissä kirjoitus ohitetaan.
