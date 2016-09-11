# Amazon AWS

[Tuloslaskentapalvelu](https://github.com/hyy-vaalit/vaalitulostin) tallettaa
lasketun vaalituloksen ja äänet Amazon S3  -palveluun teksti- ja JSON-muodossa.
Valituloksesta S3:een talletettava tieto on julkista. Tavoitteena on, että
vaalitulos talletetaan itsenäiseen pysyvään ympäristöön, joka ei ole
riippuvainen muissa verkkopalveluissa tapahtuvista muutoksista. Tällöin
esimerkiksi HYYn omilla verkkosivuilla tehtävät muutokset eivät
riko aiempien vaalitulosten arkistoa tai vaalitulosten URL-polkuja.

Vaalitulokset löytyvät osoitteesta http://vaalitulos.hyy.fi/VUOSILUKU.
Esitysmuodon luo [vaalien tulospalvelu](https://github.com/hyy-vaalit/vaalitulos).
Tulospalvelu on vaalituloksen S3-bucketiin talletettu HTML5-sivusto, joka
käyttää tietolähteenään laskentapalvelun bucketiin tallettamaa JSON-dataa.


## Sisällysluettelo

* [AWS-ympäristön määrittäminen](environment.rb)
* [Vaalien lopuksi](removal.md)
