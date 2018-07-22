### PROCESO PARA VER EN GIS , CON DATOS SIGWEB CLIENTES AFECTADOS POR MACRO PROYECTO
##  CON UN ALCANCE DE 30 METROS


- Primero debemos crear una table en la base de datos sigweb, schema sigweb

CREATE TABLE sigweb.buffer_m (
 id serial primary key,
geog geography (Polygon, 4326)
);


En esta tabla se grabara un poligono generado por un buffer a partir de los puntos de conexiones.

INSERT INTO sigweb.buffer_m (geog)
SELECT ST_Buffer(geometriaconexionagua::geography, 20) FROM sigweb.padron;



