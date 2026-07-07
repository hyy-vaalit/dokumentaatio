# Äänestyksen aikana

## Äänioikeutetun lisääminen käsin

Henkilö voi ilmoittautua läsnäolevaksi opiskelijaksi kesken äänestyksen.
Yliopiston käyttäjätunnus ei välttämättä aktivoidu samana päivänä. Tällaisia
äänestäjiä voidaan lisätä manuaalisesti äänioikeutetuiksi Vaalitulostimen
admin-käyttöliittymästä. Manuaalisesti lisätylle äänioikeutetulle voidaan
lähettää sisäänkirjautumislinkki, jonka avulla hän pääsee sisään
äänestysjärjestelmään ilman yliopiston käyttäjätunnusta.

Vaalitulostimen käyttöliittymä on tässä vain ohut välikerros: äänestäjän
luonti, sisäänkirjautumislinkin (JWT) muodostus ja sähköpostin lähetys on
toteutettu voting-api:ssa. Vaalitulostin kutsuu voting-api:n rajapintaa
ServiceUser-tunnuksella, ja lisäys onnistuu vain vaalien ollessa käynnissä.

**Huom:** sisäänkirjautumislinkin sisältävän sähköpostin lähettää voting-api:n
worker-dyno. Jos worker ei ole käynnissä, viesti jää hiljaa jonoon.
Linkin voimassaoloaika määräytyy voting-api:n ympäristömuuttujasta
`EMAIL_LINK_JWT_EXPIRY_MINUTES`.

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
- [ ] Varmista, että voting-api:n worker-dyno on käynnissä
- [ ] Luo äänestäjä
- [ ] (tarvittaessa) Lähetä sisäänkirjautumislinkki
