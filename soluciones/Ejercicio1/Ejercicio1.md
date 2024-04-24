1. Primer inventario

2. Inventario ficticio con 5 nodos.
> Para este ejercicio de práctica, he creado los distintos nodos como usuarios en la misma máquina, con pleno conocimiento de que es posible especificar URLs propias para cada host o grupo mediante el uso de variables, para el uso en entorno real. El inventario.yaml (realizado en este formato por elección e invetsigación personal) está en la ubicación relativa ./1.2/inventario.yaml

3. 
> He requerido hacer que los usuarios pertinentes estén en el grupo de super usuarios, con 
```console
sudo usermod -aG sudo nodo1
```

El fichero de configuración de este ejercicio está en ./ansible.cfg

El comando con el que lo ejecuto es 

```console
ansible nodo1 -m shell -a 'whoami' -K
```
Esto me pide la contraseña (pass, en este caso)

4. 
> en este caso he dado al become de ansible.cfg el valor false
ansible webservers -i ./1.2/inventory.yaml -m ping

5. 
 ```console
 ansible webservers -i ./1.2/inventory.yaml -m command -a "sudo apt install apache2" -K
 ```

