---
title: "Ajouter des propriétaires et des utilisateurs dans Azure DevTest Labs | Microsoft Docs"
description: "Ajouter des propriétaires et des utilisateurs dans Azure DevTest Labs à l’aide du portail Azure ou de PowerShell"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-craic
ms.openlocfilehash: 348952626e13b9ac73ca2ec8e101bf02e416dc9b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Ajouter des propriétaires et des utilisateurs dans Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

L’accès à Azure DevTest Labs est contrôlé par le [contrôle d’accès en fonction du rôle (RBAC) Azure](../active-directory/role-based-access-control-what-is.md). Avec le contrôle d’accès en fonction du rôle, vous pouvez séparer les tâches au sein de votre équipe en *rôles* dans lesquels vous accordez aux utilisateurs uniquement les accès nécessaires pour accomplir leur travail. Trois de ces rôles RBAC sont *Propriétaire*, *Utilisateur de DevTest Labs* et *Collaborateur*. Dans cet article, vous allez découvrir quelles actions peuvent être effectuées dans chacun des trois rôles RBAC principaux. À partir de là, vous allez apprendre à ajouter des utilisateurs à un laboratoire, à la fois via le portail et via un script PowerShell, et à ajouter des utilisateurs au niveau de l’abonnement.

## <a name="actions-that-can-be-performed-in-each-role"></a>Actions qui peuvent être effectuées dans chaque rôle
Il existe trois rôles principaux que vous pouvez attribuer à un utilisateur :

* Propriétaire
* Utilisateur de DevTest Labs
* Contributeur

Le tableau suivant décrit les actions pouvant être effectuées par les utilisateurs dans chacun de ces rôles :

| **Actions que les utilisateurs dans ce rôle peuvent effectuer** | **Utilisateur de DevTest Labs** | **Propriétaire** | **Collaborateur** |
| --- | --- | --- | --- |
| **Tâches de laboratoire** | | | |
| Ajouter des utilisateurs à un laboratoire |Non  |OUI |Non  |
| Mettre à jour les paramètres de coût |Non  |OUI |OUI |
| **Tâches de base de machine virtuelle** | | | |
| Ajouter et supprimer des images personnalisées |Non  |OUI |OUI |
| Ajouter, mettre à jour et supprimer des formules |OUI |OUI |OUI |
| Images Place de marché Azure de liste blanche |Non  |OUI |OUI |
| **Tâches de machine virtuelle** | | | |
| Créer des machines virtuelles |OUI |OUI |OUI |
| Démarrer, arrêter et supprimer des machines virtuelles |Seules les machines virtuelles créées par l’utilisateur |OUI |OUI |
| Mettre à jour les stratégies de machine virtuelle |Non  |OUI |OUI |
| Ajouter des disques de données à des machines virtuelles ou en supprimer de celles-ci |Seules les machines virtuelles créées par l’utilisateur |OUI |OUI |
| **Tâches d’artefact** | | | |
| Ajouter et supprimer des référentiels d’artefact |Non  |OUI |OUI |
| Appliquer des artefacts |OUI |OUI |OUI |

> [!NOTE]
> Lorsqu’un utilisateur crée une machine virtuelle, le rôle **Propriétaire** de la machine virtuelle créée est automatiquement attribué à cet utilisateur.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Ajouter un utilisateur ou un propriétaire au niveau du laboratoire
Les propriétaires et les utilisateurs peuvent être ajoutés au niveau du laboratoire via le portail Azure. Cela comprend les utilisateurs externes qui disposent d’un [compte Microsoft (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account)valide.
Les étapes suivantes vous guident dans le processus d’ajout d’un propriétaire ou d’un utilisateur à un laboratoire dans Azure DevTest Labs :

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.
4. Dans le panneau du laboratoire, sélectionnez **Configuration**. 
5. Dans le panneau **Configuration**, sélectionnez **Utilisateurs**.
6. Dans le panneau **Utilisateurs**, sélectionnez **+Ajouter**.
   
    ![Ajouter un utilisateur](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
7. Dans le panneau **Sélectionner un rôle** , sélectionnez le rôle souhaité. La section [Actions qui peuvent être effectuées dans chaque rôle](#actions-that-can-be-performed-in-each-role) répertorie les différentes actions qui peuvent être effectuées par les utilisateurs dans les rôles Propriétaire, Utilisateur de DevTest Labs et Collaborateur.
8. Dans le panneau **Ajouter des utilisateurs** , entrez l’adresse de messagerie ou le nom de l’utilisateur que vous souhaitez ajouter dans le rôle spécifié. Si l’utilisateur n’est pas trouvé, un message d’erreur expliquant le problème s’affiche. Si l’utilisateur est trouvé, il est répertorié et sélectionné. 
9. Sélectionnez **Sélectionner**.
10. Cliquez sur **OK** pour fermer le panneau **Ajouter un accès**.
11. Lorsque vous revenez sur le panneau **Utilisateurs** , l’utilisateur a été ajouté.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Ajouter un utilisateur externe à un laboratoire à l’aide de PowerShell
Outre l’ajout d’utilisateurs dans le portail Azure, vous pouvez ajouter un utilisateur externe à votre laboratoire à l’aide d’un script PowerShell. Dans l’exemple suivant, modifiez simplement les valeurs des paramètres sous le commentaire **Valeurs à modifier** .
Vous pouvez récupérer les valeurs `subscriptionId`, `labResourceGroup` et `labName` à partir du panneau de laboratoire dans le portail Azure.

> [!NOTE]
> L’exemple de script suppose que l’utilisateur spécifié a été ajouté en tant qu’invité à Active Directory et échoue si ce n’est pas le cas. Pour ajouter un utilisateur qui ne se trouve pas dans Active Directory à un laboratoire, utilisez le portail Azure pour attribuer un rôle à l’utilisateur, comme illustré dans la section [Ajouter un utilisateur ou un propriétaire au niveau du laboratoire](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/en-us/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Login-AzureRmAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzureRmADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Ajouter un utilisateur ou un propriétaire au niveau du laboratoire
Les autorisations Azure sont propagées à partir de l’étendue parent vers l’étendue enfant dans Azure. Par conséquent, les propriétaires d’un abonnement Azure qui contient des laboratoires sont automatiquement les propriétaires de ces laboratoires. Ils sont également propriétaires des machines virtuelles et des autres ressources créées par les utilisateurs du laboratoire et le service Azure DevTest Labs. 

Vous pouvez ajouter des propriétaires supplémentaires à un laboratoire via le panneau du laboratoire dans le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Toutefois, l’étendue d’administration du propriétaire ajouté est plus étroite que l’étendue du propriétaire de l’abonnement. Par exemple, les propriétaires ajoutés n’ont pas un accès complet à certaines ressources qui sont créées dans l’abonnement par le service DevTest Labs. 

Pour ajouter un propriétaire à un abonnement Azure, procédez comme suit :

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Tous les services**, puis sélectionnez **Abonnements** dans la liste.
3. Sélectionnez l’abonnement souhaité.
4. Sélectionnez l’icône **Accès** . 
   
    ![Accéder aux utilisateurs](./media/devtest-lab-add-devtest-user/access-users.png)
5. Dans le panneau **Utilisateurs**, sélectionnez **Ajouter**.
   
    ![Ajouter un utilisateur](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. Dans le panneau **Sélectionner un rôle**, sélectionnez **Propriétaire**.
7. Dans le panneau **Ajouter des utilisateurs** , entrez l’adresse de messagerie ou le nom de l’utilisateur que vous souhaitez ajouter en tant que propriétaire. Si l’utilisateur n’est pas trouvé, vous obtenez un message d’erreur expliquant le problème. Si l’utilisateur est trouvé, cet utilisateur est répertorié sous la zone de texte **Utilisateur** .
8. Sélectionnez le nom d'utilisateur trouvé.
9. Sélectionnez **Sélectionner**.
10. Cliquez sur **OK** pour fermer le panneau **Ajouter un accès**.
11. Lorsque vous revenez sur le panneau **Utilisateurs** , l’utilisateur a été ajouté en tant que propriétaire. Cet utilisateur est désormais propriétaire de tous les laboratoires créés sous cet abonnement et peut par conséquent effectuer des tâches de propriétaire. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

