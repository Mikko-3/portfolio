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
  - State files. Listaus state file tiedostoista, jotka suoritetaan kohteisiin.
- Ympäristö sisältää kohteet, kohde sisältää state filet.

(VMware 2025.)

## Tehtävät

### a) Hei infrakoodi!



# Lähdeluettelo

