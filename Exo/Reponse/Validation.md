
## Exercice 1

Modifiez la collection salle afin que soient dorénavant validés les documents destinés à y être insérés ; cette validation aura lieu en mode « strict » et portera sur les champs suivants :

nom sera obligatoire et devra être de type chaîne de caractères.

capacite sera obligatoire et devra être de type entier (int).

Dans le champ adresse, les champs codePostal et ville, tous deux de type chaîne de caractères, seront obligatoires.
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


## Exercice 2

Rajoutez à vos critères de validation existants un critère supplémentaire : le champ _id devra dorénavant être de type entier (int) ou ObjectId.

Reponse:
```javascript
// Pour ajouter ce critère de validation supplémentaire, vous pouvez utiliser la méthode `db.runCommand()` avec un document de commande contenant les options pour mettre à jour la validation de la collection

db.runCommand({ 
	collMod: "salles", 
	validationAction: "error", 
	validator: { 
		$and: [ 
			{nom: { $type: "string", $exists: true } }, 
			{capacite: { $type: "int", $exists: true } }, 
			{"adresse.codePostal":{$type:"string", $exists: true } }, 
			{ "adresse.ville": { $type: "string", $exists: true } }, 
			{ _id: { $type: ["int", "objectId"], $exists: true } } 
		] 
	} 
})
```

Resultat: 
```javascript
{
	ok: 1,
	'$clusterTime': {
		clusterTime: Timestamp({ t: 1675844248, i: 1 }),
		signature: {
			hash: Binary(Buffer.from("f559228ae979b8f92064a7dda0b9cb34ae56f0e4", "hex"), 0),
			keyId: Long("7171806562036482052")
		}
	},
	operationTime: Timestamp({ t: 1675844248, i: 1 })
}
```

Que se passe-t-il si vous tentez de mettre à jour l’ensemble des documents existants dans la collection à l’aide de la requête suivante :

```javascript
db.salles.updateMany({}, {$set: {"verifie": true}}) 
```

Resultat: 
```javascript
{
	acknowledged: true,
	insertedId: null,
	matchedCount: 1,
	modifiedCount: 1,
	upsertedCount: 0
}
// Si on exécute cette requête, tous les documents existants dans la collection "salles" seront mis à jour avec un nouveau champ "verifie" défini à "true".
```

Supprimez les critères rajoutés à l’aide de la méthode delete en JavaScript

Reponse:
```javascript
db.runCommand({ collMod: "salles", validationAction: "error", validator: {} })
```

Resultat:
```javascript
{
	ok: 1,
	'$clusterTime': {
		clusterTime: Timestamp({ t: 1675844597, i: 1 }),
		signature: {
			hash: Binary(Buffer.from("d3a9541ab3b54f731e712b7286d4b5c626a56fdd", "hex"), 0),
			keyId: Long("7171806562036482052")
		}
	},
	operationTime: Timestamp({ t: 1675844597, i: 1 })
}
```


## Exercice 3

Rajoutez aux critères de validation existants le critère suivant :

Le champ smac doit être présent OU les styles musicaux doivent figurer parmi les suivants : "jazz", "soul", "funk" et "blues".

Reponse:
```javascript
db.runCommand({
   collMod: "salles",
   validationAction: "strict",
   validator: {
      $and: [
         { nom: { $type: "string", $exists: true } },
         { capacite: { $type: "int", $exists: true } },
         { "adresse.codePostal": { $type: "string", $exists: true } },
         { "adresse.ville": { $type: "string", $exists: true } },
         {
            $or: [
               { smac: { $exists: true } },
               { stylesMusicaux: { $in: ["jazz", "soul", "funk", "blues"] } }
            ]
         }
      ]
   }
})

```


Que se passe-t-il lorsque nous exécutons la mise à jour suivante ?
db.salles.update({"_id": 3}, {$set: {"verifie": false}}) 

Reponse:
```javascript
//Si aucun document n'a un champ `_id` avec la valeur 3, alors cette requête de mise à jour ne fera aucun effet et ne modifie aucun document. Si un document existe avec un champ `_id` avec la valeur 3, il sera mis à jour pour inclure le champ `verifie` avec la valeur `false`.
```