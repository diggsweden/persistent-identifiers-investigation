# Referenser

Nedan listar vi de viktigaste källorna som legat till grund eller inspirerat denna specifikation.

## [Architecture of the World Wide Web, Volume One](https://www.w3.org/TR/webarch/)
Denna W3C rekommendation definierar grundläggande principer kring resurser, representationer och format, URI:er, uppslagningsmekanismer osv.

## [RFC 1035 Doman Names - Implementation and Specification](https://datatracker.ietf.org/doc/html/rfc1035)
DNS är tillsammans med HTTP basen för denna specifikation. Uppslagning av domännamn sker oftast på initiativ av olika applikationer, t.ex. webbläsare, och dessa förlitar sig nästan uteslutande på DNS resolvers som är inbyggda i operativsystemen. Detta innebär att vi behöver nästan aldrig bekymra oss om hur DNS protokollet är uppbyggt. Att uppslagningen fungerar smidigt är dock en förutsättning för denna specifikation.

## [RFC 9110 HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)
HTTP protokollet är tillsammans med DNS basen för denna specifikation, särskilt viktigt är de olika HTTP metoderna, hur de olika status koderna fungerar, samt principer och HTTP headrar för content negotiation. Till skillnad från DNS är vi inte isolerade från HTTP protokollet utan vi behöver i denna specifikation ange specifikt hur vi använder dess funktionalitet på en mängd olika sätt.

## [RFC 8288 Web Linking](https://www.rfc-editor.org/rfc/rfc8288.html)
Web linking beskriver hur man kan använda HTTP headern `link` för informera om relationer till olika resurser. Den [förteckningen över relationer](https://www.iana.org/assignments/link-relations/link-relations.xhtml) som finns hos IANA har också fungerat som bra grund för att identifiera relationer som är lämpliga att använda.

## [Content Negotiation by Profile](https://www.w3.org/TR/dx-prof-conneg/)
Denna specifikation (fortfarande ett W3C draft) beskriver hur man kan utvidga content negotiation för att beskriva vilken "data profil" man vill ha. Detta är nyttigt när man har olika uttryck av samma resurs som inte fångas av formatet, t.ex. version 1 och 2 av ett API kan använda samma format men ha olika uttryck. 

## [Linked Data](https://www.w3.org/DesignIssues/LinkedData.html)
I denna design issue introducerar Tim-Berners Lee grunderna för hur man för samman webben med RDF och får det som kallas länkade data. Dokumentet introducerar 5-stjärnig data där man får den fjärde stjärnan när man skapar beständiga identifierare i form av URI:er för att möjliggöra länkning till ting (och kan uttrycka påståenden om dessa via RDF). Principerna som beskrivs, förutom kravet på RDF, är också en del av grunderna för denna specifikation.

## [Data on the Web Best Practices](https://www.w3.org/TR/dwbp)
Rekommendationen handlar i huvudsak om data, men många principer har en bredare tillämning, t.ex.:
1. förtydlingar kring användning av metadata (1 & 2)
2. inte skapa onödiga överbyggnader (16)
3. förtydlignar kring content negotiation (19)
4. att man ska förlita sig på web standards för API:er (24)
5. hantering och användning av beständiga identifierare (9, 11 och 27).

## [Cool URIs for the Semantic Web](https://www.w3.org/TR/cooluris/)
En W3C Interest Group Note som beskriver hur man ska använda 303 redirects för att kunna använda URI:er för företeelser (diskuteras i form av "real world objects"). 

## [10 Rules for Persistent URIs](https://joinup.ec.europa.eu/collection/semic-support-centre/document/10-rules-persistent-uris)
De 10 reglerna kommer från en rapport inom "ISA Action 1.1 on Semantic Interoperability" med titeln "Study on persistent URIs, with identification of best practices and recommendations on the topic for the
MSs and the EC". Rapporten går igenom ett antal länders initiativ med hänsyn till beständiga identifierare och för samman erfarenheterna. De flesta av de 10 reglerna har förts vidare till denna specifikation.

## [Diggs vägledning om tillgängliggöra information](https://digg.se/kunskap-och-stod/oppna-och-delade-data/offentliga-aktorer/vagledning-for-att-tillgangliggora-information#h-Anvandbestandigaidentifierare)
I vägledningen finns en sektion om beständiga identifierare som pekar på principer bakom länkade data samt en rekommenderad utformning som är inspirerad av "10 rules for persistent identifiers" rapporten.