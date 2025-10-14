# Scoring y configuración del scoring

- Elasticsearch utiliza de forma predeterminada el modelo **BM25** para generar el score.

score => puntuación relativa que asigna Elasticsearch a los documentos presentados en la respuesta de la consulta basándose en la cantidad de parámetros que cumplen

Es decir:

1) Cuanto mayor es la consulta (más parámetros), mayor es la cantidad de concordancia que debe encontrar en los documentos
2) A mayor número de parámetros, mayor es el scoring que presentan los primeros documentos

 Por tanto, la puntuación que Elasticsearch presenta en la respuesta varía en estas 3 queries. Suponiendo que los datos son de libros:

 1) Dime los libros escritos por "Cervantes"
~~~sql
SELECT * FROM libros **WHERE** autor = "Cervantes";
~~~

2) Dime los libros escritos por "Cortázar" entre 1940 y 1948
~~~sql
SELECT * FROM libros WHERE autor = 'Cortázar' AND año_publicacion BETWEEN 1940 AND 1948;
~~~

3) Dime los libros escrito entre 1900 y 1920, publicados por la Editorial Alianza, que tengan como etiqueta "Clásico" y su precio de venta oscile entre 10 y 20
~~~sql
SELECT * FROM libros WHERE año_publicacion BETWEEN 1900 AND 1920 AND editorial = 'Alianza' AND etiqueta = 'Clásico' AND precio_venta BETWEEN 10 AND 20;
~~~
