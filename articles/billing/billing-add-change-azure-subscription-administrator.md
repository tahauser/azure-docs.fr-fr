---
title: "Ajout ou modification de rôles d’abonnement administrateur Azure | Microsoft Docs"
description: "Décrit comment ajouter ou modifier un coadministrateur, administrateur de services et administrateur de compte Azure"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/04/2018
ms.author: genli
ms.openlocfilehash: dc09f29fec78d408e1560bfa0a943f16ab50c760
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="add-or-change-azure-subscription-administrators"></a>Ajout ou modification des administrateurs d’abonnements Azure

Les administrateurs d’abonnements Azure Classic et Azure [RBAC (Role-Based Access Control)](../active-directory/role-based-access-control-what-is.md) sont deux systèmes de gestion de l’accès aux ressources Azure :

* Les rôles d’administrateur d’abonnements Classic offrent une gestion des accès de base et incluent l’administrateur de compte, l’administrateur de services fédérés et les coadministrateurs.
    * Lorsque vous souscrivez un nouvel abonnement Azure, votre compte est défini en tant qu’administrateur de compte et administrateur de services fédérés par défaut.
    * Des coadministrateurs peuvent être ajoutés après l’inscription.
* RBAC est un nouveau système qui offre une gestion fine des accès avec de nombreux rôles intégrés, une flexibilité de la portée et des rôles personnalisés.
    * Toutefois, les utilisateurs ayant uniquement des rôles RBAC et aucun rôle d’administrateur d’abonnements Classic ne peuvent pas gérer les déploiements Azure Classic.

Pour assurer un meilleur contrôle et simplifier la gestion des accès, nous vous recommandons d’utiliser RBAC pour tous vos besoins de gestion des accès. Si cela est possible, nous vous recommandons de reconfigurer les stratégies d’accès existantes à l’aide de RBAC. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Ajouter un administrateur RBAC propriétaire pour un abonnement dans le portail Azure 

Pour ajouter un utilisateur comme administrateur pour l’administration de services d’abonnement Azure, attribuez-lui un rôle de propriétaire RBAC pour l’abonnement. Le rôle Propriétaire a la possibilité de gérer les ressources de l’abonnement que vous avez affecté sans avoir de privilèges d’accès à d’autres abonnements.

1. Visitez [**Abonnements** dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Sélectionnez l’abonnement auquel octroyer un accès.
3. Sélectionnez **Contrôle d’accès (IAM)** dans le menu.
4. Dans la zone **Rôle**, sélectionnez **Propriétaire**. 
5. Dans la zone **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou application Azure AD**. 
6. Dans la zone **Sélectionner**, tapez l’adresse e-mail de l’utilisateur à ajouter comme propriétaire. Sélectionnez l’utilisateur, puis **Enregistrer**.

    ![Capture d’écran montrant le rôle Propriétaire sélectionné](./media/billing-add-change-azure-subscription-administrator/add-role.png)

L’utilisateur obtient ainsi un accès total à toutes les ressources, ainsi que le droit de déléguer l’accès à d’autres personnes. Pour accorder l’accès à une portée différente, comme un groupe de ressources, ouvrez le menu IAM pour cette portée. 

## <a name="add-or-change-co-administrator"></a>Ajouter ou modifier un coadministrateur

Seul un propriétaire peut être ajouté en tant que coadministrateur. Les autres utilisateurs dont les rôles sont Contributeur et Lecteur ne peuvent pas être ajoutés en tant que coadministrateurs.

> [!TIP]
> Vous devez ajouter le compte « Propriétaire » comme coadministrateur uniquement si l’utilisateur a besoin de gérer les déploiements Azure Classic. Nous vous recommandons d’utiliser RBAC pour toutes les autres opérations.

1. Si vous ne l’avez pas déjà fait, ajoutez un utilisateur en tant que propriétaire en suivant les instructions ci-dessus.
2. **Cliquez avec le bouton droit** sur l’utilisateur Propriétaire que vous venez d’ajouter, puis sélectionnez **Ajouter comme coadministrateur**. Si vous ne voyez pas l’option **Ajouter comme coadministrateur**, actualisez la page ou essayez avec un autre navigateur Internet. 

    ![Capture d’écran d’ajout d’un coadministrateur](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    Pour supprimer l’autorisation de coadministrateur, **cliquez avec le bouton droit** sur l’utilisateur « coadministrateur », puis sélectionnez **Supprimer le coadministrateur**.

    ![Capture d’écran de suppression de coadministrateur](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Modifier l’administrateur de services fédérés d’un abonnement Azure

Seul l’administrateur de compte peut modifier l’administrateur de services fédérés d’un abonnement. Par défaut, au moment de l’inscription, l’administrateur de services est le même que l’administrateur de compte. Si le rôle d’administrateur de services fédérés est affecté à un autre utilisateur, l’administrateur de compte perd l’accès au portail Azure. Toutefois, l’administrateur de compte peut toujours utiliser le centre des comptes pour se réaffecter le rôle d’administrateur de services fédérés à lui-même.

1. Vérifiez que votre scénario est pris en charge en vérifiant les [limites de modification des administrateurs de services fédérés](#limits).
1. Connectez-vous au [Centre des comptes](https://account.windowsazure.com/subscriptions) en tant qu’administrateur de compte.
1. Sélectionnez un abonnement.
1. Sur le côté droit, sélectionnez **Modifier les détails de l’abonnement**.

    ![Capture d’écran montrant le bouton Modifier l’abonnement dans le Centre des comptes](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. Dans la zone **ADMINISTRATEUR DE SERVICES** , entrez l’adresse de messagerie du nouvel administrateur de services fédérés.

    ![Capture d’écran montrant la zone de modification de l’adresse e-mail de l’administrateur de services fédérés](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Limites de modification des administrateurs de services fédérés

* Chaque abonnement est associé à un annuaire Azure AD. Pour identifier l’annuaire auquel est associé l’abonnement, accédez à [**Abonnements**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), puis sélectionnez un abonnement pour voir l’annuaire.
* Si vous êtes connecté avec un compte professionnel ou scolaire, vous pouvez ajouter d’autres comptes à votre organisation en tant qu’administrateur de services fédérés. Par exemple, abby@contoso.com peut ajouter bob@contoso.com en tant qu’administrateur de services fédérés, mais ne peut pas ajouter john@notcontoso.com, sauf si john@notcontoso.com est présent dans l’annuaire de contoso.com. Les utilisateurs connectés avec des comptes professionnels ou scolaires peuvent continuer à ajouter des utilisateurs de comptes Microsoft en tant qu’administrateur de services fédérés.

  | Méthode de connexion | Ajouter l’utilisateur de compte Microsoft comme administrateur de services fédérés ? | Ajouter le compte professionnel ou scolaire de la même organisation comme administrateur de services fédérés ? | Ajouter le compte professionnel ou scolaire d’une autre organisation comme administrateur de services fédérés ? |
  | --- | --- | --- | --- |
  |  Compte Microsoft |Oui |Non  |Non  |
  |  Compte professionnel ou scolaire |Oui |Oui |Non  |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Modifier l’administrateur de compte d’un abonnement Azure

L’administrateur de compte est l’utilisateur qui a initialement souscrit l’abonnement Azure et qui est responsable en tant que propriétaire de facturation de l’abonnement. Pour modifier l’administrateur de compte d’un abonnement, consultez [Transférer la propriété d’un abonnement Azure à un autre compte](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Besoin d’identifier l’administrateur de compte ?** Procédez comme suit :

1. Visitez [**Abonnements** dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez l’abonnement que vous souhaitez vérifier, puis regardez sous **Paramètres**.
1. Sélectionner **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte**.  

## <a name="types-of-classic-subscription-admins"></a>Types d’administrateur d’abonnements Classic

 Dans Azure, les trois types de rôle d’administrateur d’abonnements Classic sont Administrateur de compte, Administrateur de services fédérés et Coadministrateur. Le compte qui est utilisé pour l’inscription à Azure est automatiquement défini en tant qu’administrateur de compte et administrateur de services fédérés. Par la suite, des coadministrateurs peuvent être ajoutés. Le tableau suivant décrit les différences exactes entre ces trois rôles d’administrateur. 

> [!TIP]
> Pour un meilleur contrôle et une gestion fine des accès, nous vous recommandons l’utilisation d’Azure RBAC (Role-Based Access Control), qui permet aux utilisateurs d’être ajoutés à plusieurs rôles. Pour en savoir plus, consultez la rubrique [Contrôle d’accès en fonction du rôle Azure Active Directory](../active-directory/role-based-access-control-what-is.md).

| Administrateur d’abonnements classiques | Limite | Description |
| --- | --- | --- |
| Administrateur de compte |1 par compte Azure |Il s’agit de l’utilisateur qui a souscrit l’abonnement Azure et qui est autorisé à accéder au [Centre des comptes](https://account.azure.com/Subscriptions) et à effectuer diverses tâches de gestion. Ces tâches incluent la possibilité de créer et d’annuler des abonnements, de modifier la facturation d’un abonnement et de changer l’administrateur de services fédérés. Sur le plan conceptuel, l’administrateur de compte est le propriétaire de facturation de l’abonnement. Dans RBAC, l’administrateur de compte ne reçoit pas de rôle.|
| Administrateur de services fédérés |1 par abonnement Azure |Ce rôle est autorisé à gérer les services sur le [portail Azure](https://portal.azure.com). Par défaut, pour un nouvel abonnement, l’administrateur de compte est également l’administrateur de services fédérés. Dans RBAC, le rôle de propriétaire est donné à l’administrateur de services fédérés pour l’abonnement.|
| Coadministrateur (CA) |200 par abonnement |Ce rôle possède les mêmes privilèges d’accès que l’administrateur de services fédérés, à ceci près qu’il ne peut pas modifier la manière dont les abonnements sont associés aux répertoires Azure. Dans RBAC, le rôle de propriétaire est donné au coadministrateur pour l’abonnement.|

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>En savoir plus sur le contrôle d’accès aux ressources et Active Directory

* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, consultez la page[Présentation de l’accès aux ressources dans Azure](../active-directory/active-directory-understanding-resource-access.md).
* Pour plus d’informations sur Azure Active Directory, consultez les pages [Association des abonnements Azure avec Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) et [Affecter des rôles Administrateur dans Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
