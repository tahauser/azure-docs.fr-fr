---
title: "Vue d’ensemble de Azure Analysis Services | Microsoft Docs"
description: "Obtenez une vue d’ensemble d’Analysis Services dans Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: c71d6fd7e3542a22edf093b0c9fccbd5185e0f85
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-analysis-services-overview"></a>Vue d’ensemble de Azure Analysis Services
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services fournit sur le cloud des données de modélisation de qualité professionnelle. Il s’agit d’une plateforme en tant que service (PaaS) entièrement gérée et intégrée aux services de plateforme de données Azure. 

Analysis Services vous permet de regrouper et combiner des données provenant de plusieurs sources, de définir des mesures et de sécuriser vos données dans un seul modèle de données sémantique approuvé. Le modèle de données offre à vos utilisateurs un moyen plus simple et plus rapide de parcourir d’importants volumes de données avec des applications clientes telles que Power BI, Excel, Reporting Services, ainsi que des applications tierces et personnalisées.

![Sources de données](./media/analysis-services-overview/aas-overview-data-sources.png)

Regardez [cette vidéo](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4) pour découvrir comment Azure Analysis Services s’intègre aux fonctionnalités de décisionnel globales de Microsoft et comment vous pouvez tirer parti du passage au cloud de vos modèles de données.

## <a name="built-on-sql-server-analysis-services"></a>Basé sur SQL Server Analysis Services
Azure Analysis Services est compatible avec de nombreuses fonctionnalités d’exception déjà intégrées à SQL Server Analysis Services Enterprise Edition. Azure Analysis Services prend en charge les modèles tabulaires aux [niveaux de compatibilité](analysis-services-compat-level.md) 1200 et 1400. Les partitions, la sécurité au niveau des lignes, les relations bidirectionnelles et les traductions sont toutes prises en charge. Des modes en mémoire et des modes DirectQuery signifient des requêtes ultra-rapides sur des jeux de données massifs et complexes.

Les modèles tabulaires offrent un développement rapide et sont hautement personnalisables. Pour les développeurs, les modèles tabulaires incluent le modèle d’objet tabulaire (TOM) pour décrire les modèles d’objet. Le modèle d’objet tabulaire est affiché au format JSON via le langage [TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) et le langage de définition de données AMO via l’espace de noms [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx).

## <a name="better-with-azure"></a>Meilleur avec Azure
Azure Analysis Services s’intègre à de nombreux services Azure qui vous permettent de créer des solutions d’analyse élaborées. Son intégration à [Azure Active Directory](../active-directory/active-directory-whatis.md) assure un accès sécurisé en fonction du rôle à vos données critiques. L’intégration aux pipelines [Azure Data Factory](../data-factory/introduction.md) est possible grâce à l’inclusion d’une activité qui charge des données dans le modèle. [Azure Automation](../automation/automation-intro.md) et [Azure Functions](../azure-functions/functions-overview.md) sont utilisables pour une orchestration légère de modèles à l’aide d’un code personnalisé.

## <a name="get-up-and-running-quickly"></a>Opérationnel rapidement
Dans le portail Azure, vous pouvez [créer un serveur](analysis-services-create-server.md) en quelques minutes. Et, avec les [modèles](../azure-resource-manager/resource-manager-create-first-template.md) Azure Resource Manager et PowerShell, vous pouvez approvisionner des serveurs à l’aide d’un modèle déclaratif. Un même modèle vous permet de déployer plusieurs services, ainsi que d’autres composants Azure tels que des comptes de stockage et Azure Functions. 

Une fois que vous avez créé un serveur, vous pouvez créer un modèle tabulaire directement dans le portail Azure. La nouvelle [fonctionnalité de concepteur web](analysis-services-create-model-portal.md) (préversion) vous permet de vous connecter à une base de données Azure SQL ou à une source de données Azure SQL Data Warehouse, ou d’importer un fichier .pbix Power BI Desktop. Les relations entre les tables sont créées automatiquement et vous pouvez créer les mesures ou éditer le fichier model.bim au format json directement depuis votre navigateur.

## <a name="scale-to-your-needs"></a>Évolutif selon vos besoins

### <a name="the-right-tier-when-you-need-it"></a>Le niveau approprié, quand vous en avez besoin

Azure Analysis Services est disponible pour les niveaux Développeur, De base et Standard. Dans chaque niveau, les coûts de plan varient en fonction de la puissance de traitement, des QPUs et de la taille de la mémoire. Lorsque vous créez un serveur, vous sélectionnez un plan au sein d’un niveau. Vous pouvez modifier les plans vers le haut ou vers le bas au sein du même niveau, ou le passer à un niveau supérieur, mais vous ne pouvez pas le rétrograder d’un niveau supérieur à un niveau inférieur.

Augmentez la puissance, réduisez la puissance ou suspendez votre serveur. Utilisez le portail Azure ou obtenez le contrôle total à la volée à l’aide de PowerShell. Vous paierez uniquement pour ce que vous utiliserez. Pour plus d’informations sur les différents plans et niveaux, et pour utiliser le calculateur de prix permet afin de déterminer le plan adapté pour vous, consultez la [Tarification Analysis Services Azure](https://azure.microsoft.com/pricing/details/analysis-services/).

### <a name="scale-out-resources-for-fast-query-responses"></a>Ressources évolutives pour des réponses aux requêtes rapides

Grâce à l’évolutivité d’Azure Analysis Services, les requêtes des clients sont distribuées entre plusieurs *réplicas de requête* dans un pool de requêtes. Les réplicas de requête contiennent des copies synchronisées de vos modèles tabulaires. Grâce à la répartition de la charge de travail de requête, les temps de réponse lors de charges de travail de requête élevées peuvent être réduits. Les opérations de traitement du modèle peuvent être séparées du pool de requêtes, garantissant ainsi que les requêtes des clients ne soient pas affectées par les opérations de traitement. Vous pouvez créer un pool de requêtes comportant jusqu’à sept réplicas de requête supplémentaires (pour un total de huit, votre serveur compris). 

Comme pour la modification de votre niveau, vous pouvez faire évoluer les réplicas de requête selon vos besoins. Configurez l’évolution dans le portail ou à l’aide d’API REST. Pour en savoir plus, voir [Évolution d’Azure Analysis Services](analysis-services-scale-out.md).

## <a name="keep-your-data-close"></a>Conserver vos données à proximité
Les serveurs Analysis Services Azure peuvent être créés dans les [régions Azure](https://azure.microsoft.com/regions/) suivantes :

| Amérique | Europe | Asie-Pacifique |
|----------|--------|--------------|
|  Sud du Brésil<br> Centre du Canada<br> Est des États-Unis 2<br> Centre-Nord des États-Unis<br> États-Unis - partie centrale méridionale<br> Centre-Ouest des États-Unis<br> États-Unis de l’Ouest | Europe du Nord<br> Sud du Royaume-Uni<br> Europe de l'Ouest |   Sud-est de l’Australie<br> Est du Japon<br> Asie du Sud-Est<br> Inde occidentale  |

De nouvelles régions continuent d’être ajoutées, alors la liste peut être incomplète. Vous choisissez un emplacement lorsque vous créez votre serveur dans le portail Azure ou à l’aide des modèles Azure Resource Manager. Pour optimiser au mieux vos performances, choisissez l’emplacement le plus proche de votre plus grande base d’utilisateurs. Garantissez-vous une [haute disponibilité](analysis-services-bcdr.md) en déployant vos modèles sur des serveurs redondants dans plusieurs régions.

## <a name="migrate-your-existing-tabular-models"></a>Migrer vos modèles tabulaires existants
Si vous disposez déjà de solutions locales existantes pour les modèles SQL Server Analysis Services, vous pouvez migrer vers Azure Analysis Services sans réaliser de modification importante. Pour effectuer la migration, vous pouvez utiliser SSDT pour déployer votre modèle sur votre serveur. Ou, dans SSMS, vous pouvez utiliser la fonction de sauvegarde et restauration ou TMSL.

Si vous avez des sources de données locales, vous devez installer et configurer une [Passerelle de données locale](analysis-services-gateway.md). Si vos rôles et vos membres de rôle sont déjà configurés, vos rôles migreront mais vous devrez rajouter les membres de rôle à l’aide de SSMS ou de PowerShell.

## <a name="connect-to-popular-data-sources"></a>Se connecter aux sources de données courantes
Azure Analysis Services prend en charge la [connexion aux sources de données](analysis-services-datasource.md) locales dans votre organisation et dans le cloud. Combinez des données provenant de sources de données locales et d’un cloud pour une solution hybride. 

Les nouveaux modèles tabulaires 1400 utilisent la fonctionnalité moderne d’obtention des données dans SSDT, basée sur le langage de requête de formule M. Avec Get Data, vous avez plus de transformation des données et de fonctionnalités de mashup, et vous avez la possibilité de créer et modifier vos propres requêtes avancées en langage de formule M . Par exemple, avec les modèles tabulaires 1400, vous pouvez modéliser sur les fichiers de données dans Azure Blob Storage.

## <a name="use-the-tools-you-already-know"></a>Utiliser les outils que vous connaissez déjà

![Outils de développement BI](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>SQL Server Data Tools pour Visual Studio (SSDT)
Développez et déployez gratuitement des modèles avec [SQL Server Data Tools (SSDT) pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). SSDT inclut des modèles de projet Analysis Services opérationnels rapidement. SSDT inclut maintenant la requête de source de données et les fonctionnalités de mashup modernes Get Data pour les modèles de tabulaire 1400. Si connaissez déjà Get Data dans Power BI Desktop et Excel 2016, vous devriez déjà savoir à quel point il est aisé de créer des requêtes de source de données hautement personnalisables.

#### <a name="sql-server-management-studio"></a>SQL Server Management Studio
Gérez vos serveurs et bases de données model à l’aide de [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Connectez-vous à vos serveurs dans le cloud. Exécutez les scripts TMSL directement à partir de la fenêtre de requête XMLA et automatisez des tâches à l’aide des scripts TMSL. De nouvelles fonctionnalités sont régulièrement implémentées : SSMS est mis à jour tous les mois.

#### <a name="powershell"></a>PowerShell
Les tâches de gestion des ressources du serveur telles que la création d’un serveur, l’interruption ou la reprise des opérations du serveur ou la modification du niveau de service utilisent les applets de commande Azure Resource Manager (AzureRM). D’autres tâches de gestion des bases de données telles que l’ajout ou la suppression de membres de rôle, le traitement, ou l’exécution de scripts TMSL utilisent l’applet de commande dans le module Sqlserver. Les modules AzureRM et SQLServer sont tous les deux disponibles dans la [Galerie PowerShell](https://www.powershellgallery.com/).


## <a name="your-data-is-secure"></a>Sécuriser vos données
![Visualisations de données](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>Authentification
L’authentification utilisateur pour Azure Analysis Services est gérée par [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). Lors d’une tentative de connexion à une base de données Azure Analysis Services, les utilisateurs utilisent une identité de compte d’entreprise ayant accès à la base de données à laquelle ils tentent d’accéder. Ces identités utilisateur doivent être des membres de l’Azure Active Directory par défaut pour l’abonnement dans lequel se trouve Azure Analysis Services. Pour en savoir plus, consultez [Authentification et autorisations utilisateur](analysis-services-manage-users.md).

#### <a name="data-security"></a>Sécurité des données
Azure Analysis Services utilise le stockage Blob Azure pour conserver le stockage et les métadonnées des bases de données Analysis Services. Les fichiers de données Blob sont chiffrés à l’aide du chiffrement côté serveur (SSE) Azure Blob. Si vous utilisez le mode Requête directe, seules les métadonnées sont stockées. Les données réelles sont accessibles à partir de la source de données au moment de la requête.

#### <a name="firewall"></a>Pare-feu

Le pare-feu Azure Analysis Services bloque toutes les connexions clients autres que celles spécifiées dans les règles. Configurez des règles spécifiant des adresses IP autorisées par adresses IP clients individuelles ou par plage. Les connexions Power BI (service) peuvent également être autorisées ou bloquées. 

#### <a name="on-premises-data-sources"></a>Sources de données locales
Vous sécurisez l’accès aux données locales dans votre entreprise en installant et en configurant une [Passerelle de données locale](analysis-services-gateway.md). Les passerelles fournissent un accès aux données pour les requêtes directes et les modes en mémoire. Lorsqu’un modèle Azure Analysis Services se connecte à une source de données locale, une requête est créée, ainsi que les informations d’identification chiffrées pour la source de données locale. Le service cloud de la passerelle analyse la requête et envoie la requête vers Azure Service Bus. La passerelle locale interroge Azure Service Bus pour connaître les requêtes en attente. La passerelle reçoit ensuite la requête, déchiffre les informations d’identification et se connecte à la source de données pour l’exécution. Les résultats sont ensuite renvoyés de la source de données vers la passerelle, puis vers la base de données Azure Analysis Services.

Azure Analysis Services est régi par les [Termes du contrat Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) et la [Déclaration de confidentialité Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Pour plus d’informations sur la sécurité Azure, consultez [Microsoft Trust Center](https://www.microsoft.com/trustcenter/Security/AzureSecurity).



## <a name="supports-the-latest-client-tools"></a>Prise en charge des derniers outils clients
![Visualisations de données](./media/analysis-services-overview/aas-overview-clients.png)

Les outils modernes d’exploration et de visualisation de données comme Power BI, Excel, SQL Server 2017 Reporting Services et d’autres outils tiers sont tous pris en charge, fournissant aux utilisateurs des informations hautement interactives et visuellement riches sur vos données de modèles. 

Les clients utilisent les [Bibliothèques clientes](analysis-services-data-providers.md) MSOLAP, AMO ou ADOMD pour se connecter aux serveurs Analysis Services. Les applications clientes Microsoft telles que Power BI Desktop et Excel installent les trois bibliothèques clientes. Mais gardez à l’esprit que selon la version ou la fréquence des mises à jour, les bibliothèques clientes peuvent ne pas être les dernières versions requises par Azure Analysis Services. Il en va de même pour les applications personnalisées ou d’autres interfaces telles que AsCmd, TOM, ADOMD.NET. Ces applications nécessitent généralement l’installation manuelle des bibliothèques en même temps que le reste du package.


## <a name="get-help"></a>Obtenir de l’aide

#### <a name="documentation"></a>Documentation
Azure Analysis Services est simple à configurer et à gérer. Vous trouverez ici toutes les informations dont vous avez besoin pour créer et gérer vos services de serveur. Lorsque vous créez un modèle de données à déployer sur votre serveur, ceci est très similaire à la création d’un modèle de données que vous déployez sur un serveur local. Une bibliothèque importante de concepts, de procédures, de didacticiels et d’articles de référence est disponible dans [l’aide de SQL Server Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services).

#### <a name="videos"></a>vidéos
Visionnez différentes vidéos utiles dans [Azure Analysis Services sur Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

#### <a name="blogs"></a>Blogs
Les choses évoluent rapidement. Vous pouvez toujours obtenir les dernières informations en consultant le [Blog de l’équipe Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/) et le [Blog Azure](https://azure.microsoft.com/blog/).

#### <a name="community"></a>Communauté
Analysis Services a une communauté active d’utilisateurs. Rejoignez la conversation sur le [forum Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Commentaires
Vous avez des suggestions ou des demandes de fonctionnalités ? Veillez à laisser vos commentaires sur [Azure Analysis Services Feedback](https://aka.ms/azureanalysisservicesfeedback).

Vous avez des suggestions concernant la documentation ? Vous pouvez ajouter des commentaires à l’aide de Livefyre en bas de chaque article.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous en savez plus sur Azure Analysis Services, il est temps de commencer. Découvrez comment [créer un serveur](analysis-services-create-server.md) dans Azure. Lorsque votre serveur est prêt, parcourez le [Didacticiel Adventure Works](tutorials/aas-adventure-works-tutorial.md) pour apprendre à créer un modèle tabulaire entièrement fonctionnel et le déployer sur votre serveur.
