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
