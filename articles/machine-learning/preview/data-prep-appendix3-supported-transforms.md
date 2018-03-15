---
title: "Utiliser les transformations de données pour la préparation des données dans Azure Machine Learning | Microsoft Docs"
description: "Cet article fournit la liste complète des transformations disponibles pour la préparation des données Azure Machine Learning."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 9cd6e6cdf8d8dc2b1872d6f7b76821122fd427fa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Utiliser les transformations de données pour la préparation des données dans Azure Machine Learning

Une *transformation* dans Azure Machine Learning consomme des données dans un format spécifié, effectue une opération sur les données (telle que le changement du type de données), puis génère des données dans le nouveau format. Chaque transformation présente une interface utilisateur et un comportement qui lui sont propres. En chaînant plusieurs transformations par le biais d’étapes du flux de données, vous pouvez effectuer des transformations complexes et renouvelées de vos données. Ceci constitue la base de la fonctionnalité de préparation des données.

Vous trouverez ci-après les transformations disponibles dans Azure Machine Learning. 

## <a name="column-selection"></a>Sélection de colonnes 
La plupart des transformations figurant dans cette liste fonctionnent sur une ou plusieurs colonnes. Pour sélectionner plusieurs colonnes, utilisez la touche Ctrl. Pour sélectionner une plage de colonnes, utilisez la touche Maj.

## <a name="transforms-from-the-main-menu-or-the-grid-header"></a>Transformations du menu principal ou de l’en-tête de grille 
Accédez aux transformations dans l’option Transformations du menu principal. Vous pouvez également sélectionner des transformations en cliquant avec le bouton droit sur le nom de la colonne dans la grille de données. Si plusieurs colonnes sont sélectionnées, cliquez avec le bouton droit sur l’une d’elles pour ouvrir un menu de transformations.

Le menu contextuel affiche uniquement les transformations valides pour le type de données sélectionné. Le menu principal affiche toutes les transformations, mais désactive celles qui ne sont pas pertinentes pour les colonnes sélectionnées.

Vous pouvez accéder à quelques transformations contextuelles en cliquant avec le bouton droit sur une cellule. Il s’agit des transformations Copier, Remplacer et Filtrer. Celles-ci tiennent compte du type de données. Par conséquent, les options proposées pour une colonne numérique sont différentes de celles d’une colonne de chaîne.

## <a name="derive-column-by-example"></a>Dériver des colonnes par l’exemple
Utilisez cette transformation pour créer une colonne dérivée d’une ou de plusieurs colonnes existantes. La transformation examine les colonnes d’entrée (sélectionnées) et l’exemple fourni, puis détermine le résultat à afficher dans la nouvelle colonne. 

Pour utiliser cette transformation, sélectionnez une ou plusieurs colonnes. Ajoutez une nouvelle colonne dérivée (vide) par l’exemple. Tapez un exemple de ce que vous souhaitez afficher dans la colonne dérivée (en supposant qu’elle est dérivée d’autres colonnes). La technologie « Par exemple » tente alors de renseigner toutes les autres cellules de la colonne. 

Pour obtenir des exemples complexes, vous devrez peut être fournir plusieurs exemples. Pour cela, sélectionnez une autre cellule et tapez un autre exemple.

La technologie « Par l’exemple » utilise les colonnes sélectionnées pour tenter de déterminer la signification d’un exemple. Si aucune colonne n’est sélectionnée lorsque cette transformation est appelée, toutes les cellules de la ligne active sont utilisées. Si vous sélectionnez uniquement les colonnes requises, vous obtiendrez des résultats plus précis.

Vous pouvez sélectionner les colonnes avant d’appeler la transformation. Lorsque l’éditeur de transformation est ouvert, les cases à cocher situées en haut de chaque colonne indiquent les colonnes sélectionnées comme entrées. Vous pouvez ajouter ou supprimer des colonnes dans la sélection à l’aide des cases à cocher figurant dans les en-têtes de colonne.

Pour en savoir plus sur la transformation **Dériver des colonnes par exemple** et obtenir d’autres exemples, consultez [l’article de référence](data-prep-derive-column-by-example.md).  

## <a name="split-column-by-example"></a>Fractionner des colonnes par l’exemple
Cette transformation tente de fractionner une colonne existante en *n* autres colonnes grâce à la technologie « Par exemple ». Vous pouvez exécuter le fractionnement automatique sur les colonnes générées par la suite.

Pour en savoir plus sur la transformation **Fractionner des colonnes par exemple** et obtenir d’autres exemples, consultez [l’article de référence](data-prep-split-column-by-example.md).

## <a name="expand-json"></a>Développer JSON

Cette transformation vous permet d’ajouter plusieurs colonnes en développant une colonne avec le texte JSON valide.

Pour en savoir plus sur la transformation **Développer JSON** et obtenir d’autres exemples, consultez [l’article de référence](data-prep-expand-json.md).


## <a name="combine-columns-by-example"></a>Combiner des colonnes par l’exemple

Cette transformation permet d’ajouter une nouvelle colonne en combinant les valeurs de plusieurs colonnes. 

Pour en savoir plus sur la transformation **Combiner des colonnes par exemple** et obtenir d’autres exemples, consultez [l’article de référence](data-prep-combine-columns-by-example.md).


## <a name="duplicate-column"></a>Dupliquer une colonne
Cette transformation effectue une copie exacte d’une ou de plusieurs colonnes sélectionnées et donne à chaque colonne copiée un nouveau nom dérivé du nom de la colonne d’origine.

## <a name="text-clustering"></a>Clustering de texte 
Cette transformation est conçue pour accepter et regrouper des valeurs incohérentes qui devraient être identiques.  

Avec cette transformation, les valeurs d’une colonne font l’objet d’une analyse de similarité et sont regroupées en clusters. À chaque cluster correspond une valeur canonique, qui remplace toutes les instances du cluster et toutes les valeurs d’instance. Les clusters complets peuvent être supprimés, et la valeur canonique modifiée. Les instances peuvent être supprimées à partir d’un cluster donné. Il est possible de modifier le filtre Seuil du score de similarité utilisé pour regrouper les instances en cluster.

Par défaut, cette transformation remplace toutes les valeurs d’instance de cluster par la valeur canonique pour ce cluster, ce qui entraîne la création d’une colonne qui contient les nouvelles valeurs. Vous pouvez également ajouter le score de similarité de chaque instance dans une nouvelle colonne (que vous pouvez nommer) afin de l’utiliser ultérieurement dans le flux de données.

## <a name="replace-values"></a>Remplacer des valeurs
Cette transformation permet de remplacer une chaîne par une autre. La chaîne source peut être une chaîne partielle ou une cellule complète, et ce remplacement peut s’appliquer à une ou plusieurs colonnes. La chaîne de recherche prend en charge la recherche de caractères spéciaux et de caractères normaux. 

## <a name="replace-na-values"></a>Remplacer des valeurs NA
Utilisez cette transformation pour remplacer les différentes formes de NA (n/a, NA, null, NaN, etc.) ou les chaînes vides par une même valeur, par souci de cohérence. Cette transformation prend en charge une ou plusieurs colonnes, et elle est indiquée dans la liste uniquement si une colonne est sélectionnée. Elle ne figure pas dans le menu de transformation principal si aucune colonne n’est sélectionnée.

## <a name="replace-missing-values"></a>Remplacer des valeurs manquantes
Cette transformation remplace les données manquantes par une même valeur et prend en charge une ou plusieurs colonnes. Cette transformation est uniquement répertoriée si une colonne est sélectionnée. Elle ne figure pas dans le menu de transformation principal si aucune colonne n’est sélectionnée.

## <a name="replace-error-values"></a>Remplacer des valeurs d’erreur
Cette transformation remplace les erreurs par une même valeur et prend en charge une ou plusieurs colonnes. Cette transformation est uniquement répertoriée si une colonne est sélectionnée. Elle ne figure pas dans le menu de transformation principal si aucune colonne n’est sélectionnée.

## <a name="trim-string"></a>Découper une chaîne
Cette transformation supprime les espaces de début et de fin (espaces, tabulations, etc.) dans une ou plusieurs colonnes.

## <a name="adjust-precision"></a>Ajuster la précision
Cette transformation permet de définir le nombre de décimales dans une colonne numérique.

## <a name="rename-column"></a>Renommer une colonne
Cette transformation modifie le nom de la colonne sélectionnée. Vous pouvez également l’appeler directement dans l’en-tête de colonne en cliquant sur le nom de la colonne.

## <a name="remove-column"></a>Supprimer une colonne
Cette transformation supprime les colonnes sélectionnées et fonctionne sur une ou plusieurs colonnes. 

## <a name="keep-column"></a>Conserver une colonne
Cette transformation conserve uniquement les colonnes sélectionnées et fonctionne sur une ou plusieurs colonnes.

## <a name="handle-path-column"></a>Gérer la colonne des chemins d’accès
Lors de l’importation d’un fichier, une colonne de chemins d’accès est automatiquement ajoutée au jeu de données par la fonctionnalité **Ajouter une source de données**. Elle contient le nom de fichier complet correspondant au chemin d’accès du jeu de données. Cette transformation ajoute ou supprime cette colonne supplémentaire dans le jeu de données.

## <a name="convert-field-type-to-numeric"></a>Convertir le type de champ au format Nombre
Cette transformation convertit le type de la colonne au format Numeric. Vous pouvez spécifier le séparateur pour les valeurs non entières. Par défaut, cette transformation n’affiche aucune invite concernant le séparateur. Utilisez donc l’élément de menu **Modifier** pour appeler l’éditeur. Cette modification s’applique à une ou plusieurs colonnes.

## <a name="convert-field-type-to-date"></a>Convertir le type de champ au format Date
Cette transformation convertit le type de la colonne au format Date. Un format de date/heure par défaut est utilisé, mais vous pouvez le remplacer à l’aide des directives `strftime`. Vous pouvez également ajouter des valeurs de temps avec une date fixe.

Par défaut, cette transformation n’affiche aucune invite concernant le format. Utilisez donc l’élément de menu **Modifier** à l’étape résultante pour appeler l’éditeur. Cette modification s’applique à une ou plusieurs colonnes.

Seules les dates comprises entre 9-22-1677 et 4-11-2262 peuvent être converties au format Date.

## <a name="convert-field-type-to-boolean"></a>Convertir le type de champ au format Booléen
Cette transformation convertit le type de la colonne au format True/False. Elle prend en charge le mappage de plusieurs valeurs à la valeur true ou false, et vous pouvez modifier ces mappages. Elle prend également en charge une constante à laquelle vous pouvez mapper des valeurs qui n’existent pas dans les tables de mappage true/false. Cette modification s’applique à une ou plusieurs colonnes.

## <a name="convert-field-type-to-string"></a>Convertir le type de champ au format String
Cette transformation modifie le type de colonne en chaîne et fonctionne sur une ou plusieurs colonnes.

## <a name="convert-unix-timestamp-to-datetime"></a>Convertir l’horodatage Unix au format DateTime
Cette transformation gère le format de timestamp Unix, même s’il est représenté sous forme de chaîne dans les données. Elle le convertit correctement au format Date lors de la préparation des données.

## <a name="filter"></a>Filtrer
Cette transformation prend en charge le filtrage des lignes en fonction des valeurs présentes dans une ou plusieurs colonnes. Comme les conditions du filtre dépendent du type de données de chaque colonne, vous pouvez filtrer les colonnes de chaîne à l’aide des conditions « contient » ou « ne contient pas ». Pour les colonnes numériques, vous pouvez utiliser les conditions « supérieur à » et « inférieur à ».

Lorsque la transformation **Filtrer** est appelée à partir du menu principal ou par un clic droit sur l’en-tête d’une colonne, vous pouvez dupliquer les lignes en échec dans un autre flux de données. Le flux de données principal affiche les lignes **incluses** dans le filtre, et le nouveau flux de données contient toutes les lignes qui en sont **exclues**.

## <a name="use-first-row-as-headers"></a>Utiliser la première ligne comme en-tête
Cette transformation promeut la première ligne du jeu de données comme nom de la colonne. Si la première ligne de certaines colonnes ne contient pas de données, un nom est généré automatiquement. Avec cette transformation, les données peuvent être importées à partir de la ligne 2 seulement. Selon le paramètre **Ignorer les lignes**, l’importation peut commencer encore plus bas dans le jeu de données. Vous pouvez également l’utiliser pour supprimer la promotion et utiliser uniquement les noms générés automatiquement.

## <a name="join"></a>Join
Cette transformation est utilisée pour joindre deux flux de données. Vous pouvez choisir le résultat de la jointure à afficher : les lignes dont la jointure a réussi, ou les lignes « gauche » ou « droite » dont la jointure est en échec.

La transformation **Joindre** démarre à partir d’un seul flux de données qui représente un côté de la jointure. Elle vous invite ensuite à sélectionner un autre flux de données pour désigner l’autre côté de la jointure. Une fois que vous avez sélectionné les deux flux, la transformation requiert qu’une seule colonne de chaque côté de la jointure soit sélectionnée. Si la jointure nécessite plusieurs colonnes, créez une colonne dérivée avant de démarrer la transformation pour créer une colonne concaténée à utiliser pour la jointure. Cette transformation propose des suggestions et, éventuellement, génère automatiquement la colonne dérivée.

Une fois la jointure effectuée, un diagramme Sankey de la jointure s’affiche. La largeur des différentes lignes représente le nombre de lignes parcourant cette partie du flux de la jointure. Dans le volet droit, vous pouvez sélectionner les lignes dont la jointure a réussi, ou les lignes gauche ou droite dont la jointure a échoué. Vous pouvez également choisir une branche unique.

## <a name="append-rows"></a>Ajouter des lignes
Cette transformation ajoute des données d’un autre flux de données dans le flux actuel. Elle mappe les colonnes par position pour ajouter les nouvelles lignes à la fin.

## <a name="append-columns"></a>Ajouter des colonnes
Cette transformation ajoute de nouvelles colonnes d’un autre flux de données au flux actuel. Elle ajoute les nouvelles colonnes sur la droite. Elle ne tente pas de remonter les données dans les lignes.

## <a name="summarize"></a>Agréger
Cette transformation calcule les agrégats pour la combinaison de valeurs uniques d’une ou de plusieurs colonnes sélectionnées. 

Les agrégats pris en charge sont les suivants : COUNT, SUM, MIN, MAX, MEAN, VARIANCE et STANDARD DEVIATION. La liste des agrégats pour une colonne spécifique est filtrée sur les seuls agrégats qui s’appliquent au type de données de la colonne. Les agrégats qui ne s’appliquent pas sont désactivés. Par exemple, il est impossible de calculer la MOYENNE d’une colonne de chaîne, mais il est possible de calculer les valeurs MINIMALE et MAXIMALE.

Lorsque l’éditeur est disponible, effectuez un glissement de l’en-tête de colonne vers le côté supérieur gauche du panneau dans lequel les colonnes à agréger sont affichées. Comme ce panneau est hiérarchique, vous pouvez effectuer des agrégats imbriqués. Le panneau de l’éditeur situé en haut à droite permet de sélectionner l’agrégat à appliquer à une colonne. Une colonne peut être agrégée une ou plusieurs fois. Dès qu’au moins un agrégat a été choisi, la grille située en bas à droite affiche un aperçu des données sous leur forme agrégée. 

Cette transformation est analogue à une requête `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Supprimer les doublons
Cette transformation supprime la totalité de la ligne qui contient des valeurs en double dans une ou plusieurs colonnes sélectionnées. Si aucune colonne n’est choisie, les lignes supprimées sont celles qui contiennent uniquement des valeurs de colonne identiques.

## <a name="sort"></a>Trier
Cette transformation trie les données. Ce tri peut être effectué sur les données d’une ou de plusieurs colonnes, par ordre croissant (valeur par défaut) ou décroissant (modifiable dans l’éditeur).

Cette transformation est analogue à une requête `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Transformations de sortie
Les transformations ci-après génèrent des données. Vous pouvez disposer de plusieurs blocs d’écriture dans un seul et même flux pour écrire les données à différentes étapes du flux.

### <a name="write-to-csv"></a>Écriture des données au format CSV
Cette transformation écrit les données au format CSV à partir de la position actuelle dans le flux de données. Elle contrôle l’emplacement (local ou distant) et différents paramètres du fichier.

### <a name="write-to-parquet"></a>Écriture des données au format Parquet
Cette transformation écrit les données au format Parquet à partir de la position actuelle dans le flux de données. Elle contrôle l’emplacement (local ou distant) et différents paramètres du fichier.

## <a name="script-based-transforms"></a>Transformations basées sur le script
Les transformations ci-après utilisent un script (Python) pour exécuter les fonctionnalités absentes du produit principal. 

>[!NOTE]
>Avant d’utiliser l’une de ces transformations, consultez l’article [Utilisation de l’extensibilité Python](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Ajouter une colonne (script)
Cette transformation ajoute une colonne aux données à l’aide d’une expression Python.
Pour plus d’informations, consultez l’article [Exemple Python pour dériver de nouvelles colonnes](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Filtre avancé (script)
Cette transformation permet d’écrire un filtre au niveau d’une ligne Python.
Pour plus d’informations, consultez l’article [Exemple d’expressions de filtre](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Transformation d’un flux de données (script)
Cette transformation applique Python à la totalité du jeu de données.
Pour plus d’informations, consultez l’article [Exemples de transformations de flux de données](data-prep-appendix7-sample-transform-data-flow-python.md).

Cette transformation peut également appliquer Python à la totalité d’une partition de données.
Pour plus d’informations, consultez l’article [Exemples de transformations de flux de données](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Écriture d’un flux de données (script)
Cette transformation utilise Python pour écrire l’intégralité d’un jeu de données.
Pour plus d’informations, consultez l’article [Exemple Python pour dériver de nouvelles colonnes](data-prep-appendix9-sample-destination-connections-python.md).



