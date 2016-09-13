# HYYn vaalijärjestelmien dokumentaatio

Näissä dokumenteissa kuvataan vaalijärjestelmien asentamiseen ja ylläpitoon
liittyvät työvaiheet.

Dokumentaation kohderyhmänä on vaalien atk-vastuuhenkilöstö. Kuvakusissa ei
selitetä auki kaikkia teknisiä esitietovaatimuksia. Tarkistuslistojen
ensisijainen tehtävä on varmistaa, että tuotantoympäristön konfiguroinnissa
muistetaan tehdä kaikki työvaiheet. Lisäksi tarkistuslistat mahdollistavat
myös ylläpitoprosessin ulkopuolisen tarkkailun esimerkiksi pääsihteerin tai
Keskusvaalilautakunnan jäsenen toimesta.

HYYn Keskusvaalilautakunta on 30.6.2016 linjannut, että kaikki
[äänestys-](https://github.com/hyy-vaalit/voting-api) ja
[tuloslaskentajärjestelmän](https://github.com/hyy-vaalit/vaalitulostin)
ylläpitotoiminnot tulee määritellä sellaisiksi, että niihin vaaditaan
vähintään kaksi henkilöä ([Dual Control -periaate](heroku/dual-control.md)).
Ylläpitotoiminnoilla tarkoitetaan kaikkia niitä toimintoja, joiden kautta pääsee
vaalien aikana käsiksi Heroku.com-hallintapaneeliin tai
`heroku`-komentorivityökaluun.

Vaaliprosessin perinpohjaisuuden tarkoituksena on turvata vaalituloksen
luotettavuus tilanteessa, jossa joku haastaa organisaation oikeuteen tai pyrkii
muutoin mitätöimään vaalituloksen. Lisäksi Dual Control tarjoaa ylläpidolle
oikeusturvaa: prosessia noudattaen yksittäisen ylläpitäjän ei ole mahdollista
peukaloida vaalitulosta. Prosessin mukainen huolellinen toiminta on tarvittaessa
voitava näyttää toteen, sen vuoksi muun muassa asianmukaisten vaalipöytäkirjojen
kirjoittaminen on tärkeää.


## Sisällysluettelo

* [Git-versionhallinta](git/README.md)
* [Heroku](heroku/README.md)
* [CSC Haka](haka/README.md)
* [Amazon AWS](aws/README.md)
* [Vaalit](elections/README.md)


## Muokkaajalle ohjeeksi:

* Asenna editoriisi Markdown-plugin, jotta näet kirjoittaessasi live-previewin.
  - Atom: markdown-preview

* Säilytä 80-merkin rivipituus.
  - Tällöin Markdown säilyy luettavamampa myös kirjoitustilassa.
  - Käytä editorissa joko
    a) pakotettua rivipitutta tai
    b) automaattista rivitystoimintoa (justify).
  - Atom, justify text: 1) select text 2) cmd-alt-q
