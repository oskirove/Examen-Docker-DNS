# Examen-Docker-DNS
### 1. Explica métodos para 'abrir' una consola/shell a un contenedor que se está ejecutando

Para abrir una consola shell en un contenedor que se encuentra en ejecución debemos lanzar el siguiente comando:

>docker exec -it (nombre_del_contenedor_o_el_id) bash

Este comando abrirá una sesión interactiva de bash dentro del contenedor que hemos seleccionado y nos permitirá ejecutar cualquier comando.

Por otra parte si queremos abrir una consola con interprete de comandos sh debemos remplazar bash por sh como en el siguiente ejemplo:

>docker exec -it (nombre_del_contenedor_o_el_id) sh

También podemos agilizar el trabajo ejecutando directamente un comando sin abrir una sesión interactiva utilizando el siguiente ejemplo:

>docker exec -it (nombre_del_contenedor_o_el_id) (comando_que_queremos_utilizar)

