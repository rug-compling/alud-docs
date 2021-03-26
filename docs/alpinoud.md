# Cookbook Alpino + UD

In deze sectie laten we een aantal voorbeelden zien waarbij de queries naar zowel de Lassy annotatie-laag verwijzen als naar de UD laag. Sommige informatie is in de ene annotatielaag makkelijker of explicieter gecodeerd, zodat zo'n eclectische aanpak soms voordelen heeft.

## Zoeken naar nominalisaties van copula-constructies

Kun je koppelwerkwoorden met hun predicatieve complement nominaliseren? Jawel. In de volgende query gaan we op zoek naar een NP (die informatie heeft Lassy, maar is in UD niet direct beschikbaar). Van het hoofd van de NP eisen we dat die een `cop` dependency relatie heeft met zijn hoofd. Omdat we nu dus de UD annotatielaag bevragen via de Lassy annotatielaag kunnen we niet werken met iets als `/cop` maar moeten via `/ud` het attribuut `deprel` gebruiken:

```xquery
//node[@cat="np" and node[@rel="hd"]/ud[@deprel="cop"]]
```

## Regen, sneeuw, storm en hagel

In de volgende query gaan we op zoek naar de zogenaamde `weather verbs`. In UD kunnen we hiertoe refereren aan de `expl` dependeny relatie. Die relatie is niet direct beschikbaar in de Lassy annotatielaag. De relatie `expl` wordt ook voor andere fenomenen gebruikt (inherente reflexieven, voorlopig onderwerp, voorlopig lijdend voorwerp), en die fenomenen zijn wellicht dan weer het makkelijkst uit te filteren door naar de Lassy annotatielaag te verwijzen. In ieder geval is dat wat in de volgende query gebeurt:

```xquery
//node[
    @pt="ww" and
    ud[not(@deprel_main="aux")] and
    ../node[
        @rel="su" and
        ud[@deprel_main="expl"] and
        not( @index = //node[@rel="sup"]/@index)
    ]
]
```

Volgens deze definitie blijkt ook het werkwoord `ontbreken` tot deze groep werkwoorden te horen, naast de "usual suspects" zoals `regenen`, `vriezen`, `sneeuwen`, `hagelen`, `motregenen`, `stormen`, `onweren`.

Mocht je je afvragen wat de rol is van ingewikkelde laatste regel met negatie van een index waarde, dan kun je dezelfde query stellen waarbij je de 'not' simpelweg verwijdert. Het blijkt dan te gaan om voorlopige onderwerpen die in raising-to-subject gevallen als "gewoon" onderwerp fungeren van raising-to-subject werkwoorden (schijnen, laten, ...).

## Voorzetseluitdrukkingen

Voorzetseluitdrukkingen zoals `in plaats van` worden in de Lassy annotatielaag als multi-word-unit gecodeerd, maar daarbij is vervolgens niet helemaal duidelijk of ze
als voorzetsel optreden. In de volgende query gebruiken we de `case` relatie in de UD-annotatielaag hiervoor:

```xquery
//node[@cat="mwu" and node[@pt="vz"] and node/ud[@deprel_main="case"]]
```

## Opsporen van inconsistenties

Voor specifieke doeleinden is het soms interessant om te zien of er onverwachte verschillen zijn tussen de Lassy-laag en de UD-laag.

Bij deze query vinden we dus alle woorden, en tellen vervolgens de combinaties van `pt` en `upos`.

```xquery
//node[@pt]
```

Meer specifiek kunnen we bijvoorbeeld zoeken naar alle determiners, en dan tellen welke `pt` of `postag` daarmee samengaan:

```xquery
//node[ud[@upos="DET"]]
```

Het UD-label `advmod` suggereert dat de modifier een bijwoord moet zijn. Is dat altijd zo?

```xquery
//node[ud[@deprel="advmod"] and not(@pt="bw")]
```
Als je vervolgens telt op `pt` ben je mogelijkerwijs verrast door het optreden van de `tsw`. Dus die bekijken we vervolgens wat beter:
```xquery
//node[ud[@deprel="advmod"] and @pt="tsw"]
```

