# docker-holamundo

Aprende Docker ahora! curso completo gratis desde cero!

Commands:

Descargar imágenes

- docker images // Devuelve todas las imágenes descargadas en nuestra máquina
- docker pull <...> // Descarga la imagen mas reciente "latest", MEJOR USAR UNA VERSIÓN EN ESPECÍFICO
- docker pull <node:18> // Acá se quiere descargar la versión 18 de nodeJS
- docker pull -- plarform <linux/x86_64> <mysql> //en algunos casos hay que decirle que chip tenemos
- docker image rm <imagen> //eliminar las imágenes específicas

Crear un contenedor a partir de una imagen

- docker create <imagen> //crear el contender con la imagen...
- docker container create <imagen> //segunda opción, pero es lo mismo 
- docker create --name <nombre_contenedor> <imagen>// si queremos crear un contenedor y con un alias

Qué contenedores están corriendo

- docker ps //listado con 
    CONTAINER_ID //id contenedor corto
    IMAGE // en base a q imagen se ha creado nuestro contenedor
    COMMAND // comando que usa el contenedor para ejecutarse
    CREATED // hace cuanto se creó el contenedor
    PORTS //Puertos que usa este contenedor
    NAMES // nombre del contenedor
- docker ps -a //muestra todos los contenedores así esté detenido

Parar el contenedor

- docker stop <contenedor> //parar el contenedor

PORT MAPPING: Redireccionar a un puerto diferente o exponer a las peticiones EXTERNAS

- docker create -p<numero_puerto_ANFITRION>:<puerto_CONTENEDOR_A_MAPEAR> <imagen>// 0.0.0.0:27017->27017/tcp
- docker create -p<numero_puerto_ANFITRION> <imagen> //Se deja que docker escoja el puerto q quiera aleatoriamente        0.0.0.0:56530->27017/tcp

LOGS DEL CONTENEDOR

- docker logs <contenedor> //logs
- docker logs --follow c<contenedor> //para escuchar todo el tiempo los logs

COMBO DOCKER

- docker run mongo //inicia un contenedor pero si no encuentra la imagen, la descarga, etc...
- docker run -d mongo //detach -d
- docker run --name monguito -p27017:27017 -d mongo //todo en una sola línea

EJERCICIO PRÁCTICO
Descargar para node: <https://github.com/nschurmann/mongoapp-curso-docker>
-Dependencias (express, mongoose)

// la siguiente línea dice:
mongoose.connect('mongodb://nico:password@monguito:27017/miapp?authSource=admin')

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