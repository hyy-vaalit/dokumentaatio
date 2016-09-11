# Identiteetintarjoajan määrittäminen

Voting API:iin esikonfiguroidaan halutun identiteetintarjoajan (IdP) tiedot.
Tällä tavoin ainoastaan halutun IdP:n käyttäjätunnuksilla pääsee kirjautumaan
sisään.

Jäljempänä esitetyt ympäristömuuttujat asetetaan voting-apin tiedostoon `.env`.
Katso [.env.example](https://github.com/hyy-vaalit/voting-api/blob/master/.env.example)

Huom! Haka test käyttää eri Name Identifier Format -asetusta kuin
`login.helsinki.fi`. Muuttuja SAML_NAME_IDENTIFIER_FORMAT on:
* Haka test: "urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"
* login.helsinki.fi: "urn:oasis:names:tc:SAML:2.0:nameid-format:transient"


## Tietojen haku metadatasta

1) Etsi tagi `<EntityDescriptor>` , jossa kohteena on haluttu IdP.

2) Tämän EntityDescriptorin sisältä hae tagit:
  - `<SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" ..>`
  - `<ds:X509Certificate>`


### Esimerkiksi testiympäristössä:

Identiteetintarjoajan Entity ID:

`<EntityDescriptor xmlns:ds="http://www.w3.org/2000/09/xmldsig#" [..] entityID="https://testidp.funet.fi/idp/shibboleth">`

=> `SAML_IDP_ENTITY_ID="https://testidp.funet.fi/idp/shibboleth"`

Sisäänkirjautumissivun osoite:

`<SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://testidp.funet.fi/idp/profile/SAML2/Redirect/SSO"/>`

=> `SAML_IDP_SSO_TARGET_URL`="https://testidp.funet.fi/idp/profile/SAML2/Redirect/SSO"`


Identiteetintarjoajan sertifikaatti:

```
<ds:X509Certificate>
.. BASE64 Encoded Data ..
</ds:X509Certificate>
```

Talleta data esim. tiedostoon `certs/haka-test-idp.crt`:
- Lisää alkuun `-----BEGIN CERTIFICATE-----`
- Lisää loppuun `-----END CERTIFICATE-----`
- Tarkista, että jokainen rivi (paitsi viimeinen) on saman pituinen.

Lue sertifikaatti ympäristömuuttujaan:

=> `SAML_IDP_CERT="$(cat cert/haka-test-idp.crt)"`

Voit kokeilla miten sertifikaatti näkyy Rubylle `rails console`:
```ruby
# vaihtoehtoisesti: cert = File.read("cert/haka-test-idp.crt")
cert = ENV.fetch 'SAML_IDP_CERT'
puts cert
OpenSSL::X509::Certificate.new cert
```

Ks myös hakemisto `doc/example_certificates`


### Esimerkiksi helsinki.fi:

IdP Entity ID:
`<EntityDescriptor [..] entityID="https://login.helsinki.fi/shibboleth">`

=> `SAML_IDP_ENTITY_ID="https://login.helsinki.fi/shibboleth"`

Sisäänkirjautumissivun osoite:

`<SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.helsinki.fi/idp/profile/SAML2/Redirect/SSO"/>`

=> `SAML_IDP_SSO_TARGET_URL="https://login.helsinki.fi/idp/profile/SAML2/Redirect/SSO"`


IdP Certificate, kuten Haka Test:
=> `SAML_IDP_CERT="$(cat cert/hy-idp.crt)"`
