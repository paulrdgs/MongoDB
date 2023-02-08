

## Exercice 1

Un bref examen de vos fichiers journaux a révélé que la plupart des requêtes effectuées sur la collection salles cible des capacités ainsi que des départements, comme ceci :

```
db.salles.find({"capacite": {$gt: 500}, "adresse.codePostal": /^30/}) 
db.salles.find({"adresse.codePostal": /^30/, "capacite": {$lte: 400}}) 
```

Que proposez-vous comme index qui puisse couvrir ces requêtes ?

```javascript
db.salles.createIndex(
	{ "capacite": 1, "adresse.codePostal": 1 }, 
	{"name": "index_capacite_cp"}
);
```

Détruisez ensuite l’index créé.
```javascript
db.salles.dropIndex("index_capacite_cp");
```

