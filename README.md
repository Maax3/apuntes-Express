# Detalles del Framework Express

## Lista de módulos útiles

| Módulo | Descripción                                                                              |
|--------|------------------------------------------------------------------------------------------|
| dotenv | Permite usar el archivo .env y definir allí las variables de entorno como: PORT, BDD_PASS, BBD_USER |
| mysql2 | Versión mejorada de la libreria mysql que permite usar promesas. |
| nodemon| Permite el refresco automático del servidor. Se recomienda instalar con ``npm install nodemon -D`` (como dependencia dev). Además, si estas usando el contenedor docker debes añadir --legacy-watch en lugar de --watch en el script |
| express | La librería oficial de express |
| express-generator | Crea un equeleto básico de una API en express. Se puede crear con vista y sin vista. |

## Express-generator

Paso previo: Instalar el módulo express-generator
* npm install express-generator -g

Para crear una API básica sin vista:

```js
express --no-view --git nombreAPP
```

Para crear una API básica con vista:

```js
express --view=pug --git nombreAPP
```

## Simplificacion de creación del servidor.
Evita el uso de la carpeta bin y el archivo ``WWW``. Puedes crear un servidor directamente con Express, ya que este usa el modulo __HTTP__ por debajo. Sintaxis:

```js
//En en el main o app.js:
const PORT = process.env.PORT || 3000;
app.listen(PORT, () =>{
  console.log('Servidor corriendo')
})
```

## Simplificación del uso 'Router' en /routes.

En lugar de crear variables adicionales, puedes anidar el método __Router()__ con ``EXPRESS``.

```js
const router = require('express').Router();
```

## Cabeceras adicionales
Express y *otros framework* añaden su 'marca de agua' en forma de cabecera a cualquier petición. Esto puede llegar a ser un problema grave de seguridad puesto que le estás diciendo al usuario que tecnología estas usando. Para desactivar esto, puedes usar:

```js
app.disable('x-powered-by');
```

# Respuestas de EXPRESS (RESPONSE)

Express es un framework que te ahorra trabajo. No es necesario definir las cabeceras HTTP, ya que por debajo éste se encarga de responder de forma adecuada. Adicionalmente, muchas de estas respuestas se pueden encadenar una tras otra. 

Por ejemplo:

```js
app.use(req, res) => {
  res.status(201).json(datosJSON)
}
```

Lo más importante de express es entender que las rutas se ejecutan de forma secuencial. Si tenemos 2 rutas, una que nos lleva a /contacto y otra que nos da un error es esencial ponerlas en orden. 

Si ponemos:

```js
app.use(req, res) => {
  res.status(404).send(<h1>Error, URL no encontrada</h1>)
}

app.get(req, res) => {
  res.send(<h1>Ruta encontrada con éxito</h1>)
}
```

__La ruta app.get nunca se ejecutará.__ 

## Ten cuidado con las rutas variables.

Puede darse el caso de las rutas con parámetros opcionales sobreescriban las rutas estáticas. Recuerda ponerlas en orden.

### En este caso las rutas funcionarán correctamente

```sh
router.get(/productos/sin-stock)
```

```sh
router.get(/productos/:id)
```

### En este caso, nunca se llegará a la ruta de productos-stock

```js
router.get(productos/:id)
```

```js
router.get(productos/sin-stock)
```

### Respuestas comunes de express - [Doc oficial](https://expressjs.com/es/4x/api.html#res)

| RESPUESTA | Descripción                                                                              |
|--------|------------------------------------------------------------------------------------------|
| res.json() |  Todo lo que vaya dentro se convierte automáticamente en JSON y se aplica la cabecera 'application/json'  |
| res.redirect('ruta')| Redirige a la ruta seleccionada. Puedes usar res.redirect('back') para que el usuario vuelva a la página anterior.|


# PETICIONES a EXPRESS (REQUEST).

Es la información que le llega al servidor. Express permite, mediante una serie de métodos, obtener la información, cabeceras, método empleado...etc.

La ``REQUEST`` es única para cada petición. Por lo que, también es posible mutar la información antes de que llegue a una determinada ruta.

| PETICIÓN | Descripción                                                                              |
|--------|------------------------------------------------------------------------------------------|
| req.body| Contiene toda la información.|
| req.method| Contiene el método utilizado: POST, GET, PUT, DELETE, PATCH...|
| req.headers| Contiene todas las cabeceras |
| req.params.atributo | Contiene el atributo pasado por la URL |

# Pasos de configuración/inicialización del proyecto

1. Lanzar docker
2. Instalar express con npm install express-generator -g
3. Crear el proyecto con npm install --no-view --git miApi
4. Instalar dependencias: mysql2, dotenv, nodemon -D
5. Eliminar basura del proyecto y refactorizar variables como ``var``.
6. Ajustar rutas para las variables de entorno ``.ENV`` ``require('dotenv').config({path: '../.env'});``
7. Configurar el servidor con ``app.listen()``.
8. Configurar el archivo de base de datos para establecer la conexión.

## Ejemplo de configuración de base de datos

```js
const db = mysql2.createPool({
  host: process.env.MYSQL_HOST,
  user: process.env.MYSQL_USER,
  password: process.env.MYSQL_PASSWORD,
  database: process.env.MYSQL_DB
}).promise();
```

### Ejemplo de función para probar la conexión BDD

```js
/*-------------------------
    IFFE AUTOINVOCADORA PARA TESTEAR: 
    cd database
    node database.js
---------------------------*/

 (
 async function test(){
  const [resultado] = await db.query(`SELECT * FROM producto`);
  await db.end();
  console.log(resultado);
  }
)(); 
```

# Prueba tu API 

Puedes probar el funcionamiento de tu api con la extensión ``REST CLIENT`` de VScode. Permite generar un archivo .http o .rest que permite escribir peticiones ``GET``, ``POST``, ``PUT`` etcétera y tenerlas guardadas como si fuera un archivo.txt. Ejemplo:

![](img/apirest.png)

# Recursos útiles para aprender EXPRESS
* [NODE BÁSICO](https://www.youtube.com/watch?v=yB4n_K7dZV8)
* [API BÁSICA](https://www.youtube.com/watch?v=YmZE1HXjpd4)
* [CRUD BÁSICO](https://www.youtube.com/watch?v=lSdg8rQd4Ms)
* [API CON ASYNC-AWAIT / Ingles](https://www.youtube.com/watch?v=Hej48pi_lOc)
* [CRUD CON ASYNC-AWAIT / Ingles](https://www.youtube.com/watch?v=YkBOkV0s5eQ)
* [SUPER_MEGA_EXTRA_TUTORIAL_EXPRESS / 4 horas](https://www.youtube.com/watch?v=JmJ1WUoUIK4)
* [EJEMPLO API DEL MISMO AUTOR](https://www.youtube.com/watch?v=Aj8E-Vhs1VM)

``BONUS``: [STATUS_CODE](https://http.cat/)
