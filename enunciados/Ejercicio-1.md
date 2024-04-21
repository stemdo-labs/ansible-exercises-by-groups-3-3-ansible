# Introducción

1. Crear la carpeta de trabajo y el primer inventario solo con el nodo 1.

2. Crear un inventario (ficticio) donde tengamos 5 nodos (nodo1 a nodo5) agrupados en los siguientes grupos:
   - `webservers` (nodo1, nodo4, nodo5)
   - `dataservers` (nodo3)
   - `proxy` (nodo2)

3. Crear un fichero de configuración para evitar tener que incluir el inventario, para que nos haga todo con permisos de superusuario, etc.

4. Lanzar con comando: ping a todos los nodos webservers del inventario ficticio para comprobar cuales están activos y cuales no.

5. Instalar en linea de comandos con apt, apache2.

6. Crear un playbook que a través de shell lance un comando que guarde la fecha actual en un fichero llamado `/tmp/fechahora.data`.

7. Crear un playbook que haga las tareas anteriores 4 y 5 para el nodo1.

8. Modificar el anterior playbook para añadir:
   - una tarea más que compruebe que apache2 está funcionando.
   - una tarea que instale la base de datos mariadb en su última versión.
