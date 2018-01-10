---
title: "Opérations du fournisseur Azure Resource Manager | Microsoft Docs"
description: "Décrit en détails les opérations disponibles sur les fournisseurs de ressources Microsoft Azure Resource Manager"
services: active-directory
documentationcenter: 
author: jboeshart
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: jaboes
ms.openlocfilehash: 27880402d377701448d095a1295ece875729cd67
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Opérations du fournisseur de ressources Azure Resource Manager

Ce document répertorie les opérations disponibles pour chaque fournisseur de ressources Microsoft Azure Resource Manager. Celles-ci peuvent être utilisées dans des rôles personnalisés pour fournir des autorisations de contrôle d’accès en fonction du rôle (RBAC) granulaires aux ressources dans Azure. Notez que cette liste n’est pas exhaustive et que des opérations peuvent être ajoutées ou supprimées à mesure que chaque fournisseur est mis à jour. Les chaînes d’opération sont au format `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Pour obtenir une liste complète et actuelle, utilisez `Get-AzureRmProviderOperation` (dans PowerShell) ou `az provider operation list` (dans Azure CLI v2) pour répertorier les opérations des fournisseurs de ressources Azure.

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Opération | Description |
|---|---|
|/configuration/action|Met à jour la configuration du client.|
|/services/action|Met à jour une instance de service dans le client.|
|/configuration/write|Crée une configuration de client.|
|/configuration/read|Lit la configuration du client.|
|/services/write|Crée une instance de service dans le client.|
|/services/read|Lit les instances de service dans le client.|
|/services/delete|Supprime une instance de service dans le client.|
|/services/servicemembers/action|Crée une instance de membre de service dans le service.|
|/services/servicemembers/read|Lit l’instance de membre de service dans le service.|
|/services/servicemembers/delete|Supprime une instance de membre de service dans le service.|
|/services/servicemembers/alerts/read|Lit les alertes pour un membre de service.|
|/services/alerts/read|Lit les alertes pour un service.|
|/services/alerts/read|Lit les alertes pour un service.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Opération | Description |
|---|---|
|/generateRecommendations/action|Génère des recommandations|
|/suppressions/action|Crée/met à jour des suppressions|
|/register/action|Inscrit l’abonnement pour Microsoft Advisor|
|/generateRecommendations/read|Obtient l’état de génération de recommandations|
|/recommendations/read|Lit les recommandations|
|/suppressions/read|Obtient les suppressions|
|/suppressions/delete|Supprime la suppression|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Opération | Description |
|---|---|
|/servers/read|Récupère les informations de l’Analysis Server spécifié.|
|/servers/write|Crée ou met à jour l’Analysis Server spécifié.|
|/servers/delete|Supprime l’Analysis Server.|
|/servers/suspend/action|Suspend l’Analysis Server.|
|/servers/resume/action|Reprend l’Analysis Server.|
|/servers/checkNameAvailability<br>/action|Vérifie que le nom d’Analysis Server donné est valide et non utilisé.|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Opération | Description |
|---|---|
|/checkNameAvailability/action|Vérifie si le nom du service indiqué est disponible|
|/register/action|Inscrire l’abonnement pour le fournisseur de ressources Microsoft.ApiManagement|
|/unregister/action|Annuler l’inscription de l’abonnement pour le fournisseur de ressources Microsoft.ApiManagement|
|/service/write|Créer une nouvelle instance du service Gestion des API|
|/service/read|Lire les métadonnées d’une instance du service Gestion des API|
|/service/delete|Supprimer l’instance du service Gestion des API|
|/service/updatehostname/action|Configurer, mettre à jour ou supprimer des noms de domaine personnalisés pour un service Gestion des API|
|/service/uploadcertificate/action|Télécharger le certificat SSL pour un service Gestion des API|
|/service/backup/action|Sauvegarder le service Gestion des API dans le conteneur spécifié dans un compte de stockage fourni par l’utilisateur|
|/service/restore/action|Restaurer le service Gestion des API à partir du conteneur spécifié dans un compte de stockage fourni par l’utilisateur|
|/service/managedeployments/action|Modifier une référence/unité, ajouter/supprimer des déploiements régionaux du service Gestion des API|
|/service/getssotoken/action|Obtient le jeton d’authentification unique qui peut être utilisé pour se connecter au portail hérité du service Gestion des API en tant qu’administrateur|
|/service/applynetworkconfigurationupdates/action|Met à jour les ressources Microsoft.ApiManagement exécutées dans le réseau virtuel pour sélectionner les paramètres réseau mis à jour.|
|/service/operationresults/read|Obtient l’état actuel d’une opération longue|
|/service/networkStatus/read|Obtient l’état d’accès réseau des ressources.|
|/service/loggers/read|Obtenir la liste des enregistreurs ou obtenir les détails d’un enregistreur|
|/service/loggers/write|Ajouter un nouvel enregistreur ou mettre à jour les détails d’un enregistreur existant|
|/service/loggers/delete|Supprimer l’enregistreur existant|
|/service/users/read|Obtenir la liste des utilisateurs inscrits ou obtenir les informations de compte d’un utilisateur|
|/service/users/write|Inscrire un nouvel utilisateur ou mettre à jour les informations de compte d’un utilisateur existant|
|/service/users/delete|Supprimer le compte d’utilisateur|
|/service/users/generateSsoUrl/action|Générez l’URL d’authentification unique. L’URL peut être utilisée pour accéder au portail d’administration|
|/service/users/subscriptions/read|Obtenir la liste des abonnements utilisateur|
|/service/users/keys/read|Obtenir la liste des clés utilisateur|
|/service/users/groups/read|Obtenir la liste des groupes d’utilisateurs|
|/service/tenant/operationResults/read|Obtenir la liste des résultats d’opération ou obtenir le résultat d’une opération spécifique|
|/service/tenant/policy/read|Obtenir la configuration de la stratégie du client|
|/service/tenant/policy/write|Définir la configuration de la stratégie du client|
|/service/tenant/policy/delete|Supprimer la configuration de la stratégie du client|
|/service/tenant/configuration/save/action|Crée une validation avec un instantané de configuration dans la branche spécifiée du référentiel|
|/service/tenant/configuration/deploy/action|Exécute une tâche de déploiement pour appliquer les modifications de la branche git spécifiée à la configuration dans la base de données.|
|/service/tenant/configuration/validate/action|Valide les modifications de la branche git spécifiée|
|/service/tenant/configuration/operationResults/read|Obtenir la liste des résultats d’opération ou obtenir le résultat d’une opération spécifique|
|/service/tenant/configuration/syncState/read|Obtenir l’état de la dernière synchronisation git|
|/service/tenant/access/read|Obtenir les informations d’accès client|
|/service/tenant/access/write|Mettre à jour les informations d’accès client|
|/service/tenant/access/regeneratePrimaryKey/action|Régénérer la clé d’accès primaire|
|/service/tenant/access/regenerateSecondaryKey/action|Régénérer la clé d’accès secondaire|
|/service/identityProviders/read|Obtenir la liste des fournisseurs d’identité ou obtenir les détails du fournisseur d’identité|
|/service/identityProviders/write|Créer un nouveau fournisseur d’identité ou mettre à jour les détails d’un fournisseur d’identité existant|
|/service/identityProviders/delete|Supprimer le fournisseur d’identité existant|
|/service/subscriptions/read|Obtenir la liste des abonnements de produit ou obtenir les détails de l’abonnement de produit|
|/service/subscriptions/write|Abonnez un utilisateur existant à un produit existant ou mettez à jour les détails de l’abonnement existant. Cette opération peut être utilisée pour renouveler l’abonnement|
|/service/subscriptions/delete|Supprimez l’abonnement. Cette opération peut être utilisée pour supprimer l’abonnement|
|/service/subscriptions/regeneratePrimaryKey/action|Régénérer la clé primaire d’abonnement|
|/service/subscriptions/regenerateSecondaryKey/action|Régénérer la clé secondaire d’abonnement|
|/service/backends/read|Obtenir la liste des serveurs principaux ou obtenir les détails du serveur principal|
|/service/backends/write|Ajouter un nouveau serveur principal ou mettre à jour les détails du serveur principal existant|
|/service/backends/delete|Supprimer le serveur principal existant|
|/service/apis/read|Obtenir la liste de toutes les API inscrites ou obtenir les détails de l’API|
|/service/apis/write|Créer une nouvelle API ou mettre à jour les détails de l’API existante|
|/service/apis/delete|Supprimer l’API existante|
|/service/apis/policy/read|Obtenir les détails de configuration de la stratégie pour l’API|
|/service/apis/policy/write|Définir les détails de configuration de la stratégie pour l’API|
|/service/apis/policy/delete|Supprimer la configuration de la stratégie de l’API|
|/service/apis/operations/read|Obtenir la liste des opérations d’API existantes ou obtenir les détails de l’opération d’API|
|/service/apis/operations/write|Créer une nouvelle opération d’API ou mettre à jour une opération d’API existante|
|/service/apis/operations/delete|Supprimer l’opération d’API existante|
|/service/apis/operations/policy/read|Obtenir les détails de configuration de la stratégie pour l’opération|
|/service/apis/operations/policy/write|Définir les détails de configuration de la stratégie pour l’opération|
|/service/apis/operations/policy/delete|Supprimer la configuration de la stratégie de l’opération|
|/service/products/read|Obtenir la liste des produits ou obtenir les détails du produit|
|/service/products/write|Créer un nouveau produit ou mettre à jour des détails du produit existants|
|/service/products/delete|Supprimer un produit existant|
|/service/products/subscriptions/read|Obtenir la liste des abonnements de produit|
|/service/products/apis/read|Obtenir la liste des API ajoutées à un produit existant|
|/service/products/apis/write|Ajouter une API existante à un produit existant|
|/service/products/apis/delete|Supprimer une API existante d’un produit existant|
|/service/products/policy/read|Obtenir la configuration de stratégie d’un produit existant|
|/service/products/policy/write|Définir la configuration de stratégie pour un produit existant|
|/service/products/policy/delete|Supprimer la configuration de stratégie d’un produit existant|
|/service/products/groups/read|Obtenir la liste des groupes de développeurs associés au produit|
|/service/products/groups/write|Associer un groupe de développeurs existant à un produit existant|
|/service/products/groups/delete|Supprime l’association d’un groupe de développeurs existant à un produit existant|
|/service/openidConnectProviders/read|Obtenir la liste des fournisseurs OpenID Connect ou obtenir les détails du fournisseur OpenID Connect|
|/service/openidConnectProviders/write|Créer un nouveau fournisseur OpenID Connect ou mettre à jour les détails d’un fournisseur OpenID Connect existant|
|/service/openidConnectProviders/delete|Supprimer le fournisseur OpenID Connect existant|
|/service/certificates/read|Obtenir la liste des certificats ou obtenir les détails du certificat|
|/service/certificates/write|Ajouter un nouveau certificat|
|/service/certificates/delete|Supprimer le certificat existant|
|/service/properties/read|Obtient la liste de toutes les propriétés ou obtient les détails de la propriété spécifiée|
|/service/properties/write|Crée une nouvelle propriété ou met à jour la valeur de la propriété spécifiée|
|/service/properties/delete|Supprime la propriété existante|
|/service/groups/read|Obtenir la liste des groupes ou obtient les détails d’un groupe|
|/service/groups/write|Créer un nouveau groupe ou mettre à jour les détails du groupe existant|
|/service/groups/delete|Supprimer le groupe existant|
|/service/groups/users/read|Obtenir la liste des utilisateurs du groupe|
|/service/groups/users/write|Ajouter un utilisateur existant à un groupe existant|
|/service/groups/users/delete|Supprimer un utilisateur existant d’un groupe existant|
|/service/authorizationServers/read|Obtenir la liste des serveurs d’autorisation ou obtenir les détails du serveur d’autorisation|
|/service/authorizationServers/write|Créer un nouveau serveur d’autorisation ou mettre à jour les détails d’un serveur d’autorisation existant|
|/service/authorizationServers/delete|Supprimer le serveur d’autorisation existant|
|/service/reports/bySubscription/read|Obtenez le rapport agrégé par abonnement.|
|/service/reports/byRequest/read|Obtenir des données de republication de requêtes|
|/service/reports/byOperation/read|Obtenir le rapport agrégé par opérations|
|/service/reports/byGeo/read|Obtenir le rapport agrégé par région géographique|
|/service/reports/byUser/read|Obtenez le rapport agrégé par développeurs.|
|/service/reports/byTime/read|Obtenir le rapport agrégé par périodes|
|/service/reports/byApi/read|Obtenir le rapport agrégé par API|
|/service/reports/byProduct/read|Obtenez le rapport agrégé par produits.|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Opération | Description |
|---|---|
|/elevateAccess/action|Accorde à l’appelant un accès Administrateur de l’accès utilisateur au niveau de la portée du client|
|/classicAdministrators/read|Lit les administrateurs de l’abonnement.|
|/classicAdministrators/write|Ajoutez ou modifiez un administrateur à un abonnement.|
|/classicAdministrators/delete|Supprime l’administrateur de l’abonnement.|
|/locks/read|Obtient des verrous au niveau de la portée spécifiée.|
|/locks/write|Ajoute des verrous au niveau de la portée spécifiée.|
|/locks/delete|Supprime des verrous au niveau de la portée spécifiée.|
|/policyAssignments/read|Obtenez des informations sur une affectation de stratégie.|
|/policyAssignments/write|Créez une affectation de stratégie au niveau de la portée spécifiée.|
|/policyAssignments/delete|Supprimez une affectation de stratégie au niveau de la portée spécifiée.|
|/permissions/read|Répertorie toutes les autorisations dont dispose l’appelant au niveau d’une portée donnée.|
|/roleDefinitions/read|Obtenez des informations sur une définition de rôle.|
|/roleDefinitions/write|Créez ou mettez à jour une définition de rôle personnalisé avec des autorisations spécifiées et des portées pouvant être affectées.|
|/roleDefinitions/delete|Supprimez la définition de rôle personnalisé spécifiée.|
|/providerOperations/read|Obtenez des opérations pour tous les fournisseurs de ressources qui peuvent être utilisées dans les définitions de rôles.|
|/policyDefinitions/read|Obtenez des informations sur une définition de stratégie.|
|/policyDefinitions/write|Créez une définition de stratégie personnalisée.|
|/policyDefinitions/delete|Supprimez une définition de stratégie.|
|/roleAssignments/read|Obtenez des informations sur une affectation de rôle.|
|/roleAssignments/write|Créez une affectation de rôle au niveau de la portée spécifiée.|
|/roleAssignments/delete|Supprimez une affectation de rôle au niveau de la portée spécifiée.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Opération | Description |
|---|---|
|/automationAccounts/read|Obtient un compte Azure Automation|
|/automationAccounts/write|Crée ou met à jour un compte Azure Automation|
|/automationAccounts/delete|Supprime un compte Azure Automation|
|/automationAccounts/configurations/readContent/action|Obtient un contenu d’Azure Automation DSC|
|/automationAccounts/hybridRunbookWorkerGroups/read|Lit des ressources Runbook Worker hybrides|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Supprime des ressources Runbook Worker hybrides|
|/automationAccounts/jobSchedules/read|Obtient une planification du travail Azure Automation|
|/automationAccounts/jobSchedules/write|Crée une planification du travail Azure Automation|
|/automationAccounts/jobSchedules/delete|Supprime une planification du travail Azure Automation|
|/automationAccounts/connectionTypes/read|Obtient une ressource de type de connexion Azure Automation|
|/automationAccounts/connectionTypes/write|Crée une ressource de type de connexion Azure Automation|
|/automationAccounts/connectionTypes/delete|Supprime une ressource de type de connexion Azure Automation|
|/automationAccounts/modules/read|Obtient un module Azure Automation|
|/automationAccounts/modules/write|Crée ou met à jour un module Azure Automation|
|/automationAccounts/modules/delete|Supprime un module Azure Automation|
|/automationAccounts/credentials/read|Obtient une ressource d’information d’identification Azure Automation|
|/automationAccounts/credentials/write|Crée ou met à jour une ressource d’information d’identification Azure Automation|
|/automationAccounts/credentials/delete|Supprime une ressource d’information d’identification Azure Automation|
|/automationAccounts/certificates/read|Obtient une ressource de certificat Azure Automation|
|/automationAccounts/certificates/write|Crée ou met à jour une ressource de certificat Azure Automation|
|/automationAccounts/certificates/delete|Supprime une ressource de certificat Azure Automation|
|/automationAccounts/schedules/read|Obtient une ressource de planification Azure Automation|
|/automationAccounts/schedules/write|Crée ou met à jour une ressource de planification Azure Automation|
|/automationAccounts/schedules/delete|Supprime une ressource de planification Azure Automation|
|/automationAccounts/jobs/read|Obtient un travail Azure Automation|
|/automationAccounts/jobs/write|Crée un travail Azure Automation|
|/automationAccounts/jobs/stop/action|Arrête un travail Azure Automation|
|/automationAccounts/jobs/suspend/action|Suspend un travail Azure Automation|
|/automationAccounts/jobs/resume/action|Reprend un travail Azure Automation|
|/automationAccounts/jobs/runbookContent/action|Obtient le contenu du runbook Azure Automation au moment de l’exécution du travail|
|/automationAccounts/jobs/output/action|Obtient le résultat d’un travail|
|/automationAccounts/jobs/read|Obtient un travail Azure Automation|
|/automationAccounts/jobs/write|Crée un travail Azure Automation|
|/automationAccounts/jobs/stop/action|Arrête un travail Azure Automation|
|/automationAccounts/jobs/suspend/action|Suspend un travail Azure Automation|
|/automationAccounts/jobs/resume/action|Reprend un travail Azure Automation|
|/automationAccounts/jobs/streams/read|Obtient un flux de travail Azure Automation|
|/automationAccounts/connections/read|Obtient une ressource de connexion Azure Automation|
|/automationAccounts/connections/write|Crée ou met à jour une ressource de connexion Azure Automation|
|/automationAccounts/connections/delete|Supprime une ressource de connexion Azure Automation|
|/automationAccounts/variables/read|Lit une ressource de variable Azure Automation|
|/automationAccounts/variables/write|Crée ou met à jour une ressource de variable Azure Automation|
|/automationAccounts/variables/delete|Supprime une ressource de variable Azure Automation|
|/automationAccounts/runbooks/readContent/action|Obtient le contenu d’un runbook Azure Automation|
|/automationAccounts/runbooks/read|Obtient un runbook Azure Automation|
|/automationAccounts/runbooks/write|Crée ou met à jour un runbook Azure Automation|
|/automationAccounts/runbooks/delete|Supprime un runbook Azure Automation|
|/automationAccounts/runbooks/draft/readContent/action|Obtient le contenu d’un brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/writeContent/action|Crée le contenu d’un brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/read|Obtient un brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/publish/action|Publie un brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/undoEdit/action|Annule les modifications apportées à un brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/testJob/read|Obtient un travail de test de brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/testJob/write|Crée un travail de test de brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/testJob/stop/action|Arrête un travail de test de brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Suspend un travail de test de brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/testJob/resume/action|Reprend un travail de test de brouillon de runbook Azure Automation|
|/automationAccounts/webhooks/read|Lit un webhook Azure Automation|
|/automationAccounts/webhooks/write|Crée ou met à jour un webhook Azure Automation|
|/automationAccounts/webhooks/delete|Supprime un webhook Azure Automation |
|/automationAccounts/webhooks/generateUri/action|Génère un URI pour un webhook Azure Automation|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

Ce fournisseur n’est pas un fournisseur ARM complet et ne fournit aucune opération ARM.

## <a name="microsoftbatch"></a>Microsoft.Batch

| Opération | Description |
|---|---|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources Batch et permet la création de comptes Batch|
|/batchAccounts/write|Crée un nouveau compte Batch ou met à jour un compte Batch existant|
|/batchAccounts/read|Répertorie les comptes Batch ou obtient les propriétés d’un compte Batch|
|/batchAccounts/delete|Supprime un compte Batch|
|/batchAccounts/listkeys/action|Répertorie les clés d’accès pour un compte Batch|
|/batchAccounts/regeneratekeys/action|Régénère les clés d’accès pour un compte Batch|
|/batchAccounts/syncAutoStorageKeys/action|Synchronise les clés d’accès pour le compte de stockage automatique configuré pour un compte Batch|
|/batchAccounts/applications/read|Répertorie les applications ou obtient les propriétés d’une application|
|/batchAccounts/applications/write|Crée une nouvelle application ou met à jour une application existante|
|/batchAccounts/applications/delete|Supprime une application|
|/batchAccounts/applications/versions/read|Obtient les propriétés d’un package d’application|
|/batchAccounts/applications/versions/write|Crée une nouveau package d’application ou met à jour un package d’application existant|
|/batchAccounts/applications/versions/activate/action|Active un package d’application|
|/batchAccounts/applications/versions/delete|Supprime un package d’application|
|/locations/quotas/read|Obtient les quotas Batch de l’abonnement spécifié au niveau de la région Azure spécifiée|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Opération | Description |
|---|---|
|/invoices/read|Répertorie les factures disponibles|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Opération | Description |
|---|---|
|/mapApis/Read|Lire une opération|
|/mapApis/Write|Écrire une opération|
|/mapApis/Delete|Supprimer une opération|
|/mapApis/regenerateKey/action|Régénère la clé|
|/mapApis/listSecrets/action|Répertorie les secrets|
|/mapApis/listSingleSignOnToken/action|Lire le jeton d’autorisation d’authentification unique pour la ressource|
|/Operations/read|Description de l’opération.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Opération | Description |
|---|---|
|/checknameavailability/action|Vérifie si un nom peut être utilisé avec un nouveau cache Redis|
|/register/action|Inscrit le fournisseur de ressources « Microsoft.Cache » à un abonnement|
|/unregister/action|Annule l’inscription du fournisseur de ressources « Microsoft.Cache » à un abonnement|
|/redis/write|Modifier les paramètres et la configuration du cache Redis dans le portail de gestion|
|/redis/read|Afficher les paramètres et la configuration du cache Redis dans le portail de gestion|
|/redis/delete|Supprimer l’intégralité du cache Redis|
|/redis/listKeys/action|Afficher la valeur des clés d’accès du cache Redis dans le portail de gestion|
|/redis/regenerateKey/action|Modifier la valeur des clés d’accès du cache Redis dans le portail de gestion|
|/redis/import/action|Importer des données d’un format spécifié à partir de plusieurs objets blob dans Redis|
|/redis/export/action|Exporter des données Redis vers des objets blob préfixés dans un format spécifié|
|/redis/forceReboot/action|Forcez le redémarrage d’instance de cache, potentiellement avec une perte de données.|
|/redis/stop/action|Arrêtez une instance de cache.|
|/redis/start/action|Démarrez une instance de cache.|
|/redis/metricDefinitions/read|Obtient les mesures disponibles pour un cache Redis|
|/redis/firewallRules/read|Obtenir les règles de pare-feu IP d’un cache Redis|
|/redis/firewallRules/write|Modifier les règles de pare-feu IP d’un cache Redis|
|/redis/firewallRules/delete|Supprimer des règles de pare-feu IP d’un cache Redis|
|/redis/listUpgradeNotifications/read|Répertoriez les dernières notifications de mise à niveau du client de cache.|
|/redis/linkedservers/read|Obtenez les serveurs liés associés à un cache Redis.|
|/redis/linkedservers/write|Ajouter un serveur lié à un cache Redis|
|/redis/linkedservers/delete|Supprimer un serveur lié d’un cache Redis|
|/redis/patchSchedules/read|Obtient la planification de mise à jour corrective d’un cache Redis|
|/redis/patchSchedules/write|Modifier la planification de mise à jour corrective d’un cache Redis|
|/redis/patchSchedules/delete|Supprimer la planification de correctif d’un cache Redis|

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Opération | Description |
|---|---|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Approvisionner le principal de service pour un principal d’application de service|
|/validateCertificateRegistrationInformation/Action|Valider l’objet d’achat de certificat sans le soumettre|
|/register/action|Inscrire le fournisseur de ressources de certificats Microsoft pour l’abonnement|
|/certificateOrders/Write|Ajouter un nouveau certificateOrder ou en mettre un existant à jour|
|/certificateOrders/Delete|Supprimer un AppServiceCertificate existant|
|/certificateOrders/Read|Obtenir la liste des CertificateOrders|
|/certificateOrders/reissue/Action|Réémettre un certificateorder existant|
|/certificateOrders/renew/Action|Renouveler un certificateorder existant|
|/certificateOrders/retrieveCertificateActions/Action|Récupérer la liste des actions de certificat|
|/certificateOrders/retrieveEmailHistory/Action|Récupérer l’historique d’e-mails de certificat|
|/certificateOrders/resendEmail/Action|Renvoyer l’e-mail de certificat|
|/certificateOrders/verifyDomainOwnership/Action|Vérifier la propriété du domaine|
|/certificateOrders/resendRequestEmails/Action|Renvoyer des e-mails de demande à une autre adresse de messagerie|
|/certificateOrders/resendRequestEmails/Action|Récupérer le Seal de site pour un App Service Certificate émis|
|/certificateOrders/certificates/Write|Ajouter un nouveau certificat ou en mettre un existant à jour|
|/certificateOrders/certificates/Delete|Supprimer un certificat existant|
|/certificateOrders/certificates/Read|Obtenir la liste des certificats|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Opération | Description |
|---|---|
|/register/action|S’inscrire à Classic Compute|
|/checkDomainNameAvailability/action|Vérifie la disponibilité d’un nom de domaine donné.|
|/moveSubscriptionResources/action|Déplacez toutes les ressources classiques vers un autre abonnement.|
|/validateSubscriptionMoveAvailability/action|Validez la disponibilité de l’abonnement pour l’opération de déplacement classique.|
|/operatingSystemFamilies/read|Répertorie les familles de système d’exploitation invité disponibles dans Microsoft Azure et répertorie également les versions de système d’exploitation disponibles pour chaque famille
|/capabilities/read|Affiche les fonctionnalités|
|/operatingSystems/read|Répertorie les versions de système d’exploitation invité qui sont actuellement disponibles dans Microsoft Azure.|
|/resourceTypes/skus/read|Obtient la liste des références (SKU) pour les types de ressources pris en charge.|
|/domainNames/read|Retournez les noms de domaine pour les ressources.|
|/domainNames/write|Ajoutez ou modifiez les noms de domaine pour les ressources.|
|/domainNames/delete|Supprimez les noms de domaine pour les ressources.|
|/domainNames/swap/action|Permute l’emplacement intermédiaire vers l’emplacement de production.|
|/domainNames/serviceCertificates/read|Retourne les certificats de service utilisés.|
|/domainNames/serviceCertificates/write|Ajoutez ou modifiez les certificats de service utilisés.|
|/domainNames/serviceCertificates/delete|Supprimez les certificats de service utilisés.|
|/domainNames/serviceCertificates/operationStatuses/read|Lit l’état de l’opération pour les certificats de service de noms de domaine.|
|/domainNames/capabilities/read|Affiche les fonctionnalités de nom de domaine|
|/domainNames/extensions/read|Retourne les extensions de nom de domaine.|
|/domainNames/extensions/write|Ajoutez les extensions de nom de domaine.|
|/domainNames/extensions/delete|Supprimez les extensions de nom de domaine.|
|/domainNames/extensions/operationStatuses/read|Lit l’état de l’opération pour les extensions de noms de domaine.|
|/domainNames/active/write|Définit le nom de domaine actif.|
|/domainNames/slots/read|Affiche les emplacements de déploiement.|
|/domainNames/slots/write|Crée ou met à jour le déploiement.|
|/domainNames/slots/delete|Supprime un emplacement de déploiement donné.|
|/domainNames/slots/start/action|Démarre un emplacement de déploiement.|
|/domainNames/slots/stop/action|Suspend l’emplacement de déploiement.|
|/domainNames/slots/operationStatuses/read|Lit l’état de l’opération pour les emplacements de noms de domaine.|
|/domainNames/slots/roles/read|Obtenez le rôle de l’emplacement de déploiement.|
|/domainNames/slots/roles/extensionReferences/read|Retourne la référence d’extension pour le rôle d’emplacement de déploiement.|
|/domainNames/slots/roles/extensionReferences/write|Ajoutez ou modifiez la référence d’extension pour le rôle d’emplacement de déploiement.|
|/domainNames/slots/roles/extensionReferences/delete|Supprimez la référence d’extension pour le rôle d’emplacement de déploiement.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|Lit l’état de l’opération pour les références d’extension de rôles d’emplacements de noms de domaine.|
|/domainNames/slots/roles/roleInstances/read|Obtenez l’instance de rôle.|
|/domainNames/slots/roles/roleInstances/restart/action|Redémarre des instances de rôle.|
|/domainNames/slots/roles/roleInstances/reimage/action|Réinitialise l’instance de rôle.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|Lit l’état de l’opération pour les instances de rôle de rôles d’emplacements de noms de domaine.|
|/domainNames/slots/state/start/write|Modifie l’état d’emplacement de déploiement sur arrêté.|
|/domainNames/slots/state/stop/write|Modifie l’état d’emplacement de déploiement sur démarré.|
|/domainNames/slots/upgradeDomain/write|Parcourez la mise à niveau du domaine.|
|/domainNames/internalLoadBalancers/read|Obtient les équilibreurs de charge interne.|
|/domainNames/internalLoadBalancers/write|Crée un nouvel équilibrage de charge interne.|
|/domainNames/internalLoadBalancers/delete|Supprimez un nouvel équilibrage de charge interne.|
|/domainNames/internalLoadBalancers/operationStatuses/read|Lit l’état de l’opération pour les équilibreurs de charge interne de noms de domaine.|
|/domainNames/loadBalancedEndpointSets/read|Affiche les ensembles de points de terminaison à charge équilibrée|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|Lit l’état de l’opération pour les ensembles de points de terminaison à charge équilibrée de noms de domaine.|
|/domainNames/availabilitySets/read|Affichez le groupe à haute disponibilité de la ressource.|
|/quotas/read|Obtenez le quota de l’abonnement.|
|/virtualMachines/read|Récupère la liste des machines virtuelles.|
|/virtualMachines/write|Ajoutez ou modifiez des machines virtuelles.|
|/virtualMachines/delete|Supprime des machines virtuelles.|
|/virtualMachines/start/action|Démarrez la machine virtuelle.|
|/virtualMachines/redeploy/action|Redéploie la machine virtuelle.|
|/virtualMachines/restart/action|Redémarre des machines virtuelles.|
|/virtualMachines/stop/action|Arrête la machine virtuelle.|
|/virtualMachines/shutdown/action|Arrêtez la machine virtuelle.|
|/virtualMachines/attachDisk/action|Attache un disque de données à une machine virtuelle.|
|/virtualMachines/detachDisk/action|Détache un disque de données d’une machine virtuelle.|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|Télécharge le fichier RDP pour la machine virtuelle.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/read|Obtient le groupe de sécurité réseau associé à l’interface réseau.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/write|Ajoute un groupe de sécurité réseau associé à l’interface réseau.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/delete|Supprime le groupe de sécurité réseau associé à l’interface réseau.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/operationStatuses/read|Lit l’état de l’opération pour les machines virtuelles associées à des groupes de sécurité réseau.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Obtient les définitions de mesures.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Obtient les paramètres des diagnostics.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Ajoutez ou modifiez des paramètres de diagnostics.|
|/virtualMachines/metrics/read|Obtient les mesures.|
|/virtualMachines/operationStatuses/read|Lit l’état de l’opération pour les machines virtuelles.|
|/virtualMachines/extensions/read|Obtient l’extension de machine virtuelle.|
|/virtualMachines/extensions/write|Place l’extension de machine virtuelle.|
|/virtualMachines/extensions/operationStatuses/read|Lit l’état de l’opération pour les extensions de machines virtuelles.|
|/virtualMachines/asyncOperations/read|Obtient les opérations asynchrones possibles|
|/virtualMachines/disks/read|Récupère la liste des disques de données|
|/virtualMachines/associatedNetworkSecurityGroups/read|Obtient le groupe de sécurité réseau associé à la machine virtuelle.|
|/virtualMachines/associatedNetworkSecurityGroups/write|Ajoute un groupe de sécurité réseau associé à la machine virtuelle.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|Supprime le groupe de sécurité réseau associé à la machine virtuelle.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|Lit l’état de l’opération pour les machines virtuelles associées à des groupes de sécurité réseau.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Opération | Description |
|---|---|
|/register/action|S’inscrire à un réseau classique|
|/gatewaySupportedDevices/read|Récupère la liste des appareils pris en charge.|
|/reservedIps/read|Obtient les adresses IP réservées|
|/reservedIps/write|Ajouter une nouvelle adresse IP réservée|
|/reservedIps/delete|Supprimez une adresse IP réservée.|
|/reservedIps/link/action|Lier une adresse IP réservée|
|/reservedIps/join/action|Joindre une adresse IP réservée|
|/reservedIps/operationStatuses/read|Lit l’état de l’opération pour les adresses IP réservées.|
|/virtualNetworks/read|Obtenez le réseau virtuel.|
|/virtualNetworks/write|Ajoutez un nouveau réseau virtuel.|
|/virtualNetworks/delete|Supprime le réseau virtuel.|
|/virtualNetworks/peer/action|Appaire un réseau virtuel avec un autre réseau virtuel.|
|/virtualNetworks/join/action|Joint le réseau virtuel.|
|/virtualNetworks/checkIPAddressAvailability/action|Vérifie la disponibilité d’une adresse IP donnée dans un réseau virtuel.|
|/virtualNetworks/capabilities/read|Affiche les fonctionnalités|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/read|Obtient le groupe de sécurité réseau associé au sous-réseau.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/write|Ajoute un groupe de sécurité réseau associé au sous-réseau.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/delete|Supprime le groupe de sécurité réseau associé au sous-réseau.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/operationStatuses/read|Lit l’état de l’opération pour le sous-réseau de réseau virtuel associé au groupe de sécurité réseau.|
|/virtualNetworks/operationStatuses/read|Lit l’état de l’opération pour les réseaux virtuels.|
|/virtualNetworks/gateways/read|Obtient les passerelles de réseau virtuel.|
|/virtualNetworks/gateways/write|Ajoute une passerelle de réseau virtuel.|
|/virtualNetworks/gateways/delete|Supprime la passerelle de réseau virtuel.|
|/virtualNetworks/gateways/startDiagnostics/action|Démarre le diagnostic pour la passerelle de réseau virtuel.|
|/virtualNetworks/gateways/stopDiagnostics/action|Arrête le diagnostic pour la passerelle de réseau virtuel.|
|/virtualNetworks/gateways/downloadDiagnostics/action|Télécharge les diagnostics de la passerelle.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Récupère la clé de service du circuit.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|Télécharge le script de configuration d’appareil.|
|/virtualNetworks/gateways/listPackage/action|Répertorie le package de passerelle de réseau virtuel.|
|/virtualNetworks/gateways/operationStatuses/read|Lit l’état de l’opération pour les passerelles de réseaux virtuels.|
|/virtualNetworks/gateways/packages/read|Obtient le package de passerelle de réseau virtuel.|
|/virtualNetworks/gateways/connections/read|Récupère la liste des connexions.|
|/virtualNetworks/gateways/connections/connect/action|Établit une connexion de passerelle site à site.|
|/virtualNetworks/gateways/connections/disconnect/action|Déconnecte une connexion de passerelle site à site.|
|/virtualNetworks/gateways/connections/test/action|Teste une connexion de passerelle site à site.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|Lisez les certificats clients révoqués.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|Révoque un certificat client.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|Annule la révocation d’un certificat client.|
|/virtualNetworks/gateways/clientRootCertificates/read|Recherchez les certificats racine client.|
|/virtualNetworks/gateways/clientRootCertificates/write|Télécharge un nouveau certificat racine client.|
|/virtualNetworks/gateways/clientRootCertificates/delete|Supprime le certificat client de passerelle de réseau virtuel.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|Télécharge le certificat par empreinte.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|Répertorie le package de certificat de passerelle de réseau virtuel.|
|/networkSecurityGroups/read|Obtient le groupe de sécurité réseau.|
|/networkSecurityGroups/write|Ajoute un nouveau groupe de sécurité réseau.|
|/networkSecurityGroups/delete|Supprime le groupe de sécurité réseau.|
|/networkSecurityGroups/operationStatuses/read|Lit l’état de l’opération pour le groupe de sécurité réseau.|
|/networkSecurityGroups/securityRules/read|Obtient la règle de sécurité.|
|/networkSecurityGroups/securityRules/write|Ajoute ou met à jour une règle de sécurité.|
|/networkSecurityGroups/securityRules/delete|Supprime la règle de sécurité.|
|/networkSecurityGroups/securityRules/operationStatuses/read|Lit l’état de l’opération pour les règles de sécurité du groupe de sécurité réseau.|
|/quotas/read|Obtient le quota de l’abonnement.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Opération | Description |
|---|---|
|/register/action|S’inscrire à un stockage classique|
|/checkStorageAccountAvailability/action|Vérifie la disponibilité d’un compte de stockage.|
|/capabilities/read|Affiche les fonctionnalités|
|/publicImages/read|Obtient l’image de machine virtuelle publique.|
|/images/read|Retourne l’image.|
|/storageAccounts/read|Retourne le compte de stockage avec le compte spécifique.|
|/storageAccounts/write|Ajoute un nouveau compte de stockage.|
|/storageAccounts/delete|Supprime le compte de stockage.|
|/storageAccounts/listKeys/action|Répertorie les clés d’accès des comptes de stockage.|
|/storageAccounts/regenerateKey/action|Régénère les clés d’accès existantes du compte de stockage.|
|/storageAccounts/operationStatuses/read|Lit l’état de l’opération pour la ressource.|
|/storageAccounts/images/read|Retourne l’image du compte de stockage.|
|/storageAccounts/images/delete|Supprime une image du compte de stockage spécifique.|
|/storageAccounts/disks/read|Retourne le disque du compte de stockage.|
|/storageAccounts/disks/write|Ajoute un disque de compte de stockage.|
|/storageAccounts/disks/delete|Supprime un disque de compte de stockage spécifique.|
|/storageAccounts/disks/operationStatuses/read|Lit l’état de l’opération pour la ressource.|
|/storageAccounts/osImages/read|Retourne l’image du système d’exploitation de compte de stockage.|
|/storageAccounts/osImages/delete|Supprime une image du système d’exploitation de compte de stockage spécifique.|
|/storageAccounts/services/read|Obtient les services disponibles.|
|/storageAccounts/services/metricDefinitions/read|Obtient les définitions de mesures.|
|/storageAccounts/services/metrics/read|Obtient les mesures.|
|/storageAccounts/services/diagnosticSettings/read|Obtient les paramètres des diagnostics.|
|/storageAccounts/services/diagnosticSettings/write|Ajoutez ou modifiez des paramètres de diagnostics.|
|/disks/read|Retourne le disque du compte de stockage.|
|/osImages/read|Retourne l’image du système d’exploitation.|
|/quotas/read|Obtient le quota de l’abonnement.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Opération | Description |
|---|---|
|/accounts/read|Lit les comptes d’API.|
|/accounts/write|Écrit les comptes d’API.|
|/accounts/delete|Supprime les comptes d’API|
|/accounts/listKeys/action|Afficher la liste des clés|
|/accounts/regenerateKey/action|Régénérer la clé|
|/accounts/skus/read|Lit les références disponibles pour une ressource existante.|
|/accounts/usages/read|Obtenir l’utilisation du quota pour une ressource existante.|
|/Operations/read|Description de l’opération.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Opération | Description |
|---|---|
|/RateCard/read|Retourne les données, les métadonnées de ressource/compteur et les taux d’offre de l’abonnement concerné.|
|/UsageAggregates/read|Récupère l’utilisation de Microsoft Azure par un abonnement. Le résultat contient des données d’utilisation, des informations sur l’abonnement et la ressource agrégées, sur un intervalle de temps donné.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Opération | Description |
|---|---|
|/register/action|Inscrit l’abonnement auprès du fournisseur de ressources Microsoft.Compute|
|/restorePointCollections/read|Obtenir les propriétés d’une collection de points de restauration|
|/restorePointCollections/write|Crée une nouvelle collection de points de restauration ou met à jour une collection existante|
|/restorePointCollections/delete|Supprimer la collection de points de restauration et les points de restauration contenus|
|/restorePointCollections/restorePoints/read|Obtenir les propriétés d’un point de restauration|
|/restorePointCollections/restorePoints/write|Créer un nouveau point de restauration|
|/restorePointCollections/restorePoints/delete|Supprimer le point de restauration|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Obtenir les propriétés d’un point de restauration, ainsi que les URI SAP des objets blob|
|/virtualMachineScaleSets/read|Obtenir les propriétés d’un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/write|Créer ou mettre à jour un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/delete|Supprimer le groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/start/action|Démarrer les instances du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/powerOff/action|Mettre hors tension les instances du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/restart/action|Redémarrer les instances du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/deallocate/action|Mettre hors tension et libérer les ressources de calcul des instances du groupe de machines virtuelles identiques |
|/virtualMachineScaleSets/manualUpgrade/action|Mettre manuellement à jour les instances avec le dernier modèle du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/scale/action|Augmenter/diminuer le nombre d’instances d’un groupe de machines virtuelles identiques existant|
|/virtualMachineScaleSets/instanceView/read|Générer une vue d’instance du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/skus/read|Répertorier les références valides pour un groupe de machines virtuelles identiques existant|
|/virtualMachineScaleSets/virtualMachines/read|Récupérer les propriétés d’une machine virtuelle dans un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/virtualMachines/delete|Supprimer une machine virtuelle spécifique d’un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/virtualMachines/start/action|Démarrer une instance de machine virtuelle dans un groupe de machines virtuelles identiques.|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|Mettre hors tension une instance de machine virtuelle dans un groupe de machines virtuelles identiques.|
|/virtualMachineScaleSets/virtualMachines/restart/action|Redémarrer une instance de machine virtuelle dans un groupe de machines virtuelles identiques.|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|Mettre hors tension et libérer les ressources de calcul d’une machine virtuelle d’un groupe de machines virtuelles identiques.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|Récupérer la vue d’instance d’une machine virtuelle dans un groupe de machines virtuelles identiques.|
|/images/read|Obtenir les propriétés de l’image|
|/images/write|Créer ou mettre à jour une image|
|/images/delete|Supprimer l’image|
|/operations/read|Répertorier les opérations disponibles dans le fournisseur de ressources Microsoft.Compute|
|/disks/read|Obtenir les propriétés d’un disque|
|/disks/write|Créer ou mettre à jour un disque|
|/disks/delete|Supprimer le disque|
|/disks/beginGetAccess/action|Obtenir l’URI SAP du disque pour l’accès aux objets blob|
|/disks/endGetAccess/action|Révoquer l’URI SAP du disque|
|/snapshots/read|Obtenir les propriétés d’une capture instantanée|
|/snapshots/write|Créer ou mettre à jour une capture instantanée|
|/snapshots/delete|Supprimer une capture instantanée|
|/availabilitySets/read|Obtenir les propriétés d’un groupe à haute disponibilité|
|/availabilitySets/write|Créer ou mettre à jour un groupe à haute disponibilité|
|/availabilitySets/delete|Supprimer le groupe à haute disponibilité|
|/availabilitySets/vmSizes/read|Répertorier les tailles disponibles pour la création ou la mise à jour d’une machine virtuelle dans un groupe à haute disponibilité|
|/virtualMachines/read|Obtenir les propriétés d’une machine virtuelle|
|/virtualMachines/write|Créer ou mettre à jour une machine virtuelle|
|/virtualMachines/delete|Supprimer la machine virtuelle|
|/virtualMachines/start/action|Démarrer la machine virtuelle|
|/virtualMachines/powerOff/action|Mettre hors tension la machine virtuelle. Notez que la machine virtuelle sera toujours facturée.|
|/virtualMachines/redeploy/action|Redéployer la machine virtuelle|
|/virtualMachines/restart/action|Redémarrer la machine virtuelle|
|/virtualMachines/deallocate/action|Mettre hors tension la machine virtuelle et libérer les ressources de calcul|
|/virtualMachines/generalize/action|Définir l’état de la machine virtuelle sur Généralisée et préparer la machine virtuelle pour la capture|
|/virtualMachines/capture/action|Capturer la machine virtuelle en copiant les disques durs virtuels et générer un modèle qui peut être utilisé pour créer des machines virtuelles similaires|
|/virtualMachines/convertToManagedDisks/action|Convertir les disques basés sur des objets blob de la machine virtuelle en disques gérés|
|/virtualMachines/vmSizes/read|Répertorier les tailles disponibles pour la mise à jour de la machine virtuelle|
|/virtualMachines/instanceView/read|Afficher l’état d’exécution détaillé de la machine virtuelle et de ses ressources|
|/virtualMachines/extensions/read|Afficher les propriétés d’une extension de machine virtuelle|
|/virtualMachines/extensions/write|Créer ou mettre à jour une extension de machine virtuelle|
|/virtualMachines/extensions/delete|Supprimer l’extension de machine virtuelle|
|/locations/vmSizes/read|Répertorier les tailles de machines virtuelles disponibles dans un emplacement|
|/locations/usages/read|Afficher les limites du service et les quantités en cours d’utilisation pour les ressources de calcul de l’abonnement dans un emplacement|
|/locations/operations/read|Afficher l’état d’une opération asynchrone|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Opération | Description |
|---|---|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources du registre de conteneurs et active la création de registres de conteneurs.|
|/checknameavailability/read|Vérifie que le nom du registre est valide et qu’il n’est pas déjà utilisé.|
|/registries/read|Retourne la liste des registres de conteneurs ou affiche les propriétés pour le registre de conteneurs spécifié.|
|/registries/write|Crée un registre de conteneurs avec les paramètres spécifiés ou met à jour les propriétés ou les balises pour le registre de conteneurs spécifié.|
|/registries/delete|Supprime un registre de conteneurs existant.|
|/registries/listCredentials/action|Répertorie les informations d’identification pour le registre de conteneurs spécifié.|
|/registries/regenerateCredential/action|Régénère les informations d’identification pour le registre de conteneurs spécifié.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Opération | Description |
|---|---|
|/containerServices/subscriptions/read|Affiche les services de conteneur spécifiés en fonction de l’abonnement|
|/containerServices/resourceGroups/read|Affiche les services de conteneur spécifiés en fonction du groupe de ressources|
|/containerServices/resourceGroups/ContainerServiceName/read|Affiche le service de conteneur spécifié|
|/containerServices/resourceGroups/ContainerServiceName/write|Ajoute ou met à jour le service de conteneur spécifié|
|/containerServices/resourceGroups/ContainerServiceName/delete|Supprime le service de conteneur spécifié|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Opération | Description |
|---|---|
|/updateCommunicationPreference/action|Mettre à jour les préférences de communication|
|/listCommunicationPreference/action|Répertorier les préférences de communication|
|/applications/read|Opération de lecture|
|/applications/write|Opération d’écriture|
|/applications/write|Opération d’écriture|
|/applications/delete|Opération de suppression|
|/applications/listSecrets/action|Afficher la liste des clés secrètes|
|/applications/listSingleSignOnToken/action|Afficher les jetons d’authentification unique|
|/operations/read|opérations de lecture|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Opération | Description |
|---|---|
|/hubs/read|Afficher un concentrateur Azure Customer Insights|
|/hubs/write|Créer ou mettre à jour un concentrateur Azure Customer Insights|
|/hubs/delete|Supprimer un concentrateur Azure Customer Insights|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Afficher les mesures disponibles pour la ressource|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Afficher le paramètre de diagnostic pour la ressource|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Créer ou mettre à jour le paramètre de diagnostic pour la ressource|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|Obtenir les journaux disponibles pour la ressource|
|/hubs/authorizationPolicies/read|Afficher une stratégie de signature d’accès partagé Azure Customer Insights|
|/hubs/authorizationPolicies/write|Créer ou mettre à jour une stratégie de signature d’accès partagé Azure Customer Insights|
|/hubs/authorizationPolicies/delete|Supprimer une stratégie de signature d’accès partagé Azure Customer Insights|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Régénérer la clé primaire d’une stratégie de signature d’accès partagé Azure Customer Insights|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Régénérer la clé secondaire d’une stratégie de signature d’accès partagé Azure Customer Insights|
|/hubs/profiles/read|Afficher un profil Azure Customer Insights|
|/hubs/profiles/write|Écrire un profil Azure Customer Insights|
|/hubs/kpi/read|Afficher un indicateur de performance clé Azure Customer Insights|
|/hubs/kpi/write|Créer ou mettre à jour un indicateur de performance clé Azure Customer Insights|
|/hubs/kpi/delete|Supprimer un indicateur de performance clé Azure Customer Insights|
|/hubs/views/read|Afficher une vue d’application Azure Customer Insights|
|/hubs/views/write|Créer ou mettre à jour une vue d’application Azure Customer Insights|
|/hubs/views/delete|Supprimer une vue d’application Azure Customer Insights|
|/hubs/interactions/read|Afficher une interaction Azure Customer Insights|
|/hubs/interactions/write|Créer ou mettre à jour une interaction Azure Customer Insights|
|/hubs/roleAssignments/read|Afficher une affectation RBAC Azure Customer Insights|
|/hubs/roleAssignments/write|Créer ou mettre à jour une affectation RBAC Azure Customer Insights|
|/hubs/roleAssignments/delete|Supprimer une affectation RBAC Azure Customer Insights|
|/hubs/connectors/read|Afficher un connecteur Azure Customer Insights|
|/hubs/connectors/write|Créer ou mettre à jour un connecteur Azure Customer Insights|
|/hubs/connectors/delete|Supprimer un connecteur Azure Customer Insights|
|/hubs/connectors/mappings/read|Afficher un mappage de connecteur Azure Customer Insights|
|/hubs/connectors/mappings/write|Créer ou mettre à jour un mappage de connecteur Azure Customer Insights|
|/hubs/connectors/mappings/delete|Supprimer un mappage de connecteur Azure Customer Insights|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Opération | Description |
|---|---|
|/checkNameAvailability/action|Vérifie la disponibilité du nom de catalogue pour le locataire.|
|/catalogs/read|Affiche les propriétés du ou des catalogues associés à l’abonnement ou au groupe de ressources.|
|/catalogs/write|Crée un catalogue ou met à jour les balises et les propriétés du catalogue.|
|/catalogs/delete|Supprime le catalogue.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Opération | Description |
|---|---|
|/datafactories/read|Affiche la fabrique de données.|
|/datafactories/write|Crée ou met à jour la fabrique de données.|
|/datafactories/delete|Supprime la fabrique de données.|
|/datafactories/datapipelines/read|Affiche le pipeline.|
|/datafactories/datapipelines/delete|Supprime le pipeline.|
|/datafactories/datapipelines/pause/action|Suspend le pipeline.|
|/datafactories/datapipelines/resume/action|Rétablit le pipeline.|
|/datafactories/datapipelines/update/action|Met à jour le pipeline.|
|/datafactories/datapipelines/write|Crée ou met à jour un pipeline.|
|/datafactories/linkedServices/read|Affiche le service lié.|
|/datafactories/linkedServices/delete|Supprime le service lié.|
|/datafactories/linkedServices/write|Crée ou met à jour un service lié.|
|/datafactories/tables/read|Affiche la table.|
|/datafactories/tables/delete|Supprime la table.|
|/datafactories/tables/write|Crée ou met à jour la table.|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Opération | Description |
|---|---|
|/accounts/read|Obtenir des informations sur le compte Data Lake Analytics.|
|/accounts/write|Créer ou mettre à jour le compte Data Lake Analytics.|
|/accounts/delete|Supprimer le compte Data Lake Analytics.|
|/accounts/firewallRules/read|Obtenir des informations sur une règle de pare-feu.|
|/accounts/firewallRules/write|Créer ou mettre à jour une règle de pare-feu.|
|/accounts/firewallRules/delete|Supprimer une règle de pare-feu.|
|/accounts/storageAccounts/read|Afficher le compte de stockage lié au compte Data Lake Analytics.|
|/accounts/storageAccounts/write|Associer un compte de stockage au compte Data Lake Analytics.|
|/accounts/storageAccounts/delete|Dissocier un compte de stockage du compte Data Lake Analytics.|
|/accounts/storageAccounts/Containers/read|Afficher les conteneurs associés au compte de stockage.|
|/accounts/storageAccounts/Containers/listSasTokens/action|Répertorier les jetons SAP pour le conteneur de stockage.|
|/accounts/dataLakeStoreAccounts/read|Afficher le compte Data Lake Store lié au compte Data Lake Analytics.|
|/accounts/dataLakeStoreAccounts/write|Associer un compte Data Lake Store au compte Data Lake Analytics.|
|/accounts/dataLakeStoreAccounts/delete|Dissocier un compte Data Lake Store du compte Data Lake Analytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Opération | Description |
|---|---|
|/accounts/read|Obtenir des informations sur un compte Data Lake Store existant.|
|/accounts/write|Créer un nouveau compte Data Lake Store ou mettre à jour un compte Data Lake Store existant.|
|/accounts/delete|Supprimer un compte Data Lake Store existant.|
|/accounts/firewallRules/read|Obtenir des informations sur une règle de pare-feu.|
|/accounts/firewallRules/write|Créer ou mettre à jour une règle de pare-feu.|
|/accounts/firewallRules/delete|Supprimer une règle de pare-feu.|
|/accounts/trustedIdProviders/read|Obtenir des informations sur un fournisseur d’identité approuvé.|
|/accounts/trustedIdProviders/write|Créer ou mettre à jour un fournisseur d’identité approuvé.|
|/accounts/trustedIdProviders/delete|Supprimer un fournisseur d’identité approuvé.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Opération | Description |
|---|---|
|/register/action|Inscrire l’abonnement pour le fournisseur de ressources IotHub et activer la création de ressources IotHub|
|/checkNameAvailability/Action|Vérifier que le nom IotHub est disponible|
|/usages/Read|Obtenir des informations sur l’utilisation de l’abonnement pour ce fournisseur|
|/operations/Read|Afficher toutes les opérations du fournisseur de ressources|
|/iotHubs/Read|Afficher les ressources IotHub|
|/iotHubs/Write|Créer ou mettre à jour une ressource IotHub|
|/iotHubs/Delete|Supprimer une ressource IotHub|
|/iotHubs/listkeys/Action|Obtenir toutes les clés IotHub|
|/iotHubs/exportDevices/Action|Exporter des appareils|
|/iotHubs/importDevices/Action|Importer des appareils|
|/IotHubs/metricDefinitions/read|Obtenir les mesures disponibles pour le service IotHub|
|/iotHubs/iotHubKeys/listkeys/Action|Obtenir la clé IotHub pour le nom spécifié|
|/iotHubs/iotHubStats/Read|Afficher les statistiques IotHub|
|/iotHubs/quotaMetrics/Read|Obtenir les mesures de quota|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Créer un groupe de consommateurs EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Afficher les groupes de consommateurs EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|Supprimer le groupe de consommateurs EventHub|
|/iotHubs/routing/routes/$testall/Action|Tester un message sur l’ensemble des itinéraires existants|
|/iotHubs/routing/routes/$testnew/Action|Tester un message sur un itinéraire test fourni|
|/IotHubs/diagnosticSettings/read|Afficher le paramètre de diagnostic pour la ressource|
|/IotHubs/diagnosticSettings/write|Créer ou mettre à jour le paramètre de diagnostic pour la ressource|
|/iotHubs/skus/Read|Afficher les références IotHub valides|
|/iotHubs/jobs/Read|Obtenir des informations sur les travaux soumis à un concentrateur IotHub donné|
|/iotHubs/routingEndpointsHealth/Read|Afficher l’état de tous les points de terminaison de routage pour un concentrateur IotHub|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Opération | Description |
|---|---|
|/Subscription/register/action|Enregistrer l’abonnement.|
|/labs/delete|Supprimer des laboratoires.|
|/labs/read|Afficher des laboratoires.|
|/labs/write|Ajouter ou modifier des laboratoires.|
|/labs/ListVhds/action|Répertorier les images de disque disponibles pour la création d’images personnalisées.|
|/labs/GenerateUploadUri/action|Générer un URI pour charger des images de disque personnalisées dans un laboratoire.|
|/labs/CreateEnvironment/action|Créer des machines virtuelles dans un laboratoire.|
|/labs/ClaimAnyVm/action|Revendiquer une machine virtuelle exigible aléatoire dans le laboratoire.|
|/labs/ExportResourceUsage/action|Exporter les données d’utilisation des ressources du laboratoire vers un compte de stockage.|
|/labs/users/delete|Supprimer des profils utilisateur.|
|/labs/users/read|Afficher les profils utilisateur.|
|/labs/users/write|Ajouter ou modifier des profils utilisateur.|
|/labs/users/secrets/delete|Supprimer des clés secrètes.|
|/labs/users/secrets/read|Afficher les clés secrètes.|
|/labs/users/secrets/write|Ajouter ou modifier des clés secrètes.|
|/labs/users/environments/delete|Supprimer des environnements.|
|/labs/users/environments/read|Afficher les environnements.|
|/labs/users/environments/write|Ajouter ou modifier des environnements.|
|/labs/users/disks/delete|Supprimer des disques.|
|/labs/users/disks/delete|Afficher les disques.|
|/labs/users/disks/write|Ajouter ou modifier des disques.|
|/labs/users/disks/Attach/action|Créer le bail du disque et l’associer à la machine virtuelle.|
|/labs/users/disks/Detach/action|Casser et dissocier le bail du disque associé à la machine virtuelle.|
|/labs/customImages/delete|Supprimer des images personnalisées.|
|/labs/customImages/read|Lire des images personnalisées.|
|/labs/customImages/write|Ajouter ou modifier des images personnalisées.|
|/labs/serviceRunners/delete|Supprimer des exécuteurs de service.|
|/labs/serviceRunners/read|Lire des exécuteurs de service.|
|/labs/serviceRunners/write|Ajouter ou modifier des exécuteurs de service.|
|/labs/artifactSources/delete|Supprimer des sources d’artefact.|
|/labs/artifactSources/read|Lire des sources d’artefact.|
|/labs/artifactSources/write|Ajouter ou modifier des sources d’artefact.|
|/labs/artifactSources/artifacts/read|Lire des artefacts.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Générer un modèle ARM pour l’artefact donné, télécharger les fichiers requis vers un compte de stockage et valider l’artefact généré.|
|/labs/artifactSources/armTemplates/read|Lire les modèles Azure Resource Manager.|
|/labs/costs/read|Lire les coûts.|
|/labs/costs/write|Ajouter ou modifier les coûts.|
|/labs/virtualNetworks/delete|Supprimer des réseaux virtuels.|
|/labs/virtualNetworks/read|Lire les réseaux virtuels.|
|/labs/virtualNetworks/write|Ajouter ou modifier des réseaux virtuels.|
|/labs/formulas/delete|Supprimer des formules.|
|/labs/formulas/read|Lire des formules.|
|/labs/formulas/write|Ajouter ou modifier des formules.|
|/labs/schedules/delete|Supprimer des planifications.|
|/labs/schedules/read|Lire les planifications.|
|/labs/schedules/write|Ajouter ou modifier des planifications.|
|/labs/schedules/Execute/action|Exécuter une planification.|
|/labs/schedules/ListApplicable/action|Répertorier toutes les planifications applicables.|
|/labs/galleryImages/read|Lire des images de la galerie.|
|/labs/policySets/EvaluatePolicies/action|Évaluer la stratégie de laboratoire.|
|/labs/policySets/policies/delete|Supprimer des stratégies.|
|/labs/policySets/policies/read|Lire les stratégies.|
|/labs/policySets/policies/write|Ajouter ou modifier des stratégies.|
|/labs/virtualMachines/delete|Supprimer des machines virtuelles.|
|/labs/virtualMachines/read|Lire les machines virtuelles.|
|/labs/virtualMachines/write|Ajouter ou modifier des machines virtuelles.|
|/labs/virtualMachines/Start/action|Démarrer une machine virtuelle.|
|/labs/virtualMachines/Stop/action|Arrêt d’une machine virtuelle|
|/labs/virtualMachines/ApplyArtifacts/action|Appliquer des artefacts à une machine virtuelle.|
|/labs/virtualMachines/AddDataDisk/action|Attacher un nouveau disque de données ou un disque de données existant à la machine virtuelle.|
|/labs/virtualMachines/DetachDataDisk/action|Détacher le disque spécifié de la machine virtuelle.|
|/labs/virtualMachines/Claim/action|Prendre possession d’une machine virtuelle existante.|
|/labs/virtualMachines/ListApplicableSchedules/action|Répertorier toutes les planifications applicables.|
|/labs/virtualMachines/schedules/delete|Supprimer des planifications.|
|/labs/virtualMachines/schedules/read|Lire les planifications.|
|/labs/virtualMachines/schedules/write|Ajouter ou modifier des planifications.|
|/labs/virtualMachines/schedules/Execute/action|Exécuter une planification.|
|/labs/notificationChannels/delete|Supprimer des canaux de notification.|
|/labs/notificationChannels/read|Lire les canaux de notification.|
|/labs/notificationChannels/write|Ajouter ou modifier des canaux de notification.|
|/labs/notificationChannels/Notify/action|Envoyer une notification au canal fourni.|
|/schedules/delete|Supprimer des planifications.|
|/schedules/read|Lire les planifications.|
|/schedules/write|Ajouter ou modifier des planifications.|
|/schedules/Execute/action|Exécuter une planification.|
|/schedules/Retarget/action|Mettre à jour l’ID de ressource cible d’une planification.|
|/locations/operations/read|Opérations de lecture.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Opération | Description |
|---|---|
|/databaseAccountNames/read|Vérifier la disponibilité du nom.|
|/databaseAccounts/read|Lire un compte de base de données.|
|/databaseAccounts/write|Mettre à jour les comptes de base de données.|
|/databaseAccounts/listKeys/action|Répertorier les clés d’un compte de base de données.|
|/databaseAccounts/regenerateKey/action|Régénérer les clés d’un compte de base de données.|
|/databaseAccounts/listConnectionStrings/action|Obtenir les chaînes de connexion d’un compte de base de données.|
|/databaseAccounts/changeResourceGroup/action|Modifier le groupe de ressources d’un compte de base de données.|
|/databaseAccounts/failoverPriorityChange/action|Modifier les priorités de basculement des régions pour un compte de base de données. Cela permet d’effectuer un basculement manuel.|
|/databaseAccounts/delete|Supprimer les comptes de base de données.|
|/databaseAccounts/metricDefinitions/read|Lire les définitions de mesures du compte de base de données.|
|/databaseAccounts/metrics/read|Lire les mesures du compte de base de données.|
|/databaseAccounts/usages/read|Lire les données d’utilisation du compte de base de données.|
|/databaseAccounts/databases/collections/metricDefinitions/read|Lire les définitions de mesures de la collection.|
|/databaseAccounts/databases/collections/metrics/read|Lire les mesures de la collection.|
|/databaseAccounts/databases/collections/usages/read|Lire les données d’utilisation de la collection.|
|/databaseAccounts/databases/metricDefinitions/read|Lire les définitions de mesures de la base de données.|
|/databaseAccounts/databases/metrics/read|Lire les mesures de la base de données.|
|/databaseAccounts/databases/usages/read|Lire les données d’utilisation de la base de données.|
|/databaseAccounts/readonlykeys/read|Lire les clés en lecture seule du compte de base de données.|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Opération | Description |
|---|---|
|/generateSsoRequest/Action|Générer une demande de connexion au centre de contrôle du domaine.|
|/validateDomainRegistrationInformation/Action|Valider l’objet d’achat de domaine sans le soumettre.|
|/checkDomainAvailability/Action|Vérifier si un domaine est disponible à l’achat.|
|/listDomainRecommendations/Action|Récupérer les recommandations de domaine en fonction de mots clés.|
|/register/action|Inscrire le fournisseur de ressources de domaines Microsoft pour l’abonnement.|
|/domains/Read|Obtenir la liste des domaines.|
|/domains/Write|Ajouter ou mettre à jour un domaine.|
|/domains/Delete|Supprimer un domaine existant.|
|/domains/operationresults/Read|Obtenir une opération de domaine.|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Opération | Description |
|---|---|
|/lcsprojects/read|Afficher les projets Microsoft Dynamics Lifecycle Services appartenant à un utilisateur|
|/lcsprojects/write|Créer et mettre à jour des projets Microsoft Dynamics Lifecycle Services appartenant à l’utilisateur. Seules les propriétés de nom et de description peuvent être mises à jour. L’abonnement et l’emplacement associés au projet ne peuvent pas être mis à jour après la création.|
|/lcsprojects/delete|Supprimer des projets Microsoft Dynamics Lifecycle Services appartenant à l’utilisateur|
|/lcsprojects/clouddeployments/read|Afficher les déploiements de test Microsoft Dynamics AX 2012 R3 dans un projet Microsoft Dynamics Lifecycle Services appartenant à un utilisateur|
|/lcsprojects/clouddeployments/write|Créer un déploiement de test Microsoft Dynamics AX 2012 R3 dans un projet Microsoft Dynamics Lifecycle Services appartenant à un utilisateur. Les déploiements peuvent être gérés depuis le portail de gestion Azure.|
|/lcsprojects/connectors/read|Lire les connecteurs appartenant à un projet Microsoft Dynamics Lifecycle Services|
|/lcsprojects/connectors/write|Créer et mettre à jour des connecteurs appartenant à un projet Microsoft Dynamics Lifecycle Services|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Opération | Description |
|---|---|
|/checkNameAvailability/action|Vérifier la disponibilité d’un espace de noms sous un abonnement donné|
|/register/action|Inscrire l’abonnement pour le fournisseur de ressources EventHub et activer la création de ressources EventHub|
|/namespaces/write|Créer une ressource Namespace et mettre à jour ses propriétés. Les balises et l’état de la ressource Namespace sont les propriétés qui peuvent être mises à jour.|
|/namespaces/read|Obtenir la liste des descriptions des ressources Namespace.|
|/namespaces/Delete|Supprimer une ressource Namespace|
|/namespaces/metricDefinitions/read|Obtenir la liste des descriptions de mesures des ressources Namespace|
|/namespaces/authorizationRules/read|Obtenir la liste des descriptions des règles d’autorisation des ressources Namespaces|
|/namespaces/authorizationRules/write|Créer des règles d’autorisation au niveau d’une ressource Namespace et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/namespaces/authorizationRules/delete|Supprimer une règle d’autorisation de ressource Namespace. La règle d’autorisation de ressource Namespace par défaut ne peut pas être supprimée. |
|/namespaces/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à la ressource Namespace|
|/namespaces/authorizationRules/regenerateKeys/action|Régénérer la clé primaire ou secondaire pour la ressource|
|/namespaces/eventhubs/write|Créer ou mettre à jour des propriétés EventHub|
|/namespaces/eventhubs/read|Obtenir la liste des descriptions de ressources EventHub|
|/namespaces/eventhubs/Delete|Supprimer des ressources EventHub|
|/namespaces/eventHubs/consumergroups/write|Créer ou mettre à jour les propriétés d’un groupe de consommateurs|
|/namespaces/eventHubs/consumergroups/read|Obtenir la liste des descriptions de ressources du groupe de consommateurs|
|/namespaces/eventHubs/consumergroups/Delete|Supprimer des ressources du groupe de consommateurs|
|/namespaces/eventhubs/authorizationRules/read| Obtenir la liste des règles d’autorisation EventHub|
|/namespaces/eventhubs/authorizationRules/write|Créer des règles d’autorisation EventHub et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/namespaces/eventhubs/authorizationRules/delete|Supprimer des règles d’autorisation EventHub|
|/namespaces/eventhubs/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à EventHub|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|Régénérer la clé primaire ou secondaire pour la ressource|
|/namespaces/diagnosticSettings/read|Obtenir la liste des descriptions des ressources des paramètres de diagnostics Namespace|
|/namespaces/diagnosticSettings/write|Obtenir la liste des descriptions des ressources des paramètres de diagnostics Namespace|
|/namespaces/logDefinitions/read|Obtenir la liste des descriptions des ressources des journaux Namespace|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Opération | Description |
|---|---|
|/providers/features/read|Afficher les fonctionnalités d’un abonnement pour un fournisseur de ressources donné|
|/providers/features/register/action|Enregistrer les fonctionnalités d’un abonnement pour un fournisseur de ressources donné|
|/features/read|Afficher les fonctionnalités d’un abonnement|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Opération | Description |
|---|---|
|/clusters/write|Créer ou mettre à jour un cluster HDInsight|
|/clusters/read|Obtenir des informations sur un cluster HDInsight|
|/clusters/delete|Supprimer un cluster HDInsight|
|/clusters/changerdpsetting/action|Modifier le paramètre RDP du cluster HDInsight|
|/clusters/configurations/action|Mettre à jour la configuration du cluster HDInsight|
|/clusters/configurations/read|Afficher les configurations du cluster HDInsight|
|/clusters/roles/resize/action|Redimensionner un cluster HDInsight|
|/locations/capabilities/read|Afficher les fonctionnalités de l’abonnement|
|/locations/checkNameAvailability/read|Vérifier la disponibilité du nom|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Opération | Description |
|---|---|
|/register/action|Registers the subscription for the import/export resource provider and enables the creation of import/export jobs.|
|/jobs/write|Crée un travail avec les paramètres spécifiés ou met à jour les propriétés ou les balises pour le travail spécifié.|
|/jobs/read|Affiche les propriétés du travail spécifié ou retourne la liste des travaux.|
|/jobs/listBitLockerKeys/action|Génère les clés BitLocker pour le travail spécifié.|
|/jobs/delete|Supprime un travail existant.|
|/locations/read|Affiche les propriétés de l’emplacement spécifié ou retourne la liste des emplacements.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Opération | Description |
|---|---|
|/Register/Action|Inscrire le fournisseur Microsoft.Insights|
|/AlertRules/Write|Écrire dans une configuration de règle d’alerte|
|/AlertRules/Delete|Supprimer une configuration de règle d’alerte|
|/AlertRules/Read|Lire une configuration de règle d’alerte|
|/AlertRules/Activated/Action|Règle d’alerte activée|
|/AlertRules/Resolved/Action|Règle d’alerte résolue|
|/AlertRules/Throttled/Action|Règle d’alerte limitée|
|/AlertRules/Incidents/Read|Lire une configuration d’incident de règle d’alerte|
|/MetricDefinitions/Read|Lire les définitions des mesures|
|/eventtypes/values/Read|Lire les valeurs de types d’événement de gestion|
|/eventtypes/digestevents/Read|Lire le résumé des types d’événement de gestion|
|/Metrics/Read|Lire des mesures|
|/LogProfiles/Write|Écrire dans une configuration de profil de journal|
|/LogProfiles/Delete|Supprimer la configuration de profil de journal|
|/LogProfiles/Read|Lire les profils de journal|
|/AutoscaleSettings/Write|Écrire dans une configuration de mise à l’échelle|
|/AutoscaleSettings/Delete|Supprimer une configuration de mise à l’échelle|
|/AutoscaleSettings/Read|Lire une configuration de mise à l’échelle|
|/AutoscaleSettings/Scaleup/Action|Effectuer une opération de montée en puissance|
|/AutoscaleSettings/Scaledown/Action|Effectuer une opération de descente en puissance|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|Lire les définitions des mesures|
|/ActivityLogAlerts/Activated/Action|Déclencher une alerte de journal d’activité|
|/DiagnosticSettings/Write|Écrire dans la configuration de diagnostic|
|/DiagnosticSettings/Delete|Supprimer la configuration de diagnostic|
|/DiagnosticSettings/Read|Lire la configuration de diagnostic|
|/LogDefinitions/Read|Lire les définitions de journal|
|/ExtendedDiagnosticSettings/Write|Écrire dans la configuration de diagnostic étendue|
|/ExtendedDiagnosticSettings/Delete|Supprimer la configuration de diagnostic étendue|
|/ExtendedDiagnosticSettings/Read|Lire une configuration de diagnostic étendue|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Opération | Description |
|---|---|
|/register/action|Enregistrer un abonnement|
|/checkNameAvailability/read|Vérifier que le nom du coffre Key Vault est valide et qu’il n’est pas déjà utilisé|
|/vaults/read|Afficher les propriétés d’un coffre Key Vault|
|/vaults/write|Créer un coffre Key Vault ou mettre à jour les propriétés d’un coffre Key Vault existant|
|/vaults/delete|Supprimer un coffre Key Vault|
|/vaults/deploy/action|Permettre l’accès aux clés secrètes dans un coffre Key Vault lors du déploiement de ressources Azure|
|/vaults/secrets/read|Afficher les propriétés d’une clé secrète, mais pas sa valeur|
|/vaults/secrets/write|Créer une nouvelle clé sécrète ou mettre à jour la valeur d’une clé secrète existante|
|/vaults/accessPolicies/write|Mettre à jour une stratégie d’accès existante via une fusion ou un remplacement, ou ajouter une nouvelle stratégie d’accès à un coffre|
|/deletedVaults/read|Afficher les propriétés de coffres Key Vault supprimés de manière réversible|
|/locations/operationResults/read|Vérifier le résultat d’une opération longue|
|/locations/deletedVaults/read|Afficher les propriétés d’un coffre Key Vault supprimé de manière réversible|
|/locations/deletedVaults/purge/action|Vider un coffre Key Vault supprimé de manière réversible|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Opération | Description |
|---|---|
|/workflows/read|Lit le workflow.|
|/workflows/write|Crée ou met à jour le workflow.|
|/workflows/delete|Supprime le workflow.|
|/workflows/run/action|Démarre l’exécution du workflow.|
|/workflows/disable/action|Désactive le workflow.|
|/workflows/enable/action|Active le workflow.|
|/workflows/validate/action|Valide le workflow.|
|/workflows/move/action|Déplace le workflow vers un ID d’abonnement, un groupe de ressources et/ou un nom différent.|
|/workflows/listSwagger/action|Obtient les définitions Swagger du workflow.|
|/workflows/regenerateAccessKey/action|Régénère les secrets de clé d’accès.|
|/workflows/listCallbackUrl/action|Obtient l’URL de rappel pour le workflow.|
|/workflows/versions/read|Lit la version du workflow.|
|/workflows/versions/triggers/listCallbackUrl/action|Obtient l’URL de rappel pour le déclencheur.|
|/workflows/runs/read|Lit l’exécution d’un workflow.|
|/workflows/runs/cancel/action|Annule l’exécution d’un workflow.|
|/workflows/runs/actions/read|Lit l’action d’exécution d’un workflow.|
|/workflows/runs/operations/read|Lit l’état d’exécution d’un workflow.|
|/workflows/triggers/read|Lit le déclencheur.|
|/workflows/triggers/run/action|Exécute le déclencheur.|
|/workflows/triggers/listCallbackUrl/action|Obtient l’URL de rappel pour le déclencheur.|
|/workflows/triggers/histories/read|Lit l’historique du déclencheur.|
|/workflows/triggers/histories/resubmit/action|Soumet à nouveau le déclencheur du workflow.|
|/workflows/accessKeys/read|Lit la clé d’accès.|
|/workflows/accessKeys/write|Crée ou met à jour la clé d’accès.|
|/workflows/accessKeys/delete|Supprime la clé d’accès.|
|/workflows/accessKeys/list/action|Répertorie les secrets de clé d’accès.|
|/workflows/accessKeys/regenerate/action|Régénère les secrets de clé d’accès.|
|/locations/workflows/validate/action|Valide le workflow.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Opération | Description |
|---|---|
|/register/action|Enregistrer l’abonnement pour le fournisseur de ressources du service web Machine Learning et activer la création de services web|
|/webServices/action|Créer des propriétés régionales de service web pour les régions prises en charge|
|/commitmentPlans/read|Lire un plan d’engagement Machine Learning|
|/commitmentPlans/write|Créer ou mettre à jour un plan d’engagement Machine Learning|
|/commitmentPlans/delete|Supprimer un plan d’engagement Machine Learning|
|/commitmentPlans/join/action|Rejoindre un plan d’engagement Machine Learning|
|/commitmentPlans/commitmentAssociations/read|Lire une association de plans d’engagement Machine Learning|
|/commitmentPlans/commitmentAssociations/move/action|Déplacer une association de plans d’engagement Machine Learning|
|/Workspaces/read|Afficher un espace de travail Machine Learning|
|/Workspaces/write|Créer ou mettre à jour un espace de travail Machine Learning|
|/Workspaces/delete|Supprimer un espace de travail Machine Learning|
|/Workspaces/listworkspacekeys/action|Répertorier les clés d’un espace de travail Machine Learning|
|/Workspaces/resyncstoragekeys/action|Resynchroniser les clés d’un compte de stockage configuré pour un espace de travail Machine Learning|
|/webServices/read|Afficher un service web Machine Learning|
|/webServices/write|Créer ou mettre à jour un service web Machine Learning|
|/webServices/delete|Supprimer un service web Machine Learning|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Opération | Description |
|---|---|
|/agreements/offers/plans/read|Renvoyer un contrat pour un élément de place de marché donné|
|/agreements/offers/plans/sign/action|Signer un contrat pour un élément de place de marché donné|
|/agreements/offers/plans/cancel/action|Annuler un contrat pour un élément de place de marché donné|

## <a name="microsoftmedia"></a>Microsoft.Media

| Opération | Description |
|---|---|
|/mediaservices/read||
|/mediaservices/write||
|/mediaservices/delete||
|/mediaservices/regenerateKey/action||
|/mediaservices/listKeys/action||
|/mediaservices/syncStorageKeys/action||

## <a name="microsoftnetwork"></a>Microsoft.Network

| Opération | Description |
|---|---|
|/register/action|Enregistrer l’abonnement.|
|/unregister/action|Désenregistrer l’abonnement.|
|/checkTrafficManagerNameAvailability/action|Vérifier la disponibilité d’un nom DNS relatif dans Traffic Manager.|
|/dnszones/read|Obtenir la zone DNS au format JSON. Exemples de propriétés de la zone : balises, etag, numberOfRecordSets et maxNumberOfRecordSets. Notez que cette commande ne récupère pas les jeux d’enregistrements contenus dans la zone.|
|/dnszones/write|Créer ou mettre à jour une zone DNS dans un groupe de ressources.  Permet de mettre à jour les balises dans une ressource de la zone DNS. Notez que cette commande ne peut pas être utilisée pour créer ou mettre à jour des jeux d’enregistrements au sein de la zone.|
|/dnszones/delete|Supprimer la zone DNS au format JSON. Exemples de propriétés de la zone : balises, etag, numberOfRecordSets et maxNumberOfRecordSets.|
|/dnszones/MX/read|Obtenir le jeu d’enregistrements de type « MX » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag.|
|/dnszones/MX/write|Créer ou mettre à jour un jeu d’enregistrements de type « MX » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements.|
|/dnszones/MX/delete|Supprimer le jeu d’enregistrements d’un nom donné et de type « MX » d’une zone DNS.|
|/dnszones/NS/read|Obtenir le jeu d’enregistrements DNS de type NS|
|/dnszones/NS/write|Créer ou mettre à jour un jeu d’enregistrements DNS de type NS|
|/dnszones/NS/delete|Supprimer le jeu d’enregistrements DNS de type NS|
|/dnszones/AAAA/read|Obtenir le jeu d’enregistrements de type « AAAA » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag.|
|/dnszones/AAAA/write|Créer ou mettre à jour un jeu d’enregistrements de type « AAAA » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements.|
|/dnszones/AAAA/delete|Supprimer le jeu d’enregistrements d’un nom donné et de type « AAAA » d’une zone DNS.|
|/dnszones/CNAME/read|Obtenir le jeu d’enregistrements de type « CNAME » au format JSON. Le jeu d’enregistrements contient la durée de vie, les balises et l’etag.|
|/dnszones/CNAME/write|Créer ou mettre à jour un jeu d’enregistrements de type « CNAME » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements.|
|/dnszones/CNAME/delete|Supprimer le jeu d’enregistrements d’un nom donné et de type « CNAME » d’une zone DNS.|
|/dnszones/SOA/read|Obtenir le jeu d’enregistrements DNS de type SOA|
|/dnszones/SOA/write|Créer ou mettre à jour un jeu d’enregistrements DNS de type SOA|
|/dnszones/SRV/read|Obtenir le jeu d’enregistrements de type « SRV » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag.|
|/dnszones/SRV/write|Créer ou mettre à jour un jeu d’enregistrements de type SRV|
|/dnszones/SRV/delete|Supprimer le jeu d’enregistrements d’un nom donné et de type « SRV » d’une zone DNS.|
|/dnszones/PTR/read|Obtenir le jeu d’enregistrements de type « PTR » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag.|
|/dnszones/PTR/write|Créer ou mettre à jour un jeu d’enregistrements de type « PTR » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements.|
|/dnszones/PTR/delete|Supprimer le jeu d’enregistrements d’un nom donné et de type « PTR » d’une zone DNS.|
|/dnszones/A/read|Obtenir le jeu d’enregistrements de type « A » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag.|
|/dnszones/A/write|Créer ou mettre à jour un jeu d’enregistrements de type « A » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements.|
|/dnszones/A/delete|Supprimer le jeu d’enregistrements d’un nom donné et de type « A » d’une zone DNS.|
|/dnszones/TXT/read|Obtenir le jeu d’enregistrements de type « TXT », au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag.|
|/dnszones/TXT/write|Créer ou mettre à jour un jeu d’enregistrements de type « TXT » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements.|
|/dnszones/TXT/delete|Supprimer le jeu d’enregistrements d’un nom donné et de type « TXT » d’une zone DNS.|
|/dnszones/recordsets/read|Obtient les jeux d’enregistrements DNS parmi les types.|
|/networkInterfaces/read|Obtient une définition d’interface réseau. |
|/networkInterfaces/write|Crée une interface réseau ou met à jour une interface réseau existante. |
|/networkInterfaces/join/action|Joint une machine virtuelle à une interface réseau.|
|/networkInterfaces/delete|Supprime une interface réseau.|
|/networkInterfaces/effectiveRouteTable/action|Obtenir la table de routage configurée sur l’interface réseau de la machine virtuelle.|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|Obtenir les groupes de sécurité réseau configurés sur l’interface réseau de la machine virtuelle.|
|/networkInterfaces/loadBalancers/read|Obtient tous les équilibrages de charge auxquels l’interface réseau appartient.|
|/networkInterfaces/ipconfigurations/read|Obtient une définition de la configuration de l’adresse IP de l’interface réseau. |
|/publicIPAddresses/read|Obtient une définition de l’adresse IP publique.|
|/publicIPAddresses/write|Crée une adresse IP publique ou met à jour une adresse IP publique existante. |
|/publicIPAddresses/delete|Supprime une adresse IP publique.|
|/publicIPAddresses/join/action|Joint une adresse IP publique.|
|/routeFilters/read|Obtient une définition de filtre de routage.|
|/routeFilters/join/action|Joint un filtre de routage.|
|/routeFilters/delete|Supprime une définition de filtre de routage.|
|/routeFilters/write|Crée un filtre de routage ou met à jour un filtre de routage existant.|
|/routeFilters/rules/read|Obtient une définition de règle de filtre de routage.|
|/routeFilters/rules/write|Crée une règle de filtre de routage ou met à jour une règle de filtre de routage existante.|
|/routeFilters/rules/delete|Supprime une définition de règle de filtre de routage.|
|/networkWatchers/read|Obtient la définition d’un observateur de réseau.|
|/networkWatchers/write|Crée un observateur de réseau ou met à jour un observateur de réseau existant.|
|/networkWatchers/delete|Supprime un observateur de réseau.|
|/networkWatchers/configureFlowLog/action|Configure la journalisation des flux pour une ressource cible.|
|/networkWatchers/ipFlowVerify/action|Indique si le paquet est autorisé ou refusé vers ou depuis une destination particulière.|
|/networkWatchers/nextHop/action|Pour une adresse IP de destination et une cible spécifiées, indique le type de saut suivant et l’adresse IP de saut suivant.|
|/networkWatchers/queryFlowLogStatus/action|Obtient l’état de la journalisation des flux sur une ressource.|
|/networkWatchers/queryTroubleshootResult/action|Obtient le résultat de la résolution des problèmes à partir de l’opération de résolution des problèmes en cours d’exécution ou exécutée précédemment.|
|/networkWatchers/securityGroupView/action|Afficher les règles de groupe de sécurité réseau configurées et en vigueur appliquées à une machine virtuelle.|
|/networkWatchers/topology/action|Obtient une vue au niveau du réseau des ressources et de leurs relations dans un groupe de ressources.|
|/networkWatchers/troubleshoot/action|Démarre la résolution des problèmes sur une ressource réseau dans Azure.|
|/networkWatchers/packetCaptures/queryStatus/action|Obtient des informations sur les propriétés et l’état d’une ressource de capture de paquets.|
|/networkWatchers/packetCaptures/stop/action|Arrêter la session de capture de paquets en cours d’exécution.|
|/networkWatchers/packetCaptures/read|Obtenir la définition de la capture de paquets.|
|/networkWatchers/packetCaptures/write|Crée une capture de paquets.|
|/networkWatchers/packetCaptures/delete|Supprimer une capture de paquets.|
|/loadBalancers/read|Obtient une définition d’équilibrage de charge.|
|/loadBalancers/write|Crée un équilibrage de charge ou met à jour un équilibrage de charge existant.|
|/loadBalancers/delete|Supprime un équilibrage de charge.|
|/loadBalancers/networkInterfaces/read|Obtient les références à toutes les interfaces réseau sous un équilibrage de charge.|
|/loadBalancers/loadBalancingRules/read|Obtient une définition de règle d’équilibrage de charge.|
|/loadBalancers/backendAddressPools/read|Obtient une définition de pool d’adresses principales d’équilibrage de charge.|
|/loadBalancers/backendAddressPools/join/action|Joint un pool d’adresses principales d’équilibrage de charge.|
|/loadBalancers/inboundNatPools/read|Obtient une définition de pool NAT entrant d’équilibrage de charge.|
|/loadBalancers/inboundNatPools/join/action|Joint un pool NAT entrant d’équilibrage de charge.|
|/loadBalancers/inboundNatRules/read|Obtient une définition de règle NAT entrante d’équilibrage de charge.|
|/loadBalancers/inboundNatRules/write|Crée une règle NAT entrante d’équilibrage de charge ou met à jour une règle NAT entrante d’équilibrage de charge existante.|
|/loadBalancers/inboundNatRules/delete|Supprime une règle NAT entrante d’équilibrage de charge.|
|/loadBalancers/inboundNatRules/join/action|Joint une règle NAT entrante d’équilibrage de charge.|
|/loadBalancers/outboundNatRules/read|Obtient une définition de règle NAT sortante d’équilibrage de charge.|
|/loadBalancers/probes/read|Obtient une sonde d’équilibrage de charge.|
|/loadBalancers/virtualMachines/read|Obtient les références à toutes les machines virtuelles sous un équilibrage de charge.|
|/loadBalancers/frontendIPConfigurations/read|Obtient une définition de configuration d’adresse IP frontale d’équilibrage de charge.|
|/trafficManagerGeographicHierarchies/read|Obtient la hiérarchie géographique Traffic Manager contenant des régions qui peuvent être utilisées dans la méthode de routage du trafic géographique.|
|/bgpServiceCommunities/read|Obtenir les communautés de services BGP.|
|/applicationGatewayAvailableWafRuleSets/read|Obtient les ensembles de règles WAF disponibles pour Application Gateway.|
|/virtualNetworks/read|Obtenir la définition de réseau virtuel.|
|/virtualNetworks/write|Crée un réseau virtuel ou met à jour un réseau virtuel existant.|
|/virtualNetworks/delete|Supprime un réseau virtuel.|
|/virtualNetworks/peer/action|Appaire un réseau virtuel avec un autre réseau virtuel.|
|/virtualNetworks/virtualNetworkPeerings/read|Obtient une définition d’homologation de réseau virtuel.|
|/virtualNetworks/virtualNetworkPeerings/write|Crée une homologation de réseau virtuel ou met à jour une homologation de réseau virtuel existante.|
|/virtualNetworks/virtualNetworkPeerings/delete|Supprime une homologation de réseau virtuel.|
|/virtualNetworks/subnets/read|Obtient une définition de sous-réseau de réseau virtuel.|
|/virtualNetworks/subnets/write|Crée un sous-réseau de réseau virtuel ou met à jour un sous-réseau de réseau virtuel existant.|
|/virtualNetworks/subnets/delete|Supprime un sous-réseau de réseau virtuel.|
|/virtualNetworks/subnets/join/action|Joint un réseau virtuel.|
|/virtualNetworks/subnets/joinViaServiceTunnel/action|Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau avec tunneling de service activé.|
|/virtualNetworks/subnets/virtualMachines/read|Obtient les références à toutes les machines virtuelles dans un sous-réseau de réseau virtuel.|
|/virtualNetworks/checkIpAddressAvailability/read|Vérifier si l’adresse IP est disponible sur le réseau virtuel spécifié.|
|/virtualNetworks/virtualMachines/read|Obtient les références à toutes les machines virtuelles dans un réseau virtuel.|
|/expressRouteServiceProviders/read|Obtient les fournisseurs de services ExpressRoute.|
|/dnsoperationresults/read|Obtient les résultats d’une opération DNS.|
|/localnetworkgateways/read|Obtient une LocalNetworkGateway.|
|/localnetworkgateways/write|Crée une LocalNetworkGateway ou met à jour une LocalNetworkGateway existante.|
|/localnetworkgateways/delete|Supprime une LocalNetworkGateway.|
|/trafficManagerProfiles/read|Obtient la configuration du profil Traffic Manager. Cela inclut les paramètres DNS, les paramètres de routage du trafic, les paramètres de surveillance des points de terminaison et la liste des points de terminaison acheminés par ce profil Traffic Manager.|
|/trafficManagerProfiles/write|Créer un profil Traffic Manager ou modifier la configuration d’un profil Traffic Manager existant. Cela inclut l’activation ou la désactivation d’un profil et la modification des paramètres DNS, des paramètres de routage du trafic ou des paramètres de surveillance des points de terminaison. Les points de terminaison acheminés par le profil Traffic Manager peuvent être ajoutés, supprimés, activés ou désactivés.|
|/trafficManagerProfiles/delete|Supprimer le profil Traffic Manager. Tous les paramètres associés au profil Traffic Manager seront perdus et le profil ne pourra plus être utilisé pour acheminer le trafic.|
|/dnsoperationstatuses/read|Obtient l’état d’une opération DNS. |
|/operations/read|Obtenir les opérations disponibles.|
|/expressRouteCircuits/read|Obtenir un ExpressRouteCircuit.|
|/expressRouteCircuits/write|Crée un ExpressRouteCircuit ou met à jour un ExpressRouteCircuit existant.|
|/expressRouteCircuits/delete|Supprime un ExpressRouteCircuit.|
|/expressRouteCircuits/stats/read|Obtient les statistiques d’un ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/read|Obtient une homologation ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/write|Crée une homologation ExpressRouteCircuit ou met à jour une homologation ExpressRouteCircuit existante.|
|/expressRouteCircuits/peerings/delete|Supprime une homologation ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/arpTables/action|Obtient une ArpTable d’homologation ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/routeTables/action|Obtient une RouteTable d’homologation ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/<br>routeTablesSummary/action|Obtient un résumé RouteTable d’homologation ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/stats/read|Obtient les statistiques d’homologation d’un ExpressRouteCircuit.|
|/expressRouteCircuits/authorizations/read|Obtient une autorisation ExpressRouteCircuit.|
|/expressRouteCircuits/authorizations/write|Crée une autorisation ExpressRouteCircuit ou met à jour une autorisation ExpressRouteCircuit existante.|
|/expressRouteCircuits/authorizations/delete|Supprime une autorisation ExpressRouteCircuit.|
|/connections/read|Obtient une VirtualNetworkGatewayConnection.|
|/connections/write|Crée une VirtualNetworkGatewayConnection ou met à jour une VirtualNetworkGatewayConnection existante.|
|/connections/delete|Supprime une VirtualNetworkGatewayConnection.|
|/connections/sharedKey/read|Obtient une SharedKey VirtualNetworkGatewayConnection.|
|/connections/sharedKey/write|Crée une SharedKey VirtualNetworkGatewayConnection ou met à jour une SharedKey VirtualNetworkGatewayConnection existante.|
|/networkSecurityGroups/read|Obtient une définition de groupe de sécurité réseau.|
|/networkSecurityGroups/write|Crée un groupe de sécurité réseau ou met à jour un groupe de sécurité réseau existant.|
|/networkSecurityGroups/delete|Supprime un groupe de sécurité réseau.|
|/networkSecurityGroups/join/action|Joint un groupe de sécurité réseau.|
|/networkSecurityGroups/defaultSecurityRules/read|Obtient une définition de règle de sécurité par défaut.|
|/networkSecurityGroups/securityRules/read|Obtient une définition de règle de sécurité.|
|/networkSecurityGroups/securityRules/write|Crée une règle de sécurité ou met à jour une règle de sécurité existante.|
|/networkSecurityGroups/securityRules/delete|Supprime une règle de sécurité.|
|/applicationGateways/read|Obtient une passerelle d’application.|
|/applicationGateways/write|Crée une passerelle d’application ou met à jour une passerelle d’application.|
|/applicationGateways/delete|Supprime une passerelle d’application.|
|/applicationGateways/backendhealth/action|Obtient l’intégrité du serveur principal d’une passerelle d’application.|
|/applicationGateways/start/action|Démarre une passerelle d’application.|
|/applicationGateways/stop/action|Arrête une passerelle d’application.|
|/applicationGateways/backendAddressPools/join/action|Joint un pool d’adresses principales de passerelle d’application.|
|/routeTables/read|Obtient une définition de table de routage.|
|/routeTables/write|Crée une table de routage ou met à jour une table de routage existante.|
|/routeTables/delete|Supprime une définition de table de routage.|
|/routeTables/join/action|Joint une table de routage.|
|/routeTables/routes/read|Obtient une définition de routage.|
|/routeTables/routes/write|Crée un routage ou met à jour un routage existant.|
|/routeTables/routes/delete|Supprime une définition de routage.|
|/locations/operationResults/read|Obtient le résultat d’une opération DELETE ou POST asynchrone.|
|/locations/checkDnsNameAvailability/read|Vérifie si le nom DNS est disponible à l’emplacement spécifié.|
|/locations/usages/read|Obtient les mesures d’utilisation des ressources.|
|/locations/operations/read|Obtient la ressource d’opération qui représente l’état d’une opération asynchrone.|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Opération | Description |
|---|---|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources NotificationHubs et active la création de ressources Namespaces et NotificationHubs.|
|/CheckNamespaceAvailability/action|Vérifie si un nom de ressource Namespace donné est disponible dans le service NotificationHub.|
|/Namespaces/write|Créer une ressource Namespace et mettre à jour ses propriétés. Les balises et l’état de la ressource Namespace sont les propriétés qui peuvent être mises à jour.|
|/Namespaces/read|Obtenir la liste des descriptions des ressources Namespace.|
|/Namespaces/Delete|Supprimer une ressource Namespace.|
|/Namespaces/authorizationRules/action|Obtenir la liste des descriptions des règles d’autorisation des ressources Namespaces.|
|/Namespaces/CheckNotificationHubAvailability/action|Vérifie si un nom de NotificationHub donné est disponible à l’intérieur d’une ressource Namespace.|
|/Namespaces/authorizationRules/write|Créer des règles d’autorisation au niveau d’une ressource Namespace et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/Namespaces/authorizationRules/read|Obtenir la liste des descriptions des règles d’autorisation des ressources Namespaces.|
|/Namespaces/authorizationRules/delete|Supprimer une règle d’autorisation de ressource Namespace. La règle d’autorisation de ressource Namespace par défaut ne peut pas être supprimée. |
|/Namespaces/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à la ressource Namespace.|
|/Namespaces/authorizationRules/regenerateKeys/action|Règle d’autorisation de ressource Namespace pour régénérer les clés primaires et secondaires ; spécifier la clé qui doit être régénérée.|
|/Namespaces/NotificationHubs/write|Créer un concentrateur de notification et mettre à jour ses propriétés. Ses propriétés incluent principalement des informations d’identification PNS. Durée de vie et règles d’autorisation|
|/Namespaces/NotificationHubs/read|Obtenir la liste des descriptions des ressources de concentrateur de notification.|
|/Namespaces/NotificationHubs/Delete|Supprimer une ressource de concentrateur de notification.|
|/Namespaces/NotificationHubs/authorizationRules/action|Obtenir la liste des règles d’autorisation de concentrateur de notification.|
|/Namespaces/NotificationHubs/pnsCredentials/action|Obtenir toutes les informations d’identification PNS du concentrateur de notification. Cela inclut les informations d’identification WNS, MPNS, APNS, GCM et BAIDU.|
|/Namespaces/NotificationHubs/debugSend/action|Envoyer une notification Push de test.|
|/Namespaces/NotificationHubs/metricDefinitions/read|Obtenir la liste des descriptions des mesures de ressource Namespace.|
|/Namespaces/NotificationHubs/<br>authorizationRules/write|Créer des règles d’autorisation de concentrateur de notification et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/Namespaces/NotificationHubs/<br>authorizationRules/read|Obtenir la liste des règles d’autorisation de concentrateur de notification.|
|/Namespaces/NotificationHubs/<br>authorizationRules/delete|Supprimer les règles d’autorisation de concentrateur de notification.|
|/Namespaces/NotificationHubs/<br>authorizationRules/listkeys/action|Obtenir la chaîne de connexion au concentrateur de notification.|
|/Namespaces/NotificationHubs/<br>authorizationRules/regenerateKeys/action|Règle d’autorisation de concentrateur de notification pour régénérer les clés primaires et secondaires ; spécifier la clé qui doit être régénérée.|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Opération | Description |
|---|---|
|/register/action|Inscrire un abonnement à un fournisseur de ressources.|
|/linkTargets/read|Répertorie les comptes existants qui ne sont pas associés à un abonnement Azure. Pour lier cet abonnement Azure à un espace de travail, utilisez un ID de client renvoyé par cette opération dans la propriété d’ID de client de l’opération de création d’un espace de travail.|
|/workspaces/write|Crée un espace de travail ou lie un espace de travail existant en fournissant l’ID de client à partir de l’espace de travail existant.|
|/workspaces/read|Obtient un espace de travail existant.|
|/workspaces/delete|Supprime un espace de travail. Si l’espace de travail a été lié à un espace de travail existant au moment de la création, l’espace de travail auquel il a été lié n’est pas supprimé.|
|/workspaces/generateregistrationcertificate/action|Génère le certificat d’inscription de l’espace de travail. Ce certificat est utilisé pour connecter Microsoft System Center Operation Manager à l’espace de travail.|
|/workspaces/sharedKeys/action|Récupère les clés partagées de l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail.|
|/workspaces/search/action|Exécute une requête de recherche.|
|/workspaces/datasources/read|Obtenir des sources de données sous un espace de travail.|
|/workspaces/datasources/write|Créer/mettre à jour des sources de données sous un espace de travail.|
|/workspaces/datasources/delete|Supprimer des sources de données sous un espace de travail.|
|/workspaces/managementGroups/read|Obtient les noms et les métadonnées des groupes d’administration System Center Operations Manager connectés à cet espace de travail.|
|/workspaces/schema/read|Obtient le schéma de recherche de l’espace de travail.  Le schéma de recherche inclut les champs exposés et leurs types.|
|/workspaces/usages/read|Obtient les données d’utilisation d’un espace de travail, y compris la quantité de données lues par l’espace de travail.|
|/workspaces/intelligencepacks/read|Répertorie tous les Intelligence Packs visibles pour un espace de travail donné et répertorie également si le pack est activé ou désactivé pour cet espace de travail.|
|/workspaces/intelligencepacks/enable/action|Active un Intelligence Pack pour un espace de travail donné.|
|/workspaces/intelligencepacks/disable/action|Désactive un Intelligence Pack pour un espace de travail donné.|
|/workspaces/sharedKeys/read|Récupère les clés partagées de l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail.|
|/workspaces/savedSearches/read|Obtient une requête de recherche enregistrée.|
|/workspaces/savedSearches/write|Crée une requête de recherche enregistrée.|
|/workspaces/savedSearches/delete|Supprime une requête de recherche enregistrée.|
|/workspaces/storageinsightconfigs/write|Crée une configuration de stockage. Ces configurations sont utilisées pour extraire des données à partir d’un emplacement dans un compte de stockage existant.|
|/workspaces/storageinsightconfigs/read|Obtient une configuration de stockage.|
|/workspaces/storageinsightconfigs/delete|Supprime une configuration de stockage. Cela empêchera Microsoft Operational Insights de lire des données du compte de stockage.|
|/workspaces/configurationScopes/read|Obtenir l’étendue de la configuration.|
|/workspaces/configurationScopes/write|Définir l’étendue de la configuration.|
|/workspaces/configurationScopes/delete|Supprimer l’étendue de la configuration.|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Opération | Description |
|---|---|
|/register/action|Inscrire un abonnement à un fournisseur de ressources.|
|/solutions/write|Créer une solution OMS.|
|/solutions/read|Obtenir la solution OMS existante.|
|/solutions/delete|Supprimer la solution OMS existante.|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Opération | Description |
|---|---|
|/Vaults/backupJobsExport/action|Travaux d’exportation|
|/Vaults/write|L’opération de création de coffre entraîne la création d’une ressource Azure de type « coffre».|
|/Vaults/read|L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ».|
|/Vaults/delete|L’opération de suppression de coffre supprime la ressource Azure de type « coffre » spécifiée.|
|/Vaults/refreshContainers/read|Actualise la liste de conteneurs.|
|/Vaults/backupJobsExport/operationResults/read|Renvoie le résultat de l’opération de travail d’exportation.|
|/Vaults/backupOperationResults/read|Renvoie le résultat de l’opération de sauvegarde pour le coffre Recovery Services.|
|/Vaults/monitoringAlerts/read|Obtient les alertes pour le coffre Recovery Services.|
|/Vaults/monitoringAlerts/{uniqueAlertId}/read|Obtient les détails de l’alerte.|
|/Vaults/backupSecurityPIN/read|Renvoie les informations relatives au code PIN de sécurité pour le coffre Recovery Services.|
|/vaults/replicationEvents/read|Lire des événements.|
|/Vaults/backupProtectableItems/read|Renvoie la liste de tous les éléments pouvant être protégés.|
|/vaults/replicationFabrics/read|Lire des structures.|
|/vaults/replicationFabrics/write|Créer ou mettre à jour des structures.|
|/vaults/replicationFabrics/remove/action|Supprimer une structure.|
|/vaults/replicationFabrics/checkConsistency/action|Vérifie la cohérence de la structure.|
|/vaults/replicationFabrics/delete|Supprimer des structures.|
|/vaults/replicationFabrics/renewcertificate/action||
|/vaults/replicationFabrics/deployProcessServerImage/action|Déployer une image de serveur de traitement.|
|/vaults/replicationFabrics/reassociateGateway/action|Réassocier une passerelle.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>read|Lire des fournisseurs Recovery Services.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>remove/action|Supprimer un fournisseur Recovery Services.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>delete|Supprimer des fournisseurs Recovery Services.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>refreshProvider/action|Actualiser un fournisseur.|
|/vaults/replicationFabrics/replicationStorageClassifications/read|Lire des classifications de stockage.|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/read|Lire des mappages de classifications de stockage.|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/write|Créer ou mettre à jour des mappages de classifications de stockage.|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/delete|Supprimer des mappages de classifications de stockage.|
|/vaults/replicationFabrics/replicationvCenters/read|Lire des travaux.|
|/vaults/replicationFabrics/replicationvCenters/write|Créer ou mettre à jour des travaux.|
|/vaults/replicationFabrics/replicationvCenters/delete|Supprimer des travaux.|
|/vaults/replicationFabrics/replicationNetworks/read|Lire des réseaux.|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/read|Lire des mappages de réseau.|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/write|Créer ou mettre à jour des mappages de réseau.|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/delete|Supprimer des mappages de réseau.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>read|Lire des conteneurs de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>discoverProtectableItem/action|Découvrir un élément pouvant être protégé.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>write|Créer ou mettre à jour des conteneurs de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>remove/action|Supprimer un conteneur de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>switchprotection/action|Basculer un conteneur de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectableItems/read|Lire des éléments pouvant être protégés.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/read|Lire des mappages de conteneurs de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/write|Créer ou mettre à jour des mappages de conteneurs de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/remove/action|Supprimer un mappage de conteneurs de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/delete|Supprimer des mappages de conteneurs de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/read|Lire des éléments protégés.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/write|Créer ou mettre à jour des éléments protégés.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/delete|Supprimer des éléments protégés.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/remove/action|Supprimer un élément protégé.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/plannedFailover/action|Basculement planifié.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/unplannedFailover/action|Basculement|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailover/action|Test Failover|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailoverCleanup/action|Nettoyage de basculement test.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/failoverCommit/action|Validation du basculement.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/reProtect/action|Reprotéger l’élément protégé.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/updateMobilityService/action|Mettre à jour le service Mobilité.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/repairReplication/action|Réparer la réplication.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/applyRecoveryPoint/action|Appliquer un point de récupération.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/recoveryPoints/read|Lire des points de récupération de réplication.|
|/vaults/replicationPolicies/read|Lire des stratégies.|
|/vaults/replicationPolicies/write|Créer ou mettre à jour des stratégies.|
|/vaults/replicationPolicies/delete|Supprimer des stratégies.|
|/vaults/replicationRecoveryPlans/read|Lire des plans de récupération.|
|/vaults/replicationRecoveryPlans/write|Créer ou mettre à jour des plans de récupération.|
|/vaults/replicationRecoveryPlans/delete|Supprimer des plans de récupération.|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Plan de récupération de basculement planifié.|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Plan de récupération de basculement.|
|/vaults/replicationRecoveryPlans/testFailover/action|Plan de récupération de basculement test.|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Plan de récupération de nettoyage de basculement test.|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Plan de récupération de validation de basculement.|
|/vaults/replicationRecoveryPlans/reProtect/action|Reprotéger le plan de récupération.|
|/Vaults/extendedInformation/read|L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ».|
|/Vaults/extendedInformation/write|L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ».|
|/Vaults/extendedInformation/delete|L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ».|
|/Vaults/backupManagementMetaData/read|Renvoie les métadonnées de gestion des sauvegardes pour le coffre Recovery Services.|
|/Vaults/backupProtectionContainers/read|Renvoie tous les conteneurs appartenant à l’abonnement.|
|/Vaults/backupFabrics/operationResults/read|Renvoie l’état de l’opération.|
|/Vaults/backupFabrics/protectionContainers/read|Renvoie tous les conteneurs inscrits.|
|/Vaults/backupFabrics/protectionContainers/<br>operationResults/read|Obtient les résultats de l’opération effectuée sur le conteneur de protection.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/read|Renvoie des détails d’objet de l’élément protégé.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/write|Créer un élément protégé de sauvegarde.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/delete|Supprime un élément protégé.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/backup/action|Effectue la sauvegarde d’un élément protégé.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationResults/read|Obtient les résultats de l’opération effectuée sur les éléments protégés.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationStatus/read|Renvoie l’état de l’opération effectuée sur les éléments protégés.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/read|Obtenir les points de récupération des éléments protégés.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>restore/action|Restaurer les points de récupération des éléments protégés.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>provisionInstantItemRecovery/action|Approvisionner la récupération d’éléments instantanée pour l’élément protégé.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>revokeInstantItemRecovery/action|Révoquer la récupération d’éléments instantanée pour l’élément protégé.|
|/Vaults/usages/read|Renvoie des détails d’utilisation d’un coffre Recovery Services.|
|/vaults/usages/read|Lire des utilisations de coffre.|
|/Vaults/certificates/write|L’opération de mise à jour de certificat de ressource met à jour le certificat d’identification du coffre/de la ressource.|
|/Vaults/tokenInfo/read|Renvoie des informations de jeton pour le coffre Recovery Services.|
|/vaults/replicationAlertSettings/read|Lire des paramètres d’alertes.|
|/vaults/replicationAlertSettings/write|Créer ou mettre à jour des paramètres d’alertes.|
|/Vaults/backupOperations/read|Renvoie l’état de l’opération de sauvegarde pour le coffre Recovery Services.|
|/Vaults/storageConfig/read|Renvoie la configuration de stockage pour le coffre Recovery Services.|
|/Vaults/storageConfig/write|Met à jour la configuration de stockage pour le coffre Recovery Services.|
|/Vaults/backupUsageSummaries/read|Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services.|
|/Vaults/backupProtectedItems/read|Renvoie la liste de tous les éléments protégés.|
|/Vaults/backupconfig/vaultconfig/read|Renvoie la configuration pour le coffre Recovery Services.|
|/Vaults/backupconfig/vaultconfig/write|Met à jour la configuration pour le coffre Recovery Services.|
|/Vaults/registeredIdentities/write|L’opération d’inscription d’un conteneur de service peut être utilisée pour inscrire un conteneur avec Recovery Services.|
|/Vaults/registeredIdentities/read|L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource.|
|/Vaults/registeredIdentities/delete|L’opération d’annulation de l’inscription d’un conteneur peut être utilisée pour annuler l’inscription d’un conteneur.|
|/Vaults/registeredIdentities/operationResults/read|L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone.|
|/vaults/replicationJobs/read|Lire des travaux.|
|/vaults/replicationJobs/cancel/action|Annuler un travail.|
|/vaults/replicationJobs/restart/action|Redémarrer un travail.|
|/vaults/replicationJobs/resume/action|Reprendre un travail.|
|/Vaults/backupPolicies/read|Renvoie toutes les stratégies de protection.|
|/Vaults/backupPolicies/write|Crée une stratégie de protection.|
|/Vaults/backupPolicies/delete|Supprimer une stratégie de protection.|
|/Vaults/backupPolicies/operationResults/read|Obtenir les résultats de l’opération de stratégie.|
|/Vaults/backupPolicies/operationStatus/read|Obtenir l’état de l’opération de stratégie.|
|/Vaults/vaultTokens/read|L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre.|
|/Vaults/monitoringConfigurations/notificationConfiguration/read|Obtient la configuration de notification du coffre Recovery Services.|
|/Vaults/backupJobs/read|Renvoie tous les objets de travail.|
|/Vaults/backupJobs/cancel/action|Annuler le travail.|
|/Vaults/backupJobs/operationResults/read|Renvoie le résultat de l’opération de travail.|
|/locations/allocateStamp/action|AllocateStamp est une opération interne utilisée par le service.|
|/locations/allocatedStamp/read|GetAllocatedStamp est une opération interne utilisée par le service.|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Opération | Description |
|---|---|
|/checkNamespaceAvailability/action|Vérifie la disponibilité d’un espace de noms sous un abonnement donné.|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources Relay et active la création de ressources Relay.|
|/namespaces/write|Créer une ressource Namespace et mettre à jour ses propriétés. Les balises et l’état de la ressource Namespace sont les propriétés qui peuvent être mises à jour.|
|/namespaces/read|Obtenir la liste des descriptions des ressources Namespace.|
|/namespaces/Delete|Supprimer une ressource Namespace.|
|/namespaces/authorizationRules/write|Créer des règles d’autorisation au niveau d’une ressource Namespace et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/namespaces/authorizationRules/delete|Supprimer une règle d’autorisation de ressource Namespace. La règle d’autorisation de ressource Namespace par défaut ne peut pas être supprimée. |
|/namespaces/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à la ressource Namespace.|
|/namespaces/HybridConnections/write|Créer ou mettre à jour des propriétés HybridConnection.|
|/namespaces/HybridConnections/read|Obtenir la liste des descriptions des ressources HybridConnection.|
|/namespaces/HybridConnections/Delete|Opération de suppression des ressources HybridConnection.|
|/namespaces/HybridConnections/authorizationRules/write|Créer des règles d’autorisation HybridConnection et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/namespaces/HybridConnections/authorizationRules/delete|Opération de suppression des règles d’autorisation HybridConnection.|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à HybridConnection.|
|/namespaces/WcfRelays/write|Créer ou mettre à jour des propriétés WcfRelay.|
|/namespaces/WcfRelays/read|Obtenir la liste des descriptions des ressources WcfRelay.|
|/namespaces/WcfRelays/Delete|Opération de suppression des ressources WcfRelay.|
|/namespaces/WcfRelays/authorizationRules/write|Créer des règles d’autorisation WcfRelay et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/namespaces/WcfRelays/authorizationRules/delete|Opération de suppression des règles d’autorisation WcfRelay.|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à WcfRelay.|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Opération | Description |
|---|---|
|/AvailabilityStatuses/read|Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée.|
|/AvailabilityStatuses/current/read|Obtient l’état de disponibilité de la ressource spécifiée.|

## <a name="microsoftresources"></a>Microsoft.Resources

| Opération | Description |
|---|---|
|/checkResourceName/action|Vérifier la validité du nom de la ressource.|
|/providers/read|Obtient la liste des fournisseurs.|
|/subscriptions/read|Obtient la liste des abonnements.|
|/subscriptions/operationresults/read|Obtenir les résultats de l’opération de l’abonnement.|
|/subscriptions/providers/read|Obtient ou répertorie les fournisseurs de ressources.|
|/subscriptions/tagNames/read|Obtient ou répertorie les balises d’abonnement.|
|/subscriptions/tagNames/write|Ajoute une balise d’abonnement.|
|/subscriptions/tagNames/delete|Supprime une balise d’abonnement.|
|/subscriptions/tagNames/tagValues/read|Obtient ou répertorie les valeurs des balises d’abonnement.|
|/subscriptions/tagNames/tagValues/write|Ajoute une valeur de balise d’abonnement.|
|/subscriptions/tagNames/tagValues/delete|Supprime une valeur de balise d’abonnement.|
|/subscriptions/resources/read|Obtient les ressources d’un abonnement.|
|/subscriptions/resourceGroups/read|Obtient ou répertorie les groupes de ressources.|
|/subscriptions/resourceGroups/write|Crée ou met à jour un groupe de ressources.|
|/subscriptions/resourceGroups/delete|Supprime un groupe de ressources, ainsi que toutes ses ressources.|
|/subscriptions/resourceGroups/moveResources/action|Déplace les ressources d’un groupe de ressources à un autre.|
|/subscriptions/resourceGroups/validateMoveResources/action|Valide le déplacement des ressources d’un groupe de ressources à un autre.|
|/subscriptions/resourcegroups/resources/read|Obtient les ressources du groupe de ressources.|
|/subscriptions/resourcegroups/deployments/read|Obtient ou répertorie les déploiements.|
|/subscriptions/resourcegroups/deployments/write|Crée ou met à jour un déploiement.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|Obtient ou répertorie les états de l’opération de déploiement.|
|/subscriptions/resourcegroups/deployments/operations/read|Obtient ou répertorie les opérations de déploiement.|
|/subscriptions/locations/read|Obtient la liste des emplacements pris en charge.|
|/links/read|Obtient ou répertorie les liens des ressources.|
|/links/write|Crée ou met à jour un lien de ressource.|
|/links/delete|Supprime un lien de ressource.|
|/tenants/read|Obtient la liste des clients.|
|/resources/read|Obtenir la liste des ressources en fonction des filtres.|
|/deployments/read|Obtient ou répertorie les déploiements.|
|/deployments/write|Crée ou met à jour un déploiement.|
|/deployments/delete|Supprime un déploiement.|
|/deployments/cancel/action|Annule un déploiement.|
|/deployments/validate/action|Valide un déploiement.|
|/deployments/operations/read|Obtient ou répertorie les opérations de déploiement.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Opération | Description |
|---|---|
|/jobcollections/read|Obtenir une collection de travaux.|
|/jobcollections/write|Crée ou met à jour une collection de travaux.|
|/jobcollections/delete|Supprime une collection de travaux.|
|/jobcollections/enable/action|Active une collection de travaux.|
|/jobcollections/disable/action|Désactive une collection de travaux.|
|/jobcollections/jobs/read|Obtient un travail.|
|/jobcollections/jobs/write|Crée ou met à jour un travail.|
|/jobcollections/jobs/delete|Supprime un travail.|
|/jobcollections/jobs/run/action|Exécute un travail.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Génère une définition d’application logique basée sur un travail de Scheduler.|
|/jobcollections/jobs/jobhistories/read|Affiche l’historique des travaux.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Opération | Description |
|---|---|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources de recherche et active la création de services de recherche.|
|/checkNameAvailability/action|Vérifie la disponibilité du nom du service.|
|/searchServices/write|Crée ou met à jour le service de recherche.|
|/searchServices/read|Lit le service de recherche.|
|/searchServices/delete|Supprime le service de recherche.|
|/searchServices/start/action|Démarre le service de recherche.|
|/searchServices/stop/action|Arrête le service de recherche.|
|/searchServices/listAdminKeys/action|Lit les clés d’administration.|
|/searchServices/regenerateAdminKey/action|Régénère la clé d’administration.|
|/searchServices/createQueryKey/action|Crée la clé de requête.|
|/searchServices/queryKey/read|Lit les clés de requête.|
|/searchServices/queryKey/delete|Supprime la clé de requête.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Opération | Description |
|---|---|
|/jitNetworkAccessPolicies/read|Obtient les stratégies d’accès réseau immédiat.|
|/jitNetworkAccessPolicies/write|Crée une stratégie d’accès réseau immédiat ou met à jour une stratégie d’accès réseau immédiat existante.|
|/jitNetworkAccessPolicies/initiate/action|Initie une stratégie d’accès réseau immédiat.|
|/securitySolutionsReferenceData/read|Obtient les données de référence des solutions de sécurité.|
|/securityStatuses/read|Obtient les états d’intégrité de la sécurité des ressources Azure.|
|/webApplicationFirewalls/read|Obtient le pare-feu d’applications web.|
|/webApplicationFirewalls/write|Crée un pare-feu d’applications web nouvelles ou met à jour un pare-feu d’applications web existant.|
|/webApplicationFirewalls/delete|Supprime un pare-feu d’applications web.|
|/securitySolutions/read|Obtient les solutions de sécurité.|
|/securitySolutions/write|Crée une solution de sécurité ou met à jour une solution de sécurité existante.|
|/securitySolutions/delete|Supprime une solution de sécurité.|
|/tasks/read|Obtient toutes les recommandations de sécurité disponibles.|
|/tasks/dismiss/action|Ignorer une recommandation de sécurité.|
|/tasks/activate/action|Activer une recommandation de sécurité.|
|/policies/read|Obtient la stratégie de sécurité.|
|/policies/write|Met à jour la stratégie de sécurité.|
|/applicationWhitelistings/read|Obtient les listes blanches des applications.|
|/applicationWhitelistings/write|Crée une liste blanche des applications ou met à jour une liste blanche des applications existante.|

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

| Opération | Description |
|---|---|
|/subscriptions/write|Crée ou met à jour un abonnement.|
|/gateways/write|Crée ou met à jour une passerelle.|
|/gateways/delete|Supprime une passerelle.|
|/gateways/read|Obtient une passerelle.|
|/gateways/regenerateprofile/action|Régénère le profil de passerelle.|
|/gateways/upgradetolatest/action|Met à niveau la passerelle vers la dernière version.|
|/nodes/write|Crée ou met à jour un nœud.|
|/nodes/delete|Supprime un nœud.|
|/nodes/read|Obtient un nœud.|
|/sessions/write|Crée ou met à jour une session.|
|/sessions/read|Obtient une session.|
|/sessions/delete|Supprime une session.|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Opération | Description |
|---|---|
|/checkNameAvailability/action|Vérifier la disponibilité d’un espace de noms sous un abonnement donné|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources ServiceBus et active la création de ressources ServiceBus.|
|/namespaces/write|Créer une ressource Namespace et mettre à jour ses propriétés. Les balises et l’état de la ressource Namespace sont les propriétés qui peuvent être mises à jour.|
|/namespaces/read|Obtenir la liste des descriptions des ressources Namespace.|
|/namespaces/Delete|Supprimer une ressource Namespace|
|/namespaces/metricDefinitions/read|Obtenir la liste des descriptions des mesures de ressource Namespace.|
|/namespaces/authorizationRules/write|Créer des règles d’autorisation au niveau d’une ressource Namespace et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/namespaces/authorizationRules/read|Obtenir la liste des descriptions des règles d’autorisation des ressources Namespaces.|
|/namespaces/authorizationRules/delete|Supprimer une règle d’autorisation de ressource Namespace. La règle d’autorisation de ressource Namespace par défaut ne peut pas être supprimée. |
|/namespaces/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à la ressource Namespace|
|/namespaces/authorizationRules/regenerateKeys/action|Régénérer la clé primaire ou secondaire pour la ressource.|
|/namespaces/diagnosticSettings/read|Obtenir la liste des descriptions des ressources des paramètres de diagnostics Namespace|
|/namespaces/diagnosticSettings/write|Obtenir la liste des descriptions des ressources des paramètres de diagnostics Namespace.|
|/namespaces/queues/write|Créer ou mettre à jour des propriétés de file d’attente.|
|/namespaces/queues/read|Obtenir la liste des descriptions des ressources de file d’attente.|
|/namespaces/queues/Delete|Opération de suppression des ressources de file d’attente.|
|/namespaces/queues/authorizationRules/write|Créer des règles d’autorisation de file d’attente et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/namespaces/queues/authorizationRules/read| Obtenir la liste des règles d’autorisation de file d’attente.|
|/namespaces/queues/authorizationRules/delete|Opération de suppression des règles d’autorisation de file d’attente.|
|/namespaces/queues/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à la file d’attente.|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Régénérer la clé primaire ou secondaire pour la ressource.|
|/namespaces/logDefinitions/read|Obtenir la liste des descriptions des ressources des journaux Namespace.|
|/namespaces/topics/write|Créer ou mettre à jour des propriétés de rubrique.|
|/namespaces/topics/read|Obtenir la liste des descriptions des ressources de rubrique.|
|/namespaces/topics/Delete|Opération de suppression des ressources de rubrique.|
|/namespaces/topics/authorizationRules/write|Crée des règles d’autorisation de rubrique et met à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/namespaces/topics/authorizationRules/read| Récupère la liste des règles d’autorisation de rubrique.|
|/namespaces/topics/authorizationRules/delete|Opération de suppression des règles d’autorisation de rubrique|
|/namespaces/topics/authorizationRules/listkeys/action|Récupère la chaîne de connexion à la rubrique.|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Régénère la clé primaire ou secondaire pour la ressource.|
|/namespaces/topics/subscriptions/write|Crée ou met à jour les propriétés TopicSubscription.|
|/namespaces/topics/subscriptions/read|Récupère la liste des descriptions des ressources TopicSubscription.|
|/namespaces/topics/subscriptions/Delete|Opération de suppression d’une ressource TopicSubscription|
|/namespaces/topics/subscriptions/rules/write|Crée ou met à jour les propriétés de règle.|
|/namespaces/topics/subscriptions/rules/read|Récupère la liste des descriptions des ressources de règle.|
|/namespaces/topics/subscriptions/rules/Delete|Opération de suppression d’une ressource de règle.|

## <a name="microsoftsql"></a>Microsoft.Sql

| Opération | Description |
|---|---|
|/servers/read|Retourne une liste de serveurs dans un groupe de ressources sur un abonnement.|
|/servers/write|Crée un serveur ou modifie les propriétés d’un serveur existant dans un groupe de ressources sur un abonnement.|
|/servers/delete|Supprime un serveur et toutes les bases de données et les pools élastiques qu’il contient.|
|/servers/import/action|Crée une base de données sur le serveur et déploie le schéma et les données à partir d’un package DacPac.|
|/servers/upgrade/action|Active les nouvelles fonctionnalités disponibles dans la dernière version du serveur et spécifie les mappages de conversion des éditions de base de données.|
|/servers/VulnerabilityAssessmentScans/action|Exécute l’analyse du serveur d’évaluation des vulnérabilités.|
|/servers/operationResults/read|L’opération permet de suivre la progression de la mise à niveau du serveur à partir d’une version inférieure.|
|/servers/operationResults/delete|Abandonner la mise à niveau en cours de la version du serveur|
|/servers/securityAlertPolicies/read|Récupère les détails de la stratégie de détection des menaces configurée sur un serveur donné.|
|/servers/securityAlertPolicies/write|Modifie la détection des menaces de serveur pour un serveur donné.|
|/servers/securityAlertPolicies/operationResults/read|Récupère les résultats de l’opération de définition de la stratégie de détection des menaces de serveur.|
|/servers/administrators/read|Récupère les détails d’administrateur du serveur.|
|/servers/administrators/write|Crée ou met à jour un administrateur de serveur.|
|/servers/administrators/delete|Supprime un administrateur de serveur sur le serveur.|
|/servers/recoverableDatabases/read|Cette opération est utilisée pour la récupération d’urgence de base de données active pour restaurer la base de données au point de sauvegarde correct connu. Elle retourne des informations sur la dernière sauvegarde correcte, mais ne restaure pas réellement la base de données.|
|/servers/serviceObjectives/read|Récupère la liste des objectifs de niveau de service (également appelés niveaux de performance) disponibles sur un serveur donné.|
|/servers/firewallRules/read|Récupère les détails de la règle de pare-feu du serveur.|
|/servers/firewallRules/write|Crée ou met à jour la règle de pare-feu du serveur qui contrôle la plage d’adresses IP autorisées à se connecter au serveur.|
|/servers/firewallRules/delete|Supprime la règle de pare-feu du serveur.|
|/servers/administratorOperationResults/read|Récupère les résultats de l’opération de l’administrateur du serveur.|
|/servers/recommendedElasticPools/read|Récupère une recommandation pour les pools de bases de données élastiques afin de réduire les coûts ou d’améliorer les performances en fonction de l’utilisation des ressources d’historique.|
|/servers/recommendedElasticPools/metrics/read|Récupère les mesures pour les pools de bases de données élastiques recommandés pour un serveur donné.|
|/servers/recommendedElasticPools/databases/read|Récupère les bases de données qui doivent être ajoutées aux pools de bases de données élastiques recommandés pour un serveur donné.|
|/servers/elasticPools/read|Récupère les détails d’un pool de bases de données élastique sur un serveur donné.|
|/servers/elasticPools/write|Crée ou modifie les propriétés d’un pool de bases de données élastique existant.|
|/servers/elasticPools/delete|Supprime un pool de bases de données élastique existant.|
|/servers/elasticPools/operationResults/read|Récupère les détails d’une opération effectuée dans un pool de bases de données élastique donné.|
|/servers/elasticPools/providers/Microsoft.Insights/<br>metricDefinitions/read|Retourne les types de mesure disponibles pour les pools de bases de données élastiques.|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/read|Récupère le paramètre de diagnostic pour la ressource.|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource.|
|/servers/elasticPools/metrics/read|Retourne les mesures d’utilisation des ressources du pool de bases de données élastique.|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Récupère les activités et les détails d’une base de données spécifique faisant partie d’un pool de bases de données élastique.|
|/servers/elasticPools/advisors/read|Retourne la liste des conseillers disponibles pour le pool élastique.|
|/servers/elasticPools/advisors/write|Met à jour l’état d’exécution automatique d’un conseiller au niveau du pool élastique.|
|/servers/elasticPools/advisors/recommendedActions/read|Retourne la liste des actions recommandées du conseiller spécifié pour le pool élastique.|
|/servers/elasticPools/advisors/recommendedActions/write|Appliquer l’action recommandée sur le pool élastique|
|/Servers/elasticPools/elasticPoolActivity/Read|Récupérer les activités et les détails sur un pool de bases de données élastique donné|
|/servers/elasticPools/databases/read|Récupérer la liste et les détails des bases de données qui font partie du pool de bases de données élastique sur un serveur donné|
|/servers/auditingPolicies/read|Récupérer les détails de la stratégie d’audit de table du serveur par défaut configurée sur un serveur donné|
|/servers/auditingPolicies/write|Modifier l’audit de table du serveur par défaut pour un serveur donné|
|/servers/disasterRecoveryConfiguration/operationResults/read|Obtenir les résultats des opérations de configuration de la récupération d’urgence|
|/servers/advisors/read|Renvoie la liste des conseillers disponibles pour le serveur|
|/servers/advisors/write|Met à jour l’état d’exécution automatique d’un conseiller au niveau du serveur.|
|/servers/advisors/recommendedActions/read|Renvoie la liste des actions recommandées du conseiller spécifié pour le serveur|
|/servers/advisors/recommendedActions/write|Appliquer l’action recommandée sur le serveur|
|/servers/usages/read|Renvoyer le quota DTU du serveur et la consommation DTU actuelle par toutes les bases de données au sein du serveur|
|/servers/elasticPoolEstimates/read|Renvoie la liste des estimations du pool élastique déjà créée pour ce serveur|
|/servers/elasticPoolEstimates/write|Crée la nouvelle estimation du pool élastique pour la liste des bases de données fournie|
|/servers/auditingSettings/read|Récupérer les détails de la stratégie d’audit des objets blob de serveur configurée sur un serveur donné|
|/servers/auditingSettings/write|Modifier l’audit des objets blob de serveur pour un serveur donné|
|/servers/auditingSettings/operationResults/read|Récupérer le résultat l’opération de configuration de la stratégie d’audit des objets blob de serveur|
|/servers/backupLongTermRetentionVaults/read|Cette opération est utilisée pour obtenir un coffre de rétention de sauvegarde à long terme. Elle renvoie des informations sur le coffre inscrit sur ce serveur.|
|/servers/backupLongTermRetentionVaults/write|Inscrire un coffre de rétention de sauvegarde à long terme|
|/servers/restorableDroppedDatabases/read|Récupérer une liste des bases de données supprimées d’un serveur donné qui se trouvent toujours dans la stratégie de rétention. Cette opération renvoie une liste de bases de données et les métadonnées associées, telle que la date de suppression.|
|/servers/databases/read|Renvoyer une liste de serveurs dans un groupe de ressources sur un abonnement|
|/servers/databases/write|Créer un nouveau serveur ou modifier les propriétés d’un serveur existant dans un groupe de ressources sur un abonnement|
|/servers/databases/delete|Supprimer un serveur et toutes les bases de données et les pools élastiques qu’il contient|
|/servers/databases/export/action|Créer une nouvelle base de données sur le serveur et de déployer le schéma et les données à partir d’un package DacPac|
|/servers/databases/VulnerabilityAssessmentScans/action|Exécuter l’analyse de la base de données d’évaluation de la vulnérabilité.|
|/servers/databases/pause/action|Interrompre une base de données de l’édition DataWarehouse|
|/servers/databases/resume/action|Reprendre une base de données de l’édition DataWarehouse|
|/servers/databases/operationResults/read|L’opération est utilisée pour suivre la progression de l’opération de base de données à long terme, telle que la mise à l’échelle.|
|/servers/databases/replicationLinks/read|Renvoie des informations sur les liens de réplication établis pour une base de données spécifique|
|/servers/databases/replicationLinks/delete|Mettre fin à la relation de réplication volontairement avec une perte de données potentielle|
|/servers/databases/replicationLinks/unlink/action|Mettre fin à la relation de réplication volontairement ou après la synchronisation avec le partenaire|
|/servers/databases/replicationLinks/failover/action|Basculement après synchronisation de toutes les modifications de la base de données principale, transformant cette base de données en base de données principale dans la relation de réplication et transformant la base de données principale distante en base de données secondaire|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Basculement immédiat avec perte de données potentielle, transformant cette base de données en base de données principale dans la relation de réplication et transformant la base de données principale distante en base de données secondaire|
|/servers/databases/replicationLinks/updateReplicationMode/action|Mettre à jour le mode de réplication pour le lien vers un mode synchrone ou asynchrone|
|/servers/databases/replicationLinks/operationResults/read|Obtenir l’état des opérations à long terme sur les liens de réplication de base de données|
|/servers/databases/dataMaskingPolicies/read|Récupérer les détails de la stratégie de masquage des données configurée sur une base de données spécifique|
|/servers/databases/dataMaskingPolicies/write|Modifier la stratégie de masquage des données pour une base de données spécifique|
|/servers/databases/dataMaskingPolicies/rules/read|Récupérer les détails de la règle de stratégie de masquage des données configurée sur une base de données spécifique|
|/servers/databases/dataMaskingPolicies/rules/write|Modifier la règle de stratégie de masquage des données pour une base de données spécifique|
|/servers/databases/securityAlertPolicies/read|Récupérer les détails de la stratégie de détection des menaces configurée sur une base de données spécifique|
|/servers/databases/securityAlertPolicies/write|Modifier la stratégie de détection des menaces pour une base de données spécifique|
|/servers/databases/providers/Microsoft.Insights/<br>metricDefinitions/read|Renvoie les types de mesures disponibles pour les bases de données|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/servers/databases/providers/Microsoft.Insights/<br>logDefinitions/read|Obtient les journaux disponibles pour les bases de données|
|/servers/databases/topQueries/read|Renvoie les statistiques d’exécution agrégées de la requête sélectionnée dans la période choisie|
|/servers/databases/topQueries/queryText/read|Retourne le texte Transact-SQL pour l’ID de requête sélectionné|
|/servers/databases/topQueries/statistics/read|Renvoie les statistiques d’exécution agrégées de la requête sélectionnée dans la période choisie|
|/servers/databases/connectionPolicies/read|Récupère les détails de la stratégie de connexion configurée d’une base de données spécifique.|
|/servers/databases/connectionPolicies/write|Modifie la stratégie de connexion d’une base de données spécifique.|
|/servers/databases/metrics/read|Retourne les mesures d’utilisation des ressources de base de données.|
|/servers/databases/auditRecords/read|Récupère les enregistrements d’audit d’objet blob de base de données.|
|/servers/databases/transparentDataEncryption/read|Récupère l’état et les détails de la fonctionnalité de sécurité Transparent Data Encryption pour une base de données spécifique.|
|/servers/databases/transparentDataEncryption/write|Active ou désactive Transparent Data Encryption pour une base de données spécifique.|
|/servers/databases/transparentDataEncryption/operationResults/read|Récupère l’état et les détails de la fonctionnalité de sécurité Transparent Data Encryption pour une base de données spécifique.|
|/servers/databases/auditingPolicies/read|Récupère les détails de la stratégie d’audit des tables configurée dans une base de données spécifique.|
|/servers/databases/auditingPolicies/write|Modifie la stratégie d’audit des tables pour une base de données spécifique.|
|/servers/databases/dataWarehouseQueries/read|Retourne les informations de requête de distribution d’entrepôt de données pour l’ID de requête sélectionné.|
|/servers/databases/dataWarehouseQueries/<br>dataWarehouseQuerySteps/read|Retourne les informations d’étape de requête distribuée de la requête de l’entrepôt de données pour l’ID d’étape sélectionné.|
|/servers/databases/serviceTierAdvisors/read|Retourne une suggestion sur la montée ou la descente en puissance d’une base de données en fonction des statistiques d’exécution de requête afin d’améliorer les performances ou de réduire les coûts.|
|/servers/databases/advisors/read|Retourne la liste des conseillers disponibles pour la base de données.|
|/servers/databases/advisors/write|Met à jour l’état d’exécution automatique d’un conseiller au niveau de la base de données.|
|/servers/databases/advisors/recommendedActions/read|Retourne la liste des actions recommandées du conseiller spécifié pour la base de données.|
|/servers/databases/advisors/recommendedActions/write|Applique l’action recommandée sur la base de données.|
|/servers/databases/usages/read|Retourne la taille maximale de base de données qui peut être atteinte ainsi que la taille actuelle occupée par les données.|
|/servers/databases/queryStore/read|Retourne les valeurs actuelles des paramètres de magasin de requêtes pour la base de données.|
|/servers/databases/queryStore/write|Met à jour les paramètres de magasin de requêtes pour la base de données.|
|/servers/databases/auditingSettings/read|Récupère les détails de la stratégie d’audit d’objets blob configurée dans une base de données spécifique.|
|/servers/databases/auditingSettings/write|Modifie la stratégie d’audit d’objets blob pour une base de données spécifique.|
|/servers/databases/schemas/tables/recommendedIndexes/read|Récupère la liste des recommandations d’index d’une base de données.|
|/servers/databases/schemas/tables/recommendedIndexes/write|Applique une recommandation d’index.|
|/servers/databases/schemas/tables/columns/read|Récupère la liste des colonnes d’une table.|
|/servers/databases/missingindexes/read|Retourne des suggestions sur les index de base de données à créer, modifier ou supprimer afin d’améliorer les performances des requêtes.|
|/servers/databases/missingindexes/write|Utilise une recommandation d’index de base de données dans une base de données particulière.|
|/servers/databases/importExportOperationResults/read|Retourne les détails de l’opération d’importation ou d’exportation de la base de données à partir du fichier DacPac situé dans le compte de stockage.|
|/servers/importExportOperationResults/read|Retourne la liste des détails des opérations d’importation de base de données du compte de stockage sur un serveur donné.|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Opération | Description |
|---|---|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources de stockage et permet la création de comptes de stockage.|
|/checknameavailability/read|Vérifie que le nom du valide est valide et qu’il n’est pas déjà utilisé.|
|/storageAccounts/write|Crée un compte de stockage avec les paramètres spécifiés ou met à jour les propriétés ou les balises, ou ajoute un domaine personnalisé pour le compte de stockage spécifié.|
|/storageAccounts/delete|Supprime un compte de stockage existant.|
|/storageAccounts/listkeys/action|Retourne les clés d’accès au compte de stockage spécifié.|
|/storageAccounts/regeneratekey/action|Régénère les clés d’accès au compte de stockage spécifié.|
|/storageAccounts/read|Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié.|
|/storageAccounts/listAccountSas/action|Retourne le jeton SAS du compte de stockage spécifié.|
|/storageAccounts/listServiceSas/action|Jeton SAS du service de stockage|
|/storageAccounts/services/diagnosticSettings/write|Crée/met à jour les paramètres de diagnostic de compte de stockage.|
|/skus/read|Répertorie les références prises en charge par Microsoft.Storage.|
|/usages/read|Retourne la limite et le nombre actuel d’utilisations des ressources dans l’abonnement spécifié.|
|/operations/read|Interroge l’état d’une opération asynchrone.|
|/locations/deleteVirtualNetworkOrSubnets/action|Informe Microsoft.Storage que le sous-réseau ou le réseau virtuel est en cours de suppression.|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Opération | Description |
|---|---|
|/managers/clearAlerts/action|Efface toutes les alertes associées au gestionnaire d’appareils.|
|/managers/getActivationKey/action|Récupère la clé d’activation du gestionnaire d’appareils.|
|/managers/regenerateActivationKey/action|Régénère la clé d’activation du gestionnaire d’appareils.|
|/managers/regenarateRegistationCertificate/action|Régénère le certificat d’inscription du gestionnaire d’appareils.|
|/managers/getEncryptionKey/action|Récupère la clé de chiffrement du gestionnaire d’appareils.|
|/managers/read|Répertorie ou récupère les gestionnaires d’appareils.|
|/managers/delete|Supprime les gestionnaires d’appareils.|
|/managers/write|Crée ou met à jour les gestionnaires d’appareils.|
|/managers/configureDevice/action|Configure un appareil.|
|/managers/listActivationKey/action|Récupère la clé d’activation du gestionnaire StorSimple Device Manager.|
|/managers/listPublicEncryptionKey/action|Répertorie les clés de chiffrement publiques d’un gestionnaire StorSimple Device Manager.|
|/managers/listPrivateEncryptionKey/action|Récupère la clé de chiffrement publique d’un gestionnaire StorSimple Device Manager.|
|/managers/provisionCloudAppliance/action|Crée une appliance cloud.|
|/Managers/write|L’opération de création de coffre entraîne la création d’une ressource Azure de type « coffre ».|
|/Managers/read|L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ».|
|/Managers/delete|L’opération de suppression de coffre supprime la ressource Azure de type « coffre » spécifiée.|
|/managers/storageAccountCredentials/write|Crée ou met à jour les informations d’identification du compte de stockage.|
|/managers/storageAccountCredentials/read|Répertorie ou récupère les informations d’identification du compte de stockage.|
|/managers/storageAccountCredentials/delete|Supprime les informations d’identification du compte de stockage.|
|/managers/storageAccountCredentials/listAccessKey/action|Répertorie les clés d’accès des informations d’identification de compte de stockage.|
|/managers/accessControlRecords/read|Répertorie ou récupère les enregistrements de contrôle d’accès.|
|/managers/accessControlRecords/write|Crée ou met à jour les enregistrements de contrôle d’accès.|
|/managers/accessControlRecords/delete|Supprime les enregistrements de contrôle d’accès.|
|/managers/metrics/read|Répertorie ou récupère les mesures.|
|/managers/bandwidthSettings/read|Répertorie les paramètres de bande passante (série 8000 uniquement).|
|/managers/bandwidthSettings/write|Crée ou met à jour les paramètres de bande passante (série 8000 uniquement).|
|/managers/bandwidthSettings/delete|Supprime des paramètres de bande passante existants (série 8000 uniquement).|
|/Managers/extendedInformation/read|L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ».|
|/Managers/extendedInformation/write|L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ».|
|/Managers/extendedInformation/delete|L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ».|
|/managers/alerts/read|Répertorie ou récupère les alertes.|
|/managers/storageDomains/read|Répertorie ou récupère les domaines de stockage.|
|/managers/storageDomains/write|Crée ou met à jour les domaines de stockage.|
|/managers/storageDomains/delete|Supprime les domaines de stockage.|
|/managers/devices/scanForUpdates/action|Recherche des mises à jour sur un appareil.|
|/managers/devices/download/action|Télécharge les mises à jour d’un appareil.|
|/managers/devices/install/action|Installe des mises à jour sur un appareil.|
|/managers/devices/read|Répertorie ou récupère les appareils.|
|/managers/devices/write|Crée ou met à jour les appareils.|
|/managers/devices/delete|Supprime les appareils.|
|/managers/devices/deactivate/action|Désactive un appareil.|
|/managers/devices/publishSupportPackage/action|Publie le package de support d’un appareil pour la résolution des problèmes du Support Microsoft.|
|/managers/devices/failover/action|Basculement de l’appareil.|
|/managers/devices/sendTestAlertEmail/action|Envoie un test d’alerte par e-mail aux destinataires de courrier électronique configurés.|
|/managers/devices/installUpdates/action|Installe des mises à jour sur les appareils.|
|/managers/devices/listFailoverSets/action|Répertorie les ensembles de basculement d’un appareil existant.|
|/managers/devices/listFailoverTargets/action|Répertorie les cibles de basculement des appareils.|
|/managers/devices/publicEncryptionKey/action|Répertorie les clés de chiffrement publiques d’un gestionnaire d’appareils.|
|/managers/devices/hardwareComponentGroups/<br>read|Répertorie les groupes de composants matériels.|
|/managers/devices/hardwareComponentGroups/<br>changeControllerPowerState/action|Modifie l’état d’alimentation du contrôleur des groupes de composants matériels.|
|/managers/devices/metrics/read|Répertorie ou récupère les mesures.|
|/managers/devices/chapSettings/write|Crée ou met à jour les paramètres CHAP.|
|/managers/devices/chapSettings/read|Répertorie ou récupère les paramètres CHAP.|
|/managers/devices/chapSettings/delete|Supprime les paramètres CHAP.|
|/managers/devices/backupScheduleGroups/read|Répertorie ou récupère les groupes de planification de sauvegarde.|
|/managers/devices/backupScheduleGroups/write|Crée ou met à jour les groupes de planification de sauvegarde.|
|/managers/devices/backupScheduleGroups/delete|Supprime les groupes de planification de sauvegarde.|
|/managers/devices/updateSummary/read|Répertorie ou récupère le résumé de la mise à jour.|
|/managers/devices/migrationSourceConfigurations/<br>import/action|Importe les configurations de source pour la migration.|
|/managers/devices/migrationSourceConfigurations/<br>startMigrationEstimate/action|Démarre un travail pour estimer la durée du processus de migration.|
|/managers/devices/migrationSourceConfigurations/<br>startMigration/action|Démarre la migration à l’aide des configurations de source.|
|/managers/devices/migrationSourceConfigurations/<br>confirmMigration/action|Vérifie et valide une migration réussie.|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationEstimate/action|Extrait l’état du travail d’estimation de la migration.|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationStatus/action|Extrait l’état de la migration.|
|/managers/devices/migrationSourceConfigurations/<br>fetchConfirmMigrationStatus/action|Extrait l’état de confirmation de la migration.|
|/managers/devices/alertSettings/read|Répertorie ou récupère les paramètres d’alerte.|
|/managers/devices/alertSettings/write|Crée ou met à jour les paramètres d’alerte.|
|/managers/devices/networkSettings/read|Répertorie ou récupère les paramètres réseau.|
|/managers/devices/networkSettings/write|Crée ou met à jour les paramètres réseau.|
|/managers/devices/jobs/read|Répertorie ou récupère les travaux.|
|/managers/devices/jobs/cancel/action|Annule un travail en cours d’exécution.|
|/managers/devices/metricsDefinitions/read|Répertorie ou récupère les définitions de mesures.|
|/managers/devices/volumeContainers/write|Crée ou met à jour des conteneurs de volumes (série 8000 uniquement).|
|/managers/devices/volumeContainers/read|Répertorie les conteneurs de volumes (série 8000 uniquement).|
|/managers/devices/volumeContainers/delete|Supprime des conteneurs de volumes existants (série 8000 uniquement).|
|/managers/devices/volumeContainers/listEncryptionKeys/action|Répertorie les clés de chiffrement des conteneurs de volumes.|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|Clés de chiffrement de substitution des conteneurs de volumes|
|/managers/devices/volumeContainers/metrics/read|Répertorie les mesures.|
|/managers/devices/volumeContainers/volumes/read|Répertorie les volumes.|
|/managers/devices/volumeContainers/volumes/write|Crée un volume ou met à jour des volumes existants.|
|/managers/devices/volumeContainers/volumes/delete|Supprime un volume existant.|
|/managers/devices/volumeContainers/volumes/metrics/read|Répertorie les mesures.|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|Répertorie les définitions de mesures.|
|/managers/devices/volumeContainers/metricsDefinitions/read|Répertorie les définitions de mesures.|
|/managers/devices/iscsiservers/read|Répertorie ou récupère les serveurs iSCSI.|
|/managers/devices/iscsiservers/write|Crée ou met à jour les serveurs iSCSI.|
|/managers/devices/iscsiservers/delete|Supprime les serveurs iSCSI.|
|/managers/devices/iscsiservers/backup/action|Exécute la sauvegarde d’un serveur iSCSI.|
|/managers/devices/iscsiservers/metrics/read|Répertorie ou récupère les mesures.|
|/managers/devices/iscsiservers/disks/read|Répertorie ou récupère les disques.|
|/managers/devices/iscsiservers/disks/write|Crée ou met à jour les disques.|
|/managers/devices/iscsiservers/disks/delete|Supprime les disques.|
|/managers/devices/iscsiservers/disks/metrics/read|Répertorie ou récupère les mesures.|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|Répertorie ou récupère les définitions de mesures.|
|/managers/devices/iscsiservers/metricsDefinitions/read|Répertorie ou récupère les définitions de mesures.|
|/managers/devices/backups/read|Répertorie ou récupère le jeu de sauvegarde.|
|/managers/devices/backups/delete|Supprime le jeu de sauvegarde.|
|/managers/devices/backups/restore/action|Restaure tous les volumes d’un jeu de sauvegarde.|
|/managers/devices/backups/elements/clone/action|Clone un partage ou un volume à l’aide d’un élément de sauvegarde.|
|/managers/devices/backupPolicies/write|Crée ou met à jour des stratégies de sauvegarde (série 8000 uniquement).|
|/managers/devices/backupPolicies/read|Répertorie les stratégies de sauvegarde (série 8000 uniquement).|
|/managers/devices/backupPolicies/delete|Supprime des stratégies de sauvegarde existantes (série 8000 uniquement).|
|/managers/devices/backupPolicies/backup/action|Exécute une sauvegarde manuelle pour créer une sauvegarde à la demande de tous les volumes protégés par la stratégie.|
|/managers/devices/backupPolicies/schedules/write|Crée une planification ou met à jour des planifications existantes.|
|/managers/devices/backupPolicies/schedules/read|Répertorie les planifications.|
|/managers/devices/backupPolicies/schedules/delete|Supprime une planification existante.|
|/managers/devices/securitySettings/update/action|Met à jour les paramètres de sécurité.|
|/managers/devices/securitySettings/read|Répertorie les paramètres de sécurité.|
|/managers/devices/securitySettings/<br>syncRemoteManagementCertificate/action|Synchronise le certificat de gestion à distance d’un appareil.|
|/managers/devices/securitySettings/write|Crée ou met à jour les paramètres de sécurité.|
|/managers/devices/fileservers/read|Répertorie ou récupère les serveurs de fichiers.|
|/managers/devices/fileservers/write|Crée ou met à jour les serveurs de fichiers.|
|/managers/devices/fileservers/delete|Supprime les serveurs de fichiers.|
|/managers/devices/fileservers/backup/action|Exécute la sauvegarde d’un serveur de fichiers.|
|/managers/devices/fileservers/metrics/read|Répertorie ou récupère les mesures.|
|/managers/devices/fileservers/shares/write|Crée ou met à jour les partages.|
|/managers/devices/fileservers/shares/read|Répertorie ou récupère les partages.|
|/managers/devices/fileservers/shares/delete|Supprime les partages.|
|/managers/devices/fileservers/shares/metrics/read|Répertorie ou récupère les mesures.|
|/managers/devices/fileservers/shares/metricsDefinitions/read|Répertorie ou récupère les définitions de mesures.|
|/managers/devices/fileservers/metricsDefinitions/read|Répertorie ou récupère les définitions de mesures.|
|/managers/devices/timeSettings/read|Répertorie ou récupère les paramètres horaires.|
|/managers/devices/timeSettings/write|Crée ou met à jour les paramètres horaires.|
|/Managers/certificates/write|L’opération de mise à jour de certificat de ressource met à jour le certificat d’informations d’identification du coffre/de la ressource.|
|/managers/cloudApplianceConfigurations/read|Répertorie les configurations prises en charge d’appliance cloud.|
|/managers/metricsDefinitions/read|Répertorie ou récupère les définitions de mesures.|
|/managers/encryptionSettings/read|Répertorie ou récupère les paramètres de chiffrement.|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Opération | Description |
|---|---|
|/streamingjobs/Start/action|Démarre une tâche Stream Analytics.|
|/streamingjobs/Stop/action|Arrête une tâche Stream Analytics.|
|/streamingjobs/Read|Lit une tâche Stream Analytics.|
|/streamingjobs/Write|Écrit une tâche Stream Analytics.|
|/streamingjobs/Delete|Supprime une tâche Stream Analytics.|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|Récupère les mesures disponibles pour les tâches de diffusion de contenu.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Lit le paramètre de diagnostic.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Écrit un paramètre de diagnostic.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|Récupère les journaux disponibles pour les tâches de diffusion de contenu.|
|/streamingjobs/transformations/Read|Lit une transformation de tâche Stream Analytics.|
|/streamingjobs/transformations/Write|Écrit une transformation de tâche Stream Analytics.|
|/streamingjobs/transformations/Delete|Supprime une transformation de tâche Stream Analytics.|
|/streamingjobs/inputs/Read|Lit une entrée de tâche Stream Analytics.|
|/streamingjobs/inputs/Write|Écrit une entrée de tâche Stream Analytics.|
|/streamingjobs/inputs/Delete|Supprime une entrée de tâche Stream Analytics.|
|/streamingjobs/outputs/Read|Lit une sortie de tâche Stream Analytics.|
|/streamingjobs/outputs/Write|Écrit une sortie de tâche Stream Analytics.|
|/streamingjobs/outputs/Delete|Supprime une sortie de tâche Stream Analytics.|

## <a name="microsoftsupport"></a>Microsoft.Support

| Opération | Description |
|---|---|
|/register/action|S’inscrit auprès du fournisseur de ressources de support.|
|/supportTickets/read|Récupère les détails du ticket de support (notamment l’état, la gravité, les détails du contact et les communications) ou la liste des tickets de support des abonnements.|
|/supportTickets/write|Crée ou met à jour un ticket de support. Vous pouvez créer un ticket de support pour les problèmes techniques ou les problèmes liés à la facturation, aux quotas ou à la gestion des abonnements. Vous pouvez mettre à jour la gravité des problèmes, les détails du contact et les communications pour les tickets de support existants.|

## <a name="microsoftweb"></a>Microsoft.Web

| Opération | Description |
|---|---|
|/unregister/action|Désinscrit le fournisseur de ressources Microsoft.Web pour l’abonnement.|
|/validate/action|Valide.|
|/register/action|Inscrit le fournisseur de ressources Microsoft.Web pour l’abonnement.|
|/hostingEnvironments/Read|Récupère les propriétés d’un environnement App Service.|
|/hostingEnvironments/Write|Crée un environnement App Service ou en met un à jour.|
|/hostingEnvironments/Delete|Supprime un environnement App Service.|
|/hostingEnvironments/reboot/Action|Redémarre tous les ordinateurs dans un environnement App Service.|
|/hostingenvironments/resume/action|Reprend les environnements d’hébergement.|
|/hostingenvironments/suspend/action|Interrompt les environnements d’hébergement.|
|/hostingenvironments/metricdefinitions/read|Récupère les définitions métriques des environnements d’hébergement.|
|/hostingEnvironments/workerPools/Read|Récupère les propriétés d’un pool de workers dans un environnement App Service.|
|/hostingEnvironments/workerPools/Write|Crée un pool de workers dans un environnement App Service ou en met un à jour.|
|/hostingenvironments/workerpools/metricdefinitions/read|Récupère les définitions métriques des pools de workers des environnements d’hébergement.|
|/hostingenvironments/workerpools/metrics/read|Récupère les mesures des pools de workers des environnements d’hébergement.|
|/hostingenvironments/workerpools/skus/read|Récupère les références des pools de workers des environnements d’hébergement.|
|/hostingenvironments/workerpools/usages/read|Récupère les utilisations des pools de workers des environnements d’hébergement.|
|/hostingenvironments/sites/read|Récupère les applications web des environnements d’hébergement.|
|/hostingenvironments/serverfarms/read|Récupère les plans App Service des environnements d’hébergement.|
|/hostingenvironments/usages/read|Récupère les utilisations des environnements d’hébergement.|
|/hostingenvironments/capacities/read|Récupère les fonctionnalités des environnements d’hébergement.|
|/hostingenvironments/operations/read|Récupère les opérations des environnements d’hébergement.|
|/hostingEnvironments/multiRolePools/Read|Récupère les propriétés d’un pool frontal dans un environnement App Service.|
|/hostingEnvironments/multiRolePools/Write|Crée un pool frontal dans un environnement App Service ou en met un à jour.|
|/hostingenvironments/multirolepools/metricdefinitions/read|Récupère les définitions métriques des pools multirôles des environnements d’hébergement.|
|/hostingenvironments/multirolepools/metrics/read|Récupère les mesures des pools multirôles des environnements d’hébergement.|
|/hostingenvironments/multirolepools/skus/read|Récupère les références des pools multirôles des environnements d’hébergement.|
|/hostingenvironments/multirolepools/usages/read|Récupère les utilisations des pools multirôles des environnements d’hébergement.|
|/hostingenvironments/diagnostics/read|Récupère les diagnostics des environnements d’hébergement.|
|/publishingusers/read|Récupère la publication des utilisateurs.|
|/publishingusers/write|Met à jour la publication des utilisateurs.|
|/checknameavailability/read|Vérifie si le nom de la ressource est disponible.|
|/geoRegions/Read|Récupère la liste des zones géographiques.|
|/sites/Read|Récupère les propriétés d’une application web.|
|/sites/Write|Crée une application web ou en met une à jour.|
|/sites/Delete|Supprimer une application web existante|
|/sites/backup/Action|Crée une sauvegarde d’application web.|
|/sites/publishxml/Action|Récupère le profil de publication xml pour une application web.|
|/sites/publish/Action|Publie une application web.|
|/sites/restart/Action|Redémarre une application web.|
|/sites/start/Action|Démarre une application web.|
|/sites/stop/Action|Arrête une application web.|
|/sites/slotsswap/Action|Permute des emplacements de déploiement d’application web.|
|/sites/slotsdiffs/Action|Récupère les différences de configuration entre l’application web et les emplacements.|
|/sites/applySlotConfig/Action|Applique la configuration d’emplacement d’application web de l’emplacement cible à l’application web actuelle.|
|/sites/resetSlotConfig/Action|Réinitialise la configuration d’application web.|
|/sites/functions/action|Fonctions Web Apps.|
|/sites/listsyncfunctiontriggerstatus/action|Répertorie le statut de déclenchement de fonction de synchronisation Web Apps.|
|/sites/networktrace/action|Trace réseau Web Apps.|
|/sites/newpassword/action|Newpassword Web Apps.|
|/sites/sync/action|Synchronise Web Apps.|
|/sites/operationresults/read|Récupère les résultats de l’opération Web Apps.|
|/sites/webjobs/read|Récupère WebJobs de Web Apps.|
|/sites/backup/read|Récupère la sauvegarde Web Apps.|
|/sites/backup/write|Met à jour la sauvegarde Web Apps.|
|/sites/metricdefinitions/read|Récupère les définitions métriques Web Apps.|
|/sites/metrics/read|Récupère les mesures Web Apps.|
|/sites/continuouswebjobs/delete|Supprime des tâches web continues Web Apps.|
|/sites/continuouswebjobs/read|Récupère des tâches web continues Web Apps.|
|/sites/continuouswebjobs/start/action|Démarre des tâches web continues Web Apps.|
|/sites/continuouswebjobs/stop/action|Arrête des tâches web continues Web Apps.|
|/sites/domainownershipidentifiers/read|Récupère l’identificateur de propriété de domaine Web Apps.|
|/sites/domainownershipidentifiers/write|Met à jour l’identificateur de propriété de domaine Web Apps.|
|/sites/premieraddons/delete|Supprime les modules complémentaires Premier Web Apps.|
|/sites/premieraddons/read|Récupère les modules complémentaires Premier Web Apps.|
|/sites/premieraddons/write|Met à jour les modules complémentaires Premier Web Apps.|
|/sites/triggeredwebjobs/delete|Supprime les tâches web déclenchées dans Web Apps.|
|/sites/triggeredwebjobs/read|Récupère les tâches web déclenchées dans Web Apps.|
|/sites/triggeredwebjobs/run/action|Exécute les tâches web déclenchées dans Web Apps.|
|/sites/hostnamebindings/delete|Supprime les liaisons de nom d’hôte Web Apps.|
|/sites/hostnamebindings/read|Récupère les liaisons de nom d’hôte Web Apps.|
|/sites/hostnamebindings/write|Met à jour les liaisons de nom d’hôte Web Apps.|
|/sites/virtualnetworkconnections/delete|Supprime les connexions de réseau virtuel Web Apps.|
|/sites/virtualnetworkconnections/read|Récupère les connexions de réseau virtuel Web Apps.|
|/sites/virtualnetworkconnections/write|Met à jour les connexions de réseau virtuel Web Apps.|
|/sites/virtualnetworkconnections/gateways/read|Récupère les passerelles de connexions de réseau virtuel Web Apps.|
|/sites/virtualnetworkconnections/gateways/write|Met à jour les passerelles de connexions de réseau virtuel Web Apps.|
|/sites/publishxml/read|Récupère publication XML Web Apps.|
|/sites/hybridconnectionrelays/read|Récupère les relais de connexions hybrides Web Apps.|
|/sites/perfcounters/read|Récupère les compteurs de performances Web Apps.|
|/sites/usages/read|Récupère les utilisations Web Apps.|
|/sites/slots/Write|Crée un emplacement d’application web ou en met un à jour.|
|/sites/slots/Delete|Supprime un emplacement d’application web existant.|
|/sites/slots/backup/Action|Crée une sauvegarde d’emplacement d’application web.|
|/sites/slots/publishxml/Action|Récupère le profil de publication xml pour l’emplacement d’application web.|
|/sites/slots/publish/Action|Publie un emplacement d’application web.|
|/sites/slots/restart/Action|Redémarre un emplacement d’application web.|
|/sites/slots/start/Action|Démarre un emplacement d’application web.|
|/sites/slots/stop/Action|Arrête un emplacement d’application web.|
|/sites/slots/slotsswap/Action|Permute des emplacements de déploiement d’application web.|
|/sites/slots/slotsdiffs/Action|Récupère les différences de configuration entre l’application web et les emplacements.|
|/sites/slots/applySlotConfig/Action|Applique la configuration d’emplacement d’application web de l’emplacement cible à l’emplacement actuel.|
|/sites/slots/resetSlotConfig/Action|Réinitialise la configuration d’emplacement de l’application web.|
|/sites/slots/Read|Récupère les propriétés d’un emplacement de déploiement d’une application web.|
|/sites/slots/newpassword/action|Emplacements Newpassword Web Apps.|
|/sites/slots/sync/action|Synchronise les emplacements Web Apps.|
|/sites/slots/operationresults/read|Récupère les résultats de l’opération des emplacements Web Apps.|
|/sites/slots/webjobs/read|Récupère WebJobs des emplacements Web Apps.|
|/sites/slots/backup/write|Met à jour la sauvegarde des emplacements Web Apps.|
|/sites/slots/metricdefinitions/read|Récupère les définitions métriques des emplacements Web Apps.|
|/sites/slots/metrics/read|Récupère les mesures des emplacements Web Apps.|
|/sites/slots/continuouswebjobs/delete|Supprime des tâches web continues dans les emplacements Web Apps.|
|/sites/slots/continuouswebjobs/read|Récupère des tâches web continues dans les emplacements Web Apps.|
|/sites/slots/continuouswebjobs/start/action|Démarre des tâches web continues dans les emplacements Web Apps.|
|/sites/slots/continuouswebjobs/stop/action|Arrête des tâches web continues dans les emplacements Web Apps.|
|/sites/slots/premieraddons/delete|Supprime les modules complémentaires Premier dans les emplacements Web Apps.|
|/sites/slots/premieraddons/read|Récupère les modules complémentaires Premier dans les emplacements Web Apps.|
|/sites/slots/premieraddons/write|Met à jour les modules complémentaires Premier dans les emplacements Web Apps.|
|/sites/slots/triggeredwebjobs/delete|Supprime les tâches web déclenchées dans les emplacements Web Apps.|
|/sites/slots/triggeredwebjobs/read|Récupère les tâches web déclenchées dans les emplacements Web Apps.|
|/sites/slots/triggeredwebjobs/run/action|Exécute les tâches web déclenchées dans les emplacements Web Apps.|
|/sites/slots/hostnamebindings/delete|Supprime les liaisons de nom d’hôte dans les emplacements Web Apps.|
|/sites/slots/hostnamebindings/read|Récupère les liaisons de nom d’hôte des emplacements Web Apps.|
|/sites/slots/hostnamebindings/write|Met à jour les liaisons de nom d’hôte des emplacements Web Apps.|
|/sites/slots/phplogging/read|Récupère Phplogging des emplacements Web Apps.|
|/sites/slots/virtualnetworkconnections/delete|Supprime les connexions de réseau virtuel dans les emplacements Web Apps.|
|/sites/slots/virtualnetworkconnections/read|Récupère les connexions de réseau virtuel dans les emplacements Web Apps.|
|/sites/slots/virtualnetworkconnections/write|Met à jour les connexions de réseau virtuel dans les emplacements Web Apps.|
|/sites/slots/virtualnetworkconnections/gateways/write|Met à jour les passerelles de connexions de réseau virtuel dans les emplacements Web Apps.|
|/sites/slots/usages/read|Récupère les utilisations des emplacements Web Apps.|
|/sites/slots/hybridconnection/delete|Supprime une connexion hybride dans les emplacements Web Apps.|
|/sites/slots/hybridconnection/read|Récupère une connexion hybride dans les emplacements Web Apps.|
|/sites/slots/hybridconnection/write|Met à jour une connexion hybride dans les emplacements Web Apps.|
|/sites/slots/config/Read|Récupère les paramètres de configuration de l’emplacement d’application web.|
|/sites/slots/config/list/Action|Répertorie les paramètres sensibles de sécurité de l’emplacement d’application web, tels que les informations d’identification de publication, les paramètres d’application et les chaînes de connexion.|
|/sites/slots/config/Write|Met à jour les paramètres de configuration de l’emplacement d’application web.|
|/sites/slots/config/delete|Supprime la configuration des emplacements Web Apps.|
|/sites/slots/instances/read|Récupère les instances des emplacements Web Apps.|
|/sites/slots/instances/processes/read|Récupère les processus des instances des emplacements Web Apps.|
|/sites/slots/instances/deployments/read|Récupère les déploiements des instances des emplacements Web Apps.|
|/sites/slots/sourcecontrols/Read|Récupère les paramètres de configuration du contrôle de code source de l’emplacement d’application Web.|
|/sites/slots/sourcecontrols/Write|Met à jour les paramètres de configuration du contrôle de code source de l’emplacement d’application web.|
|/sites/slots/sourcecontrols/Delete|Supprime les paramètres de configuration du contrôle de code source de l’emplacement d’application web.|
|/sites/slots/restore/read|Récupère la restauration des emplacements Web Apps.|
|/sites/slots/analyzecustomhostname/read|Récupère le nom d’hôte personnalisé d’analyse des emplacements Web Apps.|
|/sites/slots/backups/Read|Récupère les propriétés de sauvegarde des emplacements d’une application web.|
|/sites/slots/backups/list/action|Répertorie les sauvegardes des emplacements Web Apps.|
|/sites/slots/backups/restore/action|Restaure les sauvegardes des emplacements Web Apps.|
|/sites/slots/deployments/delete|Supprime les déploiements des emplacements Web Apps.|
|/sites/slots/deployments/read|Récupère les déploiements des emplacements Web Apps.|
|/sites/slots/deployments/write|Met à jour les déploiements des emplacements Web Apps.|
|/sites/slots/deployments/log/read|Récupère le journal des déploiements des emplacements Web Apps.|
|/sites/hybridconnection/delete|Supprime une connexion hybride Web Apps.|
|/sites/hybridconnection/read|Récupère une connexion hybride Web Apps.|
|/sites/hybridconnection/write|Met à jour une connexion hybride Web Apps.|
|/sites/recommendationhistory/read|Récupère un historique des recommandations Web Apps.|
|/sites/recommendations/Read|Récupère la liste des recommandations pour l’application web.|
|/sites/recommendations/disable/action|Désactive les recommandations des applications web.|
|/sites/config/Read|Récupère les paramètres de configuration des applications web.|
|/sites/config/list/Action|Répertorie les paramètres sensibles de sécurité d’application web, tels que les informations d’identification de publication, les paramètres d’application et les chaînes de connexion.|
|/sites/config/Write|Met à jour les paramètres de configuration d’application web.|
|/sites/config/delete|Supprime la configuration de Web Apps.|
|/sites/instances/read|Récupère des instances Web Apps.|
|/sites/instances/processes/delete|Supprime les processus des instances Web Apps.|
|/sites/instances/processes/read|Récupère les processus des instances Web Apps.|
|/sites/instances/deployments/read|Récupère les déploiements des instances Web Apps.|
|/sites/sourcecontrols/Read|Récupère les paramètres de configuration du contrôle de code source d’application web.|
|/sites/sourcecontrols/Write|Met à jour les paramètres de configuration du contrôle de code source d’application web.|
|/sites/sourcecontrols/Delete|Supprime les paramètres de configuration du contrôle de code source d’application web.|
|/sites/restore/read|Récupère la restauration Web Apps.|
|/sites/analyzecustomhostname/read|Analyse le nom d’hôte personnalisé.|
|/sites/backups/Read|Récupère les propriétés de sauvegarde d’une application web.|
|/sites/backups/list/action|Répertorie les sauvegardes Web Apps.|
|/sites/backups/restore/action|Restaure les sauvegardes Web Apps.|
|/sites/snapshots/read|Récupère les captures instantanées de Web Apps.|
|/sites/functions/delete|Supprime les fonctions Web Apps.|
|/sites/functions/listsecrets/action|Répertorie les fonctions Web Apps des clés secrètes.|
|/sites/functions/read|Récupère les fonctions Web Apps.|
|/sites/functions/write|Met à jour les fonctions Web Apps.|
|/sites/deployments/delete|Supprime les déploiements de Web Apps.|
|/sites/deployments/read|Récupère les déploiements de Web Apps.|
|/sites/deployments/write|Met à jour les déploiements de Web Apps.|
|/sites/deployments/log/read|Récupère le journal des déploiements de Web Apps.|
|/sites/diagnostics/read|Récupère les diagnostics de Web Apps.|
|/sites/diagnostics/workerprocessrecycle/read|Récupère le recyclage du processus Worker des diagnostics Web Apps.|
|/sites/diagnostics/workeravailability/read|Récupère la disponibilité Worker des diagnostics Web Apps.|
|/sites/diagnostics/runtimeavailability/read|Récupère la disponibilité d’exécution des diagnostics Web Apps.|
|/sites/diagnostics/cpuanalysis/read|Récupère l’analyse du processeur dans les diagnostics de Web Apps.|
|/sites/diagnostics/servicehealth/read|Récupère l’état du service dans les diagnostics de Web Apps.|
|/sites/diagnostics/frebanalysis/read|Récupère l’analyse FREB dans les diagnostics de Web Apps.|
|/availablestacks/read|Récupère les piles disponibles.|
|/isusernameavailable/read|Vérifie si le nom d’utilisateur est disponible.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Read|Récupère la liste des API.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Write|Ajoute une nouvelle API ou en met une à jour.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Delete|Supprime une API existante.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Read|Récupère la liste des connexions.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Write|Enregistre une nouvelle connexion ou en met une à jour.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Delete|Supprime une connexion existante.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Read|Lit ConnectionAcls.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Write|Ajout ou met à jour ConnectionAcl.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Delete|Supprime ConnectionAcl.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connectionAcls/Read|Lit ConnectionAcls pour l’API.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Read|Lit ConnectionAcls.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Write|Crée ou met à jour les listes de contrôle d’accès d’API.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Delete|Supprime les listes de contrôle d’accès d’API.|
|/serverfarms/Read|Récupère les propriétés d’un plan App Service.|
|/serverfarms/Write|Crée un plan App Service ou en met un à jour.|
|/serverfarms/Delete|Supprimer un plan App Service existant|
|/serverfarms/restartSites/Action|Redémarre toutes les applications web d’un plan App Service.|
|/serverfarms/operationresults/read|Récupère les résultats de l’opération des plans App Service.|
|/serverfarms/capabilities/read|Récupère les fonctionnalités des plans App Service.|
|/serverfarms/metricdefinitions/read|Récupère les définitions métriques des plans App Service.|
|/serverfarms/metrics/read|Récupère les mesures des plans App Service.|
|/serverfarms/hybridconnectionplanlimits/read|Récupère les limites de plan de connexion hybride des plans App Service.|
|/serverfarms/virtualnetworkconnections/read|Récupère les connexions de réseau virtuel des plans App Service.|
|/serverfarms/virtualnetworkconnections/routes/delete|Supprime les itinéraires des connexions de réseau virtuel des plans App Service.|
|/serverfarms/virtualnetworkconnections/routes/read|Récupère les itinéraires des connexions de réseau virtuel des plans App Service.|
|/serverfarms/virtualnetworkconnections/routes/write|Met à jour les itinéraires des connexions de réseau virtuel des plans App Service.|
|/serverfarms/virtualnetworkconnections/gateways/write|Met à jour les passerelles de connexions de réseau virtuel des plans App Service.|
|/serverfarms/firstpartyapps/settings/delete|Supprime les paramètres des applications principales des plans App Service.|
|/serverfarms/firstpartyapps/settings/read|Récupère les paramètres des applications principales des plans App Service.|
|/serverfarms/firstpartyapps/settings/write|Met à jour les paramètres des applications principales des plans App Service.|
|/serverfarms/sites/read|Récupère les applications web des plans App Service.|
|/serverfarms/workers/reboot/action|Redémarre les Workers des plans App Service.|
|/serverfarms/hybridconnectionrelays/read|Récupère les relais de connexion hybride des plans App Service.|
|/serverfarms/skus/read|Récupère les références des plans App Service.|
|/serverfarms/usages/read|Récupère les usages des plans App Service.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|Récupère les applications web des relais d’espace de noms de connexion hybride des plans App Service.|
|/ishostnameavailable/read|Vérifie si le nom d’hôte est disponible.|
|/connectionGateways/Read|Récupère la liste des passerelles de connexions.|
|/connectionGateways/Write|Crée ou met à jour une passerelle de connexion.|
|/connectionGateways/Delete|Supprime une passerelle de connexion.|
|/connectionGateways/Join/Action|Joint une passerelle de connexion.|
|/classicmobileservices/read|Récupère Classic Mobile Services.|
|/skus/read|Récupère des références.|
|/certificates/Read|Récupère la liste des certificats.|
|/certificates/Write|Ajoute un nouveau certificat ou en met un à jour.|
|/certificates/Delete|Supprime un certificat existant.|
|/operations/read|Récupère les opérations.|
|/recommendations/Read|Récupère la liste des recommandations pour les abonnements.|
|/ishostingenvironmentnameavailable/read|Récupère des éléments si le nom de l’environnement d’hébergement est disponible.|
|/apiManagementAccounts/Read|Récupère la liste des comptes ApiManagementAccounts.|
|/apiManagementAccounts/Write|Ajoute un nouveau compte ApiManagementAccount ou en met un à jour.|
|/apiManagementAccounts/Delete|Supprime un compte ApiManagementAccount existant.|
|/apiManagementAccounts/connectionAcls/Read|Récupère la liste des ACL de connexions.|
|/apiManagementAccounts/apiAcls/Read|Lit ConnectionAcls.|
|/connections/Read|Récupère la liste des connexions.|
|/connections/Write|Crée ou met à jour une connexion.|
|/connections/Delete|Supprime une connexion.|
|/connections/Join/Action|Joint une connexion.|
|/connections/confirmconsentcode/action|Vérifie le code de consentement des connexions.|
|/connections/listconsentlinks/action|Répertorie les liens de consentement pour les connexions.|
|/deploymentlocations/read|Récupère les emplacements de déploiement.|
|/sourcecontrols/read|Récupère les contrôles de code source.|
|/sourcecontrols/write|Met à jour les contrôles de code source.|
|/managedhostingenvironments/read|Récupère les environnements d’hébergement géré.|
|/managedhostingenvironments/sites/read|Récupère les applications web des environnements d’hébergement géré.|
|/managedhostingenvironments/serverfarms/read|Récupère les plans App Service des environnements d’hébergement géré.|
|/locations/managedapis/read|Récupère les API managées des emplacements.|
|/locations/apioperations/read|Récupère les opérations API d’emplacements.|
|/locations/connectiongatewayinstallations/read|Récupère les installations de la passerelle de connexion des emplacements.|
|/listSitesAssignedToHostName/Read|Récupère les noms de sites affectés à un nom d’hôte.|

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer un rôle personnalisé](role-based-access-control-custom-roles.md).
- Passez en revue les [rôles RBAC intégrés](role-based-access-built-in-roles.md).
- Découvrez comment gérer les affectations d’accès [par utilisateur](role-based-access-control-manage-assignments.md) ou [par ressource](role-based-access-control-configure.md). 
- Découvrez comment [afficher les journaux d’activité pour auditer les actions sur les ressources](~/articles/azure-resource-manager/resource-group-audit.md).
