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
    Prametrizaremos los valores constantes que son susceptibles de ser repetidos
    firewall_service: https
    firewall_ports: 
      - 8081/tcp
      - 161-162/udp    
    firewall_source: 192.168.20.0/24
   ----------------------------------------------------------------
   entonces en el yml:
     ```yaml
     
- name: Configurar firewall
  hosts: web
  tasks:
    - firewalld:
        service: '{{ firewall_service }}'
        permanent: true
        state: enable
    - firewalld:
        source: '{{ firewall_source }}'
        zone: internal
        state: enable
    - name: Configurar puertos en el firewall
      firewalld:
        port: '{{ item }}'
        permanent: true
        state: disabled
      loop: '{{ firewall_ports }}'
      

2. Definir un playbbok donde se pida al usuario el entorno(``dev``,``pre``,``pro``) de despliegue para cargar las variables del fichero correspondiente al entorno. 


3. Siguiendo la documentación de ansible obtener estos valores de un nuestro máquina local como de un nodo: `os family` , `hostname` y `ipv4`
ansible localhost -m setup -a 'filter=ansible_distribution_family,ansible_hostname,ansible_default_ipv4'
ansible nodo2 -m setup -a 'filter=ansible_distribution_family,ansible_hostname,ansible_default_ipv4'


4. Usar el módulo `setup` para obtener la ipv6 del nodo, utilizando comandos ad-hoc. El resultado se debe almacenarse en un fichero, todo se debe ejecutar en un único comando.
ansible nodo2 -m setup -a 'filter=ansible_default_ipv6' > ipv6.txt




5. Crea un fact personalizado que te permita obtener la variables del nodo remoto.