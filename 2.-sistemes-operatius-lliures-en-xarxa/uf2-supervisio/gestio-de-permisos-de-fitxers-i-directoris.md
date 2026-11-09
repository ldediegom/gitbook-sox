# Gestió de permisos de fitxers i directoris

## Introducció

### Permisos, drets i privilegis

En els sistemes multiusuari és molt important definir què pot fer cada usuari sobre els recursos del sistema.

| Concepte       | Descripció                                                           | Exemples                                                           |
| -------------- | -------------------------------------------------------------------- | ------------------------------------------------------------------ |
| **Permisos**   | Determinen què pot fer un usuari o grup sobre un recurs concret.     | Lectura, escriptura, execució.                                     |
| **Drets**      | Defineixen les capacitats generals d'un usuari dins del sistema.     | Crear usuaris, instal·lar programari, modificar configuracions.    |
| **Privilegis** | Permeten executar accions que afecten el sistema operatiu o l'equip. | Aturar el sistema, canviar la data i hora, instal·lar aplicacions. |

### Access Control List (ACL)

Una **ACL** (_Access Control List_ o _Llista de Control d'Accés_) permet especificar quins usuaris o grups poden accedir a un determinat recurs del sistema. Ho permet fer de manera més precissa que amb el model tradicional.

Pots veure'n més d'aquest model al punt [Control d'accés avançat amb ACL](gestio-de-permisos-de-fitxers-i-directoris.md#control-dacces-avancat-amb-acl).

## Permisos a GNU/Linux

Els permisos bàsics de GNU/Linux són:

| Permís | Significat         |
| ------ | ------------------ |
| `r`    | Read (lectura)     |
| `w`    | Write (escriptura) |
| `x`    | Execute (execució) |

Aquests permisos tenen un comportament diferent segons si s'apliquen a fitxers o directoris.&#x20;

### Permisos sobre fitxers

| Permís | Descripció                                            |
| ------ | ----------------------------------------------------- |
| `r`    | Permet veure el contingut del fitxer.                 |
| `w`    | Permet escriure o modificar el contingut del fitxer.  |
| `x`    | Permet executar el fitxer si és un programa o script. |

### Permisos sobre directoris

| Permís | Descripció                                                      |
| ------ | --------------------------------------------------------------- |
| `r`    | Permet llistar els fitxers i directoris que conté el directori. |
| `w`    | Permet crear, eliminar i reanomenar fitxers i directoris.       |
| `x`    | Permet accedir al directori i entrar-hi amb `cd`.               |

## Estructura dels permisos

### Model UGO

Els permisos es divideixen en tres grups principals:

| Domini            | Símbol | Descripció                                  |
| ----------------- | ------ | ------------------------------------------- |
| Usuari propietari | `u`    | Propietari del fitxer o directori.          |
| Grup propietari   | `g`    | Grup al qual pertany el fitxer o directori. |
| Altres usuaris    | `o`    | Resta d'usuaris del sistema.                |

Cada grup pot tenir permisos independents de:

* Lectura (`r`)
* Escriptura (`w`)
* Execució (`x`)

### Representació de permisos

Per exemple:

```
-rwxr-xr-x
```

Es divideix de la manera següent:

| Bloc  | Significat                     |
| ----- | ------------------------------ |
| `-`   | Tipus de fitxer                |
| `rwx` | Permisos del propietari        |
| `r-x` | Permisos del grup              |
| `r-x` | Permisos de la resta d'usuaris |

***

#### Exemple real

```bash
ls -l /usr/bin/top
```

Sortida:

```
-rwxr-xr-x 1 root root 68524 2011-12-19 07:18 /usr/bin/top
```

Interpretació:

| Camp               | Descripció          |
| ------------------ | ------------------- |
| `-rwxr-xr-x`       | Permisos            |
| `1`                | Nombre d'enllaços   |
| `root`             | Propietari          |
| `root`             | Grup                |
| `68524`            | Mida en bytes       |
| `2011-12-19 07:18` | Data de modificació |
| `/usr/bin/top`     | Nom del fitxer      |

## Modificació de permisos amb chmod

### Canvi de permisos

Només poden modificar permisos:

* `root`
* l'usuari propietari
* un usuari amb permisos d'administrador (`sudo`)

La modificació es fa amb la comanda:

```bash
chmod
```

Hi ha dues formes d'utilitzar-la:

1. Representació simbòlica
2. Representació numèrica

### `chmod` amb representació simbòlica

#### Sintaxi

```bash
chmod {a,u,g,o}{+,-,=} {r,w,x} objecte
```

#### Dominis

| Símbol | Significat        |
| ------ | ----------------- |
| `a`    | Tots els usuaris  |
| `u`    | Usuari propietari |
| `g`    | Grup propietari   |
| `o`    | Altres usuaris    |

#### Operacions

| Operador | Funció                                   |
| -------- | ---------------------------------------- |
| `+`      | Afegeix permisos                         |
| `-`      | Elimina permisos                         |
| `=`      | Assigna exactament els permisos indicats |

#### Permisos

| Símbol | Significat |
| ------ | ---------- |
| `r`    | Lectura    |
| `w`    | Escriptura |
| `x`    | Execució   |

#### Exemples amb representació simbòlica

| Comanda              | Descripció                                            |
| -------------------- | ----------------------------------------------------- |
| `chmod u=rwx fitxer` | Assigna lectura, escriptura i execució al propietari. |
| `chmod a+ x fitxer`  | Dona permís d'execució a tots els usuaris.            |
| `chmod g=u fitxer`   | Iguala els permisos del grup als del propietari.      |
| `chmod o-r fitxer`   | Elimina el permís de lectura dels altres usuaris.     |
| `chmod go= fitxer`   | Elimina tots els permisos del grup i dels altres.     |

### `chmod` amb representació numèrica

#### Sintaxi

```bash
chmod xxx objecte
```

On:

* Primera x → propietari
* Segona x → grup
* Tercera x → altres usuaris

#### Valor dels permisos

| r | w | x | Valor |
| - | - | - | ----- |
| 0 | 0 | 0 | 0     |
| 0 | 0 | 1 | 1     |
| 0 | 1 | 0 | 2     |
| 0 | 1 | 1 | 3     |
| 1 | 0 | 0 | 4     |
| 1 | 0 | 1 | 5     |
| 1 | 1 | 0 | 6     |
| 1 | 1 | 1 | 7     |

#### Resum de combinacions habituals

| Valor | Permisos |
| ----- | -------- |
| 7     | rwx      |
| 6     | rw-      |
| 5     | r-x      |
| 4     | r--      |
| 3     | -wx      |
| 2     | -w-      |
| 1     | --x      |
| 0     | ---      |

#### Exemples amb representació numèrica

| Comanda               | Resultat                                |
| --------------------- | --------------------------------------- |
| `chmod 700 fitxer`    | Només el propietari té accés total.     |
| `chmod 740 fitxer`    | Propietari: rwx, grup: r--, altres: --- |
| `chmod 751 directori` | Propietari: rwx, grup: r-x, altres: --x |
| `chmod 755 directori` | Propietari: rwx, grup: r-x, altres: r-x |
| `chmod 777 fitxer`    | Tothom té accés total.                  |

> ⚠️ Evita utilitzar `777` tret que sigui estrictament necessari per motius de seguretat.

## Modificació de propietaris i grups

#### Comandes utilitzades

| Comanda | Funció                                         |
| ------- | ---------------------------------------------- |
| `chown` | Modifica el propietari i opcionalment el grup. |
| `chgrp` | Modifica únicament el grup.                    |

Només poden executar aquestes modificacions:

* `root`
* propietari
* usuaris amb `sudo`

### Comanda `chown`

#### Sintaxi

```bash
chown [opcions] usuari:grup objecte
```

#### Exemples

**Modificar només l'usuari**

```bash
chown joan fitxer1
```

**Modificar només el grup**

```bash
chown :grup1 fitxer1
```

**Modificar usuari i grup**

```bash
chown joan:grup1 fitxer1
```

**Modificació recursiva**

```bash
chown -R laura directori1
```

Canvia el propietari del directori i de tot el seu contingut.

### Comanda `chgrp`

#### Sintaxi

```bash
chgrp [opcions] grup objecte
```

#### Exemple

```bash
chgrp grup1 directori1
```

#### Modificació recursiva

```bash
chgrp -R grup1 *
```

Canvia el grup de tots els fitxers i directoris del directori actual.

## Resum de comandes

| Comanda                     | Funció                        |
| --------------------------- | ----------------------------- |
| `chmod`                     | Canviar permisos              |
| `chmod 755 fitxer`          | Assignar permisos numèrics    |
| `chmod u+x fitxer`          | Afegir execució al propietari |
| `chown usuari fitxer`       | Canviar propietari            |
| `chown usuari:grup fitxer`  | Canviar propietari i grup     |
| `chgrp grup fitxer`         | Canviar grup                  |
| `chown -R usuari directori` | Canvi recursiu de propietari  |
| `chgrp -R grup directori`   | Canvi recursiu de grup        |

## Control d'accés avançat amb ACL

### Què és una ACL?

Els sistema tradicional d'assignació de permisos que hem vist, l'UGO (_Usuari, Grup i Altres_), en ocasions és poc adaptatiu. Permet una seria de configuracions genèriques què no deixen espai per les excepcions o configuracions més concretes per usuaris o grups. **ACL** (_**Access Control List**_**&#x20;o&#x20;**_**Llista de Control d'Accés**_) és un mecanisme que permet assignar permisos específics a usuaris o grups concrets sobre fitxers i directoris permetent la defició d' excepcions sense modificar els permisos generals del recurs.

{% hint style="warning" %}
Les ACL no eliminen ni substitueixen la configuració tradicional de permisos UGO (Usuari, Grup i Altres), sinó que l'amplien afegint permisos específics per a determinats usuaris o grups.
{% endhint %}

#### Característiques

ACL ens permet:

* **Permisos personalitzats:** Assignar permisos de manera més específica a usuaris o grups concrets.
* **Major flexibilitat que el model UGO**
* **Control d'accés a recursos més avançada:** Permeten una millor gestió a entorns amb molts usuaris i amb compartició de recursos.&#x20;

### Visualitzar ACL

Per consultar les ACL associades a un fitxer o directori s'utilitza la comanda:

```bash
getfacl objecte
```

**Exemple**

```bash
getfacl fitxer1
```

Possible sortida:

```
# file: fitxer1
# owner: joan
# group: alumnes
user::rw-
user:anna:r--
group::r--
mask::r--
other::---
```

**Interpretació**

| Entrada         | Significat                                                                                                                                 |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `user::rw-`     | Permisos del propietari: lectura i escriptura.                                                                                             |
| `user:anna:r--` | Permisos assignats a l'usuari anna: lectura.                                                                                               |
| `group::r--`    | Permisos del grup propietari: lectura.                                                                                                     |
| `mask::r--`     | Límit màxim dels permisos efectius per al grup propietari i per a les entrades ACL d'usuaris i grups. En aquest cas, només permet lectura. |
| `other::---`    | La resta d'usuaris no tenen cap permís sobre l'objecte.                                                                                    |

### Assignar ACL

Per afegir una ACL s'utilitza la comanda:

```bash
setfacl -m entrada objecte
```

on:

* **- m:** indica que es vol fer una moficicació.
* **entrada:** l'assignació de permisos que es vol fer.
* **objecte:** nom del fitxer o directori.

**Sintaxi per un usuari (`u`)**

```bash
setfacl -m u:usuari:permisos objecte
```

o bé per un grup (`g`)

```bash
setfacl -m g:grup:permisos objecte
```

#### **Exemples d'assignació de permisos ACL**

**A) Donar permisos de lectura i escriptura a un usuari**

Assignem a l'usuari (`u`) **anna** (`anna`) permisos de lectura i escriptura (`rw`) sobre l'objecte _fitxer1._

```bash
setfacl -m u:anna:rw fitxer1
```

**B) Donar permisos complets a un usuari**

Assignem a l'usuari (`u`) _**anna**_ (`anna`) permisos totals (`rwx`) sobre l'objecte _fitxer1_.

```bash
setfacl -m u:anna:rwx fitxer1
```

**C) Donar permisos de lectura a un grup**

Assignem al grup (`g`) **practiques** (`practiques`) permisos de lectura (`r`) sobre l'objecte _fitxer1_.

```bash
setfacl -m g:practiques:r fitxer1
```

### Eliminar ACL

#### Eliminar una entrada concreta

Per eliminar una entrada concreta farem servir el paràmentre `x` i indicarem l'usuari o grup al que volem eliminar de la configuració i l'objecte sobre el que ho volem fer.

```bash
setfacl -x entrada objecte
```

**Exemple**

Eliminem l'entrada ACL (`-x`) assignada a l'usuari (`u`) **anna** sobre l'objecte _fitxer1_.

```bash
setfacl -x u:anna fitxer1
```

#### Eliminar totes les ACL

Per eliminar totes les ACL d'un fitxer o directori farem servir el paràmetre `b` i indicarem l'objecte on volem aplicar aquesta eliminació de configuració.

```bash
setfacl -b objecte
```

**Exemple**

```bash
setfacl -b fitxer1
```

{% hint style="info" %}
Després d'executar aquesta comanda només es conservaran els permisos tradicionals UGO.
{% endhint %}

### ACL per defecte en directoris

Es poden definir permisos ACL per defecte perquè els nous fitxers i directoris heretin automàticament aquests permisos. És el que s'anomena **ACL per defecte**.

**Sintaxi**

```bash
setfacl -m d:u:usuari:permisos directori
```

**Exemple**

Assignem una ACL per defecte a l'usuari (`u`) **anna** (`anna`) perquè hereti permisos complets (`rwx`) sobre tots els fitxers i directoris nous que es creïn dins del **directori** (`d`) _projectes_.

```bash
setfacl -m d:u:anna:rwx projectes
```

Per tant, qualsevol fitxer creat dins del directori `projectes` heretarà aquests permisos per a l'usuari `anna`.

> **Important:** Aquesta configuració no modifica els permisos dels fitxers ja existents dins del directori, sinó únicament els que es creïn a partir d'aquell moment.

### Resum de comandes ACL

| Comanda                             | Funció                                    |
| ----------------------------------- | ----------------------------------------- |
| `getfacl fitxer`                    | Mostra les ACL del fitxer.                |
| `setfacl -m u:anna:rwx fitxer`      | Assigna permisos ACL a l'usuari anna.     |
| `setfacl -m g:professors:r fitxer`  | Assigna permisos ACL a un grup.           |
| `setfacl -x u:anna fitxer`          | Elimina una ACL concreta.                 |
| `setfacl -b fitxer`                 | Elimina totes les ACL del fitxer.         |
| `setfacl -m d:u:anna:rwx directori` | Defineix ACL per defecte en un directori. |

> Les ACL són especialment útils quan un usuari necessita permisos específics sobre un fitxer o directori sense modificar els permisos del propietari, del grup o de la resta d'usuaris.
