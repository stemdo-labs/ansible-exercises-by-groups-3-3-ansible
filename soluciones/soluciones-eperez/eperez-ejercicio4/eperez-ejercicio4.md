# Imports y handler
1. Reutiliza el playbook de instalación de apache2 agregandole un paso adicional donde nos permita iniciar el servicio. Tras tener esto hacer uso del import para llamar a nuestor playbook de instalación y arranque del servicio.

```yml
- name: Tareas
  hosts: grupo1
  become: yes
  tasks:
    - name: Instalación de Apache
      apt:
        name: apache2
        state: present

    - name: Iniciar el servicio
      service:
        name: apache2
        state: started
        enabled: yes

    - name: Comprobar si Apache2 está funcionando
      systemd:
        name: apache2
        state: started
        enabled: yes
```
2. Para hacer la importación del playbook anterior, se crearía el siguiente archivo
```yml
- name: Importar playbook de Apache
  ansible.builtin.import_playbook: ejercicio-4-1.yaml
```

3. Queremos que si modificamos la configuración de nuestro servicio de apache2 automaticamente se reinicie el servicio. ¿ Como podemos aplicar esto con ansible ? Crea el recurso necesario

Se añade un handler con changed_when para que se reinicie solo cuando haya cambiado la configuración


4. Crea un playbook Limpiar dependencias y la cache, después crear otro playbook que haga uso de este mismo mediante un import

``` yml                                                                     
- name: Limpieza
  hosts: grupo1
  become: yes
  tasks:

    - name: limpiar caché
      apt:
        autoclean: yes

```
```yml
- name: Importar playbook de limpieza
  ansible.builtin.import_playbook: ejercicio-4-4.yaml
```
