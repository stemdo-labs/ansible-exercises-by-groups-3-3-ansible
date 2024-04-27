1. Parametrizar todos los valores posibles con cierto sentido mediante un fichero de variables:

Crear archivo myvar.yml
``` 
port1: 8081/tcp  
port2: 161-162/udp 
source1: 192.168.20.0/24
```

``` yml
- name: Configurar firewall
  hosts: web
  vars_files:
    - myvar.yml
    
  tasks:

  - firewalld:
    service: https
    permanent: true
    state: enable
  
  - firewalld:
      port: {{port1}}   
      permanent: true
      state: disable

  - firewalld:
      port: {{port2}}    
      permanent: true
      state: disable

  - firewalld:
      source: {{source1}}   
      zone: internal
      state: enable
```

2. Definir un playbbok donde se pida al usuario el entorno(dev,pre,pro) de despliegue para cargar las variables del fichero correspondiente al entorno.

Se crean los archivos dev_vars.yml, pre_vars.yml, pro_vars.yml, 

Crear archivo var_prompt con las variables 

``` yml
- name: Pedir entorno
  hosts: grupo1
  become: yes
  vars_prompt:

    - name: dev
      prompt: Introduzca el entorno de despliegue ¿dev, pre o pro?
      private: no
    
    tasks:
    - name: Cargar variables del entorno
      include_vars: "{{ entorno }}_vars.yml"
```


3. Siguiendo la documentación de ansible obtener estos valores de un nuestro máquina local como de un nodo: os family , hostname y ipv4
                              
``` yml                              
- name: Obtener valores os_family, hostname e ipv4
  hosts: all
  become: yes
  tasks:
    - name: Obtener valor os_family
      debug:
        msg: "El sistema operativo de mi máquina es {{ ansible_os_family }}"

    - name: Obtener valor hostname
      debug:
        msg: "El nombre de mi máquina es {{ ansible_hostname }}"

    - name: Obtener valor ipv4
      debug:
        msg: "La dirección IP de mi máquina es {{ ansible_default_ipv4.address }}"
```

4. Usar el módulo setup para obtener la ipv6 del nodo, utilizando comandos ad-hoc. El resultado se debe almacenarse en un fichero, todo se debe ejecutar en un único comando.

```
ansible -m setup -a 'filter=ansible_default_ipv6.address' -i inventario --ask-pass -u eperez > ipv6.txt
```

5. Crea un fact personalizado que te permita obtener la variables del nodo remoto.

- Crear archivo de hechos variables con el siguiente contenido:

```
ruta: /etc/ansible/facts.d
myfile: variablesmelas.fact

```
- Crear archivo variablesmelas.fact con el siguiente contenido:

```                          
[myvar]
manzana = fruta
matrix = pelicula
```
- Crear playbook para crear el directorio y copiar el archivo de variables en el nodo remoto

``` yml                          
- name: Obtener variables
  hosts: grupo1
  become: yes
  tasks:
    - name: Crear el directorio {{ ruta }}
      file:
        path: "{{ ruta }}"
        state: directory
        recurse: yes
    - name: Copiar el archivo en el directorio
        copy:
          src: "{{ myfile }}"
          dest: "{{ ruta }}"
```
- Ejecutar el playbook
- ansible variables -m command -a "ls /etc/ansible/facts.d" - i inventario -u eperez para comprobar que el archivo se ha creado
- para obtener las variables: 

```
ansible grupo1 -m setup -a 'filter=ansible_local' -i inventario -u eperez
```