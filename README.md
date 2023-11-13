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

### 5. ¿Que comando de consola puedo usar para saber las ips de los contenedores anteriores? Filtra todo lo que puedas la salida

Para saber las direcciones ip de los contenedores mencionados anteriormente podemos usar el comando ```docker network inspect``` con el parámetro ```--format``` para filtrar la busqueda.

**Ejemplo:**

>docker network inspect --format='{{range .Containers}}{{.Name}}: {{.IPv4Address}}{{end}}' bind9_red

Al lanzar el anterior comando deberia mostrarnos por pantalla la siguiente información:

```bash
bind9_1: 192.168.1.10/24
bind9_2: 192.168.1.11/24
```

### 6. ¿Cual es la funcionalidad del apartado "ports" en docker compose?

La funcionalidad que tiene el apartado ports en docker compose es realizar un mapeo de puertos entre un contenedor que tengamos creado y la máquina host.

En sintesis, vinculamos el puerto 53 del contenedor con el puerto 53 de la máquina host.

### 7. ¿Para que sirve el registro CNAME? Pon un ejemplo

Un registro **CNAME** se utiliza para crear un alias para un nombre de dominio. Su función principal es redirigir un nombre de dominio a otro, permitiendo que varios nombres apunten a una única ubicación.

**Ejemplo:**

>mail.asir.com.    IN   CNAME   correo-asir.com.

### 8. ¿Como puedo hacer para que la configuración de un contenedor DNS no se borre si creo otro contenedor?


### 9. Añade una zona tiendadeelectronica.int en tu docker DNS que tenga

### - www a la IP 172.16.0.1

### - owncloud sea un CNAME de www

### - un registro de texto con el contenido "1234ASDF"

### - Comprueba que todo funciona con el comando "dig"

### - Muestra en los logs que el servicio arranca correctamente

