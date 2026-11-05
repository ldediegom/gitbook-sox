# Unir un client Linux a un domini Windows amb Samba

## Introducció

Una de les problemàtiques més rellevants quan volem unir dos servidors d'Active Directory, o integrar sistemes operatius heterogenis en un mateix domini, és la **compatibilitat dels mecanismes d’autenticació i d’encriptació de les contrasenyes**.&#x20;

Si es fa servir Active Diretory:

* **Windows Server 2025** necessita **NTLMv2** i **Kerberos amb xifratge AES** (AES128-CTS, AES256-CTS, i checksum AES256-SHA2)&#x20;
* **Ubuntu 24.04 i posteriors**, utilitzen princialment **SSSD, realmd** i **Kerberos** (AES128 i AES256). Poden fer servir NTLMv2 via Samba/Winbind.

{% hint style="warning" %}
El conflicte apareix quan:

1. AD té habilitat “AES SHA2” però el client Linux no el suporta.
2. SSSD/realmd no actualitza correctament els enctypes del compte d’ordinador.
3. El domini té “RC4 disabled” i el client Linux no ofereix AES correctament.
{% endhint %}

Una de les solucions més senzilles per evitar aquests problemes, consisteix a utilitzar el servei **Samba** com a mecanisme d’autenticació.

L'autenticació amb **Samba** fa servir els serveis **SMB**, **Winbind**, **NSS**, **PAM** i configuracions pròpies. Aquest enfocament proporciona un sistema lleuger i molt apropiat per entorns amb **compartició de recursos**.&#x20;

Tot i ser molt potent, no utilitzar integració amb Active Directory mitjançant el flux Kerberos (SSSD + realmd + krb5) pot comportar l'aparició de diversos **punts febles** (no vulnerabilitats):

* &#x20;Més fitxers de configuració (smb.conf, krb5.conf, nsswitch.conf, PAM):
  * Estructura més extensa
  * Més punts febles.
  * Més dificultat per diagnosticar problemes d’autenticació o resolució d’identitats
* Dependència de versions específiques de Samba i del seu suport de Kerberos.

Tot i que aquesta opció **no proporciona el nivell més alt d’integració i robustesa** en comparació amb una solució basada íntegrament en Kerberos amb SSSD, sí que és una alternativa ràpida i pràctica, especialment en entorns on es prioritza la **compatibilitat** i la **facilitat de configuració**.

## Procés d'unió de client Linux a AD amb autenticació Samba

### Consideracions prèvies

1. **No és recomenable utilitzar una màquina** on ja s’hagin fet intents previs d’unió a AD, ja que alguns serveis i configuracions no són compatibles entre si i poden deixar residus que interfereixin en el procés.
2. Realitza les configuracions prèvies indicades al punt [**Configuració inicial**](configuracio-inicial.md).
3. **Un únic proveidor de DNS**: Per evitar problemes de resolució del domini i del controlador de domini (DC), es recomana configurar el client perquè utilitzi una sola interfície de xarxa i un únic servidor DNS, preferiblement el del DC o un DNS integrat amb Active Directory. És **crític** perquè la resolució de `_ldap._tcp` i `_kerberos._tcp` funcioni

### Passos de configuració client AD amb Samba...

Els passos següents inclouen els paquets bàsics i les dependències necessàries.

Atesa la idiosincràsia de Linux, cal assegurar-se que tots els paquets siguin compatibles amb la distribució Ubuntu (Ubuntu 24.04.3 i endavant).

{% hint style="info" %}
Dades bàsiques dels exemples:

* Client al domini: **ubxxx.adxxx.local**
* IP client ('X' és el un número): **192.168.X.100**&#x20;
* Domini: **adxxx.local**
* Servidor al domini: **dc1.adxxx.local**
* IP servidor ('X' és el un número): **192.168.X.20**
* Realm: **ADXXX.LOCAL**
{% endhint %}

{% stepper %}
{% step %}
#### **Instal·lació de paquets**

```bash
sudo apt update
sudo apt -y install realmd sssd sssd-tools libnss-sss libpam-sss adcli samba-common-bin oddjob oddjob-mkhomedir packagekit
```

Alguns d'aquests paquets són:

* **realmd:** detecta dominis AD i simplifica el procés d'unió al domini (_join)_.
* **sssd / sssd-tools:** proporciona serveis d’identitat (usuaris, grups) i gestiona l'autenticació mitjançant Kerberos i LDAP.
* **adcli:** gestiona la creació del compte de l'equip dins del domini.
* **samba-common-bin:** necessari per a l’ús de Samba com a _membership software_ (NTLMv2).
{% endstep %}

{% step %}
#### **Verificació de DNS**

Abans d’unir el client al domini, cal verificar que la resolució DNS funciona correctament.

**Comprovacions bàsiques**

* `ping` al DC

```shellscript
ping 192.168.X.20
```

* `ping` al domini (en alguns casos, aquesta prova pot fallar sense cap repercussió)

```shellscript
ping adxxx.local
```

* Comprovar la resolució del domini AD

```shellscript
nslookup adxxx.local
```

* Comprovar la resolució del controlador de domini

```shellscript
nslookup dc1.adxxx.local
```

<mark style="color:$warning;">Per continuar el procés</mark>, és convenient que funcioni la connectivitat i la resolució de noms (DNS).
{% endstep %}

{% step %}
#### Detecció del domini

Si `nslookup` funciona, aquest pas hauria de mostrar tota la informació del domini.

```bash
realm discover teu-domini.local
```

Una possible sortida és:

```console
$ realm discover adxxx.local
adxxx.local
  type: kerberos
  realm-name: ADXXX.LOCAL
  domain-name: adxxx.local
  configured: no
  server-software: active-directory
  client-software: sssd
  required-package: sssd-tools
  required-package: sssd
  required-package: libnss-sss
  required-package: libpam-sss
  required-package: adcli
  required-package: samba-common-bin
  login-formats: %U
  login-policy: allow-realm-logins
```
{% endstep %}

{% step %}
#### Unir el client al domini

<pre class="language-bash"><code class="lang-bash"><strong>sudo realm join -v adxxx.local --membership-software=samba
</strong></code></pre>

Amb aquesta comanda:

* Es crea el compte d'ordinador a AD.
* La pertinença al domini (`--membership-software`) es gestiona mitjançant **Samba**.
* L’autenticació pot fer ús de **NTLMv2**, evitant conflictes amb **Kerberos/AES**.
* `-v`: ens mostrarà el procés. Molt útil per realitzar el diagnòstic d'errors.
{% endstep %}

{% step %}
#### Verificació de la integració

**1) Verificar que el client apareix a l'Active Directory**

Al servidor es pot verificar gràficament anant a l'eina _Active Directory Users and Computers_ i accedint al contenidor _Computers_.

**2) Verificar l'estat del&#x20;**_**join**_**&#x20;(unió)**

Mitjançant el paquet `realmd` també podem mostrar la informació detallada sobre la unió del sistema Linux amb Active Directory.

```bash
realm list
```

Una possible sortida podria ser:

<pre><code>$ realm list
adxxx.local
  type: kerberos
<strong>  realm-name: ADXXX.LOCAL
</strong>  domain-name: adxxx.local
  configured: kerberos-member
  server-software: active-directory
  client-software: sssd
  required-package: sssd-tools
  required-package: sssd
  login-formats: %U@adxxx.local
  login-policy: allow-realm-logins
</code></pre>

**3) Verificació de resolució d'usuari**

Una manera senzilla de verificar l'accés és fent una petició d'informació d'un usuari del domini.

```bash
 id Administrator@adxxx.local
```

<mark style="color:$danger;">**Si no mostra la informació d'usuari:**</mark>

{% hint style="warning" %}
Si no mostra la informació de l'usuari, prova a reiniciar els serveis, la millor manera per no deixar-ne cap és **reiniciant el sistema operatiu**.
{% endhint %}

{% hint style="danger" %}
Si després de reiniciar una o diverses vegades no funciona, [desconnecta el client del domini](unir-linux-domini-windows.md#desconnectar-la-maquina-client-del-domini) i [elimina'l de l'Active Directory](unir-linux-domini-windows.md#eliminar-el-client-de-la-d) per què no hi hagi duplicitat.
{% endhint %}

Una possible sortida podria ser:

```
$ id Administrator@adxxx.local
uid=1273000500(administrator) gid=1273000513(domain users) grupos=1273000513(domain users),
1273000520(group policy creator owners),1273000519(enterprise admins),1273000512(domain admins),
1273000518(schema admins),1273000572(denied rodc password replication group)
```
{% endstep %}

{% step %}
#### Creació automàtica del directori personal

Tot i que no és obligatori per autenticar-se, el directori personal és necessari per utilitzar l’entorn gràfic.

**Opció 1: Gràficament**

La comanda `pam-auth-update` és l'opció més senzilla per configurar les opcions d'autenticació i gestió dels usuaris.

```bash
sudo pam-auth-update
```

S'ha d'escollir l'opció _**Create home directory on Login**_ i _Aceptar._

<figure><img src="../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

**Opció 2: Modicant el fitxer**

Es pot modificar el fitxer `/etc/pam.d/common-session` per afegir una línia indicant que es crei el directori personal en inciar sessió per primer cop.

```bash
sudo nano /etc/pam.d/common-session
```

S'ha d'afegir la següent línia de text:

```
session optional pam_mkhomedir.so skel=/etc/skel umask=077
```
{% endstep %}

{% step %}
#### Inciar sessió al terminal

Es fa la primera prova d'inici de sessió pel terminal. Si tot funciona correctament es crearà el  **directori personal**.

Si tenim l'usuari **uprova**:

```bash
sudo su uprova@adxxx.test
```
{% endstep %}

{% step %}
### (opcional) Ometre el nom de domini

Per defecte, l’inici de sessió requereix el format _usuari@domini._&#x20;

Es pot evitar editant el fitxer de configuració de SSSD i modificar el paràmentre corresponent.

Hem d'editar el següent fitxer:

```bash
sudo nano /etc/sssd/sssd.conf
```

I establir el paràmentre `use_fully_qualified_names` com <mark style="color:red;">`False`</mark> , cerca la línia corresponent i modifca-la si és necessari:

```console
use_fully_qualified_names = False
```

Es reinicia el servei SSSD:

```bash
sudo systemctl restart sssd
```

Comprova que ara es pot accedir a la informació d'usuari sense el domini.

```bash
 id Administrator
```
{% endstep %}
{% endstepper %}

### Validar usuaris del domini Windows

Per poder validar usuaris des de l'**entorn gràfic** escrivint el seu identificador.

Es poden utilitzar els mateixos formats que s'utilitzen en Windows:

* **ADXXX\usuari**
* **usuari@adxxx.local**

Exemple d'accés gràfic amb **Ubuntu**:

_Posem usuari i contrasenya._

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

_Si tot ha funcionat correctament, es podrà accedir a l'espai de l'usuari._

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

## Desconnexió del domini en el client Linux

### Desconnectar la màquina client del domini

La comanda necessària és:

`sudo realm -v leave ADXXX.LOCAL`

Una possible sortida podria ser:

```
sudo realm -v leave ADXXX.LOCAL
 * Removing entries from keytab for realm
 * /usr/sbin/sss_cache --users --groups --netgroups --services --autofs-maps
 * Removing domain configuration from sssd.conf
 * /usr/sbin/update-rc.d sssd disable
 * /usr/sbin/service sssd stop
 * Successfully unenrolled machine from realm
```

Aquesta acció elimina la confiança des del client, però **el comple d'ordinador encara existeix al domini**.

### Eliminar el client Linux de l'AD

S'ha d'eliminar el client de l'estructura de domini **des del DC**.

**Opció 1: Gràficament**

1. Accedir a l'eina **Active Directory Users and Computers**
2. Anar a la UO **Computers** (o l'UO on estan els clients)
3. Cerca el client
4. **Botó dret** sobre el client -> **Delete**

**Opció 1: Powershell**

Eliminar el client:

```powershell
Remove-ADComputer -Identity "NomDelClient" -Confirm:$false
```

Comprovar que s'ha eliminat (<mark style="color:red;">ha de donar error</mark>):

```powershell
Get-ADComputer -Identity "NomDelClient"
```

### Treure el DNS del client

Per evitar que el client faci les cerques al servidor DNS, és millor treure l'adreça preferent del DC o substituir-la.

Es pot fer mitjançant la **interfície gràfica** o del fitxer de configuració de _**netplan**_.

## Altres tutorials

Hi ha diverses maneres de realitzar la configuració amb **Samba**, aquí hi ha alguns exemples:

* [Join Linux Systems to a Windows Active Directory Domain (Video)](https://www.youtube.com/watch?v=UhYzoyQXXMA)
* [Unir un cliente Ubuntu 24.04 a un dominio de Active Directory sobre Windows Server 2022 (Parte 1)](https://somebooks.es/unir-un-cliente-ubuntu-24-04-a-un-dominio-de-active-directory-sobre-windows-server-2022-parte-1/)
* Francisco Javier Cruces Doval: [Active Directory en Ubuntu](https://www.javiercd.es/posts/sistemas/ad_ubuntu/ad_ubuntu/)
* Merox: [SMB Authentication with AD on Linux](https://merox.dev/blog/smb-cifs-active-directory-authentication-linux/) _(Versió per Red Hat, fàcil d'adaptar a Ubuntu)_

## Documentació i recursos

* [Apunts SOX (Pere Sánchez](http://moodlecf.sapalomera.cat/apunts/smx/sox/index.html?cap=3.5.0)
* **server-world.info:** [Join in Windows Active Directory Domain with Realmd](https://www.server-world.info/en/note?os=Ubuntu_25.04\&p=realmd)
* Red Hat Documentation: [RHEL](https://docs.redhat.com/es/documentation/red_hat_enterprise_linux/10/html/integrating_rhel_systems_directly_with_windows_active_directory/index)[ 10 - Integrating RHEL systems directly with Windows Active Directory](https://docs.redhat.com/es/documentation/red_hat_enterprise_linux/10/html/integrating_rhel_systems_directly_with_windows_active_directory/index)

