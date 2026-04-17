# H4 - NFC ja RFID

Tehtävänanto kurssin Moodlesta välilehdeltä "Larin luennot", 4. NFC ja RFID (Iso-Anttila s.a.).

## 1. Tarkastele käytössäsi olevia RFID tuotteita, mieti miten hyvin olet suojautunut RFID urkinnalta?

Käytössäni olevia RFID tuotteita ovat puhelimeni, asuntoni avain (iLOQ) ja pankkikorttini.
En ole erityisemmin miettinyt suojautumista RFID urkinnalita, mutta puhelimessani en pidä NFC ominaisuutta päällä, jos en käytä sitä.
Tutkittuani pikaisesti pankkikortin RFID-skimmausta, se ei vaikuta olevan kovinkaan todennäköinen tapahtuma (Hill 12.1.2021).
Olettaisin myös lukon tiirikoinnin tai rikkomisen olevan helpompaa, kuin ihmisten avainten RFID-skimmaus ja kopion tekeminen.

## 2. Tutustu APDU komentojen rakenteeseen

APDU eli **A**pplication **P**rotocol **D**ata **U**nit komentojen rakenne määritellään ISO/IEC 7816-4 standardissa.
APDU komento koostuu joko käskystä tai vastauksesta, jonka laite lähettää kortille, tai päinvastoin.
Komento-vastaus pari muodostuu lähettämällä komento, prosessoimalla se ja lähettämällä vastaus.
Vastaus vastaa aina tiettyyn komentoon. (Cardlogix s.a.)

Komento vaatii headerin, joka koostuu CLA, INS, P1 ja P2 tavuista.
Seuraavaksi tulee valinnainen body, joka koostuu Lc, Data ja Le kentistä.
Sivustolla oleva "Command APDU" taulukko selittää hyvin näiden tavujen ja kenttien sisällön ja tarkoituksen.
- CLA kertoo, onko komento toimialojen välinen (inter-industry) vai patentoitu (proprietary).
- INS kertoo komennon, kuten "kirjoita dataa".
- P1 ja P2 tavut sisältävät parametreja komennolle.
- Lc kertoo tulevan komentodatan määrän.
- Data kentässä on Lc kentän tiedon mukainen määrä tietoa.
- Le kertoo vastauksen datan maksimimäärän.

(Cardlogix s.a.)

Vastauksessa on pakollinen trailer, johon kuuluu SW1 ja SW2 kentät, jotka antavat tietoa komennon suorittamisesta.
Esimerkiksi koodi 0x9000 kertoo suorituksen onnistuneen.
Ennen traileria, on vapaaehtoinen data kenttä, jossa on komennon Le kentän mukainen maksimimäärä dataa. (Cardlogix s.a.)

Artikkelissa oli linkitettynä myös lista komennoista, joka on luettavissa GitHubissa: https://gist.github.com/hemantvallabh/d24d71a933e7319727cd3daa50ad9f2c.

## 3. Tutki ja kerro minkä mielenkiintoisen RFID hakkerointi uutiset löysit

En löytänyt viimeaikaista uutista aiheesta, mutta löysin mielestäni mielenkiintoisen artikkelin (Zduńczyk 2025), jossa hakkerit olivat testanneet yrityksen kuluvalvontaa.
Artikkelin kirjoittanut hakkeri oli saanut kloonattua siivoojan kulkukortin, jonka avulla hän selvitti käytössä olevien kulkukorttien koodit,
joiden avulla hän pääsi sisään yrityksen palvelinhuoneeseen kenenkään huomaamatta.
Tämä korostaa monitoroinnin tarvetta, sillä mm. bruteforce yrityksiä ei estetty eikä niistä tullut kenellekään tietoa.
Artikkeli on luettavissa täältä: https://www.securing.pl/en/bypassing-rfid-based-access-control-systems/.

## Lähdeluettelo

Cardlogix s.a. Application Protocol Data Unit (APDU). Luettavissa: https://www.cardlogix.com/glossary/apdu-application-protocol-data-unit-smart-card/. Luettu: 17.04.2026.

Hill, S. 12.01.2021. RFID-blocking products are practically worthless. Here’s why. Digital Trends. Luettavissa: https://www.digitaltrends.com/cool-tech/are-rfid-blocking-products-worth-your-money-we-asked-an-expert/.
Luettu: 17.04.2026.

Iso-Anttila, L. s.a. Larin luennot - 4. NFC ja RFID. Verkkoon tunkeutuminen ja tiedustelu -opintojakson materiaali Moodlessa. Haaga-Helia ammattikorkeakoulu. Luettu: 17.04.2026.

Zduńczyk, J. 14.05.2025. Red Team stories – Bypassing RFID-based access control systems. Luettavissa: https://www.securing.pl/en/bypassing-rfid-based-access-control-systems/. Luettu: 17.04.2026.
