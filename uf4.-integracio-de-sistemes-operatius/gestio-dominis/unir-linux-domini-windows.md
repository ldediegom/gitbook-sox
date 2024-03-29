# Unir un client Linux a un domini Windows

## Introducció

Hi ha moltes formes d'unir un client Linux a un domini Windows amb Active Directory, però una de les més senzilles és utilitzar un programa que s'encarregui de fer la major part de la configuració del sistema en lloc de fer-ho manualment.

En versions més antigues d'Ubuntu Desktop podem trobar en els mateixos repositoris d'Ubuntu el paquet _**Likewise Open**_, però actualment l'empresa Beyond Trust s'ha encarregat d'actualitzar-lo i li ha canviat el nom per _**PBIS Open**_.

Es pot trobar tota la documentació sobre PBIS a la [web de Beyond Trust](https://www.beyondtrust.com/products/powerbroker-identity-services-ad-bridge/).

## Configuració inicial

### Configuració per un domini .local

> **ATENCIÓ**: en Ubuntu, quan es vol connectar amb un domini de tipus _**.local**_, s'ha de canviar una línia del fitxer `/etc/avahi/avahi-daemon.conf`:

```
#domain-name=local
domain-name=.alocal
```

I reiniciem el servei:

`sudo service avahi-daemon restart`

### Instal·lació del servei SSH

Per motius de seguretat, el programa PBIS Open utilitza ssh per comunicar-se amb el servidor de forma encriptada, i necessita que estigui instal·lat el servei de **ssh** en el client:

`sudo apt install ssh`

## Instal·lació del programa PBIS Open en el client Linux

### Descarregar PBIS Open

En un client Linux, descarregar el paquet PBIS Open que es pot trobar en aquesta web: [https://github.com/BeyondTrust/pbis-open/wiki](https://github.com/BeyondTrust/pbis-open/wiki).

Descarrega el paquet PBIS Open del repositori de [GitHub](https://github.com/BeyondTrust/pbis-open/releases) (32 o 64 bits) - escull segons versió de GNU/Linux - :

* [https://github.com/BeyondTrust/pbis-open/releases/download/9.1.0/pbis-open-9.1.0.551.linux.x86\_64.deb.sh](https://github.com/BeyondTrust/pbis-open/releases/download/9.1.0/pbis-open-9.1.0.551.linux.x86\_64.deb.sh)
* [https://github.com/BeyondTrust/pbis-open/releases/download/9.1.0/pbis-open-9.1.0.551.linux.x86.deb.sh](https://github.com/BeyondTrust/pbis-open/releases/download/9.1.0/pbis-open-9.1.0.551.linux.x86.deb.sh)

**Si no es disposa d'un entorn gràfic** amb navegador, també es pot descarregar mirant l'adreça de l'enllaç adequat i utilitzant la comanda wget - canvia els X.X.X per número adequat -:

```
wget https://github.com/BeyondTrust/pbis-open/releases/download/X.X.X/pbis-open-X.X.X.X.linux.x86_64.deb.sh
```

També es pot **instal·lar PBIS Open mitjançant APT** seguin les següents instruccions:

[https://repo.pbis.beyondtrust.com/apt.html](https://repo.pbis.beyondtrust.com/apt.html)

### Instal·lar PBIS Open

Per instal·lar-lo, primer se li han de donar permisos d'execució a l'arxiu descarregat.

`chmod a+x pbis-open-X.X.X.X.linux.x86_64.deb.sh`

Després ja es pot executar amb la següent comanda (cal respondre sempre yes):

`sudo ./pbis-open-X.X.X.X.linux.x86_64.deb.sh`

> **ATENCIÓ**: encara que s'obri una finestra per connectar al domini, abans s'ha de comprovar que el servei **lwsmd** es reinicia correctament, i després reiniciar el sistema:

```
sudo service lwsmd restart
sudo reboot
```

### Comprovar el servei PBIS

Amb la comanda **pbis status** es pot comprovar l'estat del servei:

```
usuari@ucxxx:~$ pbis status
[Authentication provider: lsa-activedirectory-provider]
    Status:        Online
    Mode:          Un-provisioned
    Domain:        ADXXX.LOCAL
    Domain SID:    S-1-5-21-3903495518-2577291124-556879616
    Forest:        adxxx.local
...
    [Domain: ADXXX]
        DNS Domain:       adxxx.local
        Netbios name:     ADXXX
        Forest name:      adxxx.local
...
        [Domain Controller (DC) Information]
            DC Name:              wsxxx.adxxx.local
            DC Address:           172.30.0.10
...
```

Amb la comanda **domainjoin-cli query** per verificar, de manera més simple, l'arrel d'accés al domini del client connectat:

```
usuari@ucxxx:~$ pbis status
Name = ucxxx
Domain = adxxx.local
Distinguished Name = CN=ucxxx,CN=Computers,DC=adxxx,DC=local
```

## Configuració dels paràmetres bàsics

### Comanda per configurar PBIS

Per canviar la configuració de PBIS s'utilitza la comanda **/opt/pbis/bin/config**.

Amb els paràmetres **--list** i **--dump** es poden veure tot els paràmetres que es poden configurar i quins valors tenen actualment:

```
usuari@ucxxx:~$ /opt/pbis/bin/config --dump
AllowDeleteTo ""
AllowReadTo ""
AllowWriteTo ""
...
DomainSeparator "\\"
SpaceReplacement "^"
...
AssumeDefaultDomain false
CreateHomeDir true
...
HomeDirPrefix "/home"
HomeDirTemplate "%H/%D/%U"
RemoteHomeDirTemplate ""
HomeDirUmask "022"
LoginShellTemplate "/bin/bash"
SkeletonDirs "/etc/skel"
UserDomainPrefix ""
...
Local_HomeDirTemplate "%H/local/%D/%U"
Local_HomeDirUmask "022"
Local_LoginShellTemplate "/bin/sh"
Local_SkeletonDirs "/etc/skel"
```

### Configuracions bàsiques

**Canviar el shell (intèrpret de comandes) per defecte que utilitzaran els usuaris**

`sudo /opt/pbis/bin/config LoginShellTemplate /bin/bash`

**Canviar el directori de treball dels usuaris**

Amb la següent comanda s'aconsegueix que el directori personal d'un usuari del domini sigui **/home/ADXXX/usuari**:

`sudo /opt/pbis/bin/config HomeDirTemplate %H/%D/%U`

**Obligar a posar el prefix del domini per validar els usuaris**

`sudo /opt/pbis/bin/config AssumeDefaultDomain false`

**Forçar la creació del directori d'usuari**

Com passa quan al configurar-lo per un sistema amb LDAP a GNU/Linux, farem servir la comanda:

`sudo pam-auth-update`

## Unir el client Linux a un domini Windows

### Utilitzant la interfície gràfica (_deprecated_)

Per obrir la interfície gràfica cal executar la següent comanda:

`sudo /opt/pbis/bin/domainjoin-gui`

![](../../.gitbook/assets/pbis-domini.png)

* Nom del domini: `adxxx.local`
* Deshabilitar l'opció _**Enable default user name prefix**_

Quan es faci clic a l'opció _**Join Domain**_ demanarà el nom d'un usuari que pugui unir màquines al domini (normalment l'administrador del domini) i la seva contrasenya.

> **ATENCIÓ**: si no es desmarca l'opció _**Enable default user name prefix**_ es podran validar usuaris sense haver de posar el prefix **ADXXX\\** o el sufix **@adxxx.local**, però pot portar a confusió si existeixen usuaris locals amb el mateix nom que usuaris del domini.

Ha de sortir el missatge _**SUCCESS**_ si s'ha unit correctament al domini.

Cal **reiniciar** el sistema per poder validar usuaris de l'Active Directory.

### Utilitzant la consola

Al executar la següent comanda, demanarà la contrasenya de l'usuari **administrador** del domini.

`sudo /opt/pbis/bin/domainjoin-cli join adxxx.local administrador`

Ha de sortir el missatge _**SUCCESS**_ si s'ha unit correctament al domini.

Cal **reiniciar el sistema** per poder validar usuaris de l'Active Directory.

## Comprovació i validació d'usuaris en el client Linux

Comprovar que el sistema pot mostrar els usuaris del domini Amb les comandes **getent passwd** i **getent group** es poden veure els usuaris del sistema:

```
usuari@ucxxx:~$ getent passwd
root:x:0:0:root:/root:/bin/bash
usuari:x:1000:1000:usuari,,,:/home/usuari:/bin/bash
...
ADXXX\administrador:PBIS:2035286516:2035286529::/home/ADXXX/administrador:/bin/bash
ADXXX\usuari:PBIS:2035287018:2035286529:usuari:/home/ADXXX/usuari:/bin/bash
ADXXX\ppadilla:PBIS:2035287130:2035286529:Pau Padilla :/home/ADXXX/ppadilla:/bin/bash
ADXXX\aamat:PBIS:2035287137:2035286529:Albert Amat:/home/ADXXX/aamat:/bin/bash

usuari@ucxxx:~$ getent group
root:x:0:
usuari:x:1000:
...
ADXXX\admins.^del^dominio:PBIS:2035286528:
ADXXX\usuarios^del^dominio:PBIS:2035286529:ADXXX\ppadilla
ADXXX\profes:PBIS:2035287124:ADXXX\ppadilla
ADXXX\alumnes:PBIS:2035287125:ADXXX\aamat
```

Els usuaris i grups del domini apareixen amb el nom NetBIOS del domini al davant: `ADXXX\usuari` o `ADXXX\grup`.

### Validar usuaris del domini Windows

Per poder validar usuaris des de l'**entorn gràfic** escrivint el seu identificador, cal [configurar **LightDM**](../../uf2.-sistemes-operatius-lliures-en-xarxa/uf2-gestio-dominis/uf2-auteticacio-ldap.md#validar-usuaris-amb-entorn-grafic).

Es poden utilitzar els mateixos formats que s'utilitzen en Windows:

* **ADXXX\usuari**
* **usuari@adxxx.local**

Si s'utilitza la primera forma quan es valida des de la **consola**, és possible que calgui posar **doble contrabarra** (\\) entre el nom del domini i el nom d'usuari:

```
sudo login ADXXX\\usuari
sudo login usuari@adxxx.local
```

## Desconnexió del domini i desinstal·lació de PBIS en el client Linux

### Desconnectar la màquina client del domini

**Mode gràfic (**_**deprecated**_**)**

`sudo /opt/pbis/bin/domainjoin-gui leave`

En el diàleg que apareixerà, només cal comprovar que les dades siguin correctes (nom de la màquina i el domini) i clicar el botó **Leave Domain**.

**Amb comandes**

`sudo /opt/pbis/bin/domainjoin-cli leave`

### Desinstal·lar el programa PBIS

Per desinstal·lar el programa després de desconnectar la màquina del domini:

`sudo /opt/pbis/bin/uninstall.sh uninstall`

> **ATENCIÓ**: abans d'utilitzar la comanda anterior, s'ha d'haver desconnectat la màquina del domini. A més, s'ha d'executar des de qualsevol carpeta que no sigui (o estigui dins de) la carpeta de pbis per tal que el programa de desinstal·lació pugui esborrar la carpeta.

### Desconnectar i desinstal·lar simultàniament

Amb la següent comanda es pot desinstal·lar i esborrar completament el programa sense haver de desconnectar la màquina del domini:

`sudo /opt/pbis/bin/uninstall.sh purge`

Si cal, també s'hauran de canviar els servidors DNS i eliminar el domini de cerca.

## Documentació i recursos

* **Font d'informació**: [Apunts SOX (Pere Sánchez)](http://moodlecf.sapalomera.cat/apunts/smx/sox/index.html?cap=3.5.0)
