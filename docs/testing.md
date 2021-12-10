# QA Y TESTING 

## HTTPIE 

+ HTTPie es un cliente HTTP de línea de comandos que tiene como objetivo hacer que la interacción CLI con los servicios web sea lo más amigable posible para los humanos. HTTPie está diseñado para probar, depurar y, en general, interactuar con API y servidores HTTP.  

+ [INSTALACION](https://httpie.io/docs#installation)  
`dnf install httpie`  
`dnf upgrade httpie`  

+ GET:  
`http get <url.api/>`  
`http get <url.api/users> Content-Type:aplication/json Api-Token:xxx`  

+ POST:  
`http post <url.api/users> username=xxx password=xxx`  
`http post <url.api/> Content-Type:aplication/json Api-Token:xxx`

+ PUT:  
`echo '{"username":"miguel", "email" : xxx...}' | http put <http:api.url/users> Content-Type:aplication/json Api-Token:xxx`  
`http put <http:api.url/users> Content-Type:aplication/json Api-Token:xxx @/path/file/modifications.json`  


## POSTMAN  

+ [Postman](https://www.postman.com/) es una aplicación que nos permite realizar pruebas API. Es un cliente HTTP que nos da la posibilidad de testear 'HTTP requests' a través de una interfaz gráfica de usuario, por medio de la cual obtendremos diferentes tipos de respuesta que posteriormente deberán ser validados.  

+ [guia](https://learning.postman.com/docs/getting-started/introduction/)  

+ Los requests son peticiones a un servidor.  

+ Se pueden crear colecciones de peticiones sobre una API. Dentro de esta coleccion se puede crear diferentes ambientes como de test-local o producción. Dentro de cada ambiente se puede crear diferentes variables como la url de local y la url de producción para cambiar rapido una petición sin cambiar cosas.  

+ Para poner cosas en las peticiones, se puede poner en Body-raw-json. Como username, password, y en Headers content-type, api-token.  

+ Las peticiones mas usadas son GET, POST, PUT, PATCH, DELETE. La diferencia entre put y patch es que con put necesitas poner todos los campos para modificar algo mientras que patch solo el campo modificado.  

+ En la url de petición tambien se pueden pasar parametros debajo de la caja de peticiones como:  
`GET http://url/users?limit=10&lang=es-es&role=admin`  

+ El collection Runner hace que de un conjunto de colección, se hagan las peticiones todas de golpe por orden. No obstante, tambien podrias crear un script para decir que vaya a tal petición si hace una, que salte otras, etc.  

+ Podemos crear diferentes workspaces para cada api a probar.  

+ Podemos importar/exportar colecciones a postman. Así como compartir colección donde nos dará una url para un compañero de equipo.  

+ Podemos aprender Postman y sacar certificados con EXPLORE - [Postman student programm](https://www.postman.com/company/student-program/).  

+ En la pestaña TESTS podemos probar pruebas, tenemos snipets con ya pruebas utilizadas para poder hacer tests de nuestra api. Al hacer SEND nos saldrá abajo el Test Result de la prueba.  
```
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```  

+ También está la pestaña pre-request script para poner codigo que hacer antes de la petición.  

+ En la pestaña general de postman de EXPLORE tenemos colecciones y apis ya creadas para probar funciones, tests,etc. Para usarlo, tenemos que hacer un FORK de las colecciones ya hechas.  

+ Podemos printar las respuestas de manera más bonitas con código, para ello lo pondremos en la pestaña test y lo vemos en el resultado en VISUALIZE.  
```
const template =
<table bgcolor="ffff">
    <tr>
        <th>{{name}}</th>
        <th>{{species}}</th>
    </tr>
    {{#each response.results}}
    <tr>
        <td>{{name}}</td>
        <td>{{species}}</td>
    </tr>
    {{/each}}
</table>;

pm.visualizer.set(template, {
    response: pm.response.json()
})
```
> la info viene de un contenido RESULTS y sus campos name, species.  

+ Tambien se puede generar peticiones dinamicas como por ejemplo hacer que cada vez te vaya diciendo un id de tu pagina:  
```
const postId = Math.floor(Math.random() * 100) * 1);
pm.globals.set("postId", postId);
console.log("PostId", postId);
```

### Curso Experto  

+ [tutorial](https://postmanstudent.notion.site/postmanstudent/Guide-for-Postman-Student-Expert-Program-7a03387662c14c23b687f1952c82d6d3)  


























