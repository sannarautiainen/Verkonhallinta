## 1. Johdanto
SNMP on verkonhallinnan tiedonkeruumenetelmä, jolla voidaan lukea verkkolaitteiden ja palvelimien tila- ja suorituskykytietoja. Näihin tilatietoihin kuuluu mm. CPU-kuorma, muistin käyttö, laitteiden lämpötilat, käyttöaika ja rajapintojen liikemäärät. SNMP:n toiminta perustuu valvottavassa laitteessa toimivaan agenttiin sekä manageriin, joka kerää tietoja keskitetysti lähettämällä kyselyitä, joihin agentti vastaa. SNMP:stä on olemassa erilaisia versioita, näistä SNMPv3 on nykyaikaisin, suositeltu versio jonka tietoturva on aiempia versioita merkittävästi parempi.  

## 2. Asennus
SNMP-agentti asennettiin kirjautumalla ensin halutulle palvelimelle, jonka jälkeen asennus suoritettiin komennoilla apt update ja 
apt install snmp snmpd -y. SNMP-agentin asennuksen jälkeen asennettiin myös tarvittavat työkalut komennoilla apt install -y iputils-ping, apt install net-tools ja apt install iproute2. Kun kaikki oli asennettu, piti konfiguraatiotiedostossa snmpd.conf muuttaa yhteisön nimeksi rocommunity public ja sitten käynnistää palvelu uudelleen komennolla service snmpd restart.

Kyseilyiden tulosten luettavuutta parantamaan asennettiin myös MIB-paketti komennolla apt install snmp-mibs-downloader, ja käyttöä varten asennuksen jälkeen snmp.conf -tiedostossa kohta mibs: muutettiin kommentiksi. 

## 3. Kerätyt tiedot
Tehtävän 2.3 tuloste: root@ansible:/# snmpwalk -v2c -c public web1 system
SNMPv2-MIB::sysDescr.0 = STRING: Linux web1 6.18.33.2-microsoft-standard-WSL2 #1 SMP PREEMPT_DYNAMIC Thu Jun 18 21:54:43 UTC 2026 x86_64
SNMPv2-MIB::sysObjectID.0 = OID: NET-SNMP-MIB::netSnmpAgentOIDs.10
DISMAN-EVENT-MIB::sysUpTimeInstance = Timeticks: (4256) 0:00:42.56
SNMPv2-MIB::sysContact.0 = STRING: Me <me@example.org>
SNMPv2-MIB::sysName.0 = STRING: web1
SNMPv2-MIB::sysLocation.0 = STRING: Sitting on the Dock of the Bay
SNMPv2-MIB::sysServices.0 = INTEGER: 72
SNMPv2-MIB::sysORLastChange.0 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORID.1 = OID: SNMP-FRAMEWORK-MIB::snmpFrameworkMIBCompliance
SNMPv2-MIB::sysORID.2 = OID: SNMP-MPD-MIB::snmpMPDCompliance
SNMPv2-MIB::sysORID.3 = OID: SNMP-USER-BASED-SM-MIB::usmMIBCompliance
SNMPv2-MIB::sysORID.4 = OID: SNMPv2-MIB::snmpMIB
SNMPv2-MIB::sysORID.5 = OID: SNMP-VIEW-BASED-ACM-MIB::vacmBasicGroup
SNMPv2-MIB::sysORID.6 = OID: TCP-MIB::tcpMIB
SNMPv2-MIB::sysORID.7 = OID: UDP-MIB::udpMIB
SNMPv2-MIB::sysORID.8 = OID: IP-MIB::ip
SNMPv2-MIB::sysORID.9 = OID: SNMP-NOTIFICATION-MIB::snmpNotifyFullCompliance
SNMPv2-MIB::sysORID.10 = OID: NOTIFICATION-LOG-MIB::notificationLogMIB
SNMPv2-MIB::sysORDescr.1 = STRING: The SNMP Management Architecture MIB.
SNMPv2-MIB::sysORDescr.2 = STRING: The MIB for Message Processing and Dispatching.
SNMPv2-MIB::sysORDescr.3 = STRING: The management information definitions for the SNMP User-based Security Model.
SNMPv2-MIB::sysORDescr.4 = STRING: The MIB module for SNMPv2 entities
SNMPv2-MIB::sysORDescr.5 = STRING: View-based Access Control Model for SNMP.
SNMPv2-MIB::sysORDescr.6 = STRING: The MIB module for managing TCP implementations
SNMPv2-MIB::sysORDescr.7 = STRING: The MIB module for managing UDP implementations
SNMPv2-MIB::sysORDescr.8 = STRING: The MIB module for managing IP and ICMP implementations
SNMPv2-MIB::sysORDescr.9 = STRING: The MIB modules for managing SNMP Notification, plus filtering.
SNMPv2-MIB::sysORDescr.10 = STRING: The MIB module for logging SNMP Notifications.
SNMPv2-MIB::sysORUpTime.1 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.2 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.3 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.4 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.5 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.6 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.7 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.8 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.9 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.10 = Timeticks: (0) 0:00:00.00

Tulosteesta löytyy tiedot, että järjestelmän nimi on web1, käyttöjärjestelmä on Linux ja uptime on 0:00:42.56.

Tehtävässä 2.4 pyydetyt tiedot ovat:
Järjestelmän nimi
root@ansible:/# snmpget -v2c -c public web1 sysName.0
SNMPv2-MIB::sysName.0 = STRING: web1

Järjestelmän kuvaus
root@ansible:/# snmpget -v2c -c public web1 sysDescr.0
SNMPv2-MIB::sysDescr.0 = STRING: Linux web1 6.18.33.2-microsoft-standard-WSL2 #1 SMP PREEMPT_DYNAMIC Thu Jun 18 21:54:43 UTC 2026 x86_64

Käyttöaika
root@ansible:/# snmpget -v2c -c public web1 sysUpTime.0
DISMAN-EVENT-MIB::sysUpTimeInstance = Timeticks: (82329) 0:13:43.29

| Laite    | Nimi | Käyttöjärjestelmä | Uptime |
| -------- | ---- | ----------------- | ------ |
| web1  | web1 | Linux | 1:30:36.64 |
| db1 | db1 | Linux | 0:01:40.22 |
| branch-client  | branch-client | Linux | 0:03:04.97 |

## 4. Verkkorajapinnat
Rajapintoja löytyi kaksi: lo ja eth0. Rajapinta eth0 yhdistää laitteen verkkoon.

root@ansible:/# snmpwalk -v2c -c public web1 ifDescr
IF-MIB::ifDescr.1 = STRING: lo
IF-MIB::ifDescr.22 = STRING: eth0

## 5. OID-analyysi

| OID    | Tarkoitus |
| -------- | ------- |
| sysName.0  | Kertoo laitteen nimen |
| sysDescr.0 | Kertoo kuvauksen laitteesta, sisältää esimerkiksi tiedot käyttöjärjestelmästä |
| sysUpTime.0    | Kertoo laitteen käyttöajan |
| ifDescr | Kertoo rajapinnan nimen |
| ifOperStatus | Kertoo senhetkisen toimintatilan |



## 6. Pohdinta
Verkonhallinnassa SNMP:stä on hyötyä verkon laitteiden tilan ja suorituskyvyn valvonnassa. Sen avulla voidaan kerätä tietoa suorituskyvystä, käyttöajasta, laitteen tiedoista kuten nimi ja käyttöjärjestelmä sekä verkkorajapinnoista. SNMP:n etuna on sen tarjoama valmistajasta riippumaton tapa kerätä tietoa laitteilta, jolloin se on käytettävissä erilaisissa ympäristöissä.

SNMPv2:n ongelma on community string -arvoihin perustuva tunnistautuminen johon ei kuulu vahva salaus tai tunnistautuminen, joten sen tietoturva on heikkoa. SNMPv3 taas tarjoaa käyttäjätunnistuksen ja salauksen, jonka vuoksi sen tietoturva on merkittävästi parempi. Tämän vuoksi erityisesti tuotantoympäristöissä käyttäisin SNMPv3:a. 
