# Administrar el directori actiu de Samba 4

## Instal·lació de les eines per administrar el domini (RSAT)

Per administrar el domini Samba, utilitzarem un conjunt d’eines d’administració gratuïtes que ofereix Microsoft anomenades _**RSAT**_ (_Remote Server Administration Tool_).

Aquestes eines s'instal·len en un **client Windows** i ens permetran gestionar el domini de forma idèntica a un domini amb Windows Server.

_**RSAT**_ està disponible per diferents versions de Windows:

* Windows 11: [https://www.microsoft.com/en-us/download/details.aspx?id=45520](https://www.microsoft.com/en-us/download/details.aspx?id=45520)
* Windows 10: [https://www.microsoft.com/en-us/download/details.aspx?id=45520](https://www.microsoft.com/en-us/download/details.aspx?id=45520)

En el cas de **Windows 10**, la versió nova no serveix i cal descarregar una versió antiga: [Windows 10 RSAT antiga](https://www.google.es/url?sa=t\&rct=j\&q=\&esrc=s\&source=web\&cd=2\&cad=rja\&uact=8\&ved=0ahUKEwjDt8KAuoPTAhVGWBQKHZqZAiMQFggtMAE\&url=https%3A%2F%2Fdrive.google.com%2Ffile%2Fd%2F0B-meMiJiVDGATkpyS3J0Q29yZ1U%2Fview%3Fusp%3Dsharing\&usg=AFQjCNEP-DsxDUkmK-1FsmSMU6Ev9JFn5w\&sig2=Vr9W24TMh7VBq7V0xWRx_A\&bvm=bv.151325232,d.d24).

Un cop instal·lades, activarem a _**Características de Windows**_ les eines d’administració necessàries (Remote Server Administration Tools)

<figure><img src="../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

Instal·lar:

* RSAT: Herramienta de administración de acceso remoto.
* RSAT: Herramientas de Active Directory Domain Services y Lightweight Directory Services.

Un cop activades les eines, **iniciem sessió amb l'usuari administrador** del domini i obrim _**Administrador del servidor > Herramientas administrativas > Usuarios y equipos de Active Directory**_ i podrem **gestionar el domini** tal i com realitzaven amb Windows Server.

A partir de **Windows 10** totes les eines de RSAT s'activen per defecte al instal·lar-les.

També podem utilitzar l'eina _**Active Directory Users and Computers (AD UC)**_, que ens permet administrar els usuaris, grups i equips del domini.

> Per poder administrar el domini (afegir usuaris, grups...) cal iniciar sessió amb el **compte** d'administrador de Samba (_**Administrator**_).

## Documentació i recursos

* Somebooks: [Instalar y configurar herramientas de administración remota (RSAT) sobre Windows 11](https://somebooks.es/instalar-y-configurar-herramientas-de-administracion-remota-rsat-sobre-windows-11/)
* Somebooks: [Crear un controlador de dominio de Active Directory con Samba en Ubuntu 24.04 LTS](http://somebooks.es/crear-un-controlador-de-dominio-de-active-directory-con-samba-en-ubuntu-24-04-lts/)
* Somebooks: [Administrar Samba como controlador de dominio desde Windows 11 con RSAT](http://somebooks.es/administrar-samba-como-controlador-de-dominio-desde-windows-11-con-rsat/)
