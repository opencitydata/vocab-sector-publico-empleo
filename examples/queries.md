# Ejemplos de consultas

Se ha realizado un proceso de evaluación a través de unos datos de prueba generados, para comprobar la cobertura del vocabulario con respecto a los requisitos planteados.

Para ello se ha realizado la transformación a partir de los datos publicados por el [buscador de convoocatorias de empleo de la administración general del estado](https://administracion.gob.es/pagFront/empleoBecas/empleo/buscadorEmpleoAvanzado.htm) de los ayuntamientos de A Coruña, Madrid y Zaragoza.

Los datos transformados y anotados con este vocabulario se han cargado en un [Virtuoso endpoint](http://ciudadesabiertas.linkeddata.es/sparql).

A continuación, se presentan varias consultas que se han ejecutado en este endpoint y que han permitido evaluar el vocabulario de acuerdo con los requisitos que se plantearon en la actividad de especificación. 

El grafo de trabajo, con datos de prueba y skos, está en http://vocab.ciudadesabiertas.es/datosabiertos/grafo-recurso/sector-publico/empleo/cep

## 1. ¿Qué CEP se han publicado en una fecha concreta?

Pongamos como fecha el 5 de octubre de 2011:

```
PREFIX esempleo: <http://vocab.ciudadesabiertas.es/def/sector-publico/empleo#>
PREFIX schema: <https://schema.org/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX dct: <http://purl.org/dc/terms/>

select ?idCEP ?descripcion ?fechaPub ?numeroPlazas where {
?CEP a esempleo:ConvocatoriaEmpleoPublico .
?CEP dct:identifier ?idCEP .
?CEP dct:description ?descripcion .
?CEP schema:datePublished ?fechaPub .
?CEP esempleo:numeroPlazasConvocadas ?numeroPlazas
FILTER (?fechaPub = "2011-10-05"^^xsd:date)
} 
```
## 2. ¿Qué CEP están actualmente abiertos?

Los resultados variarán según el día que hagamos la consulta.

```
PREFIX esempleo: <http://vocab.ciudadesabiertas.es/def/sector-publico/empleo#>
PREFIX schema: <https://schema.org/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX dct: <http://purl.org/dc/terms/>

select ?idCEP ?descripcion ?fechaPub ?numeroPlazas where {
?CEP a esempleo:ConvocatoriaEmpleoPublico .
?CEP dct:identifier ?idCEP .
?CEP dct:description ?descripcion .
?CEP schema:datePublished ?fechaPub .
?CEP esempleo:numeroPlazasConvocadas ?numeroPlazas .
?CEP esempleo:estadoPlazo ?estado .
FILTER (?estado = "true"^^xsd:boolean)
}
```

## 3 Dame la información de la CEP con esta referencia de esta convocatoria

Pongamos que elegimos la convocatoria **000020**
```
PREFIX esempleo: <http://vocab.ciudadesabiertas.es/def/sector-publico/empleo#>
PREFIX schema: <https://schema.org/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX dct: <http://purl.org/dc/terms/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX dce: <http://purl.org/dc/elements/1.1/>

select  ?descripcion ?fechaPub ?numeroPlazas ?plazo ?pubLabel ?grupLabel ?OEPTitle ?turnoLabel ?plazasPorTurno
  where {
?CEP a esempleo:ConvocatoriaEmpleoPublico .
?CEP dct:identifier "cep000020"^^xsd:string .
?CEP dct:description ?descripcion .
?CEP schema:datePublished ?fechaPub .
?CEP esempleo:numeroPlazasConvocadas ?numeroPlazas .
?CEP esempleo:plazo ?plazo .
?CEP esempleo:empleadoPublico ?publico .
?publico skos:prefLabel ?pubLabel .
?CEP esempleo:grupoProfesional ?grupo .
?grupo skos:prefLabel ?grupLabel .
?CEP esempleo:plazasOEP ?OEP.
?OEP dce:title ?OEPTitle .
?CEP esempleo:plazaPorTurno ?plazaPorTurno .
?plazaPorTurno esempleo:turnoPlaza ?turno .
?turno skos:prefLabel ?turnoLabel .
?plazaPorTurno esempleo:plazasxturno ?plazasPorTurno .
FILTER (langMatches(lang(?pubLabel),"es") && langMatches(lang(?grupLabel),"es") && langMatches(lang(?turnoLabel),"es") )
} 
```

## 4 Dame la información de las plazas por turno de esta referencia

Pongamos que queremos la información de las plazas por turno de la convocatoria **000024**:

```
PREFIX esempleo: <http://vocab.ciudadesabiertas.es/def/sector-publico/empleo#>
PREFIX schema: <https://schema.org/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX dct: <http://purl.org/dc/terms/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX dce: <http://purl.org/dc/elements/1.1/>

select  ?descripcion ?turnoLabel ?plazasPorTurno
  where {
?CEP a esempleo:ConvocatoriaEmpleoPublico .
?CEP dct:identifier "cep000024"^^xsd:string .
?CEP dct:description ?descripcion .
?CEP esempleo:plazaPorTurno ?plazaPorTurno .
?plazaPorTurno esempleo:turnoPlaza ?turno .
?turno skos:prefLabel ?turnoLabel .
?plazaPorTurno esempleo:plazasxturno ?plazasPorTurno .
FILTER (langMatches(lang(?turnoLabel),"es") )
}
```

## 5. ¿Cuántas plazas totales se han ofertado en las CEP de un periodo concreto?

Pongamos que queremos desde el inicio de 2012.

```
PREFIX esempleo: <http://vocab.ciudadesabiertas.es/def/sector-publico/empleo#>
PREFIX schema: <https://schema.org/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX dct: <http://purl.org/dc/terms/>
PREFIX esadm: <http://vocab.linkeddata.es/datosabiertos/def/sector-publico/territorio#>

select (SUM(?numeroPlazas)) AS ?totalPlazas  ?municipio where  {
?CEP a esempleo:ConvocatoriaEmpleoPublico .
?CEP dct:identifier ?CEPId .
?CEP esadm:municipio ?municipio .
?CEP schema:datePublished ?fechaPub .
?CEP esempleo:numeroPlazasConvocadas ?numeroPlazas .
FILTER (?fechaPub >= "2012-01-01"^^xsd:date)
} 
group by  ?municipio
```

## 6 ¿Qué CEP de este periodo son de promoción interna?

Pongamos que queremos saber qué CEP son de promoción interna en 2020.

```
PREFIX esempleo: <http://vocab.ciudadesabiertas.es/def/sector-publico/empleo#>
PREFIX schema: <https://schema.org/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX dct: <http://purl.org/dc/terms/>

select  ?idCEP ?descripcion ?fechaPub   where {
?CEP a esempleo:ConvocatoriaEmpleoPublico .
?CEP dct:identifier ?idCEP .
?CEP dct:description ?descripcion .
?CEP schema:datePublished ?fechaPub .
?CEP esempleo:plazaPorTurno ?plazaPorTurno .
?plazaPorTurno esempleo:turnoPlaza ?turno .
?turno skos:prefLabel ?labelTurno .
FILTER (?fechaPub >= "2020-01-01"^^xsd:date && STR(?labelTurno) = "Interno" )
} 
```

## 7 ¿Qué CEP de acceso libre hay abiertas hoy?


```
PREFIX esempleo: <http://vocab.ciudadesabiertas.es/def/sector-publico/empleo#>
PREFIX schema: <https://schema.org/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX dct: <http://purl.org/dc/terms/>

select distinct ?idCEP ?labelTurno ?fechaPub   where {
?CEP a esempleo:ConvocatoriaEmpleoPublico .
?CEP dct:identifier ?idCEP .
?CEP dct:description ?descripcion .
?CEP schema:datePublished ?fechaPub .
?CEP esempleo:estadoPlazo "true"^^xsd:boolean .
?CEP esempleo:plazaPorTurno ?plazaPorTurno .
?plazaPorTurno esempleo:turnoPlaza ?turno .
?turno skos:prefLabel ?labelTurno .
FILTER (STR(?labelTurno) = "Libre")
}
```
## 8 ¿Qué CEP para este grupo profesional A1 hay abiertas hoy?


```
PREFIX esempleo: <http://vocab.ciudadesabiertas.es/def/sector-publico/empleo#>
PREFIX schema: <https://schema.org/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX dct: <http://purl.org/dc/terms/>

select distinct ?idCEP ?descripcion ?fechaPub ?labelGrupo   where {
?CEP a esempleo:ConvocatoriaEmpleoPublico .
?CEP dct:identifier ?idCEP .
?CEP dct:description ?descripcion .
?CEP schema:datePublished ?fechaPub .
?CEP esempleo:estadoPlazo "true"^^xsd:boolean .
?CEP esempleo:grupoProfesional ?grupo .
?grupo skos:prefLabel ?labelGrupo
FILTER (?labelGrupo = "A1"@es)
}
```
## 9  ¿Qué CEP contienen en su descripción la palabra "arquitecto"?

```
PREFIX esempleo: <http://vocab.ciudadesabiertas.es/def/sector-publico/empleo#>
PREFIX schema: <https://schema.org/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX dct: <http://purl.org/dc/terms/>

select ?idCEP ?descripcion ?fechaPub ?numeroPlazas where {
?CEP a esempleo:ConvocatoriaEmpleoPublico .
?CEP dct:identifier ?idCEP .
?CEP dct:description ?descripcion .
?CEP schema:datePublished ?fechaPub .
?CEP esempleo:numeroPlazasConvocadas ?numeroPlazas
FILTER (CONTAINS(?descripcion,"arquitecto"))
} 
```