# H5 Laboratorio- ja simulaatioympäristöt hyökkäyksissä

Tehtävänanto kurssin Moodlesta välilehdeltä "Larin luennot", 5. Laboratorio- ja simulaatioympäristöt hyökkäyksissä (Iso-Anttila s.a.).

## Ympäristö

Kannettava tietokone:

- OS: Windows 11
- Prosessori: AMD Ryzen 5 Pro 4650U
- RAM: 16 GB
- Virtualisointialusta: Oracle VirtualBox
- Virtuaalikone: Moodlesta ladattu Ubuntu Server, jossa tarvittavat asiat valmiiksi asennettuna
- Virtuaalikoneen RAM: 2048 Mt
- Ydinten määrä: 2
- Verkkoasetus VirtualBoxissa: Host-Only Adapter

## a) Aja tunnilla esitetty ARP hyökkäys ja tutki, miten se toimii.

### Ympäristön käyttöönotto

Latasin virtuaalikoneen Moodlesta ja lisäsin sen VirtualBoxiin luomalla uuden virtuaalikoneen ja käyttämällä olemassa olevaa virtuaalilevyä.
Kone käynnistyi onnistuneesti ja toimi kuten pitikin.
Saadakseni ympäristön toimimaan oikein, asensin [Xming](http://www.straightrunning.com/XmingNotes/) ohjelmiston, joka tuo Windowsille X11 tuen.
Näin voin saada näkyviin eri virtuaalikoneiden näytöt käyttäen SSH:ta ja Puttya.
Puttyn asetuksissa `Connection -> SSH -> X11`, laitoin asetuksen "X11 forwarding" päälle ja yhdistin virtuaalikoneen osoitteeseen.

### Toteutus

Seurasin Moodlessa olevaa ohjetta "Mininet luento materiaali ja ohjeet" ARP hyökkäyksen toteuttamisessa.
Käynnistin verkko-ohjaimen virtuaalikoneessa komennolla `ryu-manager ryu.app.simple_switch_13`.
Siirryin sitten takaisin etäyhteyteen ja käynnistin mininetin komennolla `sudo mn --topo single,3 --mac --switch ovsk --controller remote`.
Kokeilin yhteyden toimivuutta komennolla `pingall` ja kaikki liikenne meni perille.

<img width="1315" height="182" alt="image" src="https://github.com/user-attachments/assets/16472570-fba9-4190-a8a9-b850f43d9240" />

Ajoin sitten komennon `xterm h1`, mutta näyttö ei tullut näkyviin.
Poistuin mininetistä `exit` komennolla ja ajoin sitten ohjeissa olleen neuvon mukaan `./get_xauth.sh` skriptin.
Kopioin tulosteen ja ajoin komennon `sudo -s xauth add mininet-vm/unix:10  MIT-MAGIC-COOKIE-1  f3a75b743796418c3dfa0ae9698a189b`.
Käynnistin mininetin uudelleen ja ajoin uudelleen `xterm h1` komennon, jolloin uusi ikkuna avautui h1 koneeseen.
Nyt ympäristö toimi oikein.

Ajoin komennon `arp -n` h1 koneessa tarkistaakseni ARP-taulun sisällön.
Taulussa oli kaksi osoitetta, `10.0.0.2 ja ...3` ja niiden MAC-osoitteet.

<img width="625" height="66" alt="image" src="https://github.com/user-attachments/assets/4f143015-a630-43fc-8ff9-13b8ca8f2c35" />

Avasin sitten mininetistä uuden xtermin h3 koneelle `xterm h3` ja ajoin komennon `sysctl -w net.ipv4.ip_forward=1`.
Komento käskee sysctl kirjoittamaan tiettyyn parametriin tietyn arvon.
`-w` optio käskee kirjoittamaan, `net.ipv4.ip_forward` on parametri johon kirjoitetaan ja `=1` on arvo joka kirjoitetaan.
Arvo 1 kertoo laittamaan parametrin päälle.
`net.ipv4.ip_forward` asetus kertoo Linuxin kernelille välittää IPv4 paketit eteenpäin, eli periaatteessa toimia reitittimenä.
Komento laittaa asetuksen päälle väliaikaisesti, joten kun kone käynnistetään uudelleen, on asetus taas pois päältä oletuksena. (PiMyLifeUp 2024.)

Tämän jälkeen ajoin komennon `iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 22`.
Iptables on työkalu verkkopakettien filtteröintiin.

- `-t nat` optio kertoo iptablesille mitä tablea käyttää, tässä tapauksessa nat.
- `-A PREROUTING` kertoo iptablesille lisätä säännön "PREROUTING" kaikkiin paketteihin. PREROUTING muuttaa paketteja heti kun ne saapuvat.
- `-p tcp` kertoo tehdä asetukset kaikille TCP-protokollan paketeille.
- `--dport 22` on destination port, eli portti johon paketti ohjataan vastaanottajalla.
- `-j REDIRECT` on jump, joka kertoo mitä tehdään jos paketti vastaa edellämainittuja sääntöjä. Redirect toimii nat tablessa, PREROUTING ketjussa.
Se ohjaa paketin itselleen muuttamalla paketin destination address kentän sisääntulevan verkkokortin IP:seen.
- `--to-port 22` on REDIRECT:n optio, joka kertoo mihin porttiin paketti ohjataan. Tämä toimii vain jos `-p tcp` tai `-t udp` on asetettu.

(Die.net s.a.)

Jos ymmärsin komennon toiminnan oikein, iptables siis muokkaa kaikkia porttiin 22 matkalla olevia TCP-paketteja niin, että se on itse vastaanottaja.

Ajoin tämän jälkeen komennot `tcpdump –i h3-eth0 –w arp-p.pcap & wireshark & sshesame -port 22 -listen_address 0.0.0.0` h3 koneella.
Komento käynnistää ohjelman tcpdump ja kertoo sen kuunnella verkkokorttia `h3-eth0`.
`-w arp-p.pcap` kertoo kirjoittamaan kaapatut paketit tiedostoon. (Tcpdump.org 2026.)
`&` operaattori kertoo ohjelman suoritettavan taustalla.
Tämän jälkeen suoritetaan myös wireshark ja sshesame.
Sshesame on SSH "honeypot", joka käynnistää väärennetyn SSH palvelimen, päästää kaikki yhdistävät sisään ja kirjaa niiden liikenteen lokiin (Sshesame 2024).
Optiot kertovat kuunneltavan portin ja osoitteen.

Avasin uuden xterminaalin h3 koneelle ja ajoin python skriptin `python3 ./arp_poison.py`.
Sen jälkeen siirryin takaisin h1 koneelle ja otin ssh yhteyden h2 koneeseen.
Sain ohjeen mukaisen vastauksen, jossa avaimen todennus epäonnistui, koska hostin ja osoitteen 10.0.0.2 avaimet ovat väärät.
Kuvassa näkyy myös h3 koneen lokia.

<img width="1630" height="431" alt="image" src="https://github.com/user-attachments/assets/bcc72974-c8ca-4873-9a51-f59d7d873267" />

Poistin avaimen h1 koneella komennolla `ssh-keygen -R h2`, jonka jälkeen yhdistin uudestaan h2 koneeseen SSH yhteydellä.
Koska avainta ei ole enää muistissa, kysyy ohjelma haluatko yhdistää ja tallettaa avaimen.
Vastaamalla kyllä, avain talletetaan. Sen jälkeen ohjelma varoittaa, että IP-osoitteen 10.0.0.2 avain eroaa hostin h2 avaimesta, mutta sallii yhdistämisen vastaamalla yes.
Nyt sain yhteyden muodostettua. H3 lokissa näkyy yhdistäminen.

<img width="819" height="182" alt="image" src="https://github.com/user-attachments/assets/83923a7e-b561-495b-93e8-6b1ba4440ded" />

### Analyysi

Käsitykseni mukaan h3 kone on yhdistetty verkkoon h1 ja h2 koneiden kanssa, jossa on pelkkä kytkin.
Arp_poisoning.py tiedoston koodissa kone lähettää verkkoon ARP-paketteja, joissa se kertoo oman MAC-osoitteensa olevan IP-osoitteen 10.0.0.2 eli h2 MAC-osoite.
Täten 10.0.0.1 eli h1 lähettää paketit koneelle h3.
Normaalisti kone ei ottaisi vastaan paketteja, mutta `net.ipv4.ip_forward=1` asetuksella se ottaa niitä vastaan.
Yllä ajetulla `iptables` komennolla h3 muuttaa asetuksiaan ottamaan vastaan TCP-paketteja portista 22 ja ohjaa ne itseensä.
Kun h1 yhdistää h2 koneeseen, yhdistetäänkin oikeasti h3 koneeseen, jonka SSH huomaa avainten ollessa väärät.
Kun avaimet poistetaan h1 muistista, SSH ei löydä avainta ja kysyy, haluaako käyttäjä tallentaa uuden avaimen.
Koska käyttäjä suostuu, huomaa SSH sen jälkeen, ettei avain vastaa IP-osoitteen 10.0.0.2 avainta, ja varoittaa käyttäjää mahdollisesta huijauksesta.
Käyttäjän suostuessa uudelleen yhdistämään, avain korvataan ja jatkossa vastaavia varoituksia ei ilmaannu.
Koska h3 on onnistuneesti huijannut käyttäjän luulemaan sen olevan h2, voi se purkaa SSH salauksen avaimilla ja täten näkee kaikki paketit selkokielisenä.  
En ole täysin varma, ymmärsinkö kaikki komennot ja niiden optiot sekä python koodin oikein, mutta olen aika varma olevani edes oikeilla jäljillä analyysissani.

## b) Samassa hakemistossa on myös ICMP Spoofing ja TCP Session Hijacking. Aja molemmat labrat läpi ja kerro, miten molemmat tekniikat toimivat.


## Lähdeluettelo

Die.net s.a. iptables(8) - Linux man page. Luettavissa: https://linux.die.net/man/8/iptables. Luettu: 24.04.2026.

Iso-Anttila, L. s.a. Larin luennot - 5. Laboratorio- ja simulaatioympäristöt hyökkäyksissä. Verkkoon tunkeutuminen ja tiedustelu -opintojakson materiaali Moodlessa. Haaga-Helia ammattikorkeakoulu. Luettu: 24.04.2026.

PiMyLifeUp 2024. How to Enable or Disable IP Forwarding on Linux. Luettavissa: https://pimylifeup.com/linux-ip-forwarding/. Luettu: 24.04.2026.

Sshesame 2024. sshesame. Luettavissa: https://github.com/jaksi/sshesame. Luettu: 24.04.2026.

Tcpdump.org 2026. tcpdump(1) man page. Luettavissa: https://www.tcpdump.org/manpages/tcpdump.1.html. Luettu: 24.04.2026.
