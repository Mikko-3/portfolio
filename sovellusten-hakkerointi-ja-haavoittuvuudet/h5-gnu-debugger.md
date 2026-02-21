# GNU Debugger

Tehtävänanto löytyy Moodlesta kurssin välilehdeltä GNU Debugger (Moodle, 2026).
Tehtäviä tehdessäni en avannut lähdekoodia ennen, kuin olin selvittänyt ohjelman toiminnan debuggerilla.

## Main.cpp

Latasin Moodlesta kansion "Dynaaminen analyysi" ja purin sen `unzip` komennolla.
Tämän jälkeen seurasin ohjetta "01-GDB.pdf" tiedostosta `main.cpp` kääntämiseksi.
Suoritin komennon `g++ main.cpp -g -Wall -Werror -o main-dbg` ja ohjelma käännettiin debug tiedoilla. Tämä auttaa suorittamisen seurannassa.

Seuraavaksi tutkin ohjelmaa GNU Debuggerilla.
Avasin ohjelman komennolla `gdb ./main-dbg`.
Lisäsin keskeytyspisteen main -funktioon komennolla `break main`.
Tämän jälkeen suoritin ohjelman komennolla `run` ja kävin ohjelman toiminnan läpi askeleittain komennolla `step`.
Kun ohjelma asetti muuttujia, suoritin komennon `watch (muuttujan nimi)` seuratakseni muuttujien arvoa.
Kuten ohjeessa mainittiin, factorial -funktion logiikassa on virhe, jossa n-muuttujan arvoa vähennetään jo while silmukan alussa.
Tämä johtaa siihen, ettei laskutoimitusta suoriteta aluksi alkuperäisellä arvolla, vaan arvo -1 ja lopussa muuttujan arvo on 0, joten kertolaskun tulos on aina 0.

<img width="401" height="505" alt="image" src="https://github.com/user-attachments/assets/cc352e56-50ba-4031-a761-2814fb6c6680" />

<img width="412" height="293" alt="image" src="https://github.com/user-attachments/assets/c6fe12e9-17ea-415a-a677-673dc7e1f8ba" />

Korjasin silmukan logiikan lähdekoodissa ja käänsin ohjelman uudelleen.

Alkuperäinen lähdekoodi:

```
#include<iostream>
      
using namespace std;
      
long factorial(int n);
      
int main()
{
    int n(0);
    cin>>n;
    long val=factorial(n);
    cout<<val;
    cin.get();
    return 0;
}
      
long factorial(int n)
{
    long result(1);
    while(n--)
    {
        result*=n;
    }
    return result;
}
```

Muutettu while silmukka:

```
long factorial(int n)
{
    long result(1);
    while(n > 0)
    {
        result*=n;
        n--;
    }
    return result;
}
```

Nyt n-muuttujaa vähennetään vasta silmukan lopussa, eikä tulosta kerrota nollalla.
Testasin ohjelman toiminnan varmistaakseni, että olin korjannut ongelman.
Syötin n-muuttujan arvoksi 5.

<img width="713" height="687" alt="image" src="https://github.com/user-attachments/assets/afed34b9-647b-411e-aff3-3405b6fc3a2e" />

Nyt result-muuttuja kerrotaan ensimmäisellä kierroksella arvolla 5, joten se osa ohjelmasta toimii oikein.
Lopputulokseksi tuli 120, joka oli oikein, tarkistettuani laskutoimituksen laskimella.

<img width="596" height="323" alt="image" src="https://github.com/user-attachments/assets/d964da0a-325b-4a6d-b5fc-a8327bac128a" />

## Lab 0

Purin kansion ja ajoin ohjelman "buggy_program".
Ohjelma tulosti elementtien arvoja.

<img width="170" height="154" alt="image" src="https://github.com/user-attachments/assets/90b4c9af-b728-4b24-bfb1-629d4bfa6d33" />

Koska lähdekoodi oli saatavilla, käänsin sen uudelleen debug tiedoilla.
Ohjelma oli kirjoitettu C-kielellä, joten käänsin sen käyttäen GCC:tä `gcc buggy_program.c -g -o buggy_program_dbg`.
G-optio lisää debug tiedot ohjelmaan (GCC s.a.).
Tämän jälkeen avasin ohjelman GNU Debuggerissa komennolla `gdb ./buggy_program_dbg`.
Loin keskeytyspisteen `break main` ja ajoin ohjelman `run`.
Kävin askel askeleelta ohjelman toiminnan läpi.
Ohjelma tulostaa elementtejä 0-5 ja arvot numbers-muuttujasta (array).

<img width="1134" height="334" alt="image" src="https://github.com/user-attachments/assets/d92fe769-dfdf-4a7b-b81d-f0667dc67a65" />

Debug tietojen avulla, myös kommentit näkyvät koodissa.
Viimeisessä elementissä, kun i:n arvo on 5, ei numbers-muuttujassa ole sillä indeksillä dataa, joten ohjelma tulostaa arvoksi 0.
Kommentteja hyödyntäen, ajattelin, että tarkoitus olisi tulostaa vain numbers-muuttujan arvot, eli elementit 0-4.
Kun size-muuttujalle annetaan koko 5, joka on kommentin mukaan väärä, voisi ohjelman korjata muuttamalla size:n arvoksi 4.
Näin ohjelma tulostaisi vain indeksit 0-4, joissa arvot sijaitsevat.
Korjasin arvon lähdekoodiin ja käänsin ohjelman uudelleen `gcc buggy_program_fix.c -g -o buggy_program_fix_dbg`.

Alkuperäinen lähdekoodi:

```
#include <stdio.h>

void buggy_function(int *arr, int size) {
    for (int i = 0; i <= size; i++) { // Huomaa: <= aiheuttaa puskuriylivuodon
        printf("Element %d: %d\n", i, arr[i]);
    }
}

int main() {
    int numbers[] = {1, 2, 3, 4, 5};
    buggy_function(numbers, 5); // Virheellinen koko
    return 0;
}
```

Muutettu arvo funktiokutsuun:

```
int main() {
    int numbers[] = {1, 2, 3, 4, 5};
    buggy_function(numbers, 4); // Vaihdettu arvoksi suurin indeksi (0-4)
    return 0;
}
```

Nyt ohjelma tulostaa vain arvot numbers-muuttujasta:

<img width="736" height="153" alt="image" src="https://github.com/user-attachments/assets/a544f5c9-9722-4275-add0-649e4456106f" />

Nyt ohjelma toimii mielestäni oikein.

## Lab 1

Purin lab1.zip kansion ja ajoin "gdb_exmple1" ohjelman.
Ohjelma tulosti jonkinlaista tekstiä ja "Segmentation fault" virheilmoituksen.

<img width="620" height="76" alt="image" src="https://github.com/user-attachments/assets/a2f3a997-fca3-4de3-9fbe-b6b71e7e6b17" />

Tutkin internetistä tietoa segmentation faultista, ja ymmärsin sen liittyvän muistiin ja ohjelman yrittävän käyttää muistia, johon sillä ei ole pääsyä (Stackoverflow 2020).
Lähdekoodi oli saatavilla, joten käänsin ohjelman uudelleen debug tiedoilla `gcc gdb_example1.c -g -o gdb_example1-dbg`.
Sen jälkeen avasin ohjelman GNU Debuggerissa `gdb ./gdb_example1-dbg`.
Loin breakpointin main funktioon `break main`, ajoin ohjelman `run` ja kävin ohjelman toiminnan läpi aseleittain `s`.

Ohjelma luo kaksi merkkijonoa, `bad_message` ja `good_message`. Muuttujien arvot ovat `NULL` ja `Hello, world.`.
Tämän jälkeen kutsutaan funktiota `print_scrambled` muuttujalla `good_message`.
Funktio tallettaa `good_message` muuttujan arvon `message` muuttujaan.
Sen jälkeen se luo muuttujan `i` arvolla 3 ja aloittaa while -silmukan.
While -silmukka käy `message` muuttujan läpi indeksi kerrallaan ja tulostaa sen arvon, lisäten siihen ensin `i`:n.
Lopuksi se tulostaa rivinvaihdon `\n`.
Lopputuloksena on tulostettu teksti `Khoor/#zruog1`, joka muodostuu, kun tekstin `Hello, world.` jokaista merkkiä siirretään kolmella eteenpäin.

Sen jälkeen ohjelma kutsuu samaa funktiota uudelleen muuttujalla `bad_message`.
Tämän jälkeen ohjelma kohtaa virheen `SIGSEGV` eli segmentation fault ja sulkeutuu.
Virhe liittyi selvästi `bad_message` muuttujaan, sillä funktio toimi toisella muuttujalla ongelmitta.
Tutkin aikaisemmin löytämääni tietoa segmentation faultista ja huomasin kohdan "A common way to get a segfault is to dereference a null pointer:" (Stackoverflow a. 2020).
Koska `bad_message` muuttujan arvo on `NULL`, tutkin lisää "dereference a null pointer" hakusanoilla.
Löysin toisen Stackoverflow ketjun aiheesta, ja yhdessä vastauksessa selitettiin miksi tämä tuottaa segmentation faultin:

> A null pointer is a pointer that does not point to any valid data (but it is not the only such pointer). The C standard says that it is undefined behavior to dereference a null pointer. This means that absolutely anything could happen: the program could crash, it could continue working silently, or it could erase your hard drive (although that's rather unlikely).
>
>In most implementations, you will get a "segmentation fault" or "access violation" if you try to do so, which will almost always result in your program being terminated by the operating system.

(Stackoverflow 2022.)

Ohjelman suoritus keskeytetään, koska `bad_message` muuttujan arvo on tyhjä, eikä kyseistä muistisijaintia ole olemassa.
Täten ohjelman voisi korjata antamalla muuttujalle jokin muu arvo kuin `NULL`.
Avasin lähdekoodin ja korjasin sinne muuttujan arvon.

Alkuperäinen lähdekoodi:

```
#include "stdio.h"

void print_scrambled(char *message)
{
  register int i = 3;
  do {
    printf("%c", (*message)+i);
  } while (*++message);
  printf("\n");
}

int main()
{
  char * bad_message = NULL;
  char * good_message = "Hello, world.";

  print_scrambled(good_message);
  print_scrambled(bad_message);
}
```

Muokattu muuttujan arvo:

```
int main()
{
  char * bad_message = "Goodbye, world."; //muokattu arvo
  char * good_message = "Hello, world.";

  print_scrambled(good_message);
  print_scrambled(bad_message);
}
```

Käänsin ohjelman uudelleen `gcc gdb_example1-fix.c -g -o gdb_example1-fix` ja suoritin ohjelman `./gdb_example1-fix`.
Nyt ohjelma ei kohdannut ongelmaa suorituksen aikana.

<img width="670" height="73" alt="image" src="https://github.com/user-attachments/assets/9bdb233f-6aca-4a29-a788-c476be8003aa" />

Kokeilin vielä jättää `bad_message` muuttujan tekstin tyhjäksi: `char * bad_message = ""; //tyhjä teksti`.
Käänsin ohjelman ja suoritin sen uudelleen.
Nyt ohjelma tulosti saman tekstin kahteen kertaan.

<img width="199" height="78" alt="image" src="https://github.com/user-attachments/assets/b0cbc183-df03-4b18-9ae3-abd682447718" />

Tutkin ohjelman suoritusta uudelleen debuggerilla.
Debugger ei antanut virheilmoituksia tai muuta, ja ohjelma suoritti silmukan tyhjällä tekstillä identtisesti `Hello, world.` tekstin kanssa.
En tiedä mistä tämä johtui, mutta ehkä se liittyy jotenkin vierekkäisiin muistiosoitteisiin?
`print_scrambled (message=0x555555556005 "Hello, world.")` `print_scrambled (message=0x555555556004 "")`

En jäänyt tutkimaan asiaa sen enempää ja siirryin seuraavaan tehtävään.

## Lab 2

Purin kansion "lab2.zip" ja avasin README.md tiedoston.
Tehtävän tarkoituksena on löytää lippu "passtr2o" ohjelmasta käyttäen debuggeria.
Passtr2o ohjelmaan ei ole saatavilla lähdekoodia, joten avasin ohjelman suoraan debuggerissa `dbg ./passtr2o`.
Loin breakpointin ja aloin tutkimaan ohjelman toimintaa.
Ilman debug tietoja, debugger ei näyttänyt kuin ohjelman tulostaman tekstin.

<img width="965" height="412" alt="image" src="https://github.com/user-attachments/assets/1feb9902-f4a8-498c-a8da-e7cf92a3ac1a" />

Annoin gdb:lle komennon `layout asm`, jolloin sain näkyviin assembler koodin.
Tutkin koodista, näkyisikö siellä salasanaa tai lippua, mutta en löytänyt kumpaakaan.

Tutkin internetistä, miten debugata ilman lähdekoodia, ja löysin ohjeita Stackexchange ketjusta (Stackexchange 2023).
Annoin gdb:lle komennon `set disassembly-flavor intel` vaihtaakseni assembler koodin hieman helpommin luettavaan muotoon.
Annoin myös komennon `set disassemble-next-line on` jotta gdb kertoo tekstinä, missä kohdassa koodia se menee.
Kävin koodia läpi `ni` komennolla.
Ohjelma jäi useaksi stepiksi kohtaan `main+62`, jossa oletan että ohjelma teki jotain useamman kerran.

<img width="1033" height="32" alt="image" src="https://github.com/user-attachments/assets/c4092bdb-1188-4ecc-974b-558efc2b0def" />

Hain tietoa `rep movs` ohjeista ja sain selville, että `rep` toistaa operaation ja `movs` kopioi dataa kohdasta `es:[rdi]` kohtaan `ds:[rsi]`(Stackoverflow b. 2020).
En vielä täysin ymmärtänyt mitä tämä tarkoitti, mutta jatkoin tutkimista.
Myöhemmin ohjelma kutsuu `puts` funktiota, joka tulostaa tekstin "What's the password?".
Tämän jälkeen kutsutaan `scanf` funktiota, joka odottaa käyttäjän syötettä ja oletettavasti tallentaa sen muuttujaan.
Sitten ohjelma kutsuu funktiota `mAsdf3a`, jossa suoritetaan `dec` ohje, joka vähentää yhden muuttujasta `eax`(felixcloutier.com 2023).
Tämän jälkeen tehdään `xor` vertailua, jonka tuloksena hypätään seuraavaan kohtaan, jos tulos ei ole yhtä suuri (`jne`).
Kirjoittamani salasanan (testi) johdosta, ohjelma hyppää ulos vertailusta, ja kutsuu `printf` funktiota, joka tulostaa tekstin "Sorry, no bonus.".
Salasana oli siis väärä.

En tiedä ymmärsinkö ohjelman toimintaperiaatteen oikein, mutta itseä kiinnosti alun `rep movs`, joka kopioi jotain dataa paikasta toiseen.
Ajattelin, olisiko tämä salasana, joka on obfuskoitu?
Jos saisin muistin sisällön tietoon, kun ohjelma suorittaa funktiota, voisin saada salasanan selville ja saada lipun ohjelmasta.
Koetin lukea muistin sisältöä `x/` komennolla, mutta en saanut ulos mitään, mikä olisi vastannut kirjainta tai merkkiä.

<img width="1270" height="133" alt="image" src="https://github.com/user-attachments/assets/daa7e844-2753-4992-ae42-251d5f31ae4d" />

Löysin internetistä sivun, jolla kerrottiin C-kielen käyttävän oletuksena UTF-16 merkistöä, jos ajaa komennon `s` formaatilla (Sourceware s.a.).
Suoritin komennon `x/s $rsi`, mutta tuloksena oli vain sekasotkua, josta ei UTF-16 merkistöstä löytynyt mitään.

En lopuksi keksinyt enää keinoja selvittää salasanaa ohjelmasta, joten luovutin tehtävän tekemisen.
Voi olla, että olin oikeilla jäljillä etsiessäni salasanaa `rep movs` kohdasta, mutta on myös hyvin mahdollista, etten ollut lähimaillakaan oikeaa ratkaisua.
¯\_(ツ)_/¯

## Lähdeluettelo

Felixcloutier.com 2023. DEC — Decrement by 1. Luettavissa: https://www.felixcloutier.com/x86/dec. Luettu: 21.02.2026.  
GCC s.a. 3.11 Options for Debugging Your Program. Luettavissa: https://gcc.gnu.org/onlinedocs/gcc/Debugging-Options.html. Luettu: 21.02.2026.  
Iso-Anttila, L. & Karvinen, T. s.a. GNU Debugger. Sovellusten hakkerointi ja haavoittuvuudet -opintojakson tehtävänanto Moodlessa. Haaga-Helia ammattikorkeakoulu. Luettu: 21.02.2026.  
Stackexchange 2023. How to handle stripped binaries with GDB? No source, no symbols and GDB only shows addresses? Luettavissa: https://reverseengineering.stackexchange.com/a/1936. Luettu: 21.02.2026.  
Stackoverflow 2022. What exactly is meant by "dereferencing a NULL pointer"?. Luettavissa: https://stackoverflow.com/a/4009070. Luettu: 21.02.2026.  
Stackoverflow a. 2020. What is a segmentation fault?. Luettavissa: https://stackoverflow.com/a/2346849. Luettu: 21.02.2026.  
Stackoverflow b. 2020. Assembly: REP MOVS mechanism. Luettavissa: https://stackoverflow.com/a/27804935. Luettu: 21.02.2026.  
Sourceware s.a. 10.6 Examining Memory. https://sourceware.org/gdb/current/onlinedocs/gdb.html/Memory.html. Luettu: 21.02.2026.
