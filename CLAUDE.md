# Lassilan kotinäyttö (weatherlassila)

Omistajan oma seinänäyttö Lassilassa, Helsingissä.

- **Tiedosto:** `index.html` (koko sovellus yhdessä tiedostossa)
- **Osoite:** https://simbe.github.io/weatherlassila/
- **Laite:** iPad Air 2, vaakanäkymä 1024×768, Safari. Safarin palkkien kanssa käytettävä korkeus on noin 690 px, joten testaa sekä 1024×768 että 1024×690.
- **Yhteensopivuus: iPadOS 15 (iPad Air 2:n viimeinen versio).** Kirjoita JavaScript ES5-tyyliin: ei `?.`, ei `??`, ei nuolifunktioita, ei `let`/`const`, ei template-merkkijonoja. CSS: ei `:has()`, ei `inset`-lyhennettä, ei erillisiä transform-ominaisuuksia (`rotate:` jne.). Testaa aina WebKitillä.
- **Kieli:** käyttöliittymä on **englanniksi** (omistajan päätös).

## Mitä näytössä on (versio 2.1)

- **Ylärivi:** päivämäärä, liputuspäivän lippu, auringonnousu ja -lasku, kello **sekunteineen** (sekunnit pidetään).
- **Sadetutka** (FMI) kartalla, salamat kartalla ja salamamäärä. Kartan oikeassa yläkulmassa päivän pituus ja muutos eilisestä (`daylight-pill`).
- **Nykyinen sää:** lämpötila, olotila, tuntuu kuin, tuuli, puuskat, kosteus, ilmanpaine. Ahtaassa tilassa `fitCurrent()` pienentää ja piilottaa vähemmän tärkeät (`compact-1`, `compact-2`).
- **Ennuste:** tuntinauha ja päivänauha (FMI, varalla Open-Meteo).
- **Junat Pohjois-Haagan asemalta (POH)**, Digitraffic: 3 lähtöä per suunta (`TIMES_PER_DIRECTION`), suunnat Helsinki ja Airport.
  - Lähtöajat näytetään **aina kellonaikoina, ei minuutteina** (omistajan päätös).
  - Arvioaika (~), perutut, ensimmäinen/viimeinen juna (First/Last) ja Digitrafficin tiedotteet.
  - Jos tila loppuu, rivejä poistetaan siitä suunnasta, jossa on eniten aikoja.
- **Ukkosvaroitus,** kun salama on alle 20 km päässä viimeisen 30 minuutin aikana.
- **Vanhentuneen tiedon merkki** (stale dot), jos sää ei päivity.

## Tehdyt päätökset (älä muuta kysymättä)

- Ei kania, ei pääsiäismunia, ei bussiaikoja, ei sähkön hintaa.
- Junat aina kellonaikoina, kellossa sekunnit.
- Englanninkielinen.
- FMI:n säävaroitukset jätettiin pois (ei CORSia).

## Työskentelysäännöt (koskevat kaikkia muutoksia)

- **Kieli:** vastaa suomeksi ja selkokielellä. Omistaja ei ole ohjelmoija, joten selitä mitä muutos tarkoittaa käytännössä, älä koodia.
- **Kerro suunnitelma ennen kuin teet mitään:** mitä teet ja mitä jätät tekemättä. Jos jokin on epäselvää, kysy ensin. Jos omistaja pyytää vain vastausta tai selvitystä, älä muuta koodia.
- **Versio:** nosta jokaisessa muutoksessa `VERSION`-muuttujaa (pieni muutos 1.0 → 1.1, iso muutos → 2.0).
- **Commit-viesti** aina tässä muodossa:
  ```
  Version X.Y
  - muutos 1
  - muutos 2
  ```
- **Kuvat:** anna kuvakaappaukset ennen ja jälkeen aina, kun ulkoasu muuttuu. Tee kuvat laitteen oikealla näyttökoolla.
- **Testaa ennen julkaisua:** Playwright, Chromium ja WebKit. Testaa valekellolla (`page.clock.install`) ja korvaa kaikki rajapinnat mock-vastauksilla (`page.route`), jotta eri tilanteet saadaan näkyviin (yö, varoitukset, virheet, perutut lähdöt). Tarkista, ettei mikään mene ruudun yli tai katkea. Open-Meteo antaa 429-virheen, jos sitä kutsutaan testeissä liikaa, joten käytä mockeja.
- **Pidä sivu yhtenä tiedostona:** HTML, CSS ja JS samassa tiedostossa, ei build-vaihetta eikä riippuvuuksia (paitsi Leaflet CDN:stä, jos karttaa käytetään).
- **Sivu on julkinen** (GitHub Pages): ei henkilötietoja, terveystietoja eikä tarkkoja kotiosoitteita/koordinaatteja.
- **Julkaisu:** muutos menee haaraan ja pull requestiin. Kun omistaja hyväksyy (merge) päähaaraan, GitHub Pages julkaisee sen. Pages välimuistittaa noin 10 minuuttia. Livenä olevan version voi tarkistaa hakemalla sivun ja katsomalla `VERSION`-arvon.
- **Säännöt kaikkien kolmen näytön kesken:** sama koodipohja on käytössä myös repoissa `weatherlassila`, `dashboardkilo` ja `dashboardketunkorpi`. Jos korjaat yleisen bugin (esim. sää, tutka, liputuspäivät), mainitse, että sama korjaus voi koskea muitakin näyttöjä.

## Tekniset huomiot

- **Koko:** kaikki mitoitetaan muuttujasta `--u` (ks. CSS:n alku), jotta näyttö täyttyy tarkasti. Fonttikoot tulevat muuttujista `--fs-xs` … `--fs-2xl`; älä lisää irrallisia px-kokoja.
- **Aikavyöhyke:** kaikki kellonajat lasketaan Europe/Helsinki-ajassa apufunktioilla (`helsinkiDateKey`, `helsinkiHour` jne.), ei laitteen paikallisajassa.
- **Rajapinnat, jotka toimivat suoraan selaimesta (CORS):** Open-Meteo, FMI WFS (ennuste, salamat), FMI WMS (sadetutka), Digitraffic (junat ja tiedotteet), HSL Digitransit, spot-hinta.fi.
- **Eivät toimi selaimesta (ei CORSia):** FMI:n CAP-varoitukset, MeteoAlarm, porssisahko.net. Nämä vaatisivat välipalvelun.
- **Sadetutka:** uusin kuva-aika luetaan GetCapabilities-vastauksesta (muuten CORB-ongelmia).
- **Nimipäiväluettelo on lisensoitu** (Almanakkatoimisto), joten sitä ei käytetä. Liputuspäivät tulevat virallisesta listasta (`FLAG_DAYS` + sääntöpohjaiset päivät).
