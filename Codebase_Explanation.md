## 🎬 Prise de relai

> Maintenant que vous avez vu comment le programme s'exécute et comment les images arrivent au modèle, je vais vous expliquer ce qui se passe à l'intérieur et notamment comment les graphes sont utilisés pour améliorer la qualité de l'image.

## 1. `datasets/` — préparer les images avant tout:

> Avant même de lancer le programme, il faut préparer les données. Le dossier `datasets` contient deux scripts utilitaires. `prepare_DIV2K_subimages.py` découpe les grandes images du dataset DIV2K en petits morceaux de 192x192 pixels, parce qu'un réseau de neurones ne peut pas traiter une image entière d'un coup, c'est trop lourd. `make_json.py` lui crée des fichiers JSON qui sont comme des listes, ils indiquent au programme quelles images utiliser pour l'entraînement et lesquelles pour le test.


## 2. `models/IGNN.py` — le réseau principal:

> `IGNN.py` c'est le fichier central du modèle. IGNN signifie Internal Graph Neural Network. La méthode la plus importante dedans s'appelle `forward`, c'est elle qui décrit ce que le modèle fait quand il reçoit une image. Elle travaille en 4 étapes : d'abord elle extrait des caractéristiques de l'image floue, des informations cachées sur les textures et les formes. Ensuite elle construit le graphe. Ensuite elle partage les détails entre les morceaux similaires. Et enfin elle agrandit l'image pour produire le résultat net. Je vais détailler les étapes 2 et 3 qui sont le vrai apport de ce projet.


## 3. `models/graph_agg.py` — la pièce maîtresse:

> C'est le fichier le plus important du projet. Il contient deux classes.

> La première s'appelle `GraphConstruct` elle construit le graphe. Voici comment : l'image floue est découpée en petits morceaux appelés patches. Pour chaque patch, on cherche les patches qui lui ressemblent le plus ailleurs dans l'image. Ces ressemblances deviennent les connexions du graphe, les traits entre les points. Concrètement, si un morceau de ciel ressemble à 5 autres morceaux de ciel dans l'image, on les relie. La fonction `index_neighbours` fait cette recherche efficacement en ne regardant que dans une fenêtre locale plutôt que toute l'image.

> La deuxième classe s'appelle `GraphAggregation` elle utilise le graphe. Maintenant qu'on sait quels morceaux se ressemblent, on prend les détails nets des voisins et on les transfère vers le morceau qu'on veut améliorer. C'est l'idée centrale du projet : au lieu d'inventer des détails, on les emprunte à d'autres parties de la même image.


## 4. `models/submodules.py` — les briques de construction:

> `submodules.py` contient les petits blocs réutilisables que le modèle assemble. Le `ResBlock` ou Residual Block est un mini-réseau qui améliore l'image progressivement sans écraser ce qu'il a déjà appris c'est une technique standard très efficace. Le `GCNBlock` c'est le bloc qui fait l'agrégation sur le graphe il combine les informations des voisins avec des poids appris automatiquement pendant l'entraînement. Et `PixelShuffle` c'est la technique utilisée pour agrandir l'image proprement à la fin — au lieu d'étirer les pixels ce qui donnerait du flou, on réorganise intelligemment les informations déjà calculées.


## 5. `models/VGG19.py` — comparer intelligemment:

> Pour construire le graphe, on a besoin de savoir si deux patches se ressemblent. La façon naïve serait de comparer les pixels directement. Le problème c'est que deux morceaux peuvent se ressembler visuellement mais avoir des valeurs de pixels différentes à cause de la luminosité ou de l'angle. Alors on utilise VGG19 un réseau déjà entraîné sur des millions d'images pour comparer les patches de façon plus intelligente, en comparant ce qu'il perçoit dans chaque morceau : les textures, les contours, les structures. C'est beaucoup plus fiable.


## 🏁 Conclusion:

> Pour résumer : le projet prend une image floue, la découpe en morceaux, relie les morceaux similaires dans un graphe, emprunte les détails des voisins pour reconstruire chaque morceau, et produit une image nette. L'originalité par rapport à un réseau classique c'est justement cette étape graphe le modèle n'invente pas les détails, il les trouve ailleurs dans la même image. Nous sommes disponibles pour vos questions.