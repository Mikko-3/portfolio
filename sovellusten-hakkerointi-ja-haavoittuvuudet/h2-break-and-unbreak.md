# h2 Break & Unbreak

Kotitehtävän h2 raportti. Kotitehtävän tehtävänanto löytyy kurssin verkkosivulta (Karvinen 2026).

## x) Tiivistelmät artikkeleista

### OWASP Top 10 - A01 Broken Access Control

- Rikkinäinen pääsynvalvonta on yksi yleisimmistä haavoittuvuuksista.
- Pääsynhallinnan haavoittuvuuksia on mm.:
    - Vähimmän käyttöoikeuden periaatteen noudattamattomuus
    - Käyttöoikeuksien varmistamisen ohittaminen esim. URL tai HTML muokkaamalla
    - Toisen tilin tarkastelu tai muokkaaminen, antamalla sen uniikin tunnuksen
- Tapoja, joilla haavoittuvuuksia voi korjata:
    - Käyttöoikeuden estäminen oletuksena kaikkeen, paitsi julkisiin resursseihin
    - Pääsynhallinnan toteuttaminen kerran ja sen uudelleenkäyttäminen läpi sovelluksen
    - Tapahtumalokien kerääminen ja monitorointi
- Kehittäjien ja laadunvalvonnan pitäisi testata pääsyhallintaa

(OWASP Top 10 Team s.a.)

###  Find Hidden Web Directories - Fuzz URLs with ffuf

- Ffuf on työkalu, jolla voi automatisoida piilotettujen sivujen etsimistä webpalvelimilta (ja muitakin asioita).
- Ffuf:n voi asentaa apt-get komennolla Debianin oletuskirjastosta.
- Ffuf tarvitsee sanakirjan, josta se testaa eri hakemistojen nimiä.
- Tiedoston voisi kirjoittaa itsekin, mutta valmiita sanakirjoja löytyy internetistä (Seclists by Daniel Miessler käytetään artikkelissa).
- `./ffuf` näyttää kaikki ohjelman parametrit, `./ffuf |& less` näyttää ne paremmin luettavassa muodossa.
- `-w common.txt` kertoo käytettävän sanakirjan, `-u http://...` kertoo kohdeosoitteen.
- Filttereillä voi seuloa Ffuf:n ulosantia, filtterit näkee `./ffuf |& less` komennolla kohdasta "FILTER OPTIONS".

(Karvinen 2023.)

### Access control vulnerabilities and privilege escalation

- Pääsynhallinta on riippuvainen tunnistautumisesta (käyttäjä on kuka hän väittää olevansa) ja istunnon hallinnasta (seuraavat http pyynnöt ovat samalta käyttäjältä).
- Erilaisia pääsynhallintoja on vertikaalinen, horisontaalinen ja kontekstipohjainen.
- Vertikaalisessa estetään ja sallitaan pääsy tiettyihin ominaisuuksiin.
- Horisontaalisessa käyttäjillä on pääsy samantyyppisiin resursseihin (esim. pankkitili), mutta lupa vain oman tilinsä resursseihin (tilitapahtumat ja tilisiirrot vain omalta tililtään).
- Kontekstipohjaisessa estetään tai sallitaan pääsy sovelluksen tilasta riippuen. Tarkoitus estää käyttäjää tekemästä asioita väärässä järjestyksessä.
- Haavoittuvuudet johtuvat usein mm.:
    - suojaamattomista ominaisuuksista (admin sivulle ei pääsynhallintaa tms.)
    - parametripohjaisesta pääsynhallinnasta (oikeudet määritellään kirjautuessa ja tallennetaan esim. keksiin, jonka sisältö määrittää, onko käyttäjällä lupaa johonkin. Tätä sisältöä voi muokata ja siten päästä käsiksi resursseihin ilman lupaa.)
    - Alustan virheellinen konfigurointi (esim. tiettyjen URL ja HTTP ominaisuuksien estäminen käyttäjien roolin mukaan. Jos alusta tukee ei-standardeja HTTP kutsuja, voidaan nämä estot kiertää.)
- Haavoittuvuuksia voi välttää mm.:
    - Älä luota vain monimutkaistamiseen
    - Jos resurssi ei ole julkinen, kiellä pääsy oletusarvoisesti
    - Auditoi ja testaa pääsynhallinta kattavasti varmistaaksesi oikeanlainen toiminta

(Portswigger s.a.)

### Raportin kirjoittaminen

- Kerro tarkkaan mitä teit ja mitä tapahtui, on parasta kirjoittaa samalla kun tekee.
- Raportin tulee olla toistettava, täsmällinen ja helppolukuinen.
    - Toisen henkilön olisi saatava sama tulos raporttia seuraamalla.
    - Mitä komentoja annoit, onnistuiko, mitä tapahtui sen jälkeen, millä testillä totesit tämän, mahd. virheilmoitukset.
    - Kirjoita imperfektissä, käytä väliotsikoita, kirjoita huolellista kieltä ja kanavaan sopivasti.
- Viittaa lähteisiin, älä sepitä tai plagioi. Merkitse lähteet selkeästi.

(Karvinen 2006.)

## a) Break into 010-staff-only

### Ympäristö

Virtuaalikone:

- OS: Debian 13 Trixie
- Selain: Firefox 140.7.0esr (64-bit)
- 2 ydintä, 4 GB muistia
- NAT verkko, tehtävän ajaksi kytketty pois päältä

### Hakkerointi

Asensin artikkelissa mainitut ohjelmistot ja latasin "teros-challenges" zip paketin.
Purin paketin unzip:lla ja avasin "010-staff-only" tehtävän. Asensin artikkelin mukaan myös python3-flask ja python3-flask-sqlalchemy paketit.
Kytkin varmuuden vuoksi verkkoyhteyden pois päältä virtuaalikoneesta ja käynnistin harjoituksen uudelleen.
Siirryin Firefoxilla osoitteeseen, jossa sovellus toimi: `http://127.0.0.1:5000`.

<img width="639" height="238" alt="image" src="https://github.com/user-attachments/assets/39fcbe3f-8f91-4025-8629-63ff5c2f8d62" />

