# Indexació massiva

És important plantejar un escenari **òptim** per tenir una indexació de mil·lions de documents a partir de consultes **_bulk**.

1) Tenir un índex el més optimitzat possible

- Eviteu répliques
~~~json
PUT index/_settings
{
  "number_of_replicas": 0
}
~~~

- Tenir en compte el nombre de shards

> Elasticsearch recomana no tenir shards que **superin les 50GB**

> Per tant, es qüestió de fer una aproximació tenint en compte el total de documents
~~~ python
total_docs * avg_doc_size / num_shards < 50
~~~

> S'hauria d'intentar fer un aproximat per poder definir el nombre de shards

~~~json
PUT index/_settings
{
  "number_of_shards": 2
}
~~~

> Quan més gran són el shards, més ingovernables es tornen

> Si sobrecarreguem l'index de shards, l'index es torna massa pesat i també es torna ingovernable

- Si tots els índex tenen relació, la millor opció és generar un **index_template**

> Mateixa configuració, mapping i alias per a tots els índex que compleixin el **patró d'índex indicat**

~~~json
PUT /_index_template/default_template // métode http/API _index_template/nom del template
{
  "index_patterns": ["myindex-*"], // patró d'índex
  "template": { // objecte tempate
    "settings": { // definim els mappings
      "number_of_shards": 3,
      "number_of_replicas": 0
    }
  }
}
~~~

- Si teniu index que ocupin molt, per un moment també es pot baixar el nombre de rèpliques, però sobretot, el que es recomana és que sigui només l'índex que rebrà la dada

2) Preparar el clúster

- Pujar el *indexing buffer*, que per defecte està al 10%

~~~json
PUT /_cluster/settings
{
  "transient": {
    "indices.memory.index_buffer_size": "30%"
  }
}
~~~

- Desactivar el *refresh*

> Això el què farà és que l'index no s'intenti actualitzar durant cada segon que s'està ingestant

~~~json
PUT /index-name/_settings
{
  "index": {
    "refresh_interval": "-1",
    "merge.policy.merge_factor": 30,
    "merge.scheduler.max_thread_count": 1
  }
}
~~~

> Després s'ha de tornar a cambiar l'interval de refresc

> Això si, si creieu convenient posar un altre nombre que no sigui 1s, també és quelcom a tenir en compte

> Això ja depèn de la rapidesa amb la que voleu que s'actualitzin com a objectes cercables els documents de l'index
~~~json
PUT /index-name/_settings
{
  "index": {
    "refresh_interval": "1s"
  }
}
~~~

- Activar momentaniament el **thread pools**, per poder gestionar les cues i evitar qualsevol tap (cuello de botella)
~~~json
PUT /_cluster/settings
{
  "transient": {
    "threadpool.write.queue_size": 500
  }
}
~~~

3) Ajustar variables del clúster

- Momentaniament, es podria canviar el màxim de RAM que es fa servir al fitxer **jvm.options**

> Sobretot, **mai posar més de 30GB**, perquè crearà un **garbage collector**, és a dir, una cua de tot el que no pot processar amb 30GB

> Si es canvia el màxim de jvm, **s'ha de tornar a posar al valor d'abans sempre** 
