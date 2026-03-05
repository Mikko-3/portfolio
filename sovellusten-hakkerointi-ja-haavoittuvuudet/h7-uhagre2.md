# h7 Uhagre2

Tehtävänanto löytyy terokarvinen.com sivustolta (Karvinen 2026).

## x) Tiivistelmät

### Applied Cryptography, 20ed

**1.1 Terminology**

- Kappale käy läpi eri termejä, joita kryptografiassa käytetään.
- Plaintext (salaamaton viesti), encryption (salaus), ciphertext (salattu viesti), decryption (salauksen purkaminen).
- Kryptografia tarjoaa myös muita ominaisuuksia, kuten:
    - Authentication: viestin vastaanottaja voi varmistua lähettäjästä.
    - Integrity: vastaanottaja voi varmistua, ettei viestiä ole muutettu matkalla.
    - Nonrepudiation: lähettäjä ei voi kieltää myöhemmin lähettäneensä viestin.
- Cipher = matemaattinen funktio viestin salaamiseksi ja salauksen purkamiseksi.
- Modernit salaukset ovat useimmiten avoimia ja perustuvat julkisen ja salaisen avaimen periaatteeseen.
- Kun salaus on avoin, sen toimintaa voi tarkastella, arvostella ja mahdollisesti korjata.

**1.4 Simple XOR**

- XOR eli exclusive-or operaatio
- Bittioperaatio, jossa bitti asettuu arvoon 1, jos vain toinen niistä on arvoltaan 1.
- 0 ^ 0 = 0, 0 ^ 1 = 1, 1 ^ 0 = 1, 1 ^ 1 = 0
- Symmetrinen algoritmi, eli XOR operaation suorittaminen samalle arvolle kahdesti palauttaa alkuperäisen.
- Ei turvallinen, helppo purkaa

**1.7 Large numbers**

- Kirjassa (ja kryptografiassa) luvut ovat todella suuria, ja niitä voi olla vaikea hahmottaa
- Taulukko, jossa luvuille kerrotaan fyysinen vastike ja esitetään se eri muodossa.
- Esimerkki taulukon luvusta:
    - Odds of being killed by lightning (per day)	1 in 9 billion (2<sup>33</sup>).

(Schneier 2015.)

### Python Basics for Hackers

- Neuvoja ja ohjeita pythonin hyödyntämiseen hakkeroinnissa
- Aloita pienistä osista, helpottaa virheiden huomaamista ja toiminnan varmistamista.
- Asioita on helppo kokeilla REPL:n (Read-eval-print loop) avulla.
- Pythonin oma REPL saa käyttöön komennolla `python3`.
- IPython tarjoaa lisäominaisuuksia, kuten komentohistorian ja komentojen täytön.
- Voit asentaa micro editoriin lisäosan `runit`, jonka avulla voit ajaa koodia suoraan editorista F5 näppäimellä.
- Nopeampaa, kuin tiedoston tallentaminen ja suorittaminen erikseen.
- Python on kätevä laskin, kuten potenssin (power) ja jakojäännöksen (modulus) kanssa.
- Voit myös muuntaa kirjaimia numeroiksi ja toisinpäin (`ord("")` ja `chr()`).
- Numeroita voi muuntaa myös muihin muotoihin: hexaksi `hex()`, binääriksi `bin()` ja oktaaliseksi `oct()`.
- F string tulostaa helposti muuttujien arvoja tulostuksiin `print(f"teksti"{muuttuja})`.
- Print debuggaus on nopea ja helppo tapa testata koodia, kirjoita `print()` kohtia koodiin, jossa haluat tarkistaa asioita.

(Karvinen 2024.)

## Solve CryptoPals Set 1 challenges (a-d)

