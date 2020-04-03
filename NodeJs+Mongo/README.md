En este ReadMe se va a detallar el paso a paso de la instalaciòn de NodeJs en Ubunto y su correspondiente conexiòn con una base MongoDB, tanto para el método GET (traer datos) como POST (alojar datos en la base)

NodeJs en Ubuntu.
instalamos node en nuestra maquina y creamos nuestro directorio de trabao

*En nuestro directorio de trabajo arrojamos.
- curl -sl https://deb.nodesource.com/setup_13.x | sudo -E bash
- sudo apt-get install -y nodejs

Con esto tendrìamos nuestro NodeJs instalado en el equipo de trabajo

*Para probar que funcione la instalaciòn y ademas se instalen las dependencias, arrojamos:
- curl -sL https://deb.nodesource.com/test | bash -

*Para poder crear nuestra aplicacion necesitamos instalar Express que nos va a servir como server de nuestra aplicacion
- npm install -g express-generator

*Luego, debemos instalar un gestionador de plantillas para el nodejs y express. Este sera el EJS y lo llamaremos "testeo". Esta ejecuciòn nos crearà el directorio de la aplicaciòn. Allì se alojarà nuestro archivo package.json, app.js y aquellos directorios que necesita la aplicaciòn para levantarse.
-express --view="ejs" testeo

*Como vamos a hacer una integraciòn entre una app Nodejs y MongoDB es necesario crear las dependencias que nuestro Nodejs va a necesitar. La siguiente ejecuciòn en necesaria ejecutarla dentro del directorio de trabajo que se creo en el paso anterioir. Para esto: (los numeros siguientes a monk y mongodb son las versiones a las que estamos generando las dependencias)
-npm install --save monk@^7.1.2 mongodb@^3.5.4

Una vez finalizada la ejecuciòn vamos a observar que el archivo package.json se encuentra enditado con el agregado de las dependencias que pedimos.

*Una vez configuradas las dependencias, procedemos a instalarlas. El npm leerà el archivo .json e instalarà todas las dependencias que no lo estèn. Es importante que la siguiente ejecuciòn sea arrojada en el directorio de trabajo:
-npm install

*Para crear una web que diga "hello world" debemos hacer un cambio en el archivo ./router/index.js ya que este indica cuales son los get que harà nuestra aplicaciòn.
-/* GET Hello World page. */
router.get('/helloworld', function(req, res) {
  res.render('helloworld', { title: 'Hello, World!' });
});
Y en segundo lugar, debemos hacer una creaciòn de la nueva pagina .ejs, en este caso helloworld.ejs. La misma se ubicarà en ./views
-<!DOCTYPE html>
<html>
  <head>
    <title><%= title %></title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <h1><%= title %></h1>
    <p>Welcome to <%= title %></p>
  </body>
</html>

*Para poder hacer que nuestra aplicaciòn Nodejs se conecte con la base de datos mongodb, es necesario instalar el servicio de mongo.
-sudo apt-get install -y mongodb-org
Por consiguiente habilitarlo:
-sudo systemctl enable mongodb
Por consiguiente iniciarlo:
-sudo systemctl start mongodb

*Para configurar una consulta desde el Node.js a MongoDB debemos sumar en el archivo /routes/index.js la funciòn que llama y crear (de ser necesario) la web donde se va a reflejar la respuesta del GET
Ediciòn del ./routes/index.js
-/* GET Userlist page. */
router.get('/userlist', function(req, res) {
    var db = req.db;
    var collection = db.get('usercollection');
    collection.find({},{},function(e,docs){
        res.render('userlist', {
            "userlist" : docs
        });
    });
});
Posteriormente, en el archivo ./views/userlist.ejs creamos el archivo con la web de la respuesta:
- <!DOCTYPE html>
<html>
  <head>
    <title>Lista de usuarios</title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <h1>Lista de usuarios</h1>
    <ul>
      <%
        var list = '';
        for (i = 0; i < userlist.length; i++) {
          list += '<li><a href="mailto:' + userlist[i].email + '">' + userlist[i].username + '</a></li>';
        }
      %>
      <%- list %>
    </ul>
  </body>
</html>

*Para generar una inserciòn en la base de datos nuevamente tenemos que editar el archivo ./routes/index.js indicando un GET (para traer la pagina de inserciòn) y un POST (para ingresar el dato a la db)
Ediciòn del ./routes/index.js
-/* GET New User page. */
router.get('/newuser', function(req, res) {
    res.render('newuser', { title: 'Agregar nuevo usuario' });
});

/* POST to Add User Service */
router.post('/adduser', function(req, res) {

    // Set our internal DB variable
    var db = req.db;

    // Get our form values. These rely on the "name" attributes
    var userName = req.body.username;
    var userEmail = req.body.useremail;

    // Set our collection
    var collection = db.get('usercollection');

    // Submit to the DB
    collection.insert({
        "username" : userName,
        "email" : userEmail
    }, function (err, doc) {
        if (err) {
            // If it failed, return error
            res.send("There was a problem adding the information to the database.");
        }
        else {
            // And forward to success page
            res.redirect("userlist");
        }
    });

});

Posteriormente, en el archivo ./views/newuser.ejs creamos el archivo con la web de la respuesta:
-<!DOCTYPE html>
<html>
  <head>
    <title>Nuevo usuario</title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <h1><%= title %></h1>
    <form id="formAddUser" name="adduser" method="post" action="/adduser">
      <input id="inputUserName" type="text" placeholder="Nombre" name="username" />
      <input id="inputUserEmail" type="text" placeholder="E-mail" name="useremail" />
      <button id="btnSubmit" type="submit">Enviar</button>
    </form>
  </body>

Reiniciamos el servicio de npm, y obtendremos el localhost y sus paginas secundarias funcionando.

Aclaraciones:
Esta aplicaciòn consta de tener un front y 3 pàginas secundarias:
http://localhost:3000
http://localhost:3000/newuser
http://localhost:3000/userlist
http://localhost:3000/helloworld

El aplicaciòn se debe iniciar:
npm start (parados en el directorio de trabajo de la app)
