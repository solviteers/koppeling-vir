# koppeling-vir
Documentatie van de koppeling verwijsindex risicojongeren (VIR) op basis van StUF zkn0310

## StUF elementen zonder waarde (xsi:nil="true")
Het komt regelmatig voor dat een xml-element verplicht is, maar dat de waarde ervan onbekend is.
In deze situaties wordt het attribuut `xsi-nil="true"` gebruikt, meestal in combinatie met het attribuut `noValue`.
Het onderstaande overzicht beschrijft hoe het ontvangende systeem zo'n element moet interpreteren.

- `noValue="geenWaarde"` betekent: Dit veld heeft geen waarde. Heeft dit veld in jouw applicatie wel een waarde, dan moet je het leegmaken;
- `noValue="waardeOnbekend"` betekent: De waarde van dit veld is onbekend. "Omdat ik het simpelweg niet weet, of omdat ik het je niet vertel." Je mag dit element dus negeren. Heeft dit veld in jouw applicatie een waarde dan zou die best juist kunnen zijn, je mag die waarde gewoon laten staan.;
- het attribuut `noValue` ontbreekt betekent: "Geen idee wat de status van de waarde in dit element is. Doe ermee wat je wil". De veiligste oplossing: negeer dit element.

## Mutaties
Het versturen van mutaties binnen de VIR-keten werkt anders dan gebruikelijk binnen de StUF-standaarden.
De VIR verstuurt geen notificaties of mutatieberichten met daarin de gewijzigde gegevens.
Bij een mutatie wordt de volledige actuele set met gegevens aangeboden.
Hierdoor is het voor ontvangende applicaties (op basis van het bericht) niet te zien wat er gewijzigd is.
Om vast te stellen wat er gewijzigd is moet de applicatie het `zakLk01-bericht` vergelijken met de bekende gegevens in de applicatie.

### Mutatie of nieuw signaal?
Een mutatie of een nieuw signaal is te herkennen aan de combinatie van de elementen `//object/identificatie` en `//object[@sleutelVerzendend]`

| | `//object/identificatie` | `//object[@sleutelVerzendend]` | Toelichting |
| --- | --- | --- | --- |
| Nieuw signaal | <zak:identificatie stuf:noValue="geenWaarde" xsi:nil="true"/> | `stuf:sleutelVerzendend="[signaalsleutel/guid lokale instantie]"` | De explicite declaratie van `noValue="geenWaarde"` informeert de VIR-broker over het nieuwe signaal|
| Bevestiging registratie nieuw signaal in landelijke VIR | <zak:identificatie>[valide VIR-signaalnummer]</zak:identificatie> | `stuf:sleutelOntvangend="[signaalsleutel/guid lokale instantie]"` | De bevestiging vanuit de VIR-broker bevat de sleutel van het signaal in de lokale instantie zodat het VIR-signaalnummer gekoppeld kan worden aan het aangemaakte signaal |
| Mutatie | <zak:identificatie>[valide VIR-signaalnummer]</zak:identificatie> | | Mutaties bevatten alleen een VIR-signaalnummer, geen sleutel uit het lokale systeem. Mutaties worden geinitieerd vanuit de landelijke VIR, die heeft geen kennis van lokale sleutelnummers |


