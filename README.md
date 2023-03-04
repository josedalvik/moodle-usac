<b>(1) Instalación</b>

<br/><br/>

<b>(1.1) Dockerfile</b>

<br/><br/>

Para crear las imagenes de forma individual:

<br/><br/>

cd html-moodle<br/>
docker build . -t html-moodle

<br/><br/>

cd ../db-mysql<br/>
docker build . -t db-mysql

<br/><br/>

Para crear los contenedores de base de datos y html de Moodle:

<br/><br/>

docker network create -d bridge red_interna<br/>
docker container run -dt --name=capacitacion --restart=unless-stopped -p:8000:80 --network=red_interna html-moodle<br/>
docker run --name db -dt -p=3306:3306 --restart unless-stopped --network=red_interna -e MYSQL_ROOT_PASSWORD=Lld39dm332 db-mysql

<br/><br/>

Con esos comandos Moodle se publicará en http://localhost:8000 o cualquer IP asignada al host, por ejemplo http://127.0.0.1:8000.

<br/><br/>

<b>(1.2) Dockerfile compose</b>

<br/><br/>

Es posible utilizar docker-compose para levantar ambos servicios:

<br/><br/>

docker-compose up -d

<br/><br/>

<b>(2) Variables de entorno</b>

<br/><br/>

Los archivos Dockerfile de las carpetas html-moodle y db-mysql tienen variables que definen la base de datos, usuarios, contraseñas y direcciones URL y del sistema de archivos. 

<br/><br/>

<b>(2.1) Dockerfile de base de datos</b>

<br/><br/>

MOODLE_USER: Usuario para iniciar sesión en Moodle.<br/>
MOODLE_USER_FIRSTNAME: Nombre del usuario de Moodle.<br/>
MOODLE_USER_LASTNAME: Apellido del usuario de Moodle.<br/>
MOODLE_USER_EMAIL: Correo electrónico del usuario de Moodle.<br/>
MOODLE_USER_PASSWORD: Contraseña del usuario de Moodle.<br/>
MOODLE_USER_PASSWORD_FRANCISCO: Contraseña MD5 de usuario administrador de Moodle.<br/>
MOODLE_USER_PASSWORD_JOSEPABLO: Contraseña MD5 de usuario administrador de Moodle.<br/>
MYSQL_ROOT_PASSWORD: Contraseña root de Mysql.<br/>
MOODLE_DB_NAME: Nombre de la base de datos a crear.<br/>
MOODLE_DB_USER: Usuario de la base de datos a crear.<br/>
MOODLE_DB_USER_PW: Contraseña de usuario de la base de datos a crear.

<br/><br/>

<b>(2.2) Dockerfile HTML</b>

<br/><br/>

MOODLE_DB_IP: debe ser el nombre con que se creará el contenedor de base de datos. 

<br/><br/>

Si el contenedor se creó con el nombre "db-moodle", entonces MOODLE_DB_IP debe tener asignado ese nombre: 'MOODLE_DB_IP: "db-moodle"'.<br/>
docker container run -dt --name=db-moodle --restart=unless-stopped -p:8000:80 --network=red_interna html-moodle<br/>
Si en docker-compose el servicio se creó con el nombre "db-aula", entonces MOODLE_DB_IP debe tener asignado ese nombre: 'MOODLE_DB_IP: "db-aula"'.<br/>
	...<br/>
&nbsp;&nbsp;image: db<br/>
&nbsp;&nbsp;container_name: db-aula<br/>
	...

<br/><br/>

MOODLE_DB_NAME: Mismo nombre de la base de datos creada en el contenedor de base de datos.<br/>
MOODLE_DB_USER: Mismo usuario de la base de datos creada en el contenedor de base de datos.<br/>
MOODLE_DB_USER_PW: Misma contraseña de usuario de la base de datos creada en el contenedor de base de datos.<br/>
MOODLE_DB_PORT: Puerto en que se publicó la base de datos. Por defecto es 3306.<br/>
MOODLE_PUBLIC_URL: URL en donde se publicará Moodle.<br/>
MOODLE_MOODLE_DATA: Ruta de la carpeta Moodledata.<br/>
MOODLE_HTML: Ruta de la carpeta HTML.

<br/><br/>

<b>(2.3) Docker-compose</b>

<br/><br/>

Docker-compose se utiliza para levantar un conjunto de servicios (contenedores). En este caso es posible modificar las variables únicamente en este archivo.

<br/><br/>

Por ejemplo, para modificar solo el inicio de sesión de Moodle y dejar todo lo demás en su defecto:<br/>
&nbsp;...<br/>
&nbsp;db:<br/>
&nbsp;&nbsp;build: <br/>
&nbsp;&nbsp;&nbsp;context: db-mysql<br/>
&nbsp;&nbsp;&nbsp;args:<br/>
&nbsp;&nbsp;&nbsp;&nbsp;MOODLE_USER: "laplace"<br/>
&nbsp;&nbsp;&nbsp;&nbsp;MOODLE_USER_FIRSTNAME: "Pierre"<br/>
&nbsp;&nbsp;&nbsp;&nbsp;MOODLE_USER_LASTNAME: "Simon"<br/>
&nbsp;&nbsp;&nbsp;&nbsp;MOODLE_USER_EMAIL: "pierre.simon@transformada.laplace"<br/>
&nbsp;&nbsp;&nbsp;&nbsp;MOODLE_USER_PASSWORD: "laplace2600"<br/>
&nbsp;&nbsp;image: db<br/>
&nbsp;&nbsp;container_name: db-capacitacion<br/>
&nbsp;...

<br/><br/>

Al ejecutar el comando "docker-compose up -d" es posible iniciar sesión en la URL definida en el servicio html con el usuario "laplace" y la contraseña "lapalce2600".