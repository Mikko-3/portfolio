# H3 No Strings Attached

Kotitehtävän h3 raportti. Kotitehtävän tehtävänanto löytyy kurssin verkkosivulta (Karvinen 2026).

## Ympäristö

Virtuaalikone:

- Virtualisointialusta: Oracle VirtualBox
- OS: Debian 13 Trixie
- Selain: Firefox 140.7.0esr (64-bit)
- 2 ydintä, 4 GB muistia
- NAT verkko, tehtävän ajaksi kytketty pois päältä

## a) Strings

Latasin harjoituskansion Teron sivuilta wget avulla, ja purin paketin unzip:lla.
Siirryin `challenges/passtr` hakemistoon ja luin 'README.md' tiedoston.
Asensin siinä listatut esivaatimukset (make, gcc, micro) ja ajoin ohjelman `./passtr` komennolla.

<img width="556" height="106" alt="image" src="https://github.com/user-attachments/assets/0f41db51-1d6b-4000-b12d-3c323c910313" />

Ensimmäinen satunnainen arvaukseni ei ollut oikein.

Seuraavaksi tutkin ohjelman koodia strings:llä.
Ajoin komennon `strings passtr |less` ja rupesin etsimään salasanaan viittaavaa tekstiä.
Huomasinkin heti tekstin alkupäässä kohdan `What's the password?`, jossa ohjelma kysyi salasanaa.
Tämän jälkeen oli luultavasti if-lauseke koodissa, jossa tarkastetaan, vastaako käyttäjän syöte salasanaa.
Sen alta löytyikin selkokielisenä salasana `sala-hakkeri-321`.

<img width="403" height="176" alt="image" src="https://github.com/user-attachments/assets/8e101716-d6c3-469b-b90e-cf632cfb7c9f" />

Suljin less:n ja käynnistin ohjelman uudelleen kokeillakseni salasanaa.
Salasana oli oikea!

<img width="450" height="77" alt="image" src="https://github.com/user-attachments/assets/7cf213bb-6e2a-401a-a064-73376e0423f0" />

Salasanan yhteydessä oli myös lippu `FLAG{Tero-d75ee66af0a68663f15539ec0f46e3b1}`, joka näkyi myös stringsillä.

## b) Fix

