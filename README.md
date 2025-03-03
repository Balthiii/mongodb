# Prise de note du lundi 3 mars 2025.

## Qu'est-ce que le NoSQL ?

NoSQL est une catégorie de systèmes de gestion de bases de données qui ne suivent pas le modèle relationnel traditionnel. Contrairement aux bases de données SQL, les bases de données NoSQL sont conçues pour gérer des volumes massifs de données non structurées ou semi-structurées, offrant une flexibilité et une scalabilité accrues. Elles sont souvent utilisées dans des applications nécessitant des performances élevées, une disponibilité continue et une capacité à évoluer horizontalement.

## Qu'est-ce que la scalabilité ?

La scalabilité est la capacité d'un système à gérer une augmentation de la charge de travail en ajoutant des ressources supplémentaires, telles que des serveurs ou des bases de données. Elle peut être verticale (ajout de ressources à un seul serveur) ou horizontale (ajout de plusieurs serveurs). Dans le contexte des bases de données NoSQL, la scalabilité horizontale est particulièrement importante car elle permet de distribuer les données et les requêtes sur plusieurs machines, améliorant ainsi les performances et la disponibilité.

## Différence entre les bases de données relationnelles et non relationnelles

### Bases de données relationnelles

Les bases de données relationnelles (SQL) utilisent un modèle de données structuré basé sur des tables, des lignes et des colonnes. Elles reposent sur des relations définies entre les tables et utilisent le langage SQL (Structured Query Language) pour interroger et manipuler les données. Les bases de données relationnelles sont idéales pour les applications nécessitant des transactions complexes, une intégrité des données et des relations bien définies.

### Bases de données non relationnelles

Les bases de données non relationnelles (NoSQL) n'utilisent pas de schéma fixe et peuvent stocker des données sous diverses formes, telles que des documents, des graphes, des paires clé-valeur ou des colonnes larges. Elles sont conçues pour être hautement évolutives et performantes, et sont souvent utilisées dans des applications nécessitant une gestion flexible des données, une haute disponibilité et une capacité à traiter de grandes quantités de données non structurées ou semi-structurées.

## Qu'est-ce que la projection ?

La projection est une opération dans les bases de données qui permet de sélectionner uniquement certains champs d'un document ou d'un enregistrement, plutôt que de récupérer l'intégralité des données. Cela permet de réduire la quantité de données transférées et traitées, améliorant ainsi les performances des requêtes. Dans le contexte de MongoDB, par exemple, la projection est utilisée pour spécifier les champs à inclure ou à exclure dans le résultat d'une requête.
