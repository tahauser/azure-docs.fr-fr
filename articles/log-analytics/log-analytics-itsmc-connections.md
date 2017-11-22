---
title: Connexions prises en charge avec IT Service Management Connector dans Azure Log Analytics | Microsoft Docs
description: "Connectez vos produits/services ITSM à IT Service Management Connector dans Azure Log Analytics pour surveiller et gérer les éléments de travail ITSM de manière centralisée."
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: v-jysur
ms.openlocfilehash: e801d484ffb40a0d4aed517a741c45dc76b62b37
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector-preview"></a>Connecter des produits/services ITSM à IT Service Management Connector (préversion)
Cet article fournit des informations sur la façon de connecter votre service/produit de gestion des services informatiques (ITSM, IT Service Management) au connecteur ITSM dans OMS et de gérer de manière centralisée vos éléments de travail. Pour plus d’informations sur le connecteur ITSM, consultez [ITSMC Overview](log-analytics-itsmc-overview.md) (Présentation du connecteur ITSM).

Les produits/services suivants sont pris en charge :

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-oms)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-oms)
- [Provance](#connect-provance-to-it-service-management-connector-in-oms)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-oms"></a>Connecter System Center Service Manager à IT Service Management Connector dans OMS

Les sections suivantes fournissent des détails sur la connexion de votre produit System Center Service Manager au connecteur ITSM dans OMS.

### <a name="prerequisites"></a>Composants requis

Vérifiez que les prérequis suivants sont remplis :

- Connecteur ITSM installé. Pour plus d’informations, consultez [Ajout de la solution IT Service Management Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- L’application web Service Manager (application web) est déployée et configurée. Pour plus d’informations sur l’application web, cliquez [ici](#create-and-deploy-service-manager-web-app-service).
- Connexion hybride créée et configurée. Plus d’informations : [Configurer la connexion hybride](#configure-the-hybrid-connection).
- Versions prises en charge de Service Manager : 2012 R2 ou 2016.
- Rôle utilisateur : [opérateur avancé](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Procédure de connexion

Utilisez la procédure suivante pour connecter votre instance System Center Service Manager au connecteur ITSM :

1. Accédez à **OMS** >**Paramètres** > **Sources connectées**.
2. Sélectionnez **ITSM Connector**, puis cliquez sur **Ajouter une nouvelle connexion**.

    ![Service Manager ](./media/log-analytics-itsmc/itsmc-service-manager-connection.png)
3. Indiquez les informations comme décrit dans le tableau suivant, puis cliquez sur **Enregistrer** pour créer la connexion :

> [!NOTE]
> Tous ces paramètres sont obligatoires.

| **Champ** | **Description** |
| --- | --- |
| **Name**   | Tapez le nom de l’instance System Center Service Manager que vous souhaitez connecter au connecteur ITSM.  Vous utiliserez ce nom ultérieurement lorsque vous configurerez des éléments de travail dans cette instance ou afficherez une analyse de journal détaillée. |
| **Sélectionner un type de connexion**   | Sélectionnez **System Center Service Manager**. |
| **URL du serveur**   | Tapez l’URL de l’application web Service Manager. Pour plus d’informations sur l’application web Service Manager, cliquez [ici](#create-and-deploy-service-manager-web-app-service).
| **ID client**   | Tapez l’ID client que vous avez généré (en utilisant le script automatique) pour authentifier l’application web. Pour plus d’informations sur le script automatisé, cliquez [ici](log-analytics-itsmc-service-manager-script.md).|
| **Clé secrète client**   | Tapez la clé secrète client, générée pour cet ID.   |
| **Étendue de la synchronisation des données**   | Sélectionnez les éléments de travail de Service Manager que vous souhaitez synchroniser via le connecteur ITSM.  Ces éléments de travail sont importés dans Log Analytics. **Options :** incidents, demandes de modification.|
| **Synchroniser les données** | Tapez le nombre de jours passés dont vous souhaitez les données. **Limite maximale** : 120 jours. |
| **Create new configuration item in ITSM solution (Créer un élément de configuration dans la solution ITSM)** | Sélectionnez cette option si vous souhaitez créer les éléments de configuration dans le produit ITSM. Lorsque cette option est sélectionnée, OMS crée les éléments de configuration affectés en tant qu’éléments de configuration (dans le cas d’éléments de configuration non existants) dans le système ITSM pris en charge. **Par défaut** : désactivée. |

**En cas de connexion et de synchronisation réussies** :

- Les éléments de travail sélectionnés dans Service Manager sont importés dans OMS **Log Analytics**. Vous pouvez afficher le résumé de ces éléments de travail sur la vignette d’IT Service Management Connector.

- Dans OMS, vous pouvez créer des incidents à partir d’alertes OMS, d’une recherche dans les journaux ou d’alertes Azure dans cette instance Service Manager.

Plus d’informations, voir : [Créer des éléments de travail ITSM pour des alertes OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts), [Créer des éléments de travail ITSM à partir de journaux OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs) et [Créer des éléments de travail ITSM à partir des alertes Azure](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Créer et déployer l’application web Service Manager

Pour connecter l’instance Service Manager locale au connecteur ITSM dans OMS, Microsoft a créé une application web Service Manager sur GitHub.

Pour configurer l’application Web ITSM pour votre instance Service Manager, procédez comme suit :

- **Déployez l’application web** : déployez l’application web, définissez les propriétés et authentifiez-vous auprès d’Azure AD. Vous pouvez déployer l’application web à l’aide du [script automatisé](log-analytics-itsmc-service-manager-script.md) que Microsoft vous a fourni.
- **Configurez la connexion hybride** - [Configurez cette connexion](#configure-the-hybrid-connection) manuellement.

#### <a name="deploy-the-web-app"></a>Déployer l’application web
Utilisez le [script](log-analytics-itsmc-service-manager-script.md) automatisé pour déployer l’application web, définir les propriétés et vous authentifier auprès d’Azure AD.

Exécutez le script en fournissant les informations requises suivantes :

- Détails de l’abonnement Azure
- Nom de groupe ressources
- Emplacement
- Détails du serveur Service Manager (nom du serveur, domaine, nom d’utilisateur et mot de passe)
- Préfixe de nom de site pour votre application Web
- Espace de noms ServiceBus.

Le script crée l’application web en utilisant le nom que vous avez spécifié (avec quelques chaînes supplémentaires pour le rendre unique). Il génère **l’URL de l’application web**, **l’ID client** et la **clé secrète client**.

Enregistrez les valeurs. Vous les utiliserez lorsque vous créerez une connexion avec le connecteur ITSM.

**Vérifier l’installation de l’application web**

1. Accédez au **portail Azure** > **Ressources**.
2. Sélectionnez l’application web, puis cliquez sur **Paramètres** > **Paramètres de l’application**.
3. Vérifiez les informations sur l’instance Service Manager que vous avez fournies au moment du déploiement de l’application via le script.

### <a name="configure-the-hybrid-connection"></a>Configurer la connexion hybride

Utilisez la procédure suivante pour configurer la connexion hybride qui connecte l’instance Service Manager au connecteur ITSM dans OMS.

1. Recherchez l’application web Service Manager, sous **Ressources Azure**.
2. Cliquez sur **Paramètres** > **Mise en réseau**.
3. Sous **Connexions hybrides**, cliquez sur **Configurer vos points de terminaison de connexion hybride**.

    ![Mise en réseau de connexions hybrides](./media/log-analytics-itsmc/itsmc-hybrid-connection-networking-and-end-points.png)
4. Dans le panneau **Connexions hybrides**, cliquez sur **Ajouter une connexion hybride**.

    ![Ajout d’une connexion hybride](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-add.png)

5. Dans le panneau **Ajouter des connexions hybrides**, cliquez sur **Créer une connexion hybride**.

    ![Nouvelle connexion hybride](./media/log-analytics-itsmc/itsmc-create-new-hybrid-connection.png)

6. Tapez les valeurs suivantes :

    - **Nom du point de terminaison** : spécifiez un nom pour la nouvelle connexion hybride.
    -  **Hôte du point de terminaison** : nom de domaine complet du serveur d’administration de Service Manager.
    - **Port du point de terminaison** : tapez 5724.
    - **Espace de noms Servicebus** : utilisez un espace de noms Servicebus existant ou créez-en un.
    - **Emplacement** : sélectionnez l’emplacement.
    -  **Nom** : spécifiez un nom pour le Servicebus si vous le créez.

    ![Valeurs de connexion hybride](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-values.png)
6. Cliquez sur **OK** pour fermer le panneau **Créer une connexion hybride**, et créez la connexion hybride.

    Une fois la connexion hybride créée, elle s’affiche sous le panneau.

7. Une fois la connexion hybride créée, sélectionnez-la, puis cliquez sur **Ajouter la connexion hybride sélectionnée**.

    ![Nouvelle connexion hybride](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Configurer l’écouteur

Utilisez la procédure suivante pour configurer l’écouteur pour la connexion hybride.

1. Dans le panneau **Connexions hybrides**, cliquez sur **Télécharger le gestionnaire de connexions** et installez-le sur l’ordinateur sur lequel l’instance System Center Service Manager s’exécute.

    Une fois l’installation terminée, l’option **Interface utilisateur du gestionnaire de connexions hybrides** est disponible dans le menu **Démarrer**.

2. Cliquez sur **Interface utilisateur du gestionnaire de connexions hybrides**. Vous êtes invité à entrer vos informations d’identification Azure.

3. Connectez-vous avec vos informations d’identification Azure et sélectionnez votre abonnement dans lequel la connexion hybride a été créée.

4. Cliquez sur **Save**.

Votre connexion hybride est connectée avec succès.

![Connexion hybride réussie](./media/log-analytics-itsmc/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]

> Une fois la connexion hybride créée, vérifiez et testez la connexion en visitant l’application web Service Manager déployée. Assurez-vous que la connexion est établie avant d’essayer de vous connecter au connecteur ITSM dans OMS.

L’illustration suivante présente les détails d’une connexion réussie :

![Test de connexion hybride](./media/log-analytics-itsmc/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-oms"></a>Connecter ServiceNow à IT Service Management Connector dans OMS

Les sections suivantes fournissent des détails sur la connexion de votre produit ServiceNow au connecteur ITSM dans OMS.

### <a name="prerequisites"></a>Composants requis

Vérifiez que les prérequis suivants sont remplis :

- Connecteur ITSM installé. Pour plus d’informations, consultez [Ajout de la solution IT Service Management Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Versions prises en charge de ServiceNow : Fuji, Genève, Helsinki.

Les administrateurs ServiceNow doivent procéder comme suis dans leur instance ServiceNow :
- Générer l’ID client et la clé secrète client pour le produit ServiceNow. Pour plus d’informations sur la génération de l’ID client et de la clé secrète, consultez [Installation d’OAuth](http://wiki.servicenow.com/index.php?title=OAuth_Setup).
- Installer l’application utilisateur pour l’intégration de Microsoft OMS (application ServiceNow). [En savoir plus](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.0 ).
- Créer un rôle utilisateur de l’intégration pour l’application utilisateur installée. Pour plus d’informations sur la création du rôle d’utilisateur de l’intégration, cliquez [ici](#create-integration-user-role-in-servicenow-app).


### <a name="connection-procedure"></a>**Procédure de connexion**
Exécutez la procédure suivante pour créer une connexion ServiceNow :

1. Accédez à **OMS** > **Paramètres** > **Sources connectées**.
2. Sélectionnez **ITSM Connector**, puis cliquez sur **Ajouter une nouvelle connexion**.

    ![Connexion de ServiceNow](./media/log-analytics-itsmc/itsmc-servicenow-connection.png)

3. Indiquez les informations comme décrit dans le tableau suivant, puis cliquez sur **Enregistrer** pour créer la connexion :

> [!NOTE]
> Tous ces paramètres sont obligatoires.

| **Champ** | **Description** |
| --- | --- |
| **Name**   | Tapez le nom de l’instance ServiceNow que vous souhaitez connecter au connecteur ITSM.  Vous utiliserez ce nom ultérieurement dans OMS lorsque vous configurerez des éléments de travail dans cette instance ITSM ou afficherez une analyse de journal détaillée. |
| **Sélectionner un type de connexion**   | Sélectionnez **ServiceNow**. |
| **Nom d’utilisateur**   | Tapez le nom d’utilisateur de l’intégration que vous avez créé dans l’application ServiceNow pour prendre en charge la connexion au connecteur ITSM. Plus d’informations : [Create ServiceNow app user role (Créer un rôle utilisateur pour l’application ServiceNow)](#create-integration-user-role-in-servicenow-app).|
| **Mot de passe**   | Tapez le mot de passe associé à ce nom d’utilisateur. **Remarque** : le nom d’utilisateur et le mot de passe sont utilisés uniquement pour générer des jetons d’authentification. Ils ne sont pas stockés dans le service OMS.  |
| **URL du serveur**   | Tapez l’URL de l’instance ServiceNow que vous souhaitez connecter au connecteur ITSM. |
| **ID client**   | Tapez l’ID client généré précédemment que vous souhaitez utiliser pour l’authentification OAuth2.  Plus d’informations sur la génération de l’ID client et de la clé secrète : [Installation d’OAuth](http://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Clé secrète client**   | Tapez la clé secrète client, générée pour cet ID.   |
| **Étendue de la synchronisation des données**   | Sélectionnez les éléments de travail de ServiceNow que vous souhaitez synchroniser avec OMS via le connecteur ITSM.  Les valeurs sélectionnées sont importées dans Log Analytics.   **Options :** incidents et demandes de modification.|
| **Synchroniser les données** | Tapez le nombre de jours passés dont vous souhaitez les données. **Limite maximale** : 120 jours. |
| **Create new configuration item in ITSM solution (Créer un élément de configuration dans la solution ITSM)** | Sélectionnez cette option si vous souhaitez créer les éléments de configuration dans le produit ITSM. Lorsque cette option est sélectionnée, OMS crée les éléments de configuration affectés en tant qu’éléments de configuration (dans le cas d’éléments de configuration non existants) dans le système ITSM pris en charge. **Par défaut** : désactivée. |


**En cas de connexion et de synchronisation réussies** :

- Les éléments de travail sélectionnés dans la connexion ServiceNow sont importés dans OMS Log Analytics.  Vous pouvez afficher le résumé de ces éléments de travail sur la vignette d’IT Service Management Connector.
- Vous pouvez créer des incidents, des alertes et des événements à partir d’alertes OMS, d’une recherche dans les journaux ou d’alertes Azure dans cette instance ServiceNow.  


Plus d’informations, voir : [Créer des éléments de travail ITSM pour des alertes OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts), [Créer des éléments de travail ITSM à partir de journaux OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs) et [Créer des éléments de travail ITSM à partir des alertes Azure](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Créer un rôle utilisateur de l’intégration dans l’application ServiceNow

Procédez comme suit :

1.  Visitez le [magasin ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.0) et installez **l’application utilisateur pour l’intégration de Microsoft OMS et de ServiceNow** dans votre instance ServiceNow.
2.  Après l’installation, consultez la barre de navigation gauche de l’instance ServiceNow, puis recherchez et sélectionnez l’intégrateur Microsoft OMS.  
3.  Cliquez sur **Liste de vérifications d’installation**.

    L’état **Incomplet** est affiché si le rôle utilisateur doit encore être créé.

4.  Dans les zones de texte situées en regard de **Create integration user** (Créer un utilisateur de l’intégration), entrez le nom de l’utilisateur qui peut se connecter au connecteur ITSM dans OMS.
5.  Entrez le mot de passe de cet utilisateur, puis cliquez sur **OK**.  

>[!NOTE]

> Vous utilisez ces informations d’identification pour établir la connexion ServiceNow dans OMS.

L’utilisateur nouvellement créé est affiché avec les rôles par défaut affectés.

**Rôles par défaut** :
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

Une fois l’utilisateur créé, l’état de l’option **Liste de vérifications d’installation** est défini sur Terminé, et affiche les détails du rôle utilisateur créé pour l’application.

> [!NOTE]

> Pour autoriser un utilisateur à créer des **alertes** et des **événements** dans ServiceNow à partir d’OMS :

> - Vérifiez que vous avez installé le module Gestion des événements dans votre instance ServiceNow.

> - Ajoutez les rôles suivants à l’utilisateur de l’intégration :
>      - evt_mgmt_integration
>      - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-oms"></a>Connecter Provance à IT Service Management Connector dans OMS

Les sections suivantes fournissent des détails sur la connexion de votre produit Provance au connecteur ITSM dans OMS.


### <a name="prerequisites"></a>Composants requis

Vérifiez que les prérequis suivants sont remplis :


- Connecteur ITSM installé. Pour plus d’informations, consultez [Ajout de la solution IT Service Management Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- L’application Provance doit être inscrite auprès d’Azure AD, et l’ID client est mis à disposition. Pour plus d’informations, consultez [Comment configurer votre application pour utiliser la connexion Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

- Rôle utilisateur : administrateur.

### <a name="connection-procedure"></a>Procédure de connexion

Exécutez la procédure suivante pour créer une connexion Provance :

1. Accédez à **OMS** > **Paramètres** > **Sources connectées**.
2. Sélectionnez **ITSM Connector**, puis cliquez sur **Ajouter une nouvelle connexion**.  

    ![Connexion Provance](./media/log-analytics-itsmc/itsmc-provance-connection.png)
3. Indiquez les informations comme décrit dans le tableau suivant, puis cliquez sur **Enregistrer** pour créer la connexion.

> [!NOTE]
> Tous ces paramètres sont obligatoires.

| **Champ** | **Description** |
| --- | --- |
| **Name**   | Tapez le nom de l’instance Provance que vous souhaitez connecter au connecteur ITSM.  Vous utiliserez ce nom ultérieurement dans OMS lorsque vous configurerez des éléments de travail dans cette instance ITSM ou afficherez une analyse de journal détaillée. |
| **Sélectionner un type de connexion**   | Sélectionnez **Provance**. |
| **Nom d’utilisateur**   | Tapez le nom d’utilisateur pouvant se connecter au connecteur ITSM.    |
| **Mot de passe**   | Tapez le mot de passe associé à ce nom d’utilisateur. **Remarque** : le nom d’utilisateur et le mot de passe sont utilisés uniquement pour générer des jetons d’authentification. Ils ne sont pas stockés dans le service OMS.|
| **URL du serveur**   | Tapez l’URL de l’instance Provance que vous souhaitez connecter au connecteur ITSM. |
| **ID client**   | Tapez l’ID client que vous avez généré dans votre instance Provance pour authentifier cette connexion.  Pour plus d’informations sur l’ID client, consultez [Comment configurer votre application pour utiliser la connexion Azure Active Directory](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md). |
| **Étendue de la synchronisation des données**   | Sélectionnez les éléments de travail de Provance que vous souhaitez synchroniser avec OMS via le connecteur ITSM.  Ces éléments de travail sont importés dans Log Analytics.   **Options :** incidents, demandes de modification.|
| **Synchroniser les données** | Tapez le nombre de jours passés dont vous souhaitez les données. **Limite maximale** : 120 jours. |
| **Create new configuration item in ITSM solution (Créer un élément de configuration dans la solution ITSM)** | Sélectionnez cette option si vous souhaitez créer les éléments de configuration dans le produit ITSM. Lorsque cette option est sélectionnée, OMS crée les éléments de configuration affectés en tant qu’éléments de configuration (dans le cas d’éléments de configuration non existants) dans le système ITSM pris en charge. **Par défaut** : désactivée.|

**En cas de connexion et de synchronisation réussies** :

- Les éléments de travail sélectionnés dans la connexion Provance sont importés dans OMS **Log Analytics**.  Vous pouvez afficher le résumé de ces éléments de travail sur la vignette d’IT Service Management Connector.
- Vous pouvez créer des incidents et des événements à partir d’alertes OMS, d’une recherche dans les journaux ou d’alertes Azure dans cette instance Provance.

Plus d’informations, voir : [Créer des éléments de travail ITSM pour des alertes OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts), [Créer des éléments de travail ITSM à partir de journaux OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs) et [Créer des éléments de travail ITSM à partir des alertes Azure](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-oms"></a>Connecter Cherwell à IT Service Management Connector dans OMS

Les sections suivantes fournissent des détails sur la connexion de votre produit Cherwell au connecteur ITSM dans OMS.

### <a name="prerequisites"></a>Composants requis

Vérifiez que les prérequis suivants sont remplis :

- Connecteur ITSM installé. Pour plus d’informations, consultez [Ajout de la solution IT Service Management Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ID client généré. Plus d’informations : [Générer un ID client pour Cherwell](#generate-client-id-for-cherwell).
- Rôle utilisateur : administrateur.

### <a name="connection-procedure"></a>Procédure de connexion

Exécutez la procédure suivante pour créer une connexion Cherwell :

1. Accédez à **OMS** >  **Paramètres** > **Sources connectées**.
2. Sélectionnez **ITSM Connector**, puis cliquez sur **Ajouter une nouvelle connexion**.  

    ![Id utilisateur de Cherwell](./media/log-analytics-itsmc/itsmc-cherwell-connection.png)

3. Indiquez les informations comme décrit dans le tableau suivant, puis cliquez sur **Enregistrer** pour créer la connexion.

> [!NOTE]
> Tous ces paramètres sont obligatoires.

| **Champ** | **Description** |
| --- | --- |
| **Name**   | Tapez le nom de l’instance Cherwell que vous souhaitez connecter au connecteur ITSM.  Vous utiliserez ce nom ultérieurement dans OMS lorsque vous configurerez des éléments de travail dans cette instance ITSM ou afficherez une analyse de journal détaillée. |
| **Sélectionner un type de connexion**   | Sélectionnez **Cherwell**. |
| **Nom d’utilisateur**   | Tapez le nom d’utilisateur Cherwell pouvant se connecter au connecteur ITSM. |
| **Mot de passe**   | Tapez le mot de passe associé à ce nom d’utilisateur. **Remarque** : le nom d’utilisateur et le mot de passe sont utilisés uniquement pour générer des jetons d’authentification. Ils ne sont pas stockés dans le service OMS.|
| **URL du serveur**   | Tapez l’URL de l’instance Cherwell que vous souhaitez connecter au connecteur ITSM. |
| **ID client**   | Tapez l’ID client que vous avez généré dans votre instance Cherwell pour authentifier cette connexion.   |
| **Étendue de la synchronisation des données**   | Sélectionnez les éléments de travail de Cherwell que vous souhaitez synchroniser via le connecteur ITSM.  Ces éléments de travail sont importés dans Log Analytics.   **Options :** incidents, demandes de modification. |
| **Synchroniser les données** | Tapez le nombre de jours passés dont vous souhaitez les données. **Limite maximale** : 120 jours. |
| **Create new configuration item in ITSM solution (Créer un élément de configuration dans la solution ITSM)** | Sélectionnez cette option si vous souhaitez créer les éléments de configuration dans le produit ITSM. Lorsque cette option est sélectionnée, OMS crée les éléments de configuration affectés en tant qu’éléments de configuration (dans le cas d’éléments de configuration non existants) dans le système ITSM pris en charge. **Par défaut** : désactivée. |

**En cas de connexion et de synchronisation réussies** :

- Les éléments de travail sélectionnés dans cette connexion Cherwell sont importés dans OMS Log Analytics. Vous pouvez afficher le résumé de ces éléments de travail sur la vignette d’IT Service Management Connector.
- Vous pouvez créer des incidents et des événements à partir d’alertes OMS, d’une recherche dans les journaux ou d’alertes Azure dans cette instance Cherwell.

Plus d’informations, voir : [Créer des éléments de travail ITSM pour des alertes OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts), [Créer des éléments de travail ITSM à partir de journaux OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs) et [Créer des éléments de travail ITSM à partir des alertes Azure](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Générer un ID client pour Cherwell

Pour générer l’ID client/la clé de Cherwell, procédez comme suit :

1. Connectez-vous à votre instance Cherwell en tant qu’administrateur.
2. Cliquez sur **Sécurité** > **Edit REST API client settings (Modifier les paramètres du client de l’API REST)**.
3. Sélectionnez **Créer un client** > **Clé secrète client**.

    ![Id utilisateur de Cherwell](./media/log-analytics-itsmc/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Étapes suivantes
 - [Create ITSM work items for OMS alerts (Créer des éléments de travail ITSM pour des alertes OMS)](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts)
 - [Create ITSM work items from OMS logs (Créer des éléments de travail ITSM à partir de journaux OMS)](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs)
 - [Créer des éléments de travail ITSM à partir des alertes Azure](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
 - [View log analytics for your connection (Afficher l’analyse des journaux correspondant à votre connexion)](log-analytics-itsmc-overview.md#using-the-solution)
