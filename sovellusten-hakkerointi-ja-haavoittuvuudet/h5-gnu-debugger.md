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
Tutkin aikaisemmin löytämääni tietoa segmentation faultista ja huomasin kohdan "A common way to get a segfault is to dereference a null pointer:" (Stackoverflow 2020).
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



## Lähdeluettelo

https://gcc.gnu.org/onlinedocs/gcc/Debugging-Options.html
https://stackoverflow.com/a/2346849
2022 https://stackoverflow.com/a/4009070
