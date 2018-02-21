---
title: "Créer des rôles personnalisés dans RBAC Azure | Microsoft Docs"
description: "Découvrez comment définir des rôles personnalisés à l’aide du contrôle d’accès en fonction du rôle Azure pour une gestion plus précise des identités dans votre abonnement Azure."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2871ff5eea8fb99040dfab2593d1640d79f51092
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="create-custom-roles-for-azure-role-based-access-control"></a>Créer des rôles personnalisés pour le contrôle d’accès en fonction du rôle Azure
Créez un rôle personnalisé dans le contrôle d’accès en fonction du rôle (RBAC) Azure si aucun des rôles intégrés ne répond à vos besoins d’accès spécifiques. Il est possible de créer des rôles personnalisés à l’aide [d’Azure PowerShell](role-based-access-control-manage-access-powershell.md), de [l’interface de ligne de commande Azure](role-based-access-control-manage-access-azure-cli.md) et de [l’API REST](role-based-access-control-manage-access-rest.md). Comme avec les rôles intégrés, vous pouvez affecter des rôles personnalisés à des utilisateurs, des groupes et des applications dans l’étendue des abonnements, des groupes de ressources et des ressources. Les rôles personnalisés sont stockés dans un client Azure AD et peuvent être partagés entre les abonnements.

Chaque locataire peut créer jusqu’à 2 000 rôles personnalisés. 

Voici un exemple de rôle personnalisé pour surveiller et redémarrer des machines virtuelles :

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>Actions
La propriété **Actions** d’un rôle personnalisé spécifie les opérations Azure auxquelles le rôle accorde l’accès. Il s’agit d’un ensemble de chaînes d’opération qui identifient les opérations sécurisables des fournisseurs de ressources Azure. Les chaînes d’opération sont au format `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. Les chaînes d’opération contenant des caractères génériques (\*) accordent l’accès à toutes les opérations qui correspondent à la chaîne d’opération. Exemple :

* `*/read` accorde l’accès aux opérations de lecture pour tous les types de ressources de l’ensemble des fournisseurs de ressources Azure.
* `Microsoft.Compute/*` accorde l’accès à l’ensemble des opérations pour tous les types de ressources dans le fournisseur de ressources Microsoft.Compute.
* `Microsoft.Network/*/read` accorde l’accès aux opérations de lecture pour tous les types de ressources dans le fournisseur de ressources Microsoft.Network d’Azure.
* `Microsoft.Compute/virtualMachines/*` accorde l’accès à toutes les opérations des machines virtuelles et ses types de ressources enfants.
* `Microsoft.Web/sites/restart/Action` autorise le redémarrage des sites web.

Utilisez `Get-AzureRmProviderOperation` (dans PowerShell) ou `azure provider operations show` (dans l’interface de ligne de commande Azure) pour répertorier les opérations des fournisseurs de ressources Azure. Vous pouvez également utiliser ces commandes pour vérifier qu’une chaîne d’opération est valide et pour développer les chaînes d’opération génériques.

```powershell
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![Capture d’écran PowerShell - Get-AzureRMProviderOperation](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```azurecli
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Capture d’écran de l’interface de ligne de commande Azure ; les opérations du fournisseur azure affichent « Microsoft.Compute/virtualMachines/\*/action » ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
Utilisez la propriété **NotActions** si l’ensemble des opérations que vous souhaitez autoriser est plus facile à définir en excluant les opérations restreintes. L’accès accordé par un rôle personnalisé est déterminé par l’élimination des opérations **NotActions** des opérations **Actions**.

> [!NOTE]
> Si un utilisateur se voit attribuer un rôle qui exclut une opération dans **NotActions**et un second rôle qui accorde l’accès à cette même opération, il est autorisé à effectuer cette opération. **NotActions** n’est pas une règle de refus : il s’agit simplement d’un moyen pratique pour créer un ensemble d’opérations autorisées lorsque des opérations spécifiques doivent être exclues.
>
>

## <a name="assignablescopes"></a>AssignableScopes
La propriété **AssignableScopes** du rôle personnalisé spécifie les étendues (abonnements, groupes de ressources ou ressources) au sein desquelles le rôle personnalisé peut être affecté. Vous pouvez rendre le rôle personnalisé disponible uniquement dans les abonnements ou les groupes de ressources qui le nécessitent afin de ne pas surcharger l’expérience utilisateur pour le reste des abonnements ou des groupes de ressources.

Voici des exemples d’étendues assignables valides :

* “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”, “/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624” : le rôle peut être affecté dans deux abonnements.
* “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e” : le rôle peut être affecté dans un seul abonnement.
* “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network” : le rôle peut uniquement être affecté dans le groupe de ressources Network.

> [!NOTE]
> Vous devez utiliser au moins un abonnement, groupe de ressources ou ID de ressource.
>
>

## <a name="custom-roles-access-control"></a>Contrôle d’accès des rôles personnalisés
La propriété **AssignableScopes** du rôle personnalisé contrôle également les personnes autorisées à afficher, modifier et supprimer le rôle.

* Qui peut créer un rôle personnalisé ?
    Les propriétaires (et les administrateurs de l’accès utilisateur) d’abonnements, de groupes de ressources et de ressources peuvent créer des rôles personnalisés utilisables au sein de ces étendues.
    L’utilisateur qui crée le rôle doit être en mesure d’effectuer l’opération `Microsoft.Authorization/roleDefinition/write` dans l’ensemble des étendues **AssignableScopes** du rôle.
* Qui peut modifier un rôle personnalisé ?
    Les propriétaires (et les administrateurs de l’accès utilisateur) d’abonnements, de groupes de ressources et de ressources peuvent modifier des rôles personnalisés au sein de ces étendues. Les utilisateurs doivent pouvoir effectuer l’opération `Microsoft.Authorization/roleDefinition/write` dans l’ensemble des étendues **AssignableScopes** d’un rôle personnalisé.
* Qui peut afficher des rôles personnalisés ?
    Tous les rôles intégrés dans le contrôle d’accès en fonction du rôle Azure permettent d’afficher les rôles pouvant être affectés. Les utilisateurs qui peuvent effectuer l’opération `Microsoft.Authorization/roleDefinition/read` dans une étendue sont autorisés à afficher les rôles RBAC pouvant être affectés dans cette étendue.

## <a name="see-also"></a>Voir aussi
* [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md): découvrez le Contrôle d’accès en fonction du rôle Azure dans le portail Azure.
* Pour obtenir la liste des opérations disponibles, consultez [Opérations du fournisseur de ressources Azure Resource Manager](role-based-access-control-resource-provider-operations.md).
* Découvrez comment gérer l’accès avec :
  * [PowerShell](role-based-access-control-manage-access-powershell.md)
  * [Interface de ligne de commande Azure](role-based-access-control-manage-access-azure-cli.md)
  * [API REST](role-based-access-control-manage-access-rest.md)
* [RBAC : rôles intégrés](role-based-access-built-in-roles.md): obtenez des informations sur les rôles fournis en standard dans RBAC.
