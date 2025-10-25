# Tiivistykset
## Install Salt on Debian 13 Trixie
- Uuden repon asentamiseen tarvitaan kaksi asiaa: PGP julkinen avain ja sources.list.
- Lataa nämä tiedostot Salt virallisien ohjesivujen mukaisista paikoista.
- Kopioi tiedostot tarvittaviin paikkoihin
  - PGP avain: etc/apt/keyrings/salt-archive-keyring.pgp
  - Salt.sources: /etc/apt/sources.list.d/
- Päivitä apt listat ja asenna salt-minion tai salt-master.
- Testaa toiminta jollain paikallisella komennolla.

Huomioita: uuden repon asentaminen vaatii paljon luottoa. Kun olet asentanut PGP avaimen, järjestelmä luottaa lähteeseen ja voit asentaa sieltä mitä tahansa ohjelmistoa.
Jos lähde ei ole luotettava, voit mahdollisesti asentaa haittaohjelmia.

## Run Salt Command Locally
- Paikallisesti voi harjoitella komentoja ja testata komentojen toimivuutta.
- Tärkeimmät Salt komennot ovat:
  - pkg (.installed, .removed)
  - file (.managed, .absent)
  - service (.running, .dead)
  - user (.present, .absent)
  - cmd (.run)
- Pkg kertoo, että ohjelma on oltava asennettuna.
- File kertoo, että tiedosto on oltava jossain.
- Service kertoo, että prosessin (daemonin) on oltava käynnissä.
- User kertoo, että käyttäjän on oltava olemassa.
- Cmd suorittaa komennon/komentoja. Komentojen tulisi suorittua vain, jos muutoksia on tarpeen tehdä (idempotenssi).
- Ohjeet on saatavilla komennolla:

      sudo salt-call --local sys.state_doc

Kysymys: komentojen rakenne ei täysin auennut minulle, mitä -l optio tekee, sekä mitä info state.single tekee?

## Salt Quickstart
- 
