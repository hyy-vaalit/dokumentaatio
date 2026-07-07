# Amazon AWS

[Vaalitulostin](https://github.com/hyy-vaalit/vaalitulostin) tallettaa
lasketun vaalituloksen ja äänet Amazon S3 -palveluun teksti- ja JSON-muodossa.
Vaalituloksesta S3:een talletettava tieto on julkista. Tavoitteena on, että
vaalitulos talletetaan itsenäiseen pysyvään ympäristöön, joka ei ole
riippuvainen muissa verkkopalveluissa tapahtuvista muutoksista. Tällöin
esimerkiksi HYYn omilla verkkosivuilla tehtävät muutokset eivät
riko aiempien vaalitulosten arkistoa tai vaalitulosten URL-polkuja.

Vaalitulokset löytyvät osoitteesta http://vaalitulos.hyy.fi/VUOSILUKU.
Esitysmuodon luo
[vaalitulos-sivusto](https://github.com/hyy-vaalit/vaalitulos).
Vaalitulos-sivusto on vaalituloksen S3-bucketiin talletettu
HTML5-sivusto, joka käyttää tietolähteenään Vaalitulostimen bucketiin
tallettamaa JSON-dataa.


## Muut AWS-palvelut

S3:n lisäksi AWS:ää käyttävät myös muut vaalijärjestelmät:

* **voting-api** ja **ehdokastiedot** lähettävät sähköpostit AWS SES
  -palvelun kautta. Kummallakin on oma IAM-käyttäjä, jonka Access Key
  asetetaan palvelun ympäristömuuttujiin.
* **Vaalitulostin** kirjoittaa S3:een omalla IAM-käyttäjällään.

Kaikkien kolmen sovelluksen IAM-käyttäjät ja avaimet kuuluvat saman
elinkaaren piiriin: avaimet luodaan ympäristön pystytyksen yhteydessä ja
poistetaan vaalien päätyttyä (ks. [Vaalien lopuksi](removal.md)).


## Sisällysluettelo

* [AWS-ympäristön määrittäminen](environment.md)
* [Vaalien lopuksi](removal.md)
