## Lokala identifierare

## Uttrycka relation till alternativ identifierare
rdf:seeAlso - för oklart
owl:sameAs - för starkt, symetrisk
dcterms:identifier - för svagt
schema:sameAs - ska referera en webbsida, inte en annan resurs
schema:mainEntityOfPage - antyder del av relation
schema:about - handlar om, inte alternativ identifierare.
schema:url - huvudsaklig webbsida

Följande kommer från xhtml, men motsvarar exakt det vi vill. Är en xhtml version av alternate link headern.
xhv:alternate, dvs:
http://www.w3.org/1999/xhtml/vocab#alternate


## Jämför med Europeana

Noticeable requirements are:
- R1. distinction between “provided objects” (painting, book, movie, archaeology site,
  archival file, etc.) and their digital representations
- R2. distinction between objects and metadata records describing an object
- R3. multiple records for the same object should be allowed, containing potentially
  contradictory statements about this object
- R4. support for objects that are composed of other objects
- R5. compatibility with different abstraction levels of description (e.g. if a provider wishes
  to submit descriptions that follow the distinctions introduced in FRBR Group 1 [FRBR])
- R6. EDM provides a standard metadata format that can be specialized
- R7. support for contextual resources, including concepts from controlled vocabularies.


### Proxies

### edm:isRepresentationOf
A resource that is the representation of another may only be the
representation of one other resource. Conversely, a resource that is
represented may be the source of 0 to many representations.

https://pro.europeana.eu/files/Europeana_Professional/Share_your_data/Technical_requirements/EDM_Documentation//EDM_Definition_v5.2.8_102017.pdf

