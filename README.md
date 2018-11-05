# practica04

# Creación de una segunda máquina virtual para Apache.

# Atener en cuenta:

Esta paractica el igual que la anterior que hemos hecho pero la diferencia mas notoria es que emplearemos tres maquinas:

- Dos de ellas actuaran de front-ent que las dos contendran el servidor web apache HTTP.
- La tercera, back-end,donde instalaremos el servicio de MySQL.

Cada una de las maquina tendra su propia Ip por lo que la estructura se quedara con las dos maquinas de ApacheHTTP en paralelo y la máquina de MySQL conectada a las dos.

Lo que queremos conseguir con esta practica sera crear una arquitectura con alta disponibilidad que sea escalable y redundante de modo que alternara la carga entre los dos servidores web.



a continuacion adjunto el contenido del archivo vagrantfile de las tres maquinas 
