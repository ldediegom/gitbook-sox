# Gestió de dominis en escenaris heterogenis

**Windows** utilitza **Active Directory** per gestionar els usuaris i recursos d'un domini.

Es pot connectar un client Linux a un domini gestionat amb Active Directory fent una configuració manual bastant laboriosa o utilitzant un programa que s'encarregui de realitzar la major part de la configuració amb unes poques comandes.

Per crear en Linux un domini similar a Active Directory, per connectar-hi tant clients Windows com clients Linux, es pot utilitzar **Samba AD DC**.

### Per contra...

En un entorn amb diversos tipus de sistemes operatius, és difícil garantir que totes les configuracions i serveis disponibles a uns sistemes es puguin aplicar de la mateixa manera en altres.

En el cas de la integració amb Active Directory, en sistemes Linux poden aparèixer les següents limitacions:

* No s’apliquen GPO
* No s'interpreten les configuracions de sistema, scripts d'inici/tancament, restriccions d'usuari...
* Els canvis de configuració s'han de gestionar localment o mitjançant eines de tercers.
* No hi ha perfils mòbils gestionats des de Windows - poden ser locals o fent servir NFS -.

&#x20;
