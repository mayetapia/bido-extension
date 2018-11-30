# BiDO Ontology Extension
These graphs and table explain the extension to the BiDO Standard Bibliometric Measures Module from SPAR Ontology Network.
![quartile and rank example](https://user-images.githubusercontent.com/43136359/49281638-56492380-f48d-11e8-8c7e-334c1bbf89e1.jpeg)
-----------------------------------------------------------------------------------------------------------------------------------
![author num docs](https://user-images.githubusercontent.com/43136359/49303926-4f410600-f4cb-11e8-9407-8541b6ea6676.jpeg)
-----------------------------------------------------------------------------------------------------------------------------------
![paper citation count](https://user-images.githubusercontent.com/43136359/49304447-a7c4d300-f4cc-11e8-9f54-1abc9890fb03.png)
-----------------------------------------------------------------------------------------------------------------------------------
![terms](https://user-images.githubusercontent.com/43136359/49304551-f6726d00-f4cc-11e8-8b63-06f48ebe3fac.JPG)

Where C = Class; OP = Object Property; DP = Data Property; NI = Name Individual

## Quering with SPARQL  
The competency questions and the queries is presented in this section. You can click in the play button to execute the query.  
### CQ1. How many publications does a researcher have?
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
[![play](https://user-images.githubusercontent.com/43136359/47848297-3959fb80-ddce-11e8-8124-4f86d53d4d2a.png)](https://bit.ly/2Rq6MyG)  
### CQ2. How many citations does a publication have? 
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
[![play](https://user-images.githubusercontent.com/43136359/47848297-3959fb80-ddce-11e8-8124-4f86d53d4d2a.png)](https://bit.ly/2Qhzx37)  
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
[![play](https://user-images.githubusercontent.com/43136359/47848297-3959fb80-ddce-11e8-8124-4f86d53d4d2a.png)](https://bit.ly/2R9AR5d)  
### CQ4. How many publications have been published in journals indexed in SJR or JCR?
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
[![play](https://user-images.githubusercontent.com/43136359/47848297-3959fb80-ddce-11e8-8124-4f86d53d4d2a.png)](https://bit.ly/2S76NYc)  
### CQ5. How many publications have been published in a specific quartile and in which area and discipline?
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
[![play](https://user-images.githubusercontent.com/43136359/47848297-3959fb80-ddce-11e8-8124-4f86d53d4d2a.png)](https://bit.ly/2BsF7HP)  
### Publications by researcher 
```
  
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
[![play](https://user-images.githubusercontent.com/43136359/47848297-3959fb80-ddce-11e8-8124-4f86d53d4d2a.png)](https://bit.ly/2PMo8sK)   
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
[![play](https://user-images.githubusercontent.com/43136359/47848297-3959fb80-ddce-11e8-8124-4f86d53d4d2a.png)](https://bit.ly/2S7coxG)  
