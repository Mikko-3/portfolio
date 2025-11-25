# h5 - Toimiva versio

## Tiivistelmät

Tiivistelmät artikkeleista, jotka löytyvät kurssin tehtäväsivulta kohdasta h5 (Karvinen 2025).

### What is Git?

- Git tallentaa versiot kokonaisuuksina, ei vain yksittäisinä muutoksina.
- Git käyttää checksumeja, jotta muutoksia ei voi tapahtua huomaamatta.
- Kolme tilaa:
  - Modified
  - Staged
  - Committed

(Chacon & Straub 2014.)

### Gitin käyttö

- Git add: Lisää muokatut tiedostot staged tilaan, jolloin ne commitataan seuraavalla commitilla.
- Git commit: Lisää muutokset repoon. Muista commit message.
- Git pull: Lataa muutokset reposta omalle koneellesi.
- Git push: Päivittää tekemäsi muutokset repoon verkon yli.

(Gitlab s.a.)

### Suolax historia

- Luotu repo ja sinne lisenssi ja readme tiedosto.
- Korjattu readme tiedostoon otsikko isolla alkukirjaimella.
- Lisätty ensimmäinen moduuli ja asennusohjeet readme tiedostoon.
- Lisätty makefile.
- Muokattu makefilea ja lisätty uusi moduuli, joka asentaa ohjelman tree.
- Lisätty lisää ohjelmia luotuun moduuliin, sekä lisätty tila topfileen ja muokattu makefilea ajamaan topfile.
- Muokattu readme:ta ja makefilea.
- Paranneltu readme kirjoitetut asennusohjeet.

(Karvinen 2024.)

## Tehtävät

### a) Online

Loin uuden github varaston nimellä "test-snow-repo". Lisäsin valikosta lisenssin (GNU GPL 3) ja readme tiedoston.
Linkki repoon: https://github.com/Mikko-3/test-snow-repo

<img width="472" height="461" alt="Screenshot 2025-11-25 104748" src="https://github.com/user-attachments/assets/8d2d4aba-5957-424d-b59b-438cd3b2aeb4" />

<img width="353" height="300" alt="image" src="https://github.com/user-attachments/assets/b15fdac3-fd6c-4ab0-b96b-7ccd4a404877" />

### b) Dolly

Kloonasin ensin repon käyttäen SSH:ta. Olin luonut ja liittänyt ssh avaimen jo aikaisemmin githubiin, joten tätä ei tarvinnut tehdä uudelleen.
Komento "git clone git@github.com:Mikko-3/test-snow-repo.git" kloonasi repon.

<img width="473" height="162" alt="Screenshot 2025-11-25 110254" src="https://github.com/user-attachments/assets/3ea334e7-8639-4358-880c-0ba103523ea5" />

Tämän jälkeen muokkasin readme.md tiedostoa, lisäämällä sinne tekstiä.
Sitten ajoin komennot "git add ." ja "git commit" tallettaakseni muutokset paikallisesti.

<img width="332" height="63" alt="image" src="https://github.com/user-attachments/assets/4dfd3da1-ce79-4033-b175-510fbee477e7" />

<img width="405" height="189" alt="image" src="https://github.com/user-attachments/assets/fe585116-7ddc-4480-8f11-b85a14da7f2e" />

Lopuksi ajoin komennon "git push" ladatakseni muutokset verkon yli palvelimelle.

<img width="445" height="125" alt="image" src="https://github.com/user-attachments/assets/8926c291-7f9d-4d2f-bdca-74e87179294d" />

Jos repolla olisi muita muokkaajia kuin minä, olisi "git pull" järkevää ajaa ennen pushia, jotta minulla olisi viimeisimmät muiden tekemät muutokset.
Näin voi välttyä saman rivin muokkaamiselta ym. ongelmilta.  
Tarkistin vielä selaimella, että muutokset tulivat näkyviin verkkoliittymään.

<img width="567" height="229" alt="image" src="https://github.com/user-attachments/assets/6ae5e25c-661a-46c4-a55b-f39fb2ae8c4c" />

### c) Doh!

Tein readme.md tiedostoon hölmön muutoksen.

<img width="329" height="34" alt="image" src="https://github.com/user-attachments/assets/8e3ac4a8-ab37-4262-840b-ba1e0762219e" />

En commitanut muutoksia, joten alkuperäinen tiedosto on tallella repossa.
Komennolla "git reset --hard" palautin edellisen version reposta, jossa readme.md oli ennallaan.

<img width="345" height="89" alt="image" src="https://github.com/user-attachments/assets/bc50c8d0-02ab-4382-9df2-59003f89bbb9" />

### d) Tukki

Avasin lokin komennolla "git log --patch --color|less -R".

<img width="368" height="340" alt="image" src="https://github.com/user-attachments/assets/4f0dcf1b-1c02-427d-b3a5-d6da91239485" />

Loki näyttää ensimmäisenä viimeisimmän muutoksen.
Ensimmäisenä näkyy commitin checksum rivillä commit.
Tämän jälkeen on muutoksen tekijän tiedot, tässä tapauksessa oma nimeni ja sähköpostiosoitteeni.
Seuraavaksi päiväys milloin muutos on tehty.
Lopuksi eroavaisuudet edelliseen tiedostoon verrattuna, jossa '+' merkeillä ja vihreällä värillä näkyy lisätyt asiat ja '-' merkeillä ja punaisena poistetut.
Muuttumattomat asiat ovat ilman merkkejä valkoisella.
--color optio lisää värit teksteihin, muuten kaikki olisi valkoisella ja muutokset erottaisi vain edellä mainituista merkeistä.

<img width="372" height="193" alt="image" src="https://github.com/user-attachments/assets/41bb47ee-6e93-4750-b950-38cb401826ae" />

Loppu loki on vain ensimmäinen commit, kun repo luotiin ja kaikki lisenssin ja readmen sisältämä teksti.

### e) Suolattu rakki

