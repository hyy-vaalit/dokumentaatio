# AWS: Vaalien lopuksi

Vaalien päätyttyä AWS S3 -bucketit tarjoavat raakamuotoisen datan arkiston ja
vaalituloksen esitysmuodon. Bucketteja tai niihin talletettua dataa ei poisteta.
Jokaiselta AWS IAM -käyttäjältä poistetaan kuitenkin mahdollinen Access Key
ja salasana, jotta muutoksia tietoihin ei vaalien välisenä aikana synny.


## Checklist

* Poista palveluihin liitetyt AWS Access Keyt
  - Avaa AWS Console > Services > IAM > Users
  - Käyttäjälistassa on sarake "Access Keys" ja "Password".
  - Käy läpi jokainen käyttäjä, jolla on joko Access Key tai Password.
  - Avaa Käyttäjänimi > Security Credentials.
    - [ ] Poista salasana: Sign-In Credentials > Manage Password
    - [ ] Poista Access Key: Access Keys > Delete
