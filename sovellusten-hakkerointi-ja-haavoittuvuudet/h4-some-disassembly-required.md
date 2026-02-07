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

