# Accedir a recursos compartits en Windows des de Linux

## Accedir a arxiu i carpetes compartits en Windows des de Linux

### Compartir arxius i carpetes en Windows

[Apunts sobre compartició d'arxius i carpetes en Windows](../../uf3.-comparticio-de-recursos-i-seguretat/uf3-compartir-recursos-windows/uf3-compartir-arxius-windows.md)

### Preparar Linux per accedir a carpetes compartides en Windows

> Tot el que s'explica a continuació també serveix per accedir a una carpeta que s'ha compartit en Linux utilitzant el protocol **Samba**.

Per accedir a un recurs compartit amb Windows o Samba (mitjançant el protocol **SMB/CIFS**) des de Linux cal instal·lar el **client Samba** (paquet **smbclient**):

```
sudo apt update
sudo apt install smbclient cifs-utils
```

Per defecte, aquest paquet ja està instal·lat en Ubuntu Desktop.

Es pot comprovar si està instal·lat amb la comanda `dpkg`:

```
usuari@ucxxx:~$ dpkg -s smbclient
Package: smbclient
Status: install ok installed
...
```

> Quan s'accedeix de forma remota a un recurs compartit, cal identificar-se amb un usuari que tingui permisos sobre aquest recurs. Els **permisos efectius** que tindrà aquest usuari seran **els més restrictius** entre els permisos locals (NTFS) i els permisos de compartició (SMB/CIFS).

### Accedir a una carpeta compartides de forma gràfica

L'Ubuntu ens permet accedir **gràficament** als recursos compartits amb Windows o Samba amb el navegador _**Nautilus**_.

Es pot fer de vàries formes:

* Per mitjà del menú _**Xarxa**_ > _**IP\_servidor**_ o _**NOM(WSXXX)**_ .
* **Conectar con el servidor** > `smb://IP_servidor` o bé `smb://WSXXX`
* **Ctrl + L** per poder escriure a la barra d'adreces i escriure `smb://IP_servidor` o bé `smb://WSXXX`

També es pot accedir directament a una de les carpetes compartides si es coneix el nom del recurs compartit:

`smb://IP_servidor/alumnes` o bé `smb://WSXXX/alumnes`

Per accedir al servidor s'hauran d'introduir les següents dades:

* **Nom d'usuari**: usuari de Windows amb els permisos adequats.
* **Domini**: `ADXXX` (domini de Windows) o un grup de treball de Windows.
* **Contrasenya**: contrasenya de l'usuari de Windows.
  * **Oblidar immediatament la contrasenya** (**recomanada per fer proves**): cada cop que es vulgui accedir, caldrà tornar a posar l'usuari i la contrasenya.
  * **Recordar la contrasenya mentre no es tanqui la sessió de l'usuari actual** (**recomanada per treballar**): si es vol accedir amb un usuari diferent, caldrà tancar la sessió i tornar a entrar.
  * **Recordar sempre aquesta contrasenya**: si més endavant es vol eliminar l'usuari i contrasenya guardats, caldrà anar al programa _**Contraseñas y claves**_, dins l'apartat Contraseñas.

### Accedir a una carpeta utilitzant comandes

La comanda `smbclient` obre un petita aplicació que ens permet accedir a un recurs del servidor Windows o Samba.

*   Per **exemple**, si volem accedir a la carpeta compartida '_alumnes_' del servidor, executarem:

    `smbclient //IP_servidor/alumnes`
*   Si el recurs està protegit amb contrasenya, podem indicar amb quin usuari hi accedim amb el **paràmetre -U**.

    `smbclient //IP_servidor/alumnes -U usuariSMB`

Aquesta comanda s'utilitza, sobretot, per **llistar els recursos compartits** d’una màquina remota posant el paràmetre -L.

* Fins i tot els recursos amagats (aquells que s'han creat afegint un **$** al final del nom).

```
usuari@ucxxx:~$ smbclient -L //WSXXX -U usuariSMB
Enter usuariSMB's password:
Domain=[WSXXX] OS=[Windows 10 Enterprise Evaluation 9600] Server=[Windows 10 Enterprise Evaluation 6.3]
    Sharename       Type      Comment
    ---------       ----      -------
    ADMIN$          Disk      Admin remota
    Alumnes         Disk    
    C$              Disk      Recurso predeterminado
    IPC$            IPC       IPC remota
    Profes          Disk    
    Users           Disk    
...
```

### Muntar carpetes compartides

També hi ha la possibilitat **muntar les carpetes compartides** en carpetes del nostre sistema com si es tractés d'una carpeta local.

* És igual com en els recursos **NFS**.
* La **diferència** entre **NFS** i **SMB** és que NFS no requereix que l’usuari que fa la connexió s’autentifiqui i amb SMB sí cal autentificació.

Cal instal·lar el paquet **cifs-utils**:

`sudo apt install cifs-utils`

**Per exemple**, si volem accedir des de l’equip d’un professor a una carpeta compartida amb el nom de professors al servidor, executarem:

`mount –t cifs //IP_servidor/alumnes /mnt/alumnes –o username=usuari,password=contrasenya`

Si el servidor no requereix que l’usuari s’autentiqui (permet accés a convidats), els paràmetres username, password i workgroup es poden obviar.

`mount –t cifs //IP_servidor/alumnes /mnt/alumnes`

> **ATENCIÓ:** per saber els permisos efectius d'accés a una carpeta compartida cal tenir en compte els permisos que té l'usuari que es connecta a la carpeta remota (usuari Windows o Samba) i els permisos que té l'usuari actual sobre la carpeta local on està muntada la carpeta compartida.

### Muntar carpetes compartides de forma automàtica

Si volem que una carpeta compartida **es connecti sempre de forma automàtica** quan iniciem el nostre Linux, cal afegir a l'arxiu `/etc/fstab` una línia com:

Si el recurs compartit permet l’accés a convidats (guests):

`//IP_servidor/professors /mnt/professors cifs guest 0 0`

Si el recurs compartit està protegit per contrassenya:

`//IP_servidor/professors /mnt/professors cifs username=usuari,password=constrasenya,sec=ntlm 0 0`

Per muntar automàticament els recussos definits a `/etc/fstab` sense necessitat de reiniciar el sistema podeu executar.

`sudo mount -a`

Per comprovar si s'han muntat correctament les carpetes es pot utilitzar la comanda `mount`.

## Accedir a impressores compartides en Windows des de Linux

### Compartir impressores en Windows

[Apunts Compartir impressores en Windows](uf4-compartir-de-windows-a-linux.md#compartir-impressores-en-windows)

### Instal·lar en Linux impressores compartides en Windows

Per instal·lar impressores cal anar a _**Configuración**_ > _**Impresoras**_ i clicar el botó _**Añadir**_.

*   **Opció Impresora Windows via SAMBA:**

    Per instal·lar una impressora compartida amb SMB/CIFS cal desplegar la llista Impresora en red i triar l'opció _**Impresora Windows via SAMBA**_.
*   **Opció Impresora SMB:**

    Normalment, trobarem la impressora fent clic al botó _**Examinar**_ i buscant en la xarxa el servidor (`WSXXX`). Demanarà un usuari i contrasenya del servidor i es podrà seleccionar la impressora compartida.

    També es pot posar a la finestra d'edició _**Impresora SMB**_ la ruta de la impressora (_**domini/servidor/impressora**_): `WSXXX/HPLaserColor` (no cal posar smb://).

    Després cal seleccionar l'opció _**Poner detalles de autenticación**_ i posar el nom i la contrasenya d'un usuari de Windows amb permís per imprimir. Convé clicar al botó Verificar per assegurar-se que l'usuari té accés i que la contrasenya és correcta.

Un cop seguim endavant, demanarà la marca i el model per instal·lar els **drivers**.

Es pot imprimir una pàgina de prova i comprovar en el servidor que li ha arribat correctament. L'usuari propietari del document hauria de ser l'usuari que hem configurat prèviament en els paràmetres de verificació.

A part dels permisos que es posin en el servidor, també es pot limitar l'accés a la impressora dels usuaris locals (els del client) indicant quins usuaris podran utilitzar la impressora o quins no ho podran fer.

## Referències

* **Wiki Ubuntu**: [https://wiki.ubuntu.com](https://wiki.ubuntu.com/MountWindowsSharesPermanently)
* **ITE Educacion: Cliente de Samba**: [http://www.ite.educacion.es/formacion/materiales/85/cd/linux/m4/cliente\_de\_samba.html](http://www.ite.educacion.es/formacion/materiales/85/cd/linux/m4/cliente\_de\_samba.html)
