- Parametrizar todos los valores posibles con cierto sentido mediante un fichero de variables:

Crear archivo myvar.yml
```
en: enable
dis: disable
port1: 8081/tcp  
port2: 161-162/udp 
source1: 192.168.20.0/24
```

```
- name: Configurar firewall
  hosts: web
  vars_files:
    - myvar.yml
    
  tasks:

  - firewalld:
    service: https
    permanent: true
    state: {{en}}
  
  - firewalld:
      port: {{port1}}   
      permanent: true
      state: {{dis}}

  - firewalld:
      port: {{port2}}    
      permanent: true
      state: {{dis}}

  - firewalld:
      source:{{source1}}   
      zone: internal
      state: enable
```

Definir un playbbok donde se pida al usuario el entorno(dev,pre,pro) de despliegue para cargar las variables del fichero correspondiente al entorno.

Crear arvhivo var_prompt

```
- name: Pedir entorno
  hosts: grupo1
  become: yes
  vars_prompt:

    - name: dev
      prompt: ¿Dev?
      private: no

    - name: pre
      prompt: ¿Pre?
      private: no

    - name: pro
      prompt: ¿Pro?
      private: no
    
   tasks:
```


Siguiendo la documentación de ansible obtener estos valores de un nuestro máquina local como de un nodo: os family , hostname y ipv4

Usar el módulo setup para obtener la ipv6 del nodo, utilizando comandos ad-hoc. El resultado se debe almacenarse en un fichero, todo se debe ejecutar en un único comando.

Crea un fact personalizado que te permita obtener la variables del nodo remoto.