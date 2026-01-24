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
Koitin selvittää tietokannan sisältöä Portswigger sivuston artikkelin "What is SQL Injection?" avulla.
Löysin kohdan "Examining the database - Listing the contents" (Portswigger b s.a.), jossa kerrottiin esimerkkejä tavoista selvittää tietokannan sisältö.
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

Avasin sovelluksen lähdekoodin `staff-only.py` ja tutkin koodia.
Aikani pähkäiltyäni koodin toiminnan logiikkaa, aloin miettimään, miten estäisin muiden kuin numeroiden lisäämisen syötteeseen.
Huomasin koodissa kohdan:
```
def hello():
        if "pin" in request.form:
                pin = str(request.form['pin'])
        else:
                pin = "0"
```
Tässä funktiossa verkkosivun syöte talletetaan muuttujaan "pin".
Syöte otetaan "str" muodossa, eli merkkijonona. Tämä sallii kaikki mahdolliset merkit, jos niitä on syötteeseen laitettu.
Muutin koodista kyseisen kohdan muotoon: `pin = int(request.form['pin'])`.
Ajattelin tämän tuottavan virheen, jos syötteessä on jotain muuta, kuin kokonaislukuja.
Tämä estäisi injektoimasta koodia syötteeseen, kuten omani edellisessa osassa (`' OR 1=1 LIMIT 2,5;--`).
Testasin toiminnan käynnistämällä ohjelman ja laittamalla syötteeseen ensin pin koodin 123, tarkistaakseni tarkoituksen mukaisen toiminnan.
Tuloksena oli internal server error, joten korjaus ei toiminut.

Seuraavaksi aloin tutkia internetistä ratkaisuja syötteen muuntamiseen kokonaisluvuksi.
Löysin lopulta Stackoverflow vastauksen, jossa kerrottiin miten syöte voidaan muuttaa kokonaisluvuksi: `request.form.get` `type=int` argumentilla (Stackoverflow 2019).
Tämä muuntaa syötteen kokonaisluvuksi, mutta jos se ei ole mahdollista, palauttaa arvon `None`.
Tämän avulla ohjelma ei kohtaa sisäistä virhettä, jos kenttään syöttää muuta kuin numeroita, vaan palauttaa tekstin "not found".
Kyseinen korjaus ei vielä toiminut kun testasin sitä, sillä en huomannut ohjelman lokissa virhettä, jossa se ei voinut liittää merkkijonoon kokonaislukua.

<img width="597" height="101" alt="image" src="https://github.com/user-attachments/assets/6831c39f-ba56-4f39-9d99-dc98fc37ff84" />

Huomattuani virheilmoituksen, korjasin ongelman muuntamalla "pin" muuttujan takaisin kokonaisluvusta merkkijonoksi:

```
def hello():
        if "pin" in request.form:
                pin = request.form.get('pin', type=int)
        else:
                pin = "0"

        pin = str(pin)
        sql = "SELECT password FROM pins WHERE pin='"+pin+"';"
        row = ""
```

Tämän jälkeen sovellus toimi normaalisti antamalla pin koodin "123".
Kun kokeilin SQL injektiota, palautti ohjelma tekstin "(not found)".

<img width="442" height="320" alt="image" src="https://github.com/user-attachments/assets/1885c1bb-3678-4b1a-959d-2f6b41815f61" />

<img width="286" height="485" alt="image" src="https://github.com/user-attachments/assets/13862142-3c92-4679-b2aa-7da982a8724c" />

<img width="381" height="625" alt="image" src="https://github.com/user-attachments/assets/287af688-f369-4283-861d-e8674fec5456" />

Korjaus on varmasti luokkaa teippi ja kengännauha, mutta tällaista saa tällä rahalla.

**TL;DR**

- Virhe hello() funktiossa, jossa 'pin' muuttujaan tallennettava syöte sallii muitakin merkkejä, kuin numeroita.
- Korjattu muuntamalla syöte kokonaisluvuksi, jos ei onnistu, palauta `None`.
- Muutetaan 'pin' takaisin merkkijonoksi, jotta se voidaan liittää SQL kyselyyn.

## c) Solve dirfuzt-1

Asensin Ffuf:n artikkelin ohjeiden mukaan, sekä latasin sanakirjan. Tämän jälkeen latasin dirfuzt-1 binäärin wget toiminnolla.
Otin virtuaalikoneen verkon pois käytöstä, ja suoritin binäärin. Siirryin osoitteeseen selaimessa, tarkistaakseni sivuston toiminnan.
Sivusto toimi, joten seuraavaksi suoritin fuzzauksen ffuf:lla komennolla: `ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ`.
Ffuf palautti tuhottoman paljon 200 vastauksia, joissa kaikissa oli samaa niiden koko, 154 tavua.
Ajoin saman komennon uudelleen filtterillä `-fs 154`, jolloin kaikki tuon kokoiset sivut filtteröidään pois.
Jäljelle jäivät nämä sivut:

<img width="510" height="224" alt="image" src="https://github.com/user-attachments/assets/83ed3683-8132-4e40-8d06-2ac51947ae26" />

Tavoitteena oli löytää admin sivu ja versionhallintaan liittyvä sivu.
Siirryin selaimella ensin ".git" osoitteeseen, sillä ajattelin sen olevan versionhallinnan pääsivu.

<img width="472" height="201" alt="image" src="https://github.com/user-attachments/assets/c8c11f77-c09a-42c9-9fec-708c18d21fc2" />

Onnistuneen kokeilun jälkeen, siirryin "wp-admin" osoitteeseen, sillä se vaikutta aika selvästi admin sivulta.

<img width="472" height="201" alt="image" src="https://github.com/user-attachments/assets/3a828e83-aeb0-466a-9ea3-b995a18a30dc" />

Tämäkin arvaus osoittautui oikeaksi.

## d) Break into 020-your-eyes-only

Ympäristö tähän tehtävään pysyi samana kuin edellisessä tehtävässä.

### Hakkerointi

Asennettuani artikkelia (Karvinen 2024) seuraten virtualenv:n ja djangon, käynnistin ohjeiden mukaisesti "manage.py" ohjelman.
Testasin pääsyn sivulle osoitteesta `http://127.0.0.1:8000`.

<img width="353" height="241" alt="image" src="https://github.com/user-attachments/assets/f22f1e3b-8981-4d90-901b-b31e9971878b" />

Päästyäni sivulle, avasin uuden terminaalin ja kokeilin ffuf:ia osoitteeseen.
`ffuf -w common.txt -u http://127.0.0.1:8000/FUZZ`
Komento palautti vain yhden osuman, "admin-console".

<img width="501" height="47" alt="image" src="https://github.com/user-attachments/assets/06e7303b-982a-4198-8c54-23137376af48" />

Siirryin osoitteeseen selaimella. Sivusto kuitenkin uudelleenohjasi minut kirjautumissivulle.

<img width="350" height="136" alt="image" src="https://github.com/user-attachments/assets/f91d157f-9d30-4ee9-aa15-225bc94c8170" />



## Lähdeluettelo

Karvinen, T. 2006.  Raportin kirjoittaminen. Luettavissa: https://terokarvinen.com/2006/raportin-kirjoittaminen-4/. Luettu: 23.01.2026. 
Karvinen, T. 2023. Find Hidden Web Directories - Fuzz URLs with ffuf. Luettavissa: https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/. Luettu: 23.01.2026. 
Karvinen, T. 2024.
OWASP Top 10 Team s.a. A01:2021 – Broken Access Control. Luettavissa: https://owasp.org/Top10/2021/A01_2021-Broken_Access_Control/index.html. Luettu: 23.01.2026. 
Portswigger a s.a. Access control vulnerabilities and privilege escalation. Luettavissa: https://portswigger.net/web-security/access-control. Luettu: 23.01.2026. 
Portswigger b s.a. Examining the database in SQL injection attacks. Luettavissa: https://portswigger.net/web-security/sql-injection/examining-the-database. Luettu: 24.01.2026. 
Stackoverflow 2019. Cast Flask form value to int. Luettavissa: https://stackoverflow.com/a/12551565. Luettu: 24.01.2026. 
