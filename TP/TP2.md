## Exercice 1 : Création d'un jeu de données.

Pour l'exemple seulement un produit a été inséré.

```
db.ecommerce_produits.insertMany([
  {
    nom: "T-shirt",
    description: "T-shirt en coton",
    prix: 15,
    stock: 50,
    catégorie: "vêtement",
    sousCatégorie: "été",
    caractéristiquesTechnique: {
      tissu: "coton",
      couleur: "noir"
    },
    avis: [{ id: 1, note: 5, commentaire: "Excellent produit !" }],
    tags: ["Vêtement", "Homme", "Coton", "L", "Promo"]
  }
])

```

## Exercice 2 : Requêtes de lecture

### 1. Récupérer tous les produits d'une catégorie :

```
db.ecommerce_produits.find({ catégorie: "vêtement" });

```

### 2. Trouver les produits donet le prix est entre 50 et 200 euros

```
db.ecommerce_produits.find ({
prix : { $gte: 50, $lte : 200}
});
```

### 3. Lister les produits en stock (stock > 0)

```
db.ecommerce_produits.find ({
stock : { $gt: 0}
});
```

### 4. Trouver les produits avec au moins 3 avis

```
db.ecommerce_produit.find ({
    $expr : {
            $gte : [ {$sive : $avis}]
    }
})
```

## Exercice 3 : Mise à jour

### 1. Augmenter le prix de tous les produits d'une catégorie de 5%

```
db.ecommerce_produits.updateMany(
    { catégorie: "vêtement" },
    { $mul : { prix:1.05 } }
)
```

### 2. Ajouter un champ "promotion" à certains produits

```
db.ecommerce_produits.updateOne(
  {nom : "T-shirt"},
 { $set: { promotion: true } }
);
```

### 3. Ajouter un nouveau tag à tous les produits d'une catégorie

```
db.ecommerce_produits.updateMany(
  {catégorie : "vêtement"},
 { $push { tags : "nouveauté" } }
);
```

### 4. Mettre à jour le stock après une "vente"

```
db.ecommerce_produits.updateOne(
  { nom: "T-shirt" },
  { $inc: { stock: -1 } }
);
```

## Exercice 4 : Requêtes complexes

### 1. Trouver les produits disponibles avec tag1 ET tag 2

```
db.ecommerce_produits.find(
    tags : {$all : ["Technologie","Montre"]}
);
```

### 2. Lister les produits premium avec un stock faible (<5)

```
db.ecommerce_produits.find(
    categorie : {$eq : "premium"}
    stock: { $lt: 5 }
);

```

### 3. Rechercher les produits ayant reçu au moins un avis 5 étoiles

```
db.ecommerce_produits.find({
avis: {
	$elemMatch : {
		note: {$gte : 5 }
    }
}
});
```

### 4. Trouver les produits d'une catégorie, triés par prix décroissant, limités aux 5 premiers

```
db.ecommerce_produits.find({ catégorie: "vêtement" })
  .sort({ prix: -1 })
  .limit(5);

```
