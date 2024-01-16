**Aprende Docker ahora! curso completo gratis desde cero! // holaMundo**

**Table of content:**

1. [Comands - Download images](#download-images)
2. [Comands - Create container](#create-container)
3. [Comands - Container list](#container-list)
4. [Comands - Stop container](#stop-container)
5. [Commands - Port Mapping](#port-mapping)
6. [Commands - Container logs](#container-logs)
7. [Commands - Commands combo](#commands-combo)
8. [Commands - Exercise](#exercise)
9. [DOCKER FILE](#docker-file)
10. [Commands - Network](#docker-network)
11. [DOCKER COMPOSE](#docker-compose)
12. [Volumenes](#volumes)
13. [Ambientes](#ambientes)

Commands:

<a id="download-images"></a>

## Download images

```
- docker images // Devuelve todas las imágenes descargadas en nuestra máquina
- docker pull <...> // Descarga la imagen mas reciente "latest", MEJOR USAR UNA VERSIÓN EN ESPECÍFICO
- docker pull <node:18> // Acá se quiere descargar la versión 18 de nodeJS
- docker pull -- plarform <linux/x86_64> <mysql> //en algunos casos hay que decirle que chip tenemos
- docker image rm <imagen> //eliminar las imágenes específicas
```
<a id="create-container"></a>

## Crear un contenedor a partir de una imagen

```
- docker create <imagen> //crear el contender con la imagen...
- docker container create <imagen> //segunda opción, pero es lo mismo
- docker create --name <nombre_contenedor> <imagen>// si queremos crear un contenedor y con un alias
```

<a id="container-list"></a>

## Qué contenedores están corriendo

- docker ps //listado con
    CONTAINER_ID //id contenedor corto
    IMAGE // en base a q imagen se ha creado nuestro contenedor
    COMMAND // comando que usa el contenedor para ejecutarse
    CREATED // hace cuanto se creó el contenedor
    PORTS //Puertos que usa este contenedor
    NAMES // nombre del contenedor
- docker ps -a //muestra todos los contenedores así esté detenido

<a id="stop-container"></a>

## Stop container

- docker stop <contenedor> //parar el contenedor

<a id="port-mapping"></a>

## Port mapping
Redireccionar a un puerto diferente o exponer a las peticiones EXTERNAS

```
- docker create -p<numero_puerto_ANFITRION>:<puerto_CONTENEDOR_A_MAPEAR> <imagen>// 0.0.0.0:27017->27017/tcp
- docker create -p<numero_puerto_ANFITRION> <imagen> //Se deja que docker escoja el puerto q quiera aleatoriamente        0.0.0.0:56530->27017/tcp
```
<a id="container-logs"></a>

## Container logs

```
- docker logs <contenedor> //logs
- docker logs --follow c<contenedor> //para escuchar todo el tiempo los logs
```

<a id="commands-combo"></a>

## Commands combo

```
- docker run mongo //inicia un contenedor pero si no encuentra la imagen, la descarga, etc...
- docker run -d mongo //detach -d
- docker run --name monguito -p27017:27017 -d mongo //todo en una sola línea
```
<a id="exercise"></a>

## EXERCISE
Descargar para node: <https://github.com/nschurmann/mongoapp-curso-docker>
-Dependencias (express, mongoose)

```
// la siguiente línea dice:
mongoose.connect('mongodb://nico:password@monguito:27017/miapp?authSource=admin')
```

- usuario: nico
- clave: password
- monguito: máquina
- puerto: 27017
- base de datos: miapp
- parámetros de configuración: usuario tipo admin

1. Como necesitamos unos parámetros de configuración. vamos al docker hub
<https://hub.docker.com/_/mongo>  ---> Environment Variables (MONGO_INITDB_ROOT_USERNAME y MONGO_INITDB_ROOT_PASSWORD)
1.1. docker create -p27017:27017 --name monguito -e MONGO_INITDB_ROOT_USERNAME=nico -e MONGO_INITDB_ROOT_PASSWORD=password mongo
1.2. docker start monguito
1.3 vamos a la aplicación de mongoapp descargada y "node index.js"

<a id="docker-file"></a>

## DOCKER FILE

Instrucciones q necesita el contenedor para inicializarse:

```
FROM <node>:<18> //indica en que imagen se debe basar así como la etiqueta de la imagen
RUN mkdir -p </home/app> //crear la carpeta donde se va a almacenar la aplicación
COPY <.> <home/app> //ahora la instrucción de copiar donde almacenar nuestra app COPY <origen_máquina_local> <destino>
EXPOSE <3000>//Puerto para conectarnos al contenedor
CMD ["node", "/home/app/index.js"]//Comando a ejecutar para que nuestra aplicación corra CMD comandos en un array y con ruta completa (/home/app/index.js)
//Al crear contenedore sen docker pueden estar isolados (sin comunicación entre los contenedores), para eso hay q agruparlos, creando una red interna
```

- docker build -t <nombre_nuevo_imagen>:<etiqueta> <ruta_donde_esta_mi_dockerfile>//crear imagenes en base a un archivo "Dockerfile" Ejemplo: "docker build -t miapp:1 ."

<a id="docker-network"></a>

## dOCKER NETWORK

```
- docker network ls // listar redes en docker
- docker network create <mired> //crear una red entre los dockers
- docker network rm <mired> //Eliminar la red
```

Ahora tenemos que crear nuestro contenedor y ajustar la red

- docker create -p27017:27017 --name monguito --network mired -e MONGO_INITDB_ROOT_USERNAME=nico -e MONGO_INITDB_ROOT_PASSWORD=password mongo

Ahora vamos a crear el contenedor de la app que acabamos de colocar dentro de una imagen

- docker create -p3000:3000 --name chanchito --network mired miapp:1

<a id="docker-compose"></a>

##DOCKER COMPOSE

Ya instalado con docker desktop
- docker-compose.yml //este archivo se debe indicar ... Este archivo es para configuraciones

```
version: "3.9" //con comillas dobles se pone la versión
services: //Contenedores que queremos usar
    chanchito: //ojo con el TAB, se agregan los nombres que queremos asignar a los contenedores
        build: . //ojo con otro TAB, construir la imagen de esta misma ruta
        ports: 
            - "3000:3000" //CON COMILLAS, puerto de nuestro sistema anfitrion y luego el puerto del contenedor
        links: 
            - monguito //SIN COMILLAS, El nombre del contenedor a mapear que use el container de "chanchito"
    monguito:   /ojo con el TAB, esta es la app
        image: mongo //utilizar la imagen de mongo por defecto
        ports:
            - "27017:27017"
        environment:
            - MONGO_INITDB_ROOT_USERNAME=nico
            - MONGO_INITDB_ROOT_PASSWORD=password
        volumes:
            - mongo-data:/data/db //<ruta_donde_se_va_a_montar_en_el_contenedor> //solo se puede mencionar si ya se creó en el campo de volumes
            # msql -> /var/lib/mysql
            # postgres -> /var/lib/postgresql/data 
volumes:
    mongo-data:
```

<a id="docker-compose"></a>

## Ejecutar desde el docker compose

```
- docker compose up //levanta tooodo
- docker compose down // para limpiar contenedores, redes
```

<a id="volumes"></a>

## Volumenes

Los contenedores también tienen su sistema de archivos, donde se almacena la data sin embargo la idea es guardar la información POR FUERA osea en el sistema operativo anfitrión
Tipos:

- Anónimo: se indica la ruta donde se aloja.
- Anfitrión o HOST: uno decide dónde se almacena.
- Nombrado: se puede referenciar pq es como el anónimo pero tiene nombre.

Ejemplo:
(docker-compose.yml)
...
volumes:
    mongo-data:

<a id="ambientes"></a>

## VARIOS AMBIENTES

- El archivo Dockerfile.dev //usamos la herramienta nodemon (npm i -g nodemon)
NOTA: si se agrega una línea "WORKDIR", ya no es necesario dar la ruta completa en CMD, es decir CMD ["node", "/home/app/index.js"] ---> CMD ["nodemon", "index.js"]
Como vamos a trabjar con un volumen quitamos la línea "COPY . /home/app"
De igual manera hay q agregar otro docker-compose-dev.yml
- En build se agrega context para decirle dónde se trabaja
- Luego se indica que la imagen se construya según el dockerfile.dev debajo del context
y debajo de links se le agregan los volumes a usar 

```
volumes:
      - .:/home/app
```

ahora los comandos para que se monte un archivo de docker compose diferente al básico

```
docker compose -f docker-compose-dev.yml up
```