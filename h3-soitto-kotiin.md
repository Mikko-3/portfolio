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

### a) Vagrant asennus

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

Seuraavaksi loin Vagrantfile:n artikkelin "Two Machine Virtual Network With Debian 11 Bullseye and Vagrant" mukaan.
