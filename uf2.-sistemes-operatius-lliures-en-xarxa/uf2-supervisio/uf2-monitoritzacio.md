# Monitorització

## CPU, memòria RAM i memòria d'intercanvi

Amb les comandes `cat /proc/cpuinfo` i `cat /proc/meminfo` es pot veure informació detallada sobre la **CPU** i la **RAM**.

**Veure la informació detallada de cadascun dels processadors:**

```text
usuari@usxxx:~$ cat /proc/cpuinfo
processor     : 0
vendor_id     : GenuineIntel
cpu family    : 6
model         : 15
model name    : Intel(R) Core(TM)2 Duo CPU     E6750  @ 2.66GHz
cpu MHz       : 2671.640
cache size    : 4096 KB
cpu cores     : 2
flags         : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge
                mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2
                ss ht tm pbe syscall nx lm constant_tsc arch_perfmon
                pebs bts rep_good nopl aperfmperf pni dtes64
bogomips      : 5343.28
cache_alignment    : 64
address sizes      : 36 bits physical, 48 bits virtual
```

**Veure la informació detallada de la RAM:**

```text
usuari@usxxx:~$ cat /proc/meminfo
MemTotal:        4047564 kB
MemFree:          244320 kB
Buffers:           44824 kB
Cached:           669356 kB
SwapCached:        11084 kB
Active:          1477644 kB
Inactive:         941728 kB
SwapTotal:       1179644 kB
SwapFree:        1081312 kB
DirectMap4k:     1541632 kB
DirectMap2M:     2652160 kB
```

Amb la comanda `free` es pot veure informació resumida sobre la memòria RAM i la memòria virtual \(memòria d'intercanvi o **swap**\): el total, la quantitat utilitzada i la quantitat lliure.

```text
usuari@usxxx:~$ free
                  total      usado        libre    compart.     búffers     almac.
Mem:            4047564    3640916       406648       96748       38364     532712
-/+ buffers/cache:         3069840       977724
Intercambio:    1179644      98332      1081312
```

### Altres comandes per veure el maquinari

* **lshw** \(list hardware\): mostra tota la configuració del maquinari.
* **lscpu** \(list cpu\): mostra informació sobre la CPU.
* **lspci** \(list pci\): maquinari connectat als ports de la placa mare.
* **lsusb** \(list usb\): maquinari connectat als ports usb.
* **hardinfo**: eina que recull de forma gràfica la informació sobre el maquinari \(cal tenir un entorn gràfic\).

## Monitorització dels processos

### Comanda top

La comanda **top** mostra una llista en temps real dels processos actius al sistema. També permet realitzar diferents accions sobre cadascun d'ells, com matar-los o canviar la seva prioritat.

**Opcions de menú:**

* `h`: mostra l'ajuda.
* `p`: Ordena els processos per càrrega de CPU
* `m`: Ordena els processos per càrrega de RAM
* `q`: Permet sortir del programa.

```text
top - 22:58:01 up 26 min,  1 user,  load average: 0,22, 0,25, 0,26
Tasks: 273 total,   3 running, 270 sleeping,   0 stopped,   0 zombie
%Cpu(s):  3,1 us,  1,2 sy,  0,0 ni, 95,1 id,  0,6 wa,  0,0 hi,  0,0 si,  0,0 st
KiB Mem :  5985624 total,   770544 free,  2628672 used,  2586408 buff/cache
KiB Swap:  6168572 total,  6168572 free,        0 used.  2166152 avail Mem 

  PID USER      PRI  NI  VIRT   RES   SHR S CPU% MEM%   TIME+  Command
20642 sei        20   0 28228  4436  3108 R 100.  0.1  0:00.06 htop
13774 sei       20   0 1036M  146M 83696 S 50.0  2.5  1:10.47 /opt/google/chrome/chrome
    1 root       20   0  181M  3740  2052 S  0.0  0.1  0:02.91 /sbin/init splash
    2 root       20   0     0     0     0 S  0.0  0.0  0:00.00 kthreadd
    4 root        0 -20     0     0     0 I  0.0  0.0  0:00.00 kworker/0:0H
    6 root        0 -20     0     0     0 I  0.0  0.0  0:00.00 mm_percpu_wq
    7 root       20   0     0     0     0 S  0.0  0.0  0:00.31 ksoftirqd/0
    8 root       20   0     0     0     0 I  0.0  0.0  0:24.56 rcu_sched
    9 root       20   0     0     0     0 I  0.0  0.0  0:00.00 rcu_bh
   10 root       RT   0     0     0     0 S  0.0  0.0  0:00.04 migration/0
   11 root       RT   0     0     0     0 S  0.0  0.0  0:00.04 watchdog/0
   12 root       20   0     0     0     0 S  0.0  0.0  0:00.00 cpuhp/0
```

### Comanda htop

La comanda **htop** és una evolució de la comanda **top**.

```text
  1  [||||||||||||||||||||                                                 25.9%]   Tasks: 171, 675 thr; 4 running
  2  [||||||||||||||||||||||||||||||||||||||||||||||||||||||||||           77.6%]   Load average: 1.97 2.08 1.79 
  3  [||||||||||||||||||||||||||||||||                                     42.3%]   Uptime: 06:30:20
  4  [||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||100.0%]
  Mem[|||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||5.12G/5.70G]
  Swp[|||||||||||||||||                                              1.29G/5.88G]

  PID USER      PRI  NI  VIRT   RES   SHR S CPU% MEM%   TIME+  Command
    1 root       20   0  181M  3752  2044 S  0.0  0.1  0:02.88 /sbin/init splash
17991 sei        20   0 1890M 18508 15120 S  0.0  0.3  0:00.46 ├─ C:\windows\system32\explorer.exe /desktop
17995 sei        20   0 1890M 18508 15120 S  0.0  0.3  0:00.00 │  ├─ C:\windows\system32\explorer.exe /desktop
17994 sei        20   0 1890M 18508 15120 S  0.0  0.3  0:00.00 │  ├─ C:\windows\system32\explorer.exe /desktop
17993 sei        20   0 1890M 18508 15120 S  0.0  0.3  0:00.04 │  └─ C:\windows\system32\explorer.exe /desktop
17982 sei        20   0 2033M 11800 10224 S  0.0  0.2  0:00.15 ├─ C:\windows\system32\winedevice.exe
17992 sei        20   0 2033M 11800 10224 S  0.0  0.2  0:00.00 │  ├─ C:\windows\system32\winedevice.exe
17985 sei        20   0 2033M 11800 10224 S  0.0  0.2  0:00.00 │  ├─ C:\windows\system32\winedevice.exe
17984 sei        20   0 2033M 11800 10224 S  0.0  0.2  0:00.00 │  └─ C:\windows\system32\winedevice.exe
17977 sei        20   0 1805M  6356  5752 S  0.0  0.1  0:00.00 ├─ C:\windows\system32\plugplay.exe
17980 sei        20   0 1805M  6356  5752 S  0.0  0.1  0:00.00 │  ├─ C:\windows\system32\plugplay.exe
17979 sei        20   0 1805M  6356  5752 S  0.0  0.1  0:00.00 │  └─ C:\windows\system32\plugplay.exe
```

### Comanda ps

La comanda **ps** permet conèixer els processos que s'estan executant al sistema. Sense opcions ens permet conèixer els processos que s'estan executant en el terminal actual.

```text
usuari@usxxx:~$ ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
0 S  1000  5341  5337  0  80   0 -  6021 wait   pts/0    00:00:00 bash
4 R  1000  5775  5341  0  80   0 -  7607 -      pts/0    00:00:00 ps
```

Per veure la **informació detallada** de tots els processos: `ps aux`

## Monitorització de la xarxa

Per obtenir informació sobre la configuració de la xarxa, es poden utilitzar les següents comandes:

* **ipconfig** mostra l'adreça IP, la màscara \(i altres, com per exemple l'adreça MAC\).
* **route** permet veure la porta d'enllaç.
* **cat /etc/resolv.conf** serveix per saber quins servidors DNS s'han configurat.

Per comprovar la connectivitat:

* **ping** per comprovar la connectivitat punt a punt.
* **traceroute** per descobrir en quin punt falla la connectivitat entre dos punts.
* **host** permet comprovar la resolució DNS.
* **nslookup** \(obsolet; és equivalent a host\): per defecte no ve instal·lat.

Per comprovar la seguretat \(ports oberts\):

* **nmap**: serveix per comprovar els ports oberts en l'ordinador i veure el servei associat.

```text
usuari@usxxx ~ $ nmap localhost

Starting Nmap 7.01 ( https://nmap.org ) at 2018-05-10 19:04 CEST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000059s latency).
Not shown: 997 closed ports
PORT     STATE SERVICE
80/tcp   open  http
631/tcp  open  ipp
3306/tcp open  mysql
```

## Documentació i recursos

* **Guia del servidor de l'Ubuntu** [https://help.ubuntu.com/lts/serverguide/monitoring.html](https://help.ubuntu.com/lts/serverguide/monitoring.html)
* **20 Command Line Tools to Monitor Linux Performance** [http://www.tecmint.com/command-line-tools-to-monitor-linux-performance/](http://www.tecmint.com/command-line-tools-to-monitor-linux-performance/)
* **20 Linux System Monitoring Tools Every SysAdmin Should Know** [https://www.cyberciti.biz/tips/top-linux-monitoring-tools.html](https://www.cyberciti.biz/tips/top-linux-monitoring-tools.html)

