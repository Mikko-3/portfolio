# Tiivistelmät
## Install Salt on Debian 13 Trixie
Tiivistelmä artikkelista: https://terokarvinen.com/install-salt-on-debian-13-trixie/

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
Tiivistelmä artikkelista: https://terokarvinen.com/2021/salt-run-command-locally/

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
- Cmd suorittaa komennon/komentoja.
- Ohjeet on saatavilla komennolla:

      sudo salt-call --local sys.state_doc

Kysymys: komentojen rakenne ei täysin auennut minulle, mitä -l optio tekee, sekä mitä info state.single tekee?

## Salt Quickstart
Tiivistelmä artikkelista: https://terokarvinen.com/2018/03/28/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/

- Mestarilla oltava julkinen IP tiedossa, sen ei tarvitse tietää missä orjat ovat.
- Master:
    - Asenna salt-master ja tarkista IP. Jos palomuuri käytössä, salli liikenne porteista 4505/tcp ja 4506/tcp.
- Slave:
    - Asenna salt-minion ja avaa /etc/salt/minion tiedosto.
    - Lisää mestarin IP osoite master kohtaan
    - Tallenna muutokset ja käynnistä salt-minion.service uudelleen.
- Hyväksy avain/avaimet mestarilla: sudo salt-key -A
- Testaa toiminta esim. sudo salt '*' cmd.run 'whoami' komennolla.

Kysymys: Mitä salt-key avaimet ovat? PGP avaimia vai jotain muuta?

## Raportin kirjoittaminen
Tiivistelmä artikkelista: https://terokarvinen.com/2006/06/04/raportin-kirjoittaminen-4/

- Kerro tarkkaan mitä teit ja mitä tapahtui, on parasta kirjoittaa samalla kun tekee.
- Raportin tulee olla toistettava, täsmällinen ja helppolukuinen.
    - Toisen henkilön olisi saatava sama tulos raporttia seuraamalla.
    - Mitä komentoja annoit, onnistuiko, mitä tapahtui sen jälkeen, millä testillä totesit tämän, mahd. virheilmoitukset.
    - Kirjoita imperfektissä, käytä väliotsikoita, kirjoita huolellista kieltä ja kanavaan sopivasti.
- Viittaa lähteisiin, älä sepitä tai plagioi. Merkitse lähteet selkeästi.

Kysymys: käytetäänkö libguides lähteiden merkintää (https://libguides.haaga-helia.fi/lahdeviittaamisen-tueksi/tekstiviitteet-ja-lahdeluettelo), vai riittääkö vapaamuotoisempi?

# Tehtävät
a) Ei ongelmia asennuksessa.

b) 


# Lähdeluettelo
