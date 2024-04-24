# Introducción

1. Crear la carpeta de trabajo y el primer inventario solo con el nodo 1.

Crear directorio:

```
mkdir ansible-lab
```

Primer inventario:
```
db ansible_host=192.168.1.79 ansible_user=eperez ansible_become_pass=1598
```

2. Crear un inventario (ficticio) donde tengamos 5 nodos (nodo1 a nodo5) agrupados en los siguientes grupos:
   - `webservers` (nodo1, nodo4, nodo5)
   - `dataservers` (nodo3)
   - `proxy` (nodo2)

```
[webservers]
ansible_host=192.168.1.79 ansible_user=nodo1 
ansible_host=192.168.1.80 ansible_user=nodo4
ansible_host=192.168.1.81 ansible_user=nodo5 

[dataservers]
ansible_host=192.168.1.81 ansible_user=nodo3

[proxy]
ansible_host=192.168.1.81 ansible_user=nodo2
```

3. Crear un fichero de configuración para evitar tener que incluir el inventario, para que nos haga todo con permisos de superusuario, etc.

- Crear un archivo ansible.cfg en el directorio de trabajo.
- Suponiendo que el inventario se llama "inventario" y el usuario remoto "eperez":

```
[defaults]
inventory = inventario
remote_user = eperez
ask_become_pass = true
```

4. Lanzar con comando: ping a todos los nodos webservers del inventario ficticio para comprobar cuales están activos y cuales no.

```yml
- name: check Connection
  hosts: nodoaall
  tasks:
    - name: Ping host
      ping:
```

5. Instalar en linea de comandos con apt, apache2.

```
ansible web_ervers -b -m apt -a "name=apache2 state=latest"
```

6. Crear un playbook que a través de shell lance un comando que guarde la fecha actual en un fichero llamado `/tmp/fechahora.data`.

crar un archivo llamado fechadora.data 

```yml
---
- hosts: all
  tasks:
    - name: Guardar fecha en fechadora.data en la ruta /tmp
      shell: echo "22/04/2024" > /tmp/fechahora.data
...
```

Se ejecuta el playbook:

```
ansible-playbook fechaplaybook.yml --ask-pass --ask-become-pass -u eperez
```

Se ejecuta el siguiente comando para comprobar que se ha creado el archivo con la fecha:

```
ansible grupo1 -a 'cat /tmp/fechahora.data' -i inventario --ask-become-pass -u eperez
```
Respuesta:

```
192.168.1.79 | CHANGED | rc=0 >>
22/04/2024

```

7. Crear un playbook que haga las tareas anteriores 4 y 5 para el nodo1.

```yml
---
- hosts: nodo1
  user: eperez
  tasks:
   - name: Ping host
      ping:

    - name: Instalación de Apache en nodo1
        apt:
          name: apache2
          state: present
...
```

8. Modificar el anterior playbook para añadir:
   - una tarea más que compruebe que apache2 está funcionando.
   - una tarea que instale la base de datos mariadb en su última versión.

``` yml                        
- name: Tareas
  hosts: grupo1
  become: yes
  tasks:

    - name: Comprobar si Apache2 está en funcionamiento
      systemd:
        name: apache2
        state: started
        enabled: yes

    - name: Instalación de MariaDB en su última versión
      apt:
        name: mariadb-server
        state: latest

```


