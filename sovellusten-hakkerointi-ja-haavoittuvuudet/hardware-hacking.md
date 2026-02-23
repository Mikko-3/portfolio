# Hardware hacking

Avasin Moodlesta ohjeet välilehdeltä Hardware hacking.
Avasin GitHub repositorion ja kloonasin sen virtuaalikoneelleni.

Koitin ensin ladata TapoV3 -binääriä Moodlen komennolla, mutta bash ei tunnistanut komentoa.
Hain tietoa `aws s3` komennosta ja löysin, että minun piti asentaa AWSCLI (https://www.geeksforgeeks.org/devops/aws-cli-s3-commands/).
Koetin ensin asentaa AWSCLI:n ohjeiden mukaan (https://www.geeksforgeeks.org/devops/how-to-install-aws-cli-on-ubuntu/), mutta Debian ei halunnut asentaa sitä pip:n kautta.
Tämän jälkeen hain "awscli Debian" ja löysin Debianin sivun paketista "awscli" (https://packages.debian.org/sid/awscli).
Asensin paketin komennolla `sudo apt-get install awscli`.
Tämän jälkeen onnistuin lataamaan binäärin koneelleni.

Ajoin `./preinstall.sh` skriptin, ja opettajan ohjeistuksella erikseen `libssl-dev` ja `binwalk`.
Tämän jälkeen ajoin `./extract_keys.sh` skriptin, mutta se kohtasi virheen:

<img width="1265" height="329" alt="image" src="https://github.com/user-attachments/assets/3439f1c2-9c33-4560-9748-e4f39a7c6e4b" />

Etsin internetistä "debian jefferson" ja löysin GitHub repositorion Jefferson työkalulle.
Koetin ensin asentaa sen ohjeiden mukaan, mutta pip ei halunnut asentaa sitä.
(Jatka jefferson asennus)
