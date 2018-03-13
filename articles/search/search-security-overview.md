---
title: "Sécuriser les données et les opérations dans Recherche Azure | Microsoft Docs"
description: "La sécurité Recherche Azure est basée sur la conformité SOC 2, le chiffrement, l’authentification ainsi que l’identité et l’accès à travers des ID de sécurité utilisateur et de groupe dans les filtres de Recherche Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: cgronlun
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/19/2018
ms.author: heidist
ms.openlocfilehash: c3aa4883e33b1f3494f8502fe7f8b12f7d64a72f
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="security-and-controlled-access-in-azure-search"></a>Sécurité et contrôle d’accès dans Recherche Azure

Recherche Azure est [conforme à la norme SOC 2](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports), avec une architecture de sécurité complète incluant la sécurité physique, les transmissions chiffrées, le stockage chiffré et les dispositifs de protection logiciels à l’échelle de la plateforme. Sur le plan opérationnel, Recherche Azure accepte uniquement les demandes authentifiées. Si vous le souhaitez, vous pouvez ajouter des contrôles d’accès par utilisateur au contenu. Cet article traite de la sécurité au niveau de chaque couche, en se centrant principalement sur la façon dont les données et les opérations sont sécurisées dans Recherche Azure.

![Diagramme de blocs des couches de sécurité](media/search-security-overview/azsearch-security-diagram.png)

## <a name="physical-security"></a>Sécurité physique

Les centres de données Microsoft fournissent une sécurité physique de pointe leader du secteur et sont conformes à un ensemble complet de normes et réglementations. Pour plus d’informations, consultez la page sur les [centres de données globaux](https://www.microsoft.com/cloud-platform/global-datacenters) ou regardez une courte vidéo sur la sécurité des centres de données.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]

## <a name="encrypted-transmission-and-storage"></a>Stockage et transmission chiffrés

Le chiffrement s’étend dans tout le pipeline d’indexation : des connexions aux données indexées stockées dans Recherche Azure, en passant par la transmission.

| Calque de sécurité | Description |
|----------------|-------------|
| Chiffrement en transit | Recherche Azure écoute le port HTTPS 443. Sur la plateforme, les connexions aux services Azure sont chiffrées. |
| Chiffrement au repos | Le chiffrement est entièrement internalisé dans le processus d’indexation, sans aucun impact mesurable sur la durée d’exécution de l’indexation ou la taille de l’index. Il se produit automatiquement lors de toutes les indexations, y compris lors des mises à jour incrémentielles d’un index qui n’est pas entièrement chiffré (créé avant janvier 2018).<br><br>En interne, le chiffrement est basé sur le [chiffrement du service de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits.|
| [Conformité SOC 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) | Tous les services de recherche sont entièrement conformes à AICPA SOC 2, dans tous les centres de données proposant le service Recherche Azure. Pour accéder au rapport complet sur la conformité d’Azure et d’Azure Government à SOC 2 de type II, consultez le document [Azure - and Azure Government SOC 2 Type II Report](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). |

Le chiffrement est interne à Recherche Azure, tandis que les certificats et les clés de chiffrement sont gérés en interne par Microsoft et appliqués universellement. Vous ne pouvez pas activer ou désactiver le chiffrement, gérer ou substituer vos propres clés, ni afficher les paramètres de chiffrement dans le portail ou par programme. 

Le chiffrement au repos a été annoncé le 24 janvier 2018 et s’applique à tous les niveaux de service, y compris les services partagés (gratuits), dans toutes les régions. Pour un chiffrement complet, les index créés avant cette date doivent être supprimés et recréés afin que le chiffrement soit effectué. Dans le cas contraire, seules les nouvelles données ajoutées après le 24 janvier sont chiffrées.

## <a name="azure-wide-logical-security"></a>Sécurité logique à l’échelle d’Azure

Plusieurs mécanismes de sécurité sont disponibles dans Azure Stack et, de ce fait, automatiquement disponibles pour les ressources Recherche Azure que vous créez.

+ [Verrous au niveau des abonnements ou des ressources pour empêcher la suppression](../azure-resource-manager/resource-group-lock-resources.md)
+ [Contrôle d'accès en fonction du rôle (RBAC) pour contrôler l’accès aux informations et aux opérations d’administration](../active-directory/role-based-access-control-what-is.md)

Tous les services Azure prennent en charge les contrôles d’accès en fonction du rôle (RBAC) pour permettre une définition des niveaux d’accès cohérente à travers tous les services. Par exemple, l'affichage de données sensibles, comme la clé d'administration, est réservé aux rôles Propriétaire et Collaborateur, tandis que l'affichage de l'état du service est disponible aux membres de tous les rôles. RBAC fournit des rôles Propriétaire, Collaborateur et Lecteur. Par défaut, tous les administrateurs de service sont propriétaires.

## <a name="service-access-and-authentication"></a>Accès au service et authentification

Alors que le service Recherche Azure hérite des fonctions de sécurité de la plateforme Azure, il fournit également sa propre authentification basée sur clé. Le type de clé (admin ou requête) détermine le niveau d’accès. La soumission d’une clé valide est considérée comme la preuve que la requête provient d’une entité approuvée. 

L’authentification est requise à chaque requête, chaque requête étant composée d’une clé obligatoire, d’une opération et d’un objet. Quand ils sont chaînés, les deux niveaux d’autorisation (totale ou lecture seule) et le contexte sont suffisants pour fournir une sécurité couvrant l’ensemble des opérations de service. 

|Clé|Description|limites|  
|---------|-----------------|------------|  
|Admin|Accorde des droits d’accès complets à toutes les opérations, avec notamment la possibilité de gérer le service ou de créer et supprimer des index, des indexeurs et des sources de données.<br /><br /> Deux **clés API** d’administration, appelées clés *principale* et *secondaire* dans le portail, sont générées quand le service est créé et peuvent être régénérées individuellement à la demande. La possession de deux clés permet de substituer une clé quand l’autre est utilisée pour un accès continu au service.<br /><br /> Les clés d’administration sont spécifiées uniquement dans les en-têtes de requête HTTP. Vous ne pouvez pas insérer de clé API d’administration dans une URL.|2 max. par service|  
|Requête|Accorde un accès en lecture seule aux index et aux documents. Ces clés sont généralement distribuées aux applications clientes qui émettent des demandes de recherche.<br /><br /> Les clés de requête sont créées à la demande. Vous pouvez les créer manuellement dans le portail ou par programme via l’[API REST de gestion](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Les clés de requête peuvent être spécifiées dans un en-tête de requête HTTP pour les opérations de recherche, de suggestion ou de consultation. Vous pouvez également transmettre une clé de requête en tant que paramètre pour une URL. Selon la façon dont votre application cliente formule la demande, il peut être plus facile de transmettre la clé en tant que paramètre de requête :<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01&api-key=A8DA81E03F809FE166ADDB183E9ED84D`|50 par service|  

 Visuellement, il n’existe aucune distinction entre une clé d’administration et une clé de requête. Les deux clés sont des chaînes composées de 32 caractères alphanumériques générés de façon aléatoire. Si vous n’êtes pas sûr du type de clé spécifié dans votre application, vous pouvez [vérifier les valeurs de clé dans le portail](https://portal.azure.com) ou utiliser l’[API REST](https://docs.microsoft.com/rest/api/searchmanagement/) pour retourner la valeur et le type de clé.  

> [!NOTE]  
>  L’insertion de données sensibles comme une `api-key` dans l’URI de requête est considérée comme une pratique peu sécurisée. C’est pourquoi Recherche Azure accepte uniquement une clé de requête sous forme de `api-key` dans la chaîne de requête, et il est conseillé de procéder autrement, sauf si le contenu de l'index doit être accessible au public. En règle générale, nous vous recommandons de transmettre votre `api-key` en tant qu'en-tête de demande.  

### <a name="how-to-find-the-access-keys-for-your-service"></a>Comment rechercher les clés d’accès pour votre service

Vous pouvez obtenir les clés d’accès dans le portail ou via l’[API REST de gestion](https://docs.microsoft.com/rest/api/searchmanagement/). Pour plus d’informations, consultez [Gérer les clés](search-manage.md#manage-api-keys).

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Répertoriez les [services de recherche](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pour votre abonnement.
3. Sélectionnez le service, puis sur la page du service, recherchez **Paramètres** >**Clés** pour afficher les clés d’administration et de requête.

![Page du portail, section Paramètres\Clés](media/search-security-overview/settings-keys.png)

## <a name="index-access"></a>Accès aux index

Dans Recherche Azure, les index individuels ne sont pas des objets sécurisables. En effet, l’accès aux index est déterminé au niveau de la couche de service (accès en lecture ou en écriture) et du contexte d’une opération.

En cas d’accès de l’utilisateur final, vous pouvez structurer les demandes de requête dans votre application pour établir la connexion à l’aide d’une clé de requête, qui configure toutes les demandes en mode de lecture seule et qui inclut l’index spécifique utilisé par votre application. Dans une demande de requête, il est impossible de joindre des index ou d’accéder simultanément à plusieurs index. Ainsi, toutes les demandes ciblent un index unique par définition. Par conséquent, la structure de la demande de requête proprement dite (une clé plus un index unique cible) définit la limite de sécurité.

Il n’existe aucune différence entre l’accès administrateur et l’accès développeur aux index : tous deux doivent disposer d’un accès en écriture pour pouvoir créer, supprimer et mettre à jour des objets gérés par le service. Toute personne disposant d’une clé d’administration pour votre service peut lire, modifier ou supprimer un index de ce service. En ce qui concerne la protection contre la suppression accidentelle ou malveillante d’index, votre contrôle de code source en interne pour les ressources de code est la solution appropriée pour annuler des suppressions ou des modifications d’index indésirables. Recherche Azure dispose d’un système de basculement dans le cluster pour garantir sa disponibilité, mais il ne stocke pas et n’exécute pas le code propriétaire que vous avez utilisé pour créer ou charger des index.

Pour les solutions d’architecture mutualisée qui nécessitent des limites de sécurité au niveau des index, ces solutions incluent généralement un niveau intermédiaire, que les clients utilisent pour gérer l’isolation des index. Pour plus d’informations sur les cas d’usage d’architecture mutualisée, consultez [Modèles de conception pour les applications SaaS mutualisées et Recherche Azure](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access-from-client-apps"></a>Accès administrateur à partir d’applications clientes

L’API REST de gestion d’Azure Search est une extension d’Azure Resource Manager et partage ses dépendances. Active Directory est donc une condition préalable à l’administration de service d’Azure Search. Toutes les demandes administratives provenant du code client doivent être authentifiées à l’aide d’Azure Active Directory avant d’être envoyées vers Resource Manager.

L’en-tête des demandes de données concernant le point de terminaison du service Recherche Azure, tel que Créer un index (API REST du service Recherche Azure) ou Rechercher des documents (API REST du service Recherche Azure), comprend une clé API.

Si votre code d’application gère les opérations d’administration de service, ainsi que les opérations de données sur des documents ou des index de recherche, implémentez deux approches d’authentification dans votre code : la clé d’accès native à Recherche Azure et la méthodologie d’authentification Active Directory requise par Resource Manager. 

Pour plus d’informations sur la structuration d’une demande dans Recherche Azure, consultez [REST du service Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/). Pour plus d’informations sur les exigences d’authentification de Resource Manager, consultez [Utiliser l’API d’authentification de Resource Manager pour accéder aux abonnements](../azure-resource-manager/resource-manager-api-authentication.md).

## <a name="user-access-to-index-content"></a>Accès utilisateur au contenu d’index

L’accès par utilisateur au contenu d’un index est implémenté à travers les filtres de sécurité de vos requêtes, qui retournent les documents associés à une identité de sécurité donnée. Au lieu des rôles prédéfinis et des attributions de rôles, le contrôle d’accès basé sur l’identité est implémenté en tant que filtre qui limite les résultats de recherche de documents et de contenu en fonction des identités. Le tableau suivant décrit les deux approches permettant de filtrer les résultats de recherche de contenu non autorisé.

| Approche | Description |
|----------|-------------|
|[Filtrage de sécurité basé sur les filtres d’identité](search-security-trimming-for-azure-search.md)  | Cet article décrit le workflow de base pour l’implémentation du contrôle d’accès basé sur l’identité de l’utilisateur. Il décrit l’ajout d’identificateurs de sécurité à un index, puis le filtrage relatif à ce champ qui permet d’omettre les résultats de contenu non autorisé. |
|[Filtrage de sécurité basé sur les identités Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Cet article développe l’article précédent, en indiquant les étapes à suivre pour récupérer des identités d’Azure Active Directory (AAD), l’un des [services gratuits](https://azure.microsoft.com/free/) de la plateforme cloud Azure. |

## <a name="table-permissioned-operations"></a>Tableau : opérations autorisées

Le tableau suivant récapitule les opérations autorisées dans Recherche Azure, en indiquant la clé qui déverrouille l’accès à une opération particulière.

| Opération | Autorisations |
|-----------|-------------------------|
| Créer un service | Détenteur de l'abonnement Azure|
| Mettre à l’échelle un service | Clé d’administration, Propriétaire ou Collaborateur RBAC sur la ressource  |
| Supprimer un service | Clé d’administration, Propriétaire ou Collaborateur RBAC sur la ressource |
| Créer, modifier et supprimer des objets du service : <br>Index et composants (y compris les définitions de l’analyseur, les profils de score et les options CORS), indexeurs, sources de données, synonymes et générateurs de suggestions. | Clé d’administration, Propriétaire ou Collaborateur RBAC sur la ressource  |
| Interroger un index | Clé d’administration ou clé de requête (RBAC non applicable) |
| Interroger des informations système, telles que l’obtention de statistiques, de comptes et de listes d’objets. | Clé d’administration, RBAC sur la ressource (Propriétaire, Collaborateur ou Lecteur) |
| Gérer les clés d’administration | Clé d’administration, Propriétaire ou Collaborateur RBAC sur la ressource |
| Gérer les clés de requête |  Clé d’administration, Propriétaire ou Collaborateur RBAC sur la ressource Le Lecteur RBAC peut afficher les clés de requête. |


## <a name="see-also"></a>Voir aussi

+ [Bien démarrer avec .NET (décrit l’utilisation d’une clé d’administration pour créer un index)](search-create-index-dotnet.md)
+ [Bien démarrer avec REST (décrit l’utilisation d’une clé d’administration pour créer un index)](search-create-index-rest-api.md)
+ [Contrôle d’accès basé sur l’identité à l’aide des filtres Recherche Azure](search-security-trimming-for-azure-search.md)
+ [Contrôle d’accès à Active Directory basé sur l’identité à l’aide des filtres Recherche Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtres dans Recherche Azure](search-filters.md)