# Scripts

## Introducció

> Un guió o **script** és un arxiu de text executable que conté una sèrie de comandes. Quan s'executa l'script, s'executen les comandes que conté.

Es pot crear i editar un script amb qualsevol editor de text pla \(**nano**, **gedit**...\).

### Creació de l'script

El més habitual és posar l'extensió **.sh** als scripts de Linux:

`nano crea_directoris.sh`

En la **primera línia** cal indicar amb quin intèrpret de comandes s'han d'executar les comandes que conté \(`#!/bin/bash`\).

A continuació es posen les comandes en el mateix ordre en què s'han d'executar:

```bash
#!/bin/bash
#Comentari explicatiu de què fa l'script
mkdir /home/usuari/SOX
mkdir /home/usuari/SOX/UF1
mkdir /home/usuari/SOX/UF1/Apunts
mkdir /home/usuari/SOX/UF1/Practiques
```

> Tota línia que comenci amb coixinet `#` es tracta d'un **comentari** i no serà llegida pel intèrpret de comandes \(excepte la primera línia que estableix el shell pel que ha sigut dissenyat el script\).

### Assignació de permisos

Un cop creat el script cal modificar els permisos de l'arxiu per permetre que s'executi:

Li podem donar permisos d'execució amb la comanda:

```text
chmod a+x crea_directoris.sh
```

> Un cop assignats els permisos, si fem la comanda `ls` veurem com el **color del fitxer** ha canviat indicant que és un fitxer executable i per tant es pot executar.

### Execució de l'script

> Linux busca els programes executables en els directoris que estan definits a la variable de sistema `$PATH`.

```text
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```

Si el nostre script es troba en una d'aquestes ubicacions podrem executar-lo posant únicament el nom del script.

Si no es troba en aquestes ubicacions haurem d'indicar la ruta sencera on es troba el fitxer executable o bé si està al mateix directori cal posar `./`:

```text
./crea_directoris.sh
```

També es pot executar amb la comanda **bash**. En aquest cas no caldria canviar els permisos ni posar `./` al davant si estem en el mateix directori:

```text
bash crea_directoris.sh
```

Per comprovar que l'script funsció correctament, es pot executar l'script **pas a pas** amb el paràmetre `-x`.

```text
bash -x crea_directoris.sh
```

## Variables

Com en qualsevol llenguatge de programació, les variables s'utilitzen per **poder guardar informació** i a partir d'aquesta poder prendre decisions o realitzar operacions. 

És important posar un nom a la variables que descrigui el seu ús. El seu nom no pot ser cap de les  paraules reservades \(exemple: _echo_\). 

Hi ha dues formes diferents d'utilitzar-la depenent de si volem assignar-li un valor o utilitzar-la \(realitzar una operació\). A continuació veurem un exemple en el qual se li assigna a la variable nombre un valor i després es mostra per pantalla.

```bash
#!/bin/bash
numero=5
echo "el valor de la variable és "$numero
```

Com es pot veure en l'exemple quan es vol accedir a la valor de la variable s'utilitza el símbol **$**.

## Paràmetres

Sovint cal que els nostres scripts rebin paràmetres des de la línia d'ordres per fer-los més versàtils. Aquest és el cas de la comanda `ls` en que li podem passar el paràmetre _-l_ per modificar la sortida de dades `ls -l`

Els paràmetres es poden usar dins de l'script com qualsevol altra variable de _Shell_. Els paràmetres dins el shell script són accessibles utilitzant les variables: **$0** és el nom de el programa, **$1** és el primer paràmetre, **$2**, és el segon paràmetre, etc. A més, s'utilitza la variable **$\#** que estableix el nombre de paràmetres que ha rebut el shell. A continuació veurem un exemple:

```bash
#!/bin/bash
echo "El nom del programa és "$0
echo "El primer paràmetre rebut és "$1
echo "El segon paràmetre rebut és "$2
echo "..."
echo "En total s'han rebut "$#" paràmetres"
```

Si l'executem:

```
bash param_expl.sh 3 5
```

Obtenim la sortida:

`El nom del programa és param_expl.sh  
El primer paràmetre rebut és 3  
El segon paràmetre rebut és 5  
...  
En total s'han rebut 2 paràmetres`

| Paràmetre | Descripció | Valor rebut |
| :--- | :--- | :--- |
| $0 | Nom del programa | param\_expl.sh |
| $1 | Primer paràmetre | 3 |
| $2 | Segon paràmetre | 5 |
| $\# | Números de paràmetres | 2 |
| $@ | Valors dels paràmetres passats \(valors separats: "1" "2" ...\) | 3 5 |
| $\* | Valors dels paràmetres passats \(valors en bloc: "1 2"...\) | 3 5 |

## Entrada i sortida de dades

### E/S per consola

### **Redirecció de l'E/S**

### **Comentes**

En els scripts s’utilitzen **tres tipus de cometes**:

|  | Teclat | Significat | Acció |
| :--- | :---: | :---: | :---: |
| **Cometes simples \(' '\)** | Estan a sota l'interrogant ? | Literal | S'interpreta de forma literal, sense substituir camp variable. |
| **Cometes dobles \(" "\)** | Shift + Tecla 2 | Substitució de variables | Es substitueixen les variables. |
| **Cometes invertides \(\` \`\)** | Tecla accent obert + Espai | Execució de comanda o expressió | S'executen les línies de comandes o expressions. |

**Exemples**:

|  | Comanda | Resultat |
| :--- | :--- | :--- |
| **Cometes simples \(' '\)** | echo 'Hola $NOM' | Hola $NOM |
| **Cometes dobles \(" "\)** | echo "Hola $NOM" | Hola Jordi |
| **Cometes invertides \(\` \`\)** | echo 'Avui és el dia' \`date +%D\` | Avui és el dia 25/04/2020 |

**Exemple:**

```bash
#!/bin/bash
#Script amb cometes

#Demanem per pantalla el nom i el guardem a la variable NOM
read –p 'Escriu el teu nom: ' NOM

echo 'Hola $NOM'
echo "Hola $NOM"
echo `Hola $NOM`
```

```text
$ ./exemple.sh
Escriu el teu nom: Jordi
Hola $NOM
Hola Jordi
./exemple.sh: line 8: Hola: no s'ha trobat l'ordre
```

* La primera línia `echo 'Hola $NOM'` amb cometes simples no s'ha substituït el valor de la variable.
* La segona línia `echo "Hola $NOM"` amb cometes dobles s'ha substituït la variable NOM per Jordi.
* La tercera línia ``echo `Hola $NOM``` amb cometes invertides dona un error al executar-se perquè s'intenta executar una comanda però _**Hola**_ no és una comanda.

\*\*\*\*

## Comandes interessants

## Estructures de control

## Condicions

## Exemples d'scripts

### Trobar els 10 directoris més grans donat un path

```text
$ cat grans
#!/bin/bash
# Llista els 10 directoris que ocupen més espai donat un path

 du $1 | sort -n | tail
```

