# Gestió avançada de LDAP

## Exemples d'ús de _ldap-utils_

Partim del supósit que:

* Domini: **ldapxxx.local**
* IP del servidor: **192.168.1.1**

### Obtenir dades dels objectes

#### **Obtenir totes les entrades del directori**

**Si ho volem fer locament:**

Per obtenir totes les entrades (objectes) del directori LDAP des de la base:

```
ldapsearch -x -b "ldapxxx,dc=local"
```

On:

* **-x** → utilitza autenticació simple.
* **-b "dc=ldapxxx,dc=local"** → base de la cerca; indica des de quin punt del directori es farà la consulta.

Si el servidor requereix autentitcació:

```
ldapsearch -x -D "cn=admin,dc=ldapxxx,dc=local" -W -b "dc=ldapxxx,dc=local" -LLL
```

On:

* **-D "cn=admin,dc=ldapxxx,dc=local"** → DN de l’usuari que es connecta al servidor (administrador).
* **-W** → demana la contrasenya abans d’executar la cerca.
* **-LLL** → mostra la sortida en format LDIF simplificat (sense comentaris ni informació extra).

També es pot utilitzar un filtre explícit, per retornar totes les entrades que tenen almenys un **objectClass**:

```
ldapsearch -x -b "dc=ldapxxx,dc=local" "objectClass=*"
```

On:

* **"objectClass=\*"** → indica que es filtri per totes les classes d'objecte. Per tant, que es mostrin tots.

**Si es vol fer remotament:**

```
ldapsearch -x -H ldap://192.168.1.1 -b "ldapxxx,dc=local"
```

On:

* **-H ldap://192.168.1.1** → especifica el servidor LDAP.

#### **Obtenir tots els objectes d'una UO**

Es pot espeficificar una base diferent, indicant una UO de l'esctructura. En aquest exemple, es fa per la unitat organitzativa **people**.

```
ldapsearch -x -b "ou=people,dc=ldapxxx,dc=local" "objectClass=*"
```

#### **Obtenir tots els objectes amb tots els atributs**

```
ldapsearch -x -LLL -b "dc=ldapxxx,dc=local" '(&)'
```

On:&#x20;

* **'(&)'** → És una filtre per indicar que "no hi ha cap condició".

#### **Obtenir tots els DN**

```
ldapsearch -x -LLL -b "dc=ldapxxx,dc=local" dn
```

On:

* **dn** → indica que només es mostri l’atribut **DN** de cada entrada.

Si el servidor requereix autentitcació:

```
ldapsearch -x -D "cn=admin,dc=ldapxxx,dc=local" -W -b "dc=ldapxxx,dc=local" -LLL dn
```

**Mostrar tots els DN remotament:**

```
ldapsearch -x -H ldap://192.168.1.1 -D "cn=admin,dc=ldapxxx,dc=local" -W -b "dc=ldapxxx,dc=local" -LLL dn
```

#### **Obtenir tots els usuaris**

L'únic que s'ha de fer és especificar el típus d'objecte que es vol obtenir:

```
ldapsearch -x -LLL -b "dc=ldapxxx,dc=local" "(objectClass=inetOrgPerson)" uid cn
```

Remotament seria:

```
ldapsearch -x -H ldap://192.168.1.1 -D "cn=admin,dc=ldapxxx,dc=local" -W -b "dc=ldapxxx,dc=local" -LLL "(objectClass=inetOrgPerson)" uid cn
```

On:

* **"(objectClass=inetOrgPerson)"** → filtre LDAP; només retorna les entrades que són usuaris. S'ha de tenir
* **uid cn** → només mostrar els atributs **uid** (identificador) i **cn** (nom complet).

Exemple:

```bash
usuari@usxxx:~$ ldapsearch -x -LLL -b "dc=ldapxxx,dc=local" "(objectClass=inetOrgPerson)" uid cn
dn: uid=goku,ou=people,dc=ldapxxx,dc=local
uid: goku
cn: Goku

dn: uid=vegeta,ou=people,dc=ldapxxx,dc=local
uid: vegeta
cn: Vegeta
```

#### **Obtenir les classes d'un objecte**

En aquest cas es retorna les classes (**objectclass**) de l'objecte **Director**, que està dins de la UO **gestio**, que està dins de la UO **people:**

```
ldapsearch -x -b "cn=Director,ou=gestio,ou=people,dc=ldapxxx,dc=local" objectclass
```

#### **Esborrar una entrada**&#x20;

L'entrada no ha de contenir subelements

```
ldapdelete -D "cn=admin,dc=ldapxxx,dc=local" -W -x "cn=Director,ou=gestio,ou=usuaris,ou=xxx,dc=ldapxxx,dc=local"
```

### Obtenir diferents dades de l'esquema

**Obtenir totes les classes de l'esquema**

```
ldapsearch -x -s base -b "cn=subschema" objectclasses
```

**Obtenir tots els tipus d'atributs**

```
ldapsearch -x -s base -b "cn=subschema" attributeTypes
```

**Obtenir totes les regles aplicables als atributs**

```
ldapsearch -x -s base -b "cn=subschema" matchingrules
```

### Exportació o backup de l'estructura

#### Exportació des del servidor

**Opció 1:** Amb `ldapsearch` podem crear un fitxer a partir d'un consulta completa:

```
ldapsearch -x -LLL -b "dc=ldapxxx,dc=local" > backup.ldif
```

amb l'usuari **admin**:

```
ldapsearch -x -D "cn=admin,dc=ldapxxx,dc=local" -W -b "dc=ldapxxx,dc=local" -LLL > backup.ldif
```

On:

* **> backup.ldif** → redirigeix la sortida cap a un fitxer anomenat `backup.ldif`.

**Opció 2:** Fent servir la comanda `slapcat`

Si el servidor utilitza OpenLDAP, es pot fer un backup complet amb:

```
slapcat > backup.ldif
```

#### Exportar LDAP remotament

Com es poden fer consultes des de clients, és una manera per poder fer backups remots.

```
ldapsearch -x -H ldap://192.168.1.1 -D "cn=admin,dc=ldapxxx,dc=local" -W -b "dc=ldapxxx,dc=local" -LLL > backup.ldif
```

***

## LDAPscripts

**LDAPScripts** és un conjunt d'eines de scripts que permeten automatitzar la gestió d'usuaris i grups en directoris LDAP.

```
ldapsearch -x -H ldap://192.168.1.1 -D "cn=admin,dc=ldapxxx,dc=local" -W -b "dc=ldapxxx,dc=local" -LLL > backup.ldif
```

On:

* **-H ldap://192.168.1.1** → indica l’adreça del servidor LDAP al qual es connectarà la comanda.



### Instal·lació de LDAPscripts

Primer de tot, en el servidor cal instal·lar el paquet ldapscripts:

`sudo apt install ldapscripts`

Configurar els paràmetres del fitxer `/etc/ldapscripts/ldapscripts.conf` (se suposa que ja s'han creat les unitats organitzatives **usuaris**, **grups** i **maquines**):

```
SERVER="ldap://localhost"
SUFFIX="dc=ldapxxx,dc=local"
USUFFIX="ou=usuaris"
GSUFFIX="ou=grups"
MSUFFIX="ou=maquines"
BINDDN="cn=admin,dc=ldapxxx,dc=local"
BINDPWDFILE="/etc/ldapscripts/ldapscripts.passwd"
UIDSTART=10000
GIDSTART=10000
MIDSTART=20000
GCLASS="posixGroup"
USHELL="/bin/bash"
UHOMES="/home/ldapxxx/%u"
```

Guardar la contrasenya en un arxiu per permetre l'accés :

```
sudo sh -c "echo -n 'contrasenyaLDAP' > /etc/ldapscripts/ldapscripts.passwd"
sudo chmod 400 /etc/ldapscripts/ldapscripts.passwd
```

### Comandes per gestionar usuaris

Algunes comandes que es poden utilitzar (sempre amb `sudo`):

* `ldapadduser usuari grup`
* `ldapsetpasswd usuari`
* `ldapdeleteuser usuari`
* `ldapaddgroup grup`
* `ldapdeletegroup grup`
* `ldapsetprimarygroup usuari grup`
* `ldapaddusertogroup usuari grup`
* `ldapdeleteuserfromgroup usuari grup`

> Els usuaris es creen amb una plantilla diferent que la que utilitza **phpldapadmin**, per tant poden haver-hi camps diferents.

Es pot fer la prova creant un usuari amb aquests scripts i observar la diferència amb **phpldapadmin** o amb **ldapsearch**.

### Inconvenients d'aquest mètode

* No hi ha comandes per crear, modificar o eliminar unitats organitzatives.
* Tots els usuaris es creen en la unitat organitzativa configurada en l'arxiu `/etc/ldapscripts/ldapscripts.conf`.
  * Si es volen crear en altres unitats organitzatives, s'ha d'anar canviant la configuració en aquest arxiu.

## Documentació i recursos

* **Apunts SOX Pere Sánchez:** [Gestió d'usuaris i grups LDAP amb scripts](http://moodlecf.sapalomera.cat/apunts/smx/sox/index.html?cap=1.9.19)
* **Apunts SOX Pere Sánchez:** [Altres comandes per gestionar LDAP](http://moodlecf.sapalomera.cat/apunts/smx/sox/index.html?cap=1.9.21)
