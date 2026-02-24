## Förutsättningar

I diskussionen nedan kommer vi utgå från den svenska REST profilen. Men det är viktigt att notera att principerna är av generell natur och bör kunna appliceras på de flesta REST API:er.

Enligt den svenska REST profilen ska API:er byggas utifrån principen att exponera resurser med givna identifierare. Identifierarna uttrycks som URI:er och används i HTTP  protokollet, typiskt via GET, PUT, POST, PATCH och DELETE för att komma åt och manipulera de underliggande resurserna.

Vi kommer nu i tur och ordning titta på:

1. Vilka resurser som bör ha beständiga identifierare  
2. Rekommenderat utseende för beständiga identifierare  
3. Introducera HTTP redirects för beständiga identifierare  
4. Uttrycka beständiga identifierare i HTTP Messages  
5. Uttrycka länkar till resurser med beständiga identifierare

## 1. Vilka resurser som bör ha beständiga identifierare

Från REST API-profilen känner vi till att ett API exponerar resurser med identifierare i form av webbadresser, och att resurser kan delas upp i samlingsresurser och enkla resurser. Det är i första hand för en delmängd av de enkla resursererna som vi ser ett behov av beständiga identifierare. För att förenkla diskussionen nedan delar vi upp de enkla resurserna i fyra grupper:

**Företeelser** - något som inte kan fångas fullständigt i en digital representation, t.ex. en organisation, en person, en plats eller en historisk händelse. <br>
**Fullständiga** - något som kan fångas fullständigt i en digital representation, t.ex. ett meddelande, en användarprofil, ett kvitto eller sessionsdata.<br>
**Funktionella** - något som inte motsvarar en självständig resurs utan motsvarar ett sätt att operera på en företeelse, fullständig resurs etc.<br>
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

3. **API:er innehåller ofta beskrivningar.** Enkla resurser motsvarar oftast företeelser i verkligheten — bilar, personer, organisationer och det är dessa vi vill ha en beständig identifierare för. Dessa kan man inte ladda ner, men man kan ladda ner en beskrivning av dem. Notera att detta är annorlunda för artefakter som *är* sin digitala representation.

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

Det finns en poäng i att i själva meddelandet förtydliga vilken företeelse som avses, genom att inkludera den beständiga identifieraren. Det minskar risken för missförstånd och behovet av "out of band"-kunskap om just detta API. Observera att detta enbart är möjligt för företeelser och fullständiga resurser, inte för artefakter då det digitala innehållet är en svart låda ur API:ets perspektiv.

REST API-profilen föreskriver att meddelanden levereras i JSON-format. Rekommendationen här är att gå över till JSON-LD, som har ett standardiserat sätt att ange vilken resurs som beskrivs via `@id`-nyckeln. I exemplet nedan använder vi en relativ URI (utifrån domänen) istället för en absolut, det går naturligtvis bra att använda en absolut istället som rekommenderas av REST API profilen.

```json
{
  "@id": "/id/organisation/123",
  "namn": "Exempelorganisationen",
  "orgnr": "555555-5555",
  ...
}
```

Genom att inkludera `@id` med den beständiga identifieraren framgår det direkt ur meddelandet vilken företeelse som beskrivs — oavsett vilken API-adress som användes för att hämta det.

### Alternativ nyckel via JSON-LD context

Om man inte vill använda nyckeln `@id` kan man välja en egen nyckel, t.ex. `pid`, och definiera i ett JSON-LD context att den ska tolkas som `@id`:

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

## 5. Uttrycka länkar till resurser med beständiga identifierare

Att uttrycka länkar mellan olika resurser är en viktig princip (hypermedia). En uppenbar lösning är att bara använda de beständiga identifierarna och förlita sig på de redirects som beskrivits i sektion 3 ovan. Fördelen med detta är att det är elegant och risken att använda fel URL minimeras. Nackdelen är att vi introducerar en extra uppslagning som kan upplevas som onödig. I API:et vet vi ju vilken API resurs vi ska peka på, varför inte tillhandahålla den direkt i svaret. Nedan försöker vi bemöta detta genom att lista både API adressen och den beständiga identifieraren.

Vi särskiljer nu mellan tre fall där vi så långt som möjligt är kompatibla med det angreppsätt som beskrivs i den svenska REST API profilen, dvs användningen av _links enligt JSON Hypertext Application Language (HAL).

### Listningar av enkla resurser

Det här är det enklaste fallet när vi har en listning av resurser, typiskt när vi hämtar en samling.  I exemplet nedan taget från REST API profilen har vi lagt till den beständiga identifieraren i fältet "@id". Notera att detta blir exakt samma konstruktion som i sektion 4 ovan.

```
GET /foretagsinformation/v2/organisationer
HTTP/1.1 200 OK
Content-Type: application/hal+json
...
{
  "_meta": [{ "totalRecords": 1, "page": 1, "limit": 20, "count": 1 }],
  "_links": [{ "href": "/foretagsinformation/v2/organisationer", "rel": "self" }],
  "organisationslista": [{
    "@id": "/id/organisation/5560125790",
    "namn": "Aktiebolaget Volvo",
    "_links": [{
      "href" : "/foretagsinformation/v2/organisationer/5560125790",
      "rel": "self",
      "method": "GET"
    }]
  }]
}
```

### Relationer - JSON-LD alternativet 

I det här fallet har vi en relation mellan två enkla resurser. I exemplet nedan vill vi ange att en organisation har en logo. I JSON-LD anger man relationen med en nyckel "logo" som i detta fall översätts till `foaf:depiction`. I det objekt vi pekar ut anger vi den beständiga identifieraren som man alltid gör i JSON-LD med `@id`. Utöver det lägger vi till en direktlänk till resursen i API:et via `href`. Vi har föreslagit att `href` bäst representeras via propertyn `http-headers:location`, men man kan även tänka sig att använda `schema:mainEntityOfPage`.

```json
{
  "@context": {
    "namn": "http://xmlns.com/foaf/0.1/name",
    "orgnr": "http://purl.org/dc/terms/identifier",
    "logo": "http://xmlns.com/foaf/0.1/depiction",
    "href": "http://www.w3.org/2011/http-headers#location"
  },
  "@id": "/id/organisation/123",
  "namn": "Exempelorganisationen",
  "orgnr": "555555-5555",
  "logo": {
    "@id": "/id/organisation/123/logo",
    "href": "/api/v1/organisationer/123/logo.png"
  },
  ...
}
```

### Relationer - HAL alternativet

I detta fall använder vi _links konstruktionen för att peka ut logon. Precis som i fallet ovan vill vi ange både den beständiga identifieraren och den direkta adressen. För att åstadkomma detta lägger vi till nyckeln "@id" inne i länk konstruktionen. En klar nackdel är att detta inte ingår i HAL specifikationen.

```json
{
  "@id": "/id/organisation/123",
  "namn": "Exempelorganisationen",
  "orgnr": "555555-5555",
  "_links":[
    {
      "@id": "/id/organisation/123/logo",
      "rel": " **logo**",
      "method": "GET",
      "href": "api/v1/organisationer/123/logo.png"
    }
  ],
  ...
}
```

### Funktionella relationer

I detta fall är länkarna till funktionella resurser som vi konstaterat inte behöver beständiga identifierare, alltså ingen ändring behövs här.

```
{
  "kontonummer":"12345",
  "balans": 100.00,
  "_links":[
    {"rel": " **insättning**", "href":"/konton/12345/insattning"},
    {"rel": " **uttag**", "href":"/konton/12345/uttag"},
    {"rel": " **överföring**", "href":"/konton/12345/overforing"}
  ]
}
```

