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



## Lähdeluettelo

Cornelius 27.1.2024. Decode 433.92 MHz weather station data. One Transistor. Luettavissa: https://www.onetransistor.eu/2022/01/decode-433mhz-ask-signal.html. Luettu: 08.05.2026.

Hubacek, M. tammikuu 2019. Universal Radio Hacker SDR Tutorial on 433 MHz radio plugs. Video. Katsottavissa: https://www.youtube.com/watch?v=sbqMqb6FVMY&t=199s. Katsottu: 08.05.2026.

Karvinen, T. 2026. Verkkoon tunkeutuminen ja tiedustelu ICI013AS3A-3003. Luettavissa: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/#tehtavanannot. Luettu: 08.05.2026.

