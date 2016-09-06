# Amazon AWS -ympäristö

Jokaiselle palvelinympäristölle (tuotanto, qa, staging) on omat asetuksensa.

Hallintapaneeli löytyy osoitteesta https://hyy-vaalit.signin.aws.amazon.com/console

Seuraavat stepit on tehtävä jokaiseen S3-bucketiin, jota käytetään (tuotanto, staging, koe):

* Luo S3:een uusi hakemisto, johon kirjoitetaan vaalitulos.
  - Avaa Services > S3 > BUCKET
  - BUCKET: vaalitulos.hyy.fi, vaalitulostin-qa, vaalitulostin-staging
  - [ ] Luo buckettiin vuosiluvulle uusi hakemisto.

* Anna kirjoitusoikeus edellä luotuun hakemistoon.
  - Avaa Services > IAM > Users
  - Valitse palvelun käyttäjätunnus (esim vaalitulostin-qa)
  - Valitse Permissions > Inline Policies > nimi > Edit Policy
  - Muokkaa vuosiluku Inline Policyn kohtaan:
    `"Resource": [ "arn:aws:s3:::vaalitulostin-qa/2016/*" ]`
  - Vaalitulostin lukee hakemiston muuttujasta `Vaalit::Results::DIRECTORY`

* Tarkista, ettei millään AWS IAM -käyttäjällä ole kirjoitusoikeutta
  aiempien vaalien hakemistoihin. Näin vanhat tulokset ovat turvassa kämmäilyltä.

* Testaa Vaalitulostimen AWS-kirjoitusoikeus:
```ruby
AWS::S3::S3Object.store("#{Vaalit::Results::DIRECTORY}/lulz.txt", "Lulz: #{Time.now}", Vaalit::Results::S3_BUCKET_NAME, :content_type => 'text/html; charset=utf-8')
```
