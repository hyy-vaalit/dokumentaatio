# SSL-sertifikaatti Herokussa

Heroku hoitaa SSL-sertifikaatit nykyään automaattisesti:
[Automated Certificate Management (ACM)](https://devcenter.heroku.com/articles/automated-certificate-management)
hankkii ja uusii Let's Encrypt -sertifikaatin ilmaiseksi. Sertifikaattia
ei enää osteta eikä asenneta käsin. ACM uusii sertifikaatin
automaattisesti kuukautta ennen vanhenemista.

Ota ACM käyttöön:

```bash
heroku certs:auto:enable -a PALVELU
```

Tarkista sertifikaatin tila ja voimassaolo:

```bash
heroku certs:auto -a PALVELU
```

Custom domainin (esim. vaalit.hyy.fi) DNS:n on osoitettava Herokun
antamaan DNS-targetiin (`heroku domains -a PALVELU`), jotta ACM voi
myöntää sertifikaatin. Jos myöntäminen epäonnistuu, korjaa DNS ja aja
`heroku certs:auto:refresh -a PALVELU`.

Oman sertifikaatin voi edelleen asentaa käsin komennolla
`heroku certs:add`, mutta vaalipalveluissa siihen ei ole tarvetta.


## OpenSSL-cheatsheet

Self-signed-sertifikaattia tarvitaan edelleen esim. Hakan
SAML-konfiguraatioon (ks. [Haka](../haka/README.md)):

* Generate a self signed cert (eg. for Haka):
  - `openssl req -x509 -nodes -newkey rsa:2048 -keyout key.pem -out cert.pem -days 3650`

* List certificate contents:
  - `openssl x509 -in cert.pem -text -noout`

* Create a certificate signing request (CSR):
  - `openssl req -new -sha256 -key my.key -out my.csr`

* List CSR contents:
  - `openssl req -in mycsr.csr -noout -text`

* Display key details:
  - `openssl rsa -in private.key -text -noout`
