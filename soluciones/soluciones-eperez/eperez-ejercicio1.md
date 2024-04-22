# Introducción

1. Crear la carpeta de trabajo y el primer inventario solo con el nodo 1.

Crear directorio:

```
mkdir ansible-lab
```

Primer inventario:
```
[grupo1]
192.168.1.79
```

2. Crear un inventario (ficticio) donde tengamos 5 nodos (nodo1 a nodo5) agrupados en los siguientes grupos:
   - `webservers` (nodo1, nodo4, nodo5)
   - `dataservers` (nodo3)
   - `proxy` (nodo2)

```
[webservers]
nodo_1
nodo_4
nodo_5

[dataservers]
nodo_3

[proxy]
nodo2
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

```
ansible webservers -m ping 
```

5. Instalar en linea de comandos con apt, apache2.

```
ansible web_ervers -b -m apt -a "name=apache2 state=latest"
```

6. Crear un playbook que a través de shell lance un comando que guarde la fecha actual en un fichero llamado `/tmp/fechahora.data`.

crar un archivo llamado fechadora.data con la fecha actual

```
---
- hosts: all
  tasks:
    - name: Guardar fecha en fechadora.data en la ruta /tmp
      shell: echo "22/04/2024" > /tmp/fechahora.data
...
```

7. Crear un playbook que haga las tareas anteriores 4 y 5 para el nodo1.

```
---
- hosts: nodo1
  user: eperez
  tasks:
    - name: Guardar fecha en fechadora.data en la ruta /tmp
      shell: ansible web_servers -m ping 
      shell: ansible webservers -b -m apt -a "name=apache2 state=latest"

...
```

8. Modificar el anterior playbook para añadir:
   - una tarea más que compruebe que apache2 está funcionando.
   - una tarea que instale la base de datos mariadb en su última versión.



