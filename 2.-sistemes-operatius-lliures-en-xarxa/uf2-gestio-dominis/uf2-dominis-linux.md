# Introducció als dominis a GNU/Linux

## LDAP

En Linux, el **protocol** que s'encarrega de crear, modificar i accedir a informació en un directori és **LDAP**.

**LDAP** són les sigles de _**lightweight directory access protocol**_ o protocol d’accés lleuger a directoris.

El **LDAP** és un protocol obert.

Corre a nivell d’aplicació sobre **TCP/IP**.

{% hint style="success" %}
El **Lightweight Directory Access Protocol (LDAP)** defineix un protocol de missatgeria utilitzat per clients de directori i servidors de directori.
{% endhint %}

A Linux, un dels programes que permet gestionar la informació d'un directori LDAP és **OpenLDAP**.&#x20;

Així doncs, LDAP és la definició d'un **protocol estàndard**. OpenLDAP i Active Directory són implementacions basades en aquest protocol.

{% hint style="info" %}
De fet, l'Active Directory de Windows també utilitza LDAP per gestionar la informació del domini.
{% endhint %}

### OpenLDAP vs Active Directory

**OpenLDAP** és una implementació **lliure i de codi obert** del protocol LDAP, molt utilitzada en entorns GNU/Linux. La seguretat es basa en **capes de xifratge** com TLS/SSL i SASL per protegir les consultes. La seva administració requereix més coneixements tècnics i es realutza principalment mitjançant fitxers de configuració (_.ldif_) i línia d’ordres. No hi ha una interfície gràfica oficial, però existeixen moltes opcions: Apache Directory Studio, JXplorer, phpLDAPadmin, LDAP Account Manager (LAM)...

**Active Directory**, en canvi, és un servei de directori complet integrat a **Windows Server**. Tot i que també utilitza LDAP, incorpora funcionalitats addicionals com l’**autenticació amb Kerberos**, la gestió centralitzada d’equips i les polítiques de grup (GPO). La seva administració és més senzilla gràcies a eines gràfiques, però és una **solució propietària** i menys flexible que OpenLDAP.

## Precedent

A mitjans dels anys 80 existien dues organitzacions d’estandardització independents: CCITT (actual International Telecommunication Union - ITU-T) i ISO (International Organization for Standardization). Tots dos volien crear un servei de directori d’informació.

L’any 1988 va néixer X.500 fruit de l’esforç d’unir les dues especificacions de directoris independents. Aquest estàndard consisteix en una sèrie de recomanacions com X.501, X.509, X.511...; que encara avui s’actualitzen.

#### X.500:

* Organitza les entrades de directori en un espai de noms jeràrquic.&#x20;
* Especifica la comunicació entre el client i servidor de directori utilitzant el Protocol d’Accés a Directoris (DAP) dins de la capa d’Aplicació d’OSI.&#x20;

#### LDAP:

* Va ser creat per alleugerir l’accés a directori que provocava l’ús de DAP.
* Fruit de la seva simplicitat va poder implementar-se a TCP/IP. Actualment, també es poden fer servir els protocols de X.500, incloent-hi DAP, sobre TCP/IP.
* Els servidors de directori LDAP poden arribar a suportar LDAP i DAP.
* La versió actual és **LDAPv3**

## Característiques de LDAP

* **Consulta ràpida de dades:** LDAP ofereix un mètode lleuger de connexió a un dipòsit, lectura de dades i tancament de la connexió.
* **Solució distribuïda:** És un protocol natiu de xarxa d’arquitectura distribuïda, pot distribuir a tota la xarxa informació llesta per ser usada per totes les aplicacions.&#x20;
* **Independència de la plataforma:** És un protocol estàndard d’accés a dades remot i local, amb el que és possible intercanviar completament la implementació de l’LDAP sense afectar la forma accés a les dades.
* **Seguretat integrada en el repositori:** la informació d’accés s’emmagatzema en el mateix repositori. Es poden definir al servidor els drets i permisos i, també controlar els drets d’accés en funció de factors externs, com ara l’adreça IP del client.&#x20;
* **Implementació fàcil del client:** Al ser estàndard el trobem a: IBM Tivoli, Microsoft Active Directory, Novell eDirectory o RedHat Directory Server.
* **Baix cost:** LDAP és un protocol estàndard i no cal pagar llicències pel seu ús ni per disposar de clients o servidors (de codi font obert).
* Arquitectura **client-servidor**

## Ús de LDAP

L’objectiu principal és permetre l’**autenticació** en xarxa.

És un sistema ideal per **centralitzar l’administració d’usuaris** en un únic lloc.

Es pot utilitzar de manera conjunta amb una gran quantitat d’**aplicacions que disposen de suport per a l’LDAP**, com:

* Sistemes d’autenticació per a pàgines web: alguns dels gestors de continguts permeten autenticació a través de LDAP.
* Sistemes de correu electrònic.
* Sistemes d’allotjament de pàgines web i FTP.

En general, el LDAP s’utilitza quan es vol accedir a una base de dades i cal autenticar-se des de **diferents plataformes** i des de múltiples ordinadors o aplicacions ubicats en una xarxa.

El servei de directori LDAP té una **arquitectura client-servidor**.

Molt sovint un directori es descriu com una base de dades. En canvi, és **una base de dades especialitzada** que té característiques que els separen de les bases de dades relacionals de propòsit general.

<br>

<br>
