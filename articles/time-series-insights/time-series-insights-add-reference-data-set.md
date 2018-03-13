---
title: "Guide d’ajout d’un jeu de données de référence à votre environnement Azure Time Series Insights"
description: "Cet article décrit comment ajouter un jeu de données de référence pour augmenter le volume de données dans votre environnement Azure Time Series Insights."
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: MicrosoftDocs/tsidocs
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 02/15/2018
ms.openlocfilehash: e0d11f253d5aa143ff636c4dc8dff7665a80360e
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Créer un jeu de données de référence pour votre environnement Time Series Insights à l’aide du portail Azure

Cet article décrit comment ajouter un jeu de données de référence à votre environnement Azure Time Series Insights. Les données de référence sont utiles, joignez-les à votre source de données pour augmenter les valeurs.

Un jeu de données de référence est une collection d’éléments qui augmente les événements issus de votre source d’événements. Le moteur d’entrée Time Series Insights associe chaque événement de votre source d’événements à la ligne de données correspondante dans votre jeu de données de référence. Cet événement ajouté est ensuite disponible pour la requête. Cette jointure repose sur les colonnes de clé privée définies dans votre jeu de données de référence.

Les données de référence ne sont pas jointes rétroactivement. Cela signifie que seules les données d’entrée actuelles et futures sont mises en correspondance et jointes à l’ensemble de données de référence, après configuration et téléchargement.

## <a name="add-a-reference-data-set"></a>Ajouter un jeu de données de référence

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Recherchez votre environnement Time Series Insights existant. Cliquez sur **Toutes les ressources** dans le menu de gauche du portail Azure. Sélectionnez votre environnement Time Series Insights.

3. Sélectionnez la page **Vue d’ensemble**. Localisez l’**URL de l’explorateur Time Series Insights** et ouvrez le lien.  

   Affichez l’explorateur de votre environnement TSI.

4. Développez le sélecteur d’environnement dans l’explorateur TSI. Choisissez l’environnement actif. Sur la page de l’explorateur, sélectionnez l’icône des données de référence en haut à droite.

   ![Utiliser des données de référence](media/add-reference-data-set/add_reference_data.png)

5. Cliquez sur le bouton **+ Ajouter un jeu de données** pour ajouter un nouveau jeu de données.

   ![Ajouter un jeu de données](media/add-reference-data-set/add_data_set.png)

6. Sur la page **Nouveau jeu de données de référence**, choisissez le format des données : 
   - Choisissez **CSV** pour des données délimitées par des virgules. La première ligne est traitée comme une ligne d’en-tête. 
   - Choisissez **Tableau JSON** pour des données au format JSON.

   ![Choisissez le format des données.](media/add-reference-data-set/add_data.png)

7. Fournissez les données via l’une des deux méthodes suivantes :
   - Collez les données dans l’éditeur de texte. Ensuite, cliquez sur le bouton **Analyser les données de référence**.
   - Cliquez sur le bouton **Choisir un fichier** pour ajouter des données à partir d’un fichier texte local. 

   Par exemple, collez des données CSV : ![Données CSV collées](media/add-reference-data-set/csv_data_pasted.png)

   Par exemple, collez des données de tableau JSON : ![Coller des données JSON](media/add-reference-data-set/json_data_pasted.png)

   En cas d’erreur lors de l’analyse des valeurs de données, l’erreur s’affiche en rouge au bas de la page : `CSV parsing error, no rows extracted`.

8. Une fois que les données ont été analysées correctement, une grille de données est affichée. Elle contient les colonnes et les lignes qui représentent les données.  Passez en revue la grille de données pour garantir son exactitude.

   ![Utiliser des données de référence](media/add-reference-data-set/parse_data.png)

9. Passez en revue chaque colonne pour voir le type de données pris par défaut et le modifier si nécessaire.  Sélectionnez le symbole de type de données dans l’en-tête de colonne : **#** pour double (données numériques), **T|F** pour booléen ou **Abc** pour une chaîne.

   ![Choisissez les types de données dans les en-têtes de colonne.](media/add-reference-data-set/choose_datatypes.png)

10. Renommez les en-têtes de colonnes si nécessaire. Le nom de la colonne de clé est nécessaire pour joindre à la propriété correspondante dans votre source d’événements. Assurez-vous que les noms de colonne de clé de données de référence correspondent exactement au nom d’événement pour vos données entrantes, y compris la casse. Les noms des colonnes ne contenant pas de clés sont utilisés pour augmenter les données entrantes avec les valeurs de données de référence correspondantes.

11. Cliquez sur **Ajouter une ligne** ou **Ajouter une colonne** pour ajouter plusieurs valeurs de données de référence, si nécessaire.

12. Saisissez une valeur dans le champ **Filtrer les lignes...**  pour passer en revue des lignes spécifiques en fonction des besoins. Ce filtre est utile pour l’examen des données, mais il n’est pas appliqué lors du chargement des données.
 
13. Nommez le jeu de données en renseignant le champ **Nom du jeu de données** au-dessus de la grille de données.

   ![Nommez du jeu de données.](media/add-reference-data-set/name_reference_dataset.png)

14. Indiquez la colonne de **clé primaire** du jeu de données en sélectionnant la liste déroulante au-dessus de la grille de données.

   ![Sélectionnez les colonnes de clé.](media/add-reference-data-set/set_primary_key.png)

   Si vous le souhaitez, cliquez sur le bouton **+** pour ajouter une colonne de clé secondaire, comme une clé primaire composite. Si vous devez annuler la sélection, choisissez la valeur vide de la liste déroulante pour supprimer la clé secondaire.

15.  Pour télécharger les données, cliquez sur le bouton **Télécharger les lignes**.

   ![Télécharger](media/add-reference-data-set/upload_rows.png)

   La page confirme la fin du téléchargement et affiche le message **Jeu de données chargé**.

## <a name="next-steps"></a>Étapes suivantes
* [Gérez les données de référence](time-series-insights-manage-reference-data-csharp.md) par programme.
* Pour obtenir des informations de référence d’API complètes, consultez le document [API de données de référence](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).
