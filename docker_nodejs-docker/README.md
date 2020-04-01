La finalidad de este README es que sirva como guia de desarrollo

Consideraciones a tener en cuenta:
Sistema operativo: distribucion Ubuntu
Se debe tener instalado:
	*Docker
	*Docker-compose

Pasos a seguir:
Creamos un directorio de trabajo:
	*mkdir ~/Docker/MyNodeApp/Prueba

Creamos el archivo Dockeerfile para montar la imagen de Nodejs, ya que este no cuenta con imagen oficial configurada.
	*vi/vim/subl Dockerfile
	Y pegamos el contenido del archivo Dockerfile adjuntado
	
Creamos el archivo docker-compose para poder montar los contenedores tanto de Mongo como de Nodejs, y estos puedan trabajar en relacion de manera dinamica.
	* vi/vim/subl docker-compose.yml
	Y pegamos el contenido del archivo docker-compose.yml adjuntado.

Debemos contar en nuestro directorio de trabajo con el package.json, este archivo es necesario para que levante el nodejs.
	*npm init
	Nos va a pedir que configuremos el paquete de nuestro proyecto
	En este caso se genero un .json para el nombre de paquete de trabajo "prueba"
	El contenido es el del archivo package.json

-----> Al momento cuando corremos el comando "docker-compose up -d" los contenedores levantan pero, el contenedor de nodejs se reinicia constantemente. El error es el detallado debajo.
-----> Al momento el contenedor de nodejs da error:
	"npm ERR! missing script: start"