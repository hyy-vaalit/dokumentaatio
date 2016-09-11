# Heroku: Vaalien lopuksi

Vaalien päätyttyä Heroku-palveluun liitetty tietokanta poistetaan.
Ehdokastietojärjestelmä ja vaalituloksen laskentapalvelu kytketään pois käytöstä.
Äänestyspalvelu vaalit.hyy.fi voidaan jättää eloon, mutta tällöin tietokanta on
ensin poistettava ja sen jälkeen luotava tyhjästä uudelleen. Äänestyspalvelu
näyttää vaalien jälkeen infosivun ja linkin vaalitulokseen.


## Heroku

- Poista jokaisen palvelun tietokanta
  - [ ] `heroku pg:reset DATABASE --app APP_NAME`

- Poista kaikki mahdolliset varmuuskopiot, jotka henkilötietoja sisältävistä
  tietokannoista on otettu vaalien aikana.

- Avaa Overview > Dyno formation
  - Tarkista, että web ja worker ovat "off"-tilassa

- Avaa Resources
  - Poista muut AddOnit, mutta *säilytä* SendGrid.
    - Uuden applikaation luominen Sendgridiin joutuu sähköpostin lähettämisen
      osalta karanteeniin. Kun Sendgrid-palvelu jätetään henkiin, karanteenia
      ei ole, kun palvelu seuraavan kerran otetaan käyttöön.
    - Jos joudut luomaan uuden Heroku palvelun, lähetä Sendgridille
      support-pyyntönä karanteeinin poistaminen. Pahimmillaan karanteeni
      viivästyttää sähköpostin lähetystä jopa 12 tunnilla (koettu
      hallintovaaleissa syksyllä 2015).
