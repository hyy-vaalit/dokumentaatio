# Pääsyoikeuksien antaminen SSH-avaimelle

Jotta Dual Control -periaate säilyy, järjestelmään saa luoda vain sellaisia
pääsyoikeuksia, joiden käyttäminen tapahtuu molempien ATK-vastaavien ollessa
samassa fyysiessä tilassa. Tämä varmistetaan luomalla jokaisen Dual Control
-session aikana uusi SSH-avain. SSH-avain liitetään Dual Controllissa
puhdistettuun Heroku-tunnukseen: mieluiten käytetään Owner-pääkäyttäjätunnusta
Collaborator-tunnuksen sijasta. Lopuksi SSH-avain ja Collaborator-pääsyoikeudet
poistetaan.


## SSH-avaimen luominen
```bash
ls id_dual_control*
(tarvittaessa) rm id_dual_control*
cd ~/.ssh
ssh-keygen

Generating public/private rsa key pair.
Enter file in which to save the key (/Users/pre/.ssh/id_rsa): id_dual_control
Enter passphrase (empty for no passphrase): [saa jäädä tyhjäksi]
Enter same passphrase again:
Your identification has been saved in id_dual_control.
Your public key has been saved in id_dual_control.pub.
```

Lisää avain ssh-agentin tietoon, jolloin `heroku`-komento löytää sen:
```bash
ssh-add -K id_dual_control
```

Vipu `-K` tallettaa SSH-avaimen salasanan Mac OS keychainiin, jolloin `heroku`
ei kysy salasanaa uudelleen.



## Käyttäjätunnuksen on pysyttävä Dual Controllissa

Suosituksena on lisätä SSH-avain vain Owner-pääkäyttäjätunnukselle.
Owner-käyttäjätunnus on todettu Dual Control -sessiossa puhtaaksi aiemmin, kun
[Heroku-ympäristö on luotu](environment.md).

Jos SSH-avain lisätään Collaborator-tunnukselle, Collaborator-tunnukselle on
tehtävä Dual Controllissa samat puhdistustoimenpiteet, jotka tehtiin vaalien
pääkäyttäjätunnukselle [ympäristön pystytysvaiheessa](environment.md).
Oleelliset toimenpiteet ovat salasanan vaihtaminen, SSH-avaimien poistaminen ja
tunnuksen API Keyn uudelleengenerointi, joilla varmistutaan, ettei kenelläkään
muulla ole pääsyä Collaborator-tunnukselle Dual Control -session aikana.
Vähemmän työlästä on käyttää Dual Controllissa vain Owner-pääkäyttäjätunnusta.


## SSH-avaimen lisääminen Heroku-käyttäjätunnukselle

- Mene heroku.com > [Manage Account](https://dashboard.heroku.com/account)

- Varmistu, että kohta "SSH Keys" on tyhjä.

- Lisää uusi avain painamalla "Add New SSH Key"
  - Liitä kenttään julkisen avaimen sisältö.
  - Avaimen sisällön näet komennolla `cat id_dual_control.pub`

- Kirjaudu komentoriviltä Owner-käyttäjätunnukselle:
  - `heroku login`
  - Sisäänkirjautuminen kysyy sekä salasanan että kaksivaiheisen tunnistautumisen koodin.

## Dual Control -session päätteeksi

- Mene heroku.com > [Manage Account](https://dashboard.heroku.com/account)

- Poista jokainen SSH-avain kohdasta "SSH Keys".
  - Seuraavalle Dual Control -sessiolle luodaan uusi SSH-avain.

- Paina "Regenerate API Key" kohdassa "API Key".
  - Tämä kirjaa ulos kaikki aiemmat Heroku CLI -komentorivityökalun sessiot
    (`heroku`-komento).

- Tarkista, että Access-välilehden "Collaborators"-listalla ei ole muita
  kuin Owner-pääkäyttäjätunnus.
