---
title: "Fonctionnalités et concepts clés d’Azure Stack | Microsoft Docs"
description: "Découvrez les fonctionnalités et concepts clés de Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 6c02ec42874e4e3221c53e6d6e85378bbe2e414a
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Fonctionnalités et concepts clés de Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Si vous ne connaissez pas encore Microsoft Azure Stack, ces termes et descriptions de fonctionnalité peuvent vous être utiles.

## <a name="personas"></a>Rôles
Il existe deux types d’utilisateurs pour Microsoft Azure Stack, l’opérateur cloud (fournisseur) et le locataire (consommateur).

* Un **opérateur cloud** peut configurer Azure Stack et gérer les offres, les plans, les services, les quotas et la tarification pour fournir des ressources à ses locataires.  Les opérateurs cloud gèrent aussi la capacité et répondent aux alertes.  
* Un **locataire** (également appelé utilisateur) consomme des services proposés par l’administrateur de cloud. Les clients peuvent approvisionner, surveiller et gérer les services auxquels ils sont abonnés, tels que les Web Apps, le stockage et les machines virtuelles.

## <a name="portal"></a>Portail
Les principales méthodes d’interaction avec Microsoft Azure Stack sont le portail administrateur, le portail utilisateur et PowerShell.

Les portails Azure Stack sont chacun secondés par des instances distinctes d’Azure Resource Manager.  Un opérateur cloud utilise le portail administrateur pour gérer Azure Stack et effectuer des tâches telles que la création d’offres pour les locataires.  Le portail utilisateur (également appelé portail du locataire) fournit une expérience de libre-service pour la consommation de ressources cloud telles que des machines virtuelles, des comptes de stockage et des applications web. Pour plus d’informations, consultez [Utilisation des portails administrateur et utilisateur d’Azure Stack](azure-stack-manage-portals.md).

## <a name="identity"></a>Identité 
Azure Stack utilise Azure Active Directory (AAD) ou Active Directory Federation Services (AD FS) en tant que fournisseur d’identité.  

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory est le fournisseur d’identité Microsoft multilocataire basé sur le cloud.  La plupart des scénarios hybrides utilisent Azure Active Directory comme magasin d’identités.

### <a name="active-directory-federation-services"></a>Services de fédération Active Directory (AD FS)
Vous pouvez choisir d’utiliser les services AD FS pour les déploiements déconnectés d’Azure Stack.  Azure Stack, les fournisseurs de ressources et les autres applications fonctionnent quasiment de la même façon avec AD FS et avec Azure Active Directory. Azure Stack inclut sa propre instance d’AD FS et d’Active Directory, ainsi qu’une API Graph Active Directory. Le Kit de développement Azure Stack prend en charge les scénarios AD FS suivants :

- Se connecter au déploiement à l’aide d’AD FS
- Créer une machine virtuelle avec des secrets dans Key Vault
- Créer un coffre pour le stockage et l’accès aux secrets
- Créer des rôles RBAC personnalisés dans l’abonnement
- Affecter des utilisateurs aux rôles RBAC sur les ressources
- Créer des rôles RBAC à l’échelle du système par le biais d’Azure PowerShell
- Se connecter en tant qu’utilisateur par le biais d’Azure PowerShell
- Créer des principaux du service et les utiliser pour se connecter à Azure PowerShell


## <a name="regions-services-plans-offers-and-subscriptions"></a>Régions, services, plans, offres et abonnements
Dans Azure Stack, les services sont fournis aux locataires par le biais de régions, d’abonnements, d’offres et de plans. Les clients peuvent s’abonner à plusieurs offres. Les offres peuvent contenir un ou plusieurs plans, et les plans peuvent contenir un ou plusieurs services.

![](media/azure-stack-key-features/image4.png)

Exemple de hiérarchie d’abonnements d’un client aux offres, chacune avec différents plans et services.

### <a name="regions"></a>Régions
Les régions Azure Stack sont un élément de base de la mise à l’échelle et de la gestion. Une organisation peut avoir plusieurs régions avec des ressources disponibles dans chaque région. Les régions peuvent également avoir différentes offres de services disponibles. Dans le Kit de développement Azure Stack, une seule région est prise en charge. Elle est automatiquement nommée *local*.

### <a name="services"></a>Services
Microsoft Azure Stack permet aux fournisseurs de proposer un large éventail de services et d’applications, tels que des machines virtuelles, des bases de données SQL Server, SharePoint, Exchange et bien plus encore.

### <a name="plans"></a>Abonnements
Les plans regroupent un ou plusieurs services. En tant que fournisseur, vous créez des plans à proposer aux clients. En retour, les clients s’abonnent à vos offres pour utiliser les plans et les services inclus.

Chaque service ajouté à un plan peut être configuré avec des paramètres de quota pour vous aider à gérer votre capacité du cloud. Les quotas peuvent inclure des restrictions, telles que des limites de machine virtuelle, de mémoire et d’unité centrale, appliquées par abonnement de l’utilisateur. Les quotas peuvent être différenciés par emplacement. Par exemple, un plan comprenant des services de calcul à partir de la région A peut avoir un quota de deux machines virtuelles, 4 Go de RAM et dix cœurs de processeur.

Lors de la création d’une offre, l’administrateur de service peut inclure un **plan de base**. Ces plans de base sont inclus par défaut lorsqu’un client s’abonne à cette offre. Quand un utilisateur s’abonne (et que l’abonnement est créé), il a accès à tous les fournisseurs de ressources spécifiés dans ces plans de base (avec les quotas correspondants).

L’administrateur de service peut également inclure des **plans additionnels** dans une offre. Les plans additionnels ne sont pas inclus par défaut dans l’abonnement. Les plans additionnels sont des plans supplémentaires (quotas) disponibles dans une offre que le propriétaire de l’abonnement peut ajouter à ses abonnements.

### <a name="offers"></a>Offres
Les offres sont des groupes d’un ou plusieurs plans que les fournisseurs proposent à leurs clients d’acheter. Par exemple, une offre Alpha peut contenir un plan A comportant un ensemble de services de calcul et un plan B comportant un ensemble de services de stockage et réseau.

Une offre est fournie avec un ensemble de plans de base, et les administrateurs de service peuvent créer des plans additionnels que les clients peuvent ajouter à leur abonnement.

### <a name="subscriptions"></a>Abonnements
Un abonnement est la forme sous laquelle les clients achètent vos offres. Un abonnement est la combinaison d’un client avec une offre. Un client peut être abonné à plusieurs offres. Chaque abonnement s’applique à une seule offre. Les abonnements d’un client déterminent les plans/services accessibles.

Les abonnements aident les fournisseurs à organiser et à accéder aux services et aux ressources cloud.

Pour l’administrateur, un Abonnement Fournisseur par défaut est créé pendant le déploiement. Cet abonnement peut être utilisé pour gérer Azure Stack, déployer d’autres fournisseurs de ressources et créer des plans et des offres pour les locataires. Il ne doit pas être utilisé pour exécuter des applications et des charges de travail clientes. 

## <a name="azure-resource-manager"></a>Azure Resource Manager
Azure Resource Manager vous permet de travailler avec vos ressources d’infrastructure selon un modèle déclaratif et basé sur des modèles.   Il fournit une interface unique que vous pouvez utiliser pour déployer et gérer les composants de votre solution. Pour plus d’informations et pour obtenir des instructions, consultez [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Groupes de ressources
Les groupes de ressources sont des ensembles de ressources, de services et d’applications, et chaque ressource a son type, tel que les machines virtuelles, les réseaux virtuels, les IP publiques, les comptes de stockage et les sites web. Chaque ressource doit être dans un groupe de ressources. Ceux-ci aident à organiser les ressources de manière logique, par exemple par charge de travail ou par emplacement.  Dans Microsoft Azure Stack, les ressources, telles que les plans et les offres, sont également gérées dans des groupes de ressources.
 
### <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager
Avec z Resource Manager, vous pouvez créer un modèle (au format JSON) qui définit le déploiement et la configuration de votre application. Ce modèle est connu sous le nom de modèle Azure Resource Manager et permet de définir le déploiement de façon déclarative. En utilisant un modèle, vous pouvez déployer votre application à plusieurs reprises tout au long du cycle de vie de l’application et avoir ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement.

## <a name="resource-providers-rps"></a>Fournisseurs de ressources
Les fournisseurs de ressources sont les services web qui constituent la base de tous les services IaaS et PaaS Azure. Azure Resource Manager s’appuie sur différents fournisseurs de ressources pour fournir l’accès aux services.

Il existe quatre fournisseurs de ressources fondamentaux : réseau, stockage, calcul et KeyVault. Chacun de ces fournisseurs vous permet de configurer et contrôler ses ressources respectives. Les administrateurs de services peuvent également ajouter de nouveaux fournisseurs de ressources personnalisés.

### <a name="compute-rp"></a>Fournisseur de ressources de calcul
Le fournisseur de ressources de calcul (CRP) permet aux clients Azure Stack de créer leurs propres machines virtuelles. Le CRP inclut la possibilité de créer des machines virtuelles, ainsi que des extensions de machine virtuelle. Le service d’extension de machine virtuelle fournit des fonctionnalités IaaS pour les machines virtuelles Windows et Linux.  Par exemple, vous pouvez utiliser le CRP pour approvisionner une machine virtuelle Linux et exécuter des scripts Bash pendant le déploiement pour configurer la machine virtuelle.

### <a name="network-rp"></a>Fournisseur de ressources réseau
Le fournisseur de ressources réseau (NRP) offre une série de fonctionnalités SDN (Software Defined Networking) et NFV (Network Function Virtualization) pour le cloud privé.  Vous pouvez utiliser le NRP pour créer des ressources telles que des équilibreurs de charge logiciels, des adresses IP publiques, des groupes de sécurité réseau et des réseaux virtuels.

### <a name="storage-rp"></a>Fournisseur de ressources de stockage
Le fournisseur de ressources de stockage propose quatre services de stockage cohérents au niveau Azure : objets blob, table, file d’attente et gestion de compte. Il offre également un service d’administration de stockage cloud pour faciliter l’administration du fournisseur de services de stockage cohérents au niveau Azure. Azure Storage offre la flexibilité nécessaire pour stocker et récupérer de grandes quantités de données non structurées, telles que des documents et des fichiers multimédias avec des objets blob Azure, et de données NoSQL structurées avec les tables Azure. Pour plus d’informations sur le stockage Azure, consultez la page [Introduction au Stockage Microsoft Azure](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Stockage d'objets blob
Le stockage d’objets blob stocke tous les jeux de données. Un objet blob peut correspondre à n'importe quel type de données texte ou binaires, par exemple, un document, un fichier multimédia ou un programme d'installation d'application. Le stockage de tables stocke les jeux de données structurés. Le stockage de tables est un magasin de données de clés-attributs NoSQL qui permet le développement rapide et l'accès rapide à de grosses quantités de données. Le stockage de files d’attente fournit une messagerie fiable pour le traitement du workflow et pour la communication entre les composants des services cloud.

Chaque objet blob est organisé sous un conteneur. Les conteneurs fournissent également un moyen utile d'affecter des stratégies de sécurité à des groupes d'objets. Un compte de stockage peut contenir un nombre quelconque de conteneurs, et un conteneur peut contenir un nombre quelconque d'objets blobs, jusqu'à la limite de capacité de 500 To du compte de stockage. Le stockage d’objets blob propose trois types d’objets : les objets blob de bloc, les objets blob d’ajout et les objets blob de page (disques). Les objets blob de bloc sont optimisés pour la diffusion en continu et le stockage d’objets cloud. Ils constituent une solution de choix pour stocker des documents, des fichiers multimédias, des sauvegardes, etc. Les objets blob d’ajout sont similaires aux objets blob de blocs, mais ils sont optimisés pour les opérations d’ajout. Un objet blob d’ajout ne peut être mis à jour qu’en lui ajoutant un nouveau bloc à la fin. Les objets blob d’ajout sont idéaux pour des opérations telles que la journalisation, où les nouvelles données ne doivent être écrites qu’à la fin de l’objet blob. Les objets blob de page sont optimisés pour représenter les disques IaaS et prendre en charge les écritures aléatoires. Leur taille maximale est de 1 To. Un disque IaaS rattaché à un réseau de machines virtuelles Azure est un disque dur virtuel stocké en tant qu'objet blob de page.

#### <a name="table-storage"></a>Stockage de tables
Le stockage de tables est le magasin de clés/attributs NoSQL de Microsoft ; il a une conception sans schéma, ce en quoi il diffère des bases de données relationnelles classiques. Avec un magasin de données sans schéma, il est facile d’adapter vos données au fur et à mesure que les besoins de votre application évoluent. Facile à utiliser, le stockage de tables permet aux développeurs de créer rapidement des applications. Le stockage de tables est un magasin de clés/attributs : cela signifie que chaque valeur dans une table est stockée avec un nom de propriété typé. Le nom de propriété peut être utilisé pour filtrer et spécifier des critères de sélection. Une collection de propriétés et leurs valeurs constituent une entité. Dans la mesure où le stockage de tables est sans schéma, deux entités d’une même table contiennent différentes collections de propriétés, et ces propriétés peuvent être de différents types. Vous pouvez utiliser le stockage de tables pour stocker des jeux de données flexibles, par exemple, des données utilisateur pour des applications Web, des carnets d'adresses, des informations sur les périphériques et tout autre type de métadonnées requis par votre service. Vous pouvez stocker un nombre quelconque d'entités dans une table, et un compte de stockage peut contenir un nombre quelconque de tables, jusqu'à la limite de capacité du compte de stockage.

#### <a name="queue-storage"></a>Stockage de files d’attente
Le Stockage File d’attente Azure fournit une messagerie cloud entre les composants d’application. Lors de la conception d'applications pour la mise à l'échelle, des composants d'application sont souvent découplés, de sorte qu'ils peuvent être mis à l'échelle indépendamment. Le stockage de files d’attente offre une messagerie asynchrone pour la communication entre les composants d’application, qu’ils soient exécutés dans le cloud, sur le bureau, sur un serveur local ou sur un appareil mobile. Le stockage de files d’attente prend également en charge la gestion des tâches asynchrones et la création des flux de travail de processus.

### <a name="keyvault"></a>KeyVault
Le fournisseur de ressources KeyVault fournit la gestion et l’audit des secrets, tels que les mots de passe et les certificats. Par exemple, un locataire peut utiliser le fournisseur de ressources KeyVault pour fournir des mots de passe ou des clés d’administrateur lors du déploiement de machine virtuelle.

## <a name="role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle (RBAC)
Vous pouvez utiliser le contrôle RBAC pour accorder l’accès au système aux utilisateurs, groupes et services autorisés en leur attribuant des rôles au niveau de l’abonnement, du groupe de ressources ou de la ressource individuelle. Chaque rôle définit le niveau d’accès dont un utilisateur, un groupe ou un service dispose sur les ressources Microsoft Azure Stack.

Le contrôle d’accès en fonction du rôle Azure comporte trois rôles de base qui s’appliquent à tous les types de ressources : Propriétaire, Contributeur et Lecteur. Le Propriétaire dispose d’un accès total à toutes les ressources, ainsi que le droit de déléguer l’accès à d’autres personnes. Le Contributeur peut créer et gérer tous types de ressources Azure, mais ne peut pas accorder l’accès à d’autres personnes. Le Lecteur peut uniquement afficher les ressources Azure existantes. Les autres rôles RBAC dans Azure permettent de gérer des ressources Azure spécifiques. Par exemple, le rôle de Contributeur de machine virtuelle autorise la création et la gestion de machines virtuelles, mais pas la gestion du réseau virtuel ou du sous-réseau auquel la machine virtuelle se connecte.

## <a name="usage-data"></a>Données d'utilisation
Microsoft Azure Stack collecte et agrège les données d’utilisation de tous les fournisseurs de ressources, et il les transmet à Azure en vue de leur traitement par la solution commerciale Azure. Les données d’utilisation collectées sur Azure Stack sont consultables par le biais d’une API REST. Il existe une API client, et des API fournisseur et fournisseur délégué cohérentes au niveau Azure pour obtenir des données d’utilisation sur tous les abonnements du locataire. Ces données peuvent être utilisées pour intégrer un outil ou un service externe de facturation ou de facturation interne. Une fois les données d’utilisation traitées par la solution commerciale Azure, elles peuvent être visualisées dans le portail de facturation Azure.

## <a name="in-development-build-of-azure-stack-development-kit"></a>Builds de développement du Kit de développement Azure Stack
Les builds de développement permettent aux utilisateurs précoces d’évaluer la version la plus récente du Kit de développement Azure Stack. Il s’agit de builds incrémentielles basées sur la version principale la plus récente. Les versions principales continueront à être publiées tous les quelques mois, tandis que les builds de développement seront publiées par intermittence entre les versions majeures.

Les builds de développement offrent les avantages suivants :
- Résolution des bogues
- Nouvelles fonctionnalités
- Autres améliorations

## <a name="next-steps"></a>Étapes suivantes
[Prérequis pour le déploiement Azure Stack](azure-stack-deploy.md)

