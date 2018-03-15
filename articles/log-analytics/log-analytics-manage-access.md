---
title: "Gérer les espaces de travail dans Azure Log Analytics | Microsoft Docs"
description: "Vous pouvez gérer les espaces de travail dans Azure Log Analytics avec diverses tâches administratives sur les utilisateurs, comptes, espaces de travail et comptes Azure."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2017
ms.author: magoedte
ms.openlocfilehash: 5121535768b7fb430486c1c2c623e1a3a488858f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="manage-workspaces"></a>Gestion des espaces de travail

Pour gérer l’accès à Log Analytics, effectuez diverses tâches administratives liées aux espaces de travail. Cet article fournit des conseils pratiques et des procédures pour gérer les espaces de travail. Un espace de travail est en fait un conteneur qui inclut des informations de compte et des informations de configuration simple pour le compte. Vous ou d’autres membres de votre organisation pouvez utiliser plusieurs espaces de travail pour gérer différents ensembles de données provenant de tout ou partie de votre infrastructure informatique.

Pour créer un espace de travail, vous devez :

1. J’ai un abonnement Azure.
2. Choisissez un nom d’espace de travail.
3. Associez l’espace de travail à votre abonnement.
4. Choisissez un emplacement géographique.

## <a name="determine-the-number-of-workspaces-you-need"></a>Définition du nombre d’espaces de travail nécessaires
Un espace de travail est une ressource Azure et un conteneur dans lequel les données sont collectées, agrégées, analysées et présentées dans le portail Azure.

Vous pouvez disposer de plusieurs espaces de travail par abonnement Azure et vous pouvez avoir accès à plus d’un espace de travail. Auparavant, on ne pouvait analyser les données que dans l’espace de travail actif, ce qui limitait la capacité à interroger plusieurs espaces de travail définis dans l’abonnement. Il est maintenant possible [d’interroger plusieurs espaces de travail](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-cross-workspace-search), et ainsi d’obtenir une vue d’ensemble des données du système. Cette section décrit dans quelles conditions il peut être utile de créer plusieurs espaces de travail.

Aujourd'hui, un espace de travail fournit :

* un emplacement géographique pour le stockage des données ;
* des données granulaires pour la facturation ;
* l’isolation des données.
* Étendue de la configuration

Compte tenu des caractéristiques précédentes, vous pouvez créer plusieurs espaces de travail si :

* Vous travaillez pour une entreprise globale et vous avez besoin de stocker vos données dans des régions spécifiques pour des raisons de conformité ou de souveraineté des données.
* Vous utilisez Azure et vous souhaitez éviter les frais liés au transfert de données sortantes en configurant un espace de travail dans la même région que les ressources Azure qu’il gère.
* Vous souhaitez allouer les frais à différents services ou groupes d’entreprise en fonction de leur utilisation. Lorsque vous créez un espace de travail pour chaque service ou groupe d’entreprise, votre relevé de facturation ou d’utilisation Azure indique séparément les frais liés à chaque espace de travail.
* Vous êtes un fournisseur de services managés et vous devez isoler les données Log Analytics des autres données de vos clients.
* Vous gérez plusieurs clients et vous souhaitez que chaque client/service/groupe d’entreprise ait uniquement accès à ses propres données.

Lorsque vous utilisez des agents pour collecter des données, vous pouvez [configurer chacun d’entre eux pour qu’il fournisse des rapports à un ou plusieurs espaces de travail](log-analytics-windows-agent.md).

Si vous utilisez System Center Operations Manager, chaque groupe d’administration Operations Manager ne peut être connecté qu’à un seul espace de travail. Toutefois, l’agent Microsoft Monitoring Agent sur l’ordinateur peut être configuré pour envoyer des rapports à Operations Manager et à un autre espace de travail Log Analytics.  

### <a name="workspace-information"></a>Informations sur l’espace de travail

Vous pouvez afficher des détails sur votre espace de travail dans le portail Azure. 

#### <a name="view-workspace-information-in-the-azure-portal"></a>Affichage des informations de l’espace de travail dans le portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Cliquez sur **Tous les services**.  Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Cliquez sur **Log Analytics**.  
    ![Capture d’écran montrant le menu de gauche Azure](./media/log-analytics-manage-access/hub.png)  
3. Dans la page d’abonnements de Log Analytics, sélectionnez un espace de travail.
4. La page de l’espace de travail affiche des détails sur l’espace de travail et des liens vers des informations supplémentaires.  
    ![détails sur l’espace de travail](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Gérer les comptes et les utilisateurs
Chaque espace de travail peut être associé à plusieurs comptes et chaque compte (compte Microsoft ou compte professionnel) peut également avoir accès à plusieurs espaces de travail.

Par défaut, le compte Microsoft ou le compte professionnel qui crée l’espace de travail devient l’administrateur de l’espace de travail.

Il existe deux modèles d’autorisation qui contrôlent l’accès à un espace de travail Log Analytics :

1. Rôles d’utilisateur Log Analytics hérités
2. [Accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md)

Le tableau suivant résume l’accès qui peut être défini à l’aide de chaque modèle d’autorisation :

|                          | Portail Log Analytics | Portail Azure | API (y compris PowerShell) |
|--------------------------|----------------------|--------------|----------------------------|
| Rôles d’utilisateur Log Analytics | OUI                  | Non            | Non                          |
| Accès en fonction du rôle Azure  | OUI                  | OUI          | OUI                        |

> [!NOTE]
> Log Analytics est déplacé pour utiliser l’accès en fonction du rôle Azure en tant que modèle d’autorisation, à la place des rôles d’utilisateur Log Analytics.
>
>

Les rôles d’utilisateur Log Analytics hérités contrôlent uniquement l’accès aux opérations effectuées dans le [portail Log Analytics](https://mms.microsoft.com).

Les activités suivantes nécessitent également des autorisations Azure :

| Action                                                          | Autorisations Azure nécessaires | Notes |
|-----------------------------------------------------------------|--------------------------|-------|
| Ajout et suppression de solutions de gestion                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | |
| Modification du niveau tarifaire                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Affichage des données dans les mosaïques de solution *Sauvegarde* et *Site Recovery* | Administrateur/coadministrateur | Accède aux ressources déployées à l’aide du modèle de déploiement Classic |
| Gestion d’un espace de travail dans le portail Azure                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Gestion de l’accès à Log Analytics à l’aide des autorisations Azure
Pour accorder l’accès à l’espace de travail Log Analytics à l’aide des autorisations Azure, suivez les étapes de la page [Utiliser les attributions de rôle pour gérer l’accès à vos ressources d’abonnement Azure](../active-directory/role-based-access-control-configure.md).

Azure intègre deux rôles utilisateur pour Log Analytics :
- Lecteur Log Analytics
- Contributeur Log Analytics

Les membres du rôle *Lecteur Log Analytics* peuvent effectuer les opérations suivantes :
- Visualisation et recherche de toutes les données d’analyse 
- Visualisation des paramètres d’analyse, notamment la configuration des diagnostics Azure sur toutes les ressources Azure

| type    | Autorisation | Description |
| ------- | ---------- | ----------- |
| Action | `*/read`   | Possibilité de visualiser toutes les ressources et la configuration des ressources. Inclut la visualisation des éléments suivants : <br> État d’extension de machine virtuelle <br> Configuration des diagnostics Azure sur les ressources <br> Totalité des paramètres et propriétés de l’ensemble des ressources |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Possibilité d’exécuter des requêtes à l’aide de la fonction Recherche dans les journaux v2 |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | Possibilité d’exécuter des requêtes à l’aide de la fonction Recherche dans les journaux v1 |
| Action | `Microsoft.Support/*` | Possibilité d’ouvrir des cas de support |
|Non-action | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Interdiction de lecture de la clé d’espace de travail requise pour l’utilisation de l’API de collecte de données et pour l’installation des agents |


Les membres du rôle *Contributeur Log Analytics* peuvent effectuer les opérations suivantes :
- Lecture de toutes les données d’analyse 
- Création et configuration des comptes Automation
- Ajout et suppression de solutions de gestion
- Lecture des clés de compte de stockage 
- Configuration de la collecte de journaux à partir du stockage Azure
- Modification des paramètres d’analyse pour les ressources Azure, notamment :
  - Ajout de l’extension de machine virtuelle à des machines virtuelles
  - Configuration des diagnostics Azure sur toutes les ressources Azure

> [!NOTE] 
> La possibilité d’ajouter une extension de machine virtuelle à une machine virtuelle vous offre un contrôle total sur une machine virtuelle.

| Autorisation | Description |
| ---------- | ----------- |
| `*/read`     | Possibilité de visualiser toutes les ressources et la configuration des ressources. Inclut la visualisation des éléments suivants : <br> État d’extension de machine virtuelle <br> Configuration des diagnostics Azure sur les ressources <br> Totalité des paramètres et propriétés de l’ensemble des ressources |
| `Microsoft.Automation/automationAccounts/*` | Possibilité de créer et configurer les comptes Azure Automation, et notamment d’ajouter et modifier des runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Ajout, mise à jour et suppression d’extensions de machine virtuelle, notamment l’extension Microsoft Monitoring Agent et l’extension Agent OMS pour Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Visualisation de la clé du compte de stockage ; opération requise pour la configuration de Log Analytics pour la lecture des journaux à partir des comptes de stockage Azure |
| `Microsoft.Insights/alertRules/*` | Ajout, mise à jour et suppression de règles d’alerte |
| `Microsoft.Insights/diagnosticSettings/*` | Ajout, mise à jour et suppression de paramètres de diagnostic sur les ressources Azure |
| `Microsoft.OperationalInsights/*` | Ajout, mise à jour et suppression de configuration pour les espaces de travail Log Analytics |
| `Microsoft.OperationsManagement/*` | Ajout et suppression de solutions de gestion |
| `Microsoft.Resources/deployments/*` | Création et suppression de déploiements ; opération requise pour l’ajout et la suppression de solutions, d’espaces de travail et de comptes Automation |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Création et suppression de déploiements ; opération requise pour l’ajout et la suppression de solutions, d’espaces de travail et de comptes Automation |

L’ajout et la suppression d’utilisateurs au niveau d’un rôle utilisateur requièrent les autorisations `Microsoft.Authorization/*/Delete` et `Microsoft.Authorization/*/Write`.

Utilisez ces rôles pour accorder aux utilisateurs l’accès à différentes étendues :
- Abonnement : accès à tous les espaces de travail de l’abonnement
- Groupe de ressources : accès à tous les espaces de travail du groupe de ressources
- Ressource : accès uniquement à l’espace de travail spécifié

Pour créer des rôles avec les autorisations spécifiques nécessaires, utilisez des [rôles personnalisés](../active-directory/role-based-access-control-custom-roles.md).

### <a name="azure-user-roles-and-log-analytics-portal-user-roles"></a>Rôles utilisateur Azure et rôles utilisateur du portail Log Analytics
Si vous disposez au moins de l’autorisation en lecture Azure sur l’espace de travail Log Analytics, vous pouvez ouvrir le portail OMS en cliquant sur la tâche **Portail OMS** lors de l’affichage de l’espace de travail Log Analytics.

Lorsque vous ouvrez le portail OMS, vous passez aux rôles d’utilisateur Log Analytics hérités. Si vous ne disposez pas d’une affectation de rôle dans le portail Log Analytics, le service [vérifie les autorisations Azure dont vous disposez sur l’espace de travail](https://docs.microsoft.com/rest/api/authorization/permissions#Permissions_ListForResource).
Votre affectation de rôle dans le portail OMS est déterminée en utilisant les éléments suivants :

| Conditions                                                   | Rôle d’utilisateur Log Analytics affecté | Notes |
|--------------------------------------------------------------|----------------------------------|-------|
| Votre compte appartient à un rôle d’utilisateur Log Analytics hérité     | Rôle d’utilisateur Log Analytics spécifié | |
| Votre compte n’appartient pas à un rôle d’utilisateur Log Analytics hérité <br> Autorisations Azure complètes pour l’espace de travail (`*`autorisation <sup>1</sup>) | Administrateur ||
| Votre compte n’appartient pas à un rôle d’utilisateur Log Analytics hérité <br> Autorisations Azure complètes pour l’espace de travail (`*`autorisation <sup>1</sup>) <br> *non-actions* de `Microsoft.Authorization/*/Delete` et `Microsoft.Authorization/*/Write` | Contributeur ||
| Votre compte n’appartient pas à un rôle d’utilisateur Log Analytics hérité <br> Autorisation d’accès en lecture Azure | Lecture seule ||
| Votre compte n’appartient pas à un rôle d’utilisateur Log Analytics hérité <br> Les autorisations Azure ne sont pas comprises | Lecture seule ||
| Pour les abonnements gérés par le fournisseur de solutions Cloud (CSP) <br> Le compte auquel vous êtes connecté est dans l’instance Azure Active Directory associée à l’espace de travail | Administrateur | En général, il s’agit du client d’un fournisseur de solutions Cloud |
| Pour les abonnements gérés par le fournisseur de solutions Cloud (CSP) <br> Le compte auquel vous êtes connecté n’est pas dans l’instance Azure Active Directory associée à l’espace de travail | Contributeur | En général, il s’agit du fournisseur de solutions Cloud |

<sup>1</sup> Pour plus d’informations sur les définitions de rôles, reportez-vous à [Autorisations Azure](../active-directory/role-based-access-control-custom-roles.md). Lors de l’évaluation des rôles, une action de `*` n’est pas équivalente à `Microsoft.OperationalInsights/workspaces/*`.

Tenez compte des considérations suivantes concernant le portail Azure :

* Lorsque vous vous connectez dans le portail OMS à l’aide de http://mms.microsoft.com, vous voyez s’afficher la liste **Sélectionner un espace de travail**. Cette liste contient uniquement des espaces de travail dans lesquels vous avez un rôle d’utilisateur Log Analytics. Pour afficher les espaces de travail auxquels vous pouvez accéder avec un abonnement Azure, vous devez indiquer un locataire dans l’URL. Par exemple : `mms.microsoft.com/?tenant=contoso.com`. L’identificateur du locataire représente souvent la dernière partie de l’adresse électronique avec laquelle vous vous connectez.
* Si vous souhaitez accéder directement à un portail auquel vous avez accès par le biais d’autorisations Azure, vous devez spécifier la ressource dans l’URL. Il est possible d’obtenir cette URL à l’aide de PowerShell.

  Par exemple, `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  L’URL a l’aspect suivant : `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`

### <a name="managing-users-in-the-oms-portal"></a>Gestion des utilisateurs dans le portail OMS
Vous pouvez gérer les utilisateurs et les groupes sur l’onglet **Gérer les utilisateurs** sous l’onglet **Comptes** de la page Paramètres.   

![gérer des utilisateurs](./media/log-analytics-manage-access/setup-workspace-manage-users.png)


#### <a name="add-a-user-to-an-existing-workspace"></a>Ajout d’un utilisateur à un espace de travail existant
Procédez comme suit pour ajouter un utilisateur ou un groupe à un espace de travail.

1. Dans le portail OMS, cliquez sur la vignette **Paramètres**.
2. Cliquez sur l’onglet **Comptes**, puis sur l’onglet **Gérer les utilisateurs**.
3. Dans la section **Gérer les utilisateurs**, sélectionnez le type de compte à ajouter : **Compte d’organisation**, **Compte Microsoft** ou **Support Microsoft**.

   * Si vous choisissez Compte Microsoft, saisissez l’adresse électronique de l’utilisateur associé au compte Microsoft.
   * Si vous choisissez Compte d’organisation, saisissez une partie du nom de l’utilisateur/du groupe ou l’alias de messagerie électronique. Une liste d’utilisateurs et de groupes s’affiche alors dans une zone déroulante. Sélectionnez un utilisateur ou un groupe.
   * Utilisez Support Microsoft pour donner à un ingénieur du support technique Microsoft ou à un autre employé de Microsoft un accès temporaire à votre espace de travail dans le but de faciliter le dépannage.

     > [!NOTE]
     > Pour obtenir de meilleurs résultats, limitez à trois le nombre de groupes Active Directory associés à un seul compte OMS : un pour les administrateurs, un pour les collaborateurs et un pour les utilisateurs en lecture seule. L’utilisation d’un plus grand nombre de groupes peut avoir une incidence sur les performances de Log Analytics.
     >
     >
4. Choisissez le type d’utilisateur ou de groupe à ajouter : **Administrateur**, **Collaborateur**, ou **Utilisateur en lecture seule**.  
5. Cliquez sur **Add**.

   Si vous ajoutez un compte Microsoft, une invitation à joindre l’espace de travail est envoyée à l’adresse électronique que vous avez fournie. Une fois que l’utilisateur a suivi les instructions figurant dans l’invitation à rejoindre OMS, il peut accéder à cet espace de travail.
   Si vous ajoutez un compte d’organisation, l’utilisateur peut accéder à Log Analytics immédiatement.  

#### <a name="edit-an-existing-user-type"></a>Modification d’un type d’utilisateur existant
Vous pouvez modifier le rôle de compte pour un utilisateur associé à votre compte OMS. Vous pouvez choisir parmi les rôles suivants :

* *Administrateur*: peut gérer les utilisateurs, afficher et agir sur toutes les alertes, ainsi qu’ajouter et supprimer des serveurs.
* *Collaborateur*: peut afficher toutes les alertes et agir sur celles-ci, ainsi qu’ajouter et supprimer des serveurs.
* *Utilisateur en lecture seule* : les utilisateurs marqués comme étant en lecture seule ne peuvent pas :

  1. Ajouter ou supprimer des solutions. La galerie de solutions est masquée.
  2. Ajouter/modifier/supprimer des mosaïques sur **Mon tableau de bord**.
  3. Afficher les pages **Paramètres**. Les pages sont masquées.
  4. Dans la vue de recherche, la configuration de Power BI, les recherches enregistrées et les tâches relatives aux alertes sont masquées.

#### <a name="to-edit-an-account"></a>Pour modifier un compte
1. Dans le portail OMS, cliquez sur la vignette **Paramètres**.
2. Cliquez sur l’onglet **Comptes**, puis sur l’onglet **Gérer les utilisateurs**.
3. Sélectionnez le rôle de l’utilisateur que vous souhaitez modifier.
4. Cliquez sur **Oui** dans la boîte de dialogue de confirmation.

### <a name="remove-a-user-from-a-workspace"></a>Supprimer un utilisateur d’un espace de travail
Procédez comme suit pour supprimer un utilisateur d’un espace de travail. Notez que cela ne ferme pas l’espace de travail. Au lieu de cela, cela supprime l’association entre cet utilisateur et l’espace de travail. Si un utilisateur est associé à plusieurs espaces de travail, il peut encore se connecter à OMS et afficher les autres espaces de travail.

1. Dans le portail OMS, cliquez sur la vignette **Paramètres**.
2. Cliquez sur l’onglet **Comptes**, puis sur l’onglet **Gérer les utilisateurs**.
3. Cliquez sur l’option **Supprimer** située en regard du nom d’utilisateur à supprimer.
4. Cliquez sur **Oui** dans la boîte de dialogue de confirmation.

### <a name="add-a-group-to-an-existing-workspace"></a>Ajout d’un groupe à un espace de travail existant
1. Dans la section précédente, « Ajout d’un utilisateur à un espace de travail existant », suivez les étapes 1 à 4.
2. Sous **Choisir un utilisateur/groupe**, sélectionnez **Groupe**.  
   ![add a group to an existing workspace](./media/log-analytics-manage-access/add-group.png)
3. Entrez le nom d’affichage ou l’adresse électronique du groupe que vous souhaitez ajouter.
4. Sélectionnez le groupe parmi les résultats, puis cliquez sur **Ajouter**.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Liaison d’un espace de travail existant à un abonnement Azure
Tous les espaces de travail créés après le 26 septembre 2016 doivent être liés à un abonnement Azure lors de la création. Vous devez lier les espaces de travail créés avant cette date lorsque vous vous connectez. Lorsque vous créez l’espace de travail à partir du portail Azure ou que vous liez votre espace de travail à un abonnement Azure, votre répertoire Azure Active Directory est lié en tant que compte d’organisation.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Pour lier un espace de travail à un abonnement Azure dans le portail OMS

- Lorsque vous vous connectez au portail OMS, vous êtes invité à sélectionner un abonnement Azure. Sélectionnez l’abonnement que vous souhaitez lier à votre espace de travail, puis cliquez sur **Lier**.  
    ![lier un abonnement Azure](./media/log-analytics-manage-access/required-link.png)

    > [!IMPORTANT]
    > Pour que vous puissiez lier un espace de travail, votre compte Azure doit déjà y avoir accès.  En d’autres termes, le compte que vous utilisez pour accéder au portail Azure doit être **le même** que celui que vous utilisez pour accéder à votre espace de travail. Si ce n’est pas le cas, consultez [Ajout d’un utilisateur à un espace de travail existant](#add-a-user-to-an-existing-workspace).

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Pour lier un espace de travail à un abonnement Azure dans le portail Azure
1. Connectez-vous au [portail Azure](http://portal.azure.com).
2. Recherchez **Log Analytics** et sélectionnez-le.
3. Vous voyez la liste des espaces de travail existants. Cliquez sur **Add**.  
   ![liste des espaces de travail](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. Sous **Espace de travail OMS**, cliquez sur **Ou liez**.  
   ![lier un élément existant](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. Cliquez sur **Configurer les paramètres requis**.  
   ![configure required settings](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. Vous voyez la liste des espaces de travail qui ne sont pas encore liés à votre compte Azure. Sélectionnez un espace de travail.  
   ![sélectionner des espaces de travail](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. Si nécessaire, vous pouvez modifier les valeurs correspondant aux éléments suivants :
   * Abonnement
   * Groupe de ressources
   * Lieu
   * Niveau tarifaire  
     ![modifier des valeurs](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. Cliquez sur **OK**. L’espace de travail est maintenant lié à votre compte Azure.

> [!NOTE]
> Si l’espace de travail que vous souhaitez lier ne s’affiche pas, cela signifie que votre abonnement Azure n’a pas accès à l’espace de travail que vous avez créé sur le portail OMS.  Pour accorder l’accès à ce compte à partir du portail OMS, consultez [Ajout d’un utilisateur à un espace de travail existant](#add-a-user-to-an-existing-workspace).
>
>

## <a name="change-an-azure-active-directory-organization-for-a-workspace"></a>Modifier une organisation Azure Active Directory pour un espace de travail

Vous pouvez modifier l’organisation Azure Active Directory d’un espace de travail. La modification dune organisation Azure Active Directory vous permet d’ajouter des utilisateurs et groupes de ce répertoire vers l’espace de travail.

### <a name="to-change-the-azure-active-directory-organization-for-a-workspace"></a>Pour modifier l’organisation Azure Active Directory d’un espace de travail

1. Dans la page Paramètres du portail OMS, cliquez sur **Comptes**, puis sur l’onglet **Gérer les utilisateurs**.  
2. Consultez les informations sur les comptes d’organisation, puis cliquez sur **Ajouter une organisation**.  
    ![modifier l’organisation](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Entrez les informations d’identité de l’administrateur de votre domaine Azure Active Directory. Ensuite, une confirmation indiquant que votre espace de travail est lié à votre domaine Azure Active Directory s’affiche.  
    ![confirmation d’espace de travail lié](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

## <a name="next-steps"></a>Étapes suivantes
* [Comprendre l’utilisation de données](log-analytics-usage.md) pour apprendre à analyser le volume de données collectées par les solutions et envoyées par les ordinateurs.
* [Ajouter des solutions de gestion Log Analytics à partir d’Azure Marketplace](log-analytics-add-solutions.md) pour ajouter des fonctionnalités et collecter des données.
