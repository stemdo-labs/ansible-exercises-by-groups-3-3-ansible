# Ejercicios bucles

1. Queremos mostrar los ficheros con `ls /`, `ls -a /`, `ls -la /` de aquellos nodos que sean Ubuntu.

```   yml                                                                                        
- name: Mostrar archivos en nodos Ubuntu
  hosts: grupomixto
  tasks:
    - name: Mostrar ficheros
      ansible.builtin.shell: "ls {{ item }}"
      loop:
        - "/"
        - "-a /"
        - "-la /"
      register: ls_output

    - name: Imprimir salida de ls
      debug:
        var: ls_output.stdout_lines
```

2. Escribir una tarea que copie la lista de ficheros JSON que se acompaña desde `/home/usuario/json` a `/tmp/jsons`.

```      yml                                                                                  
- name: Copiar archivos JSON
  hosts: grupo1
  tasks:
    - name: Crear directorio /home/eperez/json
      ansible.builtin.file:
        path: /home/eperez/json
        state: directory
        mode: '0755'

    - name: Crear directorio /tmp/jsons
      ansible.builtin.file:
        path: /tmp/jsons
        state: directory
        mode: '0755'

    - name: Copiar archivos JSON a /tmp/jsons
      ansible.builtin.copy:
        src: "{{ item }}"
        dest: /tmp/jsons
      loop:

    - name: Lista los ficheros
      shell: ls /home/eperez/json/*.json
      register: ls_output

    - name: Copiar jsons en /tmp/jsons
      copy:
        src: "{{ item }}"
        dest: "/tmp/jsons"
      loop: "{{ ls_output.stdout_lines }}"
```

3. Crear una tarea que instale con loop: `curl`, `apt-transport-https`, `ca-certificates`, `software-properties-common`, `glances`, `git` y `vim`.

```    yml                                                                                     
- name: Instalaciones
  hosts: grupo1
  become: yes
  tasks:
    - name: Install
      apt:
        name: "{{ item }}"
        state: present
      loop:
        - curl
        - apt-transport-https
        - ca-certificates
        - software-properties-common
        - glances
        - git
        - vim

```

4. ¿Y si ahora queremos distinguir entre las distintas distribuciones de Linux? (No usar el módulo package)
    - Primero distínguelas a través de tags.
	- En segundo lugar distínguelas a partir de capturar qué distribución es la del host.

    ```         yml                                                                                
    - name: Instalaciones
      hosts: grupo1
      become: yes
      tasks:
    - name: Instalar paquetes ubuntu
      apt:
        name: "{{ item }}"
        state: latest
      loop:
        - curl
        - apt-transport-https
        - ca-certificates
        - software-properties-common
        - glances
        - git
        - vim
      tags: ubuntu
      when: ansible_distribution == "Ubuntu"

    - name: Instalar paquetes CentOS
      yum:
        name: "{{ item }}"
        state: latest
      loop:
        - curl
        - apt-transport-https
        - ca-certificates
        - software-properties-common
        - glances
        - git
        - vim
      tags: centos  
      when: ansible_distribution == "CentOS"
    ```

5. Ahora sí que sí vamos a instalar Nginx. Para ello tenemos que realizar las siguientes tareas:
	- Instalar el propio Nginx con su apt update y todo.
	- Permitir el paso a través de cortafuegos. Para ello, debemos escoger entre Ngilista nx Full, Nginx HTTP, Nginx HTTPS.
	- Verificar el status del cortafuegos. Capturar la salida para nos muestre que todo ha ido bien.
	- Verificar que el servicio está funcionando.

    ```yml
    - name: Instalar Nginx
      hosts: nodo1
      become: true
      tasks:
      - name: Instalar Nginx
        apt:
          name: nginx
          state: latest

    ```



6. Crear una tarea que, dada una lista de usuarios y una lista de grupos como los anteriores, los combine todos contra todos creando el usuario y el grupo. 

    Grupos:
   - usuarios
   - admin
   - desarrollo
   - testing
   - produccion

    Usuarios:
    - Pedro
    - Juan
    - Maria
    - Ana
    - Antonio
    - Marta
    - Jose

    ```yml
    - name: Añadir grupos
  hosts: grupo1
  become: yes
  tasks:
    - name: Crear grupos
      ansible.builtin.group:
        name: "{{ item }}"
        state: present
      loop:
        - usuarios
        - admin
        - desarrollo
        - testing
        - produccion
      register: echo

    - name: Añadir usuarios
      ansible.builtin.user:
        name: "{{ item }}"
        state: present
      loop:
        - Pedro
        - Juan
        - Maria
        - Ana
        - Antonio
        - Marta
        - Jose




    ```

7. Ahora queremos ser más específicos y crear los usuarios con estas especificaciones:

| Usuarios | Grupos a los que pertenece     | Expira la cuenta | uid  | Crear Clave SSH |
|----------|--------------------------------|-------------------|------|-----------------|
| Pedro    | admin, usuarios, produccion    | no                | 1040 | si, rsa 2048    |
| Juan     | usuarios, desarrollo           | no                | 1041 | no              |
| Maria    | usuarios, desarrollo           | no                | 1042 | no              |
| Ana      | testing, produccion            | no                | 1043 | si, rsa 2048    |
| Antonio  | usuarios, testing              | no                | 1044 | no              |
| Marta    | usuarios, produccion           | no                | 1045 | no              |
| Jose     | admin, usuarios, desarrollo    | no                | 1046 | si, rsa 2048    |
| Invitado1| usuarios                       | si                | -    | no              |
| Invitado2| usuarios                       | si                | -    | no              |

 ```yml
- name: Lista de usuarios
  hosts: localhost
  tasks:
    - name: Crear usuarios
      user:
        name: "{{ item.name }}"
        groups: "{{ item.groups }}"
        expires: "{{ item.expire }}"
        uid: "{{ item.uid }}"
        generate_ssh_key: "{{ item.generate_ssh_key }}"
        ssh_key_bits: 2048
        ssh_key_file: "/home/{{ item.name }}/.ssh/id_rsa"
      loop:
        - { name: Pedro, groups: admin,usuarios,produccion, expire: no, uid: 1040, generate_ssh_key: yes }
        - { name: Juan, groups: usuarios,desarrollo, expire: no, uid: 1041, generate_ssh_key: no }
        - { name: Maria, groups: usuarios,desarrollo, expire: no, uid: 1042, generate_ssh_key: no }
        - { name: Ana, groups: testing,produccion, expire: no, uid: 1043, generate_ssh_key: yes }
        - { name: Antonio, groups: usuarios,testing, expire: no, uid: 1044, generate_ssh_key: no }
        - { name: Marta, groups: usuarios,produccion, expire: no, uid: 1045, generate_ssh_key: no }
        - { name: Jose, groups: admin,usuarios,desarrollo, expire: no, uid: 1046, generate_ssh_key: yes }
        - { name: Invitado1, groups: usuarios, expire: yes }
        - { name: Invitado2, groups: usuarios, expire: yes }
 
    ```
