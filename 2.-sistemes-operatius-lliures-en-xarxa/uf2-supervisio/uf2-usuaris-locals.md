# Gestió d'usuaris i grups locals

## Introducció

Linux és un sistema **multiusuari**, és a dir, està pensat perquè diversos usuaris treballin a la vegada, i cadascun disposa del seu propi espai d'emmagatzematge per desar els seus arxius.

Per accedir a un sistema Linux, **s'ha de tenir un compte d'usuari**, és a dir, una parella **login/password**.

Només l'usuari **`root`**, també anomenat **superusuari**, que en Linux és l'administrador del sistema, pot crear, eliminar o administrar els comptes d'usuari.

Els usuaris més importants que inclou Linux per defecte són:

* **`root`**: administrador del sistema (per defecte està deshabilitat).
* **`nobody`**: usuari anònim o convidat.
* **usuari principal**: és el que es crea en instal·lar el sistema.\
  Té permisos d'administrador (pot executar la comanda sudo).

### Dades bàsiques d'usuari

Les dades bàsiques d'un usuari són:

* Nom del compte o login.
* Identificador (és un número).
* Grup principal (és obligatori que cada usuari tingui un grup principal).
* Grups secundaris.
* Directori personal: carpeta on l'usuari té control total per crear arxius i carpetes.
* Intèrpret de comandes que utilitzarà per defecte (normalment /bin/bash).

## Tipus d'usuaris locals

Existeix 3 tipus d'usuaris:

1. **Usuari estàndard**
   * Cada usuari té la seva carpeta personal.
   * El seu directori arrel és `/home/<username>`.
   * Exemples: raul, sergi, mrodriguez, etc.&#x20;
   * Solen tenir un identificador d'usuari (**UID**) a partir del 1000.
2. **Usuaris de Sistema**
   * Són usuaris propis del sistema necessaris per algunes tasques que ha de realitzar el SO.
   * Aquest tipus d'usuari no pot ingressar al sistema amb un login normal.&#x20;
   * Exemples: mail, ftp, bin, sys, proxy, etc.&#x20;
   * Identificador entre **l'1** i el **999**.
3. **Usuari root (superusuari)**
   * Tot sistema operatiu GNU/Linux té un superusuari.
   * Té els màxims privilegis que li permetran efectuar qualsevol operació sobre el sistema.
   * És més segur treballar habitualment amb un usuari normal.
   * El seu directori **arrel** és `/root` i el seu número d’usuari (_**UID**_) és 0.

## Gestió d'usuaris

### Creació d'usuaris

#### Consideracions prèvies

Abans de crear un usuari hem de tenir en compte:

1. El nom d'usuari no pot començar amb un número ni incloure accents ni determinats símbols. Amb les comandes de creació i modificació es pot forçar.
2. Per defecte, quan es crea un usuari també es crea un grup amb el seu mateix nom i se li assigna com a grup principal.
3. La carpeta de l'usuari no es crea fins que l'usuari es valida per primera vegada.\
   Per defecte, les carpetes d'usuari es creen dins de /home i a dins es copia el contingut de la carpeta `/etc/skel`.

#### `useradd`

És la comanda del nucli GNU/Linux per la creació d’usuaris.

La seva estructura és:

`useradd [options] username`

{% hint style="warning" %}
Per defecte, aquesta comanda **no crearà** **ni la contrasenya ni el directori d’usuari**. A més li assignarà el terminal **/bin/sh** (el terminal Unix).
{% endhint %}

Si volem afegir i crear l’usuari a un directori, una shell i un nom específics, haurem de fer servir l’estructura:

&#x20;`sudo useradd -m –d /home/username -s /bin/bash -c “Nom complet” username`

Per exemple, per l’usuari daredevil seria…:

<pre class="language-bash"><code class="lang-bash"><strong>sudo useradd -m -d /home/daredevil -s /bin/bash -c “Red Guy” daredevil
</strong></code></pre>

Algunes opcions de gestió són:

| **Atribut**      | **Descripció**                                                                                 |
| ---------------- | ---------------------------------------------------------------------------------------------- |
| _-m_             | Permet crear el directori de l’usuari amb els permisos adequats.                               |
| _-d_             | Defineix el directori de l’usuari.                                                             |
| _-p \<password>_ | Definir la contrasenya d’usuari. Aquesta ha d’estar encriptada, si no es guardarà en text pla. |
| _-s_             | Defineix la shell.                                                                             |
| _-g_             | Grup principal de l’usuari.                                                                    |
| _-G_             | Grups secundaris d’usuari, separats per comes (els grups han d’existir)                        |
| _-c_             | Posar un comentari - Nom de l’usuari.                                                          |

<mark style="color:red;">**ERROR HABITUAL!**</mark>

{% hint style="danger" %}
És habitual cometre l’error de **no crear el directori d’usuari**. Això fa que l’usuari no pugui obrir sessió en mode gràfic.
{% endhint %}

<mark style="color:$success;">**Solució:**</mark>

1. Crear el directori
2. Assignar el permisos a l'usuari tant de propietat com de grup propietari.

Exemple de solució per l'usuari _daredevil_:

```bash
sudo mkdir /home/daredevil
sudo chown darevedil:daredevil /home/daredevil
```

#### `adduser` (_només Ubuntu_)

La comanda `adduser` serveix per **crear usuaris**.

* El nom d'usuari no pot començar amb un número ni incloure accents ni determinats símbols. Si es vol evitar aquestes restriccions, es pot afegir el paràmetre `--force-badname`.
*   Per defecte, quan es crea un usuari també **es crea un grup amb el seu mateix nom** i se li assigna com a **grup principal**.

    > Tot usuari ha de pertànyer, almenys, a un grup (**grup principal**), encara que pot ser de més d'un.
* La **carpeta de l'usuari** no es crea fins que l'usuari es valida per primera vegada. Per defecte, les carpetes d'usuari es creen dins de `/home` i se li copia el contingut de la carpeta `/etc/skel`.

Per **crear un usuari**:

`sudo adduser <usuari>`

_**--ingroup**_: Per crear un usuari i assignar-li el grup principal (el grup ha d'existir):

`sudo adduser <usuari> --ingroup <grup>`

_**-d, --home**_: Per assignar un directori $HOME a l'usuari:

`sudo adduser <usuari> --home <directori_home>`

### Eliminació d'usuaris

#### `userdel`&#x20;

És la comanda del nucli GNU/Linux per l’eliminació d’usuaris.

La seva estructura és:

`userdel [options] <usuari>`

Si volem eliminar completament tota la informació d’usuari s’ha de fer servir l’argument -r:

`userdel -r <usuari>`&#x20;

#### `deluser` (_només Ubuntu_)

La comanda `deluser` serveix per **eliminar usuaris**.

Per defecte, **no esborra la carpeta de l'usuari.** Si es vol esborrar cal afegir el paràmetre `--remove-home`.

Per **eliminar un usuari**:

`sudo deluser <usuari>`

_**--remove-home**_: Per eliminar un usuari i el seu directori:

`sudo deluser --remove-home <usuari>`

## Gestió de grups

### Consideracions prèvies

#### Fitxer `/etc/group`

El fitxer `/etc/group` és un fitxer de text que conté la informació dels grups.

El seu contingut de cada línia té la següent estructura:

```
group_name:password:GID:user_list
```

Descripció de les columnes:

* _**group\_name**_**:** El nom del grup.
* _**password**_**:** La clau del grup encriptada. Si està en blanc, vol dir que no s’ha definit clau de grup.
* _**GID**_**:** Identificador únic del grup.
* _**user\_list**_**:** Llistat dels noms d’usuaris membres del grup (separats per comes).

{% hint style="info" %}
Quan la clau està oculta (per defecte), la contrasenya es reemplaça per una **x** (com passa amb els usuaris a `/etc/passwd`).
{% endhint %}

#### Fitxer `/etc/gshadow`

La contrasenya encriptada del grup es guarda a `/etc/gshadow`

L’estructura d’aquest fitxer és:

```
Group_name:Encrypted_password:Group_administrators:Group_members
```

Descripció de les columnes:

* _**Group\_members**_: els membres del grup llistats aquí (en una llista delimitada per comes) són membres habituals i no administratius del grup.
* _**Group\_name**_**:** el nom del grup.
* _**Encrypted\_password**_**:** la contrasenya xifrada del grup.&#x20;
* _**Group\_administrators**_**:** els membres del grup que apareixen aquí (en una llista delimitada per comes) poden afegir o suprimir membres del grup mitjançant l'ordre gpasswd.

### Creació de grups

#### `groupadd`

És la comanda del nucli GNU/Linux per la creació de grups.

La seva estructura és:

`groupadd [options] <grup>`

Per exemple, per crear el grup x-men seria…:

```bash
sudo groupadd x-men
```

#### `addgroup` (_només Ubuntu_)

La comanda `addgroup` serveix per **crear grups**.

Per crear un grup:

`sudo addgroup <grup>`

### Eliminació e grups

#### `groupdel`

És la comanda del nucli GNU/Linux per l’eliminació de grups.

La seva estructura és:

`groupdel [options] <grup>`

Per exemple, per crear el grup x-men seria…:

```bash
sudo groupdel x-men
```

#### `delgroup` (_només Ubuntu_)

La comanda `delgroup` serveix per **eliminar grups**.

Per eliminar un grup:

`sudo delgroup <grup>`

## Assignar usuaris a grups

#### `usermod`      &#x20;

Aquesta és la comanda per la gestió dels usuaris. En el punt [usermod ](uf2-usuaris-locals.md#usermod)hi ha la versió completa.

Per canviar el grup principal de l'usuari:

`sudo usermod -g grup usuari`

Per afegir l'usuari a un \[o més] grups secundaris:

`sudo usermod -aG grup [grup ...] usuari`

#### `adduser` (_només Ubuntu_)

Pe**r** afegir un grup secundari a un usuari.

> **ATENCIÓ**: l'usuari i el grup han d'existir.

`sudo adduser <usuari> <grup>` &#x20;

## Gestió d'usuaris

### Gestió global

#### `usermod`

Serveix per **canviar la configuració** del compte d'usuari, així com bloquejar i desbloquejar un compte.

Per exemple, es pot canviar el nom del compte (**login**), el grup principal, afegir o treure grups secundaris, canviar i/o moure el directori personal, canviar l'identificador, l'intèrpret de comandes per defecte (**shell**)...

<figure><img src="../../.gitbook/assets/Captura de pantalla 2017-01-18 13.56.00.png" alt=""><figcaption></figcaption></figure>

Algunes opcions de configuració son:

_**-g**_: Per **canviar el grup principal** de l'usuari.

`sudo usermod -g <grup> <usuari>`

_**-aG**_: Per afegir l'usuari a **grups secundaris**:

`sudo usermod -aG <grup> [, grup ...] <usuari>`

_**-m**_: Per **canviar el directori personal** i moure els seus arxius al nou directori:

`sudo usermod -m -d /home/nou_dir <usuari>`

_**-l**_: Per **canviar el nom del compte (login)**:

`sudo usermod -l nou_login <usuari>`

_**-L**_: Per **deshabilitar temporalment un usuari**:

`sudo usermod -L <usuari>`

_**-u**_: Per **canviar l'identificador del compte (UID)**; `nou_id` no ha d'existir i ha de ser un número positiu:

`sudo usermod -u nou_id <usuari>`

### Donar privilegis d’usuaris

#### A través del fitxer `/etc/sudoers`

Per veure els permisos d’administrador, podem executar la comanda **`visudo`**.

<figure><img src="../../.gitbook/assets/unknown (5).png" alt=""><figcaption></figcaption></figure>

Aquesta comanda ens mostra el contingut del fitxer `/etc/sudoers`

Per donar privilegi d’administrador a un usuari, el podem afegir al grup **sudo**, o fer-ho més adequadament, modificant el fitxer `/etc/sudoers` amb una línia amb aquesta estructura.

```bash
username ALL=(ALL:ALL) ALL
```

#### Comanda `usermod`

Una altra manera és fer servir el grup que ja existeix, sudo, per atorgar els permisos.&#x20;

Això seria, afegint l’usuari al grup **sudo** com a grup secundari.

`sudo usermod -aG sudo <usuari>`&#x20;

Es pot verificar que l'usuari està al grup:

`groups <usuari>`

## Gestionar contrassenyes

#### `passwd`

La comanda `passwd` serveix per assignar o canviar contrasenyes.

> **ATENCIÓ:** un usuari que no tingui contrasenya no pot accedir al sistema.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2017-01-27 16.08.48.png" alt=""><figcaption></figcaption></figure>

Per **canviar la pròpia contrasenya** (la de l'usuari amb el què s'està treballant):

`passwd`

Per **canviar la contrasenya d'un usuari** (si no es posa usuari, s'aplica al root):

`sudo passwd <usuari>`

Per **eliminar la contrasenya d'un usuari i desactivar el compte** (si no es posa usuari, s'aplica al root):

`sudo passwd -l <usuari>`

Per **reactivar un compte d'usuari** que ha estat desactivat.

`sudo passwd -u <usuari>`

Per obtenir la informació bàsica del password d’usuari.

`sudo passwd -S <usuari>`

<figure><img src="../../.gitbook/assets/Captura de pantalla 2017-01-18 11.06.34.png" alt=""><figcaption></figcaption></figure>

#### `chage`

La comanda `chage` modifica el nombre de dies entre canvis de contrasenya i la data de l'últim canvi de contrasenya.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2017-01-18 10.59.25.png" alt=""><figcaption></figcaption></figure>

Per **obtenir informació sobre el 'temps de vida' de la contrasenya** d'un usuari:

`sudo chage -l <usuari>`

<figure><img src="../../.gitbook/assets/Captura de pantalla 2017-01-18 11.02.10.png" alt=""><figcaption></figcaption></figure>

Per definir que l'usuari hagi de **canviar la contrasenya cada cert temps** (per exemple 90 dies):

`sudo chage -M 90 <usuari>`

Per **definir la data de caducitat de la contrasenya** (per exemple el proper 17/03/2020):

`sudo chage -E 2020-03-17 <usuari>`

Per **comprovar que els canvis s'han fet** efectius:

`sudo chage -l <usuari>`

## Iniciar sessió amb un altre usuari

Per **canviar a l'usuari root**:

`sudo su`

Per **canviar a un altre usuari**:

`sudo login <usuari>`

Per **tancar sessió** i tornar a la sessió del l'usuari anterior:

`exit`

Per saber en **quin usuari hem iniciat la secció actual**.

`whoami`

## Veure informació sobre usuaris i grups

### Per comandes

#### `id [usuari]`

Mostra l'**identificador de l'usuari (**_**UID**_**)** i els **grups al que pertany** un usuari juntament amb l'idenficacio de cada grup (_GID_).

```
usuari@ucxxx:~$ id
uid=1000(usuari) gid=1000(usuari) grupos=1000(usuari),4(adm),27(sudo),46(plugdev)...
```

#### `groups [usuari]`

Mostra els **noms dels grups als que pertany** un usuari.

> **NOTA**: El primer grup que apareix és el grup principal.

```
usuari@ucxxx:~$ groups
usuari adm cdrom sudo dip plugdev lpadmin sambashare
```

#### `getent passwd`

Mostra **informació dels usuaris**. Pot mostrar tant usuaris locals com de domini (LDAP).

```
usuari@ucxxx:~$ getent passwd
root:x:0:0:root:/root:/bin/bash
...
usuari:x:1000:1000:usuari,,,:/home/usuari:/bin/bash
alumne:x:1001:1001:alumne,,,,:/home/alumne:/bin/bash
profe:x:1002:1002:profe,,,,:/home/profe:/bin/bash
director:x:1003:1003:,,,:/home/director:/bin/bash
```

Per mostrar la **informació només d'un usuari**:

`getent passwd <usuari>`

#### `getent group`

Mostra **informació dels grups**. Pot mostrar tant grups locals com de domini (LDAP).

```
usuari@ucxxx:~$ getent group
root:x:0:
sudo:x:27:usuari
...
usuari:x:1000:
alumnes:x:1001:
profes:x:1002:usuari,director
director:x:1003:
```

Per mostrar la **informació només d'un grup**:

`getent group <grup>`

### A través de fitxers especials

Hi ha diversos **fitxers de text** en Linux que contenen informació referent als usuaris i als grups d'usuaris donats d'alta en el sistema.

> **ATENCIÓ**: aquests arxius no s'haurien d'editar mai manualment! Per fer qualsevol modificació s'han d'utilitzar les comandes corresponents.

#### `/etc/passwd`

Conté la informació dels usuaris del sistema (nom, directori _home_, etc.). Cada línia és un usuari i cada camp està separat per dos punts (:).

`usuari:x:1000:2000:usuari local,,,:/home/usuari:/bin/bash`

<figure><img src="../../.gitbook/assets/unknown (6).png" alt=""><figcaption></figcaption></figure>

* **usuari** és el nom del compte d'usuari (_login_)
* Una **x** en el segon camp indica que la contrasenya es troba en l'arxiu /etc/shadow.
* El tercer camp (**1000**) és l'identificador d'usuari.
* El quart camp (**2000**) és l'identificador del grup principal.
* Nom complet de l'usuari i altres informacions.
* `/home/usuari` és el directori personal de l'usuari.
* `/bin/bash` és l'intèrpret de comandes per defecte.

Només el _**root**_ té permís d'escriptura. La resta d'usuaris i grups només el poden llegir.

#### `/etc/shadow`

Conté les contrasenyes xifrades dels usuaris i altres informacions, com per exemple l'última data en què es va canviar la contrasenya.

Només l'usuari _**root**_ té permís d'escriptura. Només el grup _**shadow**_ té permís de lectura. La resta d'usuaris i grups no tenen cap permís.

<figure><img src="../../.gitbook/assets/unknown (7).png" alt=""><figcaption></figcaption></figure>

#### `/etc/group`

Conté la informació dels grups. Cada camp està separat per:

`profes:x:2000:usuari,director`

<figure><img src="../../.gitbook/assets/unknown (8).png" alt=""><figcaption></figcaption></figure>

* **profes** és el  nom del grup.
* Una **x** en el segon camp indica que la contrasenya es troba en l'arxiu /etc/gshadow.
* El tercer camp (**2000**) és l'identificador del grup.
* El quart camp conté els noms dels usuaris que tenen aquest grup com a grup secundari.

Només el _**root**_ té permís d'escriptura. La resta d'usuaris i grups només el poden llegir.

#### `/etc/gshadow`

Conté les contrasenyes encriptades dels grups.

Només l'usuari _**root**_ té permís d'escriptura. Només el grup _**shadow**_ té permís de lectura.

#### `/etc/sudoers`

Conté informació sobre els drets i privilegis dels usuaris.

La seva principal utilitat és afegir grups o usuaris que puguin actuar com a administradors (que puguin utilitzar la comanda `sudo`).

Només el _**root**_ pot llegir-lo. La resta d'usuaris i grups no tenen cap permís.

Per editar-lo cal utilitzar la comanda `visudo`:

`sudo visudo`

```
# User privilege specification
root    ALL=(ALL:ALL) ALL

# Allow members of group sudo to execute any command
%sudo    ALL=(ALL:ALL) ALL
```

## Referències

* **Apunts SOX Pere Sánchez:** [Arxius de Linux relacionats amb els usuaris i grups locals](http://moodlecf.sapalomera.cat/apunts/smx/sox/index.html?cap=92\&ref=2023)
* **Apunts SOX Pere Sánchez:** [Crear i eliminar usuaris locals](http://moodlecf.sapalomera.cat/apunts/smx/sox/index.html?cap=120\&ref=2321)
* **Red Hat Documentation:** [Capítulo 6. Gestión de usuarios desde la línea de comandos](https://docs.redhat.com/es/documentation/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/managing-users-from-the-command-line_configuring-basic-system-settings)
* **Learn Ubuntu:** [User Management in Ubuntu](https://learnubuntu.com/user-management/)
