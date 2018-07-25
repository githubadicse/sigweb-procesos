## PROCESO PARA VER EN GIS , CON DATOS SIGWEB CLIENTES AFECTADOS POR MACRO PROYECTO
###  CON UN ALCANCE DE 30 METROS


- Primero debemos crear una table en la base de datos sigweb, schema sigweb

CREATE TABLE sigweb.buffer_m (  
 id serial primary key,  
geog geography (Polygon, 4326)  
);


- En esta tabla se grabara un poligono generado por un buffer a partir de los puntos de conexiones.

*aqui es donde se indica a cuantos metros de crea el buffer Ejem. 20Mtrs*

INSERT INTO sigweb.buffer_m (geog)  
SELECT ST_Buffer(geometriaconexionagua::geography, 30) FROM sigweb.padron;


- Creamos una tabla buffer_intercet_tuberia


CREATE TABLE sigweb.buffer_intercet_tuberia (  
 id serial primary key,  
geog geometry (Polygon, 4326)  
);

- Insertamos las intececciones en la tabla buffer_intercet_tuberia.

INSERT INTO sigweb.buffer_intercet_tuberia(geog)  
select buffer_m.geog::geometry from sigweb.buffer_m, sigweb.tuberia_proyectada where st_intersects(buffer_m.geog::geometry,  tuberia_proyectada.geom);

- Creamos la tabla usuarios en buffer_intercet_tuberia

CREATE TABLE sigweb.tmp_padron_macro (  
 id serial primary key,  
geom geometry (Point, 4326),  
nombres varchar,  
direccion varchar,  
inscrinro varchar  
);


- Creamos los indices gemoetricos para el proceso.
CREATE INDEX padron_geometriaconexionagua
   ON sigweb.padron USING GIST (geometriaconexionagua);

CREATE INDEX buffer_intercet_tuberia_geom
   ON sigweb.buffer_intercet_tuberia USING GIST (geog);




- Procesamos todos los clientes que esten en buffer_intercet_tuberia.


insert into sigweb.tmp_padron_macro(geom,inscrinro)  
select padron.geometriaconexionagua,padron.inscrinro  
from sigweb.padron, sigweb.buffer_intercet_tuberia  
where st_within(padron.geometriaconexionagua, buffer_intercet_tuberia.geog::geometry )  
group by padron.geometriaconexionagua, padron.inscrinro  ;


xx
