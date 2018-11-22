# bido-extension
Propose to extend the BiDO Standard Bibliometric Measures from SPAR Ontology Network   
## Quering with SPARQL  
The competency questions and the queries is presented in this section. You can click in the play button to execute the query.  
### CQ1. How many publications have a researcher?  
```
prefix fabio: <http://purl.org/spar/fabio/>  
prefix bido: <http://purl.org/spar/bido-core/>  
prefix frbr: <http://purl.org/vocab/frbr/core/>  
prefix dcterms: <http://purl.org/dc/terms/>  
  
SELECT DISTINCT ?numDocs ?name AS ?authorName  (group_concat(distinct ?orgName; separator="; ") as ?affils)  
WHERE  
{  
?author bido:holdsBibliometricDataInTime ?authorMeasure ;   
             foaf:name ?name .  
?org foaf:member ?author ;  
        foaf:name ?orgName .  
?authorMeasure bido:withBibliometricData ?authorDocNumber .  
?authorDocNumber bido:hasMeasure <http://purl.org/spar/bido/author-number-documents> ;  
            bido:hasNumericValue ?numDocs2.  
bind(xsd:int(?numDocs2) as ?numDocs )  
}   
GROUP BY ?name ?numDocs  
ORDER BY DESC(?numDocs) ?authorName  
```
[![play](https://user-images.githubusercontent.com/43136359/47848297-3959fb80-ddce-11e8-8124-4f86d53d4d2a.png)](http://spar.linkeddata.es/sparql)  
### CQ2. How many citations have a publication?  
```
prefix fabio:<http://purl.org/spar/fabio/>  
prefix bido: <http://purl.org/spar/bido-core/>  
prefix frbr: <http://purl.org/vocab/frbr/core/>      
prefix dcterms: <http://purl.org/dc/terms/>  
  
SELECT DISTINCT ?numCitations ?paperTitle  
WHERE  
{  
?paper bido:holdsBibliometricDataInTime ?paperMeasure .  
?biblio frbr:realization ?paper ;  
        dcterms:title ?paperTitle .  
?paperMeasure bido:withBibliometricData ?paperCitations .  
?paperCitations bido:hasMeasure <http://purl.org/spar/bido/paper-citation-count> ;  
                 bido:hasNumericValue ?numCitations2.  
bind(xsd:int(?numCitations2) as ?numCitations )  
}  
ORDER BY DESC(?numCitations)  
```
[![play](https://user-images.githubusercontent.com/43136359/47848297-3959fb80-ddce-11e8-8124-4f86d53d4d2a.png)](http://spar.linkeddata.es/sparql)  
### CQ3. Which is the h-index of the journal?
```
prefix fabio:<http://purl.org/spar/fabio/>  
prefix bido: <http://purl.org/spar/bido-core/>  
prefix frbr: <http://purl.org/vocab/frbr/core/>     
prefix dcterms: <http://purl.org/dc/terms/>  
  
SELECT DISTINCT ?hindex ?nameSource  
WHERE  
{  
?source bido:holdsBibliometricDataInTime ?SMY ;  
        foaf:name ?nameSource .      
?SMY bido:withBibliometricData ?FI ;  
     bido:accordingTo  "SJR".  
?FI bido:hasMeasure <http://purl.org/spar/bido/h-index> .  
?FI bido:hasNumericValue ?hindex2 .  
bind(xsd:int(?hindex2) as ?hindex )  
}  
ORDER BY  DESC(?hindex)  
```
[![play](https://user-images.githubusercontent.com/43136359/47848297-3959fb80-ddce-11e8-8124-4f86d53d4d2a.png)](http://spar.linkeddata.es/sparql)  
### CQ4. How many publications have been published in journals with SJR or JCR?
```
prefix fabio:<http://purl.org/spar/fabio/>  
prefix bido: <http://purl.org/spar/bido-core/>  
prefix frbr: <http://purl.org/vocab/frbr/core/>      
prefix dcterms: <http://purl.org/dc/terms/>  
  
SELECT DISTINCT (count(?paper) as ?num) ?kind  
WHERE  
{  
?paper rdf:type ?kind ;  
       frbr:partOf ?source .  
?source bido:holdsBibliometricDataInTime ?SMY .        
?SMY bido:withBibliometricData ?FI ;  
     bido:accordingTo  "SJR".  
     OPTIONAL  
    { ?SMY bido:accordingTo "JCR" . }  
?FI bido:hasMeasure <http://purl.org/spar/bido/journal-impact-factor> .  
?FI bido:hasNumericValue ?sjr .  
}  
```
[![play](https://user-images.githubusercontent.com/43136359/47848297-3959fb80-ddce-11e8-8124-4f86d53d4d2a.png)](http://spar.linkeddata.es/sparql)  
### CQ5. How many publications have published in determinate quartile and in which area and discipline?
```
prefix fabio:<http://purl.org/spar/fabio/>  
prefix bido: <http://purl.org/spar/bido-core/>  
prefix frbr: <http://purl.org/vocab/frbr/core/>      
prefix dcterms: <http://purl.org/dc/terms/>  
  
SELECT DISTINCT (count(?paper) AS ?numPaper) ?nameDisc ?nameArea ?Q  
WHERE  
{  
?paper frbr:partOf ?source .  
?source bido:holdsBibliometricDataInTime ?SMY .  
?SMY bido:withBibliometricData ?SYAD .  
?SYAD bido:relatesToSubjectCategory ?disc ;  
      bido:hasQuartile ?Q .  
?disc skos:prefLabel ?nameDisc ;  
      skos:broader ?area .  
?area skos:prefLabel ?nameArea .  
}  
ORDER BY ?Q DESC(?numPaper)  
```
```
#publications by researcher   
prefix fabio:<http://purl.org/spar/fabio/>  
prefix bido: <http://purl.org/spar/bido-core/>  
prefix frbr: <http://purl.org/vocab/frbr/core/>      
prefix dcterms: <http://purl.org/dc/terms/>  
  
SELECT DISTINCT  ?authorName (count(?paper) AS ?numPaper) ?nameDisc ?nameArea ?Q ?author  
WHERE  
{  
?author foaf:name ?authorName ;  
        dcterms:creator ?paper .  
?paper frbr:partOf ?source .  
?source bido:holdsBibliometricDataInTime ?SMY .  
?SMY bido:withBibliometricData ?SYAD .  
?SYAD bido:relatesToSubjectCategory ?disc ;  
      bido:hasQuartile ?Q .  
?disc skos:prefLabel ?nameDisc ;  
      skos:broader ?area .  
?area skos:prefLabel ?nameArea .  
}  
ORDER BY DESC(?numPaper) ?authorName  
```
[![play](https://user-images.githubusercontent.com/43136359/47848297-3959fb80-ddce-11e8-8124-4f86d53d4d2a.png)](http://spar.linkeddata.es/sparql)   
### CQ6. Which is the rank of the journal?
```
prefix fabio:<http://purl.org/spar/fabio/>  
prefix bido: <http://purl.org/spar/bido-core/>  
prefix frbr: <http://purl.org/vocab/frbr/core/>      
prefix dcterms: <http://purl.org/dc/terms/>  
  
SELECT DISTINCT  ?rank ?Q ?nameSource  
WHERE  
{  
?source foaf:name ?nameSource ;  
        bido:holdsBibliometricDataInTime ?SMY .  
?SMY bido:withBibliometricData ?SYAD .  
?SYAD bido:relatesToSubjectCategory ?disc ;  
      bido:hasQuartile ?Q ;  
      bido:rankInQuartile ?rank .  
?disc skos:prefLabel ?nameDisc ;  
      skos:broader ?area .  
?area skos:prefLabel ?nameArea .  
}  
ORDER BY (xsd:integer(?rank))  
```
[![play](https://user-images.githubusercontent.com/43136359/47848297-3959fb80-ddce-11e8-8124-4f86d53d4d2a.png)](http://spar.linkeddata.es/sparql)  
