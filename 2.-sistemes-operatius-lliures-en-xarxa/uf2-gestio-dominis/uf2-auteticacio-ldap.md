# Autenticació LDAP

## Autenticació d'usuari LDAP

Com ja hem comentat anteriorment, una de les utilitats més importants d'un servidor LDAP és actuar com a servidor d'**autenticació** centralitzat.

**Autenticar** és necessari per accedir a un sistema GNU/Linux, utilitzar els seus recursos o per accedir-ne d'altres compartits en xarxa.

També per accedir a alguns serveis com un **servidor FTP, servidor SSH** o a **pàgines web privades** en un servidor web.

En aquest tema veurem les modificacions que cal realitzar en un sistema Linux perquè autentiqui als usuaris en un **servidor LDAP**, en lloc d'utilitzar els clàssics arxius `/etc/passwd`, `/etc/group` i `/etc/shadow`.

Aquesta configuració permet una gestió centralitzada dels usuaris i facilita l’administració en entorns amb múltiples servidors o serveis.

## Mòduls i serveis relacionats

Els sistemes GNU/Linux fan servir diversos serveis (_daemons_) i mòduls per realitzar l'autenticació i el procés d'autorització tan localment com extern (en xarxa).&#x20;

Quan fem servir LDAP com a servei d'autenticació tenim dues opcions:

1. Opció 1: **PAM + NSS + LDAP**
2. Opció 2: **PAM + NSS + SSSD + LDAP** (opció moderna)

Aquest tutorial està pensat per fer la primera opció. Encara funciona i és més lleugera.&#x20;

Si es vol aprofundir en l'opció amb SSSD es pot consultar aquest [manual](https://documentation.ubuntu.com/server/how-to/sssd/with-ldap/).

Mirem els diversos mòduls i serveis abans de seguir.

### PAM (_pluggable authentication module_)

El **PAM** és un mecanisme que utilitzen les aplicacions dels sistemes GNU/Linux per gestionar l’autenticació, l’autorització i altres tasques relacionades amb la gestió de sessions d’usuari. PAM **no gestiona identitats** ni emmagatzema usuaris. Només executa mòduls segons la configuració.

**Avantatges**:

* Permet utilitzar sistemes d’autenticació diferents del mecanisme tradicional d’autenticació dels sistemes GNU/Linux.
* Facilita la integració de serveis com **LDAP**, **Kerberos** o autenticació multifactor.
* Permet desenvolupar programes amb independència de l’esquema d’autenticació.
* La gran majoria de les aplicacions dels sistemes GNU/Linux estan preparades per utilitzar el Linux PAM.

Exemple de mòduls PAM:

* `pam_unix` → usuaris locals
* `pam_ldap` → autenticació directa contra LDAP
* `pam_sss` → delega l’autenticació a SSSD

### NSS

L’**NSS** (_Name Service Switch_) és el mecanisme que indica al sistema on i en quin ordre ha de buscar informació bàsica com ara: usuaris, grups, contrasenyes i noms d’ordinadors.

Això vol dir que cerca la informació en fitxers com `/etc/passwd`, `/etc/group`, `/etc/shadow` o `/etc/hosts`. També cercar informació en sistemes remots amb LDAP entre altres.

El seu fitxer de configuració principal és `/etc/nsswitch.conf`.

### SSSD

**SSSD** (_System Security Services Daemon_) és un servei intermediari per gestionar la identitat, autenticació i autorització d’usuaris que poden provenir tant del sistema local com de serveis externs. Actua com una capa intermèdia entre el sistema operatiu i els serveis d’identitat.&#x20;

Les seves funcions principals són:

* Autenticació d’usuaris
* Resolució d’identitats (usuaris i grups)
* Gestió d’autoritzacions
* Cau local de credencials, que permet iniciar sessió encara que el servidor remot no estigui disponible

**SSSD** centralitza aquestes funcions. Quan un usuari inicia sessió, el programa de login fa ús de **PAM**, que mitjançant el mòdul `pam_sss` es comunica amb el servei **SSSD**.\
**SSSD** consulta el servei d’identitat configurat (com **LDAP** o **Active Directory**) per obtenir la informació de l’usuari i, si escau, utilitza Kerberos per validar les credencials.\
Un cop validada la informació, SSSD retorna la resposta a **PAM** i a **NSS**, permetent al sistema autoritzar l’accés i resoldre l’usuari i els seus grups.

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Així doncs, en una autenticació externa amb LDAP amb Kerberos:

* **PAM** controla l’accés
* **NSS** resol usuaris i grups al sistema
* **SSSD** actua com intermediari gestionant identitats i autenticació
* **LDAP** defineix i emmagatzema la informació dels usuaris i atributs
* **Kerberos** valida credencials d'autenticació

## Configurar el client LDAP

A continuació configurarem un ubuntu desktop per tal que s'autentiqui amb un domini ja creat en un servidor LDAP.

Per tal que en el nostre Ubuntu Desktop client s'autentiqui per LDAP, instal·larem els paquets i eines necessàries per configurar el client.

El servei que gestiona la connexió amb LDAP és el **servei nslcd**.

Aquesta comanda instal·larà el paquets, amb el que es crearà el fitxer `/etc/nslcd.conf`.

`sudo apt-get install libnss-ldapd libpam-ldapd ldap-utils`

> **ATENCIÓ**: Si la següent configuració no es fa correctament, el més probable és que la màquina no s'engegui o trigui molt en fer-ho!!!

Els paràmetres de configuració que demana són els següents:

* Servidor LDAP: **ldap://**_**IP\_de\_servidor**_ (poseu la IP del vostre servidor)
* Base del domini (DN): **dc=ldapxxx,dc=local**
* Serveis que es faran servir LDAP: (marcar) **passwd**, **group**, **shadow**

En algunes ocasions, també poden aparèixer aquests passos:

* Versió de LDAP: **3**
* Crear una base de dades local: **Sí**
* Determinar si es requereix login per accedir a la base de dades: **No**
* CN (common name) de l'usuari administrador del directori LDAP: **cn=admin,dc=ldapxxx,dc=local**
* Contrasenya per accedir a LDAP com a root: No posar contrasenya (Intro)

La comprovació es farà validant usuaris un cop s'hagin creat alguns.

{% hint style="info" %}
**Revisar la configuració** accedint al fitxer `/etc/nslcd.conf`
{% endhint %}

<mark style="color:$danger;">**Si hi ha un error a la configuració...**</mark>

Podem tornar a fer la configuració del fitxer `/etc/nslcd.conf` amb l'ordre:

`sudo dpkg-reconfigure libnss-ldapd`

Reinicia la configuració i actualitza els fitxers:

* `/etc/nslcd.conf`
* `/etc/nsswitch.conf`

### Configurar l’autenticació d’usuaris (NSS i PAM)

Les següents comandes serveixen per indicar al sistema que es puguin autenticar usuaris utilitzant tant base de dades d'usuaris locals (arxius `/etc/passwd`, `/etc/shadow` i `/etc/group`) com la base de dades del servei LDAP.

Si volem que l'usuari LDAP tingui un espai al disc haurem de fer una modificació a la configuració. Però aquest espai és necessari per poder iniciar sessió en el mode gràfic.&#x20;

Es pot fer directament per comanda:

```
sudo pam-auth-update --enable mkhomedir
```

O, amb el procediment gràfic:

```
sudo pam-auth-update
```

> S'ha de marcar l'opció _**Create home directory on login**_.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

#### <mark style="color:$success;">**AJUDA DE**</mark> <mark style="color:$success;"></mark>_<mark style="color:$success;">auth-client-config</mark>_<mark style="color:$success;">**:**</mark>

Si no es pot fer servir la comanda **`auth-client-config`**, s'ha de configurar el fitxer de manera manual. S'ha d'afegir el protocol `ldap` als fitxers `passwd` i `group`.

El fitxer `/etc/nsswitch.conf` hauria de quedar així:

```
passwd:         files systemd sss ldap
group:          files systemd sss ldap
shadow:         files
gshadow:        files
```

> A partir ara, quan s'engegui la màquina, buscarà el servidor LDAP per validar els usuaris, per tant:
>
> * **Cal tenir engegat el servidor** abans d'engegar el client.
> * **Cal apagar el client abans que el servidor**.
> * **No s'hauria de canviar l'adreça del servidor** (si es canvia, cal [reconfigurar el client LDAP](uf2-auteticacio-ldap.md#reconfigurar-el-client-ldap)).

### Reiniciar els serveis NSCD i NSLCD

Per fer efectius els canvis, és convenient reiniciar els serveis implicats. En el cas d'aquesta configuració és:

`sudo systemctl restart nscd nslcd`

### Comprovació autenticació LDAP

Si s'ha configurat correctament el client LDAP, es podran veure els usuaris i grups LDAP amb la comanda `getent passwd`:

```
usuari@ucxxx:~$ getent passwd
root:x:0:0:root:/root:/bin/bash
...
usuari:x:1000:1000:usuari,,,:/home/usuari:/bin/bash
usuariLDAP:*:10000:10000:usuariLDAP:/home/users/usuariLDAP:/bin/bash
```

S'haurien de veure tots els usuaris i grups, tant els locals com els configurats amb LDAP.

Els **usuaris i grups LDAP** es distingeixen per les següents dades:

* Tenen un `*` en lloc d'una `x` en el segon camp.
* L'identificador ha de ser superior o igual a **10000**.
* La carpeta personal ha d'estar dins de `/home/ldapxxx`.

<mark style="color:$success;">**Si no ha funcionat**</mark>

Si el fitxer no s'ha actualitzat, és possible que el servei no pugui fer-ho, però podem optar per:

1. Mostrar la identitat d'un usuari LDAP: `id usuariLDAP`
2. Si no funciona, **reinicia** el client.

Si no funciona, s'ha de tornar a [reconfigurar el client LDAP](uf2-auteticacio-ldap.md#reconfigurar-el-client-ldap).

Si l'identificador o el directori d'usuari no són correctes, cal modificar-los amb algun dels programes de gestió de LDAP (per exemple, phpldapadmin).

> A partir d'aquest moment, quan s'engegui la màquina, **buscarà el servidor LDAP per validar els usuaris**, per tant:&#x20;
>
> 1. Cal tenir engegat el servidor abans d'engegar el client.&#x20;
> 2. Cal apagar el client abans que el servidor.
> 3. No s'hauria de canviar l'adreça del servidor (si es canvia, cal [reconfigurar el client LDAP](uf2-auteticacio-ldap.md#reconfigurar-el-client-ldap) en totes les màquines).

## Validar usuaris per consola

Ara podem **validar-nos amb un usuari de LDAP a través de terminal** fent:

`sudo login usuariLDAP`&#x20;

o

&#x20;`sudo su usuariLDAP`

i veurem que s'ha creat la carpeta _home_ per aquest usuari de LDAP.

Per comprovar quin és el **directori de treball** de l'usuari es pot utilitzar la comanda `pwd`.

## Validar usuaris amb entorn gràfic

### Sistema actual GDM

Actualment, funciona sense necessitat d'aquests passos. En Ubuntu 24.04.3, l’autenticació gràfica d’usuaris LDAP funciona directament amb **GDM** (el gestor de sessions per defecte) quan el sistema fa servir **SSSD**.

Per tant, **no cal modificar cap configuració** del gestor gràfic en la majoria dels casos.<br>

### Sistema amb LightDM (antic)

Si el sistema utilitza LightDM (instal·lat manualment o en entorns lleugers), pot ser necessari habilitar el camp per introduir manualment el nom d’usuari.

Tingues en compte que _<mark style="color:red;">aquesta solució pot ser obsoleta per algunes versions.</mark>_

Per acabar, hem d'**activar l'opció de login d’Ubuntu** a la màquina client per poder escriure el nom de l’usuari.

Cal crear el fitxer `/etc/lightdm/lightdm.conf` i afegim les línies següents:

```
[Seat:*] 
greeter-hide-users=false
greeter-show-manual-login=true
```

* **greeter-hide-users**: amaga (true) o mostra (false) la llista dels últims usuaris que han accedit.
* **greeter-show-manual-login**: si és true, obliga a introduir manualment l'identificador de l'usuari.

Per carregar aquesta configuració cal reiniciar **lightdm** (es tancarà la sessió):

`sudo systemctl restart lightdm`   &#x20;

Per acabar, és convenient reiniciar el sistema i comprovar que pot entrar amb un usuari del domini.

No cal indicar res especial per distingir usuaris locals o usuaris LDAP.

## Reconfigurar el client LDAP

Es pot reconfigurar el client LDAP amb la comanda:

`sudo dpkg-reconfigure ldap-auth-config`

## LDAP no funciona, _Rescue me_!

A continuació tens una petita guia per revisar les configuracions. Tingues en compte que és per moltes versions d'Ubuntu, Xubuntu... i alguns paquets pot ser que no funcionin. Revisa'ls abans de descartar-los.

Passos a realitzar:

{% stepper %}
{% step %}
**Connectivitat**

Fixeu-vos si podeu fer _ping_ al servidor.
{% endstep %}

{% step %}
**Comprovació de NSS**

Obriu el fitxer /etc/nsswitch.conf i assegureu-vos que tingui ldap al passwd, group i shadow:

```
passwd:         files systemd sss ldap
group:          files systemd sss ldap
shadow:         files
gshadow:        files
```
{% endstep %}

{% step %}
**Fitxer /etc/ldap.conf**

També comproveu que el fitxer /etc/ldap.conf tingui les següents línies (adaptades a la IP i el domini corresponents):

```
# The distinguished name of the search base.
base dc=ldapprova,dc=test

# Another way to specify your LDAP server is to provide an
uri ldap://192.168.200.20
```

Fixeu-vos que la línia posa ldap:// i no ldapi://
{% endstep %}

{% step %}
**Revisió de paquets**

Teniu els següents paquets ben instal·lats (compte amb els paquets obsolets)?

* auth-client-config
* ldap-auth-client
* ldap-auth-config
* libnss-db
* libnss-ldap
* libpam-ldap
* nscd
* nss-updatedb
{% endstep %}

{% step %}
### Revisar els usuaris del sistema

Executeu l'ordre següent per mostrar el contingut del fitxer `/etc/passwd`:

`sudo getent passwd`

Us han d'aparèixer tots els usuaris (els locals i els de LDAP)
{% endstep %}

{% step %}
**Reiniciar nscd**

Executeu l'ordre següent:\
`sudo systemctl restart nscd`\
`sudo systemctl enable nscd`

Torneu a executar la comanda del pas 6:\
sudo getent passwd\
Us han d'aparèixer tots els usuaris (els locals i els de LDAP)
{% endstep %}

{% step %}
**Tornem a revisar el fitxer d'usuaris**

Torneu a executar la comanda:

`sudo getent passwd`\
Us han d'aparèixer tots els usuaris (els locals i els de LDAP)
{% endstep %}

{% step %}
**Potser el problema està al servidor**

Si tot això no ha funcionat. Revisa de nou la connectivitat amb el servidor, i la funcionalitat del servei LDAP al servidor.
{% endstep %}
{% endstepper %}

## Anul·lar la validació d'usuaris LDAP

Per fer que un client deixi de validar usuaris LDAP cal executar la següent comanda:

`sudo auth-client-config -t nss -p lac_ldap -r`

## Documentació i altres recursos <a href="#documentacio-i-altres-recursos" id="documentacio-i-altres-recursos"></a>

* Cloudera: [How PAM NSS SSD work together on Linux OS](https://community.cloudera.com/t5/Community-Articles/How-PAM-NSS-SSD-work-together-on-Linux-OS/ta-p/247879)
