# Vaalien lopuksi

## Heroku
- Avaa Overview > Dyno formation
  - Tarkista, että web ja worker ovat "off"-tilassa

- Avaa Resources
  - Poista muut AddOnit paitsi SendGrid.
    - Uuden applikaation luominen Sendgridiin joutuu sähköpostin lähettämisen osalta karanteeniin.
    - Säästyy vaivaa, kun jätetään ilmainen Sendgrid-addon henkiin.

## Poista AWS access keyt

- AWS Console > Services > IAM > Users
