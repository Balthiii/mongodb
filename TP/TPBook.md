# TP : GESTION D'UNE BIBLIOTHÈQUE

## Partie 1 : Configuration et création de la base de données

### 1.1 Insertion de documents (Create)

#### Collection `livres`

```
db.livres.insertMany([
  {
    titre: "Le Petit Prince",
    auteur: "Antoine de Saint-Exupéry",
    annee_publication: 1943,
    editeur: "Gallimard",
    genre: ["Conte", "Philosophie"],
    nombre_pages: 96,
    langue: "Français",
    disponible: true,
    stock: 3,
    note_moyenne: 4.8,
    description: "Un pilote d'avion, qui s'est écrasé dans le désert du Sahara, rencontre un jeune prince venu d'une autre planète...",
    prix: 7.50,
    isbn: "9782070612758",
    date_ajout: new Date("2023-01-15")
  },
])
```

#### Collection `utilisateurs`

```
db.utilisateurs.insertMany([
  {
    nom: "Dupont",
    prenom: "Marie",
    email: "marie.dupont@example.com",
    age: 28,
    adresse: {
      rue: "123 Avenue des Livres",
      ville: "Lyon",
      code_postal: "69002"
    },
    date_inscription: new Date("2022-12-10"),
    livres_empruntes: [
      {
        livre_id: ObjectId("65d123456789abcd01234567"), // Remplace avec un vrai ID
        titre: "Le Petit Prince",
        date_emprunt: new Date("2023-02-15"),
        date_retour_prevue: new Date("2023-03-15")
      }
    ],
    tags: ["fiction", "histoire"]
  },
])
```

## Partie 2 : Requêtes de lecture (Read)

#### 1. Listez tous les livres disponibles (si vous utilisez le dataset Amazon, ajoutez d'abord un champ `disponible` à vos documents)

```
db.books.find({disponible : true})
```

#### 2. Trouvez les livres publiés après l'an 2000

```
db.books.find({
  "annee_publication": {
    $gt: 2000
  }
});
```

#### 3. Trouvez les livres d'un auteur spécifique

```
db.books.find({
  auteur : "Albert Camus"
});
```

#### 4. Trouvez les livres qui ont une note moyenne supérieure à 4

```
db.books.find({
  note_moyenne: {
    $gt: 4
  }
});
```

#### 5. Listez tous les utilisateurs habitant dans une ville spécifique

```
db.users.find({
"adresse.ville" :"Paris"
});
```

#### 6. Trouvez les livres qui appartiennent à un genre spécifique

```
db.books.find({
genre :"Philosophie"
});
```

#### 7. Trouvez les livres qui ont à la fois un prix inférieur à 15€ et une note moyenne supérieure à 4

```
db.books.find({
$and : [
  {prix : { $lt : 15} },
  {note_moyenne : {$gt : 4 } }
]
});

```

#### 8. Trouvez les utilisateurs qui ont emprunté un livre spécifique (par titre)

```
db.users.find({
"livres_empruntes.titre" : "Les Misérables"
});
```

## Partie 3 : Mise à jour de documents (Update)

#### 1. Mettez à jour le titre d'un livre spécifique

```
db.books.updateOne(
  { _id: ObjectId("67c6ad51d3e73a28d28e125a") },
  { $set: { titre: "Le Petit Prince V2" } }
);
```

#### 2. Ajoutez un champ `stock` à tous les livres avec une valeur par défaut de 5

```
db.books.updateMany(
  {},
  {$set : { stock: 5}}
)
```

#### 3. Marquez un livre comme indisponible (disponible = false)

```
db.books.updateOne(
  {_id: ObjectId("67c6ad51d3e73a28d28e125a") },
  { $set: { disponible: false } }
)

```

#### 4. Ajoutez un nouvel emprunt dans la liste `livres_empruntes` d'un utilisateur

```
db.users.updateOne(
  {_id: ObjectId("67c6b3bafde215ddf0d93bac") },
  { $push: {
		livres_empruntes: {
			livre_id: ObjectId("67c6ad51d3e73a28d28e125b"),
    	titre: "1984",
      date_emprunt: new Date("2024-03-04"),
      date_retour_prevue: new Date("2024-04-04")
				}
  }
  }
)
```

#### 5. Changez l'adresse d'un utilisateur

```
db.users.updateOne(
 { _id: ObjectId("67c6b3bafde215ddf0d93bac") },
  {$set : {
		adresse : {
		rue : "26.Av Halle tony Garnier",
		ville : "Lyon",
		code_postal : "69007"
			}
		}
  }
)

```

#### 6. Ajoutez un nouveau tag à un utilisateur

```
db.users.updateOne(
 { _id: ObjectId("67c6b3bafde215ddf0d93bac") },
  {$push : {tags : "drame"} }
)

```

#### 7. Mettez à jour la note moyenne d'un livre

```
db.books.updateOne(
 { _id: ObjectId("67c6ad51d3e73a28d28e125c") },
  {$set : {note_moyenne : 3.4} }
)

```

## Partie 4 : Suppression de documents (Delete)

#### 1. Supprimez un livre spécifique par son titre

```
db.books.deleteOne(
  {titre : "Les Misérables" }
)

```

#### 2. Supprimez tous les livres d'un auteur spécifique

```
db.books.deleteMany(
  {auteur : "Albert Camus" }
)

```

#### 3. Supprimez un utilisateur par son email

```
db.users.deleteOne(
  {email : "jean.martin@example.com" }
)

```

## Partie 5 : Requêtes avancées et projection

#### 1. Listez tous les livres triés par note moyenne (ordre décroissant)

```
db.books.find()
.sort({note_moyenne : -1})

```

#### 2. Trouvez les 3 livres les plus anciens

```
db.books.find()
.sort({année_publication : -1})
.limit(3)


```

#### 3. Comptez le nombre de livres par auteur

```
db.books.countDocuments({auteur : "Antoine de Saint-Exupéry"})

```

#### 4. Affichez uniquement le titre, l'auteur et la note moyenne des livres (sans l'id)

```
db.books.find(
  {}, // Filtre vide pour tous les livres
  { titre: 1, auteur: 1, note_moyenne: 1, _id: 0 }
);
```

#### 5. Trouvez les utilisateurs qui ont emprunté plus d'un livre

```
db.utilisateurs.find({
  "livres_empruntes.1": { $exists: true }
});
```

#### 6. Recherchez les livres dont le titre contient un mot spécifique (utilisez $regex)

```
db.books.find(
  { titre: { $regex: "Harry" } },
  { _id: 0, titre: 1 }
);

```

#### 7. Trouvez les livres dont le prix est entre 10€ et 20€

```
db.books.find(
  {$and : [
  { prix : {$gte : 10} },
  { prix : {$lte : 20} }
]
  });

```

## Partie 6 : Modélisation de données (Mini-exercice)

#### 6.1 Modèle embarqué vs référence

```
db.emprunts.insertMany([
  {
    utilisateur_id: ObjectId("67c6b3bafde215ddf0d93bac"),
    livre_id: ObjectId("67c6ad51d3e73a28d28e125b"),
    date_emprunt: new Date("2023-02-20"),
    date_retour_prevue: new Date("2023-03-20"),
    date_retour_effective: null,
    statut: "en cours"
  },
  {
    utilisateur_id: ObjectId("67c6b3bafde215ddf0d93bae"),
    livre_id: ObjectId("67c6ad51d3e73a28d28e125e"),
    date_emprunt: new Date("2023-02-22"),
    date_retour_prevue: new Date("2023-03-22"),
    date_retour_effective: null,
    statut: "en cours"
  },
  {
    utilisateur_id: ObjectId("67c6b3bafde215ddf0d93bae"),
    livre_id: ObjectId("67c6ad51d3e73a28d28e125a"),
    date_emprunt: new Date("2023-03-01"),
    date_retour_prevue: new Date("2023-03-15"),
    date_retour_effective: null,
    statut: "en cours"
  }
]);

```

Il est préférable d'utiliser une collection "emprunts" . Cela est plus simple et peut être plus performant.

#### 6.2 Réflexion sur la modélisation

##### Quels sont les avantages et inconvénients de chaque approche ?

a. Approche avec colletion "emprunts"
Avantages :

- Moins de duplications et plus facile à mettre à jour. Si un livre change, nous ne sommes pas obligés de modifier tous les emprunts associés.

Invonvénient :

- On doit faire des jointures ou utiliser des aggrégations pour récupérer des données. (Requête plus complexe)

b.Approche avec emprunt dans le document utilisateur

Avatanges -> Requête simples et rapides
Inconvénient -> Duplication des données lors de chaque emprunt les livres sont dupliqués + difficile de mettre à jour ( si un livre change, il faut mettre à jour tous les emprunts)

##### Quelle approche privilégieriez-vous pour une application réelle et pourquoi ?

Il vaut mieux privilégier l'approche avec une collection "emprunts, car elle est plus modulaire et permet de mieux gérer ses données pour une mise en production.

##### Comment modéliseriez-vous les cas où un même livre peut exister en plusieurs exemplaires ?

Il faudrait créer une collection "exemplaires" qui fait référence à chaque livre. Puis ensuite lier les emprunts à des exemplaires spécifiques.
