# Desplegament Docker

## Què hi ha?

En aquesta carpeta trobareu un [docker-compose](/docker/docker-compose.yml) i unes [instruccions](/docker/indicacions_docker.sh) per poder desplegar un entorn Elastic

## De què consta aquest entorn?

Al desplegar el contenidor Docker {nom_de_la_carpeta}, es creará un contenidor amb dos contenidors:

- **elasticsearch_redarbor**: una instància d'Elasticsearch 8.16.1
- **kibana_redarbor**: una instància de Kibana 8.16.1

El desplegament de Elasticsearch **no fa servir autentificació**, per tant:

- No es necessita usuari
- No es necessita contrasenya
- No fa falta generar cap token
- No fa falta administrar permisos ni usuaris

## Què es necessita?

Prèviament ha d'estar instal·lat `Docker` i `Docker Desktop`. Un cop està això fet:

1) S'ha de crear una carpeta i col·locar el [docker-compose](/docker/docker-compose.yml)
  a) El nom de la carpeta pot ser el què voleu
  b) Podeu crear la carpeta al lloc què volgueu

2) Entreu a les [instruccions](/docker/indicacions_docker.sh) i seguiu-les
