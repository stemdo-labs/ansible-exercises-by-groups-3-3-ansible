 Comprimir el directorio /apps/tomcat en formato gz (El directorio es opcional, siempre que puedas aplicar el ejercicio)

Sin usar variables

```
- name: Comprimir directorio
  hosts: grupo1
  become: yes
  
  tasks:
    - name: Comprimir directorio /apps/tomcat
      archive:
        path: /apps/tomcat
        dest: /home/eperez/backup.tar.gz
        format: gz

```


Usando variables
Comprimir todos los archivos de la carpeta /apps/tomcat/logs y de la carpeta /var/log/tomcat/ Excluir aquellos que tengan la palabra access y sean de extensión .txt (El directorio es opcional, siempre que puedas aplicar el ejercicio)

```
- name: Comprimir directorio
  hosts: grupo1
  become: yes
  vars:
    name: "backup.tar.gz"


  tasks:
    - name: Comprimir directorio /apps/tomcat/logs
      archive:
        path: "/apps/tomcat/logs"
        dest: "/home/eperez/{{name}}"
        format: gz
    
    - name: Comprimir directorio /var/log/tomcat
      archive:
        path: "/var/log/tomcat"
        dest: "/home/eperez/{{name}}"
        format: gz
```

Instalar paquetes .deb desde local y desde internet, probad a instalar algún programa que conozcáis

```
- name: deb
  hosts: grupo1
  become: yes

  tasks:
    - name: Instalar tree
      apt:
        name: tree
        state: latest

    - name: Instalar tree
      apt:
        name: tree
        state: latest

```