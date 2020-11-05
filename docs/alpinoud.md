# Cookbook Alpino + UD

## Zoeken naar nominalisaties van copula-constructies

```text
//node[@cat="np" and node[@rel="hd"]/ud[@deprel_main="cop"]]
```

## Vind de *weer*-werkwoorden

```text
//node[
    @pt="ww" and
    ud[not(@deprel_main="aux")] and
    ../node[
        not(@rel="se" or  @rel="sup" or @rel="pobj1") and
        ud[@deprel_main="expl"]
    ]
]
```

## Voorzetseluitdrukkingen

```text
//node[@cat="mwu" and node[@pt="vz"] and node/ud[@deprel_main="case"]]
```
