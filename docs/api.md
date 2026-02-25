## Förutsättningar

I diskussionen nedan kommer vi utgå från den svenska REST profilen. Men det är viktigt att notera att principerna är av generell natur och bör kunna appliceras på de flesta REST API:er.

Enligt den svenska REST profilen ska API:er byggas utifrån principen att exponera resurser med givna identifierare. Identifierarna uttrycks som URI:er och används i HTTP  protokollet, typiskt via GET, PUT, POST, PATCH och DELETE för att komma åt och manipulera de underliggande resurserna.

Vi kommer nu i tur och ordning titta på:

1. Vilka resurser som bör ha beständiga identifierare  
2. Rekommenderat utseende för beständiga identifierare  
3. Introducera HTTP redirects för beständiga identifierare  
4. Uttrycka beständiga identifierare i HTTP Messages  
5. Inkludera beständiga identifierare när vi listar eller länkar

## 1. Vilka resurser som bör ha beständiga identifierare

Från REST API-profilen känner vi till att ett API exponerar resurser med identifierare i form av webbadresser, och att resurser kan delas upp i samlingsresurser och enkla resurser. Det är i första hand för en delmängd av de enkla resursererna som vi ser ett behov av beständiga identifierare. För att förenkla diskussionen nedan delar vi upp de enkla resurserna i fyra grupper:

**Företeelser** - något som inte kan fångas fullständigt i en digital representation, t.ex. en organisation, en person, en plats eller en historisk händelse. <br>
**Fullständiga resurser** - något som kan fångas fullständigt i en digital representation, t.ex. ett meddelande, en användarprofil, ett kvitto eller sessionsdata.<br>
**Funktionella resurser** - något som inte motsvarar en självständig resurs utan motsvarar ett sätt att operera på en företeelse, fullständig resurs etc.<br>
**Artefakter** motsvarar något som inte är första klassens medborgare i API:et då det inte kan behandlas som övriga resurser, t.ex. bilagor, bilder, PDF:er osv. 

Notera att ingen av dessa grupper nämns i den svenska REST API profilen, vi introducerar dem eftersom hanteringen av beständiga identifierare är olika inom varje grupp.

| Typ av resurs | Exempel | Beständig identifierare |
|---|---|---|
| Företeelser | `/api/v1/organisationer/123` | Ja |
| Fullständiga resurser | `/api/v1/meddelande/20260101T1201070101` | Vid behov |
| Funktionella resurser | `/api/v1/konto/123456/insattning` | Nej |
| Artefakter | `/api/v1/organisationer/123/logo.png` | Vid behov |
| Samlingsresurs | `/api/v1/organisationer` | Nej |

## 2. Rekommenderat utseende för beständiga identifierare

Rekommendationen är att beständiga identifierare läggs under ett eget namnutrymme, t. ex. under `/id`, separerat från API:et:

| | Exempel |
|---|---|
| Beständig identifierare | `https://example.se/id/organisation/123` |
| API-adress | `https://example.se/api/v1/organisationer/123` |

Att den beständiga identifieraren och API-adressen är olika motiveras av tre skäl:

1. **Singular vs plural.** REST API-profilen föreskriver plural (`/api/v1/organisationer/123`) medan profilen för beständiga identifierare föredrar singular (`/id/organisation/123`).

2. **Beständighet vs versionering.** API:er versionshanteras och adresser kan ändras mellan versioner. En beständig identifierare ska per definition inte ändras.

3. **API:er innehåller ofta beskrivningar.** Enkla resurser motsvarar oftast företeelser i verkligheten — bilar, personer, organisationer och det är dessa vi vill ha en beständig identifierare för. Dessa kan man inte ladda ner, men man kan ladda ner en beskrivning av dem. Notera att detta är annorlunda för fullständiga resurser och artefakter som *är* sin digitala representation.

## 3. Introducera HTTP redirects för beständiga identifierare

I de föregående avsnitten har vi etablerat att företeelser, fullständiga resurser och artefakter kan ha beständiga identifierare under `/id/`-namnutrymmet, separerat från API:et under `/api/`. Här beskriver vi den konkreta HTTP-mekanismen för att nå från den beständiga identifieraren till API resursen.

### Företeelser — 303 See Other

Företeelser är ofta något som finns i verkligheten (en organisation, en person, en plats) och går därmed inte att få en fullständig digital representation för, därför kan vi bara referera till en annan informationsresurs. När en klient slår upp en beständig identifierare för en företeelse svarar servern med en 303 See Other som pekar vidare till en beskrivning av företeelsen i API:et.

```HTTP
GET /id/organisation/123 HTTP/1.1
Host: example.se
----
HTTP/1.1 303 See Other
Location: /api/v1/organisationer/123
Link: </api/v1/organisationer/123>; rel="describedby"
```

### Fullständiga resurser och artefakter — 307 Temporary Redirect

Fullständiga resurser och artefakter är i sig digitala entiteter som kan levereras direkt vid uppslag. Men det kan vara viktigt att tillhandahålla beständiga identifierare som inte är beroende av API-versioner, gateways, lagringslösningar eller liknande. I dessa fall skapar man en beständiga identifierare och använder 307 Temporary Redirect.

```HTTP
GET /id/organisation/123/logo HTTP/1.1
Host: example.se
----
HTTP/1.1 307 Temporary Redirect
Location: /api/v1/organisationer/123/logo.png
```

## 4. Uttrycka beständiga identifierare i HTTP Messages

Ofta är det en god ide att i själva meddelandet förtydliga vilken beständig identifierare resursen har oavsett från vilken API adress som används. Observera att detta är inte möjligt för artefakter då det digitala innehållet är en svart låda ur API:ets perspektiv. REST API-profilen föreskriver att meddelanden levereras i JSON-format. Nedan ser vi fyra olika möjliga JSON uttryck. 

### 4.1 Hitta på din eget fält
Du väljer själv vilket fält du vill använda för att ange den beständiga identifieraren, kanske har du redan ett fält som används för detta syfte, nedan används `pid`.

```json
{
  "pid": "/id/organisation/123",
  "namn": "Exempelorganisationen",
  "orgnr": "555555-5555",
  ...
}
```

### 4.2 JSON-LD
JSON-LD har ett standardiserat sätt att ange vilken resurs som beskrivs via `@id`-nyckeln, detta ska alltid vara den beständiga identifieraren. I exemplet nedan använder vi en relativ URI (utifrån domänen) istället för en absolut, det går naturligtvis bra att använda en absolut istället som rekommenderas av REST API profilen.

```json
{
  "@id": "/id/organisation/123",
  "namn": "Exempelorganisationen",
  "orgnr": "555555-5555",
  ...
}
```

### 4.3 JSON-LD med alternativ nyckel

Om man inte vill använda nyckeln `@id` kan man välja en egen nyckel, t.ex. `pid`, och definiera i ett JSON-LD context att den ska tolkas som `@id`. Notera att detta fall är snarlikt 4.1, skillnaden är att man hjälper de som förstår JSON-LD att på ett automatiskt sätt tolka informationen.

```json
{
  "@context": {
    "pid": "@id",
    "namn": "http://xmlns.com/foaf/0.1/name",
    "orgnr": "http://purl.org/dc/terms/identifier"
  },
  "pid": "/id/organisation/123",
  "namn": "Exempelorganisationen",
  "orgnr": "555555-5555",
  ...
}
```

Effekten är densamma — konsumenter som förstår JSON-LD vet att `pid` pekar ut den beständiga identifieraren, medan övriga konsumenter ser det som ett vanligt JSON-fält. Vi valde att här också lägga till information om hur några av de andra värdena ska tolkas enligt etablerade standarder.

### 4.4 JSON Hypertext Application Language (HAL)

HAL rekommenderar användning av `_links` för att ange länkar med olika relationer. Den speciella relationen `canonical` ([RFC6596](https://datatracker.ietf.org/doc/html/rfc6596)) är lämplig för att peka ut den beständiga identifieraren.

```json
{
  "namn": "Exempelorganisationen",
  "orgnr": "555555-5555",
  "_links": {
    "canonical": { "href": "/id/organisation/123" }
  },
  ...
}
```

## 5. Inkludera beständiga identifierare när vi listar eller länkar

Att uttrycka länkar mellan olika resurser är en viktig princip (hypermedia). En uppenbar lösning är att bara använda de beständiga identifierarna och förlita sig på de redirects som beskrivits i sektion 3 ovan. Fördelen med detta är att det är elegant och risken att använda fel URL minimeras. Nackdelen är att vi introducerar en extra uppslagning som kan upplevas som onödig. I API:et vet vi ju vilken API resurs vi ska peka på, varför inte tillhandahålla den direkt i svaret. Nedan försöker vi bemöta detta genom att lista både API adressen och den beständiga identifieraren.

Vi har tidigare konstaterat att funktionella resurser inte behöver beständiga identifierare. Följande uttryck från REST API profilen berörs därmed inte alls:

```
{
  "kontonummer":"12345",
  "balans": 100.00,
  "_links": {
    "insattning": {"href": "/konton/12345/insattning"},
    "uttag" {"href": "/konton/12345/uttag"},
    "overforing": {"href": "/konton/12345/overforing"}
  }
}
```

Därmed fokuserar vi på hur man uttrycker beständiga identfierare i listningar och relationer mellan enkla resurser (ej funktionella) nedan.
För listningar tar vi exemplet med en samling av organisationer och för relationer tar vi exemplet att vi vill berätta att en organisation har en logo.

### 5.1 Listningar - JSON-LD alternativet

I detta fall använder vi activity streams för att leverera en samling (Collection), där finns stöd för paginering om det skulle behövas.
Den beständiga identifieraren för organisationen (Aktiebolaget Volvo) anges med `describedby` då det är en företeelse (303 redirects).
Om vi istället hade sökt efter fullständiga resurser eller artefakter (307 redirects) hade vi använt `self` istället för `describedby`, båda är definierade explicit i `@context`.

```
GET /foretagsinformation/v2/organisationer
HTTP/1.1 200 OK
Content-Type: application/hal+json
...
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    {
      "namn": "http://xmlns.com/foaf/0.1/name",
      "describedby": "http://www.iana.org/assignments/relation/describedby",
      "self": "http://www.iana.org/assignments/relation/self",
    }
  ],
  "type": "Collection",
  "totalItems: 1,
  "items": [{
    "@id": "/id/organisation/5560125790",
    "namn": "Aktiebolaget Volvo",
    "describedby": "/foretagsinformation/v2/organisationer/5560125790",
  }]
}
```

### 5.2 Listningar - HAL alternativet

Här använder vi _links konstruktionen från HAL för att uttrycka både den beständiga identifieraren (`canonical`) och API resursen (`self`).
Notera att samma argument som i 5.1 gäller här också, dvs använd `describedby` för företeelser och `self` för fullständiga resurser eller artefakter.

```
GET /foretagsinformation/v2/organisationer
HTTP/1.1 200 OK
Content-Type: application/hal+json
...
{
  "_meta": [{ "totalRecords": 1, "page": 1, "limit": 20, "count": 1 }],
  "_links": [{ "href": "/foretagsinformation/v2/organisationer", "rel": "self" }],
  "organisationslista": [{
    "namn": "Aktiebolaget Volvo",
    "_links": {
      "canonical": {
        "href" : "/id/organisation/5560125790"
      },
      "self": {
        "href" : "/foretagsinformation/v2/organisationer/5560125790"
      }
    }]
  }]
}
```

### 5.3 Relationer - JSON-LD alternativet 

I JSON-LD anger man relationen med en nyckel `logo` som i detta fall översätts till `foaf:depiction`. I det objekt vi pekar ut anger vi den beständiga identifieraren som man alltid gör i JSON-LD med `@id`. Utöver det lägger vi till en direktlänk till resursen i API:et via `self`.  Notera att vi här har det omvända fallet från 5.1, dvs vi uttrycker en relation till en artefakt, därmed använder vi relationen `self` istället för `describedby`. Om vi länkade till en företeelse skulle vi använda `describedby` istället.

```json
{
  "@context": {
    "namn": "http://xmlns.com/foaf/0.1/name",
    "orgnr": "http://purl.org/dc/terms/identifier",
    "logo": "http://xmlns.com/foaf/0.1/depiction",
    "describedby": "http://www.iana.org/assignments/relation/describedby",
    "self": "http://www.iana.org/assignments/relation/self"
  },
  "@id": "/id/organisation/123",
  "namn": "Exempelorganisationen",
  "orgnr": "555555-5555",
  "logo": {
    "@id": "/id/organisation/123/logo",
    "self": "/api/v1/organisationer/123/logo.png"
  },
  ...
}
```

### 5.4 Relationer - HAL alternativet

Tyvärr finns inget snyggt alternativ i dagsläget om man ska strikt följa HAL då det inte går att gruppera länkar med samma relation (rel). I fallet då man har en enskild relation, dvs en logo, så fungerar det ändå. Men om man behöver länka till multipla enkla resurser som uppfyller samma roll (bilagor, medlemmar, etc.) så måste den beständiga identifieraren och API resursen hållas ihop.
Vi listar här två möjliga lösningar, gruppering respektive en ny nyckel:

**Gruppera länkar under relationen**

```json
{
  "@id": "/id/organisation/123",
  "namn": "Exempelorganisationen",
  "orgnr": "555555-5555",
  "logo": {
    "_links": {
      "canonical": { 
        "href": "/id/organisation/123/logo"
      },
      "self": {
        "href": "api/v1/organisationer/123/logo.png"
      }
    }
  },
  ...
}
```

**Ny nyckel för den beständiga identifieraren**

Detta fall är strukturellt nära hur det ser ut i JSON-LD, så vi väljer att använda nyckeln `@id` för att visa på likheten med 5.3.

```json
{
  "@id": "/id/organisation/123",
  "namn": "Exempelorganisationen",
  "orgnr": "555555-5555",
  "_links": {
    "self": {
      "@id": "/id/organisation/123/logo",
      "href": "api/v1/organisationer/123/logo.png"
    }
  },
  ...
}
```

