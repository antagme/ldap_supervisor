# Final Project EDT | LDAP TLS SASL
_Advanced Use of Dockerized Openldap Server and alternatives to secure your Openldap Server_

## Overview

With different _Dockers Containers_ we gonna construct some examples around _LDAP SERVER container_.

## Description of the Project

Partirem de la base que tothom te una base de [LDAP](https://es.wikipedia.org/wiki/OpenLDAP) , teorica o practica.
El que he fet ha sigut simular diferents casos reals amb _dockers Containers_ .
Tota la comunicacio de dades sensibles entre els _Containers_ es fa mitjançant _TLS_.

Tenim aixi els seguents _Dockers Containers_ :

- Docker LDAP
- Docker Kerberos
- Docker Client (Simulating a School Client)
- Docker LDAP Replica 
- Docker Apache + Mysql + Zabbix

Each _Docker Container_ have their own work. Also , when i was preparating my project , i decided to use a most secure auth than the simple one of _LDAP_ , so i decided  to implement _GSSAPI_ , the best one.

### Tecnologies Emprades.

* Openldap
  * Object Class used:
      * To Retrieve Users.
      * To Retrieve Grups.
      * To Retrieve Hosts.
  * AuthTypes Working:
     * SASL GSSAPI(Kerberos Ticket Auth)
     * SASL External(Certificate Auth)
  * StartTLS Security Transport Layer
  * Replication Consumer LDAP with StartTLS Communication And SASL GSSAPI.
* Docker 
* Openssl ( To create Own Certificates for each service that need it)
* Supervisord 
    * To Manage the Processes inside the _Dockers Containers_ 
* Nslcd 
    * For retrieve Hosts Info 
* Kerberos 
  * For Obtain ticket
  * Do _Kerberos Auth_ with _SSSD_ 
  * _GSSAPI Auth_ with ldap clients.
* PAM
  * For the propertly _System-Auth_ With _Kerberos_ + _LDAP_
* Zabbix Agentd y Zabbix Server
  * For Monitoring each _Docker Container_
  * For Monitoring  _LDAP Monitor Database_ with a _Python Script_.
* Crond
  * For Automated execution of the _Python Script_ for _LDAP Monitor Database_ each minute.

### To Start Docker Containers
#### Create Docker Network 
_This is for a very important reason, we need to always have the same container IP for the proper Ip distribution through LDAP and NSLCD.
In the default Bridge Network , we can't assign ips for containers_

 ```bash
 # docker network create --subnet 172.18.0.0/16 -d bridge test
 ```
#### Arrencar Docker LDAP! 
 ```bash
 # docker run --net test --ip 172.178.0.2 -h ldap.edt.org --name ldap -it antagme/ldap_supervisor:latest
 ```  
#### Arrencar Docker Kerberos (TGT)  
 ```bash
 # docker run --net test --ip 172.178.0.3 -h kserver.edt.org --name ldap -it antagme/kerberos:latest
 ```
#### Arrencar Docker LDAP REPLICA
 ```bash
 # docker run --net test --ip 172.178.0.4 -h ldaprepl.edt.org --name replica -it antagme/ldap_replica:latest
 ```
#### Nota important.
_Es Molt important seguir l'ordre per rebre correctament les dades del DNS , encara que si falles, nomes hauria que reiniciarse el Daemon NSLCD al docker que falla_

### Under Construction...

- [x] Zabbix Server Working in Ubuntu
- [ ] Put supervisord in all computers
- [ ] Put TLS on nslcd communication.
- [ ] Create a PAM for auth krb5 and/or unix ldap.

 
 ![Alt text](http://octodex.github.com/images/stormtroopocat.jpg "The Stormtroopocat")

- |Client Autoritçat per GSSAPI , i per SASL EXTERNAL|
- |Serveis instalats: Slapd , nslcd , nginx|
- |Clients Agafen tickets d'un docker kerberos per auth|
- | LDAP fa de dns als ordinadors de la xarxa |

La meva idea es tenir tots els dockers monitoritçats amb un servidor Zabbix central instalat a un docker httpd i agents zabbix a cada docker. En especial en el docker del servidor `LDAP` la meva intencio es fabricar uns scripts per redirigir dades de la `BBDD`
Monitor i aixi veure-ls a la interficie grafica.
