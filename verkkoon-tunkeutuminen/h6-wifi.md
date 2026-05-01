# H6 - WiFi

Tehtävänanto kurssin Moodlesta välilehdeltä "Larin luennot", 6. WiFi (Iso-Anttila s.a.).

## a) Tutustu wifi challenge lab 2.1 harjoitus ympäristöön ja käytä tarvittaessa hyväksesi jo olemassa olevia ohjeita.

Tutustuin [Wifichallenge.com](https://lab.wifichallenge.com/challenges) ympäristöön ja tein haasteet 00 - 06 ja 08 - 09.

<img width="1643" height="770" alt="image" src="https://github.com/user-attachments/assets/de9856a5-5f97-47de-8f0c-aa079a2193a6" />

Haasteen 07 verkko ei jostain syystä toiminut oikein virtuaalikoneellani, joten en pystynyt suorittamaan kyseistä tehtävää.
Verkko näkyi käyttöjärjestelmälle, mutta kun yritin skannata liikennettä, verkko ei näkynyt `airodump-ng` työkalulle.

Jouduin käyttämään walkthroughta (r4ulcl 2023) moneen tehtävään, sillä oli hankala tietää mistä aloittaa. Yritin kuitenkin aluksi ratkaista tehtävät ilman ohjeita.
En suorittanut enempää tehtäviä aikarajoitteiden takia, sekä virtuaalikoneen satunnaisten jäätymisten takia, jolloin jouduin aloittamaan kesken olleen tehtävän teon alusta.

## b) Kirjoita raportti siitä mitä opit ja mitkä asia yllättivät sinut kun tutustuit harjoitukseen.

Opin uusia työkaluja langattoman verkkoliikenteen skannaamiseen ja analysointiin, sekä salausten purkamiseen.

`Airmon-ng` voi asettaa verkkokortin monitorointitilaan, komennolla `airmon-ng start [verkkokortin nimi]` (Aircrack-ng.org a. 2022).
Tämän jälkeen voi käyttää `airodump-ng` työkalua kaappaamaan liikennettä, esimerkiksi komennolla `airodump-ng wlan0mon -w ~/wifi/scan --manufacturer --wps --band abg`.
Ensin annetaan verkkoliitännän nimi jota kuunnellaan. Tässä tapauksessa se on wlan0mon, wlan0 verkkoliitännän nimi `airmon-ng` komennon jälkeen.
`-w` optio kertoo kirjoittamaan tiedostoon, `--manufacturer` näyttää laitteen valmistajan, `--wps` näyttää wps tiedot, jos niitä on.
`--band` kertoo mitä taajuuksia `airodump-ng` kuuntelee. (Aircrack-ng.org b. 2022.)

MDK4 on työkalu, jolla harjoituksessa 04 lähetettiin paketteja access pointille, jolla oli piilotettu SSID.
Kyseisessä tehtävässä komento oli `mdk4 wlan0mon p -t F0:9F:C2:6A:88:26 -f ~/wifi-rockyou.txt`.
`wlan0mon` kertoo käytettävän verkkoliitännän, `p` kertoo hyökkäysmoodin: "SSID Probing and Bruteforcing".
`-t` kertoo kohteen MAC-osoitteen ja `-f` kertoo, mistä tiedostosta luetaan piilotettujen SSID nimiä bruteforcea varten.
MDK4:llä voi automatisoida myös muita langattomiin verkkoihin tunkeutumiseen liittyviä asioita, kuten "Deauthentication and Disassociation", jossa työkalu lähettää deauthentication paketteja access pointille,
jolloin se pudottaa clientit verkosta. (MDK4 2023.)

`Aircrack-ng` työkalua käytettiin selvittämään salatun verkon avain.
`Aircrack-ng` voi selvittää WEP ja WPA/WPA2-PSK salauksien avaimet.
Ohjelma tarvitsee WPA salausten murtamiseksi 4-osaisen kättelyn kaapattuna liikenteenä.
Komennolla `aircrack-ng ~/wifi/scanc6-02.cap -w ~/rockyou-top100000.txt` selvitettiin tehtävän 08 verkon avain.
Ensin kerrotaan tiedosto, johon kaapattu liikenne on tallennettu. Sen jälkeen `-w` optiolla kerrotaan, missä sanakirja sijaitsee.
(Aircrack-ng.org 2019.)

`Macchanger` ohjelman avulla pystyi vaihtamaan verkkokortin MAC-osoitteen.
Ennen MAC-osoitteen vaihtoa, on verkkokortti sammutettava, komennolla `ip link set wlan2 down`.
Komennolla `macchanger -m b0:72:bf:44:b0:49 wlan2` asetettiin wlan2 verkkoliitännän MAC-osoite samaksi, kuin toinen verkossa ollut laite.
`-m` optio kertoo, mikä MAC-osoitteeksi vaihdetaan, `wlan2` on verkkoliitännän nimi, jolle MAC vaihdetaan (Kali.org 2025).

`Airdecap-ng` työkalulla voi decryptata kaapatun liikenteen joka on suojattu WEP tai WPA/WPA2 salauksella, kun verkon nimi ja avain on tiedossa.
Komennolla `airdecap-ng -e wifi-mobile -p $PASSWORD ~/wifi/scanc6-02.cap`, poistettiin kaapatun liikenteen salaus, jotta voitiin selvittää verkossa olevan web-palvelimen IP-osoite.
`-e` optiolla kerrotaan verkon SSID, `-p` kerrotaan verkon salasana, ja lopuksi kerrotaan kaapatun liikenteen tiedoston nimi, jonka salaus puretaan.
(Aircrack-ng.org 2009.)

## c) Miten suhtautumisesi WLanin turvallisuuteen muuttui sen jälkeen kun teit harjoitukset?

Pidin salattua WPA2/3 yhteyttä aiemmin suhteellisen turvallisena, mutta harjoituksia tehtyäni, ei niiden murtaminen ole välttämättä kovinkaan vaikeaa.
Helpot ja yleisesti käytetyt salasanat päätyvät rockyou-listoille ja niiden avulla bruteforce on helppoa ja suhteellisen nopeaa.
Etenkin, kun liikennettä voi kaapata ja tutkia myöhemmin, käyttäen niin paljon aikaa kuin tarvitsee.
VPN salaus julkisissa verkoissa luo kerroksen lisäsuojaa vahvemmalla salauksella, joten jatkossa muistan varmasti laittaa sen päälle myös salatuissa julkisissa verkoissa.

## Lähdeluettelo

Aircrack-ng.org a. 2022. Airmon-ng. Luettavissa: https://www.aircrack-ng.org/doku.php?id=airmon-ng. Luettu: 01.05.2026.

Aircrack-ng.org b. 2022. Airodump-ng. Luettavissa: https://www.aircrack-ng.org/doku.php?id=airodump-ng. Luettu: 01.05.2026.

Aircrack-ng.org 2019. Aircrack-ng. Luettavissa: https://www.aircrack-ng.org/doku.php?id=aircrack-ng. Luettu: 01.05.2026.

Aircrack-ng.org 2009. Airdecap-ng. Luettavissa: https://www.aircrack-ng.org/doku.php?id=airdecap-ng. Luettu: 01.05.2026.

Iso-Anttila, L. s.a. Larin luennot - 6. WiFi. Verkkoon tunkeutuminen ja tiedustelu -opintojakson materiaali Moodlessa. Haaga-Helia ammattikorkeakoulu. Luettu: 01.05.2026.

Kali.org 2025. Packages and Binaries: macchanger. Luettavissa: https://www.kali.org/tools/macchanger/. Luettu: 01.05.2026.

MDK4 2023. About MDK4. Luettavissa: https://github.com/aircrack-ng/mdk4. Luettu: 01.05.2026.

R4ulcl 2023. Walkthrough WiFiChallenge Lab v2.0. Luettavissa: https://r4ulcl.com/posts/walkthrough-wifichallenge-lab-2.0/#03-what-is-the-probe-of-78c1a7bf7246-that-follows-the-format-of-the-other-networks-in-the-range-wifi-. Luettu: 01.05.2026.
