# H4 - Some Disassembly Required

Kotitehtävän h4 raportti. Kotitehtävän tehtävänanto löytyy kurssin verkkosivulta (Karvinen 2026).

## x) Tiivistelmä

### Ghidra for Reverse Engineering (PicoCTF 2022 #42 'bbbloat')

- Ennen Ghidran avaamista, voi yrittää muita keinoja, kuten `strace`, `ltrace` ja `objdump -d`.
- Ghidra on Yhdysvaltojen National Security Agency:n (NSA) kehittämä työkalu, joka julkaistiin 2019.
- Ghidra on saatavilla GitHub:sta, ja vaatii Java JDK:n oltava asennettuna.
- Kun avaat Ghidran, luo uusi projekti ja lisää tutkittava binääri (import file).
- Kaksoisklikkaa binääriä, ja Ghidra avaa binäärin CodeBrowser:ssa 🐲.
- Ghidra ehdottaa tiedoston analysointia, valitse kyllä, oletusasetukset ovat yleensä ok.
- "Window, Defined strings" valikosta saat näkyviin tiedostossa olevat stringsit.
- Klikkaamalla stingiä, siirryt kohtaan koodia, jossa kyseinen string esiintyy.
- Jos sitä käytetään esimerkiksi jossain funktiossa, voit nähdä `xref` (cross-reference) tekstin.
- Tuplaklikkaamalla `xref` voit siirtyä kyseiseen funktioon (FUN_).
- Decompile ikkuna näyttää koodin C-koodina, jossa voit nähdä funktiot, muuttujat ym.
- Tutkimalla koodia, löytyi muuttuja, johon verrattiin käyttäjän syötettä.
- Muuttamalla muuttujan sisältämän heksakoodin desimaaliluvuksi, oikea vastaus ohjelman kysymykseen löytyi ja lippu palautettiin vastauksena.

(Hammond, 2022.)

## a) Install Ghidra

Aloitin Ghidran asennuksen menemällä Ghidran GitHub sivuille (https://github.com/NationalSecurityAgency/ghidra/tree/master).
Siirryin kohtaan "Install", ja luin vaadittavat asiat asentamista varten.
Tarvitsin Javan JDK:n version 21 tai uudemman, joten asensin sen ensin.
Etsin asennettavaa Javaa komennolla `apt-cache search java jdk |less`.
Huomasin listassa OpenJDK 25:n, ja tarkistettuani hakukoneesta sen olevan aito Java versio (https://openjdk.org/), asensin sen komennolla `sudo apt-get install openjdk-25-jdk`.
Asennuksen valmistuttua tarkistin version komennolla `java --version`.

<img width="517" height="63" alt="image" src="https://github.com/user-attachments/assets/3861d3dc-ee6d-4968-b390-d00a702c8f98" />

Tämän jälkeen loin uuden hakemiston Ghidraa varten ja latasin Ghidran uusimman version GitHub:sta.
`wget https://github.com/NationalSecurityAgency/ghidra/releases/download/Ghidra_12.0.2_build/ghidra_12.0.2_PUBLIC_20260129.zip`.
Latauksen valmistuttua purin zip paketin unzip komennolla `unzip ghidra_12.0.2_PUBLIC_20260129.zip`.
Siirryin kansioon `cd ghidra_12.0.2_PUBLIC/` ja avasin Ghidran komennolla `./ghidraRun`, se avautui onnistuneesti.

<img width="417" height="322" alt="image" src="https://github.com/user-attachments/assets/fa56cdb3-6d58-4747-92a6-f764ddde9b1f" />

## b) rever-C

Loin uuden projektin Ghidrassa ja lisäsin binäärin `packd.~`. Tämä on alkuperäinen binääri edellisestä kotitehtävästä.
Avasin binäärin CodeBrowserissa, annoin Ghidran analysoida binäärin oletusasetuksilla ja aloin tutkimaan sisältöä.
Ghidra aloitti minut kohdasta "process entry", jonka päättelin olevan "main" funktio, sillä se on ohjelman ensimmäinen funktio.
Kun aloin tutkia ohjelman sisältämiä stringejä (window, defined strings), ikkunassa ei näkynyt paljoa tekstiä.
Huomasin sitten tekstin `00105870	$Info: This file is packed with the UPX executable packer http://upx.sf.net` ja muistin, että edellisessä tehtävässä pakkaus esti binäärin kunnollisen tutkimisen.
Niinpä suljin CodeBrowserin, lisäsin projektiin puretun binäärin ja avasin sen.
Nyt tekstiä löytyi enemmän, mukaanlukien salasana ja lippu.
Siirryin vasemman palkin "symbols" valikosta "funktions" kansioon, josta löysin "main" funktion. Ohjelma oli kai nimennyt sen valmiiksi, mutta se näytti oikealta.
Seuraavaksi annoin muuttujille kuvaavat nimet, `int iVar1 -> int vertaus_palautus` ja `char local_28 -> käyttäjän_syöte`.

<img width="205" height="86" alt="image" src="https://github.com/user-attachments/assets/15dd2ab0-aaaf-42b6-9ff9-80c58081a9fc" />

### Koodin toiminta

Main funktio kokonaisuudessaan:

```
undefined8 main(void)

{
  int vertaus_palautus;
  char käyttäjän_syöte [32];
  
  puts("What\'s the password?");
  __isoc99_scanf(&DAT_0010201d,käyttäjän_syöte);
  vertaus_palautus = strcmp(käyttäjän_syöte,"piilos-AnAnAs");
  if (vertaus_palautus == 0) {
    puts("Yes! That\'s the password. FLAG{Tero-0e3bed0a89d8851da933c64fefad4ff2}");
  }
  else {
    puts("Sorry, no bonus.");
  }
  return 0;
}
```

Aluksi ohjelma luo kaksi muuttujaa, kokonaisluvun "vertaus_palautus", johon myöhemmin tallennetaan merkkijonojen vertailun tulos, sekä merkkijonon "käyttäjän_syöte".
Merkkijonoon mahtuu 32 merkkiä, ja tähän tallennetaan käyttäjän syöttämä salasana.
Sen jälkeen ohjelma tulostaa tekstin "What's the password?" ja odottaa käyttäjän syötettä.
Tämän jälkeen ohjelma suorittaa funktion `strcmp`, jossa se vertaa kahta merkkijonoa keskenään.
Funktio palauttaa luvun 0, jos merkkijonot ovat samat, tai positiivisen/negatiivisen luvun, jos merkkijonot eroavat (https://www.geeksforgeeks.org/c/strcmp-in-c/).
Tämän funktion palautus tallennetaan aiemmin luotuun "vertaus_palautus" muuttujaan, jonka jälkeen siirrytään if-lauseeseen.
If-lause tarkistaa, onko `vertaus_palautus = 0`, jonka jälkeen se tulostaa tekstin "Yes! That's the password. FLAG{Tero-0e3bed0a89d8851da933c64fefad4ff2}", paljastaen lipun, jos muuttujan arvo oli 0.
If-lause palauttaa tekstin "Sorry, no bonus.", jos muuttujan arvo ei ole 0.
Tämän jälkeen funktio palauttaa nollan, ja ohjelma sulkeutuu.

## c) If backwards

Löysin internetistä ohjeen, jota seurasin tämän tehtävän tekemisessä (https://blog.cjearls.io/2019/04/editing-executable-binary-file-with.html).
Avasin uuden projektin Ghidrassa, lisäsin `passtr` ohjelman.
Valitsin ohjeen mukaan valinnan "raw binary", mutta en tiennyt mikä kieli minun olisi pitänyt valita.
Menin takaisin terminaaliin ja luin tietoja ohjelmasta `strings` komennolla.
Sieltä näin, että ohjelma oli 64 bittinen ja compilettu GCC:llä.

<img width="405" height="553" alt="image" src="https://github.com/user-attachments/assets/695732c6-7270-45fd-be00-16c7ea11941d" />

Etsin "language" kohdasta "gcc" ja valitsin 64 bittisen version.
Avasin binäärin ja analysoin sen oletusasetuksilla.

Etsin main funktion, tässä tapauksessa nimeltä `FUN_00001159` ja siirryin siihen.
Nimesin funktion uudelleen nimelle `main` tehdäkseni nimestä helppolukuisemman.
Tämän jälkeen tutkin koodin toimintaa, ja huomasin if-lauseen, jossa tarkistetaan onko muuttujan arvo 0.
Koska `strcmp` palauttaa luvun 0, jos merkkijonot ovat samat, voi logiikkaa muuttaa vaihtamalla if-lauseen vertailun muotoon `!=0`.
Näin lause on tosi, jos luku ei ole 0, joten ohjelma hyväksyy kaikki salasanat, paitsi oikean.  
Seuraavaksi luin eteenpäin ohjeesta, miten voin muuttaa koodia.
Kokeilin ohjeessa kuvailtua "patch instruction" optiota, mutta Ghidra ei jostain syystä halunnut minun antaa muokata arvoja, vaikka koetin sitä monta kertaa.
En onnistunut löytämään ongelmaan ratkaisua internetistä, mutta tämän jälkeen kun kokeilin uudelleen, muokkaaminen toimi.
En keksinyt tähän mitään syytä, ehkä odottaminen oli jostain syystä tarpeellista.

Nyt minun piti selvittää, mitä arvoa minun piti muuttaa ja miksi se pitäisi muuttaa.
Löysin sivuston, jolla kerrottiin Assemblyn yleisimpiä instructioneita (https://materials.rangeforce.com/tutorial/2020/04/12/Patching-Binaries/).
Huomasin, että if-lauseen kohdalla oli `JZN` instruction, joka tarkoitti "jump if nonzero".
Tämä oli siis kohta, jossa verrattiin `strcmp` palauttamaa arvoa nollaan. Jos arvo ei ollut nolla, ohjelma "hyppää" seuraavaan kohtaan.
Vaihdoin `JZN` instructionin `JZ` instructioniksi, jolloin if-lauseke toimii päinvastoin, "hypäten" jos arvo on nolla.
Myös decompiler näkymässä koodi muuttui vastaavanlaiseksi, kun poistuin muokkaamisesta.

Ennen:

<img width="948" height="159" alt="image" src="https://github.com/user-attachments/assets/acbc3c0c-8b10-4573-b6f5-13b067d33691" />

Jälkeen:

<img width="946" height="162" alt="image" src="https://github.com/user-attachments/assets/4f10d5ae-4d66-4bab-85c2-9a3564cd4e24" />

Tämän jälkeen tallensin ohjelman `ctrl+s` ja exportasin sen `o` (https://blog.cjearls.io/2019/04/editing-executable-binary-file-with.html).
Valitsin formaatiksi "original file" ja nimesin ohjelman "passtr_muokattu".
Tämän jälkeen siirryin kotihakemistooni terminaalissa, lisäsin suoritusoikeudet ohjelmaan `chmod +x passtr_muokattu` komennolla ja suoritin ohjelman.
Ohjelma avautui onnistuneesti ja kun kirjoitin väärän salasanan, ohjelma tulosti salasanan olevan oikein.

<img width="904" height="125" alt="image" src="https://github.com/user-attachments/assets/a3d7f0bf-2d2d-45b8-980b-ddeb2388b8a5" />

Testasin myös oikean salasanan, ja ohjelma sanoi salasanan olevan väärä.

<img width="406" height="135" alt="image" src="https://github.com/user-attachments/assets/db1a4dc3-23e8-4363-a189-46f466979864" />

## d) Nora CrackMe

Menin tehtävänannossa annettuun GitHub linkkiin (https://github.com/NoraCodes/crackmes), ja latasin tiedostot "crackme01.c", "-01e.c", "-02.c" ja "makefile".
Tämän jälkeen noudatin README:n ohjeita ja suoritin `make` komennon kaikille crackme tiedostoille.

## e) Nora crackme01

Käynnistin ohjelman `./crackme01.64` ja ohjelma tulosti "Need exactly one argument."
Tutkin ohjelmaa ensin `strigs` komennolla.
Huomasin tekstin "password1". Annoin tämän argumentiksi, kun käynnistin ohjelman uudelleen `./crackme01.64 password1`.
Tämä antoi oikean vastauksen.

<img width="524" height="88" alt="image" src="https://github.com/user-attachments/assets/06ad974b-abce-411f-a89d-8db92aa23874" />

Avasin binäärin vielä Ghidrassa, ja tutkin main funktiota.

```
undefined4 main(int param_1,long param_2)

{
  char *__s1;
  int iVar1;
  undefined4 uVar2;
  
  if (param_1 == 2) {
    __s1 = *(char **)(param_2 + 8);
    iVar1 = strncmp(__s1,"password1",9);
    if (iVar1 == 0) {
      printf("Yes, %s is correct!\n",__s1);
      uVar2 = 0;
    }
    else {
      printf("No, %s is not correct.\n",__s1);
      uVar2 = 1;
    }
  }
  else {
    puts("Need exactly one argument.");
    uVar2 = 0xffffffff;
  }
  return uVar2;
}
```

Main funktioon on lisätty `(int param_1,long param_2)`, jotka viittaavat ns. "Command-line argumentteihin" (https://www.geeksforgeeks.org/c/main-function-in-c/).
Koodi palauttaa nollan, vain jos `param_2` on sama kuin teksti "password1".
Ratkaisin tehtävän siis omasta mielestäni oikein.

## e) Nora crackme01e

