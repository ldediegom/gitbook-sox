---
hidden: true
---

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

Una **ACL** (_Access Control List_ o _Llista de Control d'Accés_) permet especificar quins usuaris o grups poden accedir a un determinat recurs del sistema. 【1-e08699】

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

***

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

***

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

***

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

***

#### Exemples amb representació numèrica

| Comanda               | Resultat                                |
| --------------------- | --------------------------------------- |
| `chmod 700 fitxer`    | Només el propietari té accés total.     |
| `chmod 740 fitxer`    | Propietari: rwx, grup: r--, altres: --- |
| `chmod 751 directori` | Propietari: rwx, grup: r-x, altres: --x |
| `chmod 755 directori` | Propietari: rwx, grup: r-x, altres: r-x |
| `chmod 777 fitxer`    | Tothom té accés total.                  |

> ⚠️ Evita utilitzar `777` tret que sigui estrictament necessari per motius de seguretat.

***

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

***

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

***

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

