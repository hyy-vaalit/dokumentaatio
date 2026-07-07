# Ehdokastiedot — ehdokasasettelu

[Ehdokastiedot](https://github.com/hyy-vaalit/ehdokastiedot) on
ehdokasasettelun järjestelmä. Siinä vaaliliittojen edustajat (asiamiehet)
perustavat vaaliliitot, ehdokkaat ilmoittautuvat, keskusvaalilautakunnan
sihteerit hyväksyvät tiedot ja lopuksi ehdokkaille arvotaan
ehdokasnumerot. Järjestelmän CSV-exportit toimivat Vaalitulostimen ja
voting-apin ehdokasdatan lähteenä.

## Käyttäjäroolit

* **Admin / sihteeri** — hallintanäkymä: vaalirenkaiden ja edustajien
  luonti, vaaliliittojen hyväksyntä (freeze), ehdokasnumeroiden arvonta,
  massasähköpostit, muutosloki, CSV-exportit.
* **Vaaliliiton edustaja (asiamies)** — luo vaaliliiton, liittää sen
  vaalirenkaaseen, hyväksyy oman liittonsa ehdokkaat.
* **Ehdokas** — rekisteröityy itse Haka-kirjautumisella ja vaaliliiton
  kutsukoodilla, ylläpitää omia tietojaan, voi perua ehdokkuutensa.

## Ehdokasasettelun elinkaari

Kolme vaihetta, joita ohjaavat ympäristömuuttujat (Herokussa config
vars — määräaikojen muuttaminen on Heroku-konfiguraation muuttamista):

1. **Ehdokasasettelu** — alkaa `CANDIDATE_NOMINATION_STARTS_AT`,
   päättyy `CANDIDATE_NOMINATION_ENDS_AT`. Ehdokkaat voivat
   rekisteröityä ja muokata tietojaan vapaasti.
2. **Korjausaika** — asettelun päättymisestä hetkeen
   `CANDIDATES_FROZEN_AT`. Tietoja voi vielä korjata, mutta jokainen
   muutos kirjautuu muutoslokiin (hallintanäkymän
   "Muutokset ehdokastiedoissa").
3. **Jäädytetty** — `CANDIDATES_FROZEN_AT` jälkeen ehdokastiedot ovat
   lukittuja.

## Ehdokkaan itserekisteröityminen

Ehdokas kirjautuu Haka-tunnuksella ja syöttää vaaliliittonsa
**kutsukoodin** (jokaisella vaaliliitolla oma koodi) — näin ehdokas voi
liittyä vain liittoon, johon hänet on kutsuttu. Ehdokas voi myös perua
ehdokkuutensa itse ("Peru ehdokkuus"), ja perutut ehdokkuudet näkyvät
hallintanäkymässä.

## Vaalityöntekijän tehtävät

### Edustajien kirjautumisen pääkytkin

Hallintanäkymän Danger Zone -sivulla on kytkin, joka sulkee kaikkien
vaaliliittojen edustajien sisäänkirjautumisen. Sulje kirjautuminen
ennen ehdokasnumeroiden arvontakokousta ja avaa se tarvittaessa
kokouksen jälkeen.

### Vaaliliiton hyväksyntä (freeze)

Vaaliliitto voidaan jäädyttää vain, kun hyväksyttyjen ehdokkaiden määrä
täsmää liiton ilmoittamaan ehdokasmäärään (ja määrä on suurempi kuin
nolla). Muussa tapauksessa järjestelmä estää jäädytyksen.

### Ehdokasnumeroiden arvonta (Danger Zone)

Numerointi onnistuu vain kun kaikki esiehdot täyttyvät:

* kaikki vaaliliitot on jäädytetty (hyväksytty),
* kaikilla vaalirenkailla on numerojärjestys,
* yhdelläkään ehdokkaalla ei ole puuttuvaa vaaliliittoa,
* yksikään vaaliliitto ei ole ilman vaalirengasta.

Numerointi alkaa numerosta 2 ja etenee renkaiden, liittojen ja
ehdokkaiden arvotussa järjestyksessä.

### Massasähköposti ehdokkaille

Hallintanäkymästä voi lähettää kaikille ehdokkaille sähköpostin
(esim. äänestyksen alkamisesta). **Lähetys vaatii worker-dynon** —
viestit jonotetaan taustatyönä. Sammuta worker lähetyksen jälkeen.

### CSV-exportit

Hallintanäkymästä saa CSV-exportit ehdokkaista, vaaliliitoista ja
vaalirenkaista. Exporteista on UTF-8- ja ISO-Latin-versiot (ISO-Latin
Exceliä varten). Vaalitulostin ja voting-api lukevat ehdokasdatan
näistä exporteista (ks. [Järjestelmien yleiskuvaus](../architecture.md)).

## Kuvakaappaukset

* [admin/](admin/) — vaalirenkaan, edustajan ja tiimin luonti
* [edustaja/](edustaja/) — vaaliliiton perustaminen ja ehdokkaiden
  hyväksyntä edustajan näkymässä
* [ehdokas/](ehdokas/) — ehdokkaan omat tiedot, muokkaus ja ehdokkuuden
  peruminen
