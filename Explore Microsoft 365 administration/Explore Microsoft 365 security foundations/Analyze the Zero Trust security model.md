# Analyze the Zero Trust security model

- **Zero Trust security model** = Tietoturvamalli, jonka perusajatus on: "Älä luota automaattisesti käyttäjiin tai laitteisiin, vaan tarkista / todenna jokainen pyyntö riippumatta siitä, tuleeko se organisaation sisä- vai ulkopuolelta. Zero Trust -mallissa ei oleteta täydellistä suojausta, vaan **varaudutaan siihen, että tietomurto voi tapahtua milloin tahansa.**
- **MFA** = Multifactor Authentication / Monivaiheinen tunnistautuminen
- **RBAC** = Role-Based Access Control / Roolipohjainen käyttäjäoikeuksien hallinta
- **PIM** = Privileged Identity Management / Tilapäinen käyttöoikeuksien myöntäminen > Käyttäjä ei pidä korkeita oikeuksia jatkuvasti aktiivisena, vaan saa ne vain tarvittaessa.
- **JIT** = Just-in-Time Access > PIM:n keskeinen ominaisuus > Käyttöoikeus pyydetään tarvittaessa, myönnetään määräajaksi ja se poisuu automaattisesti
- **SIEM** = Security Information and Event Management


## Zero Trust -mallin ydinajatus

### 1. Verify explicitly - Vahvista eksplisiittisesti

#### Perusajatus
Verify Explicitly on Zero Trust -mallin tärkein periaate.
- Jokainen käyttöpyyntö täytyy todentaa (authenticate) ja valtuuttaa (authorize).
- Mitään käyttäjää, laitetta tai sovellusta ei pidetä automaattisesti luotettavana.
- Jokainen pyyntö arvioidaan erikseen käytettävissä olevan kontekstin perusteella.
- Tämä on Verify Explicitly -periaatteen ydin: **käyttöoikeus määräytyy aina tilanteen ja riskin perusteella.**

#### Arvioitavia signaaleja
Microsoft 365 tarkastelee esimerkiksi:
- Käyttäjän identiteettiä
- Laitteen kuntoa ja vaatimustenmukaisuutta (device compliance)
- Sijaintia
- Käyttäjän toimintaa ja käyttäytymistä
- Riskitasoa
- Käytettävää sovellusta

#### Conditional Access (Ehdollinen käyttöoikeus)

Microsoft Entra ID:n Conditional Access mahdollistaa sääntöjen luomisen sen perusteella, kuka yrittää käyttää palvelua, millä laitteella ja mistä. Käytännöt voivat arvioida:
- Käyttäjän identiteetti
- Laitteen vaatimustenmukaisuus (Intune)
- Sijainti
- Sovelluksen herkkyys
- Reaaliaikainen riski

Esimerkiksi:  
  ✅ Vaadi MFA, jos SharePointiin kirjaudutaan yritysverkon ulkopuolelta.  
  ✅ Salli pääsy vain Intunen hyväksymiltä laitteilta.  
  ❌ Estä pääsy kokonaan, jos laite ei täytä organisaation vaatimuksia.  

**Session Control** tarkoittaa istunnon rajoittamista, esimerkiksi rajataan käyttäjälle vain read-only -oikeudet tai estetään tiedostojen lataaminen. Esimerkiksi ulkoinen konsultti voi tarkastella tiedostoja, mutta ei ladata niitä itselleen.  

**Conditional Access** = Ehdollinen käyttöoikeus. Määrittää, millä ehdoilla käyttäjä saa pääsyn Microsoft 365 -resursseihin. > Säännöt, jotka määrittävät milloin pääsy sallitaan, estetään tai vaatii lisävarmennuksen.    
**Secure Score** tunnistaa tietoturvapuutteita ja suosittelee uusia Conditional Access -käytäntöjä. Tarkoituksena on auttaa parantamaan turvallisuutta. 

#### Risk-Based Authentication

**Microsoft Entra Identity Protection** arvioi kirjautumisen riskitasoa jatkuvasti hyödyntämällä koneoppimista ja Microsoftin globaalia uhkatiedustelua (Threat Intelligence). Se voi tunnistaa riskejä, kuten: 
- Impossible travel: Käyttäjä kirjautuu ensin Helsingistä ja hetken päästä Australiasta
- Epätavallinen käytös
- Epätavalliset tai tuntemattomat kirjautumisominaisuudet (esim. uusi laite, selain tai sijainti)
- Vuotaneet tai vaarantuneet tunnukset

**Riskin suuruuden perusteella tehdään päätös**:
- Pieni riski > Salli pääsy
- Keskitason riski > Vaaditaan MFA
- Korkea riski > Estä pääsy

### 2. Least priviledge access - Vähimmän käyttöoikeuden periaate

#### Perusajatus
Least Privilege Access on Zero Trust -mallin perusperiaate, jonka mukaan käyttäjille, sovelluksille ja palveluille annetaan vain ne oikeudet, joita ne tarvitsevat työtehtäviensä suorittamiseen.
- Käyttäjille ei anneta ylimääräisiä oikeuksia.
- Käyttöoikeudet rajataan mahdollisimman tarkasti.
- Tavoitteena on pienentää hyökkäyspinta-alaa (attack surface).
- Jos tili vaarantuu, mahdollinen vahinko jää mahdollisimman pieneksi.

Esimerkki:  
  ❌ Helpdesk-työntekijälle annetaan Global Administrator -oikeudet.  
  ✅ Helpdesk-työntekijälle annetaan vain oikeus käyttäjien salasanojen palauttamiseen.  

Ilman vähimmän käyttöoikeuden periaatetta:  
- Liian monella käyttäjällä on korkeat käyttöoikeudet.
- Virheiden ja väärinkäytösten riski kasvaa.
- Hyökkääjä voi saada laajan pääsyn järjestelmiin yhden tilin kautta.

Least Privilege -periaate rajoittaa vahinkojen laajuutta myös silloin, jos käyttäjätili kaapataan.  

Organisaation tulee suunnitella ja määrittää käyttöoikeudet:
- Kuka tarvitsee pääsyn?
- Mihin resursseihin pääsy tarvitaan?
- Millä ehdoilla pääsy sallitaan?
- Kuinka laajat oikeudet käyttäjä tarvitsee?
- Tavoitteena on välttää ylioikeuttaminen (overprovisioning) eli tilanne, jossa käyttäjälle annetaan enemmän oikeuksia kuin hän tarvitsee.

#### Role-Based Access Control (RBAC)
- Käyttäjille annetaan oikeuksia roolien kautta eikä yksittäisiä käyttöoikeuksia erikseen.
- Esimerkki: Password Administrator _saa Nollata käyttäjien salasanoja_ **mutta** _ei saa lukea käyttäjien sähköposteja tai hallita kaikkia Microsoft 365 -asetuksia_
- Hyödyt:
  - Selkeä käyttöoikeuksien hallinta
  - Vähentää ylimääräisiä oikeuksia
  - Tukee tehtävien eriyttämistä (Separation of Duties)
  - Helpottaa vaatimustenmukaisuuden (Compliance) toteuttamista
- **Privilege Creep** = Käyttäjälle kertyy ajan myötä enemmän käyttöoikeuksia kuin hän todellisuudessa tarvitsee.

#### Privileged Identity Management (PIM)
- mahdollistaa tilapäisten käyttöoikeuksien myöntämisen.
- Käyttäjä ei pidä korkeita oikeuksia jatkuvasti aktiivisena, vaan saa ne vain tarvittaessa.
- **Just-In-Time (JIT) Access**: 
  - Käyttöoikeus pyydetään tarvittaessa
  - Hyväksytään tarvittaessa
  - Myönnetään määräajaksi
  - Poistuu automaattisesti

### 3. Assume breach - Oleta tietomurto

- Assume Breach on Zero Trust -mallin periaate, jonka mukaan organisaation tulee toimia olettaen, että hyökkääjä on jo päässyt sisään järjestelmään tai pääsee sinne ennemmin tai myöhemmin.
- Tavoitteena ei ole pelkästään estää hyökkäyksiä, vaan **havaita**, **rajata** ja **minimoida** vahingot mahdollisimman nopeasti.
- Perinteinen ajattelu: "Pidetään hyökkääjät ulkopuolella." VS Assume Breach -ajattelu: _"Entä jos hyökkääjä on jo sisällä?"_
- Tämä on tärkeää siksi, että täydellistä suojausta ei ole olemassa. Hyökkääjä pääsee sisään ennen pitkää, ja silloin on tärkeää **havaita hyökkäys nopeasti**, **estää sen leviäminen** ja saada vahingot jäämään mahdollisimman pieniksi
- Microsoft 365 toteuttaa Assume Breach -periaatetta useilla tietoturvatyökaluilla:
  - Microsoft Defender for Endpoint > näkyvyys laitteisiin (Endpoints)
  - Microsoft Sentinel > näkyvyys identiteetteihin (Identities)
  - Microsoft Defender for Identity > näkyvyys verkkoliikenteeseen (Network Traffic)

#### Defender for Endpoint

- Microsoft Defender for Endpoint suojaa työasemia, kannettavia tietokoneita ja muita päätelaitteita.
- Voi havaita hyökkääjän etenemisen järjestelmästä toiseen (Lateral Movement), yllättävän käyttöoikeuksien korottaminen (Privilege Escalation), Ransomware-toiminnan ja muun epäilyttävän käyttäytymisen
  
#### Microsoft Sentinel

#### Microsoft Defender for Identity
- Microsoft Defender for Identity valvoo identiteetteihin liittyvää toimintaa ja etsii merkkejä tilien vaarantumisesta.



## Zero Trust pillars in Microsoft 365

1. Identity
2. Endpoints
3. Data
4. Apps
5. Infrastructure
6. Network

