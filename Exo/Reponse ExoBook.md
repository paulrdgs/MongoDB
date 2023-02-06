
## 1. 
Créer une base de données sample_db et la table employees
```javascript
use sample_db /* va dans la bdd sample_db, la créer si elle n'existe pas */
db.createCollection("employees") /* créer la table employees */
```

Resultat 1: 'switched to db sample_db'
Resultat 2: { ok: 1 }



## 2.
Insérer les documents dans la table employees
```javascript
db.employees.insertMany([
   { name: "John Doe", age: 35, job: "Manager", salary: 80000 },
   { name: "Jane Doe", age: 32, job: "Developer", salary: 75000 },
   { name: "Jim Smith", age: 40, job: "Manager", salary: 85000 }
])
```

Resultat : 
```javascript
{
acknowledged: true,
insertedIds: 
	{
		'0': ObjectId("63e11bffa8977dbfb5695be8"),
		'1': ObjectId("63e11bffa8977dbfb5695be9"),
		'2': ObjectId("63e11bffa8977dbfb5695bea")
	}
}
```
La commande à créer les employées avec un id de document



## 3.
Ecrire une requête pour trouver tous les documents dans la collection employees
```javascript
db.employees.find({})
```
La commande find nous permet de chercher la table employees.
La condition vide {} indique que l'on applique pas de filtre

Resultat :
```javascript
{
	{
		_id: ObjectId("63e11bffa8977dbfb5695be8"),
		name: 'John Doe',
		age: 35,
		job: 'Manager',
		salary: 80000
	},
	{
		_id: ObjectId("63e11bffa8977dbfb5695be9"),
		name: 'Jane Doe',
		age: 32,
		job: 'Developpeur',
		salary: 75000
	},
	{
		_id: ObjectId("63e11bffa8977dbfb5695bea"),
		name: 'Jim Smith',
		age: 40,
		job: 'Manager',
		salary: 80000
	}
}
```



## 4.
Ecrire une requête pour trouver les document dont l'âge est supérieur à 33
```javascript
db.employees.find({ age: { $gt: 33 } })
```
On indique en paramètre que l'on veut filtrer l'âge et le $gt est un opérateur qui signifie supérieur à 33

Resultat:
```javascript
{
	{
		_id: ObjectId("63e11bffa8977dbfb5695be8"),
		name: 'John Doe',
		age: 35,
		job: 'Manager',
		salary: 80000
	},
	{
		_id: ObjectId("63e11bffa8977dbfb5695bea"),
		name: 'Jim Smith',
		age: 40,
		job: 'Manager',
		salary: 80000
	}
}
```



## 5.
Ecrire une requête pour afficher les documents de employees par salire décroissant
```javascript
db.employees.find({}).sort({ salary: -1 })
```
La commande sort nous permet de trier. Ici on précise que l'on veut trier les salaires et pour dire dans l'ordre décroissant on précise -1

Resultat:
```javascript
{
	{ 
		 _id: ObjectId("63e11bffa8977dbfb5695bea"),  
		 name: 'Jim Smith',  
		 age: 40,  
		 job: 'Manager',  
		 salary: 85000
	 },
	{  
		_id: ObjectId("63e11bffa8977dbfb5695be8"),  
		name: 'John Doe',  
		age: 35,  
		job: 'Manager',  
		salary: 80000
	},
	{  
		_id: ObjectId("63e11bffa8977dbfb5695be9"),  
		name: 'Jane Doe',  
		age: 32,  
		job: 'Developer',  
		salary: 75000
	}
}
```

## 6.
Ecrire une requête pour avoir que le nom et le job de chaque document
```javascript
db.employees.find({}, { _id: 0, name: 1, job: 1 })
```
Pour recupérer uniquement le nom et le job on leur assigne 1. On assigne 0 a "_id_" car il s'affiche par défaut automatiquement

Resultat:
```javascript
{  name: 'John Doe',  job: 'Manager'}
{  name: 'Jane Doe',  job: 'Developer'}
{  name: 'Jim Smith',  job: 'Manager'}
```


## 7.
Ecrire une requête pour compter le nombre d'employees par poste
```javascript
db.employees.aggregate([
   {
      $group: {
         _id: "$job",
         count: { $sum: 1 }
      }
   }
])
```
On regroupe les documents par job grâce a $group.
Avec l'opérateur $sum on compte le nombre de document de chaque groupe.
On récupérera un tableau avec une entré pour chaque poste unique dans la collection.

Resultat:
```javascript
{  _id: 'Developer',  count: 1}
{  _id: 'Manager',  count: 2}
```

## 8.
Ecrire une requête pour mettre à jour le salire de tous les développeurs à 80K
```javascript
db.employees.updateMany(
   { job: "Developer" },
   { $set: { salary: 80000 } }
)
```
On sélectionne tous les documents qui ont pour job développeur et on modifie leur salaire à 80K

Resultat: 
```javascript
{
	acknowledged: true,
	insertedId: null,
	matchedCount: 1,
	modifiedCount: 1,
	upsertedCount: 0
}
```
Cette reponse nous indique que la commande a été faite
Que cette commande agis sur un document et quelle a modifier un document.