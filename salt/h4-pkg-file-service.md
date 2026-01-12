# H4 - Pkg-file-service

## Tiivistelmä

Tiivistelmä artikkelista, joka listataan Palvelinten hallinta kurssin kotitehtävät osiossa h4 (Karvinen 2025).

### Pkg-File-Service – Control Daemons with Salt – Change SSH Server Port

- Ensin asennetaan OpenSSH server. (Pkg)
- Sitten kopioidaan asetustiedosto masterilta. (File)
- Lopuksi käynnistetään palvelu ja käynnistetään se uudelleen, jos asetustiedostoon tulee muutoksia. (Service)
- Service watch tarkkailee tiedostoa muutosten varalta.

(Karvinen 2018.)

## Tehtävät

### Ympäristö

Host - Lenovo ThinkPad L14  
OS: Windows 11 25H2  
CPU: AMD Ryzen 5 Pro 4650U  
RAM: 16 GB DDR4  
SSD: 256 GB NVMe  
Virtualisointiohjelma: Oracle VirtualBox  
Vagrant virtuaalikoneiden OS: Debian 12

### a) SSHouto

#### Ympäristön testaus

Aluksi käynnistin edellisessä tehtävässä luodut Debian 12 virtuaalikoneet komennolla "vagrant up".
Koneiden käynnistyttyä testasin, että koneet saavat edelleen yhteyden toisiinsa ja master-slave arkkitehtuuri toimii oikein.
Ajoin masterilla (m001) "sudo salt '*' state.apply" komennon ja vastauksena tuli onnistuminen.
Nyt kun tiesin arkkitehtuurin toimivan oikein, pystyin aloittamaan tehtävän tekemisen.

#### Tehtävän aloittaminen

Siirryin masterilla "/srv/salt/" hakemistoon ja loin uuden "sshd" hakemiston, sekä sinne uuden "init.sls" tiedoston.
Ensimmäisenä pkg. Jotta palvelu on olemassa, täytyy se asentaa.
Kirjoitin ensimmäiseksi init.sls tiedostoon:

```
openssh-server:
  pkg.installed
```

Vagrantilla luoduissa koneissa on jo valmiina OpenSSH serveri, jotta koneisiin saa yhteyden PowerShellin kautta.
Tämä kuitenkin varmistaa, että ohjelma on olemassa, sekä tietysti asentaisi sen koneille, joilla sitä ei ole.
Tallensin muutokset ja testasin toiminnan paikallisesti "sudo salt-call --local state.apply sshd" komennolla.
Komennon suorittaminen kesti todella pitkään, ja lopuksi sain viestin "killed".

<img width="823" height="47" alt="image" src="https://github.com/user-attachments/assets/c32c116e-2a00-4578-bd9c-d6400ee7aa93" />

#### Vianetsintää ja korjausta

Hain tietoa internetistä kyseiseen viestiin, ja luettuani kernelin lokitiedostosta (/var/log/kenr.log) viimeisimmän merkinnän, oli prosessi tapettu muistin puutteen takia (Bheda 2016).

<img width="1448" height="48" alt="image" src="https://github.com/user-attachments/assets/353a3890-8798-493b-b86d-849f149b4cfd" />

Etsin seuraavaksi ohjeen, miten lisään muistia Vagrantin virtuaalikoneisiin ja löysin GitHubista ohjeen.
Poistuin virtuaalikoneista "exit" komennolla ja sammutin ne "vagrant halt" komennolla.  
Tämän jälkeen avasin vagrantfilen notepadilla ja kirjoitin seuraavat asiat osaksi konfiguraatiota:

```
config.vm.provider "virtualbox" do |v|
    v.memory = 2048
    v.cpus = 2
end
```

(Kishore 2022.)

Tämän tulisi lisätä muistin määrä kahteen gigatavuun sekä prosessoriydinten määrän kahteen.
Tallensin muutokset ja käynnistin koneet uudelleen "vagrant up" komennolla.
Yhdistin koneeseen m001 ja tarkistin muistin ja prosessoriydinten määrän ohjeesta löytyvillä komennoilla (Kishore 2022):

<img width="539" height="138" alt="image" src="https://github.com/user-attachments/assets/9d62493c-e977-4cb9-b1f1-04b2adb6d7e3" />

Konfiguraatio onnistui.

#### Tehtävä jatkuu

Nyt ajoin uudelleen paikallisen salt komennon, testatakseni sshd moduulin pkg.installed toimivuuden.
Tällä kertaa suoritus onnistui:

<img width="355" height="207" alt="image" src="https://github.com/user-attachments/assets/10d7c5fe-415e-4a2c-ae4d-b986853e9a73" />

Todettuani tämän osan toimivan, siirryin takaisin muokkaamaan "/srv/salt/sshd/init.sls" tiedostoa.
Kun ohjelma on asennettu, pitää se konfiguroida. SSH serverin konfiguraatiotiedosto sijaitsee "etc/ssh/" hakemistossa "sshd_config" nimellä.
Jotta kaikki orjat saavat samat konfiguraatiot ja hallinta on keskitettyä, saa orjat konfiguraationsa mestarilta.
Lisäsin init.sls tiedostoon seuraavat kohdat:

```
/etc/ssh/sshd_config:
  file.managed:
    - source: /srv/salt/sshd/sshd_config
```

Nyt mestarin sshd moduulin hakemistoon tallennettu "sshd_config" tiedosto kopioidaan orjien ssh hakemistoon, jolloin ne saavat oikeat konfiguraatiot.
Ennen testausta, minun täytyi ensin tietenkin kopioida mestarin oma sshd_config oikeaan hakemistoon ja muokata sitä.
Kopioin tiedoston komennolla "sudo cp /etc/ssh/sshd_config /srv/salt/sshd".

<img width="876" height="66" alt="image" src="https://github.com/user-attachments/assets/c6bc4d1e-d41e-44f1-bdc1-3e7a21471f08" />

Tämän jälkeen avasin tiedoston sudoeditillä lisätäkseni uuden portin SSHd:n kuunneltavaksi.

```
Port 7744
```

Poistin myös kommentoinnin porin 22 kohdalta, jotta portti 7744 ei ylikirjoita asetusta. Muuten Vagrant ei olisi saanut yhteyttä koneisiin.

<img width="125" height="62" alt="image" src="https://github.com/user-attachments/assets/fcce4585-426c-4c02-b1ac-3918fbcb0185" />

Seuraavaksi testasin toiminnan paikallisesti "sudo salt-call --local state.apply sshd" komennolla.
Tila suoritettiin onnistuneesti ja tiedostoon lisättiin muutokset.

<img width="343" height="339" alt="image" src="https://github.com/user-attachments/assets/52331343-85a0-4a03-926d-ca669612b705" />

Lopuksi on muokattava init.sls tiedostoon palvelun käynnissä oleminen ja uudelleenkäynnistys, kun "sshd_config" tiedostoon tulee muutoksia.
Lisäsin seuraavat rivit init.sls tiedostoon:

```
sshd:
 service.running:
   - watch:
     - file: /etc/ssh/sshd_config
```

Watch optio tarkkailee muutoksia tiedostoon "sshd_config" ja käynnistää tarvittaessa sshd daemonin uudelleen, jotta asetukset tulevat voimaan.
Testasin myös tämän toiminnan paikallisella salt komennolla.
Tila ajettiin onnistuneesti.

<img width="301" height="147" alt="image" src="https://github.com/user-attachments/assets/8ca4564a-fedf-41f4-b616-9fac6a792e7f" />

Tämä ei kuitenkaan kerro, onko lisätty portti auki. Testasin sen toiminnan Pkg-File-Service artikkelista löytyvältä kommennolla "nc" (Karvinen 2018).

<img width="568" height="49" alt="image" src="https://github.com/user-attachments/assets/91702e3c-5e92-4109-b4d6-b39e699f6498" />

Tämän perusteella portti ei ole auki. Kokeilin ensin käynnistää sshd daemonin uudelleen itse "sudo systemctl restart sshd" komennolla.
Tämä sen takia, että sshd_config tiedostoon oli jo tehty muutoksia, joten saltin ei tarvinnut käynnistää daemonia uudelleen.

<img width="521" height="92" alt="image" src="https://github.com/user-attachments/assets/f8b0ce18-0f52-47f5-b2ee-2d2ca8f5c699" />

Nyt portti on auki, joten konfiguraatiotiedosto on oikein kirjoitettu, salt vain ei käynnistänyt daemonia uudelleen, jotta asetukset olisivat tulleet käyttöön.
Tein vielä toisen testin, jossa muokkasin olemassa olevaa "/etc/ssh/sshd_config" tiedostoa, jotta komennon suorittaessani salt havaitsee muutoksia tiedostossa.
Poistin tiedostosta portin 7744, tallensin muutokset ja ajoin paikallisen salt komennon uudelleen.

<img width="645" height="480" alt="image" src="https://github.com/user-attachments/assets/22c2392f-b058-4d45-ae63-69da9e4176e8" />

<img width="389" height="432" alt="image" src="https://github.com/user-attachments/assets/24d288fa-2411-4348-bf54-3526ab86aaaf" />

Nyt tulokset näyttävät oikeilta. Ajoin vielä "nc" komennon uudelleen varmistaakseni portin olevan auki.

<img width="547" height="47" alt="image" src="https://github.com/user-attachments/assets/780e54a2-242c-4933-8783-c51f6b7cff7b" />

Nyt portti on avoinna automaattisesti, joten koko sshd tila toimii oikein.
Ajoin tilan vielä kaikille orjille (m002) komennolla "sudo salt '*' state.apply sshd".
Suoritus ei onnistunut, vaan antoi virheilmoituksia.

<img width="444" height="241" alt="image" src="https://github.com/user-attachments/assets/232d8ab5-dee5-47a4-9e8a-49e7daeabca7" />

#### Lisää vianetsintää

Salt ei siis löytänyt srv/salt/sshd hakemistosta oikeaa tiedostoa. Tajusin, että orja etsi omasta hakemistostaan tiedostoa, eikä mestarilta.
Tarkistin Pkg-file-service artikkelista mistä tiedosto haetaan, ja huomasin kirjoittaneeni polun väärin.
Artikkelissa tiedosto haetaan polusta "salt://sshd_config", jolloin viitataan mestariin.
Oma polkuni viittaa paikalliseen tiedostojärjestelmään, joten tila toimi oikein mestarilla, jolla tiedosto on, mutta ei orjalla.
Avasin init.sls tiedoston ja muokkasin polun:

```
file.managed:
   - source: salt://sshd_config
```

(Karvinen 2018.)

Kun ajoin salt komennon uudelleen, sain erilaisen virheilmoituksen:

```
Source file salt://sshd_config not found in saltenv 'base'
```

Tiedostoa ei siis löytynyt päähakemistosta. Muistin sitten, että artikkelissa ei oltu luotu erillistä hakemistoa sshd tilaa varten, vaan tiedostot olivat kaikki päähakemistossa.
Muokkasin vielä kerran polkua:

```
file.managed:
   - source: salt://sshd/sshd_config
```

Nyt polun pitäisi olla oikein, ja tilan toimia oikein.

<img width="506" height="681" alt="image" src="https://github.com/user-attachments/assets/dbe200cd-45e5-437f-86ae-862ddafef824" />

Nyt tila toimii oikein! Testasin vielä oikean portin olevan oikeasti auki "nc" komennolla:

<img width="686" height="68" alt="image" src="https://github.com/user-attachments/assets/218407bc-873b-4eaa-b2ed-40205956f134" />

Nyt kaikki toimii oikein.

# Lähdeluettelo

Bheda, J. 2016. What killed my process and why? - Let me first explain when and why OOMKiller get invoked?. Luettavissa: https://stackoverflow.com/questions/726690/what-killed-my-process-and-why/36415199#36415199 .
Luettu: 16.11.2025.  
Karvinen, T. 2025. Palvelinten hallinta. Luettavissa: https://terokarvinen.com/palvelinten-hallinta/#h4-pkg-file-service . Luettu: 16.11.2025.  
Karvinen, T. 2018. Pkg-File-Service - Control Daemons with Salt - Change SSH Server Port. Luettavissa: https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/ .
Luettu: 16.11.2025.  
Kishore, J. 2022. Increase RAM And CPU On Vagrant Machine. Luettavissa: https://github.com/crazyuploader/TIL/blob/main/vagrant/increase-ram-and-cpu-on-vagrant-machine.md .
Luettu: 16.11.2025.
