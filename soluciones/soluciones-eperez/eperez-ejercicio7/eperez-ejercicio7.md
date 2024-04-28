# Condicionales

1. Partiendo de este playbook, y aunque ya sabemos que no se haría de esta manera, gestionar los posibles errores si no tenemos alguna
de las distribuciones. (no busco condicionales, sino bloques, etc)

    ```yaml
    - name: ejercicio
    hosts: all
    tasks:
        - name: instala en ubuntu
        apt:
            name: git
            state: present
        - name: instala en centos
        yum: 
            name: git
            state: present
    ```

    El archivo corregido sería

    ```yml
    - name: ejercicio
      hosts: all
      tasks:
        - block:
            - name: instala en ubuntu
            apt:
                name: git
                state: present
        - block:
            - name: instala en centos
            yum: 
                name: git
                state: present
    ```

2. Utilizando una estructura wait_for, esperar a que un fichero (elegid el nombre y la ubicación o por ejemplo bloqueo.lock) de la máquina 
remota deje de existir en esa máquina remota. Cuando salga del wait_for, que haga una tarea que muestre un mensaje. Para eliminar ese fichero 
de la máquina remota, podéis hacerlo de forma manual o como queráis.

``` yml
- name: Esperar aborrar archivo
  hosts: grupo1
  become: yes
  tasks:
    - name: Esperar a que el archivo deje de existir
      wait_for:
        path: /home/eperez/bloqueo.lock
        state: absent

    - name: Mensaje de archivo borrado
      debug:
        msg: "El archivo bloqueo.lock ha sido eliminado"
```

3. ¿Y si ahora lo quisiéramos como condición previa para ejecutar unas tareas cualquiera? Por ejemplo tomar algunas tareas de algún ejercicio
que sólo se ejecuten si previamente se ha eliminado ese fichero bloqueo.lock 

4. Crear un fichero: ``missecretos.yml`` que albergue unas variables llamadas miuser y micontra, cifrarlo con ansible-vault, y después llamarlo desde un playbook que tenga una tarea que muestre el valor de miuser y micontra

```
ansible-vault create missecretos.yml
```
``` yml
miuser: usuario_secreto
micontra: contraseña_secreta
```
Crear playbook para mostrar valor de miuser y mi contra
``` yml
- name: Archivos cifrados
  hosts: grupo1
  vars_files:
    -C:\Users\eperez\EjerciciosBootcamp\4-Ansible\ansible-exercises-by-groups-3-3-ansible\soluciones\soluciones-eperez\eperez-ejercicio7\missecretos.yml
  tasks:
    - name: Mostrar el valor de miuser y micontra
      debug:
        msg: "Usuario: {{ miuser }}, Contraseña: {{ micontra }}"
```

Comando para ejecutar el playbook

```
ansible-playbook ansiblevaultplay.yml -i inventario -u eperez --ask-pass --ask-vault-pass --ask-become-pass
```


5. Realizar la instalación del rol de mysql, lo podeis crear o buscar en ansible galaxy. Es obligatorio aportar la definición del rol.

Un rol de Ansible es una forma de organizar y reutilizar código en proyectos de automatización. Funciona como un conjunto de tareas, variables y archivos que se pueden utilizar para automatizar una determinada función o configuración en un sistema. Los roles permiten separar y modularizar las tareas relacionadas, lo que facilita el mantenimiento, la reutilización y la colaboración en proyectos más grandes.

Comando para crear la estructura de carpetas de rol mysql

```
ansible-galaxy init mysql
```
├── defaults
│   └── main.yml
├── files
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── README.md
├── tasks
│   ├── main.yml
│   └── mysqlinstall.yml
├── templates
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml

Dentro de la carpeta tarks crear playbook de instalación de mysql
``` yml
- hosts: grupo1
  become: yes
  tasks:
    - name: Instalación de MySQL
      package:
        name: mysql-server
        state: present

```
En el main.yml dentro de la carpeta tasks hacer referencia al playbook anterior

``` yml
# tasks file for mysql
- name: Incluir playbook de instalación de MySQL
  include_tasks: mysqlinstall.yml
```

Crear Playbook principal fuera de la estructura del rol llamando al rol

``` yml
- name: Ejecutar tasks del rol mysql
  hosts: grupo1
  become: yes
  tasks:
    - name: Incluir tareas del rol MySQL
      include_tasks: /home/elisa/ansible-lab/mysql/mysql/tasks/main.yml

```

Ejecutar el Playbook principal.

6. Escoger algún otro rol de ansible galaxy y lanzarlo en vuestras máquinas. Elegid alguno que os lo ponga facil en los requerimientos. 

El rol geerlingguy.apache es un rol de Ansible que automatiza la instalación y configuración del servidor web Apache HTTP en sistemas Linux. 

Ejecutar comando de instalación de rol

```
ansible-galaxy install geerlingguy.apache

```

``` yml
- name: roles
  hosts: grupo1
  become: yes
  roles:
    - geerlingguy.apache
```