# H7 Aaltoja harjaamassa

Kotitehtävän h7 raportti. Kotitehtävän tehtävänanto löytyy kurssin verkkosivulta (Karvinen 2026).

## x) Lue ja tiivistä

### Universal Radio Hacker SDR Tutorial on 433 MHz radio plugs (video)

- Ensin tarkistetaan, että taajuus on oikein Spectrum Analyzerissa. Start käynnistää analysoinnin.
- Taajuuden valitseminen suoraan keskeltä ei jostain syystä kaappaa signaalia oikein, joten valitse signaali keskikohdan vierestä.
- Taajuus kopioituu valikkoon, jolloin sinun ei tarvitse kirjoittaa sitä käsin.
- Seuraavaksi tallenna tutkittava signaali, valitsemalla "Record Signal" valikosta ja valitsemalla Start.
- Voit zoomata kaappaamaasi signaaliin ja tutkia taajuutta tarkemmin, jotta se tallentui oikein.
- Tallenna kaappaus Save painikkeella.
- Kaappaus avautuu pääohjelmassa, ja voit aloittaa sen tutkimisen.
- Valitse oikea modulaatio ja autodetect parameters.
- Tarkista oikea bit length valitsemalla pienin osa signaalista ja vertaamalla maalatun alueen pituutta valikon bit lengthiin.
- Maalatun alueen pituus näkyy spektrin alapuolella mikrosekunneissa (μs). Pituuden tulisi olla lähellä valittua bit lengthiä.
- Tarkista seuraavaksi, että bitit ovat oikein. Maalaa yksi biteistä ja katso, että signaali vastaa bittiä oikein.
- Lopuksi valitse "Demodulation" Signal view valikosta ja vaihda "Show signal as" hexamuotoon. Klikkaamalla hexaa näet sen valitsevan vastaavan osan signaalista, jolloin demodulaatio toimii oikein.

(Hubacek 2019, 3:19 - 7:40.)

### Decode 433.92 MHz weather station data

- Ensin tutkitaan signaalia rtl_433 ohjelmalla, joka tunnisti laitteen automaattisesti.
- On olemassa tietokanta, joka sisältää kaikki tunnetut signaalit linkitettynä rtl_433 lähdekoodiin.
- Näin voi saada lisätietoa signaalista, kuten modulaation ja tavujen merkityksen.
- Avaa Spectrum Analyzer ja odota signaalia.
- Luo uusi projekti ja täytä Spectrum Analyzerista saadut tiedot.
- Tallenna signaali Record Signal toiminnolla File valikosta.
- Aseta taajuudeksi 20-100 kHz eri kuin signaali, jota laite lähettää. On tärkeää saada tallennettua kaksi lähetystä, tallenna.
- Signaalin amplitudin tulisi olla vähintään kaksinkertainen meluun verrattuna.
- Siirrä tallenne Interpretation välilehteen ja tutki, kuinka usein laite lähettää signaalia (kahden signaalin välinen aika).
- Tämän jälkeen voit valita yhden lähetyksen ja luoda siitä uuden signaalin hiiren oikealla painikkeella ja valitsemalla "Create signal from selection".
- Tunnista modulaatio carrier signaalista, tässä tapauksessa signaalin voimakkuus vaihtelee, joten kyseessä on amplitudi modulaatio.
- Valitse modulaatioksi ASK eli amplitude shift keying ja autodetect parameters. Koska signaalilla on vain on ja off tila, on se toiselta nimeltään on-off keying eli OOK.
- Signaali on päällä 500 µs ja sitten pois päältä 1000, 2000 tai 4000 µs tai kunnes seuraava lähetys alkaa.
- Tämä on PDM eli pulse distance modulation. Siinä signaalin off ajat merkitsevät bittejä.
- Seuraavaksi on selvitettävä suurin yhteinen tekijä, joka on Samples/symbols asetukseen laitettava luku.
- Tässä tapauksessa luku on 500, sillä carrier burst kestää 500 µs eli yksi symboli ja bitit 0 ja 1 kestävät 1ms (1000 µs) ja 2ms (2000 µs).
- Useimmissa PDM skeemoissa carrier burst:n aika vastaa yhtä symbolia, ja sitä seuraavat tauot kertoimia siitä.
- Jos lähetyksessä on taukoja, vaihda pause treshold sopivaksi. Tässä tapauksessa luku on 8, sillä tauko on 4000 µs eli kahdeksan symbolia.
- Siirry Analysis välilehteen ja valitse valikosta Edit - decoding, luodaksesi uusi decodaus skeema.
- Tässä tapauksessa valitaan ensin Invert, koska nollat sisältävät tiedon ja sitten Morse code, sillä signaali lähetetään lyhyinä ja pitkinä lähetyksinä ykkösiä ja nolla paddinginä.
- Tallenna skeema ja lisää se tallentamaasi signaaliin "Decoding" valikosta.

(Cornelius 27.1.2024.)

## a) Lähteet ja läppä.

Tarkistin kotitehtäväni, ja niissä pitäisi olla kaikki tarpeelliset lähdemerkinnät Haaga-Helian LibGuidesin mukaisesti tehtynä.
Tarkistin myös virtuaalikoneeni olevan valmis CTF varten.

## b) rtl_433

Ajoin ensin `sudo apt update` ja `sudo apt upgrade -y` ja käynnistin virtuaalikoneen uudelleen Kernel päivityksen takia.
Päivityksessä oli Kernel versio 6.12.86+deb13-amd64, joka paikkasi Copy Fail haavoittuvuuden.
Tämän jälkeen ajoin komennon `sudo apt search rtl 433` tarkistaakseni paketin oikean nimen ja sitten asensin ohjelman komennolla `sudo apt-get install rtl-433 -y`.
Kokeilin ajaa ohjelman komennolla `rtl_433` ja sain tulokseksi versionumeron ja muita tietoja.

<img width="929" height="80" alt="image" src="https://github.com/user-attachments/assets/0271ee67-32c4-4472-ac37-935c16d0cb1c" />

Ohjelma oli asentunut onnistuneesti.

## c) Automaattinen analyysi

Loin uuden hakemiston tiedostoille `mkdir rtl-433` ja latasin tehtäväsivulla olevan tiedoston `wget https://terokarvinen.com/2025/verkkoon-tunkeutuminen-ja-tiedustelu--ici013as3a-3001--2025p4/samples/Converted_433.92M_2000k.cs8`.
Ajoin komennon `rtl_433 -h` tutkiakseni, miten rtl ohjelmalla luetaan tiedostoa. Löysin option `-r`, joten ajoin komennon `rtl_433 -r Converted_433.92M_2000k.cs8` ja sain tiedoston auki tutkimista varten.

Aikaleimojen perusteella, ohjelmalla on kaapattu 4 signaalia. Ohjelma näyttää jokaiselle signaalille kolme eri mahdollista laitetta, joista signaali olisi voinut tulla; `Nexa-Security`, `KlikAanKlikUit-Switch` ja `Proove-Security`.
Kaikissa on sama numerosarja `8785315`, joka vastaa Nexassa ja Proovessa `House code` parametria ja Klik laitteessa `id`:tä.

<img width="1205" height="314" alt="image" src="https://github.com/user-attachments/assets/121927dd-1bc6-4c33-9f10-60cfa369384f" />

## d) Too compex 16?

Latasin uuden tiedoston `wget`:llä ja aloin tutkia, miten muunnan tiedoston oikeaan muotoon.
Luin tehtävänannon vinkeistä (Karvinen 2026), että vain tiedostomuoto muuttuu `complex16s` muotoon `cs8`. Myös tiedostonimeä pitää muuttaa oikeaan muotoon, jotta rtl-433 ymmärtää taajuuden ja sample raten oikein.
Tarkistin tiedot linkitetystä rtl-433 GitHub sivusta, jotta sain muutettua tiedoston nimen oikeaan muotoon (rtl-433 2024).
Muutin tiedoston nimen komennolla `mv Recorded-HackRF-20250411_183354-433_92MHz-2MSps-2MHz.complex16s converted-urh_433.92M_2000k.cs8`.

<img width="1269" height="105" alt="image" src="https://github.com/user-attachments/assets/05f9a995-86b5-4947-ba22-00566cb696d6" />

Avasin tiedoston analysointia varten komennolla `rtl_433 -r converted-urh_433.92M_2000k.cs8`.
Kaappaus vaikutti olevan identtinen kohdan c kaappaukseen, aikaleimat olivat samat, sekä laitteiden `id/house code`:t olivat sama `8785315`.

<img width="1189" height="357" alt="image" src="https://github.com/user-attachments/assets/62da0d52-5456-4ca0-bcb6-3c4f51f988e6" />

## e) Ultimate

Etsin hakukoneella "urh" ja avasin ohjelman GitHub sivun. Samalla tajusin ohjelman nimen olevan Universal Radio Hacker, eikä Ultimate.
Seurasin sivun Linux asennusohjeita, joissa ohjelma asennetaan `pipx` työkalun avulla. (urh 2025.)
Ensin asensin `pipx`:n, sillä se ei ollut asennettuna. Ajoin komennon `sudo apt-get install pipx` ja asennuksen jälkeen komennon `pipx ensurepath` pipx:n asennusohjeiden mukaisesti (pipx s.a.).
Avasin terminaalin uudelleen ja ajoin komennon `pipx install urh`, joka asensi ohjelman onnistuneesti.

<img width="795" height="149" alt="image" src="https://github.com/user-attachments/assets/2249081d-0e5e-45a0-ae43-f40e9696d0a8" />

<img width="737" height="113" alt="image" src="https://github.com/user-attachments/assets/9340d319-14db-4309-9baf-b094bedc8b4e" />

## f) Yleiskuva

Loin uuden hakemiston URH tiedostoille `mkdir urh-files` ja latasin sinne tiedoston tehtävänannon sivulta `wget https://terokarvinen.com/2025/verkkoon-tunkeutuminen-ja-tiedustelu--ici013as3a-3001--2025p4/samples/1-on-on-on-HackRF-20250412_113805-433_912MHz-2MSps-2MHz.complex16s`.
Avasin URH:n komennolla `urh` ja avasin tiedoston valikosta "File - open".

Painamalla sinistä "i" painiketta, sain lisää tietoja signaalista.
Näyte on 5,49s pitkä ja tiedoston nimen perusteella signaali on taajuudella 433,912MHz ja nauhoitettu 12.04.2025 kello 11.38.05.
Näytteessä on kolme signaalia, joissa on lähetetty jotain dataa. Tehtävänannon mukaan ON-painiketta on painettu kolmesti, joka vaikuttaisi olevan oikea lukumäärä.
Signaalit näyttävät samoilta keskenään, mutta kolmannessa signaalissa on eroa kahteen edelliseen verrattuna.
Kahdessa ensimmäisessä on 5 erillistä burstia, mutta kolmannessa näyttäisi olevan vain neljä.
Lähemmällä tarkastelulla, signaalissa on myös 5 burstia, mutta ne ovat lähempänä toisiaan kuin kaksi aikaisempaa.

Kolmas burst:

<img width="417" height="140" alt="image" src="https://github.com/user-attachments/assets/3bf89b2f-c528-4343-8fd3-afb4339bc063" />

Ensimmäinen:

<img width="429" height="161" alt="image" src="https://github.com/user-attachments/assets/8e178fe9-f155-419b-b8c8-8e151015d665" />

Toinen:

<img width="424" height="150" alt="image" src="https://github.com/user-attachments/assets/25eafdbc-0678-4533-8dae-2a214666c32b" />

## g) Bittistä

Valitsin ensimmäisen signaalin ja valitsin kontekstivalikosta "crop to selection".
Tämän jälkeen aloin säätämään analysoinnin asetuksia kohdalleen.
Aikaisemman artikkelin mukaan, kyseessä olisi amplitudi modulaatio (Cornelius 27.1.2024), joten oikea valinta modulaatioksi on ASK, joka oli jo valmiiksi asetettuna.
Painoin "autodetect parameters" ja asetukset eivät muuttuneet, joten oletin niiden olleen jo oikein valmiiksi.
Huomasin tiedoston nimestä, että Sample rate olisi 2MSps, joten muutin sen "i" valikosta, jossa Sps oli 1M.

<img width="838" height="246" alt="image" src="https://github.com/user-attachments/assets/b6ee7e44-c332-4f2e-9de4-3c67f6460f2e" />

Painoin "autodetect" vielä uudelleen varmuuden vuoksi, mutta asetuksiin ei näyttänyt tulevan muutosta.

Oletin saaneeni signaalin demoduloitua oikein, joten maalasin yhden bitin ikkunasta ja katsoin, kauanko se kestää.

<img width="287" height="70" alt="image" src="https://github.com/user-attachments/assets/cc2bcefc-0b98-451a-975e-20e8b0006fcd" />

Tulos oli 261 µs eli mikrosekuntia, eli 261 sekunnin miljoonasosaa.
Wikipedian esimerkkejen mukaan, silmän räpäytys kestää 350000 µs eli noin 1/3 sekuntia (Wikipedia 2026.), joten yksi bitti on noin 1341 kertaa nopeampi kuin silmän räpäytys.
`Laskukaava: 350000/261=1340,9961..`.

## Lähdeluettelo

Cornelius 27.1.2024. Decode 433.92 MHz weather station data. One Transistor. Luettavissa: https://www.onetransistor.eu/2022/01/decode-433mhz-ask-signal.html. Luettu: 09.05.2026.

Hubacek, M. tammikuu 2019. Universal Radio Hacker SDR Tutorial on 433 MHz radio plugs. Video. Katsottavissa: https://www.youtube.com/watch?v=sbqMqb6FVMY&t=199s. Katsottu: 08.05.2026.

Karvinen, T. 2026. Verkkoon tunkeutuminen ja tiedustelu ICI013AS3A-3003. Luettavissa: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/#tehtavanannot. Luettu: 09.05.2026.

Pipx s.a. Install pipx. Luettavissa: https://pipx.pypa.io/stable/how-to/install-pipx/. Luettu: 09.05.2026.

Rtl-433 2024. Introduction to I/Q formats. Luettavissa: https://github.com/merbanan/rtl_433/blob/ac1e4a8c5a36fb90e3b06c0f01cef00bb3b2614d/docs/IQ_FORMATS.md#file-name-meta-data. Luettu: 09.05.2026.

Urh 2025. Installation. Luettavissa: https://github.com/jopohl/urh#Installation. Luettu: 09.05.2026.

Wikipedia 2026. Microsecond. Luettavissa: https://en.wikipedia.org/wiki/Microsecond. Luettu: 09.05.2026.
