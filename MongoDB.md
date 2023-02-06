
## Collection
Groupe de documents MongoDB, c'est l'équivalent d'une table dans un système de gestion de BDD. Les collections n'imposent pas de schéma précis.

Les documents présents aux seins d'une même collection



## Document

Un document est un ensemble de données clé-valeur.
Le schéma des documents est dit "dynamique".



## Notation

MongoDB notation proche du JSON

Ceci représente un object vide en JSON
```JSON
{}
```

Les propriétés d'un objet JSON sont matérialisées par un ou plusieurs parties clé/valeur:
```JSON
{
	"clé": "valeur",
	"autre clé": 
		{
			"une clé": "une valeur",
			"un tableau": []
		}
}
```

JSON prend en charge de façon native pksuieurs types de données:
- boolean
- numerique
- chaine caractère
- tableau
- object
- null ( absence de valeur )

MongoDB vient ajouter des types:
- le type Date: stocke sous la forme d'un entier signe de 8 octets (nbr sec ecoulé depuis 01/01/1970)
- ObjectId: stocke 12 octets, utilisé en interne par MongoDb afin de générer des identifiants
- NumberLong et NumberInt: par défaut MongoDb cosidère toute valeur numerique comme un nombre a virgule codé sur 8 octets
- BinData:  pour stocker des chaines de caracteres ne possédant pas de representation dans l'encodage UTF-8


## MongoDB en ligne de commande

```bash
mongod
mongod --port 27017 --dbpath /data/databases --logpath /tmp/mongodb.log --logappend
sudo service mongodb stop
```


## Creation BDD
```javascript
use tmp
db.maCollection.insertOne({"une_cle": "une_valeur"})
```


## Suppression BDD

```javascript
use laDaDbASupprimer;
db.dropDatabase()
```


## Gestion des collections
### Les collations
```javascript
{
	**locale**: <string>,
	caseLevel: <boolean>,
	caseFirst: <string>,
	strength: <entier>,
	numericOrdering: <boolean>,
	...
}
```
Au sein de ce type document le champ locale est obligatoire

### Creer une collection
```javascript
db.createCollection("maCollection", {"collation": {"locale": "fr"}})
```


### Les documents
#### Insertion d'un document
```javascript
db.maCollection.insert(<documents /*ou un tableau de documents*/ >)

db.personne.insert([
	{
		"nom": "DUPONT",
		"prenom": "Jean"
	},
	{
		"nom": "Rdgs",
		"prenom": "Paul",
		"age": 22
	}
])
```

#### Modification d'un document
```javascript
db.collection.updateOne(<filtre>, <modifications>)

db.personne.update(
	{ "_id": ObjectId("dupont") }, /* filtre */
	{
		$set: { "nom": "DUPONT" } /* $set est un opérateur */
	}, /* modification */
	{ "multi": true } /*  */
)
```
#### Cas de l'upsert: combimaison update et insert
```javascript
db.personne.updateOne(
	{ "prenom": "remi" },
	{
		$set : { "prenom": "Remi" }
	},
	{ "upsert": true}
)
```


### Validation des documents
Ajout de plsuieurs données
```javascript
var athletes = [
	{
		"nom": "Eclair",
		"prenom": "Jean-Michel",
		"discipline": "Course"
	},
	{
		"nom": "Calvera",
		"prenom": "Max",
		"discipline": "Saut d ehaies"
	},
	{
		"nom": "Hammer",
		"prenom": "Hemsi",
	}
]

db.athletes.insertMany(athletes)
```
Exemple schema validation
```javascript
var proprieties = [
	"nom": 
	{
		"bsonType": "string",
		"pattern": "^[A-Z].*",
		"description": "chaine de caractere+ expr régulière - obligatoire"
	},
	{
		...
	}
]
```
Miantenant que nos règles sont établies, nous allons modifier la collection a l'aide de la commande `collMod`
```javascript
db.runCOmmand(
	{
		"collmMod": "athletes",
		"validator": 
		{
			$jsonSchema: 
			{
				"bsonType": "object",
				"required": ["nom", "prenom", "discipline"],
				"properties": proprieties
			}
		}
	}
)
```



## Opérateur
- $ne : different de
- $gt: superieur a
- $gte: superieur ou egal 
- $lt: inferieur a 
- $lte: inferieur ou egal
- $in et $nin: absence ou presence



## Opérateur logiques



## Pour eval
- pouvoir renommer une collection mongodb.com
- recherche méthode 'findAndModify'
- essayer de lever une erreur qui permet de verifier que les schemas de validation appliquer fonctionnent