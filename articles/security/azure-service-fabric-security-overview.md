---
title: "Vue d’ensemble de la sécurité Azure Service Fabric | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble de la sécurité Azure Service Fabric."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: 64717da922701aabd27e15a67e8da1b0acb30b77
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="azure-service-fabric-security-overview"></a>Vue d’ensemble de la sécurité Azure Service Fabric
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) est une plateforme de systèmes distribués qui facilite le packaging, le déploiement et la gestion de microservices évolutifs et fiables. Service Fabric résout les défis non négligeables du développement et de la gestion des applications cloud. Les développeurs et administrateurs sont en mesure d’éviter les problèmes d’infrastructure complexes et peuvent se concentrer sur l’implémentation de charges de travail stratégiques et exigeantes, évolutives, fiables et faciles à gérer.

Cette vue d’ensemble de la sécurité Azure Service Fabric se concentre sur les points suivants :

-   Sécurisation de votre cluster
-   Compréhension de la surveillance et des diagnostics
-   Création d’environnements plus sécurisés à l’aide de certificats
-   Utilisation du contrôle d’accès en fonction du rôle (RBAC)
-   Sécurisation des clusters en utilisant la sécurité de Windows
-   Configuration de la sécurité de l’application dans Service Fabric
-   Sécurisation de la communication pour les services dans Azure Service Fabric 

## <a name="secure-your-cluster"></a>Sécuriser votre cluster
Azure Service Fabric est un orchestrateur de services sur un cluster de machines. Les clusters doivent être sécurisés pour empêcher les utilisateurs non autorisés à s’y connecter, surtout quand des charges de travail sont en cours d’exécution. Bien qu’il soit possible de créer un cluster non sécurisé, cela permet à tout utilisateur anonyme de s’y connecter (si les points de terminaison de gestion sont exposés sur l’Internet public).

Cette section fournit une vue d’ensemble des scénarios de sécurité pour les clusters qui s’exécutent soit en autonome soit sur Azure. Elle décrit également les différentes technologies utilisées pour mettre en œuvre ces scénarios. Les scénarios de sécurité des clusters sont les suivants :

-   Sécurité nœud à nœud
-   Sécurité client à nœud

### <a name="node-to-node-security"></a>Sécurité nœud à nœud
La sécurité nœud à nœud sécurise la communication entre les machines virtuelles ou les ordinateurs d’un cluster. Cette sécurité nœud à nœud garantit que seuls les ordinateurs autorisés à rejoindre le cluster peuvent participer à l’hébergement des applications et des services dans le cluster.

Les clusters qui s’exécutent sur Azure ou les clusters autonomes sur Windows peuvent utiliser la [Sécurité par certificat](https://msdn.microsoft.com/library/ff649801.aspx) ou la [Sécurité Windows](https://msdn.microsoft.com/library/ff649396.aspx) pour les ordinateurs Windows Server.

**Comprendre la sécurité de certificat de nœud à nœud**

Service Fabric utilise des certificats de serveur X.509 que vous spécifiez lorsque vous créez un cluster. Pour obtenir un rapide aperçu de ce que sont ces certificats et de la façon dont vous pouvez les acquérir ou les créer, consultez [Utilisation des certificats](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

La configuration de la sécurité par certificat s’effectue lors de la création du cluster par le biais du Portail Azure, de modèles Azure Resource Manager ou d’un modèle JSON autonome. Vous pouvez spécifier un certificat primaire et un certificat secondaire facultatif utilisé pour les renouvellements de certificats. Les certificats primaires et secondaires que vous spécifiez doivent être différents de ceux du client d’administration et du client en lecture seule que vous spécifiez pour la [sécurité client à nœud](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Sécurité client à nœud
Configurez la sécurité client à nœud à l’aide d’identités client. Pour établir une approbation entre un client et un cluster, vous devez configurer le cluster de façon à ce qu’il sache quelles identités client sont fiables. Cette opération peut être réalisée de deux manières :

-   Spécifiez les utilisateurs du groupe de domaines qui peuvent se connecter. 
-   Spécifiez les utilisateurs du nœud de domaine qui peuvent se connecter.

Service Fabric prend en charge deux types de contrôle d’accès différents pour les clients qui sont connectés à un cluster Service Fabric :

-   Administrateur
-   Utilisateur

En utilisant le contrôle d’accès, les administrateurs de cluster peuvent limiter l’accès à certains types d’opérations de cluster. Cela rend le cluster plus sécurisé.

 Les administrateurs ont un accès complet aux fonctions de gestion (y compris les fonctionnalités de lecture/écriture). Les utilisateurs, par défaut, ont uniquement un accès en lecture aux fonctionnalités de gestion (par exemple, aux fonctionnalités de requête) et la capacité à résoudre les applications et les services.

**Comprendre la sécurité par certificat client à nœud**

La configuration de la sécurité par certificat client à nœud s’effectue lors de la création du cluster par le biais du portail Azure, de modèles Gestionnaire des ressources ou d’un modèle JSON autonome. Vous devez spécifier un certificat client d’administration et/ou un certificat client utilisateur. 

Les certificats client d’administration et client utilisateur que vous spécifiez doivent être différents des certificats primaires et secondaires que vous spécifiez pour la sécurité nœud à nœud.

Les clients se connectant au cluster avec le certificat d’administration ont un accès complet aux fonctions de gestion. Les clients se connectant au cluster avec le certificat client utilisateur en lecture seule ont uniquement un accès en lecture aux fonctions de gestion. En d’autres termes, ces certificats sont utilisés pour RBAC.

Pour en savoir plus sur la configuration de la sécurité de certificat dans un cluster, consultez [Configurer un cluster à l’aide d’un modèle Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

**Comprendre la sécurité Azure Active Directory client-à-nœud sur Azure**

Les clusters s’exécutant sur Azure peuvent également sécuriser l’accès aux points de terminaison de gestion à l’aide d’Azure Active Directory (Azure AD). Pour plus d’informations sur la façon de créer les artefacts Azure Active Directory nécessaires, de les remplir pendant la création du cluster et de se connecter à ces clusters par la suite, consultez [Configurer un cluster en utilisant un modèle Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Azure AD permet aux organisation (appelées locataires) de gérer l’accès utilisateur aux applications. Il existe des applications avec une interface utilisateur de connexion Web et des applications avec une expérience client natif.

Un cluster Service Fabric offre différents points d’entrée pour leurs fonctionnalités de gestion, notamment les outils Service Fabric Explorer et Visual Studio. Par conséquent, vous allez créer deux applications Azure AD pour contrôler l’accès au cluster : une application Web et une application native.

Pour les clusters Azure, nous vous recommandons d’utiliser la sécurité Azure AD pour authentifier les clients et les certificats pour la sécurité nœud à nœud.

Pour les clusters Windows Server autonomes avec Windows Server 2012 R2 et Active Directory, nous vous recommandons d’utiliser la sécurité Windows avec des comptes gérés par des groupes.  Sinon, continuez à utiliser la sécurité Windows avec les comptes Windows.

## <a name="understand-monitoring-and-diagnostics-in-azure-service-fabric"></a>Comprendre la surveillance et les diagnostics dans Azure Service Fabric
La [surveillance et les diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) sont essentiels au développement, au test et au déploiement d’applications et de services dans tout environnement. Les solutions Service Fabric conviennent mieux pour planifier et mettre en œuvre une surveillance et des diagnostics afin de s’assurer que les applications et les services fonctionnent comme prévu dans un environnement de développement local ou de production.

Du point de vue de la sécurité, les principaux objectifs de la surveillance et des diagnostics sont les suivants :

-   Détecter et diagnostiquer les problèmes de matériel et d’infrastructure pouvant être dus à un événement de sécurité.
-   Détecter les problèmes de logiciel et d’applications pouvant indiquer une compromission.
-   Comprendre la consommation des ressources afin d’empêcher les dénis de service faits par inadvertance.

Le flux de travail global de la surveillance et des diagnostics se compose de trois étapes :

-   **Génération d’événements** : la génération d’événements inclut les événements (journaux, traces, événements personnalisés) au niveau de l’infrastructure (cluster) et de l’application/du service. Pour comprendre ce qui est fourni et comment ajouter une instrumentation, vous pouvez poursuivre votre lecture sur les [événements de niveau infrastructure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) et les [événements de niveau application](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app).

-   **Agrégation d’événements** : les événements générés doivent être collectés et agrégés avant de pouvoir être affichés. En général, nous recommandons d’utiliser [Azure Diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (similaire à la collecte de journaux basée sur un agent) ou [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (collecte de journaux in-process).

-   **Analyse** : les événements doivent être visualisés et accessibles dans un certain format, pour permettre l’analyse et l’affichage. Il existe plusieurs plateformes pour l’analyse et la visualisation des données de surveillance et de diagnostics. Les deux que nous recommandons sont [OMS (Operations Management Suite)](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) et [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights), en raison de leur meilleure intégration à Service Fabric.

Vous pouvez également utiliser [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) pour surveiller un grand nombre des ressources Azure sur lesquelles repose un cluster Service Fabric.

Un agent de surveillance est un service distinct qui peut surveiller l’intégrité et la charge des services et créer des rapports d’intégrité pour n’importe quel élément de la hiérarchie du modèle d’intégrité. L’utilisation de l’agent de surveillance permet d’éviter des erreurs qui ne seraient pas détectées à partir de l’affichage d’un service unique. 

Les agents de surveillance sont également un bon emplacement pour héberger du code qui exécute des actions correctives sans intervention de l’utilisateur (par exemple, le nettoyage de fichiers journaux dans le stockage à intervalles réguliers). Vous pouvez trouver un exemple de mise en œuvre de service de surveillance sur [l’échantillon d’agent de surveillance Azure Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="understand-how-to-secure-communication-by-using-certificates"></a>Comprendre comment sécuriser la communication à l’aide de certificats
Les certificats vous aident à sécuriser la communication entre les différents nœuds de votre cluster Windows autonome. À l’aide de certificats X.509, vous pouvez également authentifier les clients qui sont connectés à ce cluster. Cela garantit que seuls les utilisateurs autorisés peuvent accéder au cluster. Nous vous recommandons d’activer un certificat sur le cluster lors de sa création.

### <a name="x509-certificates-and-service-fabric"></a>Certificats X.509 et Service Fabric
Les certificats numériques X.509 sont couramment utilisés pour authentifier les clients et serveurs. Ils sont également utilisés pour chiffrer et signer numériquement les messages.

Le tableau suivant répertorie les certificats dont vous aurez besoin pour la configuration de votre cluster :

|Définition des informations sur le certificat |Description|
|-------------------------------|-----------|
|ClusterCertificate|    Ce certificat est requis pour sécuriser les communications entre les nœuds sur un cluster. Vous pouvez utiliser deux certificats différents : un certificat principal et un certificat secondaire pour la mise à niveau.|
|ServerCertificate| Ce certificat est présenté au client lorsqu’il tente de se connecter à ce cluster. Vous pouvez utiliser deux certificats de serveur différents : un certificat principal et un certificat secondaire pour la mise à niveau.|
|ClientCertificateThumbprints|  Il s’agit d’un jeu de certificats à installer sur les clients authentifiés.|
|ClientCertificateCommonNames|  Il s’agit du nom commun du premier certificat client pour CertificateCommonName. CertificateIssuerThumbprint est l’empreinte numérique relative à l’émetteur de ce certificat.|
|ReverseProxyCertificate|   Il s’agit d’un certificat facultatif à spécifier si vous souhaitez sécuriser votre [Proxy inverse](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Pour plus d’informations sur la sécurisation des certificats, consultez [Sécuriser un cluster autonome sur Windows à l’aide de certificats X.509](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Comprendre le contrôle d’accès en fonction du rôle
Le contrôle d'accès permet à l'administrateur du cluster de limiter l'accès à certaines opérations de cluster pour différents groupes d'utilisateurs, renforçant ainsi la sécurité du cluster. Deux types de contrôle d’accès différents sont pris en charge pour les clients se connectant à un cluster : 

- Rôle administrateur
- Rôle utilisateur

Les administrateurs ont un accès complet aux fonctions de gestion (y compris les fonctionnalités de lecture/écriture). Les utilisateurs, par défaut, ont uniquement un accès en lecture aux fonctionnalités de gestion (par exemple, aux fonctionnalités de requête) et la capacité à résoudre les applications et les services.

Spécifiez les rôles client utilisateur et administrateur au moment de la création du cluster en fournissant des identités distinctes (y compris des certificats) pour chacun. Pour plus d’informations sur les paramètres de contrôle d’accès par défaut et sur la modification des paramètres par défaut, consultez [Contrôle d’accès en fonction du rôle pour les clients Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-cluster-by-using-windows-security"></a>Sécuriser un cluster autonome à l’aide de la sécurité Windows
Pour empêcher tout accès non autorisé à un cluster Service Fabric, vous devez sécuriser le cluster. La sécurité est particulièrement importante lorsque le cluster exécute des charges de travail de production. Cette approche permet de configurer la sécurité de nœud à nœud et de client à nœud avec la sécurité Windows dans le fichier ClusterConfig.JSON.

**Configurer la sécurité Windows à l’aide de gMSA**

Configurez la sécurité nœud à nœud en définissant [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) quand Service Fabric doit s’exécuter sous le compte gMSA. Pour créer des relations d’approbation entre les nœuds, ceux-ci doivent se connaître mutuellement.

Configurez la sécurité client à nœud à l’aide de ClientIdentities. Pour établir une approbation entre un client et le cluster, vous devez configurer le cluster de façon à ce qu’il sache quelles identités client sont fiables.

**Configurer la sécurité Windows à l’aide d’un groupe de machines**

Configurez la sécurité nœud à nœud en définissant ClusterIdentity si vous souhaitez utiliser un groupe de machines dans un domaine Active Directory. Pour en savoir plus, consultez [Créer un groupe de machines dans Active Directory](https://msdn.microsoft.com/library/aa545347).

Configurez la sécurité client à nœud à l’aide de ClientIdentities. Pour établir une approbation entre un client et le cluster, vous devez configurer le cluster de façon à ce qu’il reconnaisse les identités du client auxquelles il peut faire confiance. Vous pouvez établir cette approbation de deux manières différentes :

-   Spécifiez les utilisateurs du groupe de domaines qui peuvent se connecter.
-   Spécifiez les utilisateurs du nœud de domaine qui peuvent se connecter.

## <a name="configure-application-security-in-service-fabric"></a>Configuration de la sécurité de l’application dans Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Gérer les secrets dans les applications Service Fabric
Cette méthode permet de gérer les secrets dans une application Service Fabric. Les secrets peuvent être des informations sensibles quelconques, notamment des chaînes de connexion de stockage, des mots de passe ou d’autres valeurs qui ne doivent pas être traitées en texte brut.

Cette approche utilise [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) pour gérer les clés et les secrets. Toutefois, l’utilisation de secrets dans une application est indépendant de la plateforme cloud. Cela signifie que les applications peuvent être déployées vers un cluster qui est hébergé n’importe où. Ce flux comprend quatre étapes principales :

-   Obtenez un certificat de chiffrement de données.
-   Installez le certificat dans votre cluster.
-   Chiffrez les valeurs secrètes lors du déploiement d’une application avec le certificat et injectez-les dans le fichier de configuration Settings.xml d’un service.
-   Lisez les valeurs chiffrées dans Settings.xml en les déchiffrant avec le même certificat de chiffrement.

>[!NOTE]
>En savoir plus sur la [gestion des secrets dans des applications Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-your-application"></a>Configurer les stratégies de sécurité de votre application
Grâce à la sécurité Azure Service Fabric, vous pouvez sécuriser les applications en cours d’exécution dans le cluster sous différents comptes utilisateurs. La sécurité Service Fabric permet également de sécuriser les ressources utilisées par des applications au moment du déploiement sous les comptes utilisateurs, par exemple les fichiers, les annuaires et les certificats. Ainsi, les applications en cours d’exécution sont mieux sécurisées, même dans un environnement hébergé partagé.

Procédez comme suit :

-   Configurez la stratégie pour un point d’entrée de configuration de service.
-   Lancez des commandes PowerShell à partir d’un point d’entrée de configuration.
-   Utilisation de la redirection de la console pour le débogage local.
-   Configuration d’une stratégie pour les packages de code de service.
-   Attribuer une stratégie d’accès de sécurité pour des points de terminaison HTTP et HTTPS.

## <a name="secure-communication-for-services-in-azure-service-fabric-security"></a>Sécuriser les communications pour les services dans la sécurité Azure Service Fabric
La sécurité est un des aspects les plus importants de la communication. Le framework d’application Reliable Services fournit quelques piles et outils de communication prédéfinis que vous pouvez utiliser pour améliorer la sécurité.

-   [Sécuriser un service quand vous utilisez la communication à distance des services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication)
-   [Sécuriser un service quand vous utilisez une pile de communication basée sur WCF](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack)

## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir des informations conceptuelles sur la sécurité des clusters, consultez [Créer un cluster dans Azure à l’aide d’Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) et [Portail Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Pour en savoir plus sur la sécurité des clusters dans Service Fabric, consultez [Sécurité du cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).
