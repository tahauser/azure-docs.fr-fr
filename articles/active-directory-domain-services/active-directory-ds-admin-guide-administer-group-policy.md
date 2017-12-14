---
title: "Azure Active Directory Domain Services : administrer la stratégie de groupe sur des domaines gérés | Microsoft Docs"
description: "Administrer la stratégie de groupe sur les domaines gérés par les services de domaine Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 316ddc2cbd67cfafaf44318c5baebcd8da366f93
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Administrer la stratégie de groupe sur un domaine géré par les services de domaine Azure Active Directory
Azure Active Directory Domain Services inclut des objets de stratégie de groupe (GPO) intégrés pour les conteneurs « Utilisateurs AADDC » et « Ordinateurs AADDC ». Vous pouvez personnaliser ces GPO pour configurer la stratégie de groupe sur le domaine géré. De plus, les membres du groupe « AAD DC Administrators » peuvent créer leurs propres unités d’organisation personnalisées dans le domaine géré. Ils peuvent également créer des GPO personnalisés et les lier à ces unités d’organisation personnalisées. Les utilisateurs qui appartiennent au groupe « AAD DC Administrators » bénéficient de privilèges d’administration de stratégie de groupe sur le domaine géré.

## <a name="before-you-begin"></a>Avant de commencer
Pour exécuter les tâches indiquées dans cet article, vous avez besoin des éléments suivants :

1. Un **abonnement Azure**valide.
2. Un **répertoire Azure AD** , synchronisé avec un répertoire local ou un répertoire cloud uniquement.
3. **services de domaine Azure AD** , qui doivent être activés pour le répertoire Azure AD. Si ce n’est déjà fait, suivez l’ensemble des tâches décrites dans le [Guide de mise en route](active-directory-ds-getting-started.md).
4. Une **machine virtuelle jointe au domaine** , qui vous permet d’administrer le domaine géré par les services de domaine Azure AD. Si vous ne disposez pas de cette machine, suivez toutes les tâches décrites dans l’article intitulé [Joindre une machine virtuelle Windows Server à un domaine géré](active-directory-ds-admin-guide-join-windows-vm.md).
5. Vous devez vous procurer les informations d’identification d’un **compte d’utilisateur appartenant au groupe « AAD DC Administrators »** dans votre répertoire, afin d’administrer la stratégie de groupe pour votre domaine géré.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Tâche 1 : configurer une machine virtuelle jointe au domaine afin d’administrer la stratégie de groupe à distance pour le domaine géré
Vous pouvez administrer à distance les domaines gérés par les services de domaine Azure Active Directory (AD) par l’intermédiaire des outils d’administration familiers d’Active Directory, par exemple le Centre d’administration Active Directory (ADAC, Active Directory Administrative Center) ou AD PowerShell. De même, la stratégie de groupe du domaine géré peut être administrée à distance via les outils d’administration de la stratégie de groupe.

Les administrateurs du répertoire Azure AD ne disposent pas des privilèges permettant la connexion aux contrôleurs de domaine sur le domaine géré, via le Bureau à distance. Les membres du groupe « AAD DC Administrator  » peuvent administrer à distance la stratégie de groupe des domaines gérés. Ils peuvent utiliser des outils de stratégie de groupe sur un ordinateur Windows Server/Client joint au domaine géré. Il est possible d’installer des outils de stratégie de groupe dans le cadre de la fonctionnalité facultative de gestion de stratégie de groupe sur les machines Windows Server/Client jointes au domaine géré.

La première tâche consiste à configurer une machine virtuelle Windows Server jointe au domaine géré. Pour savoir comment procéder, consultez l’article [Joindre une machine virtuelle Windows Server à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Tâche 2 : installer des outils de stratégie de groupe sur la machine virtuelle
Procédez comme suit pour installer les outils d’administration de stratégie de groupe sur la machine virtuelle jointe au domaine.

1. Accédez au portail Azure. Cliquez sur **Toutes les ressources** dans le panneau gauche. Recherchez et cliquez sur la machine virtuelle que vous avez créée dans la tâche 1.
2. Cliquez sur le bouton **Connecter** sous l’onglet Vue d’ensemble. Un fichier de protocole Remote Desktop Protocol (.rdp) est créé et téléchargé.

    ![Se connecter à une machine virtuelle Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Pour vous connecter à votre machine virtuelle, ouvrez le fichier RDP téléchargé. À l’invite, cliquez sur **Se connecter**. À l’invite de connexion, utilisez les informations d’identification d’un utilisateur appartenant au groupe « AAD DC Administrators ». Par exemple, nous utilisons « bob@domainservicespreview.onmicrosoft.com » dans notre cas. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Cliquez sur Oui ou Continuer pour poursuivre le processus de connexion.
4. Dans l’écran d’accueil, ouvrez **Gestionnaire de serveur**. Dans le volet central de la fenêtre Gestionnaire de serveur, cliquez sur l’option **Ajouter des rôles et fonctionnalités** .

    ![Lancer le gestionnaire de serveur sur la machine virtuelle](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Sur la page **Avant de commencer** de **l’Assistant Ajout de rôles et de fonctionnalités**, cliquez sur **Suivant**.

    ![Page Avant de commencer](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Sur la page **Type d’installation**, laissez l’option **Installation basée sur un rôle ou une fonctionnalité** sélectionnée et cliquez sur **Suivant**.

    ![Page Type d’installation](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Sur la page **Sélection du serveur**, choisissez la machine virtuelle actuelle dans le pool de serveurs, puis cliquez sur **Suivant**.

    ![Page Sélection du serveur](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Sur la page **Rôles de serveurs**, cliquez sur **Suivant**. Nous allons ignorer cette page, car nous ne procédons pas à l’installation des rôles sur le serveur.
9. Sur la page **Fonctionnalités**, sélectionnez la fonctionnalité **Gestion des stratégies de groupe**.

    ![Page Fonctionnalités](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. Sur la page **Confirmation**, cliquez sur **Installer** pour installer la fonctionnalité Gestion des stratégies de groupe sur la machine virtuelle. Une fois l’installation de la fonctionnalité terminée, cliquez sur **Fermer** afin de fermer l’Assistant **Ajout de rôles et de fonctionnalités**.

    ![Page Confirmation](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Tâche 3 : lancer la console de gestion de stratégie de groupe pour administrer la stratégie de groupe
Vous pouvez utiliser la console de gestion de stratégie de groupe sur la machine virtuelle jointe au domaine pour administrer la stratégie de groupe sur le domaine géré.

> [!NOTE]
> Vous devez être membre du groupe « AAD DC Administrators » pour pouvoir administrer la stratégie de groupe dans le domaine géré.
>
>

1. Dans l’écran d’accueil, cliquez sur **Outils d’administration**. La **Console de gestion des stratégies de groupe** installée sur la machine virtuelle doit s’afficher.

    ![Lancer la gestion des stratégies de groupe](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Cliquez sur **Gestion des stratégies de groupe** pour lancer la console de gestion des stratégies de groupe.

    ![Console de stratégie de groupe](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Tâche 4 : personnaliser les objets de stratégie de groupe intégrés
Il existe deux objets de stratégie de groupe (GPO) intégrés : un pour le conteneur « Ordinateurs AADDC » et un autre pour le conteneur « AADDC utilisateurs » dans votre domaine géré. Vous pouvez personnaliser ces objets de stratégie de groupe pour configurer la stratégie de groupe sur le domaine géré.

1. Dans la console **Gestion des stratégies de groupe**, cliquez pour développer les nœuds **Forest: contoso100.com** et **Domains** afin d’afficher les stratégies de groupe de votre domaine géré.

    ![Objets de stratégie de groupe (GPO) intégrés](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Vous pouvez personnaliser ces GPO intégrés pour configurer des stratégies de groupe sur votre domaine géré. Cliquez avec le bouton droit sur le GPO de votre choix, puis cliquez sur **Modifier...**  pour personnaliser le GPO intégré. L’outil Éditeur de la configuration de la stratégie de groupe vous permet de personnaliser le GPO.

    ![Modifier un GPO intégré](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Vous pouvez maintenant utiliser la console **Éditeur de gestion de stratégie de groupe** pour modifier les GPO intégrés. Par exemple, la capture d’écran suivante illustre la personnalisation du GPO « Ordinateurs AADDC » intégré.

    ![Personnaliser un GPO](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Tâche 5 : créer un objet de stratégie de groupe personnalisé
Vous pouvez créer ou importer vos propres objets de stratégie de groupe. Vous pouvez également lier des objets de stratégie de groupe personnalisés à une unité d’organisation personnalisée que vous avez créée dans votre domaine géré. Pour plus d’informations sur la création d’unités d’organisation personnalisées, consultez [Créer une unité d’organisation personnalisée sur un domaine géré](active-directory-ds-admin-guide-create-ou.md).

> [!NOTE]
> Vous devez être membre du groupe « AAD DC Administrators » pour pouvoir administrer la stratégie de groupe dans le domaine géré.
>
>

1. Dans la console **Gestion des stratégies de groupe**, cliquez pour sélectionner votre unité d’organisation personnalisée. Cliquez avec le bouton droit sur l’unité d’organisation, puis cliquez sur **Créer un objet GPO dans ce domaine, et le lier ici**.

    ![Créer un objet de stratégie de groupe personnalisé](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Spécifiez un nom pour le nouvel objet de stratégie de groupe et cliquez sur **OK**.

    ![Spécifier un nom pour l’objet de stratégie de groupe](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Un objet de stratégie de groupe est créé et lié à votre unité d’organisation personnalisée. Cliquez avec le bouton droit sur l’objet de stratégie de groupe et cliquez sur **Modifier** dans le menu.

    ![Objet de stratégie de groupe nouvellement créé](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Vous pouvez personnaliser l’objet de stratégie de groupe nouvellement créé à l’aide de l’**Éditeur de gestion des stratégies de groupe**.

    ![Personnaliser le nouvel objet de stratégie de groupe](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Pour plus d’informations sur l’utilisation de la [Console de gestion de stratégie de groupe](https://technet.microsoft.com/library/cc753298.aspx), consultez le site Technet.

## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de prise en main](active-directory-ds-getting-started.md)
* [Joindre une machine virtuelle Windows Server à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Administrer un domaine géré par les services de domaine Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)
* [Console de gestion des stratégies de groupe](https://technet.microsoft.com/library/cc753298.aspx)
