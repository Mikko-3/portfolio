# h1 Viisikko

H1 kotitehtävän vastaukset. Tehtävänannot löytyvät Palvelinten hallinta -sivustolta (Karvinen 2025a).

## Tiivistelmät

### Install Salt on Debian 13 Trixie

- Uuden repon asentamiseen tarvitaan kaksi asiaa: PGP julkinen avain ja sources.list.
- Lataa nämä tiedostot Salt virallisien ohjesivujen mukaisista paikoista.
- Kopioi tiedostot tarvittaviin paikkoihin
  - PGP avain: etc/apt/keyrings/salt-archive-keyring.pgp
  - Salt.sources: /etc/apt/sources.list.d/
- Päivitä apt listat ja asenna salt-minion tai salt-master.
- Testaa toiminta jollain paikallisella komennolla.

(Karvinen 2025b.)

Huomioita: uuden repon asentaminen vaatii paljon luottoa. Kun olet asentanut PGP avaimen, järjestelmä luottaa lähteeseen ja voit asentaa sieltä mitä tahansa ohjelmistoa.
Jos lähde ei ole luotettava, voit mahdollisesti asentaa haittaohjelmia.

### Run Salt Command Locally

- Paikallisesti voi harjoitella komentoja ja testata komentojen toimivuutta.
- Tärkeimmät Salt komennot ovat:
  - pkg (.installed, .removed)
  - file (.managed, .absent)
  - service (.running, .dead)
  - user (.present, .absent)
  - cmd (.run)
- Pkg kertoo, että ohjelma on oltava asennettuna.
- File kertoo, että tiedosto on oltava jossain.
- Service kertoo, että prosessin (daemonin) on oltava käynnissä.
- User kertoo, että käyttäjän on oltava olemassa.
- Cmd suorittaa komennon/komentoja.
- Ohjeet on saatavilla komennolla:

      sudo salt-call --local sys.state_doc

(Karvinen 2023.)

Kysymys: komentojen rakenne ei täysin auennut minulle, mitä -l optio tekee, sekä mitä info state.single tekee?

### Salt Quickstart

- Mestarilla oltava julkinen IP tiedossa, sen ei tarvitse tietää missä orjat ovat.
- Master:
    - Asenna salt-master ja tarkista IP. Jos palomuuri käytössä, salli liikenne porteista 4505/tcp ja 4506/tcp.
- Slave:
    - Asenna salt-minion ja avaa /etc/salt/minion tiedosto.
    - Lisää mestarin IP osoite master kohtaan
    - Tallenna muutokset ja käynnistä salt-minion.service uudelleen.
- Hyväksy avain/avaimet mestarilla: sudo salt-key -A
- Testaa toiminta esim. sudo salt '*' cmd.run 'whoami' komennolla.

(Karvinen 2018.)

Kysymys: Mitä salt-key avaimet ovat? PGP avaimia vai jotain muuta?

### Raportin kirjoittaminen

- Kerro tarkkaan mitä teit ja mitä tapahtui, on parasta kirjoittaa samalla kun tekee.
- Raportin tulee olla toistettava, täsmällinen ja helppolukuinen.
    - Toisen henkilön olisi saatava sama tulos raporttia seuraamalla.
    - Mitä komentoja annoit, onnistuiko, mitä tapahtui sen jälkeen, millä testillä totesit tämän, mahd. virheilmoitukset.
    - Kirjoita imperfektissä, käytä väliotsikoita, kirjoita huolellista kieltä ja kanavaan sopivasti.
- Viittaa lähteisiin, älä sepitä tai plagioi. Merkitse lähteet selkeästi.

(Karvinen 2006.)

Kysymys: käytetäänkö libguides lähteiden merkintää (https://libguides.haaga-helia.fi/lahdeviittaamisen-tueksi/tekstiviitteet-ja-lahdeluettelo), vai riittääkö vapaamuotoisempi?

## Tehtävät

### b) Asenna Salt

Asensin ensin wget työkalun komennolla sudo apt-get install wget. Wget oli kuitenkin jo asennettuna, sillä apt antoi ilmoituksen "wget is already the newest version (1.25.0-2)". Seuraavaksi tein hakemiston Salt repon tiedostoja varten: "mkdir saltrepo". Siirryin hakemistoon "cd saltrepo" komennolla ja latasin tiedostot.
Latasin tiedostot wget komennolla osoitteista: https://packages.broadcom.com/artifactory/api/security/keypair/SaltProjectKey/public ja https://github.com/saltstack/salt-install-guide/releases/latest/download/salt.sources .
Avasin tiedostot less komennolla tarkistaakseni sisällön. Kopioin samalla sijainnin, johon avain on tallennettava salt.sources tiedostosta.
Tämän jälkeen kopioin avaimen komennolla "sudo cp public /etc/apt/keyrings/salt-archive-keyring.pgp", sekä salt.sources tiedoston "sudo cp salt.sources /etc/apt/sources.list.d/".
Kopioinnin jälkeen päivitin apt listat "sudo apt-get update" komennolla ja asensin salt-minion ohjelmiston komennolla "sudo apt-get install salt-minion".
Testasin asentumisen onnistumisen komennolla "salt --version". (Karvinen 2025b.)

Komento palautti virheen "bash: salt: command not found". Tarkistin Salt install guidesta mitä tehdä, ja huomasin siellä kohdan, jossa salt pitää ottaa käyttöön ja käynnistää. Ajoin komennot "sudo systemctl enable salt-minion && sudo systemctl start salt-minion" ohjeen mukaan. (VMware s.a.)
Tämän jälkeen kokeilin uudestaan "salt --version" komentoa. Tämä palautti saman virheen kuin aikaisemmin. Sitten tajusin, että daemonin nimi on salt-minion, joten kokeilin komentoa "salt-minion --version" ja vastauksena sain saltin version: salt-minion 3007.8 (Chlorine).
Kokeilin myös paikallisen komennon "sudo salt-call --local state.single file.managed /tmp/testi". Komento suoriutui ja tarkistin tiedoston olemassaolon komennolla "ls /tmp/testi". Tiedosto löytyi, joten asennus onnistui. (Karvinen 2025b.)

### c) Viisi tärkeintä

Ajoin paikalliset komennot viidelle tärkeimmälle tilafunktiolle. Otin komennot Run Salt Command Locally -artikkelista (Karvinen 2023). Tulokset ovat kuvakaappauksina ja analysoin jokaisen tuloksen.

**Pkg:**

<img width="664" height="280" alt="Screenshot 2025-10-26 151843" src="https://github.com/user-attachments/assets/add03f74-7e66-4d07-a714-184574cdc0b1" />

<img width="543" height="383" alt="Screenshot 2025-10-26 153310" src="https://github.com/user-attachments/assets/f9cc0fb2-ec49-4326-b007-73e423e22f91" />

Pkg.installed tarkoituksena on, että tietty ohjelma on asennettuna.
Salt aloittaa kertomalla ajan milloin tilan suorittaminen aloitettiin.
Salt suorittaa tilan pkg.installed, jossa se tutkii ensin apt-cache ja dpkg-query komennoilla pakettienhallinnan paikallista välimuistia ohjelmista.
Kun ohjelmaa tree ei löydy asennetuista ohjelmista, suorittaa salt apt-get komennon ladatakseen ohjelman ja dpkg asentaakseen sen.
Tämän jälkeen salt käynnistää ohjelman hyödyntämällä systemd daemonia (systemctl). Lopuksi salt suorittaa toisen dpkg-query:n tarkistaakseen asennetun ohjelman version (tree 2.2.1-1).
Lopuksi salt kertoo ajan jolloin tilan suorittaminen saatiin päätökseen ja kauanko aikaa kului millisekunteina.
Sen jälkeen tulostuu raportti tilan suorituksesta, jossa kerrotaan mikä tila suoritettiin ja mihin se kohdennettiin, mitä tapahtui, alku ja kesto, muutokset, montako tilaa onnistui ja epäonnistui.

Toimintalogiikka pysyy muissa tiloissa samana, joten selitän vain kyseiseen tilaan relevantit komennot ja tulokset, välttääkseni turhaa toistoa.

**File:**

<img width="664" height="349" alt="Screenshot 2025-10-26 154134" src="https://github.com/user-attachments/assets/05e8d082-0565-442b-a6dc-d3954dba8330" />

<img width="296" height="181" alt="Screenshot 2025-10-26 154213" src="https://github.com/user-attachments/assets/e8adc304-427c-44b5-8c49-1e6a7dd5ba33" />

File.absent tarkoituksena on varmistaa, että tiedosto ei ole olemassa. Täten salt tarkistaa polun ja onko siellä kyseistä tiedostoa. Jos tiedosto löytyy,
salt poistaa tiedoston. Tiedosto löytyi polusta, joten salt poisti tiedoston.

**Service:**

<img width="665" height="370" alt="Screenshot 2025-10-26 154839" src="https://github.com/user-attachments/assets/e00d8f92-4220-4541-9335-eb150817cb79" />

<img width="292" height="176" alt="Screenshot 2025-10-26 154859" src="https://github.com/user-attachments/assets/f4d59be7-c22b-41f3-ac08-9f53649bd384" />

Service.running tarkoituksena on, että jokin prosessi (daemon) on käynnissä. Hyödyntäen systemctl komentoa, salt tarkistaa onko daemon käynnissä. Koska daemon
on käynnissä, ei muutoksia tarvitse tehdä, ja salt palauttaa True statuksen kommentilla "The service NetworkManager is already running".

**User:**

<img width="666" height="313" alt="Screenshot 2025-10-26 155313" src="https://github.com/user-attachments/assets/367da819-70da-4059-adf7-0e9bffe0c29d" />

<img width="268" height="357" alt="Screenshot 2025-10-26 155346" src="https://github.com/user-attachments/assets/85058f13-0b5e-439c-a6af-a0e2bb4a2a62" />

User.present tarkoitus on, että käyttäjätili on olemassa järjestelmässä. Salt suorittaa useradd komennon tilille 'testi123', mutta koska en antanut muita tietoja,
kuten salasana, muut kuin tilin nimi ja ID:t jäävät tyhjiksi. Kotikansion ja shellin se valitsee oletuksina ja ID:t Linux autogeneroi.

**Cmd:**

<img width="664" height="280" alt="Screenshot 2025-10-26 155952" src="https://github.com/user-attachments/assets/396cac2c-1936-41b5-a445-5db72397fb1e" />

<img width="314" height="313" alt="Screenshot 2025-10-26 160010" src="https://github.com/user-attachments/assets/9c591813-3677-4d19-9546-3f2592dab626" />

Cmd.run tila suorittaa komennon minionilla. Tässä tapauksessa komento luo tyhjän tiedoston tmp hakemistoon, jossa ehtona on create="tmp/testi123". Create kertoo,
että komento suoritetaan, jos siihen listatut tiedostot eivät ole olemassa (VMware 2025). Koska kyseistä tiedostoa ei ole, komento suoritetaan. Lopuksi tarkistin itse tiedoston
olemassa olon ls komennolla.

### d) Idempotentti

Idempotenssi on jonkin operaation ominaisuus, jossa yhden tai useamman kerran suoritettaessa lopputulos on sama. Tarkoitus on ratkaista ongelma,
jossa on mahdollista tahattomasti tai tahallisesti toistaa jokin operaatio, mutta lopputulos halutaan pitää samana kuin ensimmäisen suorituksen jälkeen. (Adetunji 2024.)
Yksi esimerkki on hissin kutsupainikkeet.
Painikkeen painaminen kutsuu hissiin kerrokseen, jossa olet, mutta useammat painallukset eivät nopeuta hissin tuloa tai muuta kerrosta mihin se saapuu.
Toiminta siis pysyy samana ensimmäisellä ja useammalla painalluksella.

Suoritin ja analysoin aikasemmin salt-call --local komentoja, joten en toista niitä tähän. Otan tähän esimerkiksi service.running tilan, jota analysoin aiemmin.
Tässä tilassa idempotenssi ilmenee siten, että lopputuloksena on oltava käynnissä prosessi "NetworkManager". Salt tarkistaa ensin, onko kyseinen prosessi jo käynnissä,
ja sen jälkeen käynnistää prosessin, jos se on tarpeen. Tässä tapauksessa prosessi oli jo käynnissä, joten mitään ei tarvinut tehdä lopputulokseen pääsemiseksi.
Jos prosessi ei olisi ollut käynnissä, olisi Salt jatkanut tilafunktion suorittamista eteenpäin ja suorittanut tarvittavan komennon prosessin käynnistämiseksi.
Salt hyödyntää siis ehtolauseita suorittaessaan tilafunktioita. Täten sama operaatio toimii ensimmäisellä ja kolmannellakymmenennellä kerralla samoin.

# Lähdeluettelo

Adetunji, D. 2024. Idempotence Explained. Luettavissa: https://lightcloud.substack.com/p/idempotence-explained .
Luettu: 26.10.2025.

Karvinen, T. 2025a. Palvelinten hallinta - Läksyt. Luettavissa: https://terokarvinen.com/palvelinten-hallinta/#laksyt .
Luettu: 26.10.2025.

Karvinen, T. 2025b. Install Salt on Debian 13 Trixie. Luettavissa: https://terokarvinen.com/install-salt-on-debian-13-trixie/ .
Luettu: 26.10.2025.

Karvinen, T. 2023. Run Salt Command Locally. Luettavissa: https://terokarvinen.com/2021/salt-run-command-locally/ .
Luettu: 26.10.2025.

Karvinen, T. 2018. Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux. Luettavissa: https://terokarvinen.com/2018/03/28/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/ .
Luettu: 26.10.2025.

Karvinen, T. 2006. Raportin kirjoittaminen. Luettavissa: https://terokarvinen.com/2006/06/04/raportin-kirjoittaminen-4/ .
Luettu: 26.10.2025.

VMware Inc. 2025. Salt.states.cmd. Luettavissa: https://docs.saltproject.io/en/latest/ref/states/all/salt.states.cmd.html .
Luettu: 26.10.2025.

VMware Inc. s.a. Linux (DEB) - Salt install guide. Luettavissa: https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/linux-deb.html .
Luettu: 26.10.2025.
