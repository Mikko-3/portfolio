# H1 Sniff

Kotitehtävän h1 raportti. Kotitehtävän tehtävänanto löytyy kurssin verkkosivulta (Karvinen 2026).

## x) Lue ja tiivistä

### Wireshark - Getting Started

- Artikkeli kuvaa Wireshark ohjelman asennuksen ja käyttämisen aloittamisen.
- Varmista olevasi "wireshark" ryhmässä, jotta voit kaapata paketteja ilman sudo oikeuksia.
- Voit tallentaa kaappauksen tutksiaksesi sitä myöhemmin valitsemalla "save as...".
- "Statistics" optiolla voit nähdä tietoja kaappauksesta, kuten käytetyt protokollat.
- "Display filters" voit suodattaa näkyviin vain haluamasi paketit, esim. vain DNS paketit.

(Karvinen a. 2025.)

### Network Interface Names on Linux

- Artikkeli selittää verkkoliitäntöjen nimet Linuxissa.
- Liitäntä ei ole välttämättä fyysinen verkkokortti, kuten loopback liitännät.
- Liitännät nimeää "systemd".
- Ensimmäiset kaksi kirjainta nimessä viittaavat liitännän tyyppiin:
  - en = **E**ther**n**et
  - wl = **WL**AN
  - lo = **Lo**opback
- Komennolla `ip a` voit nähdä kaikki verkkoliitäntäsi.
- `ip route` komennolla voit nähdä reititystaulun

(Karvinen b. 2025.)

## a) Asenna Debian virtuaalikone

Asensin Debian 13 Trixien. Asennus meni ongelmitta.

## b) Ei voi kalastaa

Virtuaalikoneen voi ottaa pois verkosta monella tapaa.
Voin kytkeä verkkoyhteyden pois päältä Virtualboxista menemällä koneen asetuksiin ja valitsemalla "Network" kohdasta haluamani liitännän.
Klikkaamalla täppää "Virtual cable connected" voin ottaa yhteyden pois käytöstä ja laittaa sen takaisin käyttöön.
OK ottaa muutokset käyttöön.
Tämä vastaa verkkokaapelin irroittamista fyysisestä laitteesta.

<img width="946" height="456" alt="image" src="https://github.com/user-attachments/assets/83465931-78b1-4b2d-9144-f72894516e10" />

<img width="1264" height="175" alt="image" src="https://github.com/user-attachments/assets/800ce97a-94e2-4fdf-8eaa-8e91d228cfcd" />

Voin myös kytkeä yhteyden pois päältä virtuaalikoneen sisältä.
Valitsemalla yläpalkista verkkoliitännän ym. kuvan, voi verkkoyhteyden kytkeä pois käytöstä painamalla liitännän nimeä tai sen vieressä olevaa nuolta.

<img width="380" height="377" alt="image" src="https://github.com/user-attachments/assets/16574253-d857-48aa-9420-3c569fd48fcc" />

<img width="1264" height="168" alt="image" src="https://github.com/user-attachments/assets/049a088a-7477-4036-a253-331d915cbd30" />

Kolmas vaihtoehto on terminaalin kautta `ip link set` komennolla.
Komennon syntaksi: `sudo ip link set [Interface name] down`.
Vaihtamalla option `down` optioksi `up` voi yhteyden ottaa uudelleen käyttöön. (Geeks for geeks 2024.)

<img width="1192" height="282" alt="image" src="https://github.com/user-attachments/assets/8ccc2bf1-7ee7-45cb-bf95-d4374ef8578b" />

## c) Wireshark

Wireshark on saatavilla Debianin oletusvarastoista, joten kirjoitin vain komennon `sudo apt-get install wireshark`.
Asennuksen aikana ohjelma kysyi, haluanko antaa ei-sudo käyttäjille oikeuden kaapata pakettja, johon vastasin "kyllä".
Ohjelma asentui onnistuneesti ja tarkistin komennolla `groups` että minut oli lisätty "wireshark" ryhmään.
Käyttäjäni ei ollut lisättynä ryhmään, eikä Wireshark täten antanut minun kaapata liikennettä verkkoliitännästä.

<img width="1267" height="132" alt="image" src="https://github.com/user-attachments/assets/dd114dbb-9bcc-46dc-89c5-9cbcbd6fb775" />

Testasin vielä kirjautua ulos ja sisään, mutta käyttäjäni ei ollut vieläkään lisätty ryhmään.

Lisäsin käyttäjäni "wireshark" ryhmään `sudo adduser mikko wireshark` komennolla.
Kirjauduin ulos ja takaisin sisään ja tarkastin `groups` komennon uudelleen.

<img width="1260" height="56" alt="image" src="https://github.com/user-attachments/assets/2de83233-6aa5-4336-90a4-4c84b73ae0b0" />

Nyt käyttäjäni oli lisättynä ryhmään ja kokeilin avata Wiresharkin uudelleen.
Nyt kaikki liitännät olivat käytettävissä ilman sudo oikeuksia.

<img width="758" height="427" alt="image" src="https://github.com/user-attachments/assets/0bdc69eb-1c77-4c70-82a7-2e7253cd1f61" />

Testatakseni toimivuuden, pingasin virtuaalikoneeni hostia eli kannettavaani Wiresharkin kaappauksen ollessa käynnissä "enp3s0" verkkoliittymässä.

<img width="863" height="190" alt="image" src="https://github.com/user-attachments/assets/d8e94bb4-931d-4ab4-b8cf-f7698acda79a" />

## d) Oikeesti TCP/IP

Avasin selaimella sivun "yle.fi".
Suodatin Wiresharkissa näkyviin vain TLS paketit, sillä siinä näkyy kaikki TCP/IP pinon neljä kerrosta.
Avasin paketin, jossa luki tietona "Client hello".

<img width="1019" height="548" alt="image" src="https://github.com/user-attachments/assets/42a3198d-331e-43af-81c8-0e59206f7078" />

Alhaalta ylöspäin kerrokset ovat: sovelluskerros (application layer), kuljetuskerros (transport layer), verkkokerros (internet layer) ja siirtoyhteyskerros (link layer).
- Transport Layer Security eli TLS kuuluu sovelluskerrokseen, sitä käytetään laajasti salaamaan mm. HTTPS yhteydet.
- Transmission Control Protocol eli TCP kuuluu kuljetuskerrokseen ja on vastuussa tavujen toimittamisesta sovelluskerroksen ja verkkokerroksen välillä.
- Internet Protocol version 4 eli IPv4 kuuluu verkkokerrokseen, sen tehtävänä on yhdistää verkot toisiinsa antamalla verkon laitteille osoitteet, joihin paketteja lähetetään ja vastaanotetaan.
- Ethernet kuuluu siirtokehyskerrokseen, se jakaa paketit kehyksiksi, jotka lähetetään esim. verkkokaapelia pitkin verkkokortista toiseen sen MAC-osoitteen perusteella.

(Wikipedia 2026.)
