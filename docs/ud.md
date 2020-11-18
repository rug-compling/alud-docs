# Cookbook UD

## Introductie

### Zoeken naar specifieke dependentierelaties

```xquery
//nsubj
```

De elementen in de XML representatie zijn dus de dependentierelaties
van UD. De bovenstaande query geeft dus alle elementen terug die als
onderwerp fungeren. De verdere informatie zoals de part-of-speech tag
wordt in de XML representatie als attribuut gerepresenteerd. Om te
zoeken naar subjecten die als `UPOS` de waarde `PRON` hebben kun je dus de
volgende query formuleren:

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

### Possessieven

Possessieven worden niet als zodanig in de Lassy annotatie herkend. In
de mapping naar UD wordt een poging gedaan om dit onderscheid alsnog
automatisch terug te vinden. Daarom kun je in het UD deel van de
annotatie makkelijk naar possesieven zoeken. In standaard UD notatie,
een possesief heeft de relatie `nmod:poss`. In de XML notatie zoeken we
dus naar het element nmod. De informatie dat het om `poss` gaat wordt
weergegeven als waarde van het attribuut `deprel_aux`:

```xquery
//nmod[@deprel_aux="poss"]
```
