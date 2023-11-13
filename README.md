# Examen-Docker-DNS
### 1. Explica métodos para 'abrir' una consola/shell a un contenedor que se está ejecutando

Para abrir una consola shell en un contenedor que se encuentra en ejecución debemos lanzar el siguiente comando:

>docker exec -it (nombre_del_contenedor_o_el_id) bash

Este comando abrirá una sesión interactiva de bash dentro del contenedor que hemos seleccionado y nos permitirá ejecutar cualquier comando.

Por otra parte si queremos abrir una consola con interprete de comandos sh debemos remplazar bash por sh como en el siguiente ejemplo:

>docker exec -it (nombre_del_contenedor_o_el_id) sh

También podemos agilizar el trabajo ejecutando directamente un comando sin abrir una sesión interactiva utilizando el siguiente ejemplo:

>docker exec -it (nombre_del_contenedor_o_el_id) (comando_que_queremos_utilizar)

### 2. En el contenedor anterior con que opciones tiene que haber sido arrancado para poder interactuar con las entradas y salidas del contenedor

Si queremos interactuar con las entradas y salidas del contenedor, debemos iniciarlo con los parámetros que se muestran a continuación:

>docker run -i -t -a stdin -a stdout -a stderr

Esto nos permitirá interactuar de manera completa con el contenedor, proporcionando entrada y viendo tanto la salida estándar como la de error.

### 3. ¿Cómo sería un fichero docker-compose para que dos contenedores se comuniquen entre si en una red solo de ellos?

En primer lugar debemos crear una red personalzada como se muestra acontinuación:

>$ docker network create \
  --driver=bridge \
  --subnet=192.168.1.0/24 \
  --ip-range=192.168.1.10/24 \
  --gateway=192.168.1.1 \
  bind9_red

Tras crear la red que vamos a utilizar haremos que dos contenedores se comuniquen entre si en una misma red creando un fichero ```docker-compose.yml``` como el siguiente:

```yml
services:
  bind9_1:
    container_name: bind9_1
    image: ubuntu/bind9
    platform: linux/arm64
    ports:
      - "53:53"
    networks:
      - bind9_red
    volumes:
      - ./conf:/etc/bind
      - ./zonas:/var/lib/bind
    
  binn9_2:
    container_name: bind9_2
    image: ubuntu/bind9
    platform: linux/arm64
    ports:
      - "53:53"
    networks:
      - bind9_red
    volumes:
      - ./conf:/etc/bind
      - ./zonas:/var/lib/bind

networks:
  bind9_red:
    external: true
```

### 4. ¿Qué hay que añadir al fichero anterior para que un contenedor tenga la IP fija?

Para que un contenedor tenga una dirección IPv4 fija debemos añadir dentro del ```docker-compose.yml```, en el apartado **networks** justo debajo de **bind9_red**, el apartado ```ipv4_address: (dirección_ip_que_queremos_asignar)```.

**Ejemplo:**

```yml
services:
  bind9_1:
    container_name: bind9_1
    image: ubuntu/bind9
    platform: linux/arm64
    ports:
      - "53:53"
    networks:
      bind9_red:
        ipv4_address: 192.168.1.10
    volumes:
      - ./conf:/etc/bind
      - ./zonas:/var/lib/bind
    
  bind9_2:
    container_name: bind9_2
    image: ubuntu/bind9
    platform: linux/arm64
    ports:
      - "53:53"
    networks:
      bind9_red:
        ipv4_address: 192.168.1.11
    volumes:
      - ./conf:/etc/bind
      - ./zonas:/var/lib/bind

networks:
  bind9_red:
    external: true
```

