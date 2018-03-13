---
title: "Champs personnalisés dans Azure Log Analytics | Microsoft Docs"
description: "La fonction Champs personnalisés de Log Analytics vous permet de créer vos propres champs autorisant les recherches, à partir des données Log Analytics qui s’ajoutent aux propriétés d’un enregistrement collecté.  Cet article décrit la création d’un champ personnalisé et fournit une procédure détaillée avec un exemple d’événement."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: bwren
ms.openlocfilehash: f0f3311f35f954f81560cad21e7f0e3bc850a094
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="custom-fields-in-log-analytics"></a>Champs personnalisés dans Log Analytics
La fonction **Champs personnalisés** de Log Analytics vous permet de compléter les enregistrements existants dans Log Analytics en leur ajoutant vos propres champs de recherche.  Les champs personnalisés sont renseignés automatiquement à partir des données extraites d’autres propriétés du même enregistrement.

![Vue d’ensemble des champs personnalisés](media/log-analytics-custom-fields/overview.png)

Par exemple, l’enregistrement ci-dessous contient des données utiles dans la description de l’événement.  L’extraction de ces données dans des propriétés séparées les rend disponibles pour des opérations de tri et de filtrage.

![Bouton Recherche de journal](media/log-analytics-custom-fields/sample-extract.png)

> [!NOTE]
> Dans la version préliminaire, votre espace de travail est limité à 100 champs personnalisés.  Cette limite pourra être relevée lorsque cette fonction sera disponible dans le commerce.
> 
> 

## <a name="creating-a-custom-field"></a>Création d’un champ personnalisé
Lorsque vous créez un champ personnalisé, Log Analytics doit savoir quelles données utiliser pour le renseigner.  Il fait donc appel à une technologie de Microsoft Research, appelée FlashExtract, afin d’identifier rapidement ces données.  Au lieu de vous obliger à fournir des instructions explicites, Log Analytics apprend à connaître les données à extraire des exemples que vous fournissez.

Les sections suivantes décrivent la procédure de création d’un champ personnalisé.  À la fin de cet article se trouve une procédure détaillée d’extraction.

> [!NOTE]
> Le champ personnalisé est renseigné lorsque des enregistrements correspondant aux critères spécifiés sont ajoutés à Log Analytics. Il ne s’affiche donc que sur les enregistrements collectés après la création du champ personnalisé.  Le champ personnalisé n’est pas ajouté aux enregistrements déjà présents dans le magasin de données lors de sa création.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Étape 1 : identifier les enregistrements qui contiendront le champ personnalisé
La première étape consiste à identifier les enregistrements qui recevront le champ personnalisé.  Commencez par une [recherche de journal standard](log-analytics-log-searches.md) , puis sélectionnez l’enregistrement qui va servir de modèle à Log Analytics.  Lorsque vous indiquez que vous allez extraire des données pour les placer dans un champ personnalisé, l’ **Assistant Extraction de champs** s’ouvre et vous permet d’affiner et de valider les critères.

1. Accédez à **Recherche de journal** et utilisez une [requête pour récupérer les enregistrements](log-analytics-log-searches.md) qui contiendront le champ personnalisé.
2. Sélectionnez l’enregistrement que Log Analytics utilisera comme modèle pour extraire les données à afficher dans le champ personnalisé.  Vous allez identifier les données à extraire de cet enregistrement, données que Log Analytics va utiliser pour déterminer la logique permettant de renseigner le champ personnalisé de tous les enregistrements similaires.
3. Cliquez sur le bouton à gauche d’une propriété textuelle de l’enregistrement, puis sélectionnez **Extraire les champs de**.
4. L’**Assistant Extraction de champs s’ouvre** et l’enregistrement que vous avez sélectionné s’affiche dans la colonne **Exemple principal**.  Le champ personnalisé sera défini pour les enregistrements ayant les mêmes valeurs dans les propriétés sélectionnées.  
5. Si la sélection ne correspond pas exactement à ce que vous souhaitez, sélectionnez d’autres champs pour affiner les critères.  Pour modifier les valeurs des critères, vous devez annuler l’opération et sélectionner un autre enregistrement correspondant aux critères souhaités.

### <a name="step-2---perform-initial-extract"></a>Étape 2 : effectuer l’extraction initiale.
Après avoir identifié les enregistrements qui contiendront le champ personnalisé, vous identifiez les données à extraire.  Log Analytics utilisera ces informations pour identifier des données similaires dans des enregistrements similaires.  Dans l’étape suivante, vous allez valider les résultats et fournir d’autres informations que Log Analytics va exploiter dans son analyse.

1. Mettez en surbrillance le texte de l’enregistrement exemple dont vous souhaitez renseigner dans le champ personnalisé.  Dans la boîte de dialogue qui s’affiche, indiquez le nom du champ et effectuez l’extraction initiale.  Les caractères **\_CF** sont automatiquement ajoutés à la fin.
2. Cliquez sur **Extraire** pour analyser les enregistrements collectés.  
3. Les sections **Résumé** et **Résultats** de la recherche affichent les résultats de l’extraction pour que vous puissiez vérifier qu’ils sont corrects.  **Résumé** affiche les critères utilisés pour identifier les enregistrements et le nombre de chacune des valeurs de données identifiées.  **Résultats de la recherche** fournit une liste détaillée des enregistrements correspondant aux critères.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Étape 3 : vérifier l’exactitude de l’extraction et créer le champ personnalisé
Une fois l’extraction initiale effectuée, Log Analytics affiche ses résultats en fonction des données déjà collectées.  Si les résultats semblent exacts, vous pouvez créer le champ personnalisé sans aucune opération supplémentaire.  Si tel n’est pas le cas, vous pouvez affiner les résultats afin d’améliorer la logique de Log Analytics.

1. Si des valeurs de l’extraction initiale sont incorrectes, cliquez sur l’icône **Modifier** en regard d’un enregistrement inexact, puis sélectionnez **Modifier cette mise en surbrillance** afin de modifier la sélection.
2. L’entrée est copiée dans la section **Exemples supplémentaires** sous **Exemple principal**.  Vous pouvez ajuster la mise en surbrillance pour aider Log Analytics à comprendre la sélection qu’il aurait dû faire.
3. Cliquez sur **Extraire** pour utiliser ces nouvelles informations et évaluer tous les enregistrements existants.  Les résultats peuvent être modifiés pour les enregistrements autres que celui dont vous venez de modifier la logique.
4. Continuez d’ajouter des corrections jusqu’à ce que tous les enregistrements de l’extraction identifient correctement les données à afficher dans le champ personnalisé.
5. Cliquez sur **Enregistrer l’extraction** lorsque vous êtes satisfait des résultats.  Le champ personnalisé est maintenant défini, mais il n’est encore ajouté à aucun enregistrement.
6. Attendez que des nouveaux enregistrements correspondant aux critères spécifiés soient collectés, puis relancez la recherche du journal. Les nouveaux enregistrements doivent contenir le champ personnalisé.
7. Utilisez le champ personnalisé comme n’importe quelle autre propriété d’enregistrement.  Il peut vous servir à agréger et grouper les données, et même à produire d’autres connaissances.

## <a name="viewing-custom-fields"></a>Affichage de champs personnalisés
Pour afficher une liste de l’ensemble des champs personnalisés de votre groupe de gestion, accédez au menu **Paramètres avancés** de votre espace de travail Log Analytics, dans le portail Azure.  Sélectionnez **Données** puis **Champs personnalisés** pour obtenir la liste de tous les champs personnalisés dans votre espace de travail.  

![Champs personnalisés](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Suppression d’un champ personnalisé
Il existe deux méthodes pour supprimer un champ personnalisé.  La première consiste à utiliser l’option **Supprimer** de chaque champ lorsque vous affichez la liste complète, comme indiqué ci-dessus.  L’autre consiste à extraire un enregistrement et à cliquer sur le bouton à gauche du champ.  Le menu affiche une option permettant de supprimer le champ personnalisé.

## <a name="sample-walkthrough"></a>Exemple de procédure
La section suivante décrit la procédure complète de création d’un champ personnalisé.  Cet exemple extrait le nom du service dans les événements Windows indiquant un changement d’état de service.  Il s’appuie sur les événements créés par le Gestionnaire de contrôle des services dans le Journal système des ordinateurs Windows.  Si vous souhaitez suivre cet exemple, vous devez [collecter des événements d’information du journal système](log-analytics-data-sources-windows-events.md).

Nous spécifions la requête suivante pour renvoyer tous les événements du Gestionnaire de contrôle des services dont l’ID d’événement est 7036, c’est-à-dire l’événement indiquant le démarrage ou l’arrêt d’un service.

![Requête](media/log-analytics-custom-fields/query.png)

Ensuite, nous sélectionnons un enregistrement ayant l’ID d’événement 7036.

![Enregistrement source](media/log-analytics-custom-fields/source-record.png)

Nous voulons afficher le nom du service dans la propriété **RenderedDescription** , donc nous cliquons sur le bouton en regard de cette propriété.

![Extraire des champs](media/log-analytics-custom-fields/extract-fields.png)

L’**Assistant Extraction de champs** s’ouvre. Les champs **EventLog** et **EventID** sont sélectionnés dans la colonne **Exemple principal**.  Cela indique que le champ personnalisé sera défini pour les événements du journal système ayant l’ID d’événement 7036.  Cela nous suffit, inutile de sélectionner d’autres champs.

![Exemple principal](media/log-analytics-custom-fields/main-example.png)

Nous mettons en surbrillance le nom du service dans la propriété **RenderedDescription** et utilisons **Service** pour identifier le nom du service.  Le nom du champ personnalisé sera **Service_CF**.

![Titre du champ](media/log-analytics-custom-fields/field-title.png)

Nous constatons que le nom du service est identifié correctement pour certains enregistrements, mais pas pour d’autres.   Les **Résultats de la recherche** montrent que cette partie du nom de **Carte de performance WMI** n’est pas sélectionnée.  La section **Résumé** montre que quatre enregistrements du service **DPRMA** comprenaient à tort un mot supplémentaire et que deux enregistrements identifiés **Programme d’installation pour les modules** au lieu de **Programme d’installation pour les modules Windows**.  

![Résultats de la recherche](media/log-analytics-custom-fields/search-results-01.png)

Commençons par l’enregistrement **Carte de performance WMI** .  Cliquez sur son icône de modification, puis sur **Modifier cette mise en surbrillance**.  

![Modifier la mise en surbrillance](media/log-analytics-custom-fields/modify-highlight.png)

Nous étendons la mise en surbrillance afin d’inclure le mot **WMI**, puis nous relançons l’extraction.  

![Exemple supplémentaire](media/log-analytics-custom-fields/additional-example-01.png)

Nous constatons que les entrées de **Carte de performance WMI** ont été corrigées, et que Log Analytics a utilisé ces informations pour corriger les enregistrements de **Programme d’installation pour les modules Windows**.  Cependant, dans la section **Résumé**, **DPMRA** n’est toujours pas identifié correctement.

![Résultats de la recherche](media/log-analytics-custom-fields/search-results-02.png)

Nous faisons défiler l’affichage jusqu’à un enregistrement avec le service DPMRA et suivons le même processus pour corriger cet enregistrement.

![Exemple supplémentaire](media/log-analytics-custom-fields/additional-example-02.png)

 Lorsque nous relançons l’extraction, nous voyons que tous les résultats sont désormais exacts.

![Résultats de la recherche](media/log-analytics-custom-fields/search-results-03.png)

Nous constatons que **Service_CF** est créé mais qu’il n’est encore ajouté à aucun enregistrement.

![Nombre initial](media/log-analytics-custom-fields/initial-count.png)

Après la collecte de nouveaux événements, nous pouvons voir que le champ **Service_CF** est désormais ajouté aux enregistrements qui répondent à nos critères.

![Résultats finaux](media/log-analytics-custom-fields/final-results.png)

Nous pouvons maintenant utiliser le champ personnalisé comme n’importe quelle propriété d’enregistrement.  Pour illustrer ce propos, nous créons une requête qui classe les enregistrements par le nouveau champ **Service_CF** pour identifier les services les plus actifs.

![Regrouper par requête](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [recherches de journaux](log-analytics-log-searches.md) pour générer des requêtes utilisant des champs personnalisés comme critères.
* Surveillez les [fichiers journaux personnalisés](log-analytics-data-sources-custom-logs.md) que vous analysez à l’aide de champs personnalisés.

