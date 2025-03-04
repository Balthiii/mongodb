# TP 1 : Indexation et optimisation des performances

## Exercice 1.1 : Préparation et analyse des performances sans index

#### 1.2. Analysez les performances des requêtes suivantes sans index en utilisant

1. Une recherche par titre exact
```
db.books.find({ 
    titre: "Le Rouge et le Noir - Édition 327" })
    .explain("executionStats");
```


2. Une recherche par auteur
```
db.books.find({ 
    auteur: "George Orwell" })
    .explain("executionStats");
```

3. Une recherche par plage de prix (ex: entre 10€ et 20€) et note minimale
```
db.books.find({ 
    $and: [
        { prix: { $gt: 10 } },
        { prix: { $lt: 20 } },  
        { note_moyenne: { $gte: 4 } }
    ]
}).explain("executionStats");

```

4. Une recherche filtrée par genre et langue avec tri par note décroissante
```
db.books.find({ 
    $and: [
        { genre: "Histoire" },
        { langue : "Allemand" },  
    ]
})
.sort({ "note" : -1 })
.explain("executionStats");

```
5. Pour chaque requête, notez dans un tableau le nombre de documents examinés, le temps d'exécution, le type d'étape.

| #  | Requête Description                                    | totalDocsExamined | executionTimeMillis  |Type d'étape utilisée|
|----|--------------------------------------------------------|-------------------|----------------------|---------------------|
| 1  | Rechercher un livre par titre                          | 1                 | 0                    | COLLSCAN            |
| 2  | Rechercher un livre par auteur                         | 84                | 0                    | COLLSCAN            |
| 3  | Recherche par plage de prix et note minimale           | 53                | 0                    | COLLSCAN            |
| 4  | Recherche par filtre genre et langue                   | 62                | 0                    | COLLSCAN + SORT     |

#### Exercice 1.2 : Création d'index simples et composites

1. Index sur titre
```
db.books.createIndex({ titre: 1 })
```


2. Index sur auteur
```
db.books.createIndex({ auteur: 1 })
```

3. Index sur une plage de prix et note minimal
```
db.books.createIndex({ prix: 1, note_moyenne: -1 })

```

4. Index sur filtrage par genre et langue avec tri par note décroissante
```
db.books.createIndex({ genre: 1, langue: 1, note_moyenne: -1 })

```
5. Notez particulièrement les différences.

Pour le moment je n'ai vu aucune différence entre COLLSCAN et IXSCAN car toutes mes requêtes ont mis moins de 1ms à s'éxécuter.


#### Exercice 1.3 : Index spécialisés

1. Créez un index de texte (text index) sur les champs titre et description des livres.
```
db.books.createIndex({ titre: "text", description: "text" })

```
2. Testez une recherche de texte simple et analysez ses performances.
```
db.books.find({ $text: { $search: "aventure" } }).explain("executionStats")

```
3. Créez une nouvelle collection sessions_utilisateurs

```
const sessions = [
    {
      utilisateur_id: ObjectId("67c7523ac13133e1feb21e6a"),  
      date_derniere_activite: new Date("2023-03-05T12:30:00.000+00:00"),
      donnees_session: {
        session_id: "abc123" 
      }
    },
    {
      utilisateur_id: ObjectId("67c7523ac13133e1feb21e6b"), 
      date_derniere_activite: new Date("2023-03-05T14:45:00.000+00:00"),
      donnees_session: {
        session_id: "xyz456"
      }
    },
    {
      utilisateur_id: ObjectId("67c7523ac13133e1feb21e6c"), 
      date_derniere_activite: new Date("2023-03-06T10:15:00.000+00:00"),
      donnees_session: {
        session_id: "def789" 
      }
    }
  ];
  
  sessions.forEach(session => {
    db.sessions_utilisateurs.insertOne(session);
  });
  
```
4. Créez un index TTL sur cette collection pour supprimer automatiquement les sessions après 30
minutes d'inactivité.

```
db.sessions_utilisateurs.createIndex({ 
    date_derniere_activite: 1 }, 
    { expireAfterSeconds: 1800}
);
```
#### Exercice 1.4 : Optimisations avancées

1. Créez un index qui permet d'obtenir une requête couverte (covered query). Vérifiez que la requête
n'examine aucun document (totalDocsExamined = 0).
```
db.books.createIndex(
    { titre: 1, prix: 1, note_moyenne: 1 }
  );
  
```
```
 db.books.find(
    { prix: { $lt: 20 } },
    { titre: 1, note_moyenne: 1 }
  ).explain("executionStats");
```


2. Créez un index unique sur le champ ISBN des livres et testez son comportement en essayant
d'insérer un document avec un ISBN dupliqué.
```
db.books.createIndex({ isbn: 1 }, { unique: true });

```
Lorsque l'on essaye de créer un autre livre avec le même isbn nous avons une erreur : E11000 duplicate key error collection: tp-mongo-db.books index: isbn_1 dup key: { : "9782070612758" }


3. Créez un index partiel qui n'indexe que les livres disponibles.
```
db.books.createIndex(
  { titre: 1, disponible: 1 },
  { partialFilterExpression: { disponible: true } }  
);

```

4. Activez le profiler MongoDB pour identifier les requêtes lentes (prenant plus de 100ms).

Il faut avoir la version payante de mongoDB Atlas pour activer le profiler.

## Livrable TP1

# Comparatif de performance avant et après indexation

| Requête | Temps avant indexation (ms) | Temps après indexation (ms) | Nombre de documents examinés|
|---------------------------------------------|-----------------------|----------------------------------|
| Recherche par prix et note minimale         | < 1ms                 | < 1ms                            |  53             
| Recherche par genre et langue               | < 1ms                 | < 1ms                            |  62
| Recherche de texte sur titre et description | < 1ms                 | < 1ms                            |  85
| Recherche avec index sur ISBN               | < 1ms                 | < 1ms                            |  1
|Recherche de livres disponibles              | < 1ms                 | < 1ms                            |  708


1. Quelles améliorations de performance avez-vous observées après l'ajout d'index ?

Après avoir ajouté des index sur certaines colonnes comme prix, note_moyenne, et titre, je n'ai pas observé de grandes différences de performance dans mes tests. Cela peut être attribué à la puissance de mon PC, qui est capable d'exécuter les requêtes extrêmement rapidement, même sans indexation. En effet, dans mon cas, même sans index, toutes mes requêtes ont été exécutées en moins de 1 milliseconde.
Cela dit, sur un environnement avec moins de ressources matérielles, l'impact des index aurait probablement été plus significatif. Les index auraient réduit les scans de collection, permettant des recherches plus rapides dans des collections de données volumineuses.

2. Quels types d'index ont été les plus efficaces pour vos requêtes ?

Les index les plus efficaces auraient été les suivants :

Index composés : Un index composé sur les champs prix et note_moyenne aurait été très efficace pour optimiser les requêtes qui filtrent sur ces deux critères. Cela permettrait de réduire le nombre de documents examinés sans avoir à scanner toute la collection.

Index de texte : Un index sur les champs titre et description permettrait des recherches textuelles beaucoup plus rapides. Dans le cadre d'une bibliothèque numérique où les utilisateurs cherchent souvent par titre ou par mot-clé, cet index serait essentiel.

3. Avez-vous identifié des compromis entre performance de lecture et d'écriture ?

En général, l'ajout d'index améliore les performances de lecture (recherche de documents), mais cela peut ralentir les opérations d'écriture (insertion, mise à jour, suppression), car MongoDB doit mettre à jour les index à chaque modification. Cependant, étant donné que la majorité des opérations dans mon cas sont des lectures (avec une faible fréquence de mises à jour), le compromis est acceptable.

4. Comment choisiriez-vous les index pour une application de bibliothèque en production ?

Dans un environnement de production pour une application de bibliothèque, voici les index que je choisirais :

Index sur ISBN et email : Ces champs doivent être uniques, et un index unique garantirait à la fois l'intégrité des données et de bonnes performances de recherche.

Index composite sur prix et note_moyenne : Pour optimiser les recherches par plage de prix et de note, un index composite sur ces deux champs serait très utile.

