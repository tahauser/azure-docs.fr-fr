---
title: Familiarisation avec les rôles, les autorisations et la sécurité dans Azure Monitor | Microsoft Docs
description: Découvrez comment utiliser les rôles intégrés et les autorisations d’Azure Monitor pour restreindre l’accès à l’analyse des ressources.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2686e53b-72f0-4312-bcd3-3dc1b4a9b912
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: johnkem
ms.openlocfilehash: 81f083b799e359f69605de22c30d3adc4480e44b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Familiarisation avec les rôles, les autorisations et la sécurité dans Azure Monitor
De nombreuses équipes ont besoin de réglementer strictement l’accès aux données et aux paramètres d’analyse. Par exemple, si des membres de votre équipe travaillent exclusivement sur l’analyse (ingénieurs du support technique, ingénieurs devops) ou si vous utilisez un fournisseur de services gérés, vous souhaiterez leur accorder l’accès à l’analyse des données tout en limitant leur capacité à créer, modifier ou supprimer des ressources. Cet article montre comment appliquer un rôle RBAC d’analyse intégré à un utilisateur dans Azure ou créer vos propres rôles personnalisés pour un utilisateur qui a rapidement besoin d’autorisations limitées pour l’analyse. Il évoque ensuite les considérations de sécurité pour vos ressources liées à Azure Monitor et comment vous pouvez restreindre l’accès aux données contenues.

## <a name="built-in-monitoring-roles"></a>Rôles de surveillance intégrés
Les rôles intégrés d’Azure Monitor sont conçus pour vous aider à limiter l’accès aux ressources dans un abonnement tout en permettant au responsable de l’infrastructure d’analyse d’obtenir et de configurer les données nécessaires. Azure Monitor propose deux rôles prêts à l’emploi : un lecteur d’analyse et un contributeur d’analyse.

### <a name="monitoring-reader"></a>Lecteur d’analyse
Les personnes affectées au rôle de lecteur d’analyse peuvent afficher toutes les données d’analyse dans un abonnement, mais ne peuvent pas modifier de ressource ou modifier les paramètres relatifs à l’analyse des ressources. Ce rôle est approprié pour les utilisateurs dans une organisation, tels que les ingénieurs de support ou d’opération, qui doivent être en mesure de faire ce qui suit :

* Afficher des tableaux de bord d’analyse dans le portail et créer leurs propres tableaux de bord privés d’analyse.
* Afficher les règles d’alerte définies dans [Alertes Azure](monitoring-overview-unified-alerts.md)
* Requête de mesures avec l’[API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931930.aspx), les [applets de commande PowerShell](insights-powershell-samples.md) ou le [CLI multiplateforme](insights-cli-samples.md).
* Interroger le journal d’activité via le portail, l’API REST Azure Monitor, les applets de commande PowerShell ou le CLI multiplateforme.
* Affichez les [Paramètres de diagnostic](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) pour une ressource.
* Afficher le [profil de journalisation](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) pour un abonnement.
* Affichez les paramètres de mise à l’échelle automatique.
* Afficher les activités et paramètres d’alerte.
* Accéder aux données Application Insights et affichez les données dans AI Analytics.
* Rechercher des données d’espace de travail Log Analytics, notamment les données d’utilisation de l’espace de travail.
* Afficher les groupes de gestion Log Analytics.
* Récupérer le schéma de recherche Log Analytics.
* Répertorier les Intelligence Packs Log Analytics.
* Récupérer et exécuter les recherches enregistrées Log Analytics.
* Récupérer la configuration du stockage Log Analytics.

> [!NOTE]
> Ce rôle ne donne pas l’accès en lecture aux données de journal diffusées vers un hub d’événements ou stockées dans un compte de stockage. [Consultez ce qui suit](#security-considerations-for-monitoring-data) pour plus d’informations sur la configuration de l’accès à ces ressources.
> 
> 

### <a name="monitoring-contributor"></a>Contributeur d’analyse
Les personnes affectées au rôle de contributeur d’analyse peuvent afficher toutes les données d’analyse dans un abonnement, et créer ou modifier des paramètres d’analyse, mais ne peuvent pas modifier d’autres ressources. Ce rôle est un surensemble du rôle lecteur d’analyse et est approprié pour les membres de l’équipe d’analyse d’une organisation ou les fournisseurs de services gérés qui, outre les autorisations ci-dessus, doivent également être en mesure de faire ce qui suit :

* Publier des tableaux de bord d’analyse en tant que tableau de bord partagé.
* Définissez les [Paramètres de diagnostic](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) pour une ressource.*
* Définir le [profil de journal](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) pour un abonnement.*
* Définir l’activité et les paramètres de règles d’alerte via [Alertes Azure](monitoring-overview-unified-alerts.md).
* Créer des tests web et composants Application Insights.
* Répertorier les clés partagées d’espace de travail Log Analytics.
* Activer ou désactiver les Intelligence Packs Log Analytics.
* Créer ou supprimer des recherches enregistrées Log Analytics.
* Créer ou supprimer la configuration du stockage Log Analytics.

*L’utilisateur doit aussi séparément obtenir l’autorisation ListKeys sur la ressource cible (compte de stockage ou espace de noms de hub d’événements) pour définir un profil de journalisation ou un paramètre de diagnostic.

> [!NOTE]
> Ce rôle ne donne pas l’accès en lecture aux données de journal diffusées vers un hub d’événements ou stockées dans un compte de stockage. [Consultez ce qui suit](#security-considerations-for-monitoring-data) pour plus d’informations sur la configuration de l’accès à ces ressources.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Autorisations d’analyse et rôles RBAC personnalisés
Si les rôles intégrés ci-dessus ne répondent pas aux besoins exacts de votre équipe, vous pouvez [créer un rôle RBAC personnalisé](../active-directory/role-based-access-control-custom-roles.md) avec des autorisations plus granulaires. Voici les opérations RBAC d’Azure Monitor courantes avec leurs descriptions.

| Opération | Description |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |Lire/écrire/supprimer des groupes d’actions. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Lire/écrire/supprimer des alertes de journal d’activité. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Lire/écrire/supprimer des règles d’alerte (à partir d’alertes classiques). |
| Microsoft.Insights/AlertRules/Incidents/Read |Liste d’incidents (historique de la règle d’alerte déclenchée) pour les règles d’alerte. Cela s’applique uniquement au portail. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Paramètres de mise à l’échelle automatique en lecture/écriture/suppression. |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |Paramètres de diagnostic en lecture/écriture/suppression. |
| Microsoft.Insights/EventCategories/Read |Énumérer toutes les catégories possibles dans le journal d’activité. Utilisé par le Portail Azure. |
| Microsoft.Insights/eventtypes/digestevents/Read |Cette autorisation est nécessaire pour les utilisateurs qui doivent accéder aux journaux d’activité via le portail. |
| Microsoft.Insights/eventtypes/values/Read |Événements du journal d’activité, (événements de gestion) dans un abonnement. Cette autorisation est applicable pour l’accès par programme et portail dans le journal d’activité. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | Lire/écrire/supprimer des paramètres de diagnostic pour les journaux de flux réseau. |
| Microsoft.Insights/LogDefinitions/Read |Cette autorisation est nécessaire pour les utilisateurs qui doivent accéder aux journaux d’activité via le portail. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |Lire/écrire/supprimer des profils de journal (diffusion en continu du journal d’activité vers le Event Hub ou le compte de stockage). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Lire/écrire/supprimer des alertes métriques quasiment en temps réel |
| Microsoft.Insights/MetricDefinitions/Read |Lire des définitions de mesure (liste de types de mesure disponibles pour une ressource). |
| Microsoft.Insights/Metrics/Read |Lire des mesures pour une ressource. |
| Microsoft.Insights/Register/Action |Inscrire le fournisseur de ressources Azure Monitor. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Lire/écrire/supprimer des alertes de journal pour Application Insights. |



> [!NOTE]
> Accéder aux alertes, aux paramètres de diagnostic et aux mesures pour une ressource nécessite que l’utilisateur ait accès en lecture au type de ressource et à la portée de la ressource. La création (« écriture ») d’un profil de journalisation ou de paramètre de diagnostic qui archive sur un compte de stockage ou diffuse vers des hubs d’événements exige que l’utilisateur dispose également de l’autorisation ListKeys sur la ressource cible.
> 
> 

Par exemple, en utilisant le tableau ci-dessus, vous pouvez créer un rôle RBAC personnalisé pour un « lecteur de journal d’activité », comme suit :

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Considérations de sécurité pour l’analyse des données
Les données d’analyse (les fichiers journaux en particulier) peuvent contenir des informations sensibles, comme des adresses IP ou des noms d’utilisateur. La surveillance de données dans Azure se présente sous trois formes de base :

1. Le journal d’activité qui décrit toutes les actions de plan de contrôle sur votre abonnement Azure.
2. Les journaux de diagnostic, qui sont des journaux émis par une ressource.
3. Les mesures, qui sont émises par les ressources.

Ces trois types de données peuvent être stockés dans un compte de stockage ou diffusés vers un hub d’événements, qui sont tous deux des ressources Azure à usage général. Étant donné qu’il s’agit de ressources à usage général, leur création, leur suppression et leur accès sont des opérations privilégiées réservées à un administrateur. Nous vous conseillons d’utiliser les pratiques suivantes pour les ressources liées à l’analyse afin d’éviter une mauvaise utilisation :

* Utilisez un compte de stockage unique, dédié pour l’analyse des données. Si vous devez séparer les données d’analyse sur plusieurs comptes de stockage, ne partagez jamais l’utilisation d’un compte de stockage entre les données d’analyse et les données hors analyse, car cela peut donner par inadvertance l’accès aux données hors analyse à ceux qui doivent uniquement accéder aux données d’analyse (par ex. un SIEM (Security Information and Event Management) tiers).
* Utilisez un espace de noms de hub d’événements ou Service Bus unique pour tous les paramètres de diagnostic pour la même raison que ci-dessus.
* Limitez l’accès aux comptes de stockage liés à l’analyse et aux hubs d’événements en les conservant dans un groupe de ressources distinct et [utilisez les étendues](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) sur vos rôles d’analyse pour limiter l’accès à ce groupe de ressources uniquement.
* N’accordez jamais l’autorisation ListKeys aux comptes de stockage ou hubs d’événements dont la portée comprend l’abonnement lorsqu’un utilisateur doit uniquement accéder aux données d’analyse. Au lieu de cela, accordez ces autorisations à l’utilisateur sur une ressource ou un groupe de ressources (si vous avez un groupe de ressources d’analyse).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Limiter l’accès aux comptes de stockage liés à l’analyse
Lorsqu’un utilisateur ou une application doit accéder à l’analyse des données dans un compte de stockage, vous devez [Générer un SAP de compte](https://msdn.microsoft.com/library/azure/mt584140.aspx) sur le compte de stockage qui contient les données d’analyse avec un accès en lecture au niveau de service pour le stockage d’objets Blob. Dans PowerShell, cela pourrait ressembler à :

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Vous pouvez ensuite donner le jeton à l’entité qui doit lire à partir de ce compte de stockage, et elle pourra alors répertorier et lire à partir de tous les objets Blob dans ce compte de stockage.

Si vous avez besoin de contrôler cette autorisation avec RBAC, vous pouvez également accorder à cette entité l’autorisation Microsoft.Storage/storageAccounts/listkeys/action sur ce compte de stockage particulier. Cela est nécessaire pour les utilisateurs qui doivent être en mesure de définir un paramètre de diagnostic ou un profil de journalisation pour l’archivage sur un compte de stockage. Par exemple, vous pouvez créer le rôle RBAC personnalisé suivant pour un utilisateur ou une application qui a uniquement besoin de lire à partir d’un compte de stockage :

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [!WARNING]
> L’autorisation ListKeys permet à l’utilisateur de répertorier les clés de compte de stockage principales et secondaires. Ces clés accordent à l’utilisateur toutes les autorisations signées (lecture, écriture, création d’objets Blob, suppression d’objets Blob, etc.) sur l’ensemble des services signés (Blob, file d’attente, table, fichier) sur ce compte de stockage. Nous vous recommandons d’utiliser un SAP de compte comme décrit ci-dessus, lorsque cela est possible.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Limiter l’accès aux hubs d’événements liés à l’analyse
Un modèle similaire peut être suivi avec les hubs d’événements, mais vous devez d’abord créer une règle d’autorisation d’écoute dédiée. Si vous souhaitez accorder l’accès à une application qui doit seulement écouter les hubs d’événements liés à l’analyse, procédez comme suit :

1. Créez une stratégie d’accès partagé sur les concentrateurs d’événements qui ont été créés pour la diffusion des données d’analyse avec uniquement les demandes d’écoute. Vous pouvez le faire dans le portail. Par exemple, vous pouvez l’appeler « monitoringReadOnly ». Si possible, donnez cette clé directement au consommateur et ignorez l’étape suivante.
2. Si le consommateur doit être en mesure d’obtenir la clé ad hoc, accordez à l’utilisateur l’action ListKeys pour ce hub d’événements. Cela est également nécessaire pour les utilisateurs qui doivent être en mesure de définir un paramètre de diagnostic ou un profil de journalisation pour diffuser vers les hubs d’événements. Par exemple, vous pouvez créer une règle RBAC :
   
   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur RBAC et les autorisations dans Resource Manager](../active-directory/role-based-access-control-what-is.md)
* [Lire la vue d’ensemble de l’analyse dans Azure](monitoring-overview.md)

