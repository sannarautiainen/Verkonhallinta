## 1. Johdanto

Monitoroinnilla tarkoitetaan tiedon keruuta ja sen perusteella toiminnan seurantaa. Monitoroinnin tavoitteena on havaita mahdolliset ongelmat mahdollisimman varhain, ennen kuin ne näkyvät käyttäjille. Monitorointi on tärkeää jotta muutokset palveluiden ja verkkojen toiminnassa voidaan havaita mahdollisimman aikaisin. Tällöin muutoksiin voidaan reagoida nopeasti. 

Prometheus on avoimen lähdekoodin monitorointijärjestelmä. Sen avulla voidaan valvoa palvelin-, pilvi- ja konttiympäristöjen toimintaa. Prometheus saa tietonsa exporter-ohjelman kautta. Exporter-ohjelma kerää järjestelmistä tilatietoja ja muuttaa ne Prometheuksen ymmärtämään muotoon. Nämä tiedot Prometheus tallentaa aikasarjatietokantaan, josta voidaan muodostaa kuvaajia ja trendejä.

## 2. Ympäristön rakentaminen

Tehtävää varten asensin Node Exporterin uusimman version (1.12.1). Se ladattiin komennolla wget https://github.com/prometheus/node_exporter/releases/latest/download/node_exporter-1.12.1.linux-amd64.tar.gz ja paketti purettiin komennolla tar xvf node_exporter-1.12.1.linux-amd64.tar.gz. Tämän jälkeen siirryttiin hakemistoon ja avattiin Node Exporter. Toisessa terminaali-ikkunassa toiminta testattiin komennolla curl http://localhost:9100/metrics joka palautti runsaasti arvoja, osasta kuvakaappaukset: Tehtava3.2-1, Tehtava3.2-2, Tehtava3.2-3, Tehtava3.2-4, Tehtava3.2-5, Tehtava3.2-6. 


## 3. Mittareiden kerääminen

Node exporterin avulla kerättiin tietoa prosessorin käytöstä, muistin käytöstä, levyn käytöstä sekä lähtevästä ja saapuvasta verkkoliikenteestä. 


## 4. Prometheus

Tarkistettu Prometheuksessa, että Status -> Targets -sivulla näkyy kohteena web1 ja että sen tila on UP. Tästä kuvakaappaus: Tehtava3.3


## 5. Grafana-näkymät

Grafanaan lisättiin tietolähteeksi Prometheus, palvelimen osoitteena http://prometheus:9090. Tämän jälkeen testattiin yhteyden toimivuus, josta kuvakaappaus Tehtava3.4. Grafanaan luotiin uusi Dashboard, ja siihen paneeleiksi CPU Usage % jonka PromQL: 100 - (avg by(instance)
(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100), Memory Usage % jonka PromQL: (node_memory_MemTotal_bytes -
 node_memory_MemAvailable_bytes)
/
node_memory_MemTotal_bytes
* 100, Disk Usage % jonka PromQL: 100 -
(
node_filesystem_avail_bytes
/
node_filesystem_size_bytes
* 100
), Network Receive jonka PromQL: rate(node_network_receive_bytes_total[5m]) sekä Network Transmit jonka PromQL: rate(node_network_transmit_bytes_total[5m]).

Visuaaliset mittarit ovat näkymiä kyselyn tulokseen. Visuaalisen mittarin avulla tieto on helposti ymmärrettävää ja ja olennaisen tiedon näkee nopeasti. Kun tiedon näkee nopealla vilkaisulla, on muutoksiin reagoiminen myös nopeampaa. 


## 6. Havaintoja monitoroinnista

Monitorointiympäristö tuotti tietoa CPU:n keskimääräisestä käytöstä prosentteina, käytetyn muistin osuudesta prosentteina, levytilan käytöstä prosentteina, sekä saapuvan ja lähtevän verkkoliikenteen nopeudesta tavuina.

Web1-palvelimelle tehtiin kuormitustesti, jossa aiheutettiin levykuormitusta komennolla dd if=/dev/zero of=testfile.img bs=1M count=500 ja CPU-kuormitusta komennolla yes > /dev/null. Kuormitus näkyi Grafanan dasboardilla CPU-käyrän merkittävänä nousuna, myös levyn käyttöä kuvaavassa käyrässä näkyi selkeä nousu. Verkkoliikenteen käyrät pysyivät samalla tasolla kuin ennen kuormitustestin aloitusta. Tästä kuvakaappauksena kuva Tehtava3.6.

| Ominaisuus    | SNMP | Prometheus | 
| -------- | ---- | ----------------- | 
| Tiedonkeruu  | SNMP-agentin ja managerin avulla | Hakee Exporterin keräämän tiedon ja julkaisee HTTP-rajapinnassa | 
| Käyttöönotto | vaatii agentin ja MIB-paketin asennuksen | vaatii Exporterin asennuksen | 
| Mittarien määrä  | riippuu MIB-objektien määrästä | useita, kyselyiden avulla voidaan tarkastella | 
| Visualisointi  | erillinen työkalu | Grafana | 
| Hälytysmahdollisuudet  | trap-viestit | Prometheus Alertmanager | 
| Soveltuvuus pilviympäristöihin  | soveltuu verkkolaitteiden hallintaan | soveltuu hyvin | 

## 7. Pohdinta

Tehtävissä pääsin tutustumaan Node Exporterin, Prometheuksen ja Grafanan käyttöön. Opin, miten tietoa voidaan kerätä ja miten luoda kerätystä tiedosta visuaalisia mittareita. Kuormitustestin yhteydessä pääsin myös havainnoimaan muutoksia visuaalisten mittareiden kautta. Prometheuksen vahvuuksia ovat mahdollisuus tehdä kyselyjä datasta sekä sen yhteensopivuus Grafanan kanssa, jotta tiedot saadaan visuaaliseen muotoon. 

