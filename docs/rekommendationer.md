# Rekommendationer och praxis

## 1. Förslag på utformning av beständiga identifierare 
Reglerna BI-1 till BI-7 ger förhållningsregler om vilka URL:er som är lämpliga för beständiga identifierare, t.ex. att man inte skall använda parametrar, versionsnummer eller fragment. Men reglerna tillhandahåller inte en mall för hur man ska utforma sina URL:er i mer detalj. Detta är medvetet då det finns få objektiva kriterier som man kan hänvisa till som gör att den ena utformningen är bevisbart bättre än den andra. Detta gör att det finns en uppsjö med förslag av hur URL:er ska utformas, se t.ex. den genomgång som görs i [den studie som ligger till grund för SEMICS 10 rules for Persistent URIs](https://joinup.ec.europa.eu/collection/semic-support-centre/document/10-rules-persistent-uris).

### Huvudalternativet - samma domän som resten av webben
I kapitel 4.1 i studien rekommenderas en URL design enligt nedan. Det är samma rekommendation som också förespråkas i Sverige i kapitlet ["Använd beständiga identifierare"]((https://digg.se/kunskap-och-stod/oppna-och-delade-data/offentliga-aktorer/vagledning-for-att-tillgangliggora-information#h-Anvandbestandigaidentifierare)) i vägledningen för att tillgängliggöra information hos DIGG.

>http(s)://{domän}/{typ}/{koncept}/{referens}

För att förstå detta förslag behöver vi kort gå igenom med vad som menas med typ, koncept och referens.

Med typ menas en sträng som motsvarar en grov kategorisering av något slag som passar organisationen. Ett förslag som rimmar bra med internationella förslag är att särskilja mellan företeelser, informationsresurser, begrepp (definitioner) och byggstenar till informationsmodeller i form av klasser och egenskaper (terms):

**id** (identitet) - för företeelser.<br>
**res** (resurs) - för informationsresurser.<br>
**def** (definition) - för definitioner i form av begrepp, klassificeringar, kodlistor etc.<br> 
**spec** (specifikation) - för byggblock i formella modeller som klasser, egenskaper och applikationsprofiler.<br>

Observera att både definitioner och specifikationer är företeelser och kan läggas under id om man vill ha färre typer.

Med koncept menas en gruppering / samling av referenser som har liknande karaktär, vi exemplifierar (påhittade):

**https://lm.se/id/fastighet/123** - en företeelse i form av en fastighet.<br>
**https://lm.se/res/fastighet/123** - informationsresurs som beskriver en fastighet.<br>
**https://scb.se/def/SNI2007/01.131** - definitionen av en potatisodlare enligt Svenskt NäringslivsIndex från 2007.<br>
**https://bv.se/spec/CBV-SE/Business** - en applikationsprofil som beskriver vilka egenskaper från EUs Core Business Vocabulary som används i Sverige för att beskriva ett företag.

Det är viktigt att komma ihåg att de förslagna typerna ovan (id, res, def och spec) bara är exempel, det finns gott om exempel på andra typer (t.ex. id och doc eller terms). Det är också möjligt att skapa djupare strukturer om man har ting som är organiserade i en naturlig hierarki. T.ex. så kanske man vill exponera att potatisodlare ligger inom avdelningen för "Företag inom jordbruk och skogsbruk" som betecknas med A, då blir adressen istället
**https://scb.se/def/SNI2007/A/01.131**.

### Alternativ 2 - subdomän
Hantering av beständiga identifierare i huvudalternativet som en del av den vanliga webben involverar potentiellt två existerande tekniska komponenter.
1. Webbserver - t.ex. Apache, Nginx, IIS etc.
2. CMS - t.ex. EpiServer, Wordpress, SiteVision etc.

Det går utmärkt att konfigurera webbservrar så att den CMS man har inte berörs alls. Men, det innebär alltid att man reserverar en eller flera namnrymder som CMS:en hädanefter inte kan använda sig av (t.ex id, res, def och spec).
Om man väljer att ha många typer kan detta bli ett problem. Ett alternativ är att istället förlita sig på subdomäner. T.ex:

>http(s)://{typ}.{domän}/{koncept}/{referens}

För att åstadkomma detta måste man lägga in konfigurationer i sin DNS för varje typ. Dessa konfigurationer pekar ut vilken webbserver som ska hantera dessa anrop. Denna webbserver kan i princip vara samma som hanterar resten av webben, fördelen är man minskar risken med sammanblandning med CMS:en då det formellt sett är en helt separat domän.

Välj detta alternativ om det är viktigt att inte störa den hantering av webben som redan är etablerad inom organisationen. 

## 2. När är det motiverat att skapa egna beständiga identifierare
Huvudregeln är att man ska undvika att skapa nya egna identifierare när en annan organisation redan skapat beständiga identifierare för ett ting. Med det sagt finns det situtationer som kan motivera att man skapar egna beständig identifierare.

1. Existerande identifierare följer inte god praxis, t.ex. genom att använda sig av frågeparametrar.
2. Du har skäl att tro att den egna organisationen är en mer naturlig auktoritet för tinget, t.ex. lantmäteriet är en bättre auktoritet för fastigheter än en kommun, Kungliga bibliotek är en bättre auktoritet för böcker än ett bokförlag osv. En bättre auktoritet kan man vara om t.ex:
   1. Man kan garantera identifieraren i ett längre perspektiv.
   2. Man tillhandahåller en rikare mängd information om tinget.
   3. Man tillhandahåller en mer generell / objektiv / representativ mängd information om tinget, detta perspektiv bör kunna bekräftas av oberoende parter.
3. Tinget förvaltas av den egna organisationen som en del i en samling som är mer komplett än den samling där den existerande beständiga identifieraren ingår.

Om man väljer att skapa en egen beständig identifierare som konkurrerar med en existerande identifierare enligt 1-3 ovan bör man informera den andra parten med förhoppningen om att man kan få en samsyn inklusive en ambition om att fasa ut de gamla identifierarna med tiden. I den bästa av alla världar innebär det ett minskat förvaltningsansvar för den andra parten och därmed tas emot positivt.

Notera också att i många fall kan man undvika hela problemet genom att se det som att man talar om olika ting. T.ex. folkbokföringsadress och sjukskrivning kan ses som egenskaper på en person, men är i praktiken bättre representerade som separata ting med egna beständiga identifierare med kopplingar till samma person (via dess beständiga identifierare).

## 3. Alternativ till organisationsnamn i beständiga identifierare
I [SEMICS 10 rules for Persistent URIs](https://joinup.ec.europa.eu/collection/semic-support-centre/document/10-rules-persistent-uris) är den första regeln "Avoid stating ownership", se 4.2.1. Orsaken är att man vill minska risken för brutna länkar om organisation eller projekt ändras. Denna regel är inte medtagen i denna profil den huvudsakliga målgruppen för denna profil är offentlig sektor i Sverige som genom design har en viss beständighet. Man bör också fundera på vilken nivå av beständighet man siktar på, som rapporten säger så är den bästa garanten för beständighet att de beständiga identifierarna används.

Det finns två huvudsakliga alternativ till organisationsnamn i URL:er.

### Sektorsvisa domäner för beständiga identifierare
Om det finns / kan etableras sammarbete inom en sektor där det finns en aktör som kan ta på sig ett samordningsansvar för hantering av beständiga identifierare så kan det vara en bra lösning.

Det kan leda till att man får hög kvalitet på hanteringen av beständiga identifierare där reglerna i denna profil följs. Med fördel stöds detta av tekniska system som gör att olika aktörer kan administrera sina beständiga identifierare på ett oberoende sätt. Manuell hantering via en central funktion är sannolikt inte en bra lösning då det risker att bromsa införandet av beständiga identifierare.

Det är dock viktigt att notera att man här frångår en av internets styrkor om decentralisering. En central lösning skapar potentielt en sårbarhet i form av en flaskhals som, om den får tekniska problem / utsätts för attacker, kan göra att en hel sektor drabbas.

### Ämnesspecifika domäner för beständiga identifierare
Det är välkänt att ett av de svåraste problemen inom datavetenskap är hitta bra namngivning. Att för varje behov hitta på en ny domän som lämpar sig för att fånga upp vad som innefattas både idag och på lite sikt är svårt.

Om det kan göras innebär det att man har löskopplat den beständiga identifieraren från den förvaltande organisationen, vilket kan vara bra på sikt. Det kan också vara bra i det korta perspektivet om man vill markera att beständiga identifierare inte är hårt knutna till en specifik myndighet, t.ex. olika portaler. Dataportal.se och geodataportalen.se innefattar information från många olika delar av samhället, inte bara DIGG och Lantmäteriet som har förvaltningsansvar.

Det finns också nackdelar med att koppla isär beständiga identifierare från organisationerna som förvaltar dem, t.ex. hur kan jag vara säker på att den som står bakom https://algdata.se verkligen är Länsstyrelsen utan att göra en djupare analys. Man ska inte förkasta den trygghet som våra grundläggande institutioner står för bara för att undvika ett problem som kanske inte kommer uppkomma på överskådlig tid.

Man bör också fundera på om det finns en skillnad i strategi mellan våra mest fundamentala och samhällsbärande myndigheter som t.ex. Skatteverket, Lantmäteriet, KB, SCB och de många mindre myndigheter som har större risk för att stöpas om.

## 4. Markera gamla beständiga identifierare - tombstones
Enligt regel UM-10 så ska beständiga identifierare som tagits bort besvaras med HTTP status koden 410. Utöver detta kan HTTP svaret inkludera en representation med information om tinget borttagning, en tombstone.

Utöver tombstone informationen är det möjligt att tillhandahålla information om tingets borttagning på två sätt:
1. Datum när tinget tagits bort HTTP-headern `sunset`.
2. En länk till ett dokument som förklarar mer om bakgrunden / policy / föreslagen hantering kring borttagningen. Länken uttrycks som en link-header med `rel=sunset`.

Sunset information kan även tillhandahållas innan borttagning för att hjälpa konsumenter att anpassa sig i tid.
Läs mer om Sunset på [RFC-8594](https://www.rfc-editor.org/rfc/rfc8594.html)

## 5. Relationen mellan verksamhetsidentifierare och beständiga identifierare
När man skapar beständiga identifierare bör man ta hänsyn till de verksamhetsidentifierare som redan finns och använda dem i konstruktionen av en URL, typiskt genom att lägga till verksamhetsidentifieraren sist efter en basurl. Detta beskrivs i kapitlet [Natural keys](https://patterns.dataincubator.org/book/natural-keys.html) i Linked Data Patterns. Det motsvaras också av regeln "Re-use existing identifiers" i [SEMICS 10 rules for Persistent URIs](https://joinup.ec.europa.eu/collection/semic-support-centre/document/10-rules-persistent-uris).