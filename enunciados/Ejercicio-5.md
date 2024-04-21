# Ejercicios bucles

1. Queremos mostrar los ficheros con `ls /`, `ls -a /`, `ls -la /` de aquellos nodos que sean Ubuntu.

2. Escribir una tarea que copie la lista de ficheros JSON que se acompaña desde `/home/usuario/json` a `/tmp/jsons`.

3. Crear una tarea que instale con loop: `curl`, `apt-transport-https`, `ca-certificates`, `software-properties-common`, `glances`, `git` y `vim`.

4. ¿Y si ahora queremos distinguir entre las distintas distribuciones de Linux? (No usar el módulo package)
    - Primero distínguelas a través de tags.
	- En segundo lugar distínguelas a partir de capturar qué distribución es la del host.

4. Ahora sí que sí vamos a instalar Nginx. Para ello tenemos que realizar las siguientes tareas:
	- Instalar el propio Nginx con su apt update y todo.
	- Permitir el paso a través de cortafuegos. Para ello, debemos escoger entre Nginx Full, Nginx HTTP, Nginx HTTPS.
	- Verificar el status del cortafuegos. Capturar la salida para nos muestre que todo ha ido bien.
	- Verificar que el servicio está funcionando.

5. Crear una tarea que, dada una lista de usuarios y una lista de grupos como los anteriores, los combine todos contra todos creando el usuario y el grupo. 


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

6. Ahora queremos ser más específicos y crear los usuarios con estas especificaciones:

| Usuarios | Grupos a los que pertenece     | Expira la cuenta | uid  | Crear Clave SSH |
|----------|--------------------------------|-------------------|------|-----------------|
| Pedro    | admin, usuarios, produccion    | no                | 1040 | si, rsa 2048    |
| Juan     | usuarios, desarrollo           | no                | 1041 | no              |
| Maria    | usuarios, desarrollo           | no                | 1042 | no              |
| Ana      | testing, produccion           | no                | 1043 | si, rsa 2048    |
| Antonio  | usuarios, testing              | no                | 1044 | no              |
| Marta    | usuarios, produccion           | no                | 1045 | no              |
| Jose     | admin, usuarios, desarrollo    | no                | 1046 | si, rsa 2048    |
| Invitado1| usuarios                        | si                | -    | no              |
| Invitado2| usuarios                        | si                | -    | no              |
