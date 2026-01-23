# h2 Break & Unbreak

Kotitehtävän h2 raportti. Kotitehtävän tehtävänanto löytyy kurssin verkkosivulta (Karvinen 2026).

## x) Tiivistelmät artikkeleista

### OWASP Top 10 - A01 Broken Access Control

- Rikkinäinen pääsynvalvonta on yksi yleisimmistä haavoittuvuuksista.
- Pääsynhallinnan haavoittuvuuksia on mm:
    - Vähimmän käyttöoikeuden periaatteen noudattamattomuus
    - Käyttöoikeuksien varmistamisen ohittaminen esim. URL tai HTML muokkaamalla
    - Toisen tilin tarkastelu tai muokkaaminen, antamalla sen uniikin tunnuksen
- Tapoja, joilla haavoittuvuuksia voi korjata:
    - Käyttöoikeuden estäminen oletuksena kaikkeen, paitsi julkisiin resursseihin
    - Pääsynhallinnan toteuttaminen kerran ja sen uudelleenkäyttäminen läpi sovelluksen
    - Tapahtumalokien kerääminen ja monitorointi
- Kehittäjien ja laadunvalvonnan pitäisi testata pääsyhallintaa

(OWASP Top 10 Team 2025.)

###  Find Hidden Web Directories - Fuzz URLs with ffuf

- Fuff on työkalu, jolla voi automatisoida piilotettujen sivujen etsimistä webpalvelimilta (ja muitakin asioita).
- Fuff:n voi asentaa apt-get komennolla Debianin oletuskirjastosta
- Fuff tarvitsee sanakirjan, josta se testaa eri hakemistojen nimiä.
- Tiedoston voisi kirjoittaa itsekin, mutta valmiita sanakirjoja löytyy internetistä (Seclists by Daniel Miessler käytetään artikkelissa).
- `./fuff` näyttää kaikki ohjelman parametrit, `./fuff |& less` näyttää ne paremmin luettavassa muodossa.
- `-w common.txt` kertoo käytettävän sanakirjan, `-u http://...` kertoo kohdeosoitteen.
- Filttereillä voi seuloa Fuff:n ulosantia, filtterit näkee `./fuff |& less` komennolla kohdasta "FILTER OPTIONS".

### Access control vulnerabilities and privilege escalation



### Raportin kirjoittaminen

