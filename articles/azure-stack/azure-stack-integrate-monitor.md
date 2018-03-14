---
title: "Intégrer une solution de surveillance externe à Azure Stack | Microsoft Docs"
description: "Découvrez comment intégrer Azure Stack à une solution de surveillance externe dans votre centre de données."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 3435ada40afb9f1c6e57be64d1b9086d0cdaefd9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Intégrer une solution de surveillance externe à Azure Stack

Pour une surveillance externe de l’infrastructure Azure Stack, vous devez surveiller logiciels, ordinateurs physiques et commutateurs de réseau physique d’Azure Stack. Chacun de ces composants offre une méthode de récupération des informations d’intégrité et d’alerte  :

- Le logiciel Azure Stack offre une API basée sur REST pour récupérer l’intégrité et les alertes. Grâce aux technologies à définition logicielle, telles que les espaces de stockage direct, les alertes et l’intégrité du stockage font partie de la surveillance logicielle.
- Les ordinateurs physiques peuvent rendre disponibles des informations relatives à l’intégrité et aux alertes par le biais des contrôleurs de gestion de la carte de base (BMC).
- Les périphériques réseau physiques peuvent mettre à disposition les informations relatives à l’intégrité et aux alertes par le biais du protocole SNMP.

Chaque solution Azure Stack est fournie avec un hôte de cycle de vie du matériel. Cet hôte exécute le logiciel de surveillance du fournisseur de matériel OEM (Original Equipment Manufacturer) pour les serveurs physiques et les périphériques réseau. Le cas échéant, vous pouvez contourner ces solutions de surveillance en intégrant directement les solutions de surveillance existantes dans votre centre de données.

> [!IMPORTANT]
> La solution de surveillance externe que vous utilisez doit être sans agent. Vous ne pouvez pas installer d’agents tiers à l’intérieur des composants de Azure Stack.

Le diagramme suivant montre le flux de trafic entre un système intégré Azure Stack, l’hôte de cycle de vie du matériel, une solution de surveillance externe et un système de collecte de données et de création de tickets externe.

![Schéma montrant le trafic entre Azure Stack, la surveillance et la solution de création de tickets.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

Cet article explique comment intégrer Azure Stack à des solutions de surveillance externes, telles que System Center Operations Manager et Nagios. Il traite également de l’utilisation des alertes par programmation en ayant recours à PowerShell ou à des appels d’API REST.

## <a name="integrate-with-operations-manager"></a>Intégrer Operations Manager

Vous pouvez utiliser Operations Manager pour une surveillance externe d’Azure Stack. Le Pack d’administration de System Center pour Microsoft Azure Stack vous permet de surveiller plusieurs déploiements Azure Stack avec une seule instance d’Operations Manager. Le Pack d’administration utilise les API REST du fournisseur de ressources d’intégrité et du fournisseur de ressources de mise à jour pour communiquer avec Azure Stack. Si vous envisagez de contourner le logiciel de surveillance OEM qui s’exécute sur l’hôte de cycle de vie du matériel, vous pouvez installer les Packs d’administration du revendeur pour surveiller les serveurs physiques. Vous pouvez également utiliser la détection des périphériques réseau Operations Manager pour surveiller les commutateurs réseau.

Le pack d’administration pour Azure Stack offre les fonctionnalités suivantes :

- Possibilité de gérer plusieurs déploiements Azure Stack
- Support pour Azure Active Directory (Azure AD) ou Active Directory Federation Services (AD FS)
- Possibilité de récupérer et de fermer des alertes
- Présence d’un tableau de bord d’intégrité et de capacité
- Détection du mode de maintenance automatique lorsque les correctifs et mises à jour sont en cours
- Tâches de mise à jour forcée pour le déploiement et la région
- Ajout possible d’informations personnalisées à une région
- Prise en charge de la notification et de la création de rapports

Vous pouvez télécharger le Pack d’administration de System Center pour Microsoft Azure Stack et le [guide de l’utilisateur](https://www.microsoft.com/en-us/download/details.aspx?id=55184) associé ici, ou directement à partir d’Operations Manager.

Pour une solution de création de tickets, vous pouvez intégrer Operations Manager à System Center Service Manager. Le connecteur du produit intégré assure la communication bidirectionnelle qui vous permet de fermer une alerte dans Azure Stack et Operations Manager après la résolution d’une demande de service dans Service Manager.

Le schéma suivant illustre l’intégration d’Azure Stack à un déploiement de System Center existant. Vous pouvez automatiser davantage Service Manager avec System Center Orchestrator ou Service Management Automation (SMA) pour effectuer des opérations dans Azure Stack.

![Schéma illustrant l’intégration avec OM, Service Manager et SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Intégrer Nagios

Un plug-in de surveillance Nagios a été développé en même temps que les solutions Cloudbase partenaires ; il est disponible sous licence de logiciel libre et permissive – MIT (Massachusetts Institute of Technology).

Le plug-in est écrit dans Python et s’appuie sur l’API REST du fournisseur de ressources d’intégrité. Il offre des fonctionnalités de base pour récupérer et fermer des alertes dans Azure Stack. À l’instar du Pack d’administration de System Center, il vous permet d’ajouter plusieurs déploiements Azure Stack et d’envoyer des notifications.

Le plug-in fonctionne avec Nagios Enterprise et Nagios Core. Vous pouvez le télécharger [ici](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Le site de téléchargement comporte également des informations sur l’installation et la configuration.

### <a name="plugin-parameters"></a>Paramètres du plug-in

Configurez le fichier du plug-in « Azurestack_plugin.py » avec les paramètres suivants :

| Paramètre | Description | exemples |
|---------|---------|---------|
| *arm_endpoint* | Point de terminaison Azure Resource Manager (administrateur) |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Point de terminaison Azure Resource Manager (administrateur)  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | ID d’abonnement administrateur | À récupérer via le portail d’administration ou via PowerShell |
| *User_name* | Nom d’utilisateur de l’abonnement opérateur | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Mot de passe de l’abonnement opérateur | monmotdepasse |
| *Client_id* | Client | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Nom de la région Azure Stack | local |
|  |  |

*Le GUID PowerShell qui est fourni est universel. Vous pouvez l’utiliser pour chaque déploiement.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Utiliser PowerShell pour surveiller l’intégrité et les alertes

Si vous n’utilisez pas Operations Manager, Nagios ou une solution s’appuyant sur Nagios, vous pouvez utiliser PowerShell pour activer un large éventail de solutions de surveillance à intégrer à Azure Stack.
 
1. Si vous voulez utiliser PowerShell, assurez-vous que [PowerShell est installé et configuré](azure-stack-powershell-configure-quickstart.md) pour un environnement d’opérateur Azure Stack. Installez PowerShell sur un ordinateur local qui peut atteindre le point de terminaison du Gestionnaire des ressources (administrateur) (https://adminmanagement.[région].[Nom_de_domaine_complet_externe]).

2. Exécutez les commandes suivantes pour vous connecter à l’environnement Azure Stack en tant qu’opérateur Azure Stack :

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. Accédez au répertoire où vous avez installé les [outils Azure Stack](https://github.com/Azure/AzureStack-Tools) dans le cadre de l’installation de PowerShell, par exemple c:\azurestack-tools-master. Ensuite, accédez au répertoire Infrastructure et exécutez la commande suivante pour importer le module Infrastructure :

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. Utilisez les commandes telles que les exemples suivants pour les alertes :
   ```PowerShell
   #Retrieve all alerts
   Get-AzsAlert -location [Region]

   #Filter for active alerts
   $Active=Get-AzsAlert -location [Region] | Where {$_.State -eq "active"}
   $Active

   #Close alert
   Close-AzsAlert -location [Region] -AlertID "ID"

   #Retrieve resource provider health
   Get-AzsResourceProviderHealths -location [Region]

   #Retrieve infrastructure role instance health
   Get-AzsInfrastructureRoleHealths -location [Region]
   ```

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>Utiliser l’API REST pour surveiller l’intégrité et les alertes

Vous pouvez utiliser des appels de l’API REST pour recevoir ou fermer des alertes et obtenir l’intégrité des fournisseurs de ressources.

### <a name="get-alert"></a>Obtenir une alerte

**Requête**

La requête obtient toutes les alertes actives et fermées pour l’abonnement du fournisseur par défaut. Il n’existe aucun corps de demande.


|Méthode  |URI de demande  |
|---------|---------|
|GET     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01"      |
|     |         |

**Arguments**

|Argument  |Description  |
|---------|---------|
|armendpoint     |  Point de terminaison Azure Resource Manager de votre environnement Azure Stack, au format https://adminmanagement.{NomRégion}.{FQDN externe}. Par exemple, si le nom de domaine complet externe est *azurestack.external* et que le nom de la région est *local*, le point de terminaison du Gestionnaire des ressources est https://adminmanagement.local.azurestack.external.       |
|subid     |   ID d’abonnement de l’utilisateur qui effectue l’appel. Vous pouvez utiliser cette API pour interroger uniquement avec un utilisateur qui a l’autorisation sur l’abonnement du fournisseur par défaut.      |
|RegionName     |    Nom de la région du déploiement Azure Stack.     |
|api-version     |  Version du protocole utilisé pour effectuer cette requête. Vous devez utiliser 2016-05-01.      |
|     |         |

**Réponse**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/Alerts?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Active",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},

…
```

**Détails de la réponse**


|  Argument  |Description  |
|---------|---------|
|*id*     |      ID unique de l’alerte.   |
|*name*     |     Nom interne de l’alerte.   |
|*type*     |     Définition de la ressource.    |
|*location*     |       Nom de la région.     |
|*balises*     |   Balises de ressource.     |
|*closedtimestamp*    |  Heure UTC à laquelle l’alerte a été fermée.    |
|*createdtimestamp*     |     Heure UTC à laquelle l’alerte a été créée.   |
|*description*     |    Description de l’alerte.     |
|*faultid*     | Composant concerné.        |
|*alertid*     |  ID unique de l’alerte.       |
|*faulttypeid*     |  Type unique d’un composant défectueux.       |
|*lastupdatedtimestamp*     |   Heure UTC à laquelle les informations d’alerte ont été modifiées pour la dernière fois.    |
|*healthstate*     | État d’intégrité globale.        |
|*name*     |   Nom de l’alerte spécifique.      |
|*fabricname*     |    Nom de la structure inscrite du composant défectueux.   |
|*description*     |  Description du composant de la structure inscrite.   |
|*servicetype*     |   Type du service de la structure inscrite.   |
|*remediation*     |   Étapes de correction recommandées.    |
|*type*     |   Type d’alerte.    |
|*resourceRegistrationid*    |     ID de la ressource inscrite concernée.    |
|*resourceProviderRegistrationID*   |    ID du fournisseur de ressources inscrit pour le composant concerné.  |
|*serviceregistrationid*     |    ID du service inscrit.   |
|*severity*     |     Gravité de l'alerte.  |
|*state*     |    État de l’alerte.   |
|*title*     |    Titre de l’alerte.   |
|*impactedresourceid*     |     ID de la ressource impactée.    |
|*ImpactedresourceDisplayName*     |     Nom de la ressource impactée.  |
|*closedByUserAlias*     |   Utilisateur qui a fermé l’alerte.      |

### <a name="close-alert"></a>Fermer une alerte

**Requête**

La requête ferme une alerte par son ID unique.

|Méthode    |URI de demande  |
|---------|---------|
|PUT     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01"    |

**Arguments**


|Argument  |Description  |
|---------|---------|
|*armendpoint*     |   Point de terminaison du Gestionnaire des ressources de votre environnement Azure Stack, au format https://adminmanagement.{NomRégion}.{FQDN externe}. Par exemple, si le nom de domaine complet externe est *azurestack.external* et que le nom de la région est *local*, le point de terminaison du Gestionnaire des ressources est https://adminmanagement.local.azurestack.external.      |
|*subid*     |    ID d’abonnement de l’utilisateur qui effectue l’appel. Vous pouvez utiliser cette API pour interroger uniquement avec un utilisateur qui a l’autorisation sur l’abonnement du fournisseur par défaut.     |
|*RegionName*     |   Nom de la région du déploiement Azure Stack.      |
|*api-version*     |    Version du protocole utilisé pour effectuer cette requête. Vous devez utiliser 2016-05-01.     |
|*alertid*     |    ID unique de l’alerte.     |

**Corps**

```json

{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"2017-08-10T20:18:58.1584868Z",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```
**Réponse**

```http
PUT https://adminmanagement.local.azurestack.external//subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```

**Détails de la réponse**


|  Argument  |Description  |
|---------|---------|
|*id*     |      ID unique de l’alerte.   |
|*name*     |     Nom interne de l’alerte.   |
|*type*     |     Définition de la ressource.    |
|*location*     |       Nom de la région.     |
|*balises*     |   Balises de ressource.     |
|*closedtimestamp*    |  Heure UTC à laquelle l’alerte a été fermée.    |
|*createdtimestamp*     |     Heure UTC à laquelle l’alerte a été créée.   |
|*Description*     |    Description de l’alerte.     |
|*faultid*     | Composant concerné.        |
|*alertid*     |  ID unique de l’alerte.       |
|*faulttypeid*     |  Type unique d’un composant défectueux.       |
|*lastupdatedtimestamp*     |   Heure UTC à laquelle les informations d’alerte ont été modifiées pour la dernière fois.    |
|*healthstate*     | État d’intégrité globale.        |
|*name*     |   Nom de l’alerte spécifique.      |
|*fabricname*     |    Nom de la structure inscrite du composant défectueux.   |
|*description*     |  Description du composant de la structure inscrite.   |
|*servicetype*     |   Type du service de la structure inscrite.   |
|*remediation*     |   Étapes de correction recommandées.    |
|*type*     |   Type d’alerte.    |
|*resourceRegistrationid*    |     ID de la ressource inscrite concernée.    |
|*resourceProviderRegistrationID*   |    ID du fournisseur de ressources inscrit pour le composant concerné.  |
|*serviceregistrationid*     |    ID du service inscrit.   |
|*severity*     |     Gravité de l'alerte.  |
|*state*     |    État de l’alerte.   |
|*title*     |    Titre de l’alerte.   |
|*impactedresourceid*     |     ID de la ressource impactée.    |
|*ImpactedresourceDisplayName*     |     Nom de la ressource impactée.  |
|*closedByUserAlias*     |   Utilisateur qui a fermé l’alerte.      |

### <a name="get-resource-provider-health"></a>Obtenir l’intégrité du fournisseur de ressources

**Requête**

La requête obtient l’état d’intégrité de tous les fournisseurs de ressources inscrits.


|Méthode  |URI de demande  |
|---------|---------|
|GET    |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01"   |


**Arguments**


|Arguments  |Description  |
|---------|---------|
|*armendpoint*     |    Point de terminaison du Gestionnaire des ressources de votre environnement Azure Stack, au format https://adminmanagement.{NomRégion}.{FQDN externe}. Par exemple, si le nom de domaine complet externe est azurestack.external et que le nom de la région est local, le point de terminaison du Gestionnaire des ressources est https://adminmanagement.local.azurestack.external.     |
|*subid*     |     ID d’abonnement de l’utilisateur qui effectue l’appel. Vous pouvez utiliser cette API pour interroger uniquement avec un utilisateur qui a l’autorisation sur l’abonnement du fournisseur par défaut.    |
|*RegionName*     |     Nom de la région du déploiement Azure Stack.    |
|*api-version*     |   Version du protocole utilisé pour effectuer cette requête. Vous devez utiliser 2016-05-01.      |


**Réponse**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths?api-version=2016-05-01
```

```json
{
"value":[
{
"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"name":"03ccf38f-f6b1-4540-9dc8ec7b6389ecca",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths",
"location":"local",
"tags":{},
"properties":{
"registrationId":"03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"displayName":"Key Vault",
"namespace":"Microsoft.KeyVault.Admin",
"routePrefix":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local",
"serviceLocation":"local",
"infraURI":"/subscriptions/4aa97de3-6b83-4582-86e1-65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local/infraRoles/Key Vault",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},
"healthState":"Healthy"
}
}

…
```
**Détails de la réponse**


|Argument  |Description  |
|---------|---------|
|*Id*     |   ID unique de l’alerte.      |
|*name*     |  Nom interne de l’alerte.       |
|*type*     |  Définition de la ressource.       |
|*location*     |  Nom de la région.       |
|*balises*     |     Balises de ressource.    |
|*registrationId*     |   Inscription unique pour le fournisseur de ressources.      |
|*displayName*     |Nom complet du fournisseur de ressources.        |
|*namespace*     |   Espace de noms API que le fournisseur de ressources implémente.       |
|*routePrefix*     |    URI pour interagir avec le fournisseur de ressources.     |
|*serviceLocation*     |   Région associée à ce fournisseur de ressources.      |
|*infraURI*     |   URI du fournisseur de ressources répertorié en tant que rôle d’infrastructure.      |
|*alertSummary*     |   Résumé de l’alerte critique et d’avertissement associée au fournisseur de ressources.      |
|*healthState*     |    État d’intégrité du fournisseur de ressources.     |


### <a name="get-resource-health"></a>Obtenir l’intégrité des ressources

La requête obtient l’état d’intégrité d’un fournisseur de ressources inscrit.

**Requête**

|Méthode  |URI de demande  |
|---------|---------|
|GET     |     https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01"    |

**Arguments**

|Arguments  |Description  |
|---------|---------|
|*armendpoint*     |    Point de terminaison du Gestionnaire des ressources de votre environnement Azure Stack, au format https://adminmanagement.{NomRégion}.{FQDN externe}. Par exemple, si le nom de domaine complet externe est azurestack.external et que le nom de la région est local, le point de terminaison du Gestionnaire des ressources est https://adminmanagement.local.azurestack.external.     |
|*subid*     |ID d’abonnement de l’utilisateur qui effectue l’appel. Vous pouvez utiliser cette API pour interroger uniquement avec un utilisateur qui a l’autorisation sur l’abonnement du fournisseur par défaut.         |
|*RegionName*     |  Nom de la région du déploiement Azure Stack.       |
|*api-version*     |  Version du protocole utilisé pour effectuer cette requête. Vous devez utiliser 2016-05-01.       |
|*RegistrationID* |ID d’inscription d’un fournisseur de ressources spécifique. |

**Réponse**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca /resourceHealths?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":
[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/472aaaa6-3f63-43fa-a489-4fd9094e235f/resourceHealths/028c3916-ab86-4e7f-b5c2-0468e607915c",
"name":"028c3916-ab86-4e7f-b5c2-0468e607915c",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths/resourceHealths",
"location":"local",
"tags":{},
"properties":
{"registrationId":"028c3916-ab86-4e7f-b5c2 0468e607915c","namespace":"Microsoft.Fabric.Admin","routePrefix":"/subscriptions/4aa97de3-6b83-4582-86e1 65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local",
"resourceType":"infraRoles",
"resourceName":"Privileged endpoint",
"usageMetrics":[],
"resourceLocation":"local",
"resourceURI":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/Privileged endpoint",
"rpRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},"healthState":"Unknown"
}
}
…
```

**Détails de la réponse**

|Argument  |Description  |
|---------|---------|
|*Id*     |   ID unique de l’alerte.      |
|*name*     |  Nom interne de l’alerte.       |
|*type*     |  Définition de la ressource.       |
|*location*     |  Nom de la région.       |
|*balises*     |     Balises de ressource.    |
|*registrationId*     |   Inscription unique pour le fournisseur de ressources.      |
|*resourceType*     |Type de ressource.        |
|*resourceName*     |   Nom de la ressource.   |
|*usageMetrics*     |    Métrique d’utilisation pour la ressource.     |
|*resourceLocation*     |   Nom de la région où elle est déployée.      |
|*resourceURI*     |   URI de la ressource.   |
|*alertSummary*     |   Résumé des alertes critiques et d’avertissement, de l’état d’intégrité.     |

## <a name="learn-more"></a>En savoir plus

Pour plus d’informations sur la surveillance de l’intégrité intégrée, consultez [Surveiller l’intégrité et les alertes dans Azure Stack](azure-stack-monitor-health.md).


## <a name="next-steps"></a>Étapes suivantes

[Intégration de la sécurité](azure-stack-integrate-security.md)