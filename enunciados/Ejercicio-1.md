# Introducción

1. Crear la carpeta de trabajo y el primer inventario solo con el nodo 1.
mkdir ansible
nano inventario
nodo1
192.168.162.140

2. Crear un inventario (ficticio) donde tengamos 5 nodos (nodo1 a nodo5) agrupados en los siguientes grupos:
   - `webservers` (nodo1, nodo4, nodo5)
   - `dataservers` (nodo3)
   - `proxy` (nodo2)

[webservers]
nodo1
nodo4
nodo5

[dataservers]
nodo3

[proxy]
nodo2

3. Crear un fichero de configuración para evitar tener que incluir el inventario, para que nos haga todo con permisos de superusuario, etc.
nano ansible.cfg

[defaults]
inventory = ./inventario

[privilege_escalation]
become = yes
become_user = vboxuser
become_method = sudo

4. Lanzar con comando: ping a todos los nodos webservers del inventario ficticio para comprobar cuales están activos y cuales no.
ansible webservers -m ping -i inventario


5. Instalar en linea de comandos con apt, apache2.
ansible webservers -m apt -a "name=apache2 state=present" -i inventario --become

6. Crear un playbook que a través de shell lance un comando que guarde la fecha actual en un fichero llamado `/tmp/fechahora.data`.
nano guardarfecha.yml

- name: Guardar la fecha actual en un archivo
  hosts: nodo2
  tasks:
    - name: Guardar la fecha actual en /tmp/fechahora.data
      shell: date > /tmp/fechahora.data

ansible-playbook guardarfecha.yml -i inventario -u vboxuser --ask-pass


vboxuser@control:~/ansible$ 


7. Crear un playbook que haga las tareas anteriores 4 y 5 para el nodo1.
---
- name: Tareas para el nodo1
  hosts: nodo1
  become: yes
  tasks:
    - name: Ping a todos los nodos webservers
      ping:

    - name: Instalación de Apache en nodo1
      apt:
        name: apache2
        state: present



8. Modificar el anterior playbook para añadir:
   - una tarea más que compruebe que apache2 está funcionando.
   - una tarea que instale la base de datos mariadb en su última versión.
---
- name: Tareas para el nodo1
  hosts: nodo1
  become: yes
  tasks:
    - name: Ping a todos los nodos webservers
      ping:

    - name: Instalación de Apache en nodo1
      apt:
        name: apache2
        state: present

    - name: Comprobar si Apache2 está en funcionamiento
      systemd:
        name: apache2
        state: started
        enabled: yes

    - name: Instalación de MariaDB en su última versión
      apt:
        name: mariadb-server
        state: latest
y para ejecutarlo ansible-playbook tasks.yml -i inventario -u vboxuser --ask-pass --ask-become-pass

RESULTADO:

PLAY [Tareas para el nodo1] *******************************************************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************************************************
ok: [nodo2]

TASK [Ping a todos los nodos webservers] ******************************************************************************************************************************************************************
ok: [nodo2]

TASK [Instalación de Apache en nodo1] *********************************************************************************************************************************************************************
changed: [nodo2]

TASK [Comprobar si Apache2 está en funcionamiento] ********************************************************************************************************************************************************
ok: [nodo2]

TASK [Instalación de MariaDB en su última versión] ********************************************************************************************************************************************************
changed: [nodo2]

PLAY RECAP ************************************************************************************************************************************************************************************************
nodo2                      : ok=5    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  