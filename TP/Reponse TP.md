
### Préparation des données

- Importez un jeu de données de localisation, comme les restaurants dans une ville. Vous pouvez utiliser la commande mongoimport pour importer des données dans MongoDB
- Assurez-vous d'avoir un champ de localisation géospatiale, comme la latitude et la longitude

Réponse :
```javascript
mongoimport --db Restaurant --collection business --file C:\restaurant.json

// Exemple de donnée
{
	_id: ObjectId("63e4fb39a766145a9e6eba61"),
	business_id: 'qkRM_2X51Yqxk3btlwAQIg',
	name: 'Temple Beth-El',
	address: '400 Pasadena Ave S',
	city: 'St. Petersburg',
	state: 'FL',
	postal_code: '33707',
	stars: 3.5,
	review_count: 5,
	is_open: 1,
	attributes: null,
	categories: 'Synagogues, Religious Organizations',
	hours: {
		Monday: {
			open: '9:0',
			close: '17:0'
		},
		Tuesday: {
			open: '9:0',
			close: '17:0'
		},
		Wednesday: {
			open: '9:0',
			close: '17:0'
		},
		Thursday: {
			open: '9:0',
			close: '17:0'
		},
		Friday: {
			open: '9:0',
			close: '17:0'
		}
	},
	lieu: {
		type: 'Point',
		coordinates: [
		-82.732983,
		27.76659
		]
	}
}
```

### Requêtes MongoDB

- Recherchez les restaurants qui sont ouverts à partir de 18h00. Utilisez la méthode find () et les opérateurs de comparaison pour trouver les documents qui correspondent à vos critères

```javascript

// REPONSE
db.business.find(
{ 
	$or: [ 
		{"hours.Monday.open":{ $regex: /^18:/ }}, 
		{"hours.Tuesday.open":{ $regex: /^18:/ }}, 
		{"hours.Wednesday":{ $regex: /^18:/ }}, 
		{"hours.Thursday.open":{ $regex: /^18:/ }}, 
		{"hours.Friday.open":{ $regex: /^18:/ }}, 
		{"hours.Saturday.open":{ $regex: /^18:/ }}, 
		{"hours.Sunday.open":{ $regex: /^18:/ }} 
	] 
}, 
{"name": 1}
) 


// RESULTAT

{  _id: ObjectId("63e4fb39a766145a9e6ebb3c"),  name: "O'Briens Irish Pub"}

{  _id: ObjectId("63e4fb39a766145a9e6ec4b0"),  name: 'The Grog Grill'}

{  _id: ObjectId("63e4fb39a766145a9e6ecf24"),  name: 'The Crystal Room'}

{  _id: ObjectId("63e4fb39a766145a9e6ed219"),  name: 'Chef Tony Macaroni'}

{  _id: ObjectId("63e4fb39a766145a9e6ed272"),  name: 'Unplugged'}

{  _id: ObjectId("63e4fb39a766145a9e6ed2a9"),  name: 'Johnnie Bleu'}

{  _id: ObjectId("63e4fb39a766145a9e6ed2db"),  name: 'Hooka House'}

{  _id: ObjectId("63e4fb39a766145a9e6ed50c"),  name: 'Emmanuelle'}

{  _id: ObjectId("63e4fb39a766145a9e6ed661"),  name: 'Favela Chic'}

{  _id: ObjectId("63e4fb39a766145a9e6edb84"),  name: 'Scores Gentlemens Club & Steakhouse'}

{  _id: ObjectId("63e4fb39a766145a9e6ee11c"),  name: 'Aradia Fitness Edmonton _ Whyte ave'}

{  _id: ObjectId("63e4fb39a766145a9e6ee43a"),  name: "Indy's Family Restaurant"}

{  _id: ObjectId("63e4fb39a766145a9e6ee662"),  name: 'Iris'}

{  _id: ObjectId("63e4fb3aa766145a9e6ee9c1"),  name: 'Vetri Cucina'}

{  _id: ObjectId("63e4fb3aa766145a9e6eea24"),  name: 'ICON Lounge'}

{  _id: ObjectId("63e4fb3aa766145a9e6eebcb"),  name: "Zayna's Hookah Lounge"}

{  _id: ObjectId("63e4fb3aa766145a9e6ef701"),  name: 'The Yachtsman'}

{  _id: ObjectId("63e4fb3aa766145a9e6ef9df"),  name: 'Laughing Buddha Hot Yoga'}

{  _id: ObjectId("63e4fb3aa766145a9e6efa2b"),  name: 'Tucson Padres'}

{  _id: ObjectId("63e4fb3aa766145a9e6efb8b"),  name: 'The Reno Empire'}



// Les retaurants qui ouvre a 18h un jour dans la semaine 
db.business.aggregate([ 
	{ 
		$project: { 
			name:1, 
				ouverture: { $objectToArray : "$hours" } 
		} 
	}, 
	{ 
		$match: { 
			"ouverture.v.open":{ $regex: /^18:/ } 
		} 
	}, 
	{ $project: { name:1 } } 
])


// RESULTAT
{  _id: ObjectId("63e4fb39a766145a9e6ebb3c"),  name: "O'Briens Irish Pub"}

{  _id: ObjectId("63e4fb39a766145a9e6ebc0b"),  name: 'MOSI Food Truck Rally'}

{  _id: ObjectId("63e4fb39a766145a9e6ebc32"),  name: 'Peace of Mind Pub & Eatery'}

{  _id: ObjectId("63e4fb39a766145a9e6ebd35"),  name: '365 Caffe Italiano'}

{  _id: ObjectId("63e4fb39a766145a9e6ebd7a"),  name: 'ACME Screening Room'}

{  _id: ObjectId("63e4fb39a766145a9e6ec063"),  name: 'Levittown Lanes'}

{  _id: ObjectId("63e4fb39a766145a9e6ec196"),  name: 'El Carrito'}

{  _id: ObjectId("63e4fb39a766145a9e6ec27c"),  name: 'Reno Art Bar'}

{  _id: ObjectId("63e4fb39a766145a9e6ec28c"),  name: 'Suis Generis'}

{  _id: ObjectId("63e4fb39a766145a9e6ec307"),  name: 'The Comedy Bar'}

{  _id: ObjectId("63e4fb39a766145a9e6ec4b0"),  name: 'The Grog Grill'}

{  _id: ObjectId("63e4fb39a766145a9e6ec653"),  name: 'Bistro66'}

{  _id: ObjectId("63e4fb39a766145a9e6ec6f5"),  name: 'Rooftop Garden'}

{  _id: ObjectId("63e4fb39a766145a9e6eca0a"),  name: "Fred's"}

{  _id: ObjectId("63e4fb39a766145a9e6ecb9a"),  name: 'Big Bang - Dueling Piano Bar'}

{  _id: ObjectId("63e4fb39a766145a9e6ecca3"),  name: 'Christ Presbyterian Church'}

{  _id: ObjectId("63e4fb39a766145a9e6ecdb4"),  name: "Daydreams Gentlemen's Club"}

{  _id: ObjectId("63e4fb39a766145a9e6ecdf5"),  name: 'Above The Salt Cafe'}

{  _id: ObjectId("63e4fb39a766145a9e6ece5e"),  name: 'No Ceilings Limo Service'}

{  _id: ObjectId("63e4fb39a766145a9e6ece6d"),  name: "Katsumi's Teaching Kitchen"}
```


- Triez les restaurants par note, du plus haut au plus bas. Utilisez la méthode sort () pour trier les résultats

```javascript

// REPONSE

db.business.find({}, {name: 1, stars: 1, _id: 0}).sort({stars: -1, name: 1})

// La méthode `sort()` nous permet de trier les données. Ici, on trie une premières fois par le nomvre d'étoiles puis une seconde fois par le nom.



// RESULTAT
{  name: " Joe's Throwback Barber Shop",  stars: 5}
{  name: '"No Macarena" DJ & Custom Music Service',  stars: 5}
{  name: '$155 Flat Rate Hauling Trash Removal',  stars: 5}
{  name: '$5 Fresh Burger Stop',  stars: 5}
{  name: '09 Pub',  stars: 5}
{  name: '1 Hour Jewelry and Watch Repair',  stars: 5}
{  name: '1 Solution AV',  stars: 5}
{  name: '1 Stop Mini Market',  stars: 5}
{  name: '1 Stop Sushi Express',  stars: 5}
{  name: '1-800-GOT-JUNK? Central Coast',  stars: 5}
{  name: '1-Stop Ranch & Feed',  stars: 5}
{  name: '10 & 10 Enoteca',  stars: 5}
{  name: '101 Property Mgmt',  stars: 5}
{  name: '10th Planet Jiu Jitsu Tucson',  stars: 5}
{  name: '11 Eleven Tactical USA',  stars: 5}
{  name: '1111 Massage',  stars: 5}
{  name: '12 South Dental Studio',  stars: 5}
{  name: '12 South Winter Warmer',  stars: 5}
{  name: '12 South Yoga',  stars: 5}
{  name: '1205 Distillery',  stars: 5}

// On trie par le nombre d'étoile puis par le nom.
// Etant donné qu'il y a plus de 150 000 données, mongoDB nous renvoie les 20 premières données.
```


### Indexation avec MongoDB

- Créez un index sur le champ d'ouverture des restaurants pour améliorer les performances de la recherche. Utilisez la méthode createIndex ()

```javascript

// REPONSE
db.business.createIndex({ "hours": 1 }, {name: "index_heure"})
db.business.createIndex({ "hours.Monday.open": 1 }, { "name": "IndexOuvertureLundi" }) db.business.createIndex({ "hours.Tuesday.open": 1 }, { "name": "IndexOuvertureMardi" }) db.business.createIndex({ "hours.Wednesday": 1 }, { "name": "IndexOuvertureMercredi" }) db.business.createIndex({ "hours.Thursday.open": 1 }, { "name": "IndexOuvertureJeudi" }) db.business.createIndex({ "hours.Friday.open": 1 }, { "name": "IndexOuvertureVendredi" }) db.business.createIndex({ "hours.Saturday.open": 1 }, { "name": "IndexOuvertureSamedi" }) db.business.createIndex({ "hours.Sunday.open": 1 }, { "name": "IndexOuvertureDimanche" }) db.business.createIndex({ "hours.Monday.open": 1, "hours.Tuesday.open":1, "hours.Wednesday":1, "hours.Thursday.open":1, "hours.Friday.open":1, "hours.Saturday.open":1, "hours.Sunday.open":1 }, { "name": "IndexOuvertureSemaine" })

// Ici, on créé un index sur le champ "hours" pour recupérer toute les données des horaires
// On créer un index sur chaque open de chaque jour pour pouvoir récupérer les horaires d'ouvertures d'un jours précis
// Puis un index sur tout les opens pour récupérer tout les horaires d'ouvertures de la semaine


// RESULTAT
'index_heure'
"IndexOuvertureLundi"
"IndexOuvertureMardi"
"IndexOuvertureMercredi"
"IndexOuvertureJeudi"
"IndexOuvertureVendredi"
"IndexOuvertureSamedi"
"IndexOuvertureDimanche"
"IndexOuvertureSemaine"
```


- Vérifiez que l'index a été créé en utilisant la méthode listIndexes ()

```javascript

// REPONSE
db.runCommand({ listIndexes: "business" }).cursor.firstBatch

// La méthode `runCommand()` nous permet de voir la liste des index existants dans la la collection business.
// La commande `listIndexes` nous permet d'afficher la liste des indexs de la collection business.


// RESULTAT
[
	{ v: 2, key: { _id: 1 }, name: '_id_' },
	{ v: 2, key: { hours: 1 }, name: 'index_heure' }
]
```


### Requêtes géospatiales

- Recherchez les restaurants qui se trouvent à moins de 2 km d'une certaine localisation. Utilisez la méthode find () avec un opérateur géospatial pour trouver les restaurants à l'intérieur d'un cercle

```javascript

// REPONSE
// On doit d'abord créer un index 2dsphere pour pouvoir utiliser les opérateurs spatiaux
db.business.createIndex( { lieu : "2dsphere" } )

// 
db.business.find({ 
	lieu: { 
		$near: { 
			$geometry: { 
				type: "Point", 
				coordinates: [ longitude, latitude ]
				
			}, 
			$maxDistance: 2000 
		} 
	} 
}, {name: 1,  _id: 0, address: 1, city: 1})
```


- Recherchez les restaurants qui se trouvent dans un certain rayon autour d'un point de localisation spécifique. Utilisez la méthode find () avec un opérateur géospatial pour trouver les restaurants à l'intérieur d'un cercle

```javascript

// REPONSE

db.business.find({ 
	lieu: { 
		$near: { 
			$geometry: { 
				type: "Point", 
				coordinates: [ longitude, latitude ]
				
			}, 
			$maxDistance: Nombre_Kilometre_Convertie_Metre 
		} 
	} 
}, {name: 1,  _id: 0, address: 1, city: 1})
```

```javascript
// EXEMPLE

db.business.find({ 
	lieu: { 
		$near: { 
			$geometry: { 
				type: "Point", 
				coordinates: [ -119.7111968, 34.4266787 ]
				
			}, 
			$maxDistance: 2000 
		} 
	} 
}, {name: 1,  _id: 0, address: 1, city: 1})

// RESULTAT
{  
	name: 'Abby Rappoport, LAC, CMQ',  
	address: '1616 Chapala St, Ste 2',  
	city: 'Santa Barbara'
}
{  
	name: 'Axxess Card',  
	address: '1616 Chapala St, Ste 1',  
	city: 'Santa Barbara'
}
{  
	name: 'James House Santa Barbara',  
	address: '1632 Chapala St',  
	city: 'Santa Barbara'
}
{  
	name: 'Cochrane Property Management, Inc.',  
	address: '102 W Arrellaga St',  
	city: 'Santa Barbara'
}
{  
	name: 'Cheshire Cat Inn',  
	address: '36 W Valerio St',  
	city: 'Santa Barbara'
}
{  
	name: 'El Prado Inn',  
	address: '1601 State St',  
	city: 'Santa Barbara'
}
{  
	name: 'Kunz John David, MD',  
	address: '101 W Arrellaga St, Ste B',  
	city: 'Santa Barbara'
	}
{  
	name: 'Optometry Care Santa Barbara',  
	address: '1629 State St, Ste 1',  
	city: 'Santa Barbara'
}
{  
	name: 'Heath Montgomery DMD',  
	address: '14 W Valerio St, Ste C',  
	city: 'SANTA BARBARA'
}
{  
	name: 'Century 21 Butler Realty',  
	address: '1635 State St',  
	city: 'Santa Barbara'
}
{  
	name: 'La Quinta Inn & Suites by Wyndham Santa Barbara Downtown',  
	address: '1601 State Street',  
	city: 'Santa Barbara'
}
{  
	name: "Cantwell's Market & Deli",  
	address: '1533 State St',  
	city: 'Santa Barbara'
}
{ 
	name: 'IHOP',  
	address: '1701 State St',  
	city: 'Santa Barbara'
}
{  
	name: 'Cami Ferris-Wong, DDS',  
	address: '1525 State St, Ste 201',  
	city: 'Santa Barbara'
}
{  
	name: 'Robert Half',  
	address: '1525 State St, Ste 101', 
	city: 'Santa Barbara'
}
{  
	name: 'The Presidio Hotel',  
	address: '1620 State St',  
	city: 'Santa Barbara'
}
{  
	name: 'Financial Credit Network',  
	address: '7 W Figueroa St, Ste 302',  
	city: 'Santa Barbara'
}
{  
	name: 'The Supply Room - The Presidio Motel',  
	address: '1620 State St',  
	city: 'Santa Barbara'
}
{  
	name: 'Santa Barbara Hair and Makeup',  
	address: '1634 De La Vina St',  
	city: 'Santa Barbara'
}
{  
	name: 'Lucent Skincare Santa Barbara',  
	address: '1525 State St, Ste 206',  
	city: 'Santa Barbara'
}
```

### Framework d'agrégation

- Calculez la moyenne des notes des restaurants. Utilisez le framework d'agrégation de MongoDB pour effectuer des calculs sur les données

```javascript

// REPONSE
db.Business.aggregate([ { $group: { _id: null, moy: { $avg: "$stars" } } } ])


// RESULTAT
{
	_id: null,
	moy: 3.5967235576603303
}
```

- Trouvez les restaurants les plus populaires en fonction du nombre de commentaires. Utilisez le framework d'agrégation de MongoDB pour groupes les données et effectuer des calculs

```javascript

// REPONSE
db.Business.aggregate([ 
	{ $sort: 
	   { review_count: -1 } 
	},
	{ $limit: 20 },
	{ $project: 
		{ 
			_id: 0,
			name: 1,
			city: 1, 
			review_count: 1 
		}
	} 
])


// RESULTAT
{  
	name: 'Acme Oyster House',  
	city: 'New Orleans',  
	review_count: 7568
}
{  
	name: 'Oceana Grill',  
	city: 'New Orleans',  
	review_count: 7400
}
{  
	name: 'Hattie B’s Hot Chicken - Nashville',  
	city: 'Nashville',  
	review_count: 6093
}
{  
	name: 'Reading Terminal Market',  
	city: 'Philadelphia',  
	review_count: 5721
}
{  
	name: 'Ruby Slipper - New Orleans',  
	city: 'New Orleans',  
	review_count: 5193
}
{  
	name: "Mother's Restaurant",  
	city: 'New Orleans',  
	review_count: 5185
}
{  
	name: 'Royal House',  
	city: 'New Orleans',  
	review_count: 5070
}
{  
	name: "Commander's Palace",  
	city: 'New Orleans',  
	review_count: 4876
}
{  
	name: 'Luke',  
	city: 'New Orleans',  
	review_count: 4554
}
{  
	name: 'Cochon',  
	city: 'New Orleans',  
	review_count: 4421
}
{  
	name: "Pat's King of Steaks",  
	city: 'Philadelphia',  
	review_count: 4250
}
{  
	name: 'Biscuit Love: Gulch',  
	city: 'Nashville',  
	review_count: 4207
}
{  
	name: "Pappy's Smokehouse",  
	city: 'Saint Louis',  
	review_count: 3999
}
{  
	name: "Felix's Restaurant & Oyster Bar",  
	city: 'New Orleans',  
	review_count: 3966
}
{  
	name: 'Gumbo Shop',  
	city: 'New Orleans',  
	review_count: 3902
}
{  
	name: 'Cochon Butcher',  
	city: 'New Orleans',  
	review_count: 3837
}
{  
	name: 'Los Agaves',  
	city: 'Santa Barbara',  
	review_count: 3834
}
{  
	name: "Willie Mae's Scotch House",  
	city: 'New Orleans',  
	review_count: 3582
}
{  
	name: "Geno's Steaks",  
	city: 'Philadelphia',  
	review_count: 3401
}
{  
	name: 'Grand Sierra Resort and Casino',  
	city: 'Reno',  
	review_count: 3345
}
```


### Export de la base de données

- Exportez les résultats des requêtes dans un fichier CSV pour un usage ultérieur. Utilisez la commande mongoexport pour exporter des données de MongoDB

```javascript
mongoexport --collection=business --db=Restaurant --out=restaurant.csv
```


