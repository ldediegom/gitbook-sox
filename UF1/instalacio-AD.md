# Instal·lació d'un controlador de domini Active Directory

![Active Directory](/assets/ActiveDirectory.png)

## Instal·lació del Directori Actiu

1. La instal·lació del **Directori Actiu** és la implementació d'una funció bàsica o rol del nostre Windows Server. Com qualsevol altre rol de servidor el podem instal·lar des l’**_Administrador del servidor_** i seleccionar l'opció _**Agregar roles y características**_ que obrirà l'assistent per afegir funcions.

  ![](/assets/AD_afegir.png)

2. Triem l'opció _**Instalación basada en características o en roles**_.

  ![](/assets/AD_ins2.png)

3. Després d’algunes pantalles d'informació on cal escollir a quin servidor el volem instal·lar, anirem amb el botó de **_Següent _** fins a la llista de possibles funcions per instal·lar en el sistema. 

  ![](/assets/AD_ins3.png)

4. Marcar l'opció **_Serveis de Domini d'Active Directory (AD DS)_** i **_DNS Server_** i prémer el botó de **_Següent_**.

5. Un cop finalitzada la instal·lació ens apareix una pantalla informativa on ens adverteix que per convertir el servidor en un controlador de domini funcional cal obrir l'assistent per crear un nou domini seleccionant _**Promover este servidor a controlador de dominio**_.

  ![](/assets/AD_ins4.png)

## Creació del domini
Un cop instal·lats els serveis bàsics de **Directori Actiu** és necessari completar la instal·lació mitjançant la creació d'un **nou domini** i la promoció de l'equip a controlador de domini mitjançant els següents passos:

En l’_**Administrador del servidor**_ ens apareix una notificació indicant que es requereix una confinguració del AD i mostra l’opció de _**Promover este servidor a controlador de dominio**_.

  ![](/assets/AD_ins5.png)

Després de la pantalla d'inici de l’assistent hi ha tres opcions: 
  * **_Afegir l'equip com a controlador de domini a un domini ja existent_**: serveix per afegir un controlador secundari a un domini ja creat. 
  * **_Afegir un nou domini en un bosc existent_**.
  * **_Afegir un nou bosc_**.
  
Com que no tenim encara cap domini creat escollim la tercera opció _**Afegir un nou bosc**_.

A continuació has d'introduir el nom complet del domini arrel. Aquest nom ha de complir l'estructura DNS (_Domain Name System_) amb sufix inclòs amb el que definirem el nom del bosc i l'espai de noms, per exemple `bosccoma.local`. 

  ![](/assets/AD_ins6.png)


Després ens demana el nivell de funcionalitat del sistema. Sempre escollirem el més alt, a no ser que calgui garantir la compatibilitat amb altres dominis gestionats per versions inferior. 

En el nostre cas, cal seleccionar l'opció "**_Nivell de funcionalitat Windows Server 2012 R2_**" i prem **_Següent_**.

L'assistent ens demana ara una **contrasenya **per poder administrar el **Directori Actiu**. En general, encara que poden ser diferents, sol ser convenient posar la mateixa contrasenya que l'administrador de l'equip. 

Introdueix doncs la contrasenya de l'administrador i prem **_Següent_**.

  ![](/assets/AD_ins7.png)

A continuació indicarà que no troba un servidor DNS, però **no cal fer res**; automàticament s'instal·larà el servei de DNS en aquest mateix servidor doncs **cal tenir instal·lat el servei de resolució de noms per al correcte funcionament del Directori Actiu**.

La finestra següent, demanarà el **nom NetBIOS** del domini. Es pot deixar el què proposa per defecte, que serà `BOSCCOMA` (el nom del domini en majúscules i sense .local).

Després s'ha d'indicar la localització dels arxius bàsics que utilitzarà el Directori Actiu: la carpeta per a base de dades, la carpeta d'arxius de registre i la carpeta SYSVOL que conté els arxius públics del domini que han de ser compartits. Podem deixar les opcions per defecte.

A continuació, se'ns informa mitjançant un **resum**, de tota les configuracions que hem seleccionat. 

En prémer **_Següent_** comença el procés de comprovació de requeriment previs i després podem sel·leccionar **_Instal·lar_** i comença el procés de **promoció de l'equip a Controlador de Domini**. 

Un cop acabada la instal·lació es **reiniciarà l'equip** perquè el Directori Actiu s’iniciï i estigui operatiu.

> Si tot ha funcionat correctament, el nostre equip ja està convertit en un controlador de domini del Directori Actiu per gestionar de forma centralitzada els recursos de la xarxa.

Un cop reiniciat el sistema, en la **pantalla d'inici** de sessió, on demana l'usuari i contrasenya, ja es pot veure un canvi: el nom d'usuari és `BOSCCOMA\Administrador`. 

### Eliminació del domini

1. Si s'intenta desinstal·lar **_Servicios de dominio de Active Directory_**, s'arriba a un punt en què avisa que primer s'ha de degradar el controlador de domini, i posa un enllaç per fer-ho.

2. Si aquest és l'últim controlador de domini (de fet, és l'únic), cal marcar l'opció **_Último controlador de dominio en el dominio_**.

3. Avisarà què el servidor té altres rols relacionats, però s'ha de marcar  **_Continuar con la eliminación_** i seguir endavant.

4. Després també s'ha de marcar **_Quitar esta zona de DNS..._** i **_Quitar particiones de aplicación_**.

5. I finalment clicar el botó **_Disminuir nivel_**.

Després de  reiniciar, en l'inici de sessió, el nom d'usuari ja no inclou el nom del domini.

## Unir equips al domini

1. Configureu una **xarxa interna** en VirtualBox amb un servidor Windows 2012 com a controlador de domini i un ordinador client Windows.

2. Assigneu IP Estàtica a la màquina servidor (per exemple 192.168.0.10) i a la màquina client (192.168.0.20). El controlador de domini també dona servei DNS al client per tant, al client, **cal posar com a servidor DNS principal la IP del servidor de domini**.

> **ATENCIÓ**: és important recordar que per unir un client a un domini, el primer que cal fer és configurar com a **DNS principal** l'adreça **IP del servidor DNS** del domini, que és també el controlador de domini.

3. Fes pings per a verificar que el client pot comunicar-se amb el servidor.

4. Entreu a la màquina client i **afegiu-la al domini**, canviant el mode de grup de treball al mode de domini. 
  Una forma d'arribar a la configuracio és fer clic amb el botó secundari del ratolí sobre la icona d'inici de Windows i seleccionar **_Sistema > Cambiar configuración > Cambiar..._**
  
5. Cal seleccionar l'opció **_Dominio _**i posar el nom del domini al qual voleu connercar-la (`bosccoma.local`). El nom del domini també es pot posar en el format NetBIOS (`BOSCCOMA`).
  
6. Si pot connectar amb el servidor, demanarà les credencials de l'administrador del domini. 

7. Si tot ha anat bé, ens indicarà que l'equip s'ha unit al domini però que **cal reiniciar**.

6. Ara entreu a la màquina client i veureu que podeu entrar al domini mitjançant les credencials de l'administrador. L'inici de sessió amb l'usuari de domini es pot fer posant `Administrator@bosccoma.local` o bé `BOSCCOMA\Adminitrator` tal i com s'indica a la [teoria](/UF1/usuaris-grups-i-unitats-organitzatives.html#usuaris-globals).

7. Finalment, entra al servidor i busca el nou equip a **_Eines administratives > Usuaris i equips de l’Active Directory_**.

### Desconnectar un client del domini

Si es vol desconnectar la màquina client del domini on estava connectada, cal tornar a l'apartat on hem anat per unir-la al domini, seleccionar l'opció **_Grupo de trabajo_** i posar un nom (per defecte **_WORKGROUP_**).

Demanarà el nom i la contrasenya d'un usuari vàlid per realitzar aquesta acció i ens avisarà si s'ha produït algun error. Si tot ha anat correctament, **cal reiniciar** la màquina.

Si s'elimina el servidor DNS del domini, a la configuració de xarxa s'ha de treure el servidor 
DNS del domini i posar altres servidors DNS.

### Connectar el client a un altre domini

Un cop desconnectat el client del domini, cal posar com a servidor primari de DNS la IP del servidor DNS del nou domini.

Després repetirem els passos per unir el client a un domini posant el nom del nou domini.
