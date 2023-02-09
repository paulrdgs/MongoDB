
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
#### $expr
Permet d'utiliser des expressions dans nos rrquêtes. Ces expressions pourront contenir des opérateurs, des objets ou encore des chemins d'objet pointant vers des champs.

On va chercher et afficher les documents dont la llougueur du nom multipliée par 12 est supérieur a l'age
```javascript
db.personnes.find({
	"nom": {$exists: 1},
	"age": {$exists: 1},
	$expr: 
		{ 
			$gt: 
			[ 
				{ 
					$multiply: 
					[
						{
							$strLenCP: "$nom"
						}, 12
					]
				}, "$age"
			]
		}
},
{
	"nom": 1,
	"_id": 0
})
```

Afficher les comptes dont la sommes des operations de debit est supérieur au montant de credit
```javascript
db.banque.find({
	"debit": {$exists: 1},
	$expr: {
		$gt: 
		[
			{$sum: "$debit"}, "$credit"
		]
	}
})
```

#### $type
L'opérateur $type est un opérateur d'expression qui permet de retourner le type BSON (Binary JSON) d'une expression spécifiée. Il peut être utilisé pour vérifier le type de données d'un champ dans une collection et pour filtrer les documents en fonction de ce type.
```javascript
{ champ: { $type: <type BSON> }}

 db.personnes.insertOne({
	 "nom": "Zidane", "prenom": "Z", "age": numberInt(50)
 })
```

#### $mod
L'opérateur $mod est un opérateur d'agrégation qui permet de calculer le reste d'une division entière. Il peut être utilisé dans des requêtes d'agrégation pour effectuer des opérations de calcul sur les données de la base de données. Par exemple, vous pouvez utiliser l'opérateur $mod pour trouver tous les documents où un champ donné est divisible par un certain nombre.
```javascript
{ champ: { $mod: [ diviseur, reste ] } }
```

#### $where
L'opérateur $where est un opérateur d'agrégation qui permet de filtrer les documents d'une collection en fonction de conditions spécifiées. Il peut être utilisé pour effectuer des opérations de filtrage complexe sur les données de la base de données.
```javascript
db.personnes.find({$where: "this.nom.length > 6"})

db.personnes.find({
	$where: function() {


		return obj.nom.length > 6
	}
})
```
#### $push $pull
L'opérateur $push est un opérateur de mise à jour qui permet d'ajouter un ou plusieurs éléments à un tableau existant. Il peut être utilisé p
our ajouter des éléments à un champ de tableau dans un document en utilisant une requête d'update.
```javascript
{ $push: { <champ>:<valeur>,...}}
{ $pull: { <champ>:<valeur>,...}}

 db.hobbies.updateOne({ "nom": "Yves" } , { $push: { "passion": "livres"}})
```
#### $addToSet
```javascript
// toruve les personnes avec pour interet le jardinage
db.personnes.find({"interets": "jardinage"})

// toruve les personnes qui ont pour iteret bridge et jardinage
db.personnes.find({"interets": {$all: ["bridge", "jardinage"]}})

// toruve les personnes qui on pour interet a index 1 le jardinage
db.personnes.find({"interets.1": "jardinage"})

//trouve els personne qui ont deux interets
db.personnes.find({"interets": {$size:2}})

//trouve les personnes qui ont 2 ou plus interets
db.personnes.find({"interets.1": {$exists:1}})
```

#### $elemMatch
```javascript
db.eleves.find({"notes": {$elemMatch: { $gt: 0,$lt: 10}}})

db.eleves;find({"notes": {$all: [5, 18.50]}})
```

## Les tableaux de documents
```javascript
db.eleves.find({"notes.note":10})
```
Renvoyer les documets dont les éleves ont au moins une note entre 10 et 15 dans une matières quelconque:
```javascript
db.eleves.find(
	{
		"notes": {
			$elemMatch: { 
				"note": { $gt: 10, $lte: 15 }
			}
		}
	}
)

db.eleves.find({"notes.0.note": {$lt: 10}})
```
### Le tri
```javascript
curseur.sort(<tri>)
```
## Les index

  

La nature de votre application devra impacter votre logique d'indexation :

Est-elle orientée écriture (write-heavy) ? ou lecture (read-heavy) ?

  

```javascript

db.collection.createIndex(<champ + type>, <options?>)

  

db.collection.createIndex({ "age": -1 })

{

    "createCollectionAutomatically": false,

    "numIndexesBefore": 1,

    ..

    ok:1

}

  

db.personnes.getIndexes()

// Retourne un tableau

  

db.personnes.dropIndex( "age_-1" )

db.personnes.createIndex({ "age": -1 }, { "name": "index_age" })

```

  

Un index peut porter sur plus d'un champ :

C'est ce qu'on appelle un index composé.

  

```javascript

db.personnes.createIndex({ "age": })

```

  

MongoDB permet l'utilisation de deux types d'Index qui permettent de gérer les requêtes géospatiales :

- Les indexs de type `2dsphere` sont utilisés par des requêtes géospatiales intervenant sur une surface sphérique.

- Les indexs `2d` concernent des requêtes intervenant sur un plan Euclidien.

  

Pour un champ nommé `donneesSpatiales` d'une collection `cartographie` vous pouvez par exemple créer un index de type `2d` avec la commande :

  

``` javascript

db.cartographie.createIndex({ "donneesSpatiales": "2d" })

```

  

Pour la création d'un index `2dsphere` on utilisera plutôt :

  

```javascript

db.cartographie.createIndex({ "donneesSpatiale": "2dsphere" })

```

  

Les indexs 2d font intervenir dess coordonnées de type `legacy`

  

```javascript

db.plan.insertOne({ "nom": "firstPoint", "geodata": [1, 1] })

  

db.plan.insertOne({ "nom": "firstPoint_bis", "geodata": [4.7, 44.5] })

  

db.plan.insertOne({ "nom": "firstPoint_bis", "geodata": [ "lon": 4.7, "lat": 44.5] })

```

  

## Les objets GeoJSON

  

```javascript

{ type: <type d_objet>, coordinates: <coordonnees> }

```

  

## Le type Point

  

```javascript

{

    "type": "Point",

    "coordinates": [ 14.0, 1.0 ]

}

```

  

## Le type MultiPoint

  

```javascript

{

    "type": "MultiPoint",

    "coordinates": [

        [13.0, 1.0], [13.0, 3.0]

    ]

}

```

  

## Le type LineString

  

```javascript

{

    "type": "LineString",

    "coordinates": [

        [13.0, 1.0], [13.0, 3.0]

    ]

}

```

  

## Le type Polygon

  

```javascript

{

    "type": "Polygon",

    "coordinates": [

        [13.0, 1.0], [13.0, 3.0]

    ]

    [

        [13.0, 1.0], [13.0, 3.0]

    ]

}  

```

  

#### Création d'index

  

```javascript

db.avignon.createIndex({ "localisation": "2dsphere" })

  

db.avignon2d.createIndex({ "localisation": "2d" })

```

  

## L'opérateur $nearSphere

  

```javascript

{

    $nearSphere: {

        $geometry: {

            type: "Point",

            coordinates: [<longitude>, <latitude>]

        },

        $minDistance: <distance en metres>,

        $maxDistance: <distance en metres>

    }

}

  

{

    $nearSphere: [ <x>, <y> ],

    $minDistance: <distance en radians>,

    $maxDistance: <distance en radians>

}

```

  
  

```javascript

var opera = { type: "Point", coordinates: [43.949749, 4.805325]}

  

//Requête sur la collection avignon

db.avignon.find(

    {

        "localisation": {

            $nearSphere: {

                $geometry: opera

            }  

        }

    }, { "_id": 0, "nom": 1 }

)

```

  

## L'opérateur $geowWithin

Cet opérateur n'effectue aucun tri et ne necessite pas la création d'un index geospatiale, on l'utilise de la manière suivante:

  

```javascript

{

    <champ des documents contenant les coordonnées>: {

        $geoWithin: {

            <operateur de forme>: <coordonnees>

        }

    }

}

```

  

Création d'un polygone de notre exemple :

```javascript

var polygone = [

    [43.9548, 4.80143],

    [43.95475, 4.80779],

    [43.95045, 4.81097],

    [43.4657, 4.80449],

]

```

  

La requête suivante utilise ce polygone :

```javascript

db.avignon2d.find(

    {

        "localisation": {

            $geoWithin: {

                $polygon: polygone

            }

        }

    }, { "_id": 0, "nom": 1 }  

)

```

  

## Signature pour le cas utilisation objets GeoJSON

  

```javascript

{

    <champ des documents contenant les coordonnées>: {

        $geoWithin: {

            type: < "Polygon" ou bien "MultiPolygon" >,

            coordinates: [<coordonnees>]

        }

    }

}

```

  

```javascript

var polygone = [

    [43.9548, 4.80143],

    [43.95475, 4.80779],

    [43.95045, 4.81097],

    [43.4657, 4.80449],

]

  

db.avignon2d.find(

    {

        "localisation": {

            $geoWithin: {

                $geometry: {

                    type: "Polygon",

                    coordinates: [polygone]

                }

            }

        }

    },  { "_id": 0, "nom": 1 }

)

```

  

## Le framework d'agrégation

  

MongoDB met à disposition un puissant outil d'analyse et de traitement de l'information:

Le pipeline d'agreagation (ou framework)

  

Métaphore du tapis roulant d'usine.

  

Méthode utilisée :

```javascript

db.collection.aggregate(pipeline, options)

```

  

- pipeline : désigne un tableau d'étapes

- options : désigne un document

  

Parmis les options, nous retiendrons :

  

- Les collations, permet d'affecter une collation à l'opération d'aggrégation.

- bypassDocumentValidation : Fonctionne avec un opérateur appelé `$out` et permet de passer au travers de la validation des documents.

- allowDiskUse : Donne la possibilité de faire déborder les opérations d'écriture sur le disque.

  

Vous pouvez appeler aggregate sans arguments :

```javascript

db.personnes.aggregate()

```

  

Au sein du `shell`, nous allons créer une variable pipeline :

```javascript

var pipeline = []

  

db.personnes.aggregate(pipeline)

db.personnes.aggregate(

    pipeline, {

        "collation": { "locale": "fr" }

    }

)

```

## Opérateur $addFields
```javascript
// syntaxe utilisation
{ $addFields: {<nouveau champ> : <expression>, ... } }

db.personnes.aggregate([
	{
		$addFields: {
			"numero_secu": ""
		}
	}
])

 db.personnes.aggregate([
	 {
		 $project: {
			 ......
			 "numero_secu": 1
		 }
	 }
 ])
```
## Opérateur $group
```javascript
{
	$group: {
		"_id": <expression>,
		<champ>: { <operateur d accumulation> }
	}
}

// operateur d'accumulation `$push`, `$sum`, `$avg`, `$min`, `$max`
var pipeline = [
	{
		$group: {
			"_id": "$age",
			"nombre_personnes": { $sum: 1 }
		}
	},
	{
		$sort: { "nombre_personnes": 1 }
	}
]

db.personnes.aggregate(pipeline)

db.personnes.aggregate([
	{
		$sortByCount: "$age"
	}
])

var pipeline = [
	{
		$group: {
			"_id": null,
			"nombre_personnes": { $sum: 1 }
		}
	}
]


var pipeline = [
	{
		$match: {
			"age": { $exists: true }
		}
	},
	{
		$group: {
			"_id": null,
			"avg": { $avg: "$age" }
		}
	},
	{
		$project: {
			"_id": 0,
			"Age_moyen": { $ceil: "$avg" }
		}
	}
]

```
## Pour eval
- pouvoir renommer une collection mongodb.com
```javascript
db.runCommand({ renameCollection: "ancienne_collection_name", to: "nouvelle_collection_name" })
```
- recherche méthode 'findAndModify'
- essayer de lever une erreur qui permet de verifier que les schemas de validation appliquer fonctionnent