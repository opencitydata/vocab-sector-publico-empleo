# Ejemplos de consultas

Se ha realizado un proceso de evaluación a través de unos datos de prueba generados, para comprobar la cobertura del vocabulario con respecto a los requisitos planteados.

Para ello se ha realizado la transformación a partir de los datos publicados por el [buscador de convoocatorias de empleo de la administración general del estado](https://administracion.gob.es/pagFront/empleoBecas/empleo/buscadorEmpleoAvanzado.htm) de los ayuntamientos de A Coruña, Madrid y Zaragoza.

Los datos transformados y anotados con este vocabulario se han cargado en un [Virtuoso endpoint](http://ciudadesabiertas.linkeddata.es/sparql).

A continuación, se presentan varias consultas que se han ejecutado en este endpoint y que han permitido evaluar el vocabulario de acuerdo con los requisitos que se plantearon en la actividad de especificación. 

El grafo de trabajo, con datos de prueba y skos, está en http://ciudadesabiertas.linkeddata.es/datosabiertos/grafo/sector-publico/empleo/convocatorias

## Consulta : 

```
PREFIX esempleo:<http://vocab.ciudadesabiertas.es/def/sector-publico/empleo#>
PREFIX skos:<http://www.w3.org/2004/02/skos/core#>
PREFIX xsd:<http://www.w3.org/2001/XMLSchema#>
PREFIX skos-empleo-cuerpo:<http://vocab.linkeddata.es/datosabiertos/kos/sector-publico/empleo/cuerpo>
PREFIX skos-empleo-empleado-publico:<http://vocab.linkeddata.es/datosabiertos/kos/sector-publico/empleo/empleado-publico>
PREFIX skos-empleo-grupo-profesional:<http://vocab.linkeddata.es/datosabiertos/kos/sector-publico/empleo/grupo-profesional>
PREFIX skos-empleo-modalidad:<http://vocab.linkeddata.es/datosabiertos/kos/sector-publico/empleo/modalidad>
PREFIX skos-empleo-turno:<http://vocab.linkeddata.es/datosabiertos/kos/sector-publico/empleo/turno>
PREFIX eli:<http://data.europa.eu/eli/ontology#>
PREFIX dcterms:<http://purl.org/dc/terms/>
PREFIX schema:<http://schema.org/>
SELECT  ?
WHERE { ?
}
```
