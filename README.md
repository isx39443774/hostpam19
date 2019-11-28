# PAM

Generem i entrem dins d'un container pam (els fitxers d'aquest container pam han d'estar en el directori actual):
  ```
  docker build -t isx39443774/hostpam19:aware .  

  docker run --rm --name pam -h pam -it isx39443774/hostpam19:aware /bin/bash
  ```
Executem el install perque ens generi els usuaris i ens copi els fitxers desitjats de la configuració

## Creació d'una aplicació PamAware     
Instal·lem el software de python-pam:
  ```
  pip install python-pam
  ```

Escribim el codi del nostre programa pamaware.py

En provem l'execució:
  ```
  python pamaware.py
  nom usuari: anna
  passwd: anna
  0 ​ Success
  1 2 3 4 5 6 7 8 9 10
  ```
  ```
  python pamaware.py
  nom usuari: josep
  passwd: josep
  10 ​ User not known ​ to the underlying authentication module
  ```

## Creació d'un mòdul Pam

Creem el nostre mòdul de pam anomenat pam_mates.py per validar un usuari li farà una pregunta de matemàtiques. Haurem de fer servir el mòdul pam_python.so. I aquest mòdul l'aplicarem a al servei chfn de la següent manera:
  ```
  #%PAM-1
  auth optional pam_echo.so [ auth ----------------- ]
  auth sufficient pam_python.so /tmp/pam_mates.py

  account optional pam_echo.so [ account -------------- ]
  account sufficient pam_python.so /tmp/pam_permit.py

  password include pam_deny.so
  session include pam_deny.so
  ```

Ara ens faltarà compilar pam_python.so. Per realitzar tal tasca, farem les següents ordres:
```
tar xvzf pam-python-1.0.6.tar.gz
dnf -y install sphinx python3-sphinx phyton2-sphinx gcc
dnf -y install pam-devel
dnf -y install redhat-rpm-config
dnf -y install python-devel
## editar línia 201 de: /usr/include/features.h:
# canviar 700 per 600 en la línia # define _XOPEN_SOURCE 700
make
```
Amb els passos anteriors haurem generat pam_python.so i l'haurem de copiar al directori que conté els mòduls de pam:
  ```
  cp src/pam_python.so /usr/lib64/security/.
  ```
Com que en el fitxer chfn hem especificat que els fitxer estan a /tmp els haurem de copiar allà:
  ```
  cp pam_*.py /tmp
  ```
Comprovació:
  ```
  $ chfn anna
Changing finger information for anna.
Name [2]:
Office [2]:
Office Phone [2]:
Home Phone [2]: 2
auth -----------------
Quant fan 3*2?
434
account --------------
chfn: Permission denied
chfn: changing user attribute ​ failed : ​ Permission denied
```
