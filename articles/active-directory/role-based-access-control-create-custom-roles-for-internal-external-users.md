---
title: "Créer des rôles de contrôle d’accès en fonction du rôle personnalisés et les attribuer à des utilisateurs internes et externes dans Azure | Microsoft Docs"
description: "Attribuer des rôles RBAC personnalisés créés à l’aide de PowerShell et d’Azure CLI à des utilisateurs internes et externes"
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
editor: kgremban
ms.assetid: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/06/2017
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 75a45b492c230b19d2f7237f8ea7fe2c49de29bf
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="intro-on-role-based-access-control"></a>Contrôle d’accès en fonction du rôle Azure

Le contrôle d’accès en fonction du rôle (RBAC) est une fonctionnalité exclusive du portail Azure permettant aux propriétaires d’abonnement d’attribuer des rôles granulaires à d’autres utilisateurs afin qu’ils puissent gérer des étendues de ressource spécifiques dans leur environnement.

La fonctionnalité RBAC permet une meilleure gestion de la sécurité pour les grandes organisations et pour les PME travaillant avec des collaborateurs, fournisseurs ou travailleurs indépendants externes qui doivent pouvoir accéder à des ressources spécifiques de votre environnement, mais pas nécessairement à l’ensemble de l’infrastructure ou aux domaines de la facturation. La fonctionnalité RBAC offre la flexibilité de pouvoir être propriétaire d’un seul abonnement Azure géré par le compte d’administrateur (rôle Administrateur du service au niveau d’un abonnement) et d’avoir plusieurs utilisateurs invités à travailler dans le cadre de cet abonnement, mais sans droits d’administration sur celui-ci. Du point de vue de la facturation et de la gestion, la fonctionnalité RBAC s’avère être une option efficace en termes de temps et de gestion pour l’utilisation d’Azure dans diverses circonstances.

## <a name="prerequisites"></a>Prérequis
L’utilisation de la fonctionnalité RBAC dans l’environnement Windows Azure nécessite ce qui suit :

* Avoir un abonnement Azure autonome attribué à l’utilisateur en tant que propriétaire (rôle d’abonnement)
* Avoir le rôle Propriétaire de l’abonnement Azure
* Avoir accès au [portail Azure](https://portal.azure.com)
* Avoir le fournisseur de ressources suivant inscrit pour l’abonnement de l’utilisateur : **Microsoft.Authorization**. Pour plus d’informations sur l’enregistrement des fournisseurs de ressources, voir [Fournisseurs, régions, schémas et versions d’API Resource Manager](../azure-resource-manager/resource-manager-supported-services.md).

> [!NOTE]
> Ni les abonnements Office 365 ni les licences Azure Active Directory (par exemple, Accès à Azure Active Directory) provisionnées à partir du Centre d’administration Office 365 ne sont éligibles pour l’utilisation de la fonctionnalité RBAC.

## <a name="how-can-rbac-be-used"></a>Comment la fonctionnalité RBAC peut être utilisée
La fonctionnalité RBAC peut être appliquée à trois étendues différentes dans Azure. De la plus élevée à la plus basse, ces étendues sont les suivantes :

* Abonnement (la plus élevée)
* Groupe de ressources
* Ressource (niveau d’accès le plus bas offrant des autorisations ciblées pour une étendue de ressource Azure)

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Attribuer des rôles RBAC à l’étendue d’abonnement
Il existe notamment deux cas courants d’utilisation de la fonctionnalité RBAC :

* Invitation d’utilisateurs externes à l’organisation (ne faisant pas partie du client Azure Active Directory de l’utilisateur administrateur) à gérer certaines ressources ou la totalité de l’abonnement.
* Collaboration avec des utilisateurs internes à l’organisation (faisant partie du locataire Azure Active Directory de l’utilisateur) qui appartiennent à différents groupes ou équipes et doivent avoir un accès granulaire à la totalité de l’abonnement ou seulement à certains groupes ou étendues de ressources dans l’environnement

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Octroyer l’accès au niveau d’un abonnement à un utilisateur extérieur à Azure Active Directory
Les rôles RBAC peuvent être octroyés uniquement par des **propriétaires** d’abonnement. Par conséquent, l’utilisateur administrateur doit être connecté avec un nom d’utilisateur auquel ce rôle est pré-attribué ou qui a créé l’abonnement Azure.

Dans le portail Azure, après vous être connecté en tant qu’administrateur, sélectionnez « Abonnements », puis choisissez l’abonnement souhaité.
![Panneau d’abonnement dans le portail Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/0.png) Par défaut, si l’utilisateur administrateur a acheté l’abonnement Azure, il apparaît en tant que **Administrateur de compte**, ce qui correspond au rôle d’abonnement. Pour plus d’informations sur les rôles d’abonnement Azure, voir [Ajouter ou modifier des rôles Administrateur Azure qui gèrent l’abonnement ou les services](/billing/billing-add-change-azure-subscription-administrator.md).

Dans cet exemple, l’utilisateur « alflanigan@outlook.com » est le **Propriétaire** de l’abonnement « Évaluation gratuite » dans le client AAD « Client Azure par défaut ». Étant donné que cet utilisateur est le créateur de l’abonnement Azure avec le compte Microsoft initial « Outlook » (compte Microsoft = Outlook, Live, etc.), le nom de domaine par défaut pour tous les autres utilisateurs ajoutés à ce client sera **« @alflaniganuoutlook.onmicrosoft.com »**. Par conception, la syntaxe du nouveau domaine est formée par assemblage du nom d’utilisateur et du nom domaine de l’utilisateur qui a créé le client, avec ajout de l’extension **« .onmicrosoft.com »**.
De plus, les utilisateurs peuvent se connecter avec un nom de domaine personnalisé dans le locataire (ils doivent d’abord l’ajouter et le vérifier pour le nouveau locataire). Pour plus d’informations sur la vérification d’un nom de domaine personnalisé dans un locataire Azure Active Directory, consultez [Ajouter un nom de domaine personnalisé à votre annuaire](/active-directory/active-directory-add-domain).

Dans cet exemple, l’annuaire « Client par défaut Azure » contient uniquement les utilisateurs dont le nom de domaine est « @alflanigan.onmicrosoft.com ».

Après avoir sélectionné l’abonnement, l’utilisateur administrateur doit cliquer sur **Contrôle d’accès (IAM)**, puis sur **Ajouter un nouveau rôle**.





![fonctionnalité de contrôle d’accès IAM dans le portail Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/1.png)





![ajouter un utilisateur dans la fonctionnalité de contrôle d’accès IAM dans le portail Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/2.png)

L’étape suivante consiste à sélectionner le rôle à attribuer et l’utilisateur à qui le rôle RBAC doit être attribué. Dans le menu déroulant **Rôle**, l’utilisateur administrateur voit uniquement les rôles RBAC intégrés disponibles dans Azure. Pour plus d’explications sur chaque rôle et les étendues qui peuvent lui être attribuées, voir [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](role-based-access-built-in-roles.md).

L’utilisateur administrateur doit ensuite ajouter l’adresse de messagerie de l’utilisateur externe. Le comportement attendu est que l’utilisateur externe n'apparaisse pas dans le client existant. Une fois l’utilisateur externe invité, il est visible sous **Abonnements > Contrôle d’accès (IAM)** avec tous les utilisateurs auxquels un rôle RBAC est actuellement attribué dans l’étendue de l’abonnement.





![ajouter des autorisations au nouveau rôle RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/3.png)





![liste des rôles RBAC au niveau abonnement](./media/role-based-access-control-create-custom-roles-for-internal-external-users/4.png)

L’utilisateur « chessercarlton@gmail.com » a été invité à être **Propriétaire** d’un abonnement « Évaluation gratuite ». Une fois l’invitation envoyée, l’utilisateur externe reçoit un e-mail de confirmation contenant un lien d’activation.
![e-mail d'invitation pour le rôles RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/5.png)

Étant externe à l’organisation, le nouvel utilisateur ne dispose pas de tous les attributs existants dans l’annuaire « Client Azure par défaut ». Ceux-ci sont créés une fois que l’utilisateur externe accepte d’être inscrit dans l’annuaire associé à l’abonnement pour lequel un rôle lui a été attribué.





![e-mail d’invitation pour le rôle RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/6.png)

L’utilisateur externe apparaît désormais dans le locataire Azure Active Directory en tant qu’utilisateur externe, et est visible sur le portail Azure.





![panneau des utilisateurs azure active directory sur le portail azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/7.png)



Dans la vue **Utilisateurs**, les utilisateurs externes sont signalés par le type d’icône différent dans le portail Azure.

Toutefois, l’octroi à un utilisateur externe d’un accès **Propriétaire** ou **Contributeur** à l’étendue **Abonnement** n’autorise pas l’accès à l’annuaire de l’utilisateur administrateur, sauf si l’**Administrateur général** l’autorise. Dans les propriétés de l’utilisateur, le **Type d’utilisateur** qui a deux paramètres communs, **Membre** et **Invité**, peut être identifié. Un membre est un utilisateur inscrit dans l’annuaire, tandis qu’un invité est un utilisateur invité dans l’annuaire à partir d’une source externe. Pour plus d’informations, voir [Comment les administrateurs Azure Active Directory ajoutent des utilisateurs B2B Collaboration](active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Vérifiez qu’une fois les informations d’identification entrées dans le portail, l’utilisateur externe sélectionne l’annuaire approprié auquel se connecter. Le même utilisateur peut avoir accès à plusieurs annuaires et sélectionner l’un d'eux en cliquant sur le nom d’utilisateur dans la partie supérieure droite du portail Azure, puis en choisissant l’annuaire approprié dans la liste déroulante.

Bien qu’étant un invité dans l’annuaire, l’utilisateur externe peut gérer toutes les ressources de l’abonnement Azure. En revanche, il ne peut pas accéder à l’annuaire.





![accès restreint au portail azure active directory](./media/role-based-access-control-create-custom-roles-for-internal-external-users/9.png)

Azure Active Directory et un abonnement Azure n’ont pas de relation enfant-parent comme l’ont d’autres ressources Azure (par exemple, Azure Virtual Machines, Azure Virtual Networks, Web Apps, Stockage Azure, etc.). Ces dernières sont créées, gérées et facturées en relation avec un abonnement Azure, tandis que celui-ci est utilisé pour gérer l’accès à un annuaire Azure. Pour plus d’informations, consultez [Association d’un abonnement Azure à Azure AD](/active-directory/active-directory-how-subscriptions-associated-directory).

Parmi tous les rôles RBAC intégrés, les rôles **Propriétaire** et **Contributeur** offrent un accès en gestion complet à toutes les ressources de l’environnement, à la seule différence qu’un Contributeur ne peut pas créer ou supprimer des rôles RBAC. Les autres rôles intégrés, tels que **Contributeur de machines virtuelles**, offrent un accès en gestion complet uniquement aux ressources indiquées par le nom, quel que soit le **Groupe de ressources** dans lequel ils sont créés.

L’attribution du rôle RBAC intégré **Contributeur de machines virtuelles** au niveau d’un abonnement signifie que l’utilisateur auquel le rôle est attribué :

* peut afficher tous les machines virtuelles, quelle que soit leur date de déploiement, ainsi que les groupes de ressources auxquels elles appartiennent ;
* dispose d’accès en gestion complet aux machines virtuelles faisant partie de l’abonnement ;
* ne peut pas afficher d’autres types de ressources faisant partie de l’abonnement ;
* ne peut apporter aucune modification sur le plan de la facturation.

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Attribuer un rôle RBAC intégré à un utilisateur externe
Pour un autre scénario dans ce test, l’utilisateur externe « alflanigan@gmail.com » est ajouté en tant que **Contributeur de machines virtuelles**.




![rôle prédéfini de contributeur de machines virtuelles](./media/role-based-access-control-create-custom-roles-for-internal-external-users/11.png)

Le comportement normal de cet utilisateur externe avec ce rôle intégré est de voir et gérer uniquement les machines virtuelles et uniquement les ressources adjacentes de Resource Manager nécessaires lors du déploiement. Par défaut, ces rôles limités offrent accès uniquement à leurs ressources correspondantes créées dans le portail Azure.



![Vue d’ensemble du rôle contributeur de machines virtuelles dans le portail Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Octroyer l’accès au niveau d’un abonnement à un utilisateur figurant dans le même annuaire
Le flux du processus est identique à celui de l’ajout d’un utilisateur externe, tant dans la perspective de l’administrateur octroyant le rôle RBAC, que de celle de l’utilisateur auquel l’accès au rôle est octroyé. La différence est que l’utilisateur invité ne reçoit pas d’e-mail d’invitation, car toutes les étendues de ressource au sein de l’abonnement sont disponibles dans le tableau de bord une fois la connexion établie.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Attribution de rôles RBAC au niveau de l’étendue d’un groupe de ressources
L’attribution d’un rôle RBAC au niveau de l’étendue d’un **Groupe de ressources** est un processus identique à celui de l’attribution du rôle au niveau de l’abonnement pour les deux types d’utilisateurs, externes ou internes (au sein du même annuaire). Les utilisateurs auxquels le rôle RBAC est attribué peuvent voir dans leur environnement uniquement le groupe de ressources auquel ils ont accès à partir de l’icône **Groupes de ressources** dans le portail Azure.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Attribuer des rôles RBAC au niveau de l’étendue d’une ressource
L’attribution d’un rôle RBAC au niveau de l’étendue d’une ressource dans Azure est un processus identique à celui de l’attribution du rôle au niveau de l’abonnement ou du groupe de ressources. Le flux de travail est le même pour les deux scénarios. Une fois encore, les utilisateurs auxquels le rôle RBAC est attribué peuvent voir uniquement les éléments auxquels ils ont accès, sous l’onglet **Toutes les ressources** ou directement dans leur tableau de bord.

Un aspect important de la fonctionnalité RBAC, tant au niveau du groupe de ressources qu’à celui de la ressource, est que les utilisateurs doivent vérifier qu’ils se connectent à l’annuaire approprié.





![connexion à un annuaire dans le portail azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Attribuer des rôles RBAC pour un groupe Azure Active Directory
Tous les scénarios utilisant la fonctionnalité RBAC aux trois niveaux d’étendue dans Azure offrent le privilège de pouvoir gérer, déployer et administrer diverses ressources comme un utilisateur assigné sans avoir besoin de gérer un abonnement personnel. Que le rôle RBAC soit attribué à un abonnement, un groupe de ressources ou une ressource, toutes les ressources créées ensuite par les utilisateurs attribués sont facturées sous le seul abonnement Azure auquel ils ont accès. Ainsi, les utilisateurs qui disposent d’autorisations d’administrateur de facturation pour cet abonnement Azure entier ont une vue d’ensemble complète de la consommation, quelle que soit la personne qui gère les ressources.

Pour les entreprises de grande taille, les rôles RBAC peuvent être appliqués de la même façon pour des groupes Azure Active Directory, en considérant que l’utilisateur administrateur souhaite octroyer l’accès granulaire à des équipes ou à des départements entiers plutôt qu’individuellement à chaque utilisateur, et donc en considérant qu’il s’agit d’une option extrêmement efficace sur les plans du temps et de la gestion. Pour illustrer cet exemple, le rôle **Contributeur** a été ajouté à l’un des groupes dans le client au niveau de l’abonnement.





![ajouter un rôle RBAC pour les groupes AAD](./media/role-based-access-control-create-custom-roles-for-internal-external-users/14.png)

Il s’agit de groupes de sécurité provisionnés et gérés uniquement dans Azure Active Directory.

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>Créer un rôle RBAC personnalisé pour ouvrir les demandes de support à l’aide de PowerShell
Les rôles RBAC intégrés disponibles dans Azure garantissent certains niveaux d’autorisation en fonction des ressources disponibles dans l’environnement. Toutefois, si aucun de ces rôles ne répond aux besoins de l’utilisateur administrateur, il est possible de limiter davantage l’accès en créant des rôles RBAC personnalisés.

La création d’un rôle RBAC personnalisé requiert de prendre un rôle intégré, de le modifier, puis de le réimporter dans l’environnement. Le téléchargement et le chargement du rôle sont gérés à l’aide de PowerShell ou d’Azure CLI.

Vous devez comprendre les prérequis de la création d’un rôle personnalisé capable d’accorder un accès granulaire au niveau de l’abonnement et de donner à l’utilisateur invité la possibilité d’ouvrir des demandes de support.

Pour cet exemple, le rôle intégré **Lecteur**, qui permet à l’utilisateur d’afficher toutes les étendues de ressource, mais pas de les modifier ou d’en créer, a été personnalisé pour permettre à l’utilisateur d’ouvrir des demandes de support.

La première action d’exportation du rôle **Lecteur** doit être accomplie dans PowerShell exécuté avec des autorisations élevées d’administrateur.

```
Login-AzureRMAccount

Get-AzureRMRoleDefinition -Name "Reader"

Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json

```





![Capture d’écran de PowerShell pour le rôle RBAC Lecteur](./media/role-based-access-control-create-custom-roles-for-internal-external-users/15.png)

Vous devez extraire le modèle JSON du rôle.





![Modèle JSON pour le rôle RBAC Lecteur personnalisé](./media/role-based-access-control-create-custom-roles-for-internal-external-users/16.png)

Un rôle RBAC classique comprend trois sections principales, **Actions**, **NotActions** et **AssignableScopes**.

La section **Action** répertorie toutes les opérations autorisées pour ce rôle. Il est important de comprendre que chaque action est affectée à partir d’un fournisseur de ressources. Dans ce cas, pour la création de tickets de support, le fournisseur de ressources **Microsoft.Support** doit être répertorié.

Pour voir tous les fournisseurs de ressources disponibles et enregistrés dans votre abonnement, vous pouvez utiliser PowerShell.
```
Get-AzureRMResourceProvider

```
En outre, vous pouvez vérifier la disponibilité de toutes les applets de commande PowerShell pour gérer les fournisseurs de ressources.
    ![Capture d’écran de PowerShell pour la gestion de fournisseur de ressources](./media/role-based-access-control-create-custom-roles-for-internal-external-users/17.png)

Pour restreindre toutes les actions pour un rôle RBAC particulier, les fournisseurs de ressources sont répertoriés sous la section **NotActions**.
Enfin, le rôle RBAC doit obligatoirement contenir les ID d’abonnement explicites correspondant aux emplacements où il est utilisé. Les ID d’abonnement sont répertoriés sous la section **AssignableScopes**. Sans ceux-ci, vous ne serez pas autorisé à importer le rôle dans votre abonnement.

Une fois créé et personnalisé, le rôle RBAC doit être réimporté dans l’environnement.

```
New-AzureRMRoleDefinition -InputFile "C:\rbacrole2.json"

```

Dans cet exemple, le nom de ce rôle RBAC personnalisé est « Niveau d’accès des tickets de support du Lecteur ». Il permet à l’utilisateur d’afficher tout le contenu de l’abonnement et d’ouvrir des demandes de support.

> [!NOTE]
> Les deux seuls rôles RBAC intégrés autorisant l’action d’ouverture de demandes de support sont **Propriétaire** et **Contributeur**. Pour pouvoir ouvrir des demandes de support, un utilisateur doit avoir un rôle RBAC uniquement au niveau de l’étendue de l’abonnement, car toutes les demandes de prise en charge sont créées sur la base d’un abonnement Azure.

Ce nouveau rôle personnalisé a été attribué à un utilisateur du même annuaire.





![capture d’écran d’un rôle RBAC personnalisé importé dans le portail Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/18.png)





![capture d’écran de l’attribution d’un rôle RBAC importé personnalisé à un utilisateur dans le même annuaire](./media/role-based-access-control-create-custom-roles-for-internal-external-users/19.png)





![capture d’écran d’autorisations pour un rôle RBAC importé personnalisé](./media/role-based-access-control-create-custom-roles-for-internal-external-users/20.png)

L’exemple a été détaillé davantage pour mettre en évidence les limites de ce rôle RBAC personnalisé comme suit :
* Peut créer des demandes de support
* Ne peut pas créer d’étendues de ressource (par exemple, machine virtuelle)
* Ne peut pas créer de groupes de ressources





![capture d’écran d’un rôle RBAC personnalisé créant des demandes de support](./media/role-based-access-control-create-custom-roles-for-internal-external-users/21.png)





![capture d’écran d’un rôle RBAC personnalisé ne pouvant pas créer de machine virtuelle](./media/role-based-access-control-create-custom-roles-for-internal-external-users/22.png)





![capture d’écran d’un rôle RBAC personnalisé ne pouvant pas créer de groupe de ressources](./media/role-based-access-control-create-custom-roles-for-internal-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>Créer un rôle RBAC personnalisé pour ouvrir les demandes de support à l’aide d’Azure CLI
S’exécutant sur un Mac et sans avoir accès à PowerShell, Azure CLI est le bon choix.

Les étapes pour la création d’un rôle personnalisé sont les mêmes, à la seule exception que le rôle peut pas être téléchargé dans un modèle JSON à l’aide de l’interface de ligne de commande, mais uniquement affiché dans celle-ci.

Pour cet exemple, j’ai choisi le rôle intégré **Lecteur de secours**.

```

azure role show "backup reader" --json

```





![Capture d’écran d’interface de ligne de commande pour l’affichage du rôle Lecteur de secours (Backup Reader)](./media/role-based-access-control-create-custom-roles-for-internal-external-users/24.png)

Après la modification du rôle dans Visual Studio et la copie des propriétés dans un modèle JSON, le fournisseur de ressources **Microsoft.Support** a été ajouté dans la section **Actions** de façon à ce que cet utilisateur puisse ouvrir des demandes de support tout en continuant d’être un lecteur pour les coffres de sauvegarde. Là encore, il est nécessaire d’ajouter dans la section **AssignableScopes** l’ID de l’abonnement dans lequel ce rôle sera utilisé.

```

azure role create --inputfile <path>

```





![Capture d’écran de l’Interface de ligne de commande pour l’importation de rôle RBAC personnalisé](./media/role-based-access-control-create-custom-roles-for-internal-external-users/25.png)

Le nouveau rôle est désormais disponible dans le portail Azure, et le processus d’attribution est le même que dans les exemples précédents.





![Capture d’écran du portail Azure montrant le rôle RBAC personnalisé créé à l’aide d’Azure CLI 1.0](./media/role-based-access-control-create-custom-roles-for-internal-external-users/26.png)

Depuis la dernière build 2017, Azure Cloud Shell est généralement disponible. Azure Cloud Shell est un complément de l’environnement de développement intégré (IDE) et du portail Azure. Avec ce service, vous bénéficiez d’un interpréteur de commandes basé sur un navigateur, qui est authentifié et hébergé dans Azure, et que vous pouvez utiliser à la place de l’interface de ligne de commande installée sur votre ordinateur.





![Azure Cloud Shell](./media/role-based-access-control-create-custom-roles-for-internal-external-users/27.png)
