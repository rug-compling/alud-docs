# Cookbook UD

## Introductie

In deze paragraaf gaan we uit van het scenario dat je de UD annotatie wilt bevragen, zonder hierbij ook de hiërarchie van de Lassy annotatielaag te betrekken. We gebruiken dan de informatie die onder het element <root> is gerepresenteerd. Omdat de dependentie-labels als element worden gebruikt, en de dependentie-graaf als boom wordt gerepresenteerd zijn een aantal voor de hand liggende queries erg eenvoudig.

### Zoeken naar specifieke dependentierelaties

De volgende query identificeert een 'nsubj' element, waar dan ook in het document:

```xquery
//nsubj
```

De elementen in de XML representatie zijn dus de dependentierelaties
van UD. De bovenstaande query geeft dus alle elementen terug die als
onderwerp fungeren. De verdere informatie zoals de part-of-speech tag
wordt in de XML representatie als attribuut gerepresenteerd. Om te
zoeken naar subjecten die aan bepaalde eisen voldoen, bijvoorbeeld dat de `UPOS` de waarde `PRON` moet hebben, kun je 
attributen met de gewenste waarde in de query toevoegen:

```xquery
//nsubj[@upos="PRON"]
```

De dependentierelaties tussen woorden worden in de XML representatie
hierarchisch gerepresenteerd. Om te zoeken naar een woord dat als amod
van een onderwerp optreedt specificeer je:

```xquery
//nsubj/amod
```

En je kunt dus ook verder gaan en zoeken naar woorden die als advmod
van een amod van een subject optreden:

```xquery
//nsubj/amod/advmod
```

### Attributen

Naast het attribuut `upos` zijn de volgende attributen voor elk woord in
de UD beschikbaar: `form`, `lemma`, de standaard UD attributen zoals
`Number`, `Tense`, `VerbForm`, `Person`, `PronType`, en de Lassy attributen voor
part-of-speech zoals `pt`, `postag`, `getal`, `naamval`, `persoon`, ...


Je kunt dus onderzoeken of er gevallen zijn waarbij de UD postag
afwijkt van de Lassy postag. Bijvoorbeeld:

```xquery
//*[@upos="PRON" and not(@pt="vnw")]
```

of

```xquery
//*[@pt="vnw" and not(@upos="PRON")]
```

Ten slotte heeft elk woord nog het attribuut `ud` met als mogelijke
waarden `basic` of `enhanced`. Dit attribuut kun je gebruiken om duidelijk
te maken dat je in de standaard (basic) dependentiestructuur moet
zoeken, of dat je in de enhanced dependentiestructuur moet zijn.

### Ondergespecificeerde dependentierelaties

Je kunt ook naar willekeurige relaties zoeken door gebruik te maken
van de `*` wildcard. Om te zoeken naar een specifiek woord, zonder dat
je eisen stelt aan de relatie waarin dat woord voorkomt, kun je dit
doen:

```xquery
//*[@lemma=("eten","op_eten")]
```

Natuurlijk kun je vervolgens ook alle objecten van zo’n woord vinden.
Bijvoorbeeld, welke dingen worden zoal gegeten:

```xquery
//*[@lemma=("eten","op_eten")]/obj[@upos="NOUN"]
```

En wat zit er dan op dat brood:

```
//*[@lemma="brood"]/nmod[@deprel_aux ="met"]
```

Je kunt de wildcard combineren met de kracht van XPath door gebruik te
maken van de functie name(). De functie name() verwijst naar de naam
van het element (dus de dependentierelatie). Zo kun je zoeken naar
alle dependentierelaties behalve nmod:

```xquery
//*[not(name() = "nmod") ]
```

Of naar ofwel nmod ofwel amod:

```xquery
//*[name() = "nmod" or name() = "amod" ]
```

Dit kun je ook zo opschrijven:

```xquery
//(nmod|amod)
```

Merk op dat inmiddels de naam van de dependentie ook beschikbaar is via de attributen `deprel` en `deprel_main`.


### Possessieven (gestructureerde dependency labels)

Dependency labels worden weergegeven met het attribuut `deprel`. In UD worden de 
labels soms getypeerd met een suffix `:type`. Om op de relatie dan wel op het
subtype te kunnen zoeken gebruiken we hiervoor de twee attributen `deprel_main` en 
`deprel_aux`.

Possessieven worden niet als zodanig in de Lassy annotatie herkend. In
de mapping naar UD wordt een poging gedaan om dit onderscheid alsnog
automatisch terug te vinden. Daarom kun je in het UD deel van de
annotatie makkelijk naar possesieven zoeken. In standaard UD notatie,
een possesief heeft de relatie `nmod:poss`. In de XML notatie zoeken we
dus naar het element nmod. De informatie dat het om `poss` gaat wordt
weergegeven als waarde van het attribuut `deprel_aux`. We hebben dus de volgende
mogelijkheid om possesieven te zoeken:

```xquery
//nmod[@deprel="nmod:poss"]
```

Maar omdat het subtype `poss` alleen bij `nmod` lijkt voor te komen, kunnen we ook 
volstaan met de volgende query:

```xquery
//nmod[@deprel_aux="poss"]
```

Als je álle nmod relaties wilt vinden, en de sub-typering dus wilt negeren kan dat
met het attribuut `deprel_main`:

```xquery
//nmod[@deprel_main="nmod"]
```

### Copula

Welke woorden treden op als copula? Omdat in UD het predicatief complement het hoofd is, en het koppelwerkwoord selecteert, verwacht je voor deze query dus de koppelwerkwoorden te vinden:

```xquery
//cop
```

De query die juist de predicatieve complementen selecteert is dan: een woord met
een willekeurig label (vaak `root`) dat een copula selecteert:

```xquery
//*[cop]
```

### Wat doen ministers de hele dag?

Geef de werkwoorden waarbij `minister` als onderwerp fungeert:

```xquery
//root[@upos="VERB" and nsubj[@lemma="minister"]]
```

Voor een corpus waarop wij deze query uitprobeerden waren de meest frequente lemma's: `willen`, `zeggen`, en `beloven`...

### Wat doe je in Groningen?

Geef de werkwoorden waarmee "in Groningen" voorkomt:

```xquery
//*[@upos="VERB" and *[@lemma="Groningen"]/case[@lemma="in"]]
```

### Intensifiers

Bijwoorden die adjectieven modificeren:

```xquery
//*[@upos="ADJ"]/advmod[@upos="ADV"]
```

Bij het make van dit voorbeeld vroeg ik me af met welke andere dependenties bijwoorden hier voor zouden kunnen komen.
Dat kun je bevragen met deze query:

```xquery
//*[@upos="ADJ"]/*[@upos="ADV" and not(@deprel="advmod")]
```

Dat blijkt dus een heel gemengd gezelschap te zijn.






