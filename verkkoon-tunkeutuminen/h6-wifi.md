# H6 - WiFi

Tehtävänanto kurssin Moodlesta välilehdeltä "Larin luennot", 6. WiFi (Iso-Anttila s.a.).

## a) Tutustu wifi challenge lab 2.1 harjoitus ympäristöön ja käytä tarvittaessa hyväksesi jo olemassa olevia ohjeita.

Tutustuin ympäristöön ja tein haasteet 00 - 06 ja 08 - 09.

<img width="1643" height="770" alt="image" src="https://github.com/user-attachments/assets/de9856a5-5f97-47de-8f0c-aa079a2193a6" />

Haasteen 07 verkko ei jostain syystä toiminut oikein virtuaalikoneellani, joten en pystynyt suorittamaan kyseistä tehtävää.
Verkko näkyi käyttöjärjestelmälle, mutta kun yritin skannata liikennettä, verkko ei näkynyt `airodump-ng` työkalulle.

Jouduin käyttämään walkthroughta (r4ulcl 2023) moneen tehtävään, sillä oli hankala tietää mistä aloittaa. Yritin kuitenkin aluksi ratkaista tehtävät ilman ohjeita.
En suorittanut enempää tehtäviä aikarajoitteiden takia, sekä virtuaalikoneen satunnaisten jäätymisten takia, jolloin jouduin aloittamaan kesken olleen tehtävän teon alusta.

## b) Kirjoita raportti siitä mitä opit ja mitkä asia yllättivät sinut kun tutustuit harjoitukseen.

Opin uusia työkaluja langattoman verkkoliikenteen skannaamiseen ja analysointiin, sekä salausten purkamiseen.
`Airmon-ng` voi asettaa verkkokortin monitorointitilaan, komennolla `airmon-ng start [verkkokortin nimi]` (aircrack-ng.org a. 2022).
Tämän jälkeen voi käyttää `airodump-ng` työkalua kaappaamaan liikennettä, esimerkiksi komennolla `airodump-ng wlan0mon -w ~/wifi/scan --manufacturer --wps --band abg`.
Ensin annetaan verkkoliitännän nimi jota kuunnellaan. Tässä tapauksessa se on wlan0mon, wlan0 verkkoliitännän nimi `airmon-ng` komennon jälkeen.
`-w` optio kertoo kirjoittamaan tiedostoon, `--manufacturer` näyttää laitteen valmistajan, `--wps` näyttää wps tiedot, jos niitä on.
`--band` kertoo mitä taajuuksia `airodump-ng` kuuntelee. (aircrack-ng.org b. 2022.)

MDK4

Aircrack-ng

## c) Miten suhtautumisesi WLanin turvallisuuteen muuttui sen jälkeen kun teit harjoitukset?
