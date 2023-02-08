
## Exercice 1

Modifiez la collection salle afin que soient dorénavant validés les documents destinés à y être insérés ; cette validation aura lieu en mode « strict » et portera sur les champs suivants :

nom sera obligatoire et devra être de type chaîne de caractères.

capacite sera obligatoire et devra être de type entier (int).

Dans le champ adresse, les champs codePostal et ville, tous deux de type chaîne de caractères, seront obligatoires.

Reponse:
```javascript
db.createCollection("salles", { 
	validator: { 
		$and: 
		[ 
			{nom: { $type: "string", $exists: true } }, 
			{capacite: { $type: "int", $exists: true } }, 
			{"adresse.codePostal":{ $type:"string", $exists:true } }, 
			{ "adresse.ville": { $type: "string", $exists: true } } 
		] 
		} 
	}
)
// La validation utilise l'opérateur $and pour combiner ces règles en une seule expression, ce qui signifie que toutes les règles doivent être satisfaites pour qu'un document soit considéré comme valide et inséré dans la collection "salles".
```

Resultat: 
```javascript
{ ok: 1 }
```

Que constatez-vous lors de la tentative d’insertion suivante, et quelle en est la cause ?

```javascript
db.salles.insertOne( 
{"nom": "Super salle", "capacite": 1500, "adresse": {"ville": "Musiqueville"}} 
) 
```

Resultat: 
```javascript
MongoServerError: Document failed validation

// Lors de la tentative d'insertion ci-dessus, l'erreur indique que la validation de la collection a échoué. Cela est dû au fait que le champ "adresse.codePostal" n'a pas été spécifié et est donc manquant, ce qui est requis selon la validation définie pour la collection "salles".
```



Que proposez-vous pour régulariser la situation ?

Reponse: 
```javascript 
// Pour régulariser la situation, il faudrait ajouter le champ manquant "adresse.codePostal" à l'objet à insérer, de manière à ce qu'il réponde aux exigences de validation définies pour la collection "salles". La requête d'insertion pourrait alors ressembler à ceci :

db.salles.insertOne( {"nom": "Super salle", "capacite": 1500, "adresse": {"ville": "Musiqueville", "codePostal": "00000"}} )
```

Resultat: 
```javascript
{
	acknowledged: true,
	insertedId: ObjectId("63e35931f48ed97edfe93408")
}
```