# Äänestyksen aikana

## Äänioikeutetun lisääminen käsin

Henkilö voi ilmoittautua läsnäolevaksi opiskelijaksi kesken äänestyksen.
Ylioipston käyttäjätunnus ei välttämättä aktivoidu samana päivänä. Tällaisia
äänestäjiä voidaan lisätä manuaalisesti äänioikeutetuiksi Vaalitulospalvelun
admin-näkymästä. Manuaalisesti lisätylle äänioikeutetulle voidaan lähettää
sisäänkirjautumislinkki, jonka avulla hän pääsee sisään äänestysjärjestelmään
ilman yliopiston käyttäjätunnusta.

Edellytyksenä äänioikeutetun lisäämiselle on, että henkilö todentaa
henkilöllisyytensä ja läsnäoloilmoittautumisensa luotettavasti esimerkiksi HYYn
keskustoimistolla. Äänioikeuden lisää vaalityöntekijä Vaalitulostimen
admin-käyttöliittymästä.

Käyttöliittymä antaa lähettää sisäänkirjautumislinkin ainoastaan vaalien aikana
luoduille äänestäjille. Suoralla API-kutsulla myös muille äänestäjille voi
tarvittaessa lähettää linkin. Jälkimmäistä ei ole huomioitu käyttöliittymässä,
koska se ei ole tavoiteltu käyttötapaus. Käyttöliittymässä listataan
kaikki vaalien alkamisen jälkeen luodut äänestäjät, joten lisäykset on
mahdollista auditoida jälkikäteen.


### Checklist:

- [ ] Varmista henkilöllisyys
- [ ] Varmista jäsenmaksun suoritus
- [ ] Luo äänestäjä
- [ ] (tarvittaessa) Lähetä sisäänkirjautumislinkki
