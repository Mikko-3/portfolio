# H2 Lempiväri: Violetti

Kotitehtävän h2 raportti. Kotitehtävän tehtävänanto löytyy kurssin verkkosivulta (Karvinen 2026).

## x) Lue ja vastaa lyhyesti kysymyksiin

### Selitä tuskan pyramidin idea 1-2 virkkeellä.

Tuskan pyramidi kuvaa keinoja, joilla voi hankaloittaa hyökkääjän toimimista.
Mitä korkeammalla pyramidin keinoissa olet, sen enemmän tuskaa aiheutat hyökkääjälle.

(Bianco 2014.)

### Selitä timanttimallin (Diamond Model) idea 1-2 virkkeellä

Timanttimalli kuvaa kyberhyökkäykseen liittyviä suhteita neljältä kantilta ja sen tarkoituksena on helpottaa hyökkäysten analysointia.
Hyökkäys ei voi toteutua ilman kaikkia neljää aspektia:
- Hyökkääjää (Adversary)
- Voimavarat (Capability)
- Infrastruktuuri (Infrastructure)
- Uhri (Victim)

(Goss 2024.)

## a) Apache log

Asensin Apachen seuraamalla tehtävänannon vinkeissä listattuja komentoja.
Avasin selaimessa Apachen oletussivun, `http://localhost`.
Tämän jälkeen tarkastelin lokitiedostoa "access.log" `/var/log/apache2/` hakemistosta.

<img width="1268" height="53" alt="image" src="https://github.com/user-attachments/assets/2d14519e-baa2-41bf-b693-35a76d01de61" />

Ensimmäisenä rivillä näkyy IP-osoite, josta pyyntö lähetettiin, tässä tapauksessa `127.0.0.1` eli localhost.
Seuraavat kaksi viivaa viittaavat `%l` ja `%u` parametreihin Apachen lokeissa.
L kertoisi pyynnön lähettäjän "RFC 1413 identity":n, mutta tämä asetus on oletuksena pois käytöstä. Viiva kertoo, ettei tietoa ole saatavilla.
U kertoisi pyynnön lähettäjän käyttäjänimen, jos tiedosto olisi salasanasuojattu. Koska tiedosto on salasanasuojaamaton, arvona on viiva.
Seuraavana aika, jolloin pyyntö tehtiin. Muodossa `päivä/kuukausi/vuosi:tunnit:minuutit:sekunnit +aikavyöhyke`.
Lainausmerkeissä näkyy lähetetty pyyntö (GET) ja käytetty protokolla (HTTP 1.1).
Tämän jälkeen näkyy palvelimen lähettämä vastaus, tässä tapauksessa koodi 200 eli OK.
Vastauksen jälkeen näkyy asiakkaalle lähetetyn datan koko tavuina (3383).
Lopuksi näkyy `%Referer` ja `%User-agent` kenttä.
`%Referer` on tyhjä, sillä pyyntöä ei ole ohjattu sivustolta, jossa pyydetty data sijaitsee.
`%User-agent` kenttä kertoo selaimen itsestään antamat tiedot, tässä tapauksessa Mozilla Firefox.
(Apache 2026.)

## Lähdeluettelo

Apache 2026. Log Files. Luettavissa: https://httpd.apache.org/docs/2.4/logs.html. Luettu: 10.04.2026.

Bianco, D. 2014. The Pyramid of Pain. Luettavissa: https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html. Luettu: 10.04.2026.

Goss, A. 2014. The Diamond Model of Intrusion Analysis: Simple Intelligence-Driven Analysis. Luettavissa: https://kravensecurity.com/diamond-model-analysis/. Luettu: 10.04.2026.

Karvinen, T. 2026. Verkkoon tunkeutuminen ja tiedustelu. Luettavissa: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/#h2-lempivari-violetti. Luettu: 10.04.2026.
