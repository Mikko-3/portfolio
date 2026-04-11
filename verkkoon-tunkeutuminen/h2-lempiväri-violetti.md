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

## b) Nmapped

Asensin nmap työkalun `sudo apt-get install nmap` komennolla ja asennuksen valmistuttua otin virtuaalikoneen verkkoyhteyden pois käytöstä varmuuden vuoksi.
Ajoin sen jälkeen tehtävänannon vinkeissä olevan komennon `nmap -A localhost`.
Tuloksena nmap näytti kaksi skannattua porttia, portit 80 ja 631, jotka olivat auki ja piilotti muut portit, jota olivat kiinni.

<img width="678" height="105" alt="image" src="https://github.com/user-attachments/assets/cc585abf-5fbd-42b6-b84d-784773b05cc2" />

Tuloksissa näkyy ensin portin numero ja tyyppi `80/TCP`.
Tämän jälkeen portin status, tässä tapauksessa `auki/open`.
Seuraavana kerrotaan palvelu, joka kyseisessä portissa toimii, eli `http`.
Lopuksi kerrotaan palvelun versio, tässä tapauksessa `Apache httpd 2.4.66 Debian`.

## c) Skriptit

Nmap:n -A optio yhdistää käyttöjärjestelmän selvittämisen, palvelun version selvittämisen, skripti skannauksen ja tracerouten (cyberfascinate 2023).
Skriptit, jotka olivat käytössä portin 80 skannauksessa, olivat `http-server-header` ja `http-title`.
Portin 631 skannauksessa käytettiin lisäksi `http-robots.txt` skriptiä.

<img width="499" height="127" alt="image" src="https://github.com/user-attachments/assets/e1ee032c-3d44-436b-baa8-c57dc302283d" />

## d) Jäljet lokissa

Siirryin takaisin `/var/log/apache2/` hakemistoon ja avasin tiedoston `access.log`.
Tiedostossa näkyi useita merkintöjä, joissa `%User-agent` kentässä lukee `Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)`.
Komennolla `grep -i "nmap" /var/log/apache2/access.log` voi löytää lokista kaikki kohdat, joissa lukee nmap, riippumatta kirjoitusasusta.
Nmap Network Scanning kirjassa mainitaan paremmiksi keinoiksi porttiskannauksen havaitsijat, kuten PortSentry ja Scanlogd (Lyon 2009, luku 11).

Yksi lokin merkinnöistä: `127.0.0.1 - - [11/Apr/2026:13:14:55 +0300] "GET /robots.txt HTTP/1.1" 404 491 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"`.  
Kuten aiemminkin, merkinnässä näkyy IP-osoite, josta pyyntö lähetettiin ja päiväys.
GET pyynnön jälkeen näkyy, mitä palvelulta pyydettiin, eli `robots.txt` tiedostoa.
Vastauksena lähetettiin koodi `404` eli kyseistä tiedostoa ei löytynyt.
Lopuksi `%User-agent` on merkitty Mozilla/5.0 (compatible; Nmap Scripting Engine) ja linkki Nmap:n sivuille.
Etsittyäni tietoa merkinnästä, on ymmärtääkseni kyseessä header tieto, jonka Nmap lähettää palvelulle tarkastaakseen, onko työkalu estetty.
Koska tieto näkyi lokissa, ei Nmap selvästikään ollut estetty.
Lainaus Nmap.org sivustolta:

> This script sets various User-Agent headers that are used by different
> utilities and crawling libraries (for example CURL or wget). If the request is
> redirected to a page different than a (valid) browser request would be, that
> means that this utility is banned.

(Nmap.org s.a.)

## e) Wire Sharking

Avasin Wiresharkin ja valitsin adapterin `lo`.
Aloitin kaappauksen ja ajoin terminaalissa komennon `nmap -A localhost`.
Komennon suorittamisen jälkeen lopetin Wiresharkin kaappauksen.
Rajatakseni paketit vain niihin, joissa on tieto "nmap", lisäsin Display Filterin sisällöllä "nmap", `frame contains "nmap"`.

Ennen filtteröintiä näkyi paljon `SYN` paketteja portteihin, jotka olivat suljettuja ja niiden `RST` vastaukset.
Näkyvissä paketeissa on eri HTTP kyselyjä, joita nmap on lähettänyt portteihin 80 ja 631.
Kyselyjä ovat mm. `GET, POST, PROPFIND ja OPTIONS`.

<img width="1119" height="245" alt="image" src="https://github.com/user-attachments/assets/820cc1fe-11b9-468a-8fb5-400e096560f2" />

Näissä siis nmap:n eri skriptit selvittävät tietoja http palvelimelta, kuten `PROPFIND` kyselyssä.
`PROPFIND` pyytää palvelimelta tietoja sen resursseista (Webdav.org 2007), `OPTIONS` pyytää sallittuja kommunikaatiomuotoja palvelimelta (Mozilla 2025).

## f) Net grep

Asensin ngrep työkalun `sudo apt-get install ngrep` ja ajoin komennon `sudo ngrep -d lo -i nmap`.
-d optio kertoo ngrepille mitä verkkokorttia kuunnella. -i optio on sama kuin grep komennossa, eli ngrep ei välitä isoista tai pienistä kirjaimista (Die.net s.a.).
Ngrep näytti vain kohdat, joissa oli teksti "nmap".

<img width="1258" height="698" alt="image" src="https://github.com/user-attachments/assets/fe045e9f-1ecc-40fa-acb0-b71d0873fbb0" />

## g) Agentti

Etsin tiedoston, jossa on agentin oletusnimi.
Tiedosto löytyy polusta `/usr/share/nmap/nselib/http.lua`, kohdasta `USER_AGENT`. (1bluebass.com s.a.)
Avasin tiedoston `sudoedit` komennolla ja muutin tiedon Firefoxin tiedoksi, joka löytyi apachen lokitiedoista `Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0`.

<img width="1114" height="112" alt="image" src="https://github.com/user-attachments/assets/efb9bcd1-0bf9-4db8-ade1-f0fa9a0d2a6c" />

## h) Pienemmät jäljet

Suoritin uudelleen `ncap -A localhost` skannauksen ja tutkin tuloksia.
Nmap:n tulosteessa en huomannut eroa edelliseen, mutta Apachen lokitiedoissa ero näkyy uusimmissa lisäyksissä 17:02.

<img width="1262" height="176" alt="image" src="https://github.com/user-attachments/assets/446ba89f-9985-4a2c-99e7-e89c5abc6e29" />

Nyt nmap raportoi palvelimelle olevansa Firefox selain.
Sana "nmap" löytyi vielä kuvassakin näkyvästä `nmaplowercheck1775916144` tekstistä, kun hain lokitiedoista sanaa grep komennolla `grep -i "nmap" /var/log/apache2/access.log`.

## i) LoWeR ChEcK

Etsin grep komennolla `grep -ir "nmaplowercheck1775916144" /usr/share/nmap/` mistä kyseinen teksti löytyisi, mutta en saanut osumia.
Poistin sitten numerosarjan hakutermin perästä ja sain osuman tiedostosta `/usr/share/nmap/nselib/http.lua`.
Avasin tiedoston sudoeditillä ja etsin kyseisen rivin.
Poistin nimestä "nmap" ja tallensin muutokset.
Testasin `nmap -A localhost` skannausta uudelleen ja tutkin Apachen lokeja uudelleen.
Nyt en löytänyt viimeisimmästä skannauksesta "nmap" tekstiä.

<img width="1265" height="57" alt="image" src="https://github.com/user-attachments/assets/2f62e5a3-67c6-4acf-a3e8-3642d7d25f4b" />

Vielä sama grep komennolla `grep -i "nmap" /var/log/apache2/access.log`:

<img width="1267" height="125" alt="image" src="https://github.com/user-attachments/assets/2badc772-e27f-4575-82cc-1581d90e28e0" />

Tarkistin vielä ngrep komennolla `sudo ngrep -d lo -i nmap`, ettei nmap tekstiä löydy siepatuista verkkopaketeista.

<img width="409" height="76" alt="image" src="https://github.com/user-attachments/assets/74d5d3c3-e614-415a-978e-16446bc1367d" />

Ngrep ei löytänyt paketeista tekstiä "nmap".

j) FCC ID

Löysin FCC ID:n langattomasta näppäimistö/touchpad kombostani Logitech K400.

- FCC ID: `JNZYR0019`
- Linkki FCC sivulle: https://fcc.report/FCC-ID/JNZYR0019

FCC:n sivulta löytyi kuvat puretusta laitteesta, mutta toimintakaaviot eivät olleet saatavilla.
Confidentiality Request -dokumentista selviää, että Logitech pyysi FCC:tä salaamaan toimintakaaviot.
Puretun laitteen kuvista löytyi kuitenkin kuva sirusta, jonka haettuani sain selville, että se on laitteen langattoman yhteyden SoC eli järjestelmäpiiri.
Löysin piirin, tai ainakin lähestulkoon vastaavan piirin, datasheetin (DigChip.com s.a.).
Tiedostossa on silmäilyn jälkeen paljon tietoa piirin toiminnasta ja liitännöistä, jotka varmasti auttaisivat yhteyden purkamista tai manipulointia.

## Lähdeluettelo

Apache 2026. Log Files. Luettavissa: https://httpd.apache.org/docs/2.4/logs.html. Luettu: 10.04.2026.

Bianco, D. 2014. The Pyramid of Pain. Luettavissa: https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html. Luettu: 10.04.2026.

Cyberfascinate 2023. NMAP Commands Cheat Sheet and Tutorial with Examples.pdf. Luettavissa: https://github.com/cyberfascinate/HaxorHandbook/blob/main/NMAP%20Commands%20Cheat%20Sheet%20and%20Tutorial%20with%20Examples.pdf. Luettu: 11.04.2026.

Die.net s.a. ngrep(8) - Linux man page. Luettavissa: https://linux.die.net/man/8/ngrep. Luettu: 11.04.2026.

DigChip.com s.a. Nordic VLSI nRF24LE1 datasheet. Luettavissa: https://www.digchip.com/datasheets/parts/datasheet/335/NRF24LE1-pdf.php. Luettu: 11.04.2026.

Goss, A. 2014. The Diamond Model of Intrusion Analysis: Simple Intelligence-Driven Analysis. Luettavissa: https://kravensecurity.com/diamond-model-analysis/. Luettu: 10.04.2026.

Karvinen, T. 2026. Verkkoon tunkeutuminen ja tiedustelu. Luettavissa: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/#h2-lempivari-violetti. Luettu: 10.04.2026.

Lyon, G. 2009. Nmap Network Scanning. Luku 11, Detect Nmap Scans. Nmap Project. Amerikan yhdysvallat. E-kirja. Luettu: 11.04.2026.

Nmap.org s.a. Script http-useragent-tester. Luettavissa: https://nmap.org/nsedoc/scripts/http-useragent-tester.html. Luettu: 11.04.2026.
