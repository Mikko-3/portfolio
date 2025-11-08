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
Virtualisointiohjelma: Oracle VirtualBox

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

<img width="408" height="51" alt="image" src="https://github.com/user-attachments/assets/cf7ca1f2-9339-43b1-b868-385be4ecbce1" />

Myös internetyhteys toimii oikein.

### d) Herra-orja verkossa

Asensin virtuaalikoneille Salt repon, sekä salt-master ja salt-minion ohjelmat.
Asennuksen vaiheet olivat samat kuin aiemmissa tehtävissä, voit halutessasi lukea ne täältä:
https://github.com/Mikko-3/portfolio/blob/main/h1-viisikko.md#b-asenna-salt.
Kone "m001" on master ja "m002" slave. Salt asentui molempiin ongelmitta.  
Asennuksen jälkeen muokkasin m002 koneella "/etc/salt/minion" tiedostoa ja lisäsin sinne m001 IP-osoitteen.

<img width="275" height="56" alt="image" src="https://github.com/user-attachments/assets/655c58f5-a75d-4335-bbe8-c134c9ca5992" />

Lopuksi käynnistin salt-minion daemonin uudelleen komennolla "sudo systemctl restart salt-minion".
Olemassa olevan bugin takia uudelleenkäynnistys epäonnistui, joten ajoin erilliset "stop" ja "start" komennot, jolloin uudelleenkäynnistys onnistui.

Master koneella ajoin komennon "sudo salt-key", nähdäkseni, oliko orja ottanut yhteyttä.
Avain löytyi ja hyväksyin sen komennolla "sudo salt-key -a m002".

<img width="532" height="133" alt="image" src="https://github.com/user-attachments/assets/1e6e78f0-e929-4423-9fa7-7116b3c28612" />

Testasin toiminnan komennolla:

```
sudo salt '*' cmd.run 'whoami'
```

Vastaus tuli perille.

<img width="554" height="94" alt="image" src="https://github.com/user-attachments/assets/c5dbf3c1-af00-46d9-8927-e784ee0434de" />

### e) Kaksi tilaa verkon yli

Loin masterilla kaksi eri moduulia "/srv/salt/" hakemistoon, nimillä "testfile" ja "testuser".
Tein molempiin hakemistoihin omat "init.sls" tiedostot:

```
testfile/init.sls
/tmp/testing-file:
  file.managed
-----------------
testuser/init.sls
test:
  user.present
```

Testasin toiminnan paikallisesti ajamalla tilat "sudo salt-call --local state.apply (nimi)" komennolla.
Molemmat toimivat oikein ja olivat idempotentteja.

<img width="299" height="104" alt="image" src="https://github.com/user-attachments/assets/23f2758d-9e8f-4970-9d4f-a67cf6cb2a72" />

Seuraavaksi loin "/srv/salt" hakemistoon "top.sls" tiedoston, johon sisällytin molemmat äsken tekemäni tilat.

<img width="191" height="132" alt="image" src="https://github.com/user-attachments/assets/1619fd68-fa83-482d-8973-f91d3c099012" />

Testasin myös tämän ensin paikallisesti "sudo salt-call --local state.apply" komennolla, kaikki toimi odotetusti.

<img width="249" height="95" alt="image" src="https://github.com/user-attachments/assets/848f1407-c1dc-448f-bdd0-f6a5d8e307aa" />

Testaamisen jälkeen ajoin komennon orjalle "sudo salt '*' state.apply" komennolla.

<img width="328" height="189" alt="image" src="https://github.com/user-attachments/assets/96d710bd-af57-4809-9817-197a5eabcec6" />

Tilat ajettiin onnistuneesti ja seuraavaksi tarkastin vielä orjakoneella, että nämä olivat oikeasti tapahtuneet.
Ensin testitiedosto:

<img width="552" height="47" alt="image" src="https://github.com/user-attachments/assets/57e70100-5d62-485d-b41d-f0ca299c39ba" />

Tiedosto löytyi oikeasta paikasta. Sitten käyttäjän tarkistus "cat /etc/passwd" komennolla:

<img width="443" height="51" alt="image" src="https://github.com/user-attachments/assets/dfa39026-e329-4667-b447-977d624f2aa5" />

Myös käyttäjä löytyi luotuna, eli tehtävä onnistui.

# Lähdeluettelo

