# H2 - Infraa koodina

## Tiivistelmät

Tiivistelmät artikkeleista, jotka listataan Palvelinten hallinta kurssin kotitehtävät osiossa h2 (Karvinen 2025).

### Hello Salt Infra-as-Code

- Uudet moduulit luodaan /srv/salt/ hakemistoon. (Esim. /srv/salt/hello/)
- /srv/salt/ hakemisto jaetaan orjille.
- Uuden moduulin hakemisto sisältää kaiken moduuliin liittyvän.
- init.sls tiedosto on lähtöpiste moduulin suorittamiselle.
- Suorita paikallinen käsky salt-call --local state.apply (moduulin nimi).
- Salt näyttää raportin, josta selviää mitä tapahtui ja onnistuiko komento.
- Kun ajat käskyn useammin, muutoksia ei pitäisi tapahtua.

(Karvinen 2024.)

### Salt Overview - YAML

- Salt käyttää tiedostoissaan YAML merkintäkieltä.
- YAML:ssa data kirjoitetaan "key: value" pareina. Keyn ja valuen erottaa aina ":" ja välilyönti.
- Kommentit alkavat "#" merkillä.
- Kolme eri elementtityyppiä:
  - Scalar (skalaari). Arvo voi olla numero, merkkijono tai boolean.
  - List (lista). Key, joka sisältää useampia arvoja. Arvot kirjoitetaan uudelle riville, sisennetään kahdella välilyönnillä ja aloitetaan väliviivalla ja välilyönnillä.
  - Dictionary (sanakirja). Kokoelma edellämainittuja arvoja.
- YAML toimii lohkoissa. Lohkojen konteksti määrittyy sisentämällä, kaksi välilyöntiä on standardi.

(VMware s.a.)

### The Top File - Introduction

- Infrastruktuurit muodostuvat usein useista ryhmistä koneita.
- Ryhmille voi määritellä eri konfiguraatioita roolien mukaan top file:ssa (top.sls).
- Top file:ssa on kolme osaa:
  - Enviroment (ympäristö). Kertoo hakemiston, josta state filet haetaan.
  - Target (kohde). Tietty ryhmä koneita, joihin state filet kohdistetaan.
  - State files (tilat). Listaus tiloista, jotka suoritetaan kohteisiin.
- Ympäristö sisältää kohteet, kohde sisältää tilat.

(VMware 2025.)

## Tehtävät

### a) Hei infrakoodi!

Seurasin "Hello Salt Infra-as-Code" artikkelin ohjeita tehdessäni tehtävää (Karvinen 2024).
Loin aluksi hakemiston '/srv/salt/helloinfra/'. Siirryin hakemistoon ja loin sinne 'init.sls' tiedoston komennolla "sudoedit init.sls".
Kirjoitin tiedostoon konfiguraation:

<img width="343" height="65" alt="Screenshot 2025-11-02 142418" src="https://github.com/user-attachments/assets/51c8b334-a5b1-4976-ad2f-3c73f7e86058" />

Eli file.managed luo tiedoston 'hello-infra.txt', '/tmp/' hakemistoon. Tiedoston sisällöksi annoin '- contents:' muuttujan avulla tekstin 'Hello World!'.
Tämän jälkeen testasin toiminnan ajamalla komennon "sudo salt-call --local state.apply helloinfra".
Salt ajoi komennon onnistuneesti ja loi tiedoston '/tmp/' hakemistoon.

<img width="431" height="290" alt="image" src="https://github.com/user-attachments/assets/74f6ccaf-763d-4e4d-b2a7-eb0caf4c2de4" />

Testasin vielä toisella ohjelmalla, että tiedosto on oikeasti olemassa ja sisältö on oikein. Käytin komentoja "ls /tmp/hello-infra.txt" ja "cat /tmp/hello-infra.txt".

<img width="295" height="56" alt="image" src="https://github.com/user-attachments/assets/77ec4d34-24e0-47fb-9502-af6709b1337c" />

Tiedosto löytyy ja sen sisältö on oikein.

### b) Topping

Siirryin takaisin '/srv/salt/' hakemistoon ja loin sinne 'top.sls' tiedoston komennolla "sudoedit top.sls".
Kirjoitin tiedostoon ympäristön ja kohteen kotitehtäväsivun vinkkien mukaisesti (Karvinen 2025).

```
base:
  '*':
    - helloinfra
```

Tiedostossa on vain yksi tila, 'helloinfra', koska minulla ei ole luotuna vielä enempää.  
Ajoin komennon "salt-call --local state.apply" testatakseni top filen toimivuuden.

<img width="419" height="239" alt="image" src="https://github.com/user-attachments/assets/1c947bb4-17e7-4953-9c73-c01ee6ae4133" />

Oikea tila suoritettiin, eli top file toimii.

### c) Viisikko tiedostossa.

Ensin loin hakemistot jokaiselle tilalle "sudo mkdir" komennolla. Nimesin hakemistot 'hello*', jossa tähden kohdalla tilafunktion nimi (pkg jne.).
Seuraavaksi siirryin hakemistoon 'hellopkg' ja loin tiedoston 'init.sls' "sudoedit init.sls" komennolla.
Toistin saman jokaiselle eri tilalle niiden omissa hakemistoissaan. Alla mitä kirjoitin tiedostoihin:

```
hellopkg/init.sls
micro:
  pkg.installed
-----------------------
hellofile/init.sls
/tmp/hello-file:
  file.managed
-----------------------
helloservice/init.sls
sshd:
  service.running
-----------------------
hellouser/init.sls
hacker:
  user.absent
-----------------------
hellocmd/init.sls
touch /tmp/foo/bar:
  cmd.run:
    - creates: /tmp/foo/bar
```

Luotuoani tiedostot, testasin toiminnan "salt-call --local state.apply (tilan nimi)" komennoilla. Pkg, file ja user tilat toimivat oikein, alla 'hellouser' suorituksen tulos:

<img width="477" height="224" alt="Screenshot 2025-11-02 162332" src="https://github.com/user-attachments/assets/0fe0bad9-ac56-4927-8088-c47f10fe9a84" />

Service tila antoi virheen, koska sshd ei ole asennettu, joten sen käynnistäminen ei onnistunut.

<img width="496" height="237" alt="image" src="https://github.com/user-attachments/assets/4460f389-c2cb-4424-9b49-ca6debcc8fa2" />

Cmd tilan komento ei onnistunut, vaan antoi virheen:

<img width="517" height="312" alt="Screenshot 2025-11-02 161004" src="https://github.com/user-attachments/assets/7e709ad5-f08f-4faf-9ff4-b5ab462cc1c8" />

En muistanut, että touch ei luo hakemistoja, jos niitä ei ole olemassa, joten muokkasin komentoa 'hellocmd/init.sls' tiedostossa muotoon "/tmp/foo".
Nyt tila suoritettiin onnistuneesti.

<img width="471" height="315" alt="Screenshot 2025-11-02 161040" src="https://github.com/user-attachments/assets/fc8dc7b3-b4b6-46c7-bb67-a4b0f920f130" />

Lisäsin tilat myös top fileen ja testasin toiminnan.

```
base:
  '*':
    - helloinfra
    - hellopkg
    - hellofile
    - helloservice
    - hellouser
    - hellocmd
```

"sudo salt-call --local state.apply" komennon tulos:

<img width="231" height="113" alt="image" src="https://github.com/user-attachments/assets/f7ab82fd-8ba2-42b1-b56d-50d3362aaac1" />

Vain sshd antoi virheen, kaikki muu toimi odotetusti.

### d) Kaksi eri tilafunktiota samassa tiedostossa

Loin hakemiston 'twostates' komennolla "sudo mkdir twostates" uutta tilaa varten.
Siirryin hakemistoon, tein uuden 'init.sls' tiedoston "sudoedit init.sls" komennolla ja kirjoitin sinne kaksi eri tilafunktiota.
Käytin file ja pkg tiloja.

```
/tmp/bar:
  file.managed
ddgr:
  pkg.installed
```

Testasin toiminnan ajamalla tilan "sudo salt-call --local state.apply twostates" komennolla.
Tila ajettiin onnistuneesti, ja molemmat tilafunktiot toimivat.

<img width="427" height="324" alt="image" src="https://github.com/user-attachments/assets/d11726cb-f227-41db-87a7-4e5d5e00729d" />

Tarkistin toiminnan myös toisella ohjelmalla, ensin komennolla "ls /tmp/bar". Tiedosto löytyi oikeasta hakemistosta.

<img width="330" height="28" alt="image" src="https://github.com/user-attachments/assets/2e6e17f9-f291-4274-8695-65564f142830" />

Tämän jälkeen testasin ddgr ohjelman toiminnan:

<img width="534" height="116" alt="image" src="https://github.com/user-attachments/assets/8f4c226c-8ead-4080-99b3-57b78a03d976" />

Lopuksi ajoin tilan useaan kertaan, idempotenssin testaamiseksi. Kolmen suorituskerran jälkeen, mikään ei ollut muuttunut, joten tila on idempotentti.

<img width="499" height="314" alt="image" src="https://github.com/user-attachments/assets/159906d9-143e-476c-873b-de7e641c59fc" />

# Lähdeluettelo

Karvinen, T. 2024. Hello Salt Infra-as-Code. Luettavissa: https://terokarvinen.com/2024/hello-salt-infra-as-code/ . Luettu: 02.11.2025.  
Karvinen, T. 2025. Palvelinten hallinta. Luettavissa: https://terokarvinen.com/palvelinten-hallinta/#h2-infraa-koodina . Luettu: 02.11.2025.  
VMware Inc. s.a. Salt overview. Luettavissa: https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml .
Luettu: 02.11.2025.  
VMware Inc. 2025. The Top File. Luettavissa: https://docs.saltproject.io/en/latest/ref/states/top.html . Luettu: 02.11.2025.  
