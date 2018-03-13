---
title: "Contrôle d’accès en fonction des rôles avec REST - Azure AD | Microsoft Docs"
description: "Gestion du contrôle d’accès basé sur les rôles à l’aide de l’API REST"
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: 
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: active-directory
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: rolyon
ms.openlocfilehash: ba25340e41cefe2b7847a39a6c9182cd0fc057d3
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control-with-the-rest-api"></a>Gérer le contrôle d’accès en fonction des rôles à l’aide de l’API REST
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Interface de ligne de commande Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API REST](role-based-access-control-manage-access-rest.md)

Le contrôle d’accès en fonction du rôle (RBAC) vous permet de définir l’accès des utilisateurs, des groupes et des principaux de service en attribuant des rôles dans une étendue déterminée. Cet article explique comment gérer l’accès à l’aide de l’API REST.

## <a name="list-all-role-assignments"></a>Répertorie toutes les affectations de rôle
Répertorie toutes les affectations de rôle de la portée spécifiée et des étendues secondaires.

Pour répertorier les attributions de rôle, vous devez avoir accès à l’opération `Microsoft.Authorization/roleAssignments/read` dans la portée. Tous les rôles intégrés se voient octroyer l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### <a name="request"></a>Requête
Utilisez la méthode **GET** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

1. Remplacez *{scope}* par la portée dont vous souhaitez répertorier les attributions de rôle. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

   * Abonnement : /subscriptions/{subscription-id}  
   * Groupe de ressources : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Remplacez *{api-version}* par 2015-07-01.
3. Remplacez *{filter}* par la condition que vous souhaitez appliquer pour filtrer la liste des attributions de rôle :

   * Répertorier les affectations de rôle pour la portée spécifiée seulement, sans y inclure les affectations de rôles à des étendues secondaires : `atScope()`    
   * Répertorier les affectations de rôle pour un utilisateur, un groupe ou une application spécifique : `principalId%20eq%20'{objectId of user, group, or service principal}'`  
   * Répertorier les affectations de rôle pour un utilisateur spécifique, y compris celles héritées de groupes | `assignedTo('{objectId of user}')`

### <a name="response"></a>response
Code d’état : 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>Obtention d’informations sur une affectation de rôle
Obtient des informations sur une affectation de rôle unique spécifiée par l’identificateur d’affectation de rôle.

Pour obtenir des informations sur une affectation de rôle, vous devez avoir accès à l’opération `Microsoft.Authorization/roleAssignments/read` . Tous les rôles intégrés se voient octroyer l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### <a name="request"></a>Requête
Utilisez la méthode **GET** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

1. Remplacez *{scope}* par la portée dont vous souhaitez répertorier les attributions de rôle. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

   * Abonnement : /subscriptions/{subscription-id}  
   * Groupe de ressources : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Remplacez *{role-assignment-id}* par l’identificateur GUID de l’attribution de rôle.
3. Remplacez *{api-version}* par 2015-07-01.

### <a name="response"></a>response
Code d’état : 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>Créer une affectation de rôle
Créer une affectation de rôle dans la portée spécifiée pour le principal qui octroie le rôle spécifié.

Pour créer une attribution de rôle, vous devez avoir accès à l’opération `Microsoft.Authorization/roleAssignments/write` . Parmi les rôles intégrés, seuls ceux du *propriétaire* et de *l’administrateur des accès utilisateur* se voient accorder l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### <a name="request"></a>Requête
Utilisez la méthode **PUT** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

1. Remplacez *{scope}* par la portée sur laquelle vous souhaitez créer les attributions de rôle. Lorsque vous créez une affectation de rôle pour une portée parent, toutes les portées enfants en héritent. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

   * Abonnement : /subscriptions/{subscription-id}  
   * Groupe de ressources : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1   
   * Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Remplacez *{role-assignment-id}* par un nouveau GUID, qui devient l’identificateur GUID de la nouvelle attribution de rôle.
3. Remplacez *{api-version}* par 2015-07-01.

Pour le corps de la requête, saisissez les valeurs au format suivant :

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Nom de l’élément | Obligatoire | type | Description |
| --- | --- | --- | --- |
| roleDefinitionId |OUI |Chaîne |L’identificateur du rôle. Le format de l’identificateur est : `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId |OUI |Chaîne |objectId du principal Azure AD (utilisateur, groupe ou principal de service) auquel le rôle est affecté. |

### <a name="response"></a>response
Code d’état : 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>Supprimer une affectation de rôle
Supprimez une affectation de rôle au niveau de la portée spécifiée.

Pour supprimer une attribution de rôle, vous devez avoir accès à l’opération `Microsoft.Authorization/roleAssignments/delete` . Parmi les rôles intégrés, seuls ceux du *propriétaire* et de *l’administrateur des accès utilisateur* se voient accorder l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### <a name="request"></a>Requête
Utilisez la méthode **DELETE** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

1. Remplacez *{scope}* par la portée sur laquelle vous souhaitez créer les attributions de rôle. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

   * Abonnement : /subscriptions/{subscription-id}  
   * Groupe de ressources : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Remplacez *{role-assignment-id}* par le GUID de l’ID d’attribution de rôle.
3. Remplacez *{api-version}* par 2015-07-01.

### <a name="response"></a>response
Code d’état : 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>Répertorier tous les rôles
Répertorie tous les rôles disponibles à l’attribution sur la portée spécifiée.

Pour répertorier les rôles, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/read` dans la portée. Tous les rôles intégrés se voient octroyer l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### <a name="request"></a>Requête
Utilisez la méthode **GET** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

1. Remplacez *{scope}* par la portée dont vous souhaitez répertorier les rôles. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

   * Abonnement : /subscriptions/{subscription-id}  
   * Groupe de ressources : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Remplacez *{api-version}* par 2015-07-01.
3. Remplacez *{filter}* par la condition que vous souhaitez appliquer pour filtrer la liste des rôles :

   * Répertorier les rôles disponibles à l’affectation à la portée spécifiée et chacune de ses portées enfants : `atScopeAndBelow()`
   * Rechercher un rôle utilisant le nom d’affichage exact : `roleName%20eq%20'{role-display-name}'`. Utilisez la forme codée de l’URL du nom d’affichage exact du rôle. Par exemple, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>response
Code d’état : 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>Obtention des informations sur un rôle
Obtient des informations sur un rôle unique spécifié par l’identificateur de définition de rôle. Pour obtenir des informations sur un rôle unique en utilisant son nom d’affichage, voir [Répertorier tous les rôles](role-based-access-control-manage-access-rest.md#list-all-roles).

Pour obtenir des informations sur un rôle, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/read` . Tous les rôles intégrés se voient octroyer l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### <a name="request"></a>Requête
Utilisez la méthode **GET** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

1. Remplacez *{scope}* par la portée dont vous souhaitez répertorier les attributions de rôle. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

   * Abonnement : /subscriptions/{subscription-id}  
   * Groupe de ressources : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Remplacez *{role-definition-id}* par l’identificateur de définition GUID de la définition du rôle.
3. Remplacez *{api-version}* par 2015-07-01.

### <a name="response"></a>response
Code d’état : 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>Créer un rôle personnalisé
Créez un rôle personnalisé.

Pour créer un rôle personnalisé, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/write` sur l’ensemble des `AssignableScopes`. Parmi les rôles intégrés, seuls ceux du *propriétaire* et de *l’administrateur des accès utilisateur* se voient accorder l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### <a name="request"></a>Requête
Utilisez la méthode **PUT** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

1. Remplacez *{scope}* par le premier élément *AssignableScope* du rôle personnalisé. Les exemples qui suivent montrent comment spécifier la portée sur différents niveaux :

   * Abonnement : /subscriptions/{subscription-id}  
   * Groupe de ressources : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Remplacez *{role-assignment-id}* par un nouveau GUID, qui devient l’identificateur GUID du nouveau rôle personnalisé.
3. Remplacez *{api-version}* par 2015-07-01.

Pour le corps de la requête, saisissez les valeurs au format suivant :

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nom de l’élément | Obligatoire | type | Description |
| --- | --- | --- | --- |
| Nom |OUI |Chaîne |Identificateur GUID du rôle personnalisé. |
| properties.roleName |OUI |Chaîne |Afficher le nom complet du rôle personnalisé. Taille maximale de 128 caractères. |
| properties.description |Non  |Chaîne |Description du rôle personnalisé. Taille maximale de 1024 caractères. |
| properties.type |OUI |Chaîne |Affectez la valeur « CustomRole ». |
| properties.permissions.actions |OUI |String[] |Tableau de chaînes d’action spécifiant les opérations octroyées par le rôle personnalisé. |
| properties.permissions.notActions |Non  |String[] |Tableau de chaînes d’action spécifiant les opérations à exclure des opérations octroyées par le rôle personnalisé. |
| properties.assignableScopes |OUI |String[] |Tableau des portées dans lesquelles le rôle personnalisé peut être utilisé. |

### <a name="response"></a>response
Code d’état : 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>Mettre à jour un rôle personnalisé
Modifiez un rôle personnalisé.

Pour modifier un rôle personnalisé, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/write` sur l’ensemble des `AssignableScopes`. Parmi les rôles intégrés, seuls ceux du *propriétaire* et de *l’administrateur des accès utilisateur* se voient accorder l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### <a name="request"></a>Requête
Utilisez la méthode **PUT** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

1. Remplacez *{scope}* par le premier élément *AssignableScope* du rôle personnalisé. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

   * Abonnement : /subscriptions/{subscription-id}  
   * Groupe de ressources : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Remplacez *{role-definition-id}* par l’identificateur de définition GUID du rôle personnalisé.
3. Remplacez *{api-version}* par 2015-07-01.

Pour le corps de la requête, saisissez les valeurs au format suivant :

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nom de l’élément | Obligatoire | type | Description |
| --- | --- | --- | --- |
| Nom |OUI |Chaîne |Identificateur GUID du rôle personnalisé. |
| properties.roleName |OUI |Chaîne |Nom complet du rôle personnalisé mis à jour. |
| properties.description |Non  |Chaîne |Description du rôle personnalisé mis à jour. |
| properties.type |OUI |Chaîne |Affectez la valeur « CustomRole ». |
| properties.permissions.actions |OUI |String[] |Tableau de chaînes d’action spécifiant les opérations auxquelles le rôle personnalisé mis à jour octroie l’accès. |
| properties.permissions.notActions |Non  |String[] |Tableau de chaînes d’action spécifiant les opérations à exclure des opérations que le rôle personnalisé octroie. |
| properties.assignableScopes |OUI |String[] |Tableau des portées dans lesquelles le rôle personnalisé mis à jour peut être utilisé. |

### <a name="response"></a>response
Code d’état : 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>Supprimer un rôle personnalisé
Supprimez un rôle personnalisé.

Pour supprimer un rôle personnalisé, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/delete` sur l’ensemble des `AssignableScopes`. Parmi les rôles intégrés, seuls ceux du *propriétaire* et de *l’administrateur des accès utilisateur* se voient accorder l’accès à cette opération. Pour plus d’informations sur les attributions de rôle et la gestion des accès aux ressources Azure, consultez [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md).

### <a name="request"></a>Requête
Utilisez la méthode **DELETE** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dans l’URI, procédez aux changements suivants pour personnaliser votre demande :

1. Remplacez *{scope}* par la portée dont vous souhaitez supprimer la définition de rôle. Les exemples suivants montrent comment spécifier la portée sur différents niveaux :

   * Abonnement : /subscriptions/{subscription-id}  
   * Groupe de ressources : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Remplacez *{role-definition-id}* par l’ID de définition de rôle GUID du rôle personnalisé.
3. Remplacez *{api-version}* par 2015-07-01.

### <a name="response"></a>response
Code d’état : 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

## <a name="next-steps"></a>étapes suivantes

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
