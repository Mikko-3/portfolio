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

Ajoin `./extract_keys.sh` uudelleen, ja sen suorittui nyt loppuun onnistuneesti.

<img width="609" height="79" alt="image" src="https://github.com/user-attachments/assets/3c90f819-da37-4625-bec6-b040014a3b25" />

Tämän jälkeen ajoin `make` komennon ohjeen mukaan. Tämä loi "tp-link-decrypt" ohjelman `bin` hakemistoon.

<img width="825" height="149" alt="image" src="https://github.com/user-attachments/assets/3c20c527-de18-4f07-af8b-be6fea0d597f" />

Siirryin takaisin "tapov3" hakemistoon, johon olin ladannut firmware imagen.
Ajoin komennon `./tp-link-decrypt/bin/tp-link-decrypt Tapo_C200v4_en_1.4.2.bin` ja tämä decryptasi firmwaren onnistuneesti.

<img width="1075" height="428" alt="image" src="https://github.com/user-attachments/assets/228c2e9d-e257-482c-8cde-df595748ed5d" />

## Analyse the image file

