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

Tehtävänannot luettavissa Cryptopals sivustolta (Cryptopals s.a.).

### a) Convert hex to base64

Asensin ensin IPython tulkin `sudo apt-get install ipython3` ja lisäosan microon `micro --plugin install runit` (Karvinen 2024).
Avasin ipythonin ja hain internetistä komentoa, miten muuntaa hexakoodi base64 muotoon.
Seurasin löytämääni ohjetta (https://www.delftstack.com/howto/python/convert-hex-to-base64-python/) ja suoritin komennot ipythonissa.
Sain lopputulokseksi saman tekstin kuin cryptopals tehtävänannossa.

<img width="942" height="133" alt="image" src="https://github.com/user-attachments/assets/54a0149c-7cfe-45a7-9086-db08e098c55d" />

Kirjoitin koodin lopuksi vielä tiedostoon `c1set1.py`.

```
import codecs

hex_string = "49276d206b696c6c696e6720796f757220627261696e206c696b65206120706f69736f6e6f7573206d757368726f6f6d"
b64_string = codecs.encode(codecs.decode(hex_string,"hex"),"base64").decode()

print(b64_string)
#print("SSdtIGtpbGxpbmcgeW91ciBicmFpbiBsaWtlIGEgcG9pc29ub3VzIG11c2hyb29t") #tehtävän oikea vastaus
```

### b) Fixed XOR

Kopioin aikaisemman `c1set1.py` tiedoston `cp c1set1.py c2set1.py` ja muokkasin `hex_string` muuttujaan tehtävänannon hexan.
Tämän jälkeen kommentoin `b64_string` muuttujan ja print funktion ja lisäsin `decoded_string` muuttujan, joka purkaa hexan.

```
**Kommentoidut koodit poistettu selkeyden vuoksi**
import codecs

hex_string = "1c0111001f010100061a024b53535009181c"
decoded_string = codecs.decode(hex_string,"hex")

print (decoded_string)
```

<img width="795" height="126" alt="image" src="https://github.com/user-attachments/assets/cb4efa7d-f165-44da-bb01-11b10278e2c6" />

Varmistettuani tämän osan koodista toimivan, siirryin seuraavaan kohtaan tehtävässä.
Hain tietoa, miten pythonissa suoritetaan XOR funktio kahdelle stringsille ja löysin artikkelin (https://medium.com/@amit25173/how-to-xor-strings-in-python-a-beginner-friendly-guide-17b23aa8728f).
Minun piti siis muuttaa teksti ascii muotoon, mutta kohtasin ongelman:

<img width="1096" height="123" alt="image" src="https://github.com/user-attachments/assets/ad800f2a-e6e1-43d0-af85-6e08cb5693e1" />

Päätin aloittaa "puhtaalta pöydältä" ja seurata löytämäni artikkelin ohjeita kohdasta 5.2 (Medium).
Ongelmana oli vielä muuntaa hexa ascii muotoon, sillä omat stringsini eivät olleet tekstiä.
Löysin siihenkin onneksi ohjeet (https://www.delftstack.com/howto/python/hex-to-ascii-python/).
Testailtuani koodia, sain tulokseksi tämän:

<img width="412" height="162" alt="image" src="https://github.com/user-attachments/assets/79f3ed84-9dbb-44ad-aed1-e663a492bf76" />

Tajusin sitten, että vastaus oli myös hexa muodossa, joten muunsin tekstin takaisin hexaksi (https://pythonguides.com/convert-string-to-hexadecimal-in-python/).
Lopullinen koodi:

```
def xor_strings(string1,string2):
    result = ''.join(chr(ord(m) ^ ord(k))for m, k in zip(string1, string2))
    #zip yhdistää tekstit yhdeksi tupleksi, for silmukka käy kirjain kirjaimelta läpi molemmat tuplen sanat, ord muuttaa kirjaimet ascii numeroksi, jotta XOR voidaan suorittaa, ja chr muuntaa numerot takaisin kirjaimiksi (Medium).
    print(zip(string1, string2)) #print debuggausta.
    return result

message = "1c0111001f010100061a024b53535009181c"
key = "686974207468652062756c6c277320657965"

byte_message = bytearray.fromhex(message) #hexakoodit muutetaan bytearrayksi, jotta decode funktio voi käyttää sitä.
byte_key = bytearray.fromhex(key)

print(f"{byte_message}\n{byte_key}") #print debuggausta.

ascii_message = byte_message.decode('ascii') #muunnetaan bytearrayt tekstiksi ascii muodossa.
ascii_key = byte_key.decode('ascii')

print(ascii_message) #print debuggausta.
print(ascii_key) #print debuggausta.

xor_result = xor_strings(ascii_message,ascii_key) #kutsutaan xor funktiota.

print(xor_result) #tulos tekstimuodossa.

byte_result = xor_result.encode() #muutetaan teksi bytearrayksi.
hex_result = byte_result.hex() #muutetaan bytearray hexaksi.

print(hex_result) #tulos
print("746865206b696420646f6e277420706c6179") #tehtävän vastaus, vertailua helpottamassa.
```

<img width="939" height="223" alt="image" src="https://github.com/user-attachments/assets/c0364d88-f8b9-456a-88d7-7908537f051f" />

Hexat vastaavat toisiaan, joten ongelma on ratkaistu. Tuntuu, että tämän olisi voinut toteuttaa yksinkertaisemminkin, mutta en osaa sanoa miten.

### c) Single-byte XOR cipher

