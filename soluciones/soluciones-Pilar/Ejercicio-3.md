# Variables y facts


1. Parametrizar todos los valores posibles con cierto sentido mediante un fichero de variables.

    ```yaml
    - name: Configurar firewall
      hosts: web
      tasks:

      - firewalld:
        service: https
        permanent: true
        state: enable
      
      - firewalld:
          port: 8081/tcp    
          permanent: true
          state: disable

      - firewalld:
          port: 161-162/udp    
          permanent: true
          state: disable

      - firewalld:
          source: 192.168.20.0/24   
          zone: internal
          state: enable
    ```
# Prametrizaremos los valores constantes que son susceptibles de ser repetidos
    firewall_service: https
    firewall_ports: 
      - 8081/tcp
      - 161-162/udp    
    firewall_source: 192.168.20.0/24
   ----------------------------------------------------------------
# entonces en el yml:
     ```yaml

- name: Configurar firewall
  hosts: web
  vars_files:
    - vars.yml
  become: yes
  tasks:
    - ufw:
        service: '{{ firewall_service }}'
        permanent: true
        state: enabled
    - ufw:
        source: '{{ firewall_source }}'
        zone: internal
        state: enabled
    - name: Configurar puertos en el firewall
      ufw:
        port: '{{ item }}'
        permanent: true
        state: enabled
      loop: '{{ firewall_ports }}'
---

2. Definir un playbbok donde se pida al usuario el entorno(``dev``,``pre``,``pro``) de despliegue para cargar las variables del fichero correspondiente al entorno. 
# Contenido del archivo dev.yml
app_port: 8080
database_host: localhost
# Contenido del archivo pre.yml
app_port: 8081
database_host: pre_database.example.com
# Contenido del archivo pro.yml
app_port: 80
database_host: prod_database.example.com

#Playbook para el deploy deploy.yml
- hosts: nodo2
  gather_facts: false
  become: 'yes'

  tasks:
    - name: Solicitar valor de environment
      pause:
        prompt: "Por favor, introduce el nombre del archivo de variables (por ejemplo, pre, dev, pro):"
      register: user_input

    - name: Mostrar valor de environment
      debug:
        var: user_input.user_input

    - name: Cargar variables del entorno
      include_vars:
        file: "{{ user_input.user_input }}.yml"
      when: user_input.user_input is defined
      
    - name: Mostrar variables cargadas
      debug:
        var: app_port
    


3. Siguiendo la documentación de ansible obtener estos valores de un nuestro máquina local como de un nodo: `os family` , `hostname` y `ipv4`

ansible localhost -m setup -a 'filter=ansible_distribution_family,ansible_hostname,ansible_default_ipv4'
ansible nodo2 -m setup -a 'filter=ansible_distribution_family,ansible_hostname,ansible_default_ipv4'


4. Usar el módulo `setup` para obtener la ipv6 del nodo, utilizando comandos ad-hoc. El resultado se debe almacenarse en un fichero, todo se debe ejecutar en un único comando.

ansible nodo2 -m setup -a 'filter=ansible_default_ipv6' > ipv6.txt




5. Crea un fact personalizado que te permita obtener la variables del nodo remoto.
# Primero creo mi script
#!/bin/bash
echo '{"os_type": "$(uname -s)"}'


#Creo el playbook y lo lanzo como una tarea

- hosts: nodo2
  gather_facts: no
   
  tasks:
    - name: Ejecutar script de fact personalizado
      script: script.sh
      register: custom_fact_output

    - name: Mostrar el resultado del fact personalizado
      debug:
        var: custom_fact_output.stdout