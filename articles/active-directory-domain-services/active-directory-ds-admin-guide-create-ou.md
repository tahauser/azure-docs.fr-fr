---
title: "Azure Active Directory Domain Services : guide d’administration | Microsoft Docs"
description: "Créer une UO sur des domaines gérés par les services de domaine Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 197696d737e56cbdc9fe925b6fa5b9e4134e1539
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Créer une UO sur un domaine géré par les services de domaine Azure Active Directory
Les domaines gérés par les services de domaine Azure Active Directory incluent deux conteneurs intégrés, appelés « Ordinateurs AADDC » et « Utilisateurs AADDC », respectivement. Le conteneur « Ordinateurs AADDC » inclut des objets ordinateur associés à tous les ordinateurs qui sont joints au domaine géré. Le conteneur « Utilisateurs AADDC » comprend les utilisateurs et les groupes du client Azure AD. Parfois, il peut être nécessaire de créer des comptes de service sur le domaine géré, afin de déployer des charges de travail. Pour cela, vous pouvez créer une unité d’organisation (UO) personnalisée dans le domaine géré, puis générer des comptes de service dans cette UO. Cet article vous explique comment créer une UO dans votre domaine géré.

## <a name="before-you-begin"></a>Avant de commencer
Pour exécuter les tâches indiquées dans cet article, vous avez besoin des éléments suivants :

1. Un **abonnement Azure**valide.
2. Un **répertoire Azure AD** , synchronisé avec un répertoire local ou un répertoire cloud uniquement.
3. **services de domaine Azure AD** , qui doivent être activés pour le répertoire Azure AD. Si ce n’est déjà fait, suivez l’ensemble des tâches décrites dans le [Guide de mise en route](active-directory-ds-getting-started.md).
4. Une machine virtuelle jointe au domaine, qui vous permet d’administrer le domaine géré par les services de domaine Azure AD. Si vous ne disposez pas de cette machine, suivez toutes les tâches décrites dans l’article intitulé [Joindre une machine virtuelle Windows Server à un domaine géré](active-directory-ds-admin-guide-join-windows-vm.md).
5. Vous devez vous procurer les informations d’identification d’un **compte d’utilisateur appartenant au groupe « AAD DC Administrators »** dans votre répertoire, pour créer une UO personnalisée dans votre domaine géré.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Installer les outils d’administration Active Directory sur une machine virtuelle jointe à un domaine à des fins de gestion à distance
Vous pouvez administrer à distance les domaines gérés par les services de domaine Azure Active Directory (AD) par l’intermédiaire des outils d’administration familiers d’Active Directory, par exemple le Centre d’administration Active Directory (ADAC, Active Directory Administrative Center) ou AD PowerShell. Les administrateurs clients ne disposent pas des privilèges permettant la connexion aux contrôleurs de domaine sur le domaine géré, via le Bureau à distance. Pour administrer le domaine géré, installez les outils d’administration Active Directory sur une machine virtuelle jointe au domaine géré. Pour savoir comment faire, voir [Administer an Azure AD Domain Services managed domain (Administrer un domaine géré par les services de domaine Azure AD)](active-directory-ds-admin-guide-administer-domain.md) .

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Créer une UO sur le domaine géré
Maintenant que les outils d’administration AD sont installés sur la machine virtuelle jointe au domaine, nous pouvons les utiliser pour créer une unité d’organisation sur le domaine géré. Procédez comme suit :

> [!NOTE]
> Seuls les membres du groupe « AAD DC Administrators » disposent des privilèges requis pour créer une UO personnalisée. Veillez à effectuer les étapes suivantes en tant qu’utilisateur appartenant à ce groupe.
>
>

1. Dans l’écran d’accueil, cliquez sur **Outils d’administration**. Les outils d’administration AD doivent apparaître comme installés sur la machine virtuelle.

    ![Outils d’administration installés sur le serveur](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Cliquez sur **Centre d’administration Active Directory**.

    ![Centre d’administration Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Pour afficher le domaine, cliquez sur son nom dans le volet gauche (par exemple, contoso100.com).

    ![ADAC - Affichage du domaine](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. Sur le volet **Tâches** affiché à droite, cliquez sur l’option **Nouveau** figurant sous le nœud du nom de domaine. Dans cet exemple, nous cliquons sur l’option **Nouveau** figurant sous le nœud « contoso100(local) » dans le volet **Tâches** affiché à droite.

    ![ADAC - Nouvelle unité d’organisation](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. Vous devez voir apparaître l’option permettant de créer une UO. Cliquez sur **Unité d’organisation** pour afficher la boîte de dialogue **Créer une unité d’organisation**.
6. Dans la boîte de dialogue **Créer une unité d’organisation**, indiquez une valeur dans le champ **Nom** de l’UO. Décrivez brièvement l’UO. Vous pouvez également renseigner le champ **Gérée par** de l’UO. Cliquez sur **OK**pour créer l’UO personnalisée.

    ![ADAC - Boîte de dialogue Créer une unité d’organisation](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. L’unité d’organisation créée doit désormais s’afficher dans le centre ADAC.

    ![ADAC - Unité d’organisation créée](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>Sécurité/autorisations associées aux nouvelles unités d’organisation
Par défaut, l’utilisateur (membre du groupe « AAD DC Administrators ») ayant créé l’UO personnalisée bénéficie de privilèges d’administration (contrôle total) concernant l’unité d’organisation. L’utilisateur peut ensuite accorder des privilèges à d’autres utilisateurs ou au groupe « AAD DC Administrators », au besoin. Comme indiqué dans la capture d’écran ci-dessous, l’utilisateur « bob@domainservicespreview.onmicrosoft.com », qui a créé l’unité d’organisation « MyCustomOU », bénéficie d’un contrôle absolu sur cette dernière.

 ![ADAC - Sécurité de la nouvelle UO](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Remarques sur l’administration des unités d’organisation (UO) personnalisées
Maintenant que vous avez créé une UO personnalisée, vous pouvez créer des utilisateurs, des groupes, des ordinateurs et des comptes de service dans cette dernière. Vous ne pouvez pas déplacer des utilisateurs ou des groupes de l’UO Utilisateurs AADDC vers des UO personnalisées.

> [!WARNING]
> Les comptes de service, groupes, objets ordinateur et comptes d’utilisateur que vous créez dans un UO personnalisée ne sont pas disponible sur votre client Azure AD. En d’autres termes, ces objets ne s’affichent pas via l’API Azure AD Graph ou dans l’interface utilisateur Azure AD. Ces objets sont uniquement disponibles dans votre domaine géré par les services de domaine Azure AD.
>
>

## <a name="related-content"></a>Contenu connexe
* [Administrer un domaine géré par les services de domaine Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)
* [Configurer la stratégie de groupe sur un domaine géré](active-directory-ds-admin-guide-administer-group-policy.md)
* [Centre d’administration Active Directory : Prise en main](https://technet.microsoft.com/library/dd560651.aspx)
* [Guide pas à pas des comptes de service (éventuellement en anglais)](https://technet.microsoft.com/library/dd548356.aspx)
