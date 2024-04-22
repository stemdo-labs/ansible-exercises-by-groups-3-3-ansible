# Ejercicios módulos

1. Comprimir el directorio /apps/tomcat en formato gz (El directorio es opcional, siempre que puedas aplicar el ejercicio)

    - Sin usar variables
    - Usando variables
    
   - name: Comprimir directorio /apps/tomcat sin variables
  hosts: localhost
  tasks:
    - name: Comprimir directorio /apps/tomcat
      archive:
        path: /apps/tomcat
        dest: /tmp/tomcat_backup.tar.gz
        format: gz
----------------------------------------------------------------
- name: Comprimir directorio /apps/tomcat con variables
  hosts: localhost
  vars:
    source_dir: /apps/tomcat
    dest_dir: /tmp
    archive_name: tomcat_backup.tar.gz
  tasks:
    - name: Comprimir directorio {{ source_dir }}
      archive:
        path: "{{ source_dir }}"
        dest: "{{ dest_dir }}/{{ archive_name }}"
        format: gz



2. Comprimir todos los archivos de la carpeta /apps/tomcat/logs y de la carpeta /var/log/tomcat/  Excluir aquellos que tengan la palabra access y sean de extensión ``.txt`` (El directorio es opcional, siempre que puedas aplicar el ejercicio)
 - name: Comprimir logs de Tomcat sin archivos de acceso en formato gz
  hosts: node2
  tasks:
    - name: Comprimir archivos de /apps/tomcat/logs
      archive:
        path: /apps/tomcat/logs
        dest: /tmp/tomcat_logs.tar.gz
        format: gz
        exclude_path: '*.txt'
        exclude: '*access*'

    - name: Comprimir archivos de /var/log/tomcat/
      archive:
        path: /var/log/tomcat/
        dest: /tmp/tomcat_logs_var.tar.gz
        format: gz
        exclude: '*.txt'
        exclude: '*access*'


3. Instalar paquetes .deb desde local y desde internet, probad a instalar algún programa que conozcáis 
descargamos wget http://nginx.org/packages/ubuntu/pool/nginx/n/nginx/nginx_1.18.0-2~bionic_amd64.deb


- name: Instalar paquete .deb desde local
  hosts: node2
  tasks:
    - name: Copiar paquete .deb al servidor remoto
      copy:
        src: /nginx_1.18.0-2~bionic_amd64.deb
        dest: /tmp/nginx_1.18.0-2~bionic_amd64.deb

    - name: Instalar paquete .deb
      apt:
        deb: /tmp/nginx_1.18.0-2~bionic_amd64.deb
        state: present



- name: Instalar paquete .deb desde internet
  hosts: node2
  tasks:
    - name: Instalar paquete .deb desde repositorio
      apt:
        name: vim
        state: present
