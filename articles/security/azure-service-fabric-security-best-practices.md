---
title: "Bonnes pratiques pour la sécurité Azure Service Fabric | Microsoft Docs"
description: "Cet article fournit un ensemble de bonnes pratiques pour la sécurité Azure Service Fabric."
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
ms.date: 11/01/2017
ms.author: tomsh
ms.openlocfilehash: a8b76e2895edcdbbddafbee7116e163d1789c06d
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="azure-service-fabric-security-best-practices"></a>Bonnes pratiques pour la sécurité Azure Service Fabric
Le déploiement d’une application sur Azure est rapide, simple et rentable. Avant de déployer votre application cloud dans l’environnement de production, passez en revue la liste des bonnes pratiques essentielles et recommandées pour l’implémentation de clusters sécurisés dans votre application.

Azure Service Fabric est une plateforme de systèmes distribués qui facilite le packaging, le déploiement et la gestion de microservices évolutifs et fiables. Service Fabric résout également les problèmes non négligeables du développement et de la gestion des applications cloud. Les développeurs et administrateurs sont en mesure d’éviter les problèmes d’infrastructure complexes et peuvent se concentrer sur l’implémentation de charges de travail stratégiques et exigeantes, évolutives, fiables et faciles à gérer. 

Pour chaque bonne pratique, nous détaillons les éléments suivants :

-   La nature de la bonne pratique
-   La raison pour laquelle vous devez l’implémenter
-   Ce qui peut se produire si vous ne l’implémentez pas
-   La façon d’apprendre à l’utiliser

Nous recommandons les bonnes pratiques suivantes pour la sécurité Azure Service Fabric :

-   Utiliser des modèles Azure Resource Manager et le module PowerShell Service Fabric pour créer les clusters sécurisés.
-   Utiliser des certificats X.509.
-   Configurer des stratégies de sécurité.
-   Implémenter la configuration de la sécurité de Reliable Actors.
-   Configurer SSL pour Azure Service Fabric.
-   Utiliser la sécurité et l’isolement réseau avec Azure Service Fabric.
-   Configurer Azure Key Vault pour la sécurité.
-   Affecter des utilisateurs aux rôles.


## <a name="best-practices-for-securing-your-clusters"></a>Bonnes pratiques pour sécuriser vos clusters

Utiliser toujours un cluster sécurisé :
-   Implémenter la sécurité du cluster à l’aide de certificats.
-   Fournir l’accès client (administrateur et en lecture seule) à l’aide d’Azure Active Directory (Azure AD).

Utiliser des déploiements automatisés :
-   Utiliser des scripts pour générer, déployer et substituer les secrets.
-   Stocker les secrets dans Azure Key Vault et utiliser Azure AD pour tous les autres accès clients.
-   Exiger une authentification pour l’accès aux secrets par une personne.

En outre, tenez compte des options de configuration suivantes :
-   Créer des réseaux de périmètre (également appelés zones DMZ et sous-réseaux filtrés) à l’aide de groupes de sécurité réseau Azure.
-   Accéder aux machines virtuelles du cluster ou gérer votre cluster à l’aide de serveurs de rebond (« jump ») avec la Connexion Bureau à distance.

Vos clusters doivent être sécurisés pour empêcher les utilisateurs non autorisés de se connecter, surtout quand un cluster est en cours d’exécution dans l’environnement de production. Même s’il est possible de créer des clusters non sécurisés, les utilisateurs anonymes peuvent s’y connecter si les points de terminaison de gestion sont exposés sur l’Internet public.

Il existe trois [scénarios](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) pour implémenter la sécurité du cluster à l’aide de différentes technologies :

-   Sécurité nœud à nœud : ce scénario sécurise les communications entre les machines virtuelles et les ordinateurs du cluster. Cette forme de sécurité garantit que seuls les ordinateurs qui sont autorisés à rejoindre le cluster peuvent héberger les applications et les services dans le cluster.
Dans ce scénario, les clusters qui s’exécutent sur Azure, ou les clusters autonomes qui s’exécutent sur Windows, peuvent utiliser la [Sécurité par certificat](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) ou la [Sécurité Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) pour les machines Windows Server.
-   Sécurité client à nœud : ce scénario sécurise la communication entre un client Service Fabric et les nœuds individuels du cluster.
-   Contrôle d’accès en fonction du rôle (RBAC) : ce scénario utilise des identités distinctes (certificats, Azure AD, etc.) pour chaque rôle client utilisateur et administrateur qui accède au cluster. Vous spécifiez les identités de rôle quand vous créez le cluster.

>[!NOTE]
>**Recommandation de sécurité pour les clusters Azure :** utilisez la sécurité Azure AD pour authentifier les clients et les certificats pour la sécurité nœud à nœud.

Pour configurer un cluster Windows autonome, consultez [Paramètres de configuration pour un cluster Windows autonome](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Utilisez des modèles Azure Resource Manager et le module PowerShell Service Fabric pour créer un cluster sécurisé.
Pour des instructions pas à pas sur la création d’un cluster Service Fabric sécurisé à l’aide de modèles Azure Resource Manager, consultez [Créer un cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Utiliser le modèle Azure Resource Manager :
-   Personnaliser votre cluster en utilisant le modèle afin de configurer le stockage géré pour les disques durs virtuels (VHD) de machine virtuelle.
-   Piloter les modifications apportées à votre groupe de ressources en utilisant le modèle pour faciliter la gestion de la configuration et de l’audit.

Traiter la configuration du cluster comme du code :
-   Vérifier vos configurations de déploiement avec minutie.
-   Éviter d’utiliser des commandes implicites pour modifier directement vos ressources.

Plusieurs aspects du [cycle de vie des applications Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) peuvent être automatisés. Le [module PowerShell Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatise les tâches courantes de déploiement, de mise à niveau, de suppression et de test des applications Service Fabric. Des API managées et des API HTTP pour la gestion de l’application sont également disponibles.

## <a name="use-x509-certificates"></a>Utiliser des certificats X.509
Sécurisez systématiquement vos clusters à l’aide de certificats X.509 ou de la sécurité Windows. La sécurité n’est configurée qu’à la création des clusters. Il est impossible d’activer la sécurité par la suite.

Pour spécifier un [certificat de cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), affectez à la propriété **ClusterCredentialType** la valeur X509. Pour spécifier un certificat de serveur pour des connexions externes, donnez à la propriété **ServerCredentialType** la valeur X509.

En outre, suivez les pratiques suivantes :
-   Créez les certificats pour les clusters de production à l’aide d’un service de certificats Windows Server correctement configuré. Vous pouvez également obtenir les certificats auprès d’une autorité de certification approuvée.
-   N’utilisez jamais un certificat temporaire ou de test pour les clusters de production si le certificat a été créé à l’aide de MakeCert.exe ou d’un outil similaire.
-   Utilisez un certificat auto-signé pour les clusters de test, mais pas pour les clusters de production.

Si le cluster n’est pas sécurisé, toute personne peut s’y connecter de façon anonyme pour effectuer des opérations de management. Vous devez donc sécuriser systématiquement vos clusters de production à l’aide de certificats X.509 ou de la sécurité Windows.

Pour plus d’informations sur l’utilisation de certificats X.509, consultez [Ajouter ou supprimer des certificats pour un cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Configurer des stratégies de sécurité
De plus, Service Fabric sécurise les ressources utilisées par les applications. Les ressources telles que les fichiers, les répertoires et les certificats sont stockées sous les comptes d’utilisateur quand l’application est déployée. Cette fonctionnalité sécurise l’exécution d’une application à partir d’une autre, même dans un environnement hébergé partagé.

-   Utiliser un utilisateur ou un groupe de domaine Active Directory : exécutez le service sous les informations d’identification d’un compte d’utilisateur ou de groupe Active Directory. Veillez à utiliser Active Directory en local au sein de votre domaine et non Azure Active Directory. Accédez à d’autres ressources dans le domaine qui disposent d’autorisations en utilisant un groupe ou un utilisateur de domaine. Par exemple, les ressources telles que les partages de fichiers.

-   Affecter une stratégie d’accès de sécurité pour des points de terminaison HTTP et HTTPS : spécifiez la propriété **SecurityAccessPolicy** pour appliquer une stratégie **RunAs** à un service quand le manifeste de service déclare des ressources de point de terminaison avec HTTP. Les ports alloués aux points de terminaison HTTP sont des listes de contrôle d’accès correctes pour le compte d’utilisateur d’identification sous lequel le service s’exécute. Quand la stratégie n’est pas définie, http.sys n’a pas accès au service et les appels en provenance du client échouent.

Pour savoir comment utiliser des stratégies de sécurité dans un cluster Service Fabric, consultez [Configurer les stratégies de sécurité de votre application](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implémenter la configuration de la sécurité de Reliable Actors
Service Fabric Reliable Actors est une implémentation du modèle de conception d’acteurs. Comme pour n’importe quel modèle de conception de logiciels, le choix de l’utilisation d’un modèle spécifique dépend de son degré d’adéquation avec le problème du logiciel.

En règle générale, utilisez le modèle de conception d’acteur afin de faciliter la modélisation de solutions pour les problèmes de logiciels ou les scénarios de sécurité suivants :
-   Votre espace de problème implique un nombre élevé (plusieurs milliers) de petites unités d’état et de logique indépendantes et isolées.
-   Vous utilisez des objets monothread ne nécessitant aucune interaction significative avec des composants externes, par exemple l’interrogation de l’état sur un ensemble d’acteurs.
-   Vos instances d’acteurs ne bloquent pas les appelants avec des retards inattendus en exécutant des opérations d’E/S.

Dans Service Fabric, les acteurs sont implémentés dans le framework d’application Reliable Actors. Ce framework repose sur le modèle d’acteur et [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Chaque service Reliable Actor que vous écrivez correspond à une instance Reliable Service partitionnée avec état.

Chaque acteur se définit comme une instance d’un type d’acteur, de la même façon qu’un objet .NET est une instance d’un type .NET. Par exemple, dans le cas d’un **type d’acteur** qui implémente les fonctionnalités d’une calculatrice, plusieurs acteurs de ce type peuvent être distribués sur différents nœuds d’un cluster. Chacun des acteurs distribués est identifié par un identificateur d’acteur.

Les [configurations de sécurité du réplicateur](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) sont utilisées pour sécuriser le canal de communication utilisé durant la réplication. Cette configuration empêche un service de voir le trafic de réplication des autres services et garantit que les données hautement disponibles sont sécurisées. Par défaut, une section de configuration de sécurité vide empêche de sécuriser la réplication.
Les configurations de réplicateur servent à configurer le réplicateur responsable de la haute fiabilité de l’état du fournisseur d’état d’acteur.

## <a name="configure-ssl-for-azure-service-fabric"></a>Configurer SSL pour Azure Service Fabric
Le processus d’authentification serveur [authentifie](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) les points de terminaison de gestion de cluster auprès d’un client de gestion. Le client de gestion reconnaît ensuite qu’il communique avec le cluster réel. Ce certificat fournit également un certificat [SSL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) pour l’API de gestion HTTPS et Service Fabric Explorer par le biais de HTTPS.
Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Quand vous demandez un certificat auprès d’une autorité de certification, le nom d’objet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

Pour configurer SSL pour une application, vous devez tout d’abord obtenir un certificat SSL qui a été signé par une autorité de certification. L’autorité de certification est un tiers de confiance qui émet des certificats à des fins de sécurité SSL. Si vous n’avez pas de certificat SSL, vous devez en obtenir un auprès d’une société qui en vend.

Le certificat SSL doit répondre aux prérequis suivants dans Azure :
-   Le certificat doit contenir une clé privée.

-   Le certificat doit être créé pour l’échange de clés et être exportable dans un fichier d’échange d’informations personnelles (.pfx).

-   Le nom d’objet du certificat doit correspondre au nom de domaine servant à accéder à votre service cloud.

    - Obtenez un nom de domaine personnalisé à utiliser pour accéder à votre service cloud.
    - Demandez un certificat à une autorité de certification avec un nom d’objet qui correspond au nom de domaine personnalisé de votre service. Par exemple, si votre nom de domaine personnalisé est __contoso__**.com**, le certificat fourni par votre autorité de certification doit avoir comme nom d’objet **.contoso.com** ou __www__**.contoso.com**.

    >[!NOTE]
    >Vous ne pouvez pas obtenir de certificat SSL auprès d’une autorité de certification pour le domaine __cloudapp__**.net**.
    
-   Le certificat doit utiliser au minimum un chiffrement à 2 048 bits.

Le protocole HTTP n’est pas sécurisé et peut faire l’objet d’écoutes clandestines. Les données qui sont transmises via HTTP sont envoyées en texte en clair à partir du navigateur web au serveur web ou entre d’autres points de terminaison. Des attaquants peuvent intercepter et afficher des données sensibles envoyées via HTTP, telles que les détails d’une carte de crédit et des informations de connexion de compte. Quand les données sont envoyées ou publiées par le biais d’un navigateur via HTTPS, SSL garantit que les informations sensibles sont chiffrées et protégées contre une interception.

Pour en savoir plus sur l’utilisation des certificats SSL, consultez [Configurer SSL pour des applications Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Utiliser la sécurité et l’isolement réseau avec Azure Service Fabric
Configurez un cluster sécurisé à 3 types de nœud en utilisant le [modèle Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) comme exemple. Contrôlez les trafics réseau entrant et sortant à l’aide du modèle et de groupes de sécurité réseau.

Le modèle a un groupe de sécurité réseau pour chacun des groupes de machines virtuelles identiques et permet de contrôler le trafic vers et depuis chacun d’eux. Les règles sont configurées par défaut pour autoriser tout le trafic nécessaire aux services système et aux ports d’application spécifiés dans le modèle. Passez en revue ces règles et apportez toutes les modifications nécessaires, y compris en ajoutant de nouvelles règles pour vos applications.

Pour plus d’informations, consultez [Scénarios de mise en réseau courants pour Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-azure-key-vault-for-security"></a>Configurer Azure Key Vault pour la sécurité
Service Fabric utilise les certificats à des fins d’authentification et de chiffrement pour sécuriser un cluster et ses applications.

Service Fabric utilise des certificats X.509 pour sécuriser un cluster et fournir des fonctionnalités de sécurité d’applications. Azure Key Vault sert à [gérer des certificats](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) pour des clusters Service Fabric dans Azure. Le fournisseur de ressources Azure qui crée les clusters extrait les certificats d’un coffre de clés. Ensuite, le fournisseur installe les certificats sur les machines virtuelles quand le cluster est déployé sur Azure.

Il existe une relation de certificat entre [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), le cluster Service Fabric et le fournisseur de ressources qui utilise les certificats. Quand le cluster est créé, les informations relatives à la relation de certificat sont stockées dans un coffre de clés.

Il existe deux étapes de base pour configurer un coffre de clés :
1. Créez un groupe de ressources dédié à votre coffre de clés.

    Nous vous recommandons de placer ce coffre de clés dans son propre groupe de ressources. Ainsi, vous évitez la perte de vos clés et secrets si d’autres groupes de ressources sont supprimés, tels qu’un groupe de stockage, de calcul ou le groupe qui contient votre cluster. Le groupe de ressources qui contient votre coffre de clés doit se trouver dans la même région que le cluster qui l’utilise.

2. Créez un coffre de clés dans le nouveau groupe de ressources.

    Le coffre de clés doit être activé pour le déploiement. Le fournisseur de ressources de calcul peut ensuite obtenir les certificats du coffre et les installer sur les instances de machine virtuelle.

Pour plus d’informations sur la configuration d’un coffre de clés, consultez [Bien démarrer avec Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-to-roles"></a>Affecter des utilisateurs aux rôles
Une fois que vous avez créé les applications pour représenter votre cluster, affectez les utilisateurs aux rôles qui sont pris en charge par Service Fabric : lecture seule et administrateur. Vous pouvez assigner ces rôles à l’aide du portail Azure.

>[!NOTE]
> Pour plus d’informations sur l’utilisation des rôles dans Service Fabric, consultez [Contrôle d’accès en fonction du rôle pour les clients de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Azure Service Fabric prend en charge deux types de contrôle d’accès pour les clients qui sont connectés à un [cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) : administrateur et utilisateur. L’administrateur du cluster peut utiliser le contrôle d’accès afin de limiter l’accès à certaines opérations de cluster pour différents groupes d’utilisateurs. Le contrôle d’accès rend le cluster plus sécurisé.

## <a name="next-steps"></a>Étapes suivantes
- Configurez votre [environnement de développement](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) Service Fabric.
- Découvrez les [options de support de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
