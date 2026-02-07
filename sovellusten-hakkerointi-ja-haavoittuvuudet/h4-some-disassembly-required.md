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

Käynnistin crackme01e ohjelman `./crackme01e.64` ja ohjelma tulosti saman tekstin kuin edellinen, "Need exactly one argument.".
Tutkin myös tätä ohjelmaa `strings` komennolla, mutta tällä kertaa en huomannut mitään oikeaan argumenttiin viittaavaa tekstiä.
Avasin binäärin Ghidrassa ja analysoin sen oletusasetuksilla.
Aloin tutkimaan main funktiota, joka oli lähes samanlainen kuin edellisessä ohjelmassa.
Näin kuitenkin merkkijonojen vertailussa tekstin, johon annettua argumenttia verrattiin:
`iVar1 = strncmp(__s1,"slm!paas.k",10);`.
Päätin kokeilla "slm!paas.k" argumenttina ohjelman käynnistyksessä, mutta bash kohtasi jonkinlaisen ongelman.

<img width="548" height="68" alt="image" src="https://github.com/user-attachments/assets/f48f74b3-8217-4e0a-b452-d39811f0792c" />

Seuraavaksi koetin samaa argumenttia, mutta heittomerkeillä `./crackme01e.64 "slm!paas.k"`, mutta kohtasin saman virheen.
Etsin internetistä mikä on "bash event not found", ja löysin vastauksen Stackoverflow:sta (https://stackoverflow.com/a/11816138).
Kirjoitin terminaaliin `set +H` ja ajoin sen jälkeen edellisen komennon uudelleen. Nyt sain oikean vastauksen.
Salasana toimi myös ilman heittomerkkejä.

<img width="566" height="112" alt="image" src="https://github.com/user-attachments/assets/122a17c2-dae5-42b2-9ea5-f6631831563c" />

Tutkin binääriä vielä uudelleen `strings` komennon avulla, tarkistaakseni oliko salasana näkyvissä siellä.
Salasana oli näkyvissä, mutta en ollut tajunnut sitä salasanaksi, sillä se ei ollut niin yksiselitteinen.

<img width="297" height="94" alt="image" src="https://github.com/user-attachments/assets/e19c198f-1ad3-45bf-a052-3ced0677da3a" />

## f) Nora crackme02

Käynnistin crackme02.64 ohjelman `./crackme02.64`, ja ohjelma tulosti saman tekstin kuin kaksi edellistä.
Tutkin binääriä taas `strings` avulla. Huomasin taas tekstin "password1", ja kokeilin sitä argumentiksi.
Ohjelma vastasi salasanan olevan väärä.

<img width="532" height="73" alt="image" src="https://github.com/user-attachments/assets/6ca38fd3-4d13-4509-8efd-53c69d02f717" />

Tämän jälkeen avasin binäärin Ghidrassa ja analysoin sen oletusasetuksilla.
Tutkin jälleen main funktiota:

```
undefined8 main(int param_1,long param_2)

{
  char cVar1;
  char cVar2;
  undefined8 uVar3;
  long lVar4;
  
  if (param_1 == 2) {
    cVar2 = 'p';
    lVar4 = 0;
    do {
      cVar1 = *(char *)(*(long *)(param_2 + 8) + lVar4);
      if (cVar1 == '\0') break;
      if (cVar2 + -1 != (int)cVar1) {
        printf("No, %s is not correct.\n");
        return 1;
      }
      cVar2 = "password1"[lVar4 + 1];
      lVar4 = lVar4 + 1;
    } while (cVar2 != '\0');
    printf("Yes, %s is correct!\n");
    uVar3 = 0;
  }
  else {
    puts("Need exactly one argument.");
    uVar3 = 0xffffffff;
  }
  return uVar3;
}
```

Tutkiskelin koodia ja huomasin, että ohjelma käy `do` silmukassa läpi "password1" sanan kirjaimet indeksi kerrallaan, kunhan sen viimeinen merkki ei ole `\0` eli NULL character.
Null character on jokaisen merkkijonon viimeinen merkki, vaikka sitä ei näekään (https://www.geeksforgeeks.org/c/difference-between-null-pointer-null-character-0-and-0-in-c-with-examples/).
En onnistunut pitkän miettimisen ja tutkimisen jälkeen ymmärtämään, miten `cVar1 = *(char *)(*(long *)(param_2 + 8) + lVar4);` toimii, mutta se taisi olla vain hämäystä.
Aloin miettimään do-silmukan seuraavaa vaihetta `if (cVar2 + -1 != (int)cVar1) {`.
Jos ymmärsin oikein, `cVar2 + -1` viittaa indeksiin -1 merkkijonossa cVar2. Tämä siis tarkottaisi merkkijonon viimeistä merkkiä.
Ajattelin, jos ohjelma haluaakin salasanan takaperin, mutta se ei ollut oikein.
Kokeilun jälkeen mietin, että `if (cVar2 + -1 != (int)cVar1) {` vertaisikin, onko cVar2 indeksin -1 eri, kuin cVar1 kokonaisluvuksi muutettuna.
Mutta cVar2 sisältää vain yhden merkin, eikä -1 ole indeksin osoittajana.
Sitten ajattelin, jos -1 poistaakin cVar2 muuttujasta yhden muuttujan, ensimmäisellä kierroksella siis merkin "p".
Täten muuttuja olisi tyhjä merkkijono, jota verrataan cVar1 muuttujaan, ja if-lause toteutuu, jos molemmat eivät ole tyhjiä.
Kokeilin sen jälkeen tyhjää argumenttia käynnistäessäni ohjelmaa, mutta pelkkä välilyönti ei käynyt.
Kokeilin laittaa kaksi hipsukkaa komentoon `./crackme02.64 ''`.

<img width="444" height="60" alt="image" src="https://github.com/user-attachments/assets/519567c2-be10-4ae2-a492-e15470f9dcd7" />

Tämä oli oikea vastaus! En ole itsekään täysin varma, miten onnistuin päätymään tähän ratkaisuun, mutta ainakin ohjelma itse sanoo sen olevan oikein.

![Falling up the stairs](https://media1.tenor.com/m/hNj08SF198wAAAAd/cartoons.gif)

Lopuksi muutin Ghidrassa muuttujien ja funktioiden nimet paremmin luettaviksi.

<img width="435" height="484" alt="image" src="https://github.com/user-attachments/assets/5958617c-aa5f-4abe-ba27-267d08f477dd" />

## Lähdeluettelo

Faulty Logic. 2019. Editing an Executable Binary File with Ghidra. Luettavissa: https://blog.cjearls.io/2019/04/editing-executable-binary-file-with.html. Luettu: 07.02.2026.  
GeeksForGeeks a. 2025. C strcmp(). Luettavissa: https://www.geeksforgeeks.org/c/strcmp-in-c/. Luettu: 07.02.2026.  
GeeksForGeeks b. 2025. main Function in C. Luettavissa: https://www.geeksforgeeks.org/c/main-function-in-c/. Luettu: 07.02.2026.  
GeeksForGeeks c. 2025. Difference between NULL pointer, Null character ('\0') and '0' in C with Examples. Luettavissa: https://www.geeksforgeeks.org/c/difference-between-null-pointer-null-character-0-and-0-in-c-with-examples/. Luettu: 07.02.2026.  
Ghidra. 2025. Ghidra Software Reverse Engineering Framework. Luettavissa: https://github.com/NationalSecurityAgency/ghidra/tree/master. Luettu: 07.02.2026.  
Hammond, J. 2022. GHIDRA for Reverse Engineering (PicoCTF 2022 #42 'bbbloat'). Katsottavissa: https://www.youtube.com/watch?v=oTD_ki86c9I. Katsottu: 06.02.2026.  
Karvinen, T. 2026. Application hacking - 2026 Spring. Luettavissa: https://terokarvinen.com/application-hacking/#laksyt. Luettu: 07.02.2026.  
Li, V. 2020. Patching Binaries With Ghidra. Luettavissa: https://materials.rangeforce.com/tutorial/2020/04/12/Patching-Binaries/. Luettu: 07.02.2026.  
OpenJDK. s.a. OpenJDK. Luettavissa: https://openjdk.org/. Luettu: 07.02.2026.  
Stackoverflow. 2022. echo "#!" fails -- "event not found". Luettavissa: https://stackoverflow.com/a/11816138. Luettu: 07.02.2026.  
Stackoverflow. 2017. Is there a way to add an animated GIF to a Markdown file? Luettavissa: https://stackoverflow.com/a/42366350. Luettu: 07.02.2026.  
Tindall, L. 2019. Some Crackmes. Luettavissa: https://github.com/NoraCodes/crackmes. Luettu: 07.02.2026.

GIF: https://media1.tenor.com/m/hNj08SF198wAAAAd/cartoons.gif.
