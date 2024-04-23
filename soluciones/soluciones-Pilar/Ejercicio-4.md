# Imports y handler  


1. Reutiliza el playbook de instalación de apache2 agregandole un paso adicional donde nos permita iniciar el servicio. Tras tener esto hacer uso del import para llamar a nuestor playbook de instalación y arranque del servicio.
- name: Tareas para el nodo1
  hosts: nodo1
  become: yes
  tasks:
    - name: Ping a todos los nodos webservers
      shell: ansible webservers -m ping

    - name: Instalación de Apache en nodo1
      apt:
        name: apache2
        state: present

    - name: Iniciar el servicio de Apache
      service:
        name: apache2
        state: started
        enabled: yes

    - name: Comprobar si Apache2 está en funcionamiento
      systemd:
        name: apache2
        state: started
        enabled: yes

    - name: Instalación de MariaDB en su última versión
      apt:
        name: mariadb-server
        state: latest
# import del playbook
- name: Ejecutar playbook de instalación de Apache
  hosts: nodo2
- name: Importar playbook de instalación de Apache
  import_playbook: instalarapache.yaml


2. Queremos que si modificamos la configuración de nuestro servicio de apache2 automaticamente se reinicie el servicio. ¿ Como podemos aplicar esto con ansible ? Crea el recurso necesario
# Para realizar esto utilizaremos un handler que permite asegurar que ciertas acciones se cumplan solo cuando sea  necesario
- name: Verify apache installation
  hosts: nodo1
  vars:
    http_port: 80
    max_clients: 200
  remote_user: root
  tasks:
    - name: Write the apache config file
      ansible.builtin.template:
        src: /srv/httpd.j2
        dest: /etc/httpd.conf
      notify:
        - Restart apache
    - name: Ensure apache is running
      ansible.builtin.service:
        name: httpd
        state: started
  handlers:
    - name: Restart apache
      ansible.builtin.service:
        name: httpd
        state: restarted


3. Crea un playbook ``Limpiar dependencias y la cache``, después crear otro playbook que haga uso de este mismo mediante un import 

limpiar.yml:
- name: Limpiar dependencias y la cache
  hosts: nodo2
  become: yes
  tasks:
    - name: Limpiar dependencias

    - name: Limpiar la cache
      
 # Luego, en otro playbook, importa este playbook para ejecutar las tareas de limpieza:
- name: Ejecutar limpieza de dependencias y cache
  hosts: nodo2
- name: Importar playbook de limpieza
  import_playbook: limpiar.yml



