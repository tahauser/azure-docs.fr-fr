---
title: "Forum Aux Questions Azure Data Catalog | Microsoft Docs"
description: "Forum Aux Questions sur Azure Data Catalog, y compris sur les fonctionnalités de détection de source de données, d’annotation et de gestion."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 1f69912e654ac243a0c4f30426d17ce4199e498f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Forum Aux Questions Azure Data Catalog
Cet article fournit des réponses aux questions fréquemment posées sur le service Azure Data Catalog.

## <a name="what-is-azure-data-catalog"></a>Qu’est-ce qu’Azure Data Catalog ?
Data Catalog est un service entièrement géré, hébergé par Microsoft Azure, qui sert de système d’inscription et de détection des sources de données d’entreprise. Grâce à Data Catalog, les utilisateurs (analystes, scientifiques des données et développeurs) peuvent inscrire, découvrir, comprendre et consommer les sources de données.

## <a name="what-customer-challenges-does-it-solve"></a>Quels sont les défis clients que ce service permet de résoudre ?
Data Catalog résout les problèmes de découverte des sources de données et des « données cachées », de façon à ce que les utilisateurs puissent découvrir et comprendre les sources de données d’entreprise.

## <a name="what-are-its-target-audiences"></a>Quel est le public cible ?
Data Catalog est conçu pour les utilisateurs, techniciens ou non, notamment :

* les développeurs de données et les professionnels du décisionnel et de l’analyse : personnes chargées de produire des données et du contenu d’analyse que d’autres utiliseront ;
* les gestionnaires de données : personnes qui disposent de connaissances sur les données, ce qu’elles signifient et comment elles sont censées être utilisées ;
* les consommateurs de données : personnes qui doivent être en mesure de découvrir, de comprendre et de se connecter facilement aux données dont elles ont besoin pour effectuer leur travail à l’aide de l’outil de leur choix ;
* l’équipe informatique centrale : personnes qui doivent rendre détectables des centaines de sources de données pour les utilisateurs professionnels et qui ont besoin de superviser l’utilisation de données et les personnes utilisant ces données.

## <a name="what-is-its-availability-by-region"></a>Quelle est la disponibilité du service par région ?
Les services Data Catalog sont actuellement disponibles dans les centres de données suivants :

* États-Unis de l’Ouest
* Est des États-Unis
* Europe de l'Ouest
* Europe du Nord
* Est de l’Australie
* Asie du Sud-Est

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Quelles est la limite en termes de nombre de ressources de données ?
L’édition gratuite de Data Catalog est limitée à 5 000 ressources de données inscrites.

L’édition Standard de Data Catalog prend en charge jusqu’à 100 000 ressources de données inscrites.

Tout objet inscrit dans Data Catalog, y compris les tableaux, vues, fichiers et rapports, est comptabilisé comme une ressource de données.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Quels sont les types de sources et de ressources de données pris en charge ?
Pour obtenir la liste des sources de données actuellement prises en charge, reportez-vous au [DSR Data Catalog](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Comment demander la prise en charge d’une autre source de données ?
Pour demander des fonctionnalités et envoyer d’autres commentaires, rendez-vous dans la section [Data Catalog sur les forums de commentaires Azure](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="how-do-i-get-started-with-data-catalog"></a>Comment démarrer avec Data Catalog ?
La meilleure façon de commencer est de consulter la section [Prise en main de Data Catalog](data-catalog-get-started.md). Cet article présente de bout en bout les fonctionnalités intégrées au service.

## <a name="how-do-i-register-my-data"></a>Comment inscrire mes données ?
Pour inscrire vos données dans Data Catalog :
1. Dans le portail Azure Data Catalog, dans la zone **Publier**, démarrez l’outil d’inscription Azure Data Catalog. 
2. Dans l’outil d'inscription de la source de données Data Catalog, connectez-vous avec les mêmes identifiants que ceux utilisés pour accéder au portail Data Catalog.
3. Sélectionnez la source de données et les ressources spécifiques que vous souhaitez inscrire.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Quelles propriétés sont extraites pour les ressources de données qui sont inscrites ?
Les propriétés spécifiques diffèrent selon la source de données, mais en général le service de publication Data Catalog extrait les informations suivantes :

* Nom de la ressource
* Type de ressource
* Description de la ressource
* Noms de l’attribut/de la colonne
* Types de données de l'attribut/de la colonne
* Description de l’attribut/de la colonne

> [!IMPORTANT]
> L’inscription de ressources de données auprès de Data Catalog n’a pas pour effet de déplacer ou de copier vos données dans le cloud. L’inscription de ressources à partir d’une source de données copie les métadonnées des ressources vers Azure, mais les données restent dans l’emplacement de la source de données existante. La seule exception à cette règle est quand vous décidez de charger des enregistrements ou un profil de données de la version préliminaire lors de l’inscription de ressources. Lors de l’inclusion d’une version préliminaire, jusqu’à 20 enregistrements sont copiés à partir de chaque ressource et stockés sous forme d’instantané dans Data Catalog. Lorsque vous incluez un profil de données, les informations d’agrégation sont calculées et incluses dans les métadonnées stockées dans le catalogue. Les informations d’agrégation peuvent inclure la taille des tables, le pourcentage de valeurs null par colonne ou les valeurs minimales, maximales et moyennes pour les colonnes. 
>
>

> [!NOTE]
> Pour les sources de données telles que SQL Server Analysis Services qui ont une propriété **Description** de première classe, l'outil d'inscription de la source de données Data Catalog extrait la valeur de cette propriété. Pour les bases de données relationnelles SQL Server, qui ne possèdent pas de propriété **Description** de première classe, l'outil d'inscription de la source de données Data Catalog extrait la valeur de la propriété étendue **ms_description** pour les objets et les colonnes. Pour plus d’informations, consultez la page [Utilisation de propriétés étendues sur les objets de base de données](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Combien de temps faut-il pour que les ressources nouvellement inscrites apparaissent dans le catalogue ?
Après avoir inscrit des ressources auprès de Data Catalog, il peut s’écouler un délai de 5 à 10 secondes avant qu’elles apparaissent dans le portail Data Catalog.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Comment annoter et enrichir les métadonnées pour mes ressources de données inscrites ?
La méthode la plus simple pour fournir des métadonnées pour des ressources inscrites consiste à sélectionner la ressource dans le portail Data Catalog, puis à entrer les valeurs dans le volet des propriétés ou du schéma de l’objet sélectionné.

Vous pouvez également fournir des métadonnées, telles que des experts et des balises, pendant le processus d'inscription. Les valeurs que vous fournissez dans le service de publication Data Catalog s’appliquent à toutes les ressources inscrites à ce moment-là. Pour afficher les objets récemment inscrits dans le portail pour des annotations supplémentaires, sélectionnez le bouton **Afficher le portail** dans le dernier écran de l'outil d'inscription de la source de données Data Catalog.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Comment supprimer mes objets de données inscrits ?
Vous pouvez supprimer un objet de Data Catalog en sélectionnant l’objet dans le portail, puis en cliquant sur le bouton **Supprimer**. La suppression de l’objet supprime ses métadonnées de Data Catalog, mais n’affecte pas la source de données sous-jacente.

## <a name="what-is-an-expert"></a>Qu’est-ce qu’un expert ?
Un expert est une personne qui a un point de vue éclairé sur un objet de données. Un objet peut avoir plusieurs experts. Il n’est pas nécessaire que l’expert soit le « propriétaire » d’un objet ; il s’agit simplement d’une personne qui sait comment les données peuvent et doivent être utilisées.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Comment partager des informations avec l’équipe Data Catalog si je rencontre des problèmes ?
Pour signaler des problèmes, partager des informations et poser des questions, rendez-vous dans le [forum Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Le catalogue fonctionne-t-il avec une autre source de données qui m’intéresse ?
Nous travaillons activement à l’ajout de sources de données supplémentaires à Data Catalog. Si vous souhaitez qu’une source de données spécifique soit prise en charge, suggérez-la (ou faites part de votre accord si elle a déjà été suggérée) en vous rendant dans la section [Data Catalog sur les forums de commentaires Azure](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Comment Azure Data Catalog est-il lié au catalogue de données dans Power BI pour Office 365 ?
Vous pouvez considérer Azure Data Catalog comme une évolution de Data Catalog dans Power BI. À partir du printemps 2017, Azure Data Catalog est utilisé pour activer le partage et la découverte de requêtes dans Excel 2016 et Power Query pour Excel. Les fonctions Data Catalog d’Excel sont disponibles pour les utilisateurs disposant de licences Power BI Pro.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Quelles sont les autorisations dont j’ai besoin pour inscrire des ressources auprès de Data Catalog ?
Pour exécuter l’outil de référencement pour Data Catalog, vous avez besoin d’autorisations sur la source de données qui vous permet de lire les métadonnées de la source. Pour inclure également un aperçu, vous devez disposer des autorisations qui vous permettent de lire les données à partir des objets en cours d’inscription.

Data Catalog permet également aux administrateurs de catalogue d'empêcher certains utilisateurs et groupes d'ajouter des métadonnées au catalogue. Pour plus d’informations, consultez [Guide pratique pour sécuriser l’accès à un catalogue de données et des ressources de données](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Data Catalog sera-t-il également disponible pour un déploiement local ?
Data Catalog est un service cloud qui peut fonctionner avec des sources de données cloud et locales, offrant ainsi une solution de détection de sources de données hybrides. Aucune version du service Data Catalog s’exécutant localement n’est actuellement prévue.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Est-il possible d’extraire davantage de métadonnées/des métadonnées plus riches à partir de sources de données que j’inscris ?
Nous travaillons activement au développement des fonctionnalités de Data Catalog. Si vous souhaitez que des métadonnées supplémentaires soient extraites à partir de la source de données pendant l’inscription, suggérez-les (ou votez en leur faveur si elles ont déjà été suggérées) dans la section [Data Catalog sur les forums de commentaires Azure](https://feedback.azure.com/forums/906052-data-catalog). 

Si vous souhaitez inclure des métadonnées de colonne/schéma, des aperçus ou des profils de données pour les sources de données dans lesquelles ces métadonnées ne sont pas extraites par l’outil d’inscription, vous pouvez utiliser l’API Data Catalog pour ajouter ces métadonnées. Pour plus d’informations, consultez [API REST Azure Data Catalog](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Comment restreindre la visibilité des ressources de données inscrites, afin que seules certaines personnes puissent les découvrir ?
Sélectionnez les ressources de données dans Data Catalog, puis cliquez sur le bouton **Appropriation**. Les propriétaires de ressources de données dans Data Catalog peuvent modifier les paramètres de visibilité pour autoriser tous les utilisateurs à découvrir les ressources leur appartenant, ou pour limiter la visibilité à des utilisateurs spécifiques. Pour plus d’informations, consultez [Gérer les ressources de données dans Azure Data Catalog](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Comment mettre à jour l’inscription d’une ressource de données pour que les modifications apportées dans la source de données soient reflétées dans le catalogue ?
Pour mettre à jour les métadonnées pour les ressources de données qui sont déjà inscrites dans le catalogue, réinscrivez simplement la source de données qui contient les ressources. Les modifications apportées à la source de données, telles que l’ajout ou la suppression de colonnes de tables ou de vues, sont actualisées dans le catalogue, mais les annotations fournies par les utilisateurs sont conservées.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Il n’y a pas de réponse à ma question ici. Où puis-je en trouver une ?
Rendez-vous dans le [forum Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Les questions qui y sont posées se retrouveront ici.
