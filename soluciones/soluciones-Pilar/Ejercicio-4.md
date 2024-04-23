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

