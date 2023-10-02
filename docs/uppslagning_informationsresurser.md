# Vägledande mönster för uppslagning av informationsresurser

## Mönster 1 - Enkel uppslagning av informationsresurs

En klient frågar efter en informationsresurs via dess beständiga identifierare. Det finns bara en representation av informationsresursen (ett format) och det levereras till klienten.

## Mönster 2 - Content negotiation

En klient frågar efter en informationsresurs men informerar att den föredrar vissa representationer (format) före andra. Servern har flera olika representationer och svarar med den representation som bäst motsvarar det klienten ber om.
Alla de olika möjliga representationerna indikeras via link headrar med format angivelse.

## Mönster 3 - Content negotiation by profile

En klient vet att utöver ett visst format och språk också föredrar en viss version av ett format, t.ex. det som leverades av en tidigare version av API:et. Då servern eftersträvar att vara bakåtkompatibel finns förmågan att svara i gamla format kvar och klienten får en äldre version av representationen, trots att det är samma format (typiskt JSON eller XML).
Alla de olika möjliga representationerna indikeras via link headrar med både format och profile angivelse.

## Mönster 4 - Stabil adress till en informationsresurs som flyttas runt

En klient ber om en informationsresurs, över tid har informationsresursen flyttat runt mellan olika tekniska lösningar. Dess beständiga identifierare är densamma och i varje läge besvaras uppslagningen med en HTTP status kod 307 med en location header som pekar ut var informationsresursen befinner sig för närvarande.  