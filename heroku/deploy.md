## Palvelun deploy
- [ ] Tietokonetta käyttää ATK-vastaava.
- Avaa Heroku.com > Settings.
  - Hanki Git URL palvelun sivulta Settings > Info
- Lisää git remote voting-apiin:
  - `git remote add production https://git.heroku.com/hyy-vaalit.git`



## Lopuksi

- Heroku.com > Settings:
  - [ ] Maintenance Mode: Off
  - komentoriviltä: `heroku maintenance:off -a PALVELU`
