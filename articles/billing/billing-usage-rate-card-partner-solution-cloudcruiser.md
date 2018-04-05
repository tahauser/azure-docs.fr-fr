---
title: Intégration des API Microsoft Azure Billing par Cloud Cruiser | Microsoft Docs
description: Décrit la façon dont Cloud Cruiser, un partenaire de facturation de Microsoft Azure, a intégré les API Azure Billing à son produit.  Ces informations sont particulièrement utiles pour les clients Azure et Cloud Cruiser qui souhaitent utiliser ou essayer Cloud Cruiser pour Microsoft Azure Pack.
services: ''
documentationcenter: ''
author: tonguyen
manager: tonguyen
editor: ''
tags: billing
ms.assetid: b65128cf-5d4d-4cbd-b81e-d3dceab44271
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/09/2017
ms.author: mobandyo;sirishap;bryanla
ms.openlocfilehash: 8ddb81078e8019284c0481d4ea8d72253d3f0a5a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Intégration des API Microsoft Azure Billing par Cloud Cruiser
Cet article décrit la façon dont les informations collectées à partir des nouvelles API Microsoft Azure Billing sont utilisables dans Cloud Cruiser à des fins de simulation et d’analyse des coûts de flux de travail.

## <a name="azure-ratecard-api"></a>API Azure RateCard
L’API RateCard fournit des informations sur les tarifs Azure. Après vous être authentifié avec les informations d’identification appropriées, vous pouvez interroger l’API pour collecter les métadonnées relatives aux services disponibles sur Azure, ainsi que les tarifs associés à votre ID d’offre.

Voici un exemple de réponse de l’API affichant les prix de l’instance A0 (Windows) :

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Interaction entre Cloud Cruiser et l’API Azure RateCard
Cloud Cruiser peut utiliser les informations de l’API RateCard de différentes façons. Dans cet article, nous expliquons comment utiliser ces informations pour effectuer la simulation et l’analyse des coûts de la charge de travail IaaS (infrastructure as a service).

Pour illustrer ce cas d’utilisation, imaginons une charge de travail de plusieurs instances en cours d’exécution sur Microsoft Azure Pack (WAP). L’objectif est de simuler cette même charge de travail sur Azure et d’estimer les coûts liés à cette migration. La création de cette simulation nécessite l’exécution de deux tâches principales :

1. **Importation et traitement des informations de service collectées à partir de l’API RateCard.** Cette tâche est également effectuée sur les classeurs, dans lesquels les informations extraites de l’API RateCard sont transformées et publiées sous la forme d’une nouvelle formule tarifaire. Cette nouvelle formule est appliquée aux simulations pour estimer les prix Azure.
2. **Normalisation des services WAP et des services Azure pour IaaS.** Par défaut, les services WAP reposent sur des ressources individuelles (UC, taille de la mémoire, taille de disque, etc.), alors que les services Azure sont basés sur la taille d’instance (A0, A1, A2, etc.). Cette première tâche peut être effectuée par le moteur ETL (extraction, transformation et chargement) de Cloud Cruiser, désigné sous le terme de classeurs, dans lesquels ces ressources peuvent être regroupées dans des tailles d’instance, analogues aux services d’instance d’Azure.

### <a name="import-data-from-the-ratecard-api"></a>Importer les données de l’API RateCard
Les classeurs Cloud Cruiser automatisent la collecte et le traitement des informations de l’API RateCard.  Les classeurs ETL (extraction, transformation et chargement) vous permettent de configurer la collecte, la transformation et la publication de données dans la base de données Cloud Cruiser.

Chaque classeur peut comporter une ou plusieurs collections, ce qui vous permet de mettre en corrélation les informations de différentes sources afin de compléter ou d’enrichir les données d’utilisation. Les deux captures d’écran suivantes illustrent la création d’une *collection* dans un classeur existant et l’importation d’informations de l’API RateCard dans cette *collection* :

![Figure 1 : création d’une collection][1]

![Figure 2 : importation des données de la nouvelle collection][2]

Après avoir importé les données dans le classeur, nous pouvons créer plusieurs étapes et processus de transformation afin de modifier et de modéliser les données. Dans notre exemple, étant donné que nous sommes uniquement intéressés par l’infrastructure as a service (IaaS), nous pouvons utiliser les étapes de transformation pour supprimer les lignes ou enregistrements inutiles liés aux services autres que l’IaaS.

La capture d’écran suivante illustre les étapes de transformation permettant de traiter les données collectées à partir de l’API RateCard :

![Figure 3 : étapes de transformation pour le traitement des données collectées à partir de l’API RateCard][3]

### <a name="defining-new-services-and-rate-plans"></a>Définition de nouveaux services et formules tarifaires
Il existe différentes façons de définir des services sur Cloud Cruiser. L’une des possibilités consiste à importer les services à partir des données d’utilisation. Cette méthode est couramment utilisée lorsque l’on travaille avec des clouds publics, dans lesquels les services sont déjà définis par le fournisseur.

Une formule tarifaire est un ensemble de tarifs ou de prix qui peuvent être appliqués à différents services, en fonction de dates d’effet, ou à un groupe de clients, entre autres options. Il est également possible d’utiliser les formules tarifaires sur Cloud Cruiser pour créer des scénarios de simulation afin de comprendre la façon dont les modifications apportées aux services peuvent affecter le coût total d’une charge de travail.

Dans cet exemple, nous utilisons les informations de service de l’API RateCard pour définir de nouveaux services dans Cloud Cruiser. De la même façon, nous pouvons utiliser les tarifs associés aux services pour créer une autre formule tarifaire sur Cloud Cruiser.

À la fin du processus de transformation, il est possible de créer une autre étape et de publier les données de l’API RateCard sous la forme de nouveaux services et tarifs.

![Figure 4 : publication des données de l’API RateCard sous la forme de nouveaux services et tarifs][4]

### <a name="verify-azure-services-and-rates"></a>Vérifier les services et tarifs Azure
Après avoir publié les services et les tarifs, vous pouvez vérifier la liste des services importés dans l’onglet *Services* de Cloud Cruiser :

![Figure 5 : vérification des nouveaux services][5]

Dans l’onglet *Rate Plans* , vous pouvez vérifier la nouvelle formule tarifaire appelée « AzureSimulation » en fonction des tarifs importés depuis l’API RateCard.

![Figure 6 : vérification de la nouvelle formule tarifaire et des tarifs associés][6]

### <a name="normalize-wap-and-azure-services"></a>Normaliser les services WAP et Azure
Par défaut, WAP fournit des informations d’utilisation reposant sur l’utilisation des ressources de calcul, de mémoire et de réseau. Cloud Cruiser vous permet de définir des services directement basés sur l’attribution ou l’utilisation facturée à l’usage de ces ressources. Par exemple, vous pouvez définir un tarif de base pour chaque heure d’utilisation de l’unité centrale, ou facturer les Go de mémoire alloués à une instance.

Dans notre exemple, pour comparer les coûts entre WAP et Azure, nous devons agréger l’utilisation des ressources sur WAP sous forme d’offres groupées, qui peuvent ensuite être mappées sur les services Azure. Cette transformation peut être facilement implémentée dans les classeurs :

![Figure 7 : transformation des données WAP pour normaliser les services][7]

La dernière étape au niveau du classeur consiste à publier les données dans la base de données Cloud Cruiser. Au cours de cette étape, les données d’utilisation sont désormais regroupées sous forme de services (mappés sur les services Azure) et associées aux tarifs par défaut pour créer les frais.

Après avoir terminé le classeur, vous pouvez automatiser le traitement des données en ajoutant une tâche sur le planificateur et en spécifiant la fréquence et l’heure d’exécution du classeur.

![Figure 8 : planification du classeur][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Créer des rapports concernant la simulation et l’analyse des coûts de la charge de travail
Une fois les données d’utilisation collectées et les frais chargés dans la base de données Cloud Cruiser, nous pouvons utiliser le module Cloud Cruiser Insights pour créer la simulation des coûts de charge de travail dont nous avons besoin.

Afin d’illustrer ce scénario, nous avons créé le rapport suivant :

![Comparaison des coûts][9]

Le graphique supérieur affiche une comparaison des coûts par service, qui compare le prix de l’exécution de la charge de travail pour chaque service entre WAP (bleu foncé) et Azure (bleu clair).

Le graphique inférieur présente les mêmes données, mais réparties par service. Les coûts d’exécution des charges de travail de chaque service sur WAP et Azure, ainsi que leurs différences, apparaissent dans la barre Savings (verte).

## <a name="azure-usage-api"></a>API Azure Usage
### <a name="introduction"></a>Introduction
Microsoft a récemment introduit l'API Azure Usage qui permet aux abonnés d'extraire des données d'utilisation par programmation afin d'obtenir des informations sur leur consommation. Les clients Cloud Cruiser peuvent tirer parti du jeu de données plus riche disponible à l’aide de cette API.

Cloud Cruiser peut exploiter l’intégration dans l’API d’utilisation de plusieurs manières. La granularité (informations d'utilisation par heure) et les informations de métadonnées de ressources disponibles via l'API fournissent le jeu de données nécessaire pour prendre en charge des modèles de récupération des données de facturation ou de facturation interne flexibles. 

Dans ce didacticiel, nous présentons un exemple de la manière dont Cloud Cruiser peut exploiter les informations de l’API d’utilisation. Plus précisément, nous créons un groupe de ressources dans Azure, associons des balises pour la structure de compte, puis décrivons le processus d’extraction et de traitement des informations de balise dans Cloud Cruiser.

L’objectif final est de pouvoir créer des rapports comme le suivant et d’être en mesure d’analyser les coûts et la consommation sur la base de la structure de compte renseignée par les balises.

![Figure 10 : rapport avec répartitions à l’aide de balises][10]

### <a name="microsoft-azure-tags"></a>Balises Microsoft Azure
Les données disponibles à l'aide de l'API Azure Usage incluent des informations sur la consommation, mais également des métadonnées de ressources, notamment toutes les balises qui y sont associées. Les balises fournissent un moyen simple d'organiser vos ressources. Mais, pour les exploiter, vous devez vous assurer que :

* les balises sont correctement appliquées aux ressources au moment de l'approvisionnement ;
* les balises sont correctement utilisées dans le processus de récupération des données de facturation / de facturation interne pour associer l'utilisation à la structure de compte de l'organisation.

Ces deux conditions peuvent être difficiles à remplir, en particulier lorsqu’il existe un processus manuel d’approvisionnement ou de facturation. Les balises mal saisies, incorrectes ou même manquantes sont des réclamations courantes des clients lorsqu’ils utilisent des balises. Et ces erreurs peuvent rendre la facturation difficile.

Avec la nouvelle API Azure Usage, Cloud Cruiser peut extraire les informations sur les balises de ressources et résoudre les erreurs de balisage les plus courantes à l’aide d’un outil ETL sophistiqué appelé Classeurs. À travers la transformation exploitant la corrélation des expressions régulières et des données, Cloud Cruiser peut identifier des ressources à balisage incorrect et appliquer les balises appropriées. Il garantit ainsi une association correcte des ressources au consommateur.

Pour ce qui est de la tarification, Cloud Cruiser automatise le processus de récupération des données de facturation / de facturation interne et peut utiliser les informations de balisage pour associer l’utilisation au consommateur approprié (département, division, projet, etc.). Cette automatisation représente une amélioration majeure et  garantit un processus de facturation cohérent et vérifiable.

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Création d'un groupe de ressources avec des balises dans Microsoft Azure
La première étape de ce didacticiel consiste à créer un groupe de ressources sur le Portail Azure, puis à créer des balises à associer aux ressources. Pour cet exemple, nous créons les balises suivantes : département, environnement, propriétaire, projet.

La capture d’écran suivante montre un exemple de groupe de ressources avec les balises associées.

![Figure 11 : groupe de ressources avec des balises associées dans le portail Azure][11]

L'étape suivante consiste à transmettre les informations de l'API d'utilisation à Cloud Cruiser. L'API d'utilisation fournit actuellement des réponses au format JSON. Voici un exemple des données récupérées :

    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importer des données à partir de l'API d'utilisation dans Cloud Cruiser
Les classeurs Cloud Cruiser automatisent la collecte et le traitement des informations de l'API d'utilisation. Un classeur ETL (extraction, transformation et chargement) vous permet de configurer la collecte, la transformation et la publication de données dans la base de données Cloud Cruiser.

Chaque classeur peut comporter une ou plusieurs collections. Cela vous permet de mettre en corrélation les informations de différentes sources afin de compléter ou d’enrichir les données d’utilisation. Pour cet exemple, nous créons une nouvelle feuille dans le classeur modèle Azure (*UsageAPI)* et définissons une nouvelle *collection* pour importer des informations à partir de l’API d’utilisation.

![Figure 3 : données de l’API d’utilisation importées dans la feuille UsageAPI][12]

Notez que ce classeur comporte déjà d’autres feuilles pour importer des services depuis Azure (*ImportServices*) et traiter les informations sur la consommation de l’API de facturation (*PublishData*).

Ensuite, nous utilisons l’API d’utilisation pour remplir la feuille *UsageAPI*, puis corrélons les informations avec les données de consommation de l’API de facturation de la feuille *PublishData*.

### <a name="processing-the-tag-information-from-the-usage-api"></a>Traitement des informations de balisage depuis l'API d'utilisation
Après avoir importé les données dans le classeur, nous créons des étapes de transformation dans la feuille *UsageAPI* pour traiter les informations à partir de l’API. La première étape consiste à utiliser un processeur « Fractionner JSON » pour extraire les balises à partir d’un seul champ, puis à créer des champs pour chacune d’entre elles (département, projet, propriétaire et environnement).

![Figure 4 : créer des champs pour les informations de balise][13]

Notez que les informations de balise (zone jaune) ne figurent pas dans le service « Réseau », mais nous pouvons vérifier qu’il fait partie du même groupe de ressources en examinant le champ *ResourceGroupName* . Étant donné que nous avons les balises pour les autres ressources à partir de ce groupe de ressources, nous pouvons utiliser ces informations pour appliquer les balises manquantes à cette ressource ultérieurement dans le processus.

L'étape suivante consiste à créer une table de choix associant les informations dans les balises au *ResourceGroupName*. Cette table de choix est utilisée à l’étape suivante pour enrichir les données de consommation d’informations de balises.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Ajout des informations de balise aux données de consommation
Maintenant que nous pouvons passer à la feuille *PublishData* , qui traite les informations de la consommation de l'API de facturation, puis ajouter les champs extraits à partir des balises. Pour cela, il suffit d'effectuer une recherche dans la table de choix créée à l'étape précédente en utilisant le *ResourceGroupName* comme clé de recherche.

![Figure 5 : remplissage de la structure de compte avec les informations des recherches][14]

Notez que les champs de structure de compte appropriés pour le service « Réseau » ont été appliqués, ce qui a corrigé le problème des balises manquantes. Nous également renseigné les champs de structure de compte pour les ressources autres que notre groupe de ressources cible avec « Other » (Autre), afin de les différencier des rapports.

Nous devons maintenant simplement ajouter une étape pour publier les données d’utilisation. Au cours de cette étape, les taux appropriés de chaque service définis dans notre formule tarifaire sont appliqués aux informations d’utilisation et la charge en résultant est chargée dans la base de données.

Et vous n'avez à exécuter ce processus qu'une seule fois ! Lorsque le classeur est terminé, vous n’avez qu’à simplement l’ajouter au planificateur, et il s’exécute toutes les heures ou tous les jours à l’heure planifiée. Il ne s’agit plus alors que de créer des rapports ou de personnaliser les rapports existants, afin d’analyser les données pour obtenir des informations pertinentes sur votre utilisation du cloud.

### <a name="next-steps"></a>Étapes suivantes
* Pour découvrir la procédure détaillée de création de classeurs et de rapports Cloud Cruiser, reportez-vous à la [documentation](http://docs.cloudcruiser.com/) en ligne de Cloud Cruiser (ID de connexion valide requis).  Pour obtenir plus d’informations sur Cloud Cruiser, contactez [info@cloudcruiser.com](mailto:info@cloudcruiser.com).
* Pour découvrir une présentation des API Azure Resource Usage et RateCard, voir [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure](billing-usage-rate-card-overview.md) .
* Pour plus d’informations sur ces deux API, qui font partie intégrante de l’ensemble d’API fourni par Azure Resource Manager, consultez la [Référence des API REST Azure Billing](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) .
* Si vous souhaitez vous plonger directement dans l'exemple de code, consultez nos exemples de code d'API de facturation Microsoft Azure sur [Exemples de code Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>En savoir plus
* Pour plus d’informations sur Azure Resource Manager, voir l’article [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

<!--Image references-->

[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figure 1 : création d’une collection"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figure 2 : importation des données de la nouvelle collection"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figure 3 : étapes de transformation pour le traitement des données collectées à partir de l’API RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figure 4 : publication des données de l’API RateCard sous la forme de nouveaux services et tarifs"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figure 5 : vérification des nouveaux services"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figure 6 : vérification de la nouvelle formule tarifaire et des tarifs associés"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figure 7 : transformation des données WAP pour normaliser les services"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figure 8 : planification du classeur"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figure 9 : exemple de rapport pour le scénario de comparaison des coûts de la charge de travail"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Figure 10 : rapport avec répartitions à l’aide de balises"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Figure 11 : groupe de ressources avec des balises associées dans le portail Azure"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Figure 12 : données de l’API d’utilisation importées dans la feuille UsageAPI"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Figure 13 : créer des champs pour les informations de balise"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Figure 14 : remplissage de la structure de compte avec les informations des recherches"
