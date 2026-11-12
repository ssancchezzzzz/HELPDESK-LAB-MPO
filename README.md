# HELPDESK - GESTOR DE INCIDENCIAS - DOCKER COMPOSE

Lo que he realizado hoyes una app de gestión de incidencias usando Docker Compose.La
app tiene 3 contenedores que son los siguientes:

- DDBB PostgreSQL
- Backend
- Frontend

La idea es conseguir que los 3 contenedores funcionen a la vez, se comuniquen y no se
pierdan datos cuando se pare o se eliminen contenedores.

## Requisitos:

- Docker Desktop instalado y antes de usar la terminal abrirlo, que muchas veces me da 
fallo y es por esta razón, ya me ha pasado en muchas ocasiones cuando retomo un proyecto.
- Docker Compose.
- Imágenesde front y back v1.0

## Para arrancar:

Archivos en carpeta, compose.yaml con todo configurado y .env

Después en la terminal ejecutar dentro de la carpeta del proyecto:

- docker compose config
- docker compose up -d
- docker compose ps

Cuando todo funcione, entrar en: http://localhost:8080

Para mirar los logs:

- docker compose logs
- docker compose logs backend
- docker compose logs db

Para hacer stop en contenedores:

- docker compose stop

Para eliminarlos:

- docker compose down

## Preguntas de la práctica:

### 1. ¿Por qué el frontend no necesita conectarse a PostgreSQL?

Porque el front solo se encarga de mostrar la pagina y de que el usuario puda usarla. Cuando
necesita consultar o guardar algo se lo pide al back, que es el que se conecta a la base de 
datos. Así no hace falta que el front tenga acceso directo a PostgreSQL.

### 2. ¿Por qué el backend pertenece a dos redes?

Porque necesita comunicarse con los otros contenedores. Por una red se comunica con el front y 
por la otra con la ddbb. Si solo estuviera conectado a una de las redes no podría comunicarse 
con todos los servicios que necesita.

### 3. ¿Qué servicio necesita publicar un puerto en el anfitrión?

El front, porque es al que tenemos que acceder desde nuestro navegador. En este caso he usado 
el puerto 8080 para poder entrar desde localhost, después de todos los problemas que hemos tenido
llegando a usar hasta el 8085 hasta que nos hemos dado cuenta del compose.yaml y su configuración.

El back y la ddbb no necesitan tener puertos abiertos hacia nuestro ordenador porque se comunican 
por las redes internas de Docker.

### 4. ¿Por qué publicar PostgreSQL hacia fuera no es la solución adecuada para un fallo de comunicación interno?

Porque el problema estará en la configuración de las redes o en la conexión entre los contenedores. 
Abrir el puerto de PostgreSQL hacia fuera no arreglaría eso y además sería menos seguro porque estaría
 exponiendo la ddbb sin necesidad.

### 5. ¿Qué ventaja ofrece Docker Compose respecto a ejecutar los tres contenedores manualmente?

La principal ventaja es que podemos tener toda la configuración en un solo archivo y arrancar los tres contenedores 
con un comando.

Si lo hicieramos manualmente tendríamos que crear las redes, los volumenes y arrancar cada contenedor por separado, 
y sería bastante más pesado.

### 6. ¿Por qué hemos dividido la arquitectura en dos redes?

Para separar los servicios y que cada uno solo pueda conectarse con los que necesita. En la red front están el 
front y el back, mientras que en la red datos están el back y PostgreSQL.

De esta manera el frontend no puede conectarse directamente a la base de datos y tenemos una estructura más organizada 
y segura.

### 7. ¿Por qué el backend utiliza db como dirección de PostgreSQL?

Porque db es el nombre que le hemos puesto al servicio de PostgreSQL dentro del archivo Compose.

### 8. ¿Qué diferencia existe entre detener un proyecto y eliminar sus volúmenes?

Cuando usamos docker compose stop simplemente paramos los contenedores, pero siguen estando creados y podemos volver a arrancarlos.

Con docker compose down se eliminan los contenedores y las redes del proyecto, pero los datos del volumen se mantienen.

En cambio, si utilizamos docker compose down -v, también se borra el volumen y por tanto perdemos los datos que hubiera guardados,
como hemos visto en los diferentes laboratorios prácticos no entregables.

### 9. ¿Qué consecuencias tendría para la empresa perder el volumen de PostgreSQL?

Sería un problema bastante importante porque en ese volumen están guardados los datos de la aplicación, como las incidencias que han creado
los usuarios.

Si se pierde el volumen, se perdería toda esa información y habría que recuperarla desde una copia de seguridad, si es que tenemos alguna,
en producción real, bajo mi experiencia, siempre realizo copias de seguridad por si acaso pasase algo.