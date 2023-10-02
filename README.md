# Profil för Beständiga Identifierare

Hantering av beständiga identifierare kan ske på många sätt. Denna profil motsvarar en syn på beständiga identifierare som ligger i linje med [webbens arkitektur](https://www.w3.org/TR/webarch/). I praktiken innebär det att man förlitar sig på URI:er, DNS och HTTP protokollets olika headrar.

Att följa profilen innebär att man beaktat alla de SKALL/BÖR/KAN regler som specificerats, mer specifikt:

>**SKALL** - Dessa regler måste följas, annars har man inte följt profilen.<br>
>**BÖR** - Dessa regler är starkt rekommenderade, om man inte följer dem bör man ha en stark gärna dokumenterad anledning.<br>
**KAN** - Dessa regler är valfria, om man har ett behov som matchar regelns syfte bör man följa regeln istället för att hitta på en egen lösning.

## Profilen består av följande delar
Utöver formella regler består profilen också av ett antal vägledande mönster för hur beständiga identifierare ska slås upp. Därtill finns också ett antal dokument av mer stödjande karaktär, nedan listas profilens material i en föreslagen läsordning:

1. [Bakgrund / behovsbild](docs/bakgrund.md)
2. [Regler beständiga identifierare och uppslagning](docs/regler.md) ⇐ **den formella specifikationen**
3. [Vägledande mönster - uppslagning informationsresurser](docs/uppslagning_informationsresurser.md)
4. [Vägledande mönster - uppslagning företeelser](docs/uppslagning_foreteelser.md)
5. [Definitioner / begreppslista](docs/begreppslista.md)
6. [Rekommendationer / praxis](docs/rekommendationer.md)
7. [Användarfall](docs/anvandarfall.md)
8. [Användning i API:er](docs/api.md)
9. Uppslagning av alternativa beskrivningar


## Motivation
För att undvika begränsningar på det som ska identifieras talar vi om det som ska identifieras som *ting*. Ting kan vara fastigheter, personer, begrepp, digitala dokument osv. Se [begreppslistan](docs/begreppslista.md) för mer information om den vokabulär som används.

Rekommendationerna är i huvudsak riktade till de som bygger system som tillgängliggör information om ting för en större krets på webben som nedladdningsbara filer eller via API:er. Särskilt viktiga är rekommendationerna för de som bygger system som ska samverka med andra system / aktörer.

Det finns i huvudsak tre fördelar med att använda beständiga identifierare för olika ting:

1. Konsumenter vill kunna referera till ting på ett hållbart sätt.
2. Producenter vill kunna samordna information om ting mellan aktörer.
3. Maskiner kan instrueras att kommunicera om ting på ett väldefinierat och automatiserat sätt.

## Avgränsningar
Med beständiga identifierare avser vi identifierare som är globalt unika och har en välkänd uppslagningsprocess. Identifierare som enbart är unika inom ett visst verksamhetssystem eller kräver domänspecifik kunskap för att slå upp är inte fokus i denna specifikation.

## Arbetsprocess
Profilen har tagits fram som en förvaltningsgemensam specifikation för beständiga identifierare. Arbetet sker inom byggblock metadata som leds av [Myndigheten för digital förvaltning (DIGG)](https://www.digg.se).

Se [separat dokument](process/index.md) för mer information om möten, referensgrupp osv.

## Hur man ger återkoppling

- Skapa nya ärenden
- Kommentera på existerande
- Skapa pull-requests för konkreta ändringar
- Du kan även mejla till [info@digg.se](mailto:info@digg.se)

