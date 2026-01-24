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

(Portswigger a s.a.)

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

Asensin artikkelissa (Karvinen 2024) mainitut ohjelmistot ja latasin "teros-challenges" zip paketin.
Purin paketin unzip:lla ja avasin "010-staff-only" tehtävän. Asensin artikkelin mukaan myös python3-flask ja python3-flask-sqlalchemy paketit.
Kytkin varmuuden vuoksi verkkoyhteyden pois päältä virtuaalikoneesta ja käynnistin harjoituksen uudelleen.
Siirryin Firefoxilla osoitteeseen, jossa sovellus toimi: `http://127.0.0.1:5000`.

<img width="639" height="238" alt="image" src="https://github.com/user-attachments/assets/39fcbe3f-8f91-4025-8629-63ff5c2f8d62" />

Hakkerointi oli itselleni haastavaa.
Kokeilin ensin sivuston perustoiminnan antamalla kerrotun oman pin koodini 123.
Ymmärsin, että minun olisi tehtävä SQL injektio, mutta input field ei sallinut muita merkkejä kuin numeroita.
Tutkiskelin sivua F12 dev toolseilla, ja huomasin "form" kohdassa "input" kentän, jossa oli kohta `type="number"`.
Kokeilin jättää "type" kohdan tyhjäksi, kirjoitin tekstiä kenttään ja painoin "reveal my password" nappia.
Vastaus oli "not found", mutta nyt pystyin lähettämään kyselyssä tekstiä.
Yritin seuraavaksi saada kaikki salasanat selville lisäämällä kenttään `' OR 1=1;-- `.
Näin kaikki vastaukset olisivat aina tosia, joten kaikki salasanat palautettaisiin.
Vastauksena sain salasanan "foo", mutta en muita salasanoja. Kenttään palautetaan nähtävästi vain yksi salasana kerrallaan.

Yritykseni oli lähempänä oikeaa kuin luulinkaan, mutta "harhauduin" ajattelussani tarpeettomille sivupoluille.
Mietin ensin, onko tietokannassa toista taulua, jossa olisi käyttäjien nimet ja pin koodit, jotta voisin kokeilla administratorin pin koodia kenttään.
Koitin selvittää tietokannan sisältöä Portswigger sivuston artikkelin "What is SQL Injection?" avulla (Portswigger b s.a.).
Löysin kohdan "Examining the database - Listing the contents" jossa kerrottiin esimerkkejä tavoista selvittää tietokannan sisältö.
Kokeilin läpi eri komentoja, mutta sain vastaukseksi vain internal server erroreita.

<img width="547" height="506" alt="image" src="https://github.com/user-attachments/assets/797a6bc5-5bc1-4f8e-8dc0-ed48a3844e74" />

<img width="530" height="347" alt="image" src="https://github.com/user-attachments/assets/605968ce-e545-4db0-b2fd-4ba953789261" />

En lopulta keksinyt enää tapoja saada salasana selville, joten menin "Hack 'n' Fix" (Karvinen 2024) artikkelin vinkit osioon.
Ensimmäinen osio ei tarjonnut minulle tarpeeksi tietoa yrittää lisää, joten turhautuneena siirryin suoraan seuraavaan vinkki osioon.
Sieltä huomasin "LIMIT" funktion, jonka sytaksi oli myös avattu.
Tajusin, että sillä voisin saada taulun muita rivejä palautettua vastauksessa.
Kokeilin lisätä sen kyselyyn: `' OR 1=1 LIMIT 0,5;--`.
Ajattelin kyselyn palauttavan ensimmäisen salasanan ja neljä seuraavaa, mutta vastauksessa oli vain yksi salasana, "foo".
Kokeilin seuraavaksi samaa kyselyä muuttamalla offsettia yhdellä isommaksi: `' OR 1=1 LIMIT 1,5;--`.
Vastauksena oli oma salasanani, jonka sain laittamalla kenttään pin koodini 123.
Kasvatin offsetia taas yhdellä: `' OR 1=1 LIMIT 2,5;--` ja sain vastaukseksi adminin salasanan.

<img width="467" height="356" alt="image" src="https://github.com/user-attachments/assets/895be22b-a143-4b7f-91b8-13fbcf924985" />

**TL;DR**

- Kyseessä SQL injektio haavoittuvuus (Subverting application logic).
- Muokkaamalla selaimen developer toolseilla input kentän type:n tyhjäksi, se sallii muiden kuin numeroiden lähettämisen kyselyssä.
- Kirjoittamalla kenttään `' OR 1=1 LIMIT 2,1;--`, kysely palauttaa vastauksena admin salasanan (kolmas salasana taulussa).

## b) Fix the 010-staff-only vulnerability from source code

