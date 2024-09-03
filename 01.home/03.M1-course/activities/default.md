# Activités (TP)

## Les données

créez 3 fichiers CSV dans le répertoire data/triangles

`triangle1.csv`

```csv
0,0
1,0
0,1
```

`triangle2.csv`

```csv
1,5
0,1
1,0
```

`triangle3.csv`

```csv
0,0
1,0
0.5,0.86602540378
```

## Résultats attendus:

1. vous devez publier sur une queue jms nomée `M1.equilateral-USERNAME` (e.g., `M1.equilateral-nherabut`) au format JSON tous les triangles équilatéraux.

```json
{"a":{"x":0.0,"y":0.0},"b":{"x":1.0,"y":0.0},"c":{"x":0.5,"y":0.86602540378}}
```

1. vous devez publier sur une queue jms nommée `M1.autre-USERNAME` (e.g., `M1.autre-nherbaut`) au format XML tous les triangles non équilatéraux.
2. à partir des queues `M1.equilateral-USERNAME` et `M1.autre-USERNAME`, vous devez écrire des fichiers contenant le résultat du calcul du périmètre de ces triangles e.g.,

```json
{
    "perimetre": 3.41421356237
}
```
