# Hardware hacking

Tehtävänanto löytyy kurssin Moodle sivulta, välilehdeltä Hardware hacking.

## Ohjelmistojen ja tiedostojen lataus

Avasin Moodlesta ohjeet välilehdeltä Hardware hacking.
Avasin linkatun GitHub repositorion ja kloonasin sen virtuaalikoneelleni `git clone`.

Koitin ensin ladata TapoV3 -binääriä Moodlen komennolla, mutta bash ei tunnistanut komentoa.
Hain tietoa `aws s3` komennosta ja löysin, että minun piti asentaa AWSCLI (https://www.geeksforgeeks.org/devops/aws-cli-s3-commands/).
Koetin ensin asentaa AWSCLI:n ohjeiden mukaan (https://www.geeksforgeeks.org/devops/how-to-install-aws-cli-on-ubuntu/), mutta Debian ei halunnut asentaa sitä pip:n kautta.
Tämän jälkeen hain "awscli Debian" ja löysin Debianin sivun paketista "awscli" (https://packages.debian.org/sid/awscli).
Asensin paketin komennolla `sudo apt-get install awscli`.
Tämän jälkeen onnistuin lataamaan binäärin koneelleni komennolla `aws s3 cp s3://download.tplinkcloud.com/firmware/Tapo_C200v3_en_1.4.2_Build_250313_Rel.40499n_up_boot-signed_1747894968535.bin Tapo_C200v4_en_1.4.2.bin --no-sign-request`.

## Decrypt firmware image

Siirryin kloonaamaani GitHub repoon `tp-link-decrypt` ja ajoin `./preinstall.sh` skriptin, sekä opettajan ohjeistuksella asensin erikseen `libssl-dev` ja `binwalk` paketit.
Tämän jälkeen ajoin `./extract_keys.sh` skriptin, mutta se kohtasi virheen:

<img width="1265" height="329" alt="image" src="https://github.com/user-attachments/assets/3439f1c2-9c33-4560-9748-e4f39a7c6e4b" />

Etsin internetistä "debian jefferson" ja löysin GitHub repositorion Jefferson työkalulle (https://github.com/onekey-sec/jefferson/).
Koetin ensin asentaa sen ohjeiden mukaan, mutta pip ei halunnut asentaa sitä.

```
error: externally-managed-environment

× This environment is externally managed
╰─> To install Python packages system-wide, try apt install
    python3-xyz, where xyz is the package you are trying to
    install.
    
    If you wish to install a non-Debian-packaged Python package,
    create a virtual environment using python3 -m venv path/to/venv.
    Then use path/to/venv/bin/python and path/to/venv/bin/pip. Make
    sure you have python3-full installed.
    
    If you wish to install a non-Debian packaged Python application,
    it may be easiest to use pipx install xyz, which will manage a
    virtual environment for you. Make sure you have pipx installed.
    
    See /usr/share/doc/python3.13/README.venv for more information.
```

Kokeilin ensin ilmoituksessa mainitun `sudo apt install python3-jefferson` komennon, mutta se ei myöskään tuottanut tulosta:
`Error: Unable to locate package python3-jefferson`.  
Tämän jälkeen kokeilin virheilmoituksessa mainittua `pipx` metodia.
Asensin ensin pipx:n `sudo apt-get install pipx` `pipx ensurepath --global`.
Sen asennuttua ajoin komennon `pipx install jefferson`, nyt Jefferson asentui onnistuneesti.

Ajoin `./extract_keys.sh` uudelleen, ja se suorittui nyt loppuun onnistuneesti.

<img width="609" height="79" alt="image" src="https://github.com/user-attachments/assets/3c90f819-da37-4625-bec6-b040014a3b25" />

Tämän jälkeen ajoin `make` komennon ohjeen mukaan. Tämä loi "tp-link-decrypt" ohjelman `bin` hakemistoon.

<img width="825" height="149" alt="image" src="https://github.com/user-attachments/assets/3c20c527-de18-4f07-af8b-be6fea0d597f" />

Siirryin takaisin "tapov3" hakemistoon, johon olin ladannut firmware imagen.
Ajoin komennon `./tp-link-decrypt/bin/tp-link-decrypt Tapo_C200v4_en_1.4.2.bin` ja tämä decryptasi firmwaren onnistuneesti.

<img width="1075" height="428" alt="image" src="https://github.com/user-attachments/assets/228c2e9d-e257-482c-8cde-df595748ed5d" />

## Analyse the image file

Aloin miettiä, miten analysoisin tiedostoa.
Koska tiedosto oli image kameran firmwaresta, ajattelin, että jos mounttaan imagen, pääsen tutkimaan tiedostoja.
Etsimisen jälkeen kuitenkin löysin, ettei .bin tiedostoja voi suoraan mountata (https://www.networkinghowtos.com/howto/mounting-bin-cue-files-on-linux/).  
Jatkoin etsimistä, miten analysoida .bin tiedostoa, ja törmäsin artikkeliin "binwalk: A tool for analyzing and extracting data from firmware images"(https://awjunaid.com/kali-linux/binwalk-a-tool-for-analyzing-and-extracting-data-from-firmware-images/).
Olin asentanut binwalk:n jo aikaisemmin, joten tutkin, millä komennoilla voisin tutkia tiedostoa.
Ajoin komennon `binwalk Tapo_C200v4_en_1.4.2.bin.dec |less` nähdäkseni tietoja tiedostosta, ja kiinnitin huomioni `squashfs filesystem` kohtaan.

<img width="1265" height="96" alt="image" src="https://github.com/user-attachments/assets/87a2d947-8d92-4099-a04e-0475f40de460" />

Tämä sisältäisi varmaankin järjestelmätiedostot.

## Extract rootfs from the image file

Tein uuden hakemiston tiedostoja varten `mkdir extract` ja ajoin komennon `binwalk -e --directory=extract Tapo_C200v4_en_1.4.2.bin.dec`.
Tämä extractasi tiedostot kyseiseen hakemistoon.
Siirryin hakemistoon ja listasin tiedostot `ls -la`.
Hakemistossa oli paljon pakattuja tiedostoja, mutta myös `squashfs-root` hakemisto.
Siirryin siihen, ja löysin linuxin hakemistoja:

<img width="699" height="204" alt="image" src="https://github.com/user-attachments/assets/f83b5b17-125d-44a9-a051-4c370b875a07" />

## Extract rootfs from the dump file

Toistin edellämainitun operaation binwalkilla dump tiedostolle `binwalk -e --directory=extract_dump/ dump-tapo-c200v3-1.4.2.bin`.

## Search available applications

Siirryin tutkimaan, mitä ohjelmia tiedostojärjestelmästä löytyi.
Katsoin ensiksi "bin" hakemiston:

<img width="225" height="181" alt="image" src="https://github.com/user-attachments/assets/b9a19cbd-e47d-4005-a44b-9b63477a056c" />

Main tiedosto herätti kiinnostukseni, ja tutkin sitä lisää Ghidralla.

<img width="1089" height="615" alt="image" src="https://github.com/user-attachments/assets/a4a5fdee-6ed1-48e5-be44-68f4f494202b" />

Tiedostossa on selvästi paljon kaikkea, sillä Ghidran analysoinnissakin kesti hetki.
En saanut koodista hirveästi selkoa, mutta katsoin mitä tekstiä ohjelmasta löytyi.
Stringsejä oli myös julmetusti, mutta löysin ainakin viittauksia internet yhteyden muodostamiseen ja DNS kyselyihin:

<img width="770" height="346" alt="image" src="https://github.com/user-attachments/assets/fc01d396-6087-478a-a7a6-e7a90514610b" />

Stringseissä oli myös viittauksia moottoreihin, sillä kamerassa on pan/tilt ominaisuus (https://www.tp-link.com/us/home-networking/cloud-camera/tapo-c200/).

<img width="770" height="441" alt="image" src="https://github.com/user-attachments/assets/7c9dbb62-39a5-4758-954c-ecef24f912f5" />

Tästä päättelin, että tämä on oletettavasti kameran pääohjelma (nimestäkin päätellen), jolla kaikki sen toiminnallisuus toteutetaan.
Muut ohjelmat ovat luultavasti ns. apuohjelmia tai boottauksen aikaisia ohjelmia, joilla testataan kameran olevan kunnossa (kuten "getcpuinfo" ja "impdbg").

Tutkin myös muut hakemistot läpi, jos niistä löytyisi lisää ohjelmia tai muuta kiinnostavaa.
Muista hakemistoista ei löytynyt lisää ohjelmia (tai ainakaan en itse niitä ohjelmiksi tunnistanut), mutta `usr` hakemistossa oli oma `bin` hakemisto.

<img width="939" height="176" alt="image" src="https://github.com/user-attachments/assets/aa7e54cd-79ce-4175-880c-c0cdea98d497" />

Katsoin `iperf` ohjelmaa `strings` komennolla.

<img width="1276" height="641" alt="image" src="https://github.com/user-attachments/assets/0e842f01-4427-462d-a065-1cc12b54e5e7" />

Luulen, että ohjelma liittyy kuvan lähettämiseen laitteelle, josta voi katsella kameran kuvaa.
Ohjelman asetuksia voi listattujen argumenttien perusteella myös muokata. Samankaltaisia argumentteja löytyi myös server sidelle:

<img width="1137" height="505" alt="image" src="https://github.com/user-attachments/assets/fc111109-51b8-47e4-881c-c59bd0ba3fd4" />

Tutkin internetistä, mikä "iperf" on, ja selvisi, että se on verkkoyhteyden kaistanleveyden mittaukseen käytettävä ohjelmisto (https://iperf2.sourceforge.io/iperf-manpage.html).
Ohjelma siis auttaa määrittämään maksimaalisen lähetysnopeuden kameralle, jotta oletettavasti se voi myös säätää lähetettävän kuvan laatua, riippuen yhteyden nopeudesta.
En löytänyt enempää ohjelmia, joten siirryin seuraavaan kohtaan tehtävässä.

## Analyze and try to open root password

Kameran tiedostojärjestelmä on sama kuin Linux:ssa, joten tarkistin ensin internetistä, mihin käyttäjät ja salasanat tallennettiin.
Salasanat tallennetaan `/etc/shadow` tiedostoon ja muut tiedot `/etc/passwd` tiedostoon (https://www.cyberciti.biz/faq/where-are-the-passwords-of-the-users-located-in-linux/).
Siirryin ensiksi siis tutkimaan `etc` hakemistoa.

<img width="1210" height="303" alt="image" src="https://github.com/user-attachments/assets/2cd11125-375d-4484-8553-7ed2c0f527fc" />

En nähnyt kyseisiä tiedostoja hakemistossa, joten siirryin etsimään `usr` hakemistosta, jos salasanat olisivat siellä.
En löytänyt `shadow` tai `passwd` tiedostoa sieltäkään.
Seuraavaksi ajattelin, että voisin etsiä `squashfs-root` hakemistosta viittauksia `root` käyttäjään, jolloin saattaisin löytää ainakin vihjeitä salasanan sijaintiin.
Tarkistin internetistä `grep` komennon syntaksin (https://www.man7.org/linux/man-pages/man1/grep.1.html) ja ajoin komennon: `grep -Ri "root"` `squashfs-root` hakemistossa.
Grep palautti tiedostoja, joista se löysi vastaavia tekstinpätkiä.

<img width="630" height="252" alt="image" src="https://github.com/user-attachments/assets/92b09c49-e948-4e5d-81fc-5a75be3c9a04" />

Kävin tiedostot läpi, mutta en nähnyt viittauksia salasanaan, vaan enneminkin tiedostojärjestelmään.
Koetin etsiä tietoa internetistä, miksi `/etc/shadow` tiedostoa ei ole olemassa tiedostoissa, mutta en löytänyt vastausta.
Seuraavaksi sain idean etsiä salasanoja `dump` imagesta, joka on otettu kameran muistista.
Siirryin aikaisemmin luomaani `extract_dump` hakemistoon ja siirryin siellä `etc` hakemistoon.
En kuitenkaan löytänyt kyseisiä tiedostoja sieltäkään.
Suoritin myös saman `grep` haun, mutta sain vastaukseksi samat tiedostot, jotka oletin olevan samat sisällöltään.

Itseltäni loppuivat ideat, miten löytää salasana kummastakaan imagesta. Ehkä tunnilla saan vastauksen, miten joku on löytänyt sen.

## Lähdeluettelo

