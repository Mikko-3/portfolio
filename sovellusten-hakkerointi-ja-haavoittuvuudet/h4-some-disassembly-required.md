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
Huomasin sitten tekstin "00105870	$Info: This file is packed with the UPX executable packer http://upx.sf.net" ja muistin, että edellisessä tehtävässä pakkaus esti binäärin kunnollisen tutkimisen.
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
If-lause tarkistaa, onko "vertaus_palautus" = 0, jonka jälkeen se tulostaa tekstin "Yes! That's the password. FLAG{Tero-0e3bed0a89d8851da933c64fefad4ff2}" paljastaen lipun, jos luku oli 0.
If-lause palauttaa tekstin "Sorry, no bonus.", jos "vertaus_palautus" ei ole 0.
Tämän jälkeen funktio palauttaa nollan, ja ohjelma sulkeutuu.

## c) If backwards

