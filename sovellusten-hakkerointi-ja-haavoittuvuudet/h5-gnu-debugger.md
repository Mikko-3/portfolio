# GNU Debugger

Tehtävänanto löytyy Moodlesta kurssin välilehdeltä GNU Debugger (Moodle, 2026).

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
G-optio lisää debug tiedot ohjelmaan (GCC, s.a.).
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



## Lähdeluettelo

https://gcc.gnu.org/onlinedocs/gcc/Debugging-Options.html
