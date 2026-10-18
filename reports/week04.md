## 1. Johdanto

Infrastructure as Code tarkoittaa infrastruktuurin (esim. palvelimet ja verkot) asennusta ja asetusten hallintaa koodilla. Sen avulla esimerkiksi konfiguraatiot onnistuvat automaattisesti.

## 2. Inventory

Komennolla cat /ansible/inventory.ini löytyy laitteet r1, r2, r3, client1, attacker, branch-client, web1, db1, Prometheus, Grafana, Zabbix, Cadvisor sekä Ansible. Ryhmät on muodostettu laitteen tyypin ja käyttötarkoituksen perusteella: reitittimet, asiakaskoneet, palvelimet, valvontajärjestelmät, hallintatyökalut. Ryhmien avulla määritykset voidaan kohdistaa vain tietyille laitteille. 

Komennolla ansible-playbook -i ../inventory.ini ping.yml testataan yhteyden toimivuutta inventoryn laitteisiin. Tällä tarkistetaan, että Ansible saa yhteyden laitteisiin ja pystyy suorittamaan niillä komentoja. Yhteys laitteisiin client1. attacker, branch-client, web1 ja db1 onnistui. Yhteys prometheus, grafana, zabbix, ansible ja cadvisor epäonnistuivat virheilmoituksella "Make sure this host can be reached over ssh". Yhteys r1, r2 ja r3 epäonnistui virheilmoituksella "paramiko is not installed"


## 3. SNMP Playbook
Ensimmäisellä yrityksellä asennus ei onnistunut:
root@ansible:/ansible/playbooks# install-snmp.yml
bash: install-snmp.yml: command not found

Tekoälyn avulla sain selville, että käynnistystehtävää voi muuttaa käyttämään komentoa service snmpd start. Tiedostoa muokkaamalla tämän mukaiseksi ja kokeilemalla toista komentoa lähti toimimaan. 
root@ansible:/ansible/playbooks# nano install-snmp.yml
root@ansible:/ansible/playbooks# ansible-playbook -i ../inventory.ini install-snmp.yml

PLAY [Install and configure SNMP] *********************************************************************************************

TASK [Update package cache] ***************************************************************************************************
ok: [branch-client]
ok: [web1]
ok: [db1]

TASK [Install SNMP packages] **************************************************************************************************
ok: [branch-client]
ok: [db1]
ok: [web1]

TASK [Configure SNMP community] ***********************************************************************************************
changed: [branch-client]
changed: [db1]
changed: [web1]

TASK [Start SNMP service] *****************************************************************************************************
changed: [web1]
changed: [db1]
changed: [branch-client]

PLAY RECAP ********************************************************************************************************************
branch-client              : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
db1                        : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
web1                       : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

root@ansible:/ansible/playbooks# ansible web1,db1,branch-client -i ../inventory.ini -b -m shell -a "service snmpd status"
db1 | CHANGED | rc=0 >>
 * snmpd is running
branch-client | CHANGED | rc=0 >>
 * snmpd is running
web1 | CHANGED | rc=0 >>
 * snmpd is running


## 4. Node Exporter Playbook

root@ansible:/ansible/playbooks# nano install-node-exporter.yml
root@ansible:/ansible/playbooks# ansible-playbook -i ../inventory.ini install-node-exporter.yml

PLAY [Install Prometheus Node Exporter] ***************************************************************************************

TASK [Install prerequisite packages] ******************************************************************************************
ok: [web1]
ok: [db1]

TASK [Create installation directory] ******************************************************************************************
ok: [db1]
ok: [web1]

TASK [Download Node Exporter] *************************************************************************************************
changed: [db1]
changed: [web1]

TASK [Extract archive] ********************************************************************************************************
changed: [web1]
changed: [db1]

TASK [Install exporter binary] ************************************************************************************************
changed: [db1]
changed: [web1]

TASK [Start node_exporter] ****************************************************************************************************
changed: [web1]
changed: [db1]

TASK [Verify exporter responds] ***********************************************************************************************
ok: [web1]
ok: [db1]

TASK [Show verification result] ***********************************************************************************************
ok: [web1] => {
    "msg": "Node Exporter running on web1"
}
ok: [db1] => {
    "msg": "Node Exporter running on db1"
}

PLAY RECAP ********************************************************************************************************************
db1                        : ok=8    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
web1                       : ok=8    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


## 4. Järjestelmätiedot

Suoritin komennon ansible all -i ../inventory.ini -m setup ja tuloksista etsin seuraavat tiedot:

| Laite    | Käyttöjärjestelmä | IP-osoite | Prosessorien määrä | Muistin määrä |
| -------- | ------------- | ----------------- | ------ | --------- |
| web1  | Ubuntu Debian 24.04 | 172.20.20.9 | 12 | 7760 |
| db1 | Ubuntu Debian 24.04 | 172.20.20.7 | 12 | 7760 |
| branch-client  | Ubuntu Debian 24.04 | 172.20.20.7 | 12 | 7760 |



## 6. Vertailu

Käsin asentaessa jokainen laite pitää konfiguroida erikseen, joka on hidasta. Käsin tehdessä myös virheiden todennäköisyys kasvaa. Automaatiolla asennukset saadaan tehtyä nopeammin ja usealle laitteelle kerrallaan. Asennukset ovat myös yhdenmukaisia, mikä ei välttämättä aina käsin tehdessä toteudu. 


## 7. Yhteenveto

Tehtävien aikana opin Ansiblen käyttöä ja miten suorittaa playbookeja. Opin miten asennuksia voidaan automatisoida, mikä vähentää virheiden mahdollisuutta ja nopeuttaa työskentelyä. 
