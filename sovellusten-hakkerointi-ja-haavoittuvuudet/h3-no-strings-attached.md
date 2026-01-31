# H3 No Strings Attached

Kotitehtävän h3 raportti. Kotitehtävän tehtävänanto löytyy kurssin verkkosivulta (Karvinen 2026).

## Ympäristö

Virtuaalikone:

- Virtualisointialusta: Oracle VirtualBox
- OS: Debian 13 Trixie
- Selain: Firefox 140.7.0esr (64-bit)
- 2 ydintä, 4 GB muistia
- NAT verkko, tehtävän ajaksi kytketty pois päältä

## a) Strings

Latasin harjoituskansion Teron sivuilta wget avulla, ja purin paketin unzip:lla.
Siirryin `challenges/passtr` hakemistoon ja luin 'README.md' tiedoston.
Asensin siinä listatut esivaatimukset (make, gcc, micro) ja ajoin ohjelman `./passtr` komennolla.

<img width="556" height="106" alt="image" src="https://github.com/user-attachments/assets/0f41db51-1d6b-4000-b12d-3c323c910313" />

Ensimmäinen satunnainen arvaukseni ei ollut oikein.

Seuraavaksi tutkin ohjelman koodia strings:llä.
Ajoin komennon `strings passtr |less` ja rupesin etsimään salasanaan viittaavaa tekstiä.
Huomasinkin heti tekstin alkupäässä kohdan `What's the password?`, jossa ohjelma kysyi salasanaa.
Tämän jälkeen oli luultavasti if-lauseke koodissa, jossa tarkastetaan, vastaako käyttäjän syöte salasanaa.
Sen alta löytyikin selkokielisenä salasana `sala-hakkeri-321`.

<img width="403" height="176" alt="image" src="https://github.com/user-attachments/assets/8e101716-d6c3-469b-b90e-cf632cfb7c9f" />

Suljin less:n ja käynnistin ohjelman uudelleen kokeillakseni salasanaa.
Salasana oli oikea!

<img width="450" height="77" alt="image" src="https://github.com/user-attachments/assets/7cf213bb-6e2a-401a-a064-73376e0423f0" />

Salasanan yhteydessä oli myös lippu `FLAG{Tero-d75ee66af0a68663f15539ec0f46e3b1}`, joka näkyi myös stringsillä.

## b) Fix

Avasin lähdekoodin microssa, ja tutkin koodin toimintaa.
Ajattelin ensin, että siirtämällä verrattavan tekstin muuttujaan, se piilottaisi tekstin stringsiltä.

<img width="571" height="383" alt="image" src="https://github.com/user-attachments/assets/eed58905-1905-4421-8ac1-a8e4e44fe63f" />

Tallensin tiedoston ja ajoin 'make' komennon README:n mukaisesti.
Tämän jälkeen ajoin ohjelman uudestaan tarkistaakseni, että ohjelma toimi, ja toiminnan todettuani tutkin binääriä stringsillä.

<img width="452" height="299" alt="image" src="https://github.com/user-attachments/assets/147d2642-3073-434e-8c55-d263022aa3e6" />

Tämä ei piilottanut salasanaa, vaan jakoi sen jostain syystä kahteen osaan.

Aloin tutkimaan string muuttujien piilottamista C-kielessä.
Tuntien kryptografiaan liittyvien artikkeleiden tutkimisen jälkeen en edelleenkään ymmärtänyt, miten kyseisen asian olisi voinut toteuttaa.
Lopulta löysin yksinkertaisemman esimerkin stringin piilottamisesta (https://yurisk.info/2017/06/25/binary-obfuscation-string-obfuscating-in-C/).
Kopioin koodin `passtr.c` tiedostoon, muokaten muuttujien nimet ja 'piilotettavat kirjaimet'.
`passtr.c` koodi kokonaisuudessaan:

```
// passtr - a simple static analysis warm up exercise
// Copyright 2024 Tero Karvinen https://TeroKarvinen.com

#include <stdio.h>
#include <string.h>
#define HIDE_LETTER(a)   (a) + 0x50
#define UNHIDE_STRING(str)  do { char * ptr = str ; while (*ptr) *ptr++ -= 0x50; } while(0)
#define HIDE_STRING(str)  do {char * ptr = str ; while (*ptr) *ptr++ += 0x50;} while(0)

int main() {
	char password[20];
	char vertaus[]={HIDE_LETTER('s'),HIDE_LETTER('a'),HIDE_LETTER('l'),HIDE_LETTER('a'),
	HIDE_LETTER('-'),HIDE_LETTER('h'),HIDE_LETTER('a'),HIDE_LETTER('k'),HIDE_LETTER('k'),
	HIDE_LETTER('e'),HIDE_LETTER('r'),HIDE_LETTER('i'),HIDE_LETTER('-'),HIDE_LETTER('3'),
	HIDE_LETTER('2'),HIDE_LETTER('1'), '\0'};
		
	printf("What's the password?\n");
	scanf("%19s", password);

	UNHIDE_STRING(vertaus);  // unmangle the string in-place

	if (0 == strcmp(password, vertaus)) {
		printf("Yes! That's the password. FLAG{Tero-d75ee66af0a68663f15539ec0f46e3b1}\n");
	} else {
		printf("Sorry, no bonus.\n");
	}
	return 0;
	HIDE_STRING(vertaus);  // unmangle the string in-place
}
```

Kunnia HIDE_LETTER, UNHIDE_STRING ja HIDE_STRING funktioista kuuluu siis Yuri Slobodyanyuk:lle (https://www.linkedin.com/in/yurislobodyanyuk/), joka on aikaisemmin linkittämäni artikkelin kirjoittanut.
En loppujenlopuksi ole varma, kuinka hyvä ratkaisu tämä kokonaisuudessaan on, mutta testatessani ohjelman toiminnan, ohjelma toimi oikein, kun annoin oikean ja väärän salasanan.

<img width="451" height="112" alt="image" src="https://github.com/user-attachments/assets/53270878-b285-4427-8cc2-8fab7a3de151" />

`strings` ei myöskään näyttänyt salasanaa.

<img width="451" height="365" alt="image" src="https://github.com/user-attachments/assets/fbe2965b-df00-435e-8a80-21e007f07b66" />

<img width="253" height="340" alt="image" src="https://github.com/user-attachments/assets/d0aef2eb-efa4-49be-8daa-d0f0d8838aee" />

<img width="173" height="358" alt="image" src="https://github.com/user-attachments/assets/44c4544b-ef96-4653-8c7d-b5d2e4e3ba10" />

<img width="118" height="131" alt="image" src="https://github.com/user-attachments/assets/36b134cf-3283-433a-b2e1-47b35036fbf0" />

## c) Packd

Ajoin ohjelman `./packd` ja se kysyi salasanaa. Kokeilin `asdf123`, eikä se ollut oikein. Ohjelma tuntui toimivan samalla periaatteella kuin edellinen.
Avasin binäärin strings:llä ja huomasin tekstissä salasanaan viittaavaa tekstiä:

<img width="346" height="178" alt="image" src="https://github.com/user-attachments/assets/0482ee49-a537-4836-a6ff-3bfd1b05088f" />

En uskonut, että tämä olisi koko salasana, mutta kokeilin sitä kuitenkin.

<img width="527" height="105" alt="image" src="https://github.com/user-attachments/assets/f05df8b7-f78c-43f6-b814-009adbfc578d" />

Olin oikeassa, salasana ei ollut oikein.

Luin lisää strings:n tekstiä, ja huomasin kohdan `$Info: This file is packed with the UPX executable packer http://upx.sf.net $
$Id: UPX 4.21 Copyright (C) 1996-2023 the UPX Team. All Rights Reserved. $`
Ajattelin, olisiko tämä ohjelma samalla obfuskoinut koodin, kun se pakkasi ohjelman binääriksi?
Päätin tutkia ohjelmaa lisää.
UPX:n sivuilla (https://upx.github.io/) kerrottiin sen olevan 'executable file compressor', joka siis pakkaa binäärit pienempään kokoon.
Pakkauksen takia strings ei saa kaikkea tekstiä näkyviin, joten seuraavaksi kokeilin purkaa paketin pakkaamattomaan muotoon.
Kytkin internetyhteyden takaisin virtuaalikoneeseen ja asensin UPX:n (`sudo apt-get install upx`).

<img width="509" height="290" alt="image" src="https://github.com/user-attachments/assets/7b208b54-5370-49af-96ba-6bdab8e57cc3" />

Tarkistin vielä internetistä ohjeita ja huomasin tällä sivulla (https://greatbinary.win/article/unpacking-upx-packed-binaries/) `--backup` option, joka varmuuskopioi alkuperäisen pakatun paketin.
Näin minun ei tarvitsisi ladata uudelleen pakettia, jos jotain menee pieleen.
Kytkin internetin pois käytöstä ja purin paketin `upx -d -v --backup packd`.

<img width="504" height="138" alt="image" src="https://github.com/user-attachments/assets/d160e557-9256-4c78-8ddc-29a91fa6cf42" />

Nyt hakemistossa oli kaksi tiedostoa, `packd` ja `packd.~`.
En tajunnut lisätä nimeä tiedostolle, joten tutkin `ls -la` komennolla, kumpi paketeista oli isompi tiedostokooltaan.

<img width="368" height="87" alt="image" src="https://github.com/user-attachments/assets/38c6326d-d835-4188-a5b4-2122f97c6fd3" />

`packd` oli suurempi, joten tutkin sitä strings:llä.
Nyt binäärissä näkyi paljon enemmän ja paremmin selkokielistä tekstiä, ja löysinkin salasanan ja lipun.
`piilos-AnAnAs` `FLAG{Tero-0e3bed0a89d8851da933c64fefad4ff2}`

<img width="451" height="65" alt="image" src="https://github.com/user-attachments/assets/58fc1dca-f485-49f3-a263-6c8982cedd1d" />

Varmistin vielä, että salasana oli oikea.

<img width="452" height="65" alt="image" src="https://github.com/user-attachments/assets/7f41a55d-5f3d-42a1-af44-cdeaf5a93848" />

Salasana oli oikein ja lippu vastasi koodista löytynyttä.

## Lähdeluettelo

Kayaliev, L. 2025. Unpacking UPX-packed Binaries - Complete Guide. Luettaissa: https://greatbinary.win/article/unpacking-upx-packed-binaries/. Luettu: 31.01.2026.  
Karvinen, T. 2026. Application hacking - 2026 Spring. Luettavissa: https://terokarvinen.com/application-hacking/#laksyt. Luettu: 31.01.2026.  
Slobodyanyuk, Y. 2017. Binary obfuscation - String obfuscating in C. Luettavissa: https://yurisk.info/2017/06/25/binary-obfuscation-string-obfuscating-in-C/. Luettu: 30.01.2026.
