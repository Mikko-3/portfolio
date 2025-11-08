# H3 - Soitto kotiin

## Tiivistelmät

Tiivistelmät artikkeleista, jotka listataan Palvelinten hallinta kurssin kotitehtävät osiossa h3 (Karvinen 2025).

### Two Machine Virtual Network With Debian 11 Bullseye and Vagrant

- Vagrant ohjelmalla voi automatisoida VirtualBox virtuaalikoneiden luonnin.
- Asennettuasi Vagrantin, luo uusi hakemisto ja sinne "Vagrantfile" (sisältö artikkelissa).
- Voit kirjautua luoduille virtuaalikoneille komennolla "vagrant ssh (koneen nimi)", exit sulkee ssh yhteyden.
- "Vagrant destroy" -komennolla voit tuhota virtuaalikoneet, ja "vagrant up" luoda uudet koneet.

(Karvinen 2021.)

### Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux

- Asenna salt-master herra koneelle ja selvitä sen IP-osoite.
- Jos käytössä on palomuuri, salli liikenne porteista 4505/tcp ja 4506/tcp.
- Asenna salt-minion orja koneelle ja lisää "/etc/salt/minion" tiedostoon herra koneen IP: master: (IP).
- Käynnistä salt-minion palvelu uudelleen (sudo systemctl restart salt-minion).
- Hyväksy avain herra koneella: "sudo salt-key -A".
- Testaa toimivuus komennoilla.

(Karvinen 2018.)

### Salt Vagrant - automatically provision one master and two slaves

Tiivistettynä kohdat "Infra as Code - Your wishes as a text file" ja "top.sls - What Slave Runs What States".

- Luo moduulin hakemisto "/srv/salt" hakemistoon.
- Luo moduulin hakemistoon "init.sls" tiedosto ja kirjoita suoritettavat tilat (esim. file.managed).
- Muista YAML syntaksi!
- Aja sudo salt '*' state.apply (moduulin nimi) testataksesi toiminnan.
- Top file (top.sls) luodaan "/srv/salt" hakemistoon.
- Kirjoita ympäristö (base), orjat (*=kaikki) ja suoritettavat moduulit. YAML syntaksi.
- Suorita ajamalla komento "sudo salt '*' state.apply".

(Karvinen 2023.)

## Tehtävät

### Ympäristö

#### Host - Lenovo ThinkPad L14

OS: Windows 11  
CPU: AMD Ryzen 5 Pro 4650U  
RAM: 16 GB DDR4  
SSD: 256 GB NVMe

#### Virtualisointi

Virtualisointiohjelma: Oracle VirtualBox  
Master ja slave koneet:  
OS: Debian 13 Trixie  
CPU: 2 ydintä  
RAM: 4 GB

### a) Hello Vagrant!

Seurasin asennuksessa github käyttäjän "kvietmeier" kirjoittamaa ohjetta Windows 11 asennukseen (kvietmeier 2024).  
Minulla on VirtualBox jo asennettuna, joten siirryin suoraan Vagrantin asennukseen. Aloitin lataamalla Vagrantin Windowsille Vagrantin verkkosivuilta https://developer.hashicorp.com/vagrant/install .
Käynnistin asennusohjelman, luin ja hyväksyin sopimusehdot ja odotin hetken asennuksen valmistumista. Kun asennus oli valmis, käynnistin tietokoneen uudelleen.  
Ennen Vagrantin konfigurointia ohjeessa kehotettiin tarkistamaan vboxmanage:n toiminta. Loin hakemiston "hallinta" Documents hakemistoon, johon tallennan kaiken Vagrantiin liittyvän.
Avasin PowerShellin ja siirryin hakemistoon (cd Documents/hallinta). Tämän jälkeen suoritin ohjeen komennon lisätäkseni hakemiston ympäristöksi väliaikaisesti.

```
PS C:\Users\Mikko\Documents\hallinta> $env:Path += ";C:\Program Files\Oracle\VirtualBox\
```

Suoritin komennon "vboxmanage list vms" listatakseni asennetut virtuaalikoneet, jotta näen toimiiko vboxmanage oikein. Komento listasi oikein kaikki asennetut virtuaalikoneet.

<img width="345" height="49" alt="image" src="https://github.com/user-attachments/assets/669efc16-5639-4e8c-935f-548ae8b936c6" />

Ajoin PowerShellissä sen jälkeen komennon "vagrant --version" testatakseni Vagrantin asentuneen oikein, näyttämällä Vagrantin version.

<img width="446" height="51" alt="image" src="https://github.com/user-attachments/assets/0b554f5c-71ca-4733-a99d-436baf21337a" />

Vagrant asentui onnistuneesti.

### b) Linux Vagrant

Loin uuden Linux virtuaalikoneen Vagrantilla, seuraamalla Vagrantin tutoriaalia (Vagrant s.a.).
Avasin PowerShellin ja suoritin komennon:

```
vagrant init hashicorp-education/ubuntu-24-04 --box-version 0.1.0
```

Tämä hakee "box"in HCP rekisteristä ja luo automaattisesti Vagrantfilen hakemistoon.
Kun ajoin komennon "vagrant up", vagrant luki vagrantfilen ja suoritti konfiguraation ja latasi boxin. Vagrant hyödyntää tätä boxia pohjana luodessaan uusia virtuaalikoneita,
luomalla siitä kopion. Näin vältytään lataamasta boxia uudelleen joka kerta kun uusi kone luodaan ja vältytään muutoksilta alkuperäiseen boxiin, kun virtuaalikoneita ajetaan ja konfiguroidaan.  
Kun Vagrant oli saanut operaation suoritettua, yhdistin virtuaalikoneeseen komennolla "vagrant ssh".

<img width="399" height="28" alt="image" src="https://github.com/user-attachments/assets/e1cbd405-cd56-4d28-8937-f75c8540d7bc" />

Ajoin virtuaalikoneella ohjeen komennon "lsb_release -a", varmistaakseni sen asentuneen oikein:

<img width="495" height="161" alt="image" src="https://github.com/user-attachments/assets/9b0c4a1e-21bf-4d2a-8dd3-7dac1607befb" />

Virtuaalikone asentui oikein.

### c) Kaksin kaunihimpi

Tässä tehtävässä seurasin artikkelia "Two Machine Virtual Network With Debian 11 Bullseye and Vagrant" (Karvinen 2021).  
Kirjauduin ensin ulos virtuaalikoneelta komennolla "logout" ja ajoin komennon "vagrant destroy" tuhotakseni koneen.
Tämän jälkeen poistin myös Vagrantfilen hakemistosta "hallinta" ja loin uuden Vagrantfilen.
Kopioin artikkelin Vagrantfilen sisällön, mutta muokkasin koneiden nimet (m001, m002) ja Debian version (bookworm64).
Sitten ajoin komennon "vagrant up". Vagrant ei kuitenkaan onnistunut suorittamaan komentoa ja antoi virheilmoituksen:

<img width="414" height="70" alt="image" src="https://github.com/user-attachments/assets/cd7e67f0-5595-45b1-a572-a590e6538b3c" />

Vagrant ei siis löytänyt vagrantfilea. Avasin hakemiston graafisella liittymällä ja tajusin, että tiedosto on .txt muodossa.
Poistin .txt päätteen perästä, ja ajoin komennon uudelleen. Tällä kertaa se toimi.

<img width="340" height="36" alt="image" src="https://github.com/user-attachments/assets/f99f1f30-bcd6-4c84-b60e-ea5c2d4c7fa9" />

Asennuksen päätyttyä yhdistin molempiin koneisiin ssh yhteydellä "vagrant ssh (nimi)", ja testasin pingauksen toiminnan.

<img width="602" height="52" alt="image" src="https://github.com/user-attachments/assets/ab38d14d-f945-40a1-85b1-0bdf7e41a148" />

<img width="597" height="47" alt="image" src="https://github.com/user-attachments/assets/fac959b2-2606-44cb-8c8e-f840d3cad7a0" />

Molemmat koneet onnistuivat pingauksessa toisiinsa. Seuraavaksi testasin vielä internet yhteyden pingaamalla Googlen nimipalvelimia:

<img width="413" height="50" alt="image" src="https://github.com/user-attachments/assets/4920c5bb-fdfe-4d09-a78c-aff6956080ce" />

<img width="408" height="51" alt="image" src="https://github.com/user-attachments/assets/cf7ca1f2-9339-43b1-b868-385be4ecbce1" />

Myös internetyhteys toimii oikein.

### d) Herra-orja verkossa

