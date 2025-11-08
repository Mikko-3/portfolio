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

## Tehtävät


