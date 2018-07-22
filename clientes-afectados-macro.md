## PROCESO PARA VER EN GIS , CON DATOS SIGWEB CLIENTES AFECTADOS POR MACRO PROYECTO
###  CON UN ALCANCE DE 30 METROS


- Primero debemos crear una table en la base de datos sigweb, schema sigweb

CREATE TABLE sigweb.buffer_m (
 id serial primary key,
geog geography (Polygon, 4326)
);


- En esta tabla se grabara un poligono generado por un buffer a partir de los puntos de conexiones.

//aqui es donde se indica a cuantos metros de crea el buffer Ejem. 20Mtrs
INSERT INTO sigweb.buffer_m (geog)
SELECT ST_Buffer(geometriaconexionagua::geography, 20) FROM sigweb.padron;


- Creamos una tabla buffer_intercet_tuberia


CREATE TABLE sigweb.buffer_intercet_tuberia (
 id serial primary key,
geog geometry (Polygon, 4326)
);

- Insertamos las intececciones en la tabla buffer_intercet_tuberia

INSERT INTO sigweb.buffer_intercet_tuberia(geog)
select buffer_m.geog::geometry from sigweb.buffer_m, sigweb.tuberia_proyectada where st_intersects(buffer_m.geog::geometry, tuberia_proyectada.geom);