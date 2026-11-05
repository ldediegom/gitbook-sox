# Configuració inicial

## Hora de servidor i client

Cal verificar que la data i l’hora del servidor i del client coincideixin. En cas contrari, l’autenticació d’usuaris no funcionarà.

{% hint style="danger" %}
**Kerberos** només admet un desfasament màxim d’uns **5 minuts** entre l’hora del servidor i la del client. Per aquest motiu, és essencial que tots els equips estiguin correctament sincronitzats.
{% endhint %}

Per comprovar l'hora a **Ubuntu** podem fer-ho gràficament o amb la comanda:

`timedatectl`

A **Windows** es pot fer gràficament o amb el cmdlet:

`Get-Date`

Modifica la data i l'hora si és necessari.

## Configuració per un domini .local

> **ATENCIÓ**: en Ubuntu, quan es vol connectar amb un domini de tipus _**.local**_ pot crear conflictes:

Si es vol evitar conflictes i no necessitem serveis com CUPS, podem tenim les següents opcions:

### Opció 1: Desactivar mDNS completament

Desactivar el servei:

`sudo systemctl disable --now avahi-daemon`

Verificar-ho:

`sudo systemctl status avahi-daemon`

### Opció 2: Que _systemd-resolved_ que no gestioni _.local_

Editar el fitxer `/etc/systemd/resolved.conf`:

```bash
DNSStubListener=no
MulticastDNS=no
```

Reiniciar el servei

`sudo systemd restart systemd-resolved` &#x20;

### Opció obsoleta - <mark style="color:red;">No recomenat</mark>

Modificar la resolució de domini al fitxer `/etc/dhcp/dhclient.conf`

{% code title="/etc/dhcp/dhclient.conf" %}
```bash
#domain-name=local
domain-name=.alocal
```
{% endcode %}

## Instal·lació del servei SSH (opcional)

Si es fan servir màquines virtuals, una manera de poder copiar i enganxar les instruccions, és fent servir el servei de **ssh** en el client. Així doncs, podem instal·lar-lo per comoditat:

`sudo apt install ssh`

## Modificar el nom de l'equip

Per poder afegir el sistema Ubuntu a un equip Microsoft, s'ha de modificar el de la màquina adaptat al nom de domini.

Per canviar el nom d'una màquina Linux, cal fer-ho en els arxius `/etc/hostname` i `/etc/hosts` i reiniciar el sistema.

Si partim del següent supòsit:

* Nom de l'equip client: **ubxxx**
* Nom de l'equip servidor: **wsxxx**
* Nom del domini: **adxxx.local**

Editem el fitxer `/etc/hostname`:

{% code title="/etc/hostname" %}
```bash
ubxxx.adxxx.local
```
{% endcode %}

Editem el fitxer `/etc/hosts`:

{% code title="/etc/hosts" %}
```bash
127.0.0.1   localhost
127.0.1.1   ubxxx.adxxx.local   ubxxx
```
{% endcode %}

**Reiniciar** el sistema per fer efectius els canvis.

Revisar si les configuracions són correctes amb:

`hostname -f`

i

`hostnamectl`

## Configurar el nom DNS del servidor

En l'arxiu `/etc/hosts`, afegir el nom DNS del servidor davant del nom de la màquina.

S'ha de tenir en compte això:

* Si el **servidor (**_**dc**_**) és la propia màquina** (Linux) no necessitem connexió al DNS del _dc_.

{% code title="/etc/hosts" %}
```bash
127.0.0.1   localhost
127.0.1.1   ubxxx.adxxx.local   ubxxx
```
{% endcode %}

* Si el **servidor (**_**dc**_**) és una màquina externa** (Windows)

{% code title="/etc/hosts" %}
```bash
127.0.0.1   localhost
127.0.1.1   ubxxx.adxxx.local   ubxxx

# Servidor exterm - per exemple: Windows Server
192.168.X.Y   wsxxx.adxxx.local   wsxxx
```
{% endcode %}
