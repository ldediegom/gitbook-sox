# Administració de discos amb LVM

## Què és LVM?

El LVM (Logical Volume Manager) serveix per gestionar espai d’emmagatzematge de forma flexible (crear, ampliar o reduir volums lògics sense haver de tornar a particionar).

És un sistema que va un pas més endavant de la gestió de discos habitual. Aquesta és una capa d'abstracció que ens permet agrupar (i desagrupar) múltiples dispositius físics o particions en un conjunt (**pool**) i després distribuir i redimensionar espai de forma flexible.

El concepte clau és que, amb LVM els discos deixen de ser blocs separats per convertir-se en part d'un conjunt. Des del punt de vista de l'usuari no importa la quantitat de discos físics que hi ha, sinó la suma de capacitats d'aquests.

### Conceptes clau

Alguns termes bàsics són els següents:

* **Volum físic (**_**PV - Physical Volume**_**):** Partició de disc dur (o fins i tot el propi disc o arxius en bucle) en el qual es pot tenir grups de volums. S’assignen metadades LVM sobre ell.
* **Grup de volums (**_**VG - Volume Groups**_**):** Grup de volums físics utilitzats com un volum d'emmagatzematge (com un disc de discos o particions). Contenen els volums lògics. Penseu en els grups de volums com discs durs.
* **Volums lògics (**_**LV - Logical Volume**_**):** Una partició lògica que resideix en un grup de volums i es compon d'extensions físiques. Penseu en els volums lògics com particions normals.
* **Extensió física (**_**PE - Physical Extent**_**):** La mida més petita en el volum físic que es pot assignar a un volum lògic (4 MiB per defecte). Penseu d'extensions físiques com a parts de discos que es poden assignar a qualsevol partició.

Veiem: Volum físic (PV), Grup de volums (VG), Grup de volums físics, Volums lògics (LV), Extensió física (PE).

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

En aquest cas es pot observar tota l'estructura LVM en una simulació de punts de muntatge Linux. Tenim 2 discos, cada disc amb 2 particions.

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

En realitat podríem fer servir [punts de muntatge](../uf2-introduccio/uf2-instalacio-linuxserver.md#planificar-el-disc) que estiguin en diversos discos a la vegada. Al següent exemple, es mostra una estructura amb dos discos (a i b), en què cada volum lògic es troba a particions físiques dels dos discos.

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

### Característiques

* Pot agrupar qualsevol nombre de discs com un gran disc.
* Tenir volums lògics repartits sobre diversos discos.
* Crear petits volums lògics i canviar la seva mida “dinàmicament”, tan bon punt s'omplen.
* Canviar la mida dels volums lògics independentment de la seva posició en el disc.&#x20;
* Redimensionar/crear/esborrar els volums lògics i físics en línia i en calent (amb _ext4_ i _ext5_, _btrfs_, també _xfs_, però només expandir).
* La migració en línia/en viu de LV, metre són utilitzats pels serveis, a diferents discos sense repercussions pels serveis.
* Les instantànies (_snapshots_) que permeten fer còpies de seguretat del sistema de fitxers, amb les mínimes conseqüències en l’activitat del servei.
* Suport de xifratge del sistema de fitxers.

## Comandes bàsiques de LVM

Abans de començar, s'ha de tenir en compte que les mides es poden indicar de diverses maneres:

* En unitats de Bytes (Exemple: -L 20G)
* Extensions físiques (Exemple: -l 500)
* Percentatge d'espai (Exemple: -l 100%FREE).

### Comandes de visualització/consulta

<table data-full-width="false"><thead><tr><th width="120.5999755859375">Comanda</th><th width="245">Descripció</th><th>Exemple</th></tr></thead><tbody><tr><td><code>pvdisplay</code></td><td>Mostra informació sobre un  <em>volum físic</em>.</td><td><code>pvdisplay /dev/sdb1</code>, on es visualitza la informació de  partició 1 de disc b.  </td></tr><tr><td><code>vgdisplay</code></td><td>Mostra informació sobre un  <em>grup de volums.</em></td><td><code>vgdisplay vg_data</code> on <em>vg_data</em> és el Grup de Volums</td></tr><tr><td><code>lvdisplay</code></td><td>Mostra informació sobre un <em>volum lògic</em>.</td><td><code>lvdisplay /dev/vg_data/lv_home</code> que detalla l’estat del volum <em>lv_var</em></td></tr><tr><td><code>pvs</code></td><td>Mostra tots els PV amb informació resumida. Versió reduïda de <code>pvdisplay</code></td><td><code>pvs</code></td></tr><tr><td><code>vgs</code></td><td>Mostra tots els grups de volum de manera resumida. També surt informació de l'espai. Versió reduïda de <code>vgdisplay</code></td><td><code>vgs</code></td></tr><tr><td><code>lvs</code></td><td>Mostra tots els volums lògics del sistema de manera resumida. Versió reduïda de <code>lvdisplay</code></td><td><code>lvs</code></td></tr><tr><td><code>lvscan</code></td><td>Escaneja i mostra tots els volums lògics actius</td><td><code>lvscan</code></td></tr><tr><td><code>vgscan</code> </td><td>Escaneja tots els grups de volum disponibles</td><td><code>vgscan</code></td></tr><tr><td><code>pvscan</code> </td><td>Escaneja tots els volums físics disponibles</td><td><code>pvscan</code></td></tr></tbody></table>

### Comandes de Creació

<table data-full-width="false"><thead><tr><th width="133.39996337890625">Comanda</th><th width="245">Descripció</th><th>Exemple</th></tr></thead><tbody><tr><td><code>pvcreate</code>  </td><td>Converteix un dispositiu o partició en un <em>volum físic</em> per LVM.</td><td><code>pvcreate /dev/sdb1</code>, marca la partició <em>sdb1</em>  com a LVM  </td></tr><tr><td><code>vgcreate</code></td><td>Agrupa un o més <em>PV</em> en un grup de volums.</td><td><code>vgcreate vg_backup /dev/sdc1 /dev/sdd1</code> , crea <em>vg_backup</em> amb els <em>PVs</em> sdc1 i sdc2.</td></tr><tr><td><code>lvcreate</code></td><td>Crea un volum lògic dins d’un VG amb una mida determinada. Aquesta pot ser en unitats de Bytes (<code>-L 20G</code>), extensions físiques (<code>-l 500</code>) o percentatge d'espai (<code>-l 100%FREE</code>).</td><td><code>lvcreate -L 5G -n lv_backup vg_backup</code> , crea un volum lògic de <em>5 GiB</em> dins <em>vg_backup</em>.</td></tr></tbody></table>

### Comandes de modificació (ampliació / reducció)

<table data-full-width="false"><thead><tr><th width="133.39996337890625">Comanda</th><th width="245">Descripció</th><th>Exemple</th></tr></thead><tbody><tr><td><code>vgextend</code>  </td><td>Afegeix un PV a un grup de volums existent.</td><td><code>vgextend vg_backup /dev/sde1</code>, afegeix <em>/dev/sde1</em> al grup <em>vg_backup</em></td></tr><tr><td><code>vgreduce</code></td><td>Treu un PV (prèviament buidat) d’un grup de volum.</td><td><code>vgreduce vg_backup /dev/sde1</code> , retira <em>/dev/sde1</em> de <em>vg_data</em>.</td></tr><tr><td><code>lvextend</code></td><td>Augmenta (+) o es redimensiona la mida d'un volum lògic.</td><td><code>lvextend -L +5G /dev/vg_backup/lv_home</code>, afegeix 5 GiB al volum <em>lv_home</em><br><code>lvextend -L 15G /dev/vg_backup/lv_home</code> - redimensiona (augmenta) la mida del volum <em>lv_home</em> a 15GiB</td></tr><tr><td><code>lvreduce</code></td><td>Es redueix o es redimensiona la mida d'un volum lògic.</td><td><code>lvreduce -L -5G /dev/vg_backup/lv_home</code>, redueix 5 GiB al volum <em>lv_home</em><br><code>lvreduce -L 15G /dev/vg_backup/lv_home</code> - redimensiona (redueix) la mida del volum <em>lv_home</em> a 15GiB</td></tr><tr><td><code>lvresize</code></td><td>Redimensiona el volum (ampliar o reduir).</td><td><p><code>lvresize -L +5G /dev/vg_backup/lv_home</code>, afegeix 5 GiB al volum <em>lv_home</em></p><p><code>lvresize -L -5G /dev/vg_backup/lv_home</code>, treu 5 GiB al volum <em>lv_home</em><br><code>lvextend -L 15G /dev/vg_backup/lv_home</code>, redimensiona la mida del volum <em>lv_home</em> a 15GiB</p></td></tr></tbody></table>

### Comandes d'eliminació

<table data-full-width="false"><thead><tr><th width="133.39996337890625">Comanda</th><th width="245">Descripció</th><th>Exemple</th></tr></thead><tbody><tr><td><code>lvremove</code></td><td>Elimina el volum lògic especificat.</td><td><code>lvremove /dev/vg_backup/lv_backup</code>, suprimeix el LV anomenat <code>lv_backup</code></td></tr><tr><td><code>vgremove</code></td><td>Elimina un grup de volum (no a contenir cap LV)</td><td><code>vgremove vg_backup</code>, elimina el grup <code>vg_backup</code> (Recorda: no ha de contenir volums)</td></tr><tr><td><code>pvremove</code></td><td>Desvincula un dispositiu/partició del LVM.</td><td><code>pvremove /dev/sde1</code>, fa que <code>/dev/sde1</code> deixi de ser PV.</td></tr></tbody></table>

## Altres comandes útils durant les operacions amb LVM

Hi ha comandes que es fan servir durant les operacions amb volums LVM, però que no formen part del paquet. Algunes d'elles ja venen preinstal·lades amb els sistemes GNU/Linux.

Tot  i que segons les circumstàncies en puguem fer servir d'altres, a continuació, es llisten algunes comandes molt comunes.

<table data-full-width="false"><thead><tr><th width="133.39996337890625">Comanda</th><th width="245">Descripció</th><th>Exemple</th></tr></thead><tbody><tr><td><code>fdisk</code></td><td>Eina clàssica per gestionar particions. En permet veure particions, crear, eliminar, canviar el tipus...</td><td><code>fdisk -l /dev/sda</code> , llista les particions del disc <code>/dev/sda</code></td></tr><tr><td><code>lsblk</code></td><td>Mostra l'arbre de discs/particions, punts de muntatge, mides, relacions...</td><td><code>lsblk -f</code>, mostra el sistema de fitxers, UUID i tipus per a cada partició/dispositiu.</td></tr><tr><td><code>df</code></td><td>Mostra espai usat i disponible dins dels sistemes de fitxers muntats.</td><td><code>df -h</code>, mostra espai usat i lliure global.</td></tr><tr><td><code>resize2fs</code></td><td>Eina per redimensionar sistemes de fitxers tipus <em>ext.</em></td><td><code>resize2fs /dev/sde1</code>, redimensionar el sistema de fitxers a tot l’espai lliure de la partició <em>/dev/sde1</em>.</td></tr><tr><td><code>fsck</code> </td><td>Comprovar i reparar errors en sistemes de fitxers. La partició no ha d'estar muntada.</td><td><code>fsck -f /dev/sde2</code>, força la comprovació del sistema de fitxers de la partició <em>/dev/sde1</em>.<br><code>fsck.ext4 -p /dev/sde2</code>, comprava el sistema de fitxers <em>ext4</em> de la partició <em>/dev/sde2</em>, indicant que es reparin automàticament els errors.</td></tr><tr><td><code>e2fsck</code></td><td>L'eina de baix nivell invocada amb <code>fsck</code></td><td><code>e2fsck -n /dev/sde1</code>, comprova el sistema de fitxers de la partició <em>/dev/sde1.</em> En aquest cas, ho fa només en mode lectura; no farà reparacions, només informarà. Més segur per a sistemes muntats.</td></tr></tbody></table>

## Documentació i altres recursos

* Red Hat Documentation: [Logical Volume Manager Administration](https://docs.redhat.com/es/documentation/red_hat_enterprise_linux/7/html/logical_volume_manager_administration/index)
* SomeBooks: [Qué es LVM](https://somebooks.es/que-es-lvm/)
* SomeBooks: [Crear un volumen lógico LVM en Ubuntu Server 22.04 LTS y mover la carpeta ‘var’](https://somebooks.es/crear-un-volumen-logico-lvm-en-ubuntu-server-22-04-lts-y-mover-la-carpeta-var/)
