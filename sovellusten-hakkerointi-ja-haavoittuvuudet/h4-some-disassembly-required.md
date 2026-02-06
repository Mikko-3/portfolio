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
