# Regler kring utformning och uppslagning av beständiga identifierare

Med en identifierare menas en sträng tecken som refererar till ett ting. För att identifieraren ska vara beständig menas att den på ett tydligt och välkänt sätt refererar till samma ting över en längre tid utan att behöva ändras. Begreppet ting inkluderar både digitala informationsresurser såväl som företeelser i den fysiska världen och sinnesvärlden.

Nedan listar vi först regler för hur beständiga identifierare ska utformas och därefter listas regler kring hur de ska slås upp.

## Utformning av beständiga identifierare

När en beständig identifierare skall utformas begränsar vi oss till att använda URL:er. Utöver att fungera som identifierare möjliggör nämligen URL:er en uppslagningsmekanism. Vi väljer också att begränsa oss till HTTP protokollet då det är en grundläggande och välkänd del av webbarkitekturen.

><a name="BI1"></a> **Regel BI-1:** Beständiga identifierare SKALL uttryckas som URL:er med schemat http eller https.

En bra princip är att beständiga identifierare uttrycker tillhörighet till en aktör som går att verifiera. Med tillhörighet till aktör menas dock inte egennamn eller organisationsnamn. Sådana är inte lämpliga i identifieraren då de inte kan anses vara beständiga.

><a name="BI2"></a> **Regel BI-2:** Domännamn SKALL användas i URL:er för beständiga identifierare, undvik explicita IP adresser eller localhost.

Man bör undvika att inkludera information i URL:en som inte är strikt nödvändig ur perspektivet beständiga identifierare. 

><a name="BI3"></a> **Regel BI-3:** Portnummer, användarnamn eller lösenord SKALL INTE användas i URL:er för beständiga identifierare.

Det är olämpligt att använda frågeparametrar i URL:er för beständiga identifierare. URL:er tillåter att man inkluderar parametrar efter ett frågetecken. Dessa parametrar används oftast när man bygger system av RPC (Remote Procedure Call) karaktär där man vill skicka in värden till en funktion, t.ex. för att göra en sökning. Men, då de flesta implementationer brukar acceptera att man byter ordning på parametrar är det lätt att se hur man av misstag introducerar flera olika URL:er för att referera till samma ting. Frågeparametrar har också en tendens att vara närmare knutna till den specifika implementation man valt.

><a name="BI4"></a> **Regel BI-4:** Frågeparametrar BÖR INTE användas i URL:er för beständiga identifierare.

Det är också olämpligt att använda URL:er med fragment. URL:er tillåter användning av så kallade fragment som är den del som kommer efter symbolen `#`. Fragment har den speciella karaktären att de inte ingår i HTTP protokollet, dvs. man får samma svar för alla URL:er som särskiljer sig endast i fragmentet. URL:er med fragment kan alltså inte särskiljas i uppslagningsmekanismen och man tappar många möjligheter. T.ex. är det inte möjligt att tala om att en URL med fragment har upphört att existera eller bytts ut mot en annan. Man lägger också över en större del av ansvaret på klienten. Det bör noteras att inom länkade data världen har man länge använt URL:er med fragment för företeelser. Dock är problematiken där mindre då man jobbar inom ett enhetligt ramverk med en väletablerad praxis. 

><a name="BI5"></a> **Regel BI-5:** Fragment BÖR INTE användas i URL:er för beständiga identifierare.

## Uppslagninsmekanism för beständiga identifierare

En beständiga identifierare ska kunna slås upp för att leverera information om det ting som refereras såväl som information om den beständiga identifieraren själv. Uppslagning sker via DNS och HTTP protokollen. Notera att en fullständig genomgång av DNS och HTTP ligger utanför denna rekommendation. Istället listas nedan endast de huvudsakliga situationer som förväntas uppkomma kring hantering av beständiga identifierare. Observera att, i de exempel som ges utelämnas många HTTP headrar för att öka läsbarheten.

### Grundläggande regler för uppslagning

Att slå upp URL:er med hjälp av DNS och HTTP är sedan tidigt 1990:tal en självklar del av hur webben fungerar. 

><a name="UM1"></a> **Regel UM-1:** Uppslagning av en beständig identifierare SKALL ske först via DNS för att erhålla rätt IP-adress till servern som ansvarar för domänen. Därefter SKALL HTTP protokollet användas för att slå upp den beständiga identifieraren på den servern.

HTTP protokollet erbjuder ett antal olika metoder för att hämta och skicka information. För att bara få ut information om den beständiga identifieraren använder man den enklaste metoden HEAD. HEAD metoden är idempotent, dvs enligt HTTP protokollet får den inte ha några sidoeffekter och ger samma resultat oavsett hur många gånger den anropas.

><a name="UM2"></a> **Regel UM-2** Uppslagning av information om en beständig identifierare SKALL ske via HTTP HEAD metoden där svaret ges i form av HTTP headrar.

### Uppslagning av informationsresurser

För att få tag på själva informationsresursen använder man metoden GET, även den metoden är idempotent. Notera att GET metoden är en utvidgning av HEAD metoden då information om den beständiga identifieraren också inkluderas.

><a name="UM3"></a> **Regel UM-3:** Uppslagning av en informationsresurs SKALL ske via HTTP GET och i sin enklaste form besvaras den med statuskod 200 och en representation av informationsresursen.

``` HTTP-nolint
GET /foo HTTP/1.1
Host: org1.se
---
HTTP/1.1 200 OK
Content-Type: text/html

[representation i HTML]
```

En informationsresurs kan ha flera olika likvärdiga representationer som särskiljer sig endast genom användning av olika format och språk. Vilken representation man får avgörs av vad man ber om i HTTP GET anropet, detta kallas server-driven content negotiation.

><a name="UM4"></a> **Regel UM-4:** HTTP headrar som `Accept`, `Accept-Language` och `Accept-Encoding` BÖR användas för att indikera vilken representation av en informationsresurs man är intresserad av. HTTP svar BÖR ange tillgängliga alternativa representationer med hjälp av HTTP link headern och relationen alternate. Länkarna särskiljs mha länk-attributet type för format och hreflang för språk.

``` HTTP-nolint
GET /foo HTTP/1.1
Host: org1.se
Accept: application/pdf
---
HTTP/1.1 200 OK
Content-Type: application/pdf
Link: 
  </foo>; rel="canonical"; type="text/html",
  </foo>; rel="alternate"; type="application/pdf"

[representation i pdf]
```

Ibland förändras informationsresursers uttryck över tiden. Det kan vara så att man byter ut system eller uppdaterar ett API på ett sätt som inte är bakåtkompatibelt. I de fallen räcker inte den traditionella content negotioation mekanismen till. I den situationen finns det en komplementär mekanism som kallas content-negotiotion by profile. Mekanismen innebär att man utöver formatet definierar upp ett antal profiler som man kan efterfråga. Observera att varje profil kan finnas i flera format, t.ex. version 2 av ett API finns både i XML och JSON.

><a name="UM5"></a> **Regel UM-5:** Accept-Profile headern KAN användas för att särskilja mellan representationer motsvarande olika profiler. Tillgängliga representationer anges mha. link header men i detta fall inkluderas även link-attributet profile.

``` HTTP-nolint
GET /foo HTTP/1.1
Host: org1.se
Accept: application/json
Accept-Profile: <https://dataportal.se/specifications/grillplatser/1.0>
---
HTTP/1.1 200 OK
Content-Type: application/json
Content-profile: <https://dataportal.se/specifications/grillplatser/1.0>
Link:
  </foo>;
    rel="canonical";
    type="application/json";
    profile="https://dataportal.se/specifications/grillplatser/2.0",
  </foo>;
    rel="alternate";
    type="text/xml"
    profile="https://dataportal.se/specifications/grillplatser/2.0",
  </foo>;
    rel="alternate";
    type="application/json";
    profile="https://dataportal.se/specifications/grillplatser/1.0",
  </ir/foo>;
    rel="alternate";
    type="text/xml"
    profile="https://dataportal.se/specifications/grillplatser/1.0",

[representation i json enligt version 2.0 av specifikationen för grillplatser]
```
Ibland behöver man kunna länka till en viss representation av en informationsresurs utan att förlita sig på content negotiation. I det läget kan det vara lämpligt att introducera en eller flera ytterligare identifierare för informationsresursen som är knutna till en viss representation. I det läget är det viktigt att knyta samman den beständiga identifieraren med de mer specifika identifierarna. Detta åstadkommer man genom att man använda temporära redirect från den beständiga identifieraren till den mest lämpliga identifieraren baserat på content negotiation. Samma teknik, dvs temporära redirects, kan användas för att särskilja beständiga identifierare från identifierare i en specifik implementationen som man vill markera har en kortare livstid.

><a name="UM6"></a> **Regel UM-6:** För en informationsresurs KAN uppslagning av en beständig identifierare ske i fler steg via användning av temporära redirect till andra informationsresurser med statuskod 307 och en location header. Vilken identifierar som ges i location headern kan bero på de accept headrar som givits i anropet, fler alternativa informationsresurser anges med fördel i link headrar med relationen alternate.

``` HTTP-nolint
GET /ir/foo HTTP/1.1
Host: org1.se
Accept: application/json
---
HTTP/1.1 307 OK
Location: /api/grillplatser/2.0/foo.json
Link:
  </api/grillplatser/2.0/foo.json>;
    rel="canonical";
    type="application/json";
  </api/grillplatser/2.0/foo.xml>;
    rel="alternate";
    type="text/xml"
```

### Uppslagning av företeelser

När det gäller uppslagning och ting motsvarande företeelser i den fysiska världen eller sinnesvärlden finns ingen fullständig representation, istället kan man peka ut en informationsresurs som motsvarar en beskrivning. Det vill säga, uppslagning som görs via HTTP HEAD eller GET ger en redirect 303 "see other" till en informationsresurs.

><a name="UM7"></a> **Regel UM-7:** Uppslagning av en företeelse SKALL ske med en HTTP HEAD eller GET som besvaras med en HTTP redirect med status 303 som pekar ut en informationsresurs via location headern. Informationsresursen SKALL motsvara en beskrivning av företeelsen.

``` HTTP-nolint
GET /foo HTTP/1.1
Host: org1.se
---
HTTP/1.1 303 See Other
Location: /bar
Link:
  </bar>; rel=describedby; format=text/turtle,
```

Eftersom företeelser inte är digitala kommer vi aldrig kunna tillhandahålla uttömmande beskrivningar av dem. Dvs. det är naturligt att det kan finnas flera olika konkurrerande eller kompletterande beskrivningar för samma företeelse. Den beskrivning som pekas ut per default (via en 303 redirect) av uppslagningsmekanismen kallas kanonisk och bestäms av aktören som äger den beständiga identifieraren. Ytterligare beskrivningar kan pekas ut via link headers.

><a name="UM8"></a> **Regel UM-8:** Om ytterligare beskrivningar av en företeelse utöver den kanoniska beskrivningen är kända av aktören som äger den beständiga identifieraren BÖR de pekas ut via link headrar med relationen describedby.
``` HTTP-nolint
GET /foo HTTP/1.1
Host: org1.se
---
HTTP/1.1 303 See Other
Location: /bar
Link:
  </bar>; rel=describedby; format=text/turtle,
  <https://org3.se/17>; rel=describedby; format=application/rdf+xml
  <https://org4.se/4711>; rel=describedby; format=application/rdf+xml
```

Det är naturligt att mängden beskrivningar av en företeelse ändras över tiden. Att hålla koll på vilka beskrivningar som finns utöver den kanoniska beskrivningen kan vara svårt. Eftersom aktörer som ansvarar för beständiga identifierare inte har något ansvar för att hålla en sådan förteckning aktuell kan det vara lämpligt att delegera. Delegationen sker genom att peka ut en alternativ identifierare för företeelsen i en annan uppslagningstjänst som specialiserar sig på att hålla ordning på beskrivningar.

><a name="UM9"></a> **Regel UM-9:** För att hitta fler beskrivningar KAN man peka ut en alternativ identifierare i en tjänst som specialiserar sig på att hålla reda på beskrivningar. Utpekningen SKALL då ske med hjälp av en link header med relationen alternate, omvänt SKALL tjänstens identifierare peka tillbaka på den ursprungliga beständiga identifieraren med en link header med relationen canonical.

``` HTTP-nolint
# Exempel UM-9
GET /foo HTTP/1.1
Host: org1.se
---
HTTP/1.1 303 See Other
Location: /bar
Link:
  </bar>; rel=describedby; format=text/turtle,
  <https://lookup.org2.se/bi?url=http%3A%2F%2Forg1.se%2Ffoo>; rel=alternate

## Uppslagning i utpekad tjänst som har koll på fler beskrivningar
GET bi?url=http%3A%2F%2Forg1.se%2Ffoo HTTP/1.1
Host: lookup.org2.se
---
HTTP/1.1 303 See Other
Location: https://org1.se/ir/9
Link:
  <http://org1.se/bar>; rel=canonical,
  <https://org3.se/17>; rel=describedby; format=application/rdf+xml
  <https://org4.se/4711>; rel=describedby; format=application/rdf+xml
```
Observera att denna användning av link header med relationen alternate gäller bara för företeelser, dvs när status är 303. Därmed krockar den inte med den användning som beskrivs i regel UM-6 som gäller för informationsresurser (allt annat än HTTP status 303). 

### Livscykelhantering av beständiga identifierare
Grundiden med beständiga identifierare är att de om möjligt ska vara permanenta, samtidigt är det viktigt att kunna hantera när förändring ändå sker. Beständiga identifierare som inte längre används ska markeras som så och de får inte heller återanvändas.

><a name="UM10"></a> **Regel UM-10:** Uppslagning av beständiga identifierare som upphört existera SKALL besvaras med HTTP status kod 410. En beständig identifierare får inte återanvändas för att referera till ett annat ting.

``` HTTP-nolint
GET /foo HTTP/1.1
Host: org1.se
---
HTTP/1.1 410 GONE
```

Det finns situationer när flera identifierare skapats för samma ting, i de fallen är det lämpligt att permanent peka om den beständiga identifieraren till en annan beständig identifierare. Den beständiga identifierare man pekar ut kan vara skapad av samma eller av en annan aktör.  

><a name="UM11"></a> **Regel UM-11:** Uppslagning av beständiga identifierare som har bytts ut SKALL besvaras med HTTP status kod 308 där location headern används för att peka ut den nya beständiga identifieraren.
``` HTTP-nolint
# Exempel UM-11
GET /foo HTTP/1.1
Host: org1.se
---
HTTP/1.1 308 Permanent Redirect
Location: https://org2.se/bar
```

Slutligen är det viktigt att kunna särskilja de fall när ting delas upp eller slås samman med andra ting från de fall när ting tas bort (UM-10) eller byter beständiga identifierare (UM-11).

><a name="UM12"></a> **Regel UM-12:** Uppslagning av beständiga identifierare som refererar till ting som slagits samman med andra ting eller slagits isär till flera ting SKALL besvaras med status koden 300. När så är möjligt BÖR man också peka ut beständiga identifierare till de ting som innefattar det ursprungliga tinget via link headern successor-version.