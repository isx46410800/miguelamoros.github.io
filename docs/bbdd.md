# BASES DE DATOS

## POSTGRESQL
+ Crear database y conectarnos:  
`create database diccionari;`  
`\c diccionari;`
+ Crear tablas:  
```
create table exemplar_soci (
		id_exemplar_soci serial primary key,
		id_exemplar int not null,
		id_soci int not null,
		foreign key (id_exemplar) references exemplars(id_exemplar),
		foreign key (id_soci) references socis(id_soci)
		);
create table exemplars (
		id_exemplar serial primary key,
		id_volum int not null,
		num_exemplars int not null,		
		foreign key (id_volum) references grup_volums(id_volum)
);
create table obres (
		id_obra serial primary key,
		nom_obra varchar(200) not null,
		data_publicacio date not null,
		descripcio varchar(500) not null,
		id_autor int not null,
		foreign key (id_autor) references autors(id_autor)
		);		
create table socis (
		id_soci serial primary key,
		nom_soci varchar(100) not null,
		cognom_soci varchar(200) not null,
		naixement date not null,
		localitat varchar(100) not null,
		unique(nom_soci, cognom_soci)
);
```
+ Consulta simple:  
`select * from paraules where paraula='casa'`

+ Consultas con comparaciones:  
```
training=# select objetivo, ventas, ciudad, region from oficinas where
region='Este' order by ciudad;
training=# select nombre, contrato from repventas where ventas>300000;
training=# select nombre from repventas where director=104;
training=# select nombre, contrato from repventas where contrato < '1988-1-
1';
training=# select id_fab, id_producto, descripcion from productos where
id_fab like '%i';
training=# select id_fab, descripcion, (existencias*precio) as valor_inventario
from productos;
training=# select num_pedido, importe from pedidos where importe between
20000 and 29999;
training=# select num_pedido, importe from pedidos where importe>=20000
and importe<=29999;
training=# select id_fab, descripcion from productos where (id_fab='imm' and
existencias>=200) or (id_fab='bic' and existencias>=50);
training=# select empresa from clientes where (empresa not like '%Corp.%' or
empresa not like '%Inc.%') and limite_credito>30000;
practica1=# select nomalumne from alumnes where nomalumne like 'Ann_';
```

+ JOIN:  
```
training=# select ciudad, nombre, titulo from oficinas join repventas on dir=num_empl;
training=# select ciudad, nombre, titulo from oficinas, repventas where dir=num_empl;
training=# select num_pedido, descripcion from pedidos, productos where fab=id_fab and producto=id_producto;
training=# select num_pedido, nombre, empresa from pedidos, clientes, repventas where clie=num_clie and rep=num_empl and importe>25000;
training=# select treballador.nombre, treballador.cuota, dir.nombre, dir.cuota from repventas treballador, repventas dir where treballador.director=dir.num_empl and treballador.cuota>dir.cuota;
```

+ LEFT/RIGHT JOIN + GROUP BY: 
``` 
> EJERCICIO ENTENDER JOIN Y JOIN LEFT/RIGHT **CODI_VENDEDOR, NOM_VENDEDOR, CODI_CAP, NOM_CAP, OFICINA, CIUTAT, CODI_CAPOFICINA, NOM_CAPOFICINA**  
> CON JOIN LEFT MANDA LA COLUMNA QUE QUEREMOS Y SALEN TODOS LOS CAMPOS AUNQUE HAYA NULL
se ha de coger la tabla principal, ver las relaciones manualmente como si tuvieramos una al lado de otra. coger el campo central y ver como se relaciona con la otra tabla. si es la misma tabla se ha de ir haciendo copias de tablas con cada tabla con un nombre predefinido.
```
SOLO CON JOIN TE SALDRÍA SIN LOS NULL
```
training=# select id_fab, sum(existencias) from productos where precio>54 group by id_fab having sum(existencias)>300;
training=# select id_fab, id_producto, descripcion, sum(importe) from productos join pedidos on id_fab=fab and id_producto=producto where fecha_pedido>='01-01-89' and fecha_pedido<='12-31-89' group by id_fab, id_producto order by 4;
training=# select id_fab, id_producto, descripcion, existencias, count(distinct clie) from productos join pedidos on id_fab=fab and id_producto=producto group by id_fab, id_producto order by 5 desc, 4 desc, 3 limit 5;
training=# select oficina, ciudad, count(*) from oficinas right join repventas venedors on oficina=venedors.oficina_rep join pedidos on venedors.num_empl=rep group by oficina order by oficina;
```

+ UNION:  
```
training=# select 'producto individual', id_fab, id_producto, descripcion, importe from productos join pedidos on id_fab=fab and id_producto=producto where descripcion ilike 'Bisagra%' or descripcion ilike 'Articulo%' union select 'total', id_fab, id_producto, descripcion, sum(importe) from productos join pedidos on id_fab=fab and id_producto=producto where descripcion ilike 'Bisagra%' or descripcion ilike 'Articulo%' group by id_fab, id_producto, descripcion order by 2,3,1;
```

+ SUBQUERIES:  
```
training=# select id_fab, id_producto, descripcion, (select count(num_pedido) as num_comandes from pedidos where fab=id_fab and producto=id_producto), (select count(distinct clie) as clientes from pedidos where fab=id_fab and producto=id_producto), (select sum(importe) from pedidos where fab=id_fab and producto=id_producto) from productos where id_fab in (select fab from pedidos group by fab) or id_producto in (select producto from pedidos group by producto) or id_fab not in (select fab from pedidos group by fab) or id_producto not in (select producto from pedidos group by producto) order by 1,2;
```

+ INSERT/UPDATE/DELETE:  
```
INSERT
training=# insert into copia_repventas values (1012, 'Enric Jimenez', 99, 18, 'Dir Ventas', '2012-01-02', 101, 0, 0);
training=# insert into copia_clientes (num_clie, empresa, rep_clie) values (3001, 'C2', 1013);
training=# insert into copia_repventas values (1013, 'Pere Mendoza', null, null, null, '2011-08-15', null, null, 0);
DELETE
training=# delete from copia_pedidos where rep=102;
training=# delete from copia_repventas where nombre='Enric Jimenez';
training=# delete from copia_pedidos where fecha_pedido<'1989-11-15';
training=# delete from copia_clientes where rep_clie=105 or rep_clie=109 or rep_clie=101; (rep_clie in (select num_empl from repventas where nombre like '%adams');
training=# delete from copia_repventas where contrato<'1988-07-01' and cuota is null;
UPDATE
training=# update copia_clientes set limite_credito=60000, rep_clie=109 where num_clie=2103;
training=# update copia_clientes set limite_credito=60000, rep_clie=(select num_empl from repventas where nombre='Mary Jones') where empresa='Acme Mfg.';
training=# update copia_repventas set oficina_rep=11, cuota=cuota-cuota*0.10 where oficina_rep=12;
training=# update copia_clientes set rep_clie=102 where rep_clie in (105,106,107);
training=# update copia_repventas set cuota= cuota + cuota*0.05;
training=# update copia_oficinas set objetivo=2*(select sum(ventas) from copia_repventas where oficina_rep=oficina) where objetivo<ventas;
```

+ ALTER TABLE:  
```
alter table oficinas add constraint director_de_la_oficina foreign key (dir) references repventas(num_empl)
on delete set null --si un rep desapareix, la oficina quedara temporalmente vacia
on update cascade -- si un rep canvia de codi, canviara tambe el codi a la oficina
ALTER TABLE copia_clientes ADD tel numeric(9,0) unique not null check (tel >=100000000 and tel<=999999999);
ALTER TABLE copia_repventas ADD CHECK(edad>=18 and edad<=65);
ALTER TABLE copia_repventas DROP titulo;
alter table oficinas 
	add constraint director_de_la_oficina 
	foreign key (dir) references repventas(num_empl);
alter table pedidos 
	add constraint cliente_que_ha_hecho_pedido
	foreign (clie) references clientes(num_clie)
	add constraint rep_que_atendido_pedido
	foreign key (rep) references repventas(num_empl)
	add constraint producto_del_pedido
	foreign key (fab,producto) references productos(id_fab,id_producto);
```


+ Crear funciones:  
```
CREATE FUNCTION suma (INTEGER,INTEGER)
RETURNS INTEGER
AS $$
BEGIN
RETURN $1+$2;
END;
$$ LANGUAGE 'plpgsql';
--------------------------------------------------------------------------------
CREATE OR REPLACE FUNCTION hola (TEXT)
RETURNS TEXT
AS $$
BEGIN
RETURN 'HOLA '|| $1;
END;
$$ LANGUAGE 'plpgsql';
--------------------------------------------------------------------------------
CREATE FUNCTION INS_ALUM()
RETURNS VOID
AS $$
INSERT INTO prova(a,b) VALUES (100,200);
$$ LANGUAGE sql;
--------------------------------------------------------------------------------
CREATE FUNCTION llista_Alum()
RETURNS RECORD   -- RECORD DEVUELVE UNA LISTA CON CAMPOS
AS $$            -- SI QUEREMOS QUE DEVUELVA MÁS "RETURNS SETOF RECORD"
SELECT * FROM prova;
$$ LANGUAGE sql;
--------------------------------------------------------------------------------
CREATE OR REPLACE FUNCTION counter ()
RETURNS SETOF INTEGER
AS $$
BEGIN
   FOR counter IN 1..10 LOOP
   RAISE NOTICE 'Counter: %', counter;
   END LOOP;
END;
$$ LANGUAGE 'plpgsql';
```
+ Crear triggers:  
```
CREATE OR REPLACE FUNCTION afegeix_audit() RETURNS TRIGGER AS
$$
BEGIN
    IF (TG_OP = 'DELETE') THEN
        INSERT INTO auditoria VALUES (DEFAULT, CURRENT_TIMESTAMP, TG_TABLE_NAME, 'D', CURRENT_USER);
        RETURN OLD;
    ELSIF (TG_OP = 'UPDATE') THEN
        INSERT INTO auditoria VALUES (DEFAULT, CURRENT_TIMESTAMP, TG_TABLE_NAME, 'U', CURRENT_USER);
        RETURN NEW;
    ELSIF (TG_OP = 'INSERT') THEN
        INSERT INTO auditoria VALUES (DEFAULT, CURRENT_TIMESTAMP, TG_TABLE_NAME, 'I', CURRENT_USER);
        RETURN NEW;
    END IF;
    --Aquí no hauria d'arribar-hi mai:
    RETURN NULL;    
END
$$ LANGUAGE plpgsql;
CREATE TRIGGER tauditresultats AFTER INSERT OR UPDATE OR DELETE ON resultats FOR EACH ROW EXECUTE PROCEDURE afegeix_audit();
CREATE TRIGGER tauditanalitiques AFTER INSERT OR UPDATE OR DELETE ON analitiques FOR EACH ROW EXECUTE PROCEDURE afegeix_audit();
```
+ Crear Squema y roles:  
```
--crear roles
CREATE ROLE lc_consultar NOLOGIN;
CREATE ROLE lc_inserir NOLOGIN;
CREATE ROLE lc_admin NOLOGIN;
---
--CREACION DE UN SCHEMA
CREATE SCHEMA lcschema AUTHORIZATION postgres;
--TODOS LOS SCHEMAS SE CREAN EN PUBLIC (PUBLIC-POSTGRES OWNER)
REVOKE ALL PRIVILEGES ON SCHEMA lschema FROM public;
-- todos los privilegios para los users:
GRANT ALL PRIVILEGES ON SCHEMA lschema TO postgres, lc_admin;
--privilegios para usar este schema para:
GRANT USAGE ON SCHEMA lschema TO ROLE lc_consultar, lc_inserir, lc_admin;
--s'hauria de crear un SCHEMA de lab_clinic i també fer aixo;
GRANT USAGE ON SCHEMA lschema to lc_consultar, lc_inserir, lc_admin;
GRANT CREATE ON SCHEMA lschema to lc_admin;
--DAR LOS PRIVILEGIOS A CIERTOS USUARIOS
---para poder conectarse a la bbdd
GRANT CONNECT ON DATABASE lab_clinic to lschema to lc_consultar, lc_inserir, lc_admin;
---para ver las tablas, y podra crear tablas y filas a su nombre
GRANT SELECT ON ALL TABLES IN SCHEMA public TO lc_consultar;
---para poder ver y insertar (no borrar)
GRANT SELECT, INSERT ON ALL TABLES IN SCHEMA public to lc_inserir;
---para poder insertar, tambien necesita permisos de inserir secuencias
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public to lc_inserir;
---para tener todos los privilegios en la bbdd
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public to lc_admin;
--creamos usuarios, para poder tener roles se necesita LOGIN con create role
postgres=# create user miguel password 'miguel';
CREATE ROLE
--create role miguel login password 'miguel';
postgres=# create user walid password 'walid';
CREATE ROLE
--asignar los roles a los usuarios
GRANT lc_consultar to walid;
GRANT lc_inserir to miguel;
GRANT lc_admin to miguel with admin option;
--permiso de superusuario
postgres=# alter role miguel with superuser;
ALTER ROLE
--todos los privilegios en esta base de datos
postgres=# grant all privileges on database lab_clinic to walid;
GRANT
--entrar a la bbdd como miguel
[isx46410800@i05 ipc2019]$ su postgres
Password: 
bash-4.4$ psql lab_clinic miguel;
Password for user miguel: 
psql (9.6.10)
```
+ Ver tiempos de ejecucion:  
`explain analyse select * from paraulesraw where paraula = 'comparar/00';`  
+ Crear indices:  
`create index index_paraula on paraulesraw using btree(paraula);`
``

## PHPPGADMIN
+ phpPgAdmin és una aplicació web que ens permet gestionar una BD Postgres SQL amb un entorn gràfic. Existeix un anàleg per a MariaDB (o MySQL) anomenat phpMySql. El primer pas és instal·lar l’aplicació al mateix ordinador que fa de servidor postgres.  
`sudo dnf install phpPgAdmin`  

+ Editant l'arxiu `/var/lib/pgsql/data/pg_hba.conf` i activant els mètodes d'autenticació md5 ).

+ Editar l’arxiu `/etc/phpPgAdmin/config.inc.php` i posar a __false__ la línia:  
`$conf['extra_login_security'] = true;`  
`// use 'localhost' for TCP/IP connection on this computer\\ $conf['servers'][0]['host'] = 'localhost';`

+ Entrar: `http://localhost/phpPgAdmin`  

## MARIADB
+ Instalar:  
`sudo dnf install mariadb mariadeb-server`  
`sudo systemctl start mariadb`
+ Crear database:  
`create database instagram;`
+ Entrar a una bbdd:  
`USE Nom_BD;`
+ Crear Usuario:  
`create user 'miguel'@'localhost' identified by "miguel14031993";`
+ Bloquear tablas:  
`FLUSH TABLES WITH READ LOCK;`  
`UNLOCK TABLES;`
+ Agafem les dades del servidor mestre per replicar des d'aquest punt:  
`show master status;`
+ Copiem la BD amb mydqldump:  
`mysqldump -u root -p --routines --opt nomdb > db-dump.sql`

## MONGODB
+ Entrar:  
`systemctl start mongod`
`mongo`
+ Entrar bbdd:  
`use database`
+ Ver bases:  
`show databases`
+ Importar tablas:  
`mongoimport --db foursquare --collection restaurants --file '/run/media/isx46410800/TOSHIBA EXT/HISX2/M10/UF2/json_dades_exemple/json/foursquare/restaurants.json'`
+ Entrar a una bbdd,tabla y ver algo:  
```
Database: imdb
> use imdb
switched to db imdb
> db
imdb
> db.createCollection(“movies”)
{“ok”: 1}
> db.createCollection(“oscars”)
{“ok”: 1}
> db.createCollection(“people”)
{“ok”: 1}
> show collections
movies oscars people
```
+ Consultas ejemplos:  
```
{
        "_id" : "0000002",
        "name" : "Lauren Bacall",
        "dob" : "1924-9-16",
        "pob" : "New York, New York, USA",
        "hasActed" : true
}
{
        "_id" : "0000004",
        "name" : "John Belushi",
        "dob" : "1949-1-24",
        "pob" : "Chicago, Illinois, USA",
        "hasActed" : true
}
```
`db.people.find({hasActed: true, hasDirected: { $exists: false}}).pretty().count()->1909`

```
{
        "_id" : 6,
        "name" : {
                "first" : "Guido",
                "last" : "van Rossum"
        },
        "birthYear" : 1931,
        "contribs" : [
                "Python"
        ],
        "awards" : [
                {
                        "award" : "Award for the Advancement of Free Software",
                        "year" : 2001,
                        "by" : "Free Software Foundation"
                },
                {
                        "award" : "NLUUG Award",
                        "year" : 2003,
                        "by" : "NLUUG"
                }
        ]
}
> db.bios.find({"awards.year" : 2001}).count()
3
```
`Buscar las personas que haya obtenido un premio del tipo 'National Medal of'`  
```
{
        "_id" : 7,
        "name" : {
                "first" : "Dennis",
                "last" : "Ritchie"
        },
        "birthYear" : 1956,
        "deathYear" : 2011,
        "contribs" : [
                "UNIX",
                "C"
        ],
        "awards" : [
                {
                        "award" : "Turing Award",
                        "year" : 1983,
                        "by" : "ACM"
                },
                {
                        "award" : "National Medal of Technology",
                        "year" : 1998,
                        "by" : "United States"
                },
                {
                        "award" : "Japan Prize",
                        "year" : 2011,
                        "by" : "The Japan Prize Foundation"
                }
        ]
}
> db.bios.find({ "awards.award" : /^National Medal of/}).pretty().count()
4
```

`//6.- Buscar las personas de la colección bios que destaquen en el terreno de Java, Ruby o Python (3)`  
```
{
        "_id" : 9,
        "name" : {
                "first" : "James",
                "last" : "Gosling"
        },
        "birthYear" : 1965,
        "contribs" : [
                "Java"
        ],
        "awards" : [
                {
                        "award" : "The Economist Innovation Award",
                        "year" : 2002,
                        "by" : "The Economist"
                },
                {
                        "award" : "Officer of the Order of Canada",
                        "year" : 2007,
                        "by" : "Canada"
                }
        ]
}
> db.bios.find({ contribs : { $in: [ "Java", "Ruby", "Python" ] } }).pretty().count()
3
```

```
//9.- Buscar las personas de la colección bios con 1 premio conseguido (1)
{
        "_id" : 8,
        "name" : {
                "first" : "Yukihiro",
                "aka" : "Matz",
                "last" : "Matsumoto"
        },
        "birthYear" : 1941,
        "contribs" : [
                "Ruby"
        ],
        "awards" : [
                {
                        "award" : "Award for the Advancement of Free Software",
                        "year" : "2011",
                        "by" : "Free Software Foundation"
                }
        ]
}
#size sirve para que el tamaño del array de tal cosa sea la medida que indiquemos
> db.bios.find({ awards : { $size: 1}}).pretty().count()
1
```

```
//10.- Buscar las personas de la colección bios con 3 o más premios conseguidos (6)
#que no tenga ni 2 ni 1 ni 0 ni no existe el campo
> db.bios.find({ $nor: [ {awards: {$exists: false}}, {awards: {$size: 2}}, {awards: {$size: 1}}, {awards: {$size: 0}}]}).pretty().count()
6
#que tenga o 4 o 3 o 2
> db.bios.find({ $or: [ {awards: {$size: 4}}, {awards: {$size: 3}}, {awards: {$size: 2}}]}).pretty().count()
8
```

```
//1.- Buscar todos los libros con precio superior a 100 USD (7)
#COMPRUEBO SI TODOS LOS PRECIOS SON EN USD
> db.books.find().count()
333
> db.books.find({"price.currency": "USD"}).count()
333
> db.books.find({"price.msrp": {$gt: 100}}).pretty().count()
7
```

```
//3.- Buscar los libros que tengan el tag 'programming', 'agile' y "java" (5)
#all para que salgan los 3 en los tags
> db.books.find({tags: {$all : ["programming", "agile", "java"]}}).pretty().count()
5
```

```
//5.- Buscar los libros escritos por 3 autores (17)
> db.books.find({author: {$size: 3}}).pretty().count()
17
```

+ Insertar varios:  
```
> db.stores.insertMany(
[
{ _id: 1, name: "Java Hut", description: "Coffee and cakes" },
{ _id: 2, name: "Burger Buns", description: "Gourmet hamburgers" },
{ _id: 3, name: "Coffee Shop", description: "Just coffee" },
{ _id: 4, name: "Clothes Clothes Clothes", description: "Discount
clothing" },
{ _id: 5, name: "Java Shopping", description: "Indonesian goods" }
]
)
```
+ Ver indices:  
`> db.stores.getIndexes()`
+ Crear indices:  
`db.stores.createIndex( { name: "text", description: "text" } )`
+ Consulta una:  
`> db.tweets.findOne()`

```
2.1) Buscar quants twits tenim amb Obama President
> db.tweets.find( { $text: { $search: "Obama President" } } ).count()
52
2.2) Buscar le textScore de cada twit amb Obama President
> db.tweets.find({ $text: { $search: "Obama President" } }, { puntuacio: { $meta:
"textScore" } }).sort( { puntuacio: { $meta: "textScore" } } ).pretty()
- frase exacta :"Yes we can"
> db.tweets.find( { $text: { $search: "\"Yes we can\"" } } ).count()
2
```

```
2.6) Busca les ciutats que estan a entre 20 i 50 km de Barcelona
> db.cities.find({ "loc": { "$near": { "$geometry": { type: "Point" , coordinates: [2.15899,
41.38879]}, "$maxDistance": 50000, "$minDistance": 20000 } } }).count()
72
```
+ Update/delete/insert:  
```
Y los cambiamos por Miky
> db.students.updateMany( { name: "Mikel" }, { $set: { name: "Miky" } })
Vemos cuantos usuarios tienen más de 50000 amigos:
> db.tweets.find({ "user.friends_count" : { $gt: 50000}}).pretty().count()
Incrementamos +1 a estos usuarios el contador de amigos:
> db.tweets.updateMany({ "user.friends_count" : { $gt : 50000 }}, { $inc:
{ "user.friends_count" : +1}})
```