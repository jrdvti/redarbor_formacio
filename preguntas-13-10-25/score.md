# Scoring y configuración

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


## Modificar el modelo BM25

Elasticsearch permite cambiar el modelo BM25 para presentar el scoring. Eso sí, hay que tener en cuenta:

a) ¿Qué acceso tengo yo a los datos?
b) ¿Tengo control sobre la creación de índices? 
c) ¿Solo accedo a los datos mediante búsqueda?

Más allá de las respuestas concretas a las preguntas **a** y **b**, nos centramos en la respuesta a la pregunta **c**:

- Si la respuesta es **SÍ**:
 - Existen tipos de búsquedas que permiten asignar una puntuación concreta al score
  - Script score
  ~~~json
  {
    "query": {
      "script_score": {
        "query": { "match": { "autor": "Cervantes" } },
        "script": {
          "source": "Math.min(_score / 10, 1)"
        }
      }
    }
  }
  ~~~
  - Function score
  ~~~json
  {
    "query": {
      "function_score": {
        "query": { "match": { "autor": "Cervantes" } },
        "functions": [
          { "script_score": { "script": "Math.min(_score / 5, 1)" } }
        ],
        "boost_mode": "replace"
      }
    }
  }
  ~~~

- Si la respuesta es **NO**:
 - A nivel de configuración podemos añadir la puntuación del score que queramos
 - Esta configuración es **por índice**
 - Una buena guía para poder hacerlo: [Documentación](https://www.elastic.co/docs/reference/elasticsearch/index-settings/similarity)
