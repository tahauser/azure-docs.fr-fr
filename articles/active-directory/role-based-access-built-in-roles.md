---
title: Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure (RBAC) | Microsoft Docs
description: Cette rubrique décrit les rôles intégrés pour le contrôle d’accès en fonction du rôle (RBAC). Des rôles sont ajoutés en permanence. Veillez donc à consulter la documentation la plus récente.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 0e8f1d97f51fbfedbca1619ef5e7f28a3a0570b9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure
Le contrôle d’accès basé sur un rôle (RBAC) inclut les trois rôles intégrés suivants qui peuvent être affectés à des utilisateurs, des groupes et des services. Vous ne pouvez pas modifier les définitions des rôles intégrés. Toutefois, vous pouvez créer des [rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md) en fonction des besoins spécifiques de votre entreprise.

## <a name="built-in-role-descriptions"></a>Descriptions des rôles intégrés
Le tableau ci-dessous fournit de brèves descriptions des rôles intégrés. Cliquez sur le nom du rôle pour afficher la liste détaillée de ses propriétés **actions** et **notactions**. La propriété **actions** spécifie les actions autorisées sur les ressources Azure. Les chaînes d'action peuvent utiliser des caractères génériques. La propriété **notactions** spécifie les actions qui sont exclues des actions autorisées.

L’action définit le type d’opération que vous pouvez effectuer sur un type de ressource donné. Par exemple : 
- **Write** permet d’effectuer des opérations PUT, POST, PATCH et DELETE.
- **Read** permet d’effectuer des opérations GET.

Cet article traite uniquement des différents rôles qui existent aujourd’hui. Au moment d’attribuer un rôle à un utilisateur, vous pouvez toutefois restreindre un peu plus les actions autorisées en définissant une étendue. Cette possibilité s’avère utile si vous voulez par exemple attribuer le rôle de collaborateur de site web à quelqu’un, mais seulement pour un groupe de ressources.

> [!NOTE]
> Les définitions de rôle Azure sont en constante évolution. Cet article est actualisé aussi régulièrement que possible, mais vous pouvez toujours trouver les dernières définitions de rôles dans Azure PowerShell. Utilisez l’applet de commande [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) pour afficher la liste de tous les rôles actuels. Vous pouvez explorer de manière plus approfondie un rôle déterminé en utilisant `(get-azurermroledefinition "<role name>").actions` ou `(get-azurermroledefinition "<role name>").notactions` selon le cas. [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) permet d’afficher la liste des opérations de fournisseurs de ressources Azure spécifiques.


| Rôle intégré | Description |
| --- | --- |
| [Propriétaire](#owner) | Gérer tout, y compris l’accès |
| [Collaborateur](#contributor) | Gérer tout sauf les accès |
| [Lecteur](#reader) | Afficher tout, mais ne pas apporter de modifications |
| [Collaborateur du service Gestion des API](#api-management-service-contributor) | Gérer les services de gestion des API |
| [Rôle d’opérateur du service Gestion des API](#api-management-service-operator-role) | Gérer les services de gestion des API |
| [Rôle de lecteur du service Gestion des API](#api-management-service-reader-role) | Gérer les services de gestion des API |
| [Collaborateur de composants Application Insights](#application-insights-component-contributor) | Gérer les composants Application Insights |
| [Débogueur de capture instantanée d’Application Insights](#application-insights-snapshot-debugger) | Autorise l’utilisateur à utiliser les fonctionnalités du débogueur de capture instantanée d’Application Insights. |
| [Opérateur de travaux Automation](#automation-job-operator) | Permet de créer et de gérer des travaux avec des runbooks Automation. |
| [Opérateur Automation](#automation-operator) | Démarrer, arrêter, suspendre et reprendre les travaux |
| [Opérateur de runbook Automation](#automation-runbook-operator) | Propriétés de lecture du runbook : pour pouvoir créer des travaux depuis le runbook. |
| [Propriétaire de l’inscription Azure Stack](#azure-stack-registration-owner) | Permet de gérer les inscriptions Azure Stack. |
| [Contributeur de sauvegarde](#backup-contributor) | Gérer toutes les actions de gestion des sauvegardes, à l’exception de la création du coffre Recovery Services et de l’octroi d’accès à d’autres personnes |
| [Opérateur de sauvegarde](#backup-operator) | Gérer toutes les actions de gestion des sauvegardes, à l’exception de la création des coffres, de la suppression des sauvegardes et de l’octroi d’accès à d’autres personnes |
| [Lecteur de sauvegarde](#backup-reader) | Surveiller la gestion des sauvegardes dans le coffre Recovery Services |
| [Lecteur de facturation](#billing-reader) | Afficher toutes les informations de facturation |
| [Collaborateur BizTalk](#biztalk-contributor) | Gérer BizTalk Services |
| [Contributeur de point de terminaison CDN](#cdn-endpoint-contributor) | Peut gérer les points de terminaison CDN, mais ne peut pas accorder l’accès à d’autres utilisateurs. |
| [Lecteur de point de terminaison CDN](#cdn-endpoint-reader) | Peut afficher des points de terminaison CDN, mais ne peut pas effectuer de modifications. |
| [Contributeur de profil CDN](#cdn-profile-contributor) | Peut gérer des profils CDN et leurs points de terminaison, mais ne peut pas accorder l’accès à d’autres utilisateurs. |
| [Lecteur de profil CDN](#cdn-profile-reader) | Peut afficher des profils CDN et leurs points de terminaison, mais ne peut pas y apporter des modifications. |
| [Collaborateur de réseau classique](#classic-network-contributor) | Gérer les réseaux virtuels classiques et les adresses IP réservées |
| [Collaborateur de compte de stockage classique](#classic-storage-account-contributor) | Gérer les comptes de stockage classiques |
| [Rôle de service d’opérateur de clé de compte de stockage classique](#classic-storage-account-key-operator-service-role) | Les opérateurs de clés de comptes de stockage classiques sont autorisés à lister et à régénérer des clés sur des comptes de stockage classiques |
| [Collaborateur de machine virtuelle classique](#classic-virtual-machine-contributor) | Gérer les machines virtuelles classiques, mais pas le réseau virtuel ou le compte de stockage auquel elles sont connectées |
| [Collaborateur de base de données ClearDB MySQL](#cleardb-mysql-db-contributor) | Gérer les bases de données ClearDB MySQL |
| [Rôle de lecteur de compte Cosmos DB](#cosmos-db-account-reader-role) | Lire les données de comptes Azure Cosmos DB. Consultez [Collaborateur de compte DocumentDB](#documentdb-account-contributor) pour en savoir plus sur la gestion des comptes Azure Cosmos DB. |
| [Collaborateurs de fabrique de données](#data-factory-contributor) | Créer et gérer des fabriques de données ainsi que leurs ressources enfants |
| [Développeur Data Lake Analytics](#data-lake-analytics-developer) | Permet d’envoyer, de surveiller et de gérer vos propres travaux, mais pas de créer ni de supprimer des comptes Data Lake Analytics. |
| [Utilisateur de DevTest Labs](#devtest-labs-user) | Afficher tout et connecter, démarrer, redémarrer et arrêter les machines virtuelles |
| [Contributeur de Zone DNS](#dns-zone-contributor) | Peut gérer des enregistrements et zones DNS |
| [Collaborateur de compte DocumentDB](#documentdb-account-contributor) | Gérer des comptes Azure Cosmos DB. Azure Cosmos DB était auparavant appelé DocumentDB. |
| [Collaborateur de compte Intelligent Systems](#intelligent-systems-account-contributor) | Gérer les comptes Intelligent Systems |
| [Contributeur Key Vault](#key-vault-contributor) | Permet de gérer des coffres de clés, mais pas d’y accéder. |
| [Créateur Lab](#lab-creator) | Permet de créer, de gérer et de supprimer des labs gérés dans vos comptes Azure Lab. |
| [Contributeur Log Analytics](#log-analytics-contributor) | Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. La modification des paramètres de surveillance inclut l’ajout de l’extension de machine virtuelle aux machines virtuelles, la lecture des clés de comptes de stockage permettant de configurer la collection de journaux du stockage Azure, la création et la configuration de comptes Automation, l’ajout de solutions et la configuration de diagnostics Azure sur toutes les ressources Azure. |
| [Lecteur Log Analytics](#log-analytics-reader) | Peut afficher et rechercher toutes les données de surveillance, ainsi qu’afficher les paramètres de surveillance, notamment la configuration des diagnostics Azure sur toutes les ressources Azure. |
| [Contributeur d’application logique](#logic-app-contributor) | Permet de gérer l’application logique, mais pas d’y accéder. |
| [Opérateur d’application logique](#logic-app-operator) | Permet de lire, d’activer et de désactiver l’application logique. |
| [Contributeur d’identités gérées](#managed-identity-contributor) | Peut créer, lire, mettre à jour et supprimer une identité attribuée à l’utilisateur. |
| [Opérateur d’identités gérées](#managed-identity-operator) | Peut lire et assigner une identité attribuée à l’utilisateur. |
| [Contributeur de surveillance](#monitoring-contributor) | Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Lecteur de surveillance](#monitoring-reader) | Peut lire toutes les données de surveillance (métriques, journaux, etc.) Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Collaborateur de réseau](#network-contributor) | Gérer toutes les ressources réseau |
| [Collaborateur de compte NewRelic APM](#new-relic-apm-account-contributor) | Vous permet de gérer des comptes et applications New Relic Application Performance Management, mais pas d’y accéder. |
| [Collaborateur Cache Redis](#redis-cache-contributor) | Gérer les caches Redis |
| [Collaborateur des collections de travaux du planificateur](#scheduler-job-collections-contributor) | Gérer des collections de travaux du planificateur |
| [Collaborateur du service de recherche](#search-service-contributor) | Gérer les services de recherche |
| [Administrateur de la sécurité](#security-admin) | Dans Security Center uniquement : peut afficher des stratégies de sécurité, afficher des états de sécurité, modifier des stratégies de sécurité, afficher des alertes et des recommandations, ignorer les alertes et les recommandations |
| [Gestionnaire de sécurité](#security-manager) | Gérer les composants de sécurité, les stratégies de sécurité et les machines virtuelles |
| [Lecteur de sécurité](#security-reader) | Dans Security Center uniquement : peut afficher des recommandations et des alertes, afficher des stratégies de sécurité, afficher des états de la sécurité, mais ne peut pas apporter des modifications |
| [Contributeur Site Recovery](#site-recovery-contributor) | Gérer toutes les actions de gestion Site Recovery, sauf la création de coffre Recovery Services et l’affectation de droits d’accès à d’autres utilisateurs |
| [Opérateur Site Recovery](#site-recovery-operator) | Peut effectuer le basculement et la restauration, mais ne peut pas effectuer d’autres actions de gestion Site Recovery ni affecter l’accès à d’autres utilisateurs |
| [Lecteur Site Recovery](#site-recovery-reader) | Peut surveiller l’état de Site Recovery dans le coffre Recovery Services et déclencher des tickets de support |
| [Collaborateur de base de données SQL](#sql-db-contributor) | Gérer les bases de données SQL, mais pas leurs stratégies de sécurité |
| [Gestionnaire de sécurité SQL](#sql-security-manager) | Gérer les stratégies de sécurité des serveurs et bases de données SQL |
| [Collaborateur SQL Server](#sql-server-contributor) | Gérer les serveurs et bases de données SQL, mais pas leurs stratégies de sécurité |
| [Collaborateur de compte de stockage](#storage-account-contributor) | Peut gérer les comptes de stockage, mais pas y accéder. |
| [Rôle de service d’opérateur de clé de compte de stockage](#storage-account-key-operator-service-role) | Les opérateurs de clés de comptes de stockage sont autorisés à lister et à régénérer des clés sur des comptes de stockage |
| [Contributeur de demande de support](#support-request-contributor) | Créer et gérer des tickets de support dans l’étendue de l’abonnement |
| [Contributeur Traffic Manager](#traffic-manager-contributor) | Permet de gérer des profils Traffic Manager, mais pas de contrôler qui y a accès. |
| [Administrateur de l'accès utilisateur](#user-access-administrator) | Gérer l’accès utilisateur aux ressources Azure |
| [Connexion de l’administrateur aux machines virtuelles](#virtual-machine-administrator-login) | Les utilisateurs disposant de ce rôle peuvent se connecter à une machine virtuelle avec des privilèges d’administrateur Windows ou d’utilisateur racine Linux. |
| [Collaborateur de machine virtuelle](#virtual-machine-contributor) | Gérer les machines virtuelles, mais pas le réseau virtuel ou le compte de stockage auquel elles sont connectées |
| [Connexion de l’utilisateur aux machines virtuelles](#virtual-machine-user-login) | Les utilisateurs disposant de ce rôle ont la possibilité de se connecter à une machine virtuelle en tant qu’utilisateur standard. |
| [Collaborateur de plan web](#web-plan-contributor) | Gérer les plans web |
| [Collaborateur de site web](#website-contributor) | Gérer les sites web, mais pas les plans web auxquels ils sont connectés |

Les tableaux suivants décrivent les autorisations spécifiques à chaque rôle. Cela peut inclure des propriétés **Actions** qui accordent des autorisations et **NotActions** qui restreignent les autorisations.

## <a name="owner"></a>Propriétaire
Gérer tout, y compris l’accès

| **Actions** |  |
| --- | --- |
| * | Créer et gérer les ressources de tous les types |

## <a name="contributor"></a>Contributeur
Gérer tout sauf les accès

| **Actions** |  |
| --- | --- |
| * | Créer et gérer les ressources de tous les types |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete | Ne peut pas supprimer des rôles ni des attributions de rôles. |
| Microsoft.Authorization/*/Write | Ne peut pas créer des rôles ni des attributions de rôles. |
| Microsoft.Authorization/elevateAccess/Action | Accorde à l’appelant un accès Administrateur de l’accès utilisateur au niveau de la portée du client |

## <a name="reader"></a>Lecteur
Afficher tout, mais ne pas apporter de modifications

| **Actions** |  |
| --- | --- |
| */read | Lire les ressources de tous les types, à l’exception des secrets. |

## <a name="api-management-service-contributor"></a>Collaborateur du service de gestion des API
Gérer les services de gestion des API

| **Actions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/* | Créer et gérer le service Gestion des API |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les rôles et les affectations de rôles |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="api-management-service-operator-role"></a>Rôle d’opérateur du service Gestion des API
Gérer les services de gestion des API

| **Actions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Lire les instances du service Gestion des API |
| Microsoft.ApiManagement/service/backup/action | Sauvegarder le service Gestion des API dans le conteneur spécifié dans un compte de stockage fourni par l’utilisateur |
| Microsoft.ApiManagement/service/delete | Supprimer une instance du service Gestion des API |
| Microsoft.ApiManagement/service/managedeployments/action | Modifier une référence SKU/unités ; ajouter ou supprimer des déploiements régionaux du service Gestion des API |
| Microsoft.ApiManagement/service/read | Lire les métadonnées d’une instance du service Gestion des API |
| Microsoft.ApiManagement/service/restore/action | Restaurer le service Gestion des API à partir du conteneur spécifié dans un compte de stockage fourni par l’utilisateur |
| Microsoft.ApiManagement/service/updatecertificate/action | Télécharger le certificat SSL pour un service Gestion des API |
| Microsoft.ApiManagement/service/updatehostname/action | Configurer, mettre à jour ou supprimer des noms de domaine personnalisés pour un service Gestion des API |
| Microsoft.ApiManagement/service/write | Créer une instance du service Gestion des API |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les rôles et les affectations de rôles |
| Microsoft.Support/* | Créer et gérer les tickets de support |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Obtenir la liste des clés utilisateur |

## <a name="api-management-service-reader-role"></a>Rôle de lecteur du service Gestion des API
Gérer les services de gestion des API

| **Actions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Lire les instances du service Gestion des API |
| Microsoft.ApiManagement/service/read | Lire les métadonnées d’une instance du service Gestion des API |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les rôles et les affectations de rôles |
| Microsoft.Support/* | Créer et gérer les tickets de support |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Obtenir la liste des clés utilisateur |

## <a name="application-insights-component-contributor"></a>Collaborateur de composants Application Insights
Gérer les composants Application Insights

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Insights/components/* | Créer et gérer les composants Insights |
| Microsoft.Insights/webtests/* | Créer et gérer les tests web |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="application-insights-snapshot-debugger"></a>Débogueur de capture instantanée d’Application Insights
Autorise l’utilisateur à utiliser les fonctionnalités du débogueur de capture instantanée d’Application Insights.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Insights/components/*/read |  |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="automation-job-operator"></a>Opérateur de travaux Automation
Permet de créer et de gérer des travaux avec des runbooks Automation.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Automation/automationAccounts/jobs/read | Obtient un travail Azure Automation |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Reprend un travail Azure Automation |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Arrête un travail Azure Automation |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lit des ressources Runbook Worker hybrides |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Obtient un flux de travail Azure Automation |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspend un travail Azure Automation |
| Microsoft.Automation/automationAccounts/jobs/write | Crée un travail Azure Automation |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="automation-operator"></a>Opérateur Automation
Démarrer, arrêter, suspendre et reprendre les travaux

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Automation/automationAccounts/jobs/read | Lire les travaux des comptes Automation |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Reprendre un travail de compte Automation |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Arrêter un travail de compte Automation |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Lire les flux de travail des comptes Automation |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspendre un travail de compte Automation |
| Microsoft.Automation/automationAccounts/jobs/write | Écrire les travaux des comptes Automation |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Lire une planification de travail de compte Automation |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Lire une planification de travail de compte Automation |
| Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtient l’espace de travail lié au compte Automation. |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lit des ressources Runbook Worker hybrides |
| Microsoft.Automation/automationAccounts/read | Lire les comptes Automation |
| Microsoft.Automation/automationAccounts/runbooks/read | Lire les Runbooks Automation |
| Microsoft.Automation/automationAccounts/schedules/read | Lire les planifications de compte Automation |
| Microsoft.Automation/automationAccounts/schedules/write | Écrire les planifications de compte Automation |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="automation-runbook-operator"></a>Opérateur de runbook Automation
Propriétés de lecture du runbook : pour pouvoir créer des travaux depuis le runbook.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Automation/automationAccounts/runbooks/read | Obtient un runbook Azure Automation |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="azure-stack-registration-owner"></a>Propriétaire de l’inscription Azure Stack
Permet de gérer les inscriptions Azure Stack.

| **Actions** |  |
| --- | --- |
| Microsoft.AzureStack/registrations/products/listDetails/action | Récupère les détails étendus d’un produit de la place de marché Azure Stack. |
| Microsoft.AzureStack/registrations/products/read | Obtient les propriétés d’un produit de la place de marché Azure Stack. |
| Microsoft.AzureStack/registrations/read | Obtient les propriétés d’une inscription Azure Stack. |

## <a name="backup-contributor"></a>Contributeur de sauvegarde
Gérer toutes les actions de gestion des sauvegardes, à l’exception de la création du coffre Recovery Services et de l’octroi d’accès à d’autres personnes

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Network/virtualNetworks/read | Lire les réseaux virtuels |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Gérer les résultats des opérations de gestion des sauvegardes |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Créer et gérer des conteneurs de sauvegarde dans les structures de sauvegarde du coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Créer et gérer des travaux de sauvegarde |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exporter des travaux de sauvegarde dans un fichier Excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Créer et gérer des métadonnées associées à la gestion des sauvegardes |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Créer et gérer les résultats des opérations de gestion des sauvegardes |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | Créer et gérer des stratégies de sauvegarde |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Créer et gérer les éléments qui peuvent être sauvegardés |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Créer et gérer les éléments sauvegardés |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Créer et gérer les conteneurs contenant les éléments de sauvegarde |
| Microsoft.RecoveryServices/Vaults/certificates/* | Créer et gérer des certificats associés à la sauvegarde dans le coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Créer et gérer des informations étendues associées au coffre |
| Microsoft.RecoveryServices/Vaults/read | Lire les coffres Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Gérer les opérations de découverte pour récupérer les conteneurs récemment créés |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Créer et gérer les identités inscrites |
| Microsoft.RecoveryServices/Vaults/usages/* | Créer et gérer l’utilisation du coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services. |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Storage/storageAccounts/read | Lire les comptes de stockage |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Renvoie le résultat de l’opération de travail d’exportation. |
| Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="backup-operator"></a>Opérateur de sauvegarde
Gérer toutes les actions de gestion des sauvegardes, à l’exception de la création des coffres, de la suppression des sauvegardes et de l’octroi d’accès à d’autres personnes

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Network/virtualNetworks/read | Lire les réseaux virtuels |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Lire les résultats des opérations de gestion des sauvegardes |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | Lire les résultats des opérations sur les conteneurs de protection |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/backup/action | Effectuer une opération de sauvegarde à la demande sur un élément sauvegardé |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | Lire le résultat d’une opération effectuée sur un élément sauvegardé |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | Renvoie l’état de l’opération effectuée sur les éléments protégés. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Lire les éléments sauvegardés |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | Lire le point de récupération d’un élément sauvegardé |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/restore/action | Effectuer une opération de restauration en utilisant le point de récupération d’un élément sauvegardé |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | Créer un élément de sauvegarde |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Lire les conteneurs contenant l’élément de sauvegarde |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Créer et gérer des travaux de sauvegarde |
| Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Annuler le travail. |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Renvoie le résultat de l’opération de travail. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Renvoie tous les objets de travail. |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exporter des travaux de sauvegarde dans un fichier Excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Lire les métadonnées associées à la gestion des sauvegardes |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Créer et gérer les résultats des opérations de gestion des sauvegardes |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Lire les résultats des opérations effectuées sur les stratégies de sauvegarde |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Lire les stratégies de sauvegarde |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Créer et gérer les éléments qui peuvent être sauvegardés |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Renvoie la liste de tous les éléments pouvant être protégés. |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Lire les éléments sauvegardés |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Lire les conteneurs sauvegardés contenant les éléments de sauvegarde |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Lire les informations étendues associées au coffre |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | Écrire les informations étendues associées au coffre |
| Microsoft.RecoveryServices/Vaults/read | Lire les coffres Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Gérer les opérations de découverte pour récupérer les conteneurs récemment créés |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Lire les résultats d’une opération effectuée sur les éléments enregistrés du coffre |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Lire les éléments enregistrés du coffre |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Écrire des éléments enregistrés dans le coffre |
| Microsoft.RecoveryServices/Vaults/usages/read | Lire l’utilisation du coffre Recovery Services |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Storage/storageAccounts/read | Lire les comptes de stockage |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Approvisionner la récupération d’éléments instantanée pour l’élément protégé. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Révoquer la récupération d’éléments instantanée pour l’élément protégé. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Renvoie le résultat de l’opération de travail d’exportation. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
| Microsoft.RecoveryServices/Vaults/certificates/write | L’opération de mise à jour de certificat de ressource met à jour le certificat d’informations d’identification du coffre/de la ressource. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="backup-reader"></a>Lecteur de sauvegarde
Surveiller la gestion des sauvegardes dans le coffre Recovery Services

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Lire les résultats des opérations de gestion des sauvegardes |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | Lire les résultats des opérations sur les conteneurs de protection |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | Lire le résultat d’une opération effectuée sur un élément sauvegardé |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | Renvoie l’état de l’opération effectuée sur les éléments protégés. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Lire les éléments sauvegardés |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Lire les conteneurs contenant l’élément de sauvegarde |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Lire les résultats des travaux de sauvegarde |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Lire les travaux de sauvegarde |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exporter des travaux de sauvegarde dans un fichier Excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Lire les métadonnées associées à la gestion des sauvegardes |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Lire les résultats des opérations de gestion des sauvegardes |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Lire les résultats des opérations effectuées sur les stratégies de sauvegarde |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Lire les stratégies de sauvegarde |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Lire les éléments sauvegardés |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Lire les conteneurs sauvegardés contenant les éléments de sauvegarde |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Lire les informations étendues associées au coffre |
| Microsoft.RecoveryServices/Vaults/read | Lire les coffres Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Lire le résultat de l’opération de découverte visant à récupérer les conteneurs récemment créés |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Lire les résultats d’une opération effectuée sur les éléments enregistrés du coffre |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Lire les éléments enregistrés du coffre |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | Obtenir les points de récupération des éléments protégés. |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Renvoie le résultat de l’opération de travail d’exportation. |
| Microsoft.RecoveryServices/Vaults/usages/read | Lire l’utilisation du coffre Recovery Services |

## <a name="billing-reader"></a>Lecteur de facturation
Afficher toutes les informations de facturation

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Billing/*/read | Lire les informations de facturation |
| Microsoft.Consumption/*/read |  |
| Microsoft.Commerce/*/read |  |
| Microsoft.Management/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="biztalk-contributor"></a>Collaborateur BizTalk
Gérer BizTalk Services

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.BizTalkServices/BizTalk/* | Créer et gérer BizTalk Services |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="cdn-endpoint-contributor"></a>Contributeur de point de terminaison CDN
Peut gérer les points de terminaison CDN, mais ne peut pas accorder l’accès à d’autres utilisateurs.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/* |  |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="cdn-endpoint-reader"></a>Lecteur de point de terminaison CDN
Peut afficher des points de terminaison CDN, mais ne peut pas effectuer de modifications.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/*/read |  |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="cdn-profile-contributor"></a>Contributeur de profil CDN
Peut gérer des profils CDN et leurs points de terminaison, mais ne peut pas accorder l’accès à d’autres utilisateurs.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/* |  |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="cdn-profile-reader"></a>Lecteur de profil CDN
Peut afficher des profils CDN et leurs points de terminaison, mais ne peut pas y apporter des modifications.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/*/read |  |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="classic-network-contributor"></a>Collaborateur de réseau classique
Gérer les réseaux virtuels classiques et les adresses IP réservées

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.ClassicNetwork/* | Créer et gérer des réseaux classiques |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="classic-storage-account-contributor"></a>Collaborateur de compte de stockage classique
Gérer les comptes de stockage classiques

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.ClassicStorage/storageAccounts/* | Créer et gérer les comptes de stockage |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="classic-storage-account-key-operator-service-role"></a>Rôle de service d’opérateur de clé de compte de stockage classique
Les opérateurs de clés de comptes de stockage classiques sont autorisés à lister et à régénérer des clés sur des comptes de stockage classiques

| **Actions** |  |
| --- | --- |
| Microsoft.ClassicStorage/storageAccounts/listkeys/action | Répertorie les clés d’accès des comptes de stockage. |
| Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Régénère les clés d’accès existantes du compte de stockage. |

## <a name="classic-virtual-machine-contributor"></a>Collaborateur de machine virtuelle classique
Gérer les machines virtuelles classiques, mais pas le réseau virtuel ou le compte de stockage auquel elles sont connectées

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.ClassicCompute/domainNames/* | Créer et gérer des noms de domaine de calcul classique |
| Microsoft.ClassicCompute/virtualMachines/* | Créer et gérer les machines virtuelles |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | Joindre des groupes de sécurité réseau |
| Microsoft.ClassicNetwork/reservedIps/link/action | Lier les adresses IP réservées |
| Microsoft.ClassicNetwork/reservedIps/read | Lire les adresses IP réservées |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Rejoindre des réseaux virtuels |
| Microsoft.ClassicNetwork/virtualNetworks/read | Lire les réseaux virtuels |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Lire les disques de compte de stockage |
| Microsoft.ClassicStorage/storageAccounts/images/read | Lire les images de compte de stockage |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Répertorier les clés de compte de stockage |
| Microsoft.ClassicStorage/storageAccounts/read | Lire les comptes de stockage classiques |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="cleardb-mysql-db-contributor"></a>Collaborateur de base de données ClearDB MySQL
Gérer les bases de données ClearDB MySQL

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |
| successbricks.cleardb/databases/* | Créer et gérer les bases de données ClearDB MySQL |

## <a name="cosmos-db-account-reader-role"></a>Rôle de lecteur de compte Cosmos DB
Lire les données de comptes Azure Cosmos DB. Consultez [Collaborateur de compte DocumentDB](#documentdb-account-contributor) pour en savoir plus sur la gestion des comptes Azure Cosmos DB.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les attributions de rôle, lire les autorisations accordées à chaque utilisateur |
| Microsoft.DocumentDB/*/read | Lire n’importe quelle collection |
| Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Lire le volet de clés en lecture seule |
| Microsoft.Insights/MetricDefinitions/read | Lire les définitions de métriques |
| Microsoft.Insights/Metrics/read | Lire les métriques de compte |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="data-factory-contributor"></a>Collaborateurs de fabrique de données
Créer et gérer des fabriques de données ainsi que leurs ressources enfants

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.DataFactory/dataFactories/* | Créer et gérer des fabriques de données ainsi que leurs ressources enfants |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="data-lake-analytics-developer"></a>Développeur Data Lake Analytics
Permet d’envoyer, de surveiller et de gérer vos propres travaux, mais pas de créer ni de supprimer des comptes Data Lake Analytics.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.BigAnalytics/accounts/* |  |
| Microsoft.DataLakeAnalytics/accounts/* |  |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

| **NotActions** |  |
| --- | --- |
| Microsoft.BigAnalytics/accounts/Delete |  |
| Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
| Microsoft.BigAnalytics/accounts/Write |  |
| Microsoft.DataLakeAnalytics/accounts/Delete | Supprime un compte Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Accorde des autorisations pour annuler des travaux soumis par d’autres utilisateurs. |
| Microsoft.DataLakeAnalytics/accounts/Write | Crée ou met à jour un compte Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Crée ou met à jour un compte Data Lake Store lié d’un compte Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Dissocie un compte Data Lake Store d’un compte Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Crée ou met à jour un compte de stockage lié d’un compte Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Dissocie un compte de stockage d’un compte Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Créer ou mettre à jour une règle de pare-feu. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Supprimer une règle de pare-feu. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Crée ou met à jour une stratégie de calcul. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Supprime une stratégie de calcul. |

## <a name="devtest-labs-user"></a>Utilisateur de DevTest Labs
Afficher tout et connecter, démarrer, redémarrer et arrêter les machines virtuelles

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Compute/availabilitySets/read | Lire les propriétés des groupes à haute disponibilité |
| Microsoft.Compute/virtualMachines/*/read | Lire les propriétés d’une machine virtuelle (tailles de machine virtuelle, état de l’exécution, extensions de machine virtuelle, etc.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Désallouer les machines virtuelles |
| Microsoft.Compute/virtualMachines/read | Lire les propriétés d’une machine virtuelle |
| Microsoft.Compute/virtualMachines/restart/action | Redémarrer les machines virtuelles |
| Microsoft.Compute/virtualMachines/start/action | Démarrer les machines virtuelles |
| Microsoft.DevTestLab/*/read | Lire les propriétés d’un laboratoire |
| Microsoft.DevTestLab/labs/createEnvironment/action | Créer un environnement de laboratoire |
| Microsoft.DevTestLab/labs/claimAnyVm/action | Revendiquer une machine virtuelle exigible aléatoire dans le laboratoire. |
| Microsoft.DevTestLab/labs/formulas/delete | Supprimer des formules |
| Microsoft.DevTestLab/labs/formulas/read | Lire des formules |
| Microsoft.DevTestLab/labs/formulas/write | Ajouter ou modifier des formules |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Évaluer des stratégies de laboratoire |
| Microsoft.DevTestLab/labs/virtualMachines/claim/action | Prendre possession d’une machine virtuelle existante. |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joindre un pool d’adresses principales d’équilibreur de charge |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joindre une règle NAT entrante d’équilibreur de charge |
| Microsoft.Network/networkInterfaces/*/read | Lire les propriétés d’une interface réseau (par exemple, tous les équilibreurs de charge dont l’interface réseau fait partie) |
| Microsoft.Network/networkInterfaces/join/action | Joindre une machine virtuelle à une interface réseau |
| Microsoft.Network/networkInterfaces/read | Lire des interfaces réseau |
| Microsoft.Network/networkInterfaces/write | Écrire des interfaces réseau |
| Microsoft.Network/publicIPAddresses/*/read | Lire les propriétés d’une adresse IP publique |
| Microsoft.Network/publicIPAddresses/join/action | Joindre une adresse IP publique |
| Microsoft.Network/publicIPAddresses/read | Lire des adresses IP publiques réseau |
| Microsoft.Network/virtualNetworks/subnets/join/action | Joindre un réseau virtuel |
| Microsoft.Resources/deployments/operations/read | Lire des opérations de déploiement |
| Microsoft.Resources/deployments/read | Lire des déploiements |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Storage/storageAccounts/listKeys/action | Répertorier les clés de compte de stockage |

| **NotActions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/vmSizes/read | Répertorier les tailles disponibles pour la mise à jour de la machine virtuelle |

## <a name="dns-zone-contributor"></a>Contributeur de Zone DNS
Peut gérer des enregistrements et zones DNS

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Network/dnsZones/* | Créer et gérer des enregistrements et zones DNS |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="documentdb-account-contributor"></a>Collaborateur de compte DocumentDB
Gérer des comptes Azure Cosmos DB. Azure Cosmos DB était auparavant appelé DocumentDB.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.DocumentDb/databaseAccounts/* | Créer et gérer des comptes Azure Cosmos DB |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="intelligent-systems-account-contributor"></a>Collaborateur de compte Intelligent Systems
Gérer les comptes Intelligent Systems

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.IntelligentSystems/accounts/* | Créer et gérer les comptes Intelligent Systems |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="key-vault-contributor"></a>Contributeur Key Vault
Permet de gérer des coffres de clés, mais pas d’y accéder.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.KeyVault/* |  |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

| **NotActions** |  |
| --- | --- |
| Microsoft.KeyVault/locations/deletedVaults/purge/action | Vider un coffre Key Vault supprimé de manière réversible |
| Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Créateur Lab
Permet de créer, de gérer et de supprimer des labs gérés dans vos comptes Azure Lab.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.ManagedLab/labAccounts/createLab/action | Crée un lab dans un compte lab. |
| Microsoft.ManagedLab/labAccounts/*/read |  |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="log-analytics-contributor"></a>Contributeur Log Analytics
Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. La modification des paramètres de surveillance inclut l’ajout de l’extension de machine virtuelle aux machines virtuelles, la lecture des clés de comptes de stockage permettant de configurer la collection de journaux du stockage Azure, la création et la configuration de comptes Automation, l’ajout de solutions et la configuration de diagnostics Azure sur toutes les ressources Azure.

| **Actions** |  |
| --- | --- |
| */read | Lire les ressources de tous les types, à l’exception des secrets. |
| Microsoft.Automation/automationAccounts/* |  |
| Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Répertorie les clés d’accès des comptes de stockage. |
| Microsoft.Compute/virtualMachines/extensions/* |  |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Insights/diagnosticSettings/* | Crée, met à jour ou lit le paramètre de diagnostic pour Analysis Server |
| Microsoft.OperationalInsights/* |  |
| Microsoft.OperationsManagement/* |  |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
| Microsoft.Storage/storageAccounts/listKeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="log-analytics-reader"></a>Lecteur Log Analytics
Peut afficher et rechercher toutes les données de surveillance, ainsi qu’afficher les paramètres de surveillance, notamment la configuration des diagnostics Azure sur toutes les ressources Azure.

| **Actions** |  |
| --- | --- |
| */read | Lire les ressources de tous les types, à l’exception des secrets. |
| Microsoft.OperationalInsights/workspaces/analytics/query/action | Effectue les recherches à l’aide d’un nouveau moteur. |
| Microsoft.OperationalInsights/workspaces/search/action | Exécute une requête de recherche. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

| **NotActions** |  |
| --- | --- |
| Microsoft.OperationalInsights/workspaces/sharedKeys/read | Récupère les clés partagées de l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail. |

## <a name="logic-app-contributor"></a>Contributeur d’application logique
Permet de gérer l’application logique, mais pas d’y accéder.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Répertorie les clés d’accès des comptes de stockage. |
| Microsoft.ClassicStorage/storageAccounts/read | Retourne le compte de stockage avec le compte spécifique. |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Insights/diagnosticSettings/* | Crée, met à jour ou lit le paramètre de diagnostic pour Analysis Server. |
| Microsoft.Insights/logdefinitions/* | Cette autorisation est nécessaire pour les utilisateurs qui doivent accéder aux journaux d’activité via le portail. Répertorier les catégories de journaux dans le journal d’activité. |
| Microsoft.Insights/metricDefinitions/* | Lire des définitions de mesure (liste de types de mesure disponibles pour une ressource). |
| Microsoft.Logic/* | Gère les ressources Logic Apps. |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Storage/storageAccounts/listkeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
| Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
| Microsoft.Support/* | Créer et gérer les tickets de support |
| Microsoft.Web/connectionGateways/* | Crée et gère une passerelle de connexion. |
| Microsoft.Web/connections/* | Crée et gère une connexion. |
| Microsoft.Web/customApis/* | Crée et gère une API personnalisée. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | Récupère les propriétés d’un plan App Service. |
| Microsoft.Web/sites/functions/listSecrets/action | Répertorie les fonctions Web Apps des clés secrètes. |

## <a name="logic-app-operator"></a>Opérateur d’application logique
Permet de lire, d’activer et de désactiver l’application logique.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/*/read | Lit les règles d’alerte d’Insights. |
| Microsoft.Insights/diagnosticSettings/*/read | Obtient les paramètres de diagnostic de Logic Apps. |
| Microsoft.Insights/metricDefinitions/*/read | Obtient les mesures disponibles pour Logic Apps. |
| Microsoft.Logic/*/read | Lit les ressources Logic Apps. |
| Microsoft.Logic/workflows/disable/action | Désactive le workflow. |
| Microsoft.Logic/workflows/enable/action | Active le workflow. |
| Microsoft.Logic/workflows/validate/action | Valide le workflow. |
| Microsoft.Resources/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
| Microsoft.Resources/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Support/* | Créer et gérer les tickets de support |
| Microsoft.Web/connectionGateways/*/read | Lit les passerelles de connexion. |
| Microsoft.Web/connections/*/read | Lit les connexions. |
| Microsoft.Web/customApis/*/read | Lit l’API personnalisée. |
| Microsoft.Web/serverFarms/read | Récupère les propriétés d’un plan App Service. |

## <a name="managed-identity-contributor"></a>Contributeur d’identités gérées
Peut créer, lire, mettre à jour et supprimer une identité attribuée à l’utilisateur.

| **Actions** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="managed-identity-operator"></a>Opérateur d’identités gérées
Peut lire et assigner une identité attribuée à l’utilisateur.

| **Actions** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="monitoring-contributor"></a>Contributeur d’analyse
Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Actions** |  |
| --- | --- |
| */read | Lire les ressources de tous les types, à l’exception des secrets. |
| Microsoft.AlertsManagement/alerts/* |  |
| Microsoft.AlertsManagement/alertsSummary/* |  |
| Microsoft.Insights/AlertRules/* | Règles d’alerte en lecture/écriture/suppression. |
| Microsoft.Insights/components/* | Lire/écrire/supprimer des composants Application Insights. |
| Microsoft.Insights/DiagnosticSettings/* | Paramètres de diagnostic en lecture/écriture/suppression. |
| Microsoft.Insights/eventtypes/* | Événements du journal d’activité, (événements de gestion) dans un abonnement. Cette autorisation est applicable pour l’accès par programme et portail dans le journal d’activité. |
| Microsoft.Insights/LogDefinitions/* | Cette autorisation est nécessaire pour les utilisateurs qui doivent accéder aux journaux d’activité via le portail. Répertorier les catégories de journaux dans le journal d’activité. |
| Microsoft.Insights/MetricDefinitions/* | Lire des définitions de mesure (liste de types de mesure disponibles pour une ressource). |
| Microsoft.Insights/Metrics/* | Lire des mesures pour une ressource. |
| Microsoft.Insights/Register/Action | Inscrire le fournisseur Microsoft.Insights |
| Microsoft.Insights/webtests/* | Lire/écrire/supprimer des tests web Application Insights. |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Lire/écrire/supprimer des packs de solution Log Analytics. |
| Microsoft.OperationalInsights/workspaces/savedSearches/* | Lire/écrire/supprimer des recherches enregistrées Log Analytics. |
| Microsoft.OperationalInsights/workspaces/search/action | Rechercher dans les espaces de travail Log Analytics. |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action | Répertorier les clés pour un espace de travail Log Analytics. |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Lire/écrire/supprimer les configurations des insights de stockage Log Analytics. |
| Microsoft.Support/* | Créer et gérer les tickets de support |
| Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Lecteur d’analyse
Peut lire toutes les données de surveillance (métriques, journaux, etc.) Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Actions** |  |
| --- | --- |
| */read | Lire les ressources de tous les types, à l’exception des secrets. |
| Microsoft.OperationalInsights/workspaces/search/action | Recherche dans les données Log Analytics |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="network-contributor"></a>Collaborateur de réseau
Gérer toutes les ressources réseau

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Network/* | Créer et gérer des réseaux |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="new-relic-apm-account-contributor"></a>Collaborateur de compte NewRelic APM
Vous permet de gérer des comptes et applications New Relic Application Performance Management, mais pas d’y accéder.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Support/* | Créer et gérer les tickets de support |
| NewRelic.APM/accounts/* |  |

## <a name="redis-cache-contributor"></a>Collaborateur Cache Redis
Gérer les caches Redis

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Cache/redis/* | Créer et gérer les caches Redis |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="scheduler-job-collections-contributor"></a>Collaborateur des collections de travaux du planificateur
Gérer des collections de travaux du planificateur

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Scheduler/jobcollections/* | Créer et gérer des collections de travaux |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="search-service-contributor"></a>Collaborateur du service de recherche
Gérer les services de recherche

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Search/searchServices/* | Créer et gérer les services de recherche |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="security-admin"></a>Administrateur de la sécurité
Dans Security Center uniquement : peut afficher des stratégies de sécurité, afficher des états de sécurité, modifier des stratégies de sécurité, afficher des alertes et des recommandations, ignorer les alertes et les recommandations

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Authorization/policyAssignments/* | Créer et gérer des attributions de stratégies |
| Microsoft.Authorization/policyDefinitions/* | Créer et gérer des définitions de stratégies |
| Microsoft.Authorization/policySetDefinitions/* | Créer et gérer des ensembles de stratégies |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.operationalInsights/workspaces/*/read | Afficher les données Log Analytics |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Security/*/read | Lire des stratégies et des composants de sécurité |
| Microsoft.Security/locations/alerts/dismiss/action | Ignore une alerte de sécurité. |
| Microsoft.Security/locations/tasks/dismiss/action | Ignorer une recommandation de sécurité. |
| Microsoft.Security/policies/write | Met à jour la stratégie de sécurité. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="security-manager"></a>Gestionnaire de sécurité
Gérer les composants de sécurité, les stratégies de sécurité et les machines virtuelles

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.ClassicCompute/*/read | Lire les informations de configuration relatives aux machines virtuelles classiques |
| Microsoft.ClassicCompute/virtualMachines/*/write | Écrire la configuration des machines virtuelles classiques |
| Microsoft.ClassicNetwork/*/read | Lire les informations de configuration relatives au réseau classique |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Security/* | Créer et gérer des stratégies et des composants de sécurité |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="security-reader"></a>Lecteur de sécurité
Dans Security Center uniquement : peut afficher des recommandations et des alertes, afficher des stratégies de sécurité, afficher des états de la sécurité, mais ne peut pas apporter des modifications

| **Actions** |  |
| --- | --- |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.operationalInsights/workspaces/*/read | Afficher les données Log Analytics |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Support/* | Créer et gérer les tickets de support |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Security/*/read | Lire des stratégies et des composants de sécurité |

## <a name="site-recovery-contributor"></a>Contributeur Site Recovery
Gérer toutes les actions de gestion Site Recovery, sauf la création de coffre Recovery Services et l’affectation de droits d’accès à d’autres utilisateurs

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Network/virtualNetworks/read | Lire les réseaux virtuels |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp est une opération interne utilisée par le service. |
| Microsoft.RecoveryServices/Vaults/certificates/write | Met à jour le certificat d’informations d’identification de coffre |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Créer et gérer des informations étendues associées au coffre |
| Microsoft.RecoveryServices/Vaults/read | Lire les coffres Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Gérer les opérations de découverte pour récupérer les conteneurs récemment créés |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Créer et gérer les identités inscrites |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Créer ou mettre à jour les paramètres d’alerte de réplication |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Lire les événements de réplication |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | Créer et gérer des structures de réplication |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Créer et gérer des travaux de réplication |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | Créer et gérer des stratégies de réplication |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Créer et gérer des plans de récupération |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Créer et gérer la configuration de stockage du coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Lire les informations de jeton de coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/usages/read | Lire les détails d’utilisation d’un coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre. |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Lire les alertes pour le coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Lire la configuration de notification du coffre Recovery Services |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Storage/storageAccounts/read | Lire les comptes de stockage |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="site-recovery-operator"></a>Opérateur Site Recovery
Peut effectuer le basculement et la restauration, mais ne peut pas effectuer d’autres actions de gestion Site Recovery ni affecter l’accès à d’autres utilisateurs

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Network/virtualNetworks/read | Lire les réseaux virtuels |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp est une opération interne utilisée par le service. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Lire les informations étendues associées au coffre |
| Microsoft.RecoveryServices/Vaults/read | Lire les coffres Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Gérer les opérations de découverte pour récupérer les conteneurs récemment créés |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Lire l’état et le résultat d’une opération envoyée |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Lire les conteneurs inscrits pour une ressource |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lire les paramètres d’alerte de réplication |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Lire les événements de réplication |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Vérifier la cohérence des structures |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Lire les structures de réplication |
| Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Réassocier la passerelle de réplication |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renouveler le certificat de l’infrastructure de réplication |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Lire les réseaux de structure de réplication |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | Lire les mappages réseau de structures de réplication |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Lire les conteneurs de protection |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Obtenir la liste de tous les éléments pouvant être protégés |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Appliquer un point de récupération. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Validation du basculement. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Basculement planifié. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Obtenir la liste de tous les éléments protégés |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Obtenir la liste des points de récupération disponibles |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Réparer la réplication. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/reProtect/action | Commencer à reprotéger un élément protégé |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/action | Démarrer le test de basculement d’un élément protégé |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Nettoyage de basculement test. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Basculement |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mettre à jour le service Mobilité. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Lire les mappages de conteneurs de protection |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Lire les fournisseurs Recovery Services |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/action | Actualiser un fournisseur Recovery Services |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Lire les classifications de stockage pour les structures de réplication |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | Lire les mappages de classifications de stockage |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Lire les informations vCenter inscrites |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Créer et gérer des travaux de réplication |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Lire les stratégies de réplication |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Valider le basculement d’un plan de récupération |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Démarrer le basculement d’un plan de récupération |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Lire les plans de récupération |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Commencer à reprotéger un plan de récupération |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Démarrer le test de basculement d’un plan de récupération |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Démarrer le nettoyage d’un test de basculement de plan de récupération |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Démarrer le basculement non planifié d’un plan de récupération |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Lire les alertes pour le coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Lire la configuration de notification du coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Lire la configuration de stockage d’un coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Lire les informations de jeton de coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/usages/read | Lire les détails d’utilisation d’un coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Storage/storageAccounts/read | Lire les comptes de stockage |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="site-recovery-reader"></a>Lecteur Site Recovery
Peut surveiller l’état de Site Recovery dans le coffre Recovery Services et déclencher des tickets de support

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Lire les informations étendues associées au coffre |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lire les alertes pour le coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Lire la configuration de notification du coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/read | Lire les coffres Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Gérer les opérations de découverte pour récupérer les conteneurs récemment créés |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Lire l’état et le résultat d’une opération envoyée |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Lire les conteneurs inscrits pour une ressource |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lire les paramètres d’alerte de réplication |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Lire les événements de réplication |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Lire les structures de réplication |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Lire les réseaux de structure de réplication |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | Lire les mappages réseau de structures de réplication |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Lire les conteneurs de protection |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Obtenir la liste de tous les éléments pouvant être protégés |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Obtenir la liste de tous les éléments protégés |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Obtenir la liste des points de récupération disponibles |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Lire les mappages de conteneurs de protection |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Lire les fournisseurs Recovery Services |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Lire les classifications de stockage pour les structures de réplication |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | Lire les mappages de classifications de stockage |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Lire les informations vCenter inscrites |
| Microsoft.RecoveryServices/vaults/replicationJobs/read | Lire l’état des travaux de réplication |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Lire les stratégies de réplication |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Lire les plans de récupération |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Lire la configuration de stockage d’un coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Lire les informations de jeton de coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/usages/read | Lire les détails d’utilisation d’un coffre Recovery Services |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="sql-db-contributor"></a>Collaborateur de base de données SQL
Gérer les bases de données SQL, mais pas leurs stratégies de sécurité

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/databases/* | Créer et gérer les bases de données SQL |
| Microsoft.Sql/servers/read | Lire les serveurs SQL |
| Microsoft.Support/* | Créer et gérer les tickets de support |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Impossible de modifier les stratégies d’audit |
| Microsoft.Sql/servers/databases/auditingSettings/* | Impossible de modifier les paramètres d’audit |
| Microsoft.Sql/servers/databases/auditRecords/read | Impossible de lire les enregistrements d’audit |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Impossible de modifier les stratégies de connexion |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Impossible de modifier les stratégies de masquage des données |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Impossible de modifier les stratégies d’alerte de sécurité |
| Microsoft.Sql/servers/databases/securityMetrics/* | Impossible de modifier les mesures de sécurité |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>Gestionnaire de sécurité SQL
Gérer les stratégies de sécurité des serveurs et bases de données SQL

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorisation de lecture Microsoft |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Sql/servers/auditingPolicies/* | Créer et gérer les stratégies d’audit de serveur SQL |
| Microsoft.Sql/servers/auditingSettings/* | Créer et gérer les paramètres d’audit de serveur SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Créer et gérer les stratégies d’audit de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* | Créer et gérer les paramètres d’audit de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/auditRecords/read | Lire les enregistrements d’audit |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Créer et gérer les stratégies de connexion de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Créer et gérer les stratégies de masquage de données de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/read | Lire les bases de données SQL |
| Microsoft.Sql/servers/databases/schemas/read | Lire les schémas de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Lire les colonnes de table de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/read | Lire les tables de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Créer et gérer les stratégies d’alerte de sécurité de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Créer et gérer les mesures de sécurité de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/firewallRules/* |  |
| Microsoft.Sql/servers/read | Lire les serveurs SQL |
| Microsoft.Sql/servers/securityAlertPolicies/* | Créer et gérer les stratégies d’alerte de sécurité de serveur SQL |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="sql-server-contributor"></a>Collaborateur SQL Server
Gérer les serveurs et bases de données SQL, mais pas leurs stratégies de sécurité

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/* | Créer et gérer les serveurs SQL |
| Microsoft.Support/* | Créer et gérer les tickets de support |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* | Impossible de modifier les stratégies d’audit de serveur SQL |
| Microsoft.Sql/servers/auditingSettings/* | Impossible de modifier les paramètres d’audit de serveur SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Impossible de modifier les stratégies d’audit de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* | Impossible de modifier les paramètres d’audit de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/auditRecords/read | Impossible de lire les enregistrements d’audit |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Impossible de modifier les stratégies de connexion de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Impossible de modifier les stratégies de masquage de données de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Impossible de modifier les stratégies d’alerte de sécurité de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Impossible de modifier les mesures de sécurité de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/securityAlertPolicies/* | Impossible de modifier les stratégies d’alerte de sécurité de serveur SQL |

## <a name="storage-account-contributor"></a>Collaborateur de compte de stockage
Peut gérer les comptes de stockage, mais pas y accéder.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorisation de lecture totale |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Insights/diagnosticSettings/* | Gérer les paramètres de diagnostic |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Storage/storageAccounts/* | Créer et gérer les comptes de stockage |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="storage-account-key-operator-service-role"></a>Rôle de service d’opérateur de clé de compte de stockage
Les opérateurs de clés de comptes de stockage sont autorisés à lister et à régénérer des clés sur des comptes de stockage

| **Actions** |  |
| --- | --- |
| Microsoft.Storage/storageAccounts/listkeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
| Microsoft.Storage/storageAccounts/regeneratekey/action | Régénère les clés d’accès au compte de stockage spécifié. |

## <a name="support-request-contributor"></a>Contributeur de demande de support
Créer et gérer des tickets de support dans l’étendue de l’abonnement

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les rôles et les affectations de rôles |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="traffic-manager-contributor"></a>Contributeur Traffic Manager
Permet de gérer des profils Traffic Manager, mais pas de contrôler qui y a accès.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Network/trafficManagerProfiles/* |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="user-access-administrator"></a>Administrateur de l'accès utilisateur
Gérer l’accès utilisateur aux ressources Azure

| **Actions** |  |
| --- | --- |
| */read | Lire les ressources de tous les types, à l’exception des secrets. |
| Microsoft.Authorization/* | Gérer les autorisations |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="virtual-machine-administrator-login"></a>Connexion de l’administrateur aux machines virtuelles
-   Les utilisateurs disposant de ce rôle peuvent se connecter à une machine virtuelle avec des privilèges d’administrateur Windows ou d’utilisateur racine Linux.

| **Actions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="virtual-machine-contributor"></a>Collaborateur de machine virtuelle
Gérer les machines virtuelles, mais pas le réseau virtuel ou le compte de stockage auquel elles sont connectées

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Compute/availabilitySets/* | Créer et gérer des groupes à haute disponibilité de calcul |
| Microsoft.Compute/locations/* | Créer et gérer des emplacements de calcul |
| Microsoft.Compute/virtualMachines/* | Créer et gérer les machines virtuelles |
| Microsoft.Compute/virtualMachineScaleSets/* | Créez et gérez des jeux de mise à l’échelle des machines virtuelles |
| Microsoft.DevTestLab/schedules/* |  |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Joindre des pools d’adresses principales de passerelle d’application réseau |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joindre des pools d’adresses principales d’équilibreur de charge |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Joindre des pools NAT entrants d’équilibreur de charge |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joindre des règles NAT entrantes d’équilibreur de charge |
| Microsoft.Network/loadBalancers/probes/join/action | Autorise l’utilisation des sondes d’un équilibreur de charge. Par exemple, avec cette autorisation, la propriété healthProbe du groupe de machines virtuelles identiques peut faire référence à la sonde. |
| Microsoft.Network/loadBalancers/read | Lire les équilibreurs de charge |
| Microsoft.Network/locations/* | Créer et gérer des emplacements réseau |
| Microsoft.Network/networkInterfaces/* | Créer et gérer des interfaces réseau |
| Microsoft.Network/networkSecurityGroups/join/action | Joindre des groupes de sécurité réseau |
| Microsoft.Network/networkSecurityGroups/read | Lire les groupes de sécurité réseau |
| Microsoft.Network/publicIPAddresses/join/action | Joindre des adresses IP publiques réseau |
| Microsoft.Network/publicIPAddresses/read | Lire des adresses IP publiques réseau |
| Microsoft.Network/virtualNetworks/read | Lire les réseaux virtuels |
| Microsoft.Network/virtualNetworks/subnets/join/action | Joindre des sous-réseaux de réseau virtuel |
| Microsoft.RecoveryServices/locations/* |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/*/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Renvoie des détails d’objet de l’élément protégé. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | Créer un élément protégé de sauvegarde. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Crée une intention de protection de sauvegarde. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Renvoie toutes les stratégies de protection. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/write | Crée une stratégie de protection. |
| Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
| Microsoft.RecoveryServices/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
| Microsoft.RecoveryServices/Vaults/write | L’opération de création de coffre entraîne la création d’une ressource Azure de type « coffre ». |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Storage/storageAccounts/listKeys/action | Répertorier les clés de compte de stockage |
| Microsoft.Storage/storageAccounts/read | Lire les comptes de stockage |
| Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="virtual-machine-user-login"></a>Connexion de l’utilisateur aux machines virtuelles
Les utilisateurs disposant de ce rôle ont la possibilité de se connecter à une machine virtuelle en tant qu’utilisateur standard.

| **Actions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="web-plan-contributor"></a>Collaborateur de plan web
Gérer les plans web

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |
| Microsoft.Web/serverFarms/* | Créer et gérer des batteries de serveurs |

## <a name="website-contributor"></a>Collaborateur de site web
Gérer les sites web, mais pas les plans web auxquels ils sont connectés

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Insights/components/* | Créer et gérer les composants Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |
| Microsoft.Web/certificates/* | Créer et gérer les certificats de site web |
| Microsoft.Web/listSitesAssignedToHostName/read | Lire les sites affectés à un nom d’hôte |
| Microsoft.Web/serverFarms/join/action | Joindre des batteries de serveurs |
| Microsoft.Web/serverFarms/read | Lire des batteries de serveurs |
| Microsoft.Web/sites/* | Créer et gérer des sites web (la création de sites nécessite également des autorisations d’écriture pour le plan App Service associé) |

## <a name="see-also"></a>Voir aussi
* [Contrôle d’accès en fonction du rôle](role-based-access-control-configure.md): découvrez le contrôle d’accès en fonction du rôle dans le portail Azure.
* [Rôles personnalisés dans le contrôle d’accès en fonction du rôle (RBAC) Azure](role-based-access-control-custom-roles.md): découvrez comment créer des rôles personnalisés selon vos besoins d’accès.
* [Créer un rapport d’historique des modifications d’accès](role-based-access-control-access-change-history-report.md): effectuez le suivi des changements d’affection de rôle dans RBAC.
* [Résolution des problèmes de contrôle d’accès en fonction du rôle](role-based-access-control-troubleshooting.md): obtenez des suggestions pour résoudre les problèmes courants.
* [Autorisations dans Azure Security Center](../security-center/security-center-permissions.md)
