
# SSL-sertifikaatin lisääminen Herokuun

Herokussa on kaksi eri lisäpalvelua SSL-suojaukselle:
1. Perinteinen lisämaksullinen [SSL Endpoint](https://devcenter.heroku.com/articles/ssl-endpoint)
   * Tämä add-on antaa jokaiselle SSL-endpointille oman IP-osoitteen.
2. Kesällä 2016 betaan tullut ilmainen [Heroku SSL (Beta)](https://devcenter.heroku.com/articles/ssl-beta)
   * Tämä add-on hyödyntää SNI:tä, jossa yhden IP-osoitteen takana voi olla
     monta SSL-sertifikaattia. Tämän selaintuki on vuonna 2016 riittävä.

## Enable Labs

```bash
heroku labs:enable http-sni -a PALVELU
heroku plugins:install heroku-certs
```

## Hanki sertifikaatti

Luotettava ja edullinen toimija on [RapidSSL](https://www.RapidSSL.com).

RapidSSL lähettää sertifikaattihakemuksesta vahvistuksen yhteen päätason
verkkotunnuksen hallinnollisesta sähköpostiosoitteita. Näitä ovat esimerkiksi
root@, administrator@, hostmaster@.

Lopulta RapidSSL antaa saitin sertifikaatin ja intermediate sertifikaatin,
jotka pitää yhdistää samaan tiedostoon:

```bash
cat vaalit.hyy.fi.crt intermediate.crt > vaalit.hyy.fi.combined.pem
```

## Lisää sertifikaatti Herokuun

```bash
 heroku _certs:add vaalit.hyy.fi.combined.pem server.key -a PALVELU
 ```

Aiemman sertifikaatin päivitys:
```bash
heroku _certs:update server.crt server.key
```

Jos ajossa tapahtuu virhe, poista sertifikaatti `_certs:remove`:lla ja yritä
uudelleen.

Sertifikaatin tiedot:
```bash
heroku _certs:info
```
