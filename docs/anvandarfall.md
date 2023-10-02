# Användarfall

## Svensk bolagsinformation
Separation mellan beständiga identifierare för företag och API anrop.

``` HTTP-nolint
GET http://api.bv.se/foretag?name=volvo
—
Status: 200
Entity-body:
{
  size: 3,
  offset: 0,
  Results: [
    {
      @context: "https://nordic.org/company.jsonld",
      @id: http://id.bv.se/foretag/567,
      describedBy: http://api.bv.se/foretag/v3/567
      namn: "Volvo AB",
      registrationDate: "1911-01-01",
    }
    …
  ]
}
```

## Svensk bolagsinformation länkar in begrepp från SCB
Företag klassificeras med hjälp av begrepp från SCB. Begreppen har beständiga identifierare som ligger på scb.se, viss information om begreppen cachas hos bolagsverket, men de ursprungliga beständiga identifierare bevaras. Begreppen är inte primära ting i den meningen att de enbart finns som filter på de primära tingen (företagen). Det innebär att det inte finns något API anrop för att få ut ett SCB begrepp från Bolagsverkets API, däremot följer de med som del av företagsinformationen.

## Nordisk bolagsinformation via nationella API:er
Företag från hela norden kan återfinnas i nationella API:er.
De beständiga identifierarna i olika domäner (t.ex. bolagsverket.se och bbreg.no) bevaras. Svenskt API använder de norska beständiga identifierarna. Det finns API anrop som levererar information om norska beständiga identifierare, både som sökträffar och egna API adresser till beskrivningar av de norska företagen. 

## Nordiskt centralt API för bolagsinformation
Centralt API använder de nationella beständiga identifierarna för företagen. Det centrala API:et har myntat egna beständiga identifierare.