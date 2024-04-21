# Condicionales

1. Partiendo de este playbook, y aunque ya sabemos que no se haría de esta manera, gestionar los posibles errores si no tenemos alguna
de las distribuciones. (no busco condicionales, sino bloques, etc)

    ```yaml
    - name: ejercicio
    hosts: all
    tasks:
        - name: instala en ubuntu
        apt:
            name: git
            state: present
        - name: instala en centos
        yum: 
            name: git
            state: present
    ```

2. Utilizando una estructura wait_for, esperar a que un fichero (elegid el nombre y la ubicación o por ejemplo bloqueo.lock) de la máquina 
remota deje de existir en esa máquina remota. Cuando salga del wait_for, que haga una tarea que muestre un mensaje. Para eliminar ese fichero 
de la máquina remota, podéis hacerlo de forma manual o como queráis.

3. ¿Y si ahora lo quisiéramos como condición previa para ejecutar unas tareas cualquiera? Por ejemplo tomar algunas tareas de algún ejercicio
que sólo se ejecuten si previamente se ha eliminado ese fichero bloqueo.lock 

4. Crear un fichero: ``missecretos.yml`` que albergue unas variables llamadas miuser y micontra, cifrarlo con ansible-vault, y después llamarlo desde un playbook que tenga una tarea que muestre el valor de miuser y micontra

5. Realizar la instalación del rol de mysql, lo podeis crear o buscar en ansible galaxy. Es obligatorio aportar la definición del rol.

6. Escoger algún otro rol de ansible galaxy y lanzarlo en vuestras máquinas. Elegid alguno que os lo ponga facil en los requerimientos. 
