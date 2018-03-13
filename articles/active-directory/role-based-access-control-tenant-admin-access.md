---
title: "Élever l’accès d’un administrateur client - Azure AD | Microsoft Docs"
description: "Cette rubrique décrit les rôles intégrés pour le contrôle d’accès en fonction du rôle (RBAC)."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2017
ms.author: rolyon
ms.openlocfilehash: dff3a26201507f974d52de3fe6dcb23945cd900f
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Élever l’accès en tant qu’administrateur client avec le contrôle d’accès en fonction du rôle

Le contrôle d’accès en fonction du rôle permet aux administrateurs clients d’obtenir des élévations temporaires d’accès grâce auxquelles ils peuvent accorder des autorisations plus élevées que la normale. Un administrateur client peut s’élever lui-même au rôle d’administrateur des accès utilisateur si nécessaire. Ce rôle donne à l’administrateur client les autorisations nécessaires pour accorder à lui-même ou à d’autres des rôles au niveau de l’étendue « / ».

Cette fonctionnalité est importante, car elle permet à l’administrateur client d’afficher tous les abonnements qui existent dans une organisation. Elle permet également aux applications d’automatisation (comme la facturation et les audits) d’accéder à tous les abonnements et de fournir une vue précise de l’état de l’organisation pour la facturation et la gestion des actifs.  

## <a name="use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Utiliser elevateAccess pour l’accès client au centre d’administration Azure AD

1. Accédez au [centre d’administration Azure Active Directory](https://aad.portal.azure.com) et connectez-vous à l’aide de vos informations d’identification.

2. Choisissez **Propriétés** dans le gauche menu d’Azure AD.

3. Recherchez **L’administrateur général peut gérer des abonnements Azure**, choisissez **Oui**, puis **Enregistrer**.
    > [!IMPORTANT] 
    > Lorsque vous choisissez **Oui**, assigne le rôle **Administrateur d’accès utilisateur** au niveau de la racine « / » (étendue de la racine) pour l’utilisateur avec lequel vous êtes actuellement connecté au portail. **Cela permet à l’utilisateur de voir tous les autres abonnements Azure.**
    
    > [!NOTE] 
    > Lorsque vous choisissez **Non**, retire le rôle **Administrateur d’accès utilisateur** au niveau de la racine « / » (étendue de la racine) pour l’utilisateur avec lequel vous êtes actuellement connecté au portail.

> [!TIP] 
> Elle peut donner l’impression d’être une propriété globale pour Azure Active Directory, toutefois, elle fonctionne par utilisateur, pour l’utilisateur actuellement connecté. Quand vous avez des droits d’administrateur général dans Azure Active Directory, vous pouvez appeler la fonctionnalité elevateAccess pour l’utilisateur avec lequel vous êtes connecté dans le centre d’administration d’Azure Active Directory.

![Centre d’administration Azure AD - Propriétés - L’administrateur général peut gérer l’abonnement Azure - capture d’écran](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="view-role-assignments-at-the--scope-using-powershell"></a>Afficher les attributions de rôle au niveau de la portée « / » à l’aide de PowerShell
Pour afficher l’attribution **Administrateur de l’accès utilisateur** au niveau de la portée **/**, utilisez l’applet de commande PowerShell `Get-AzureRmRoleAssignment`.
    
```powershell
Get-AzureRmRoleAssignment* | where {$_.RoleDefinitionName -eq "User Access Administrator" -and $_SignInName -eq "<username@somedomain.com>" -and $_.Scope -eq "/"}
```

**Exemple de sortie** :
```
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0    
Scope              : /    
DisplayName        : username    
SignInName         : username@somedomain.com    
RoleDefinitionName : User Access Administrator    
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9    
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc    
ObjectType         : User    
```

## <a name="delete-the-role-assignment-at--scope-using-powershell"></a>Supprimer l’attribution de rôle au niveau de la portée « / » à l’aide de PowerShell :
Vous pouvez supprimer l’attribution à l’aide de l’applet de commande PowerShell suivante :

```powershell
Remove-AzureRmRoleAssignment -SignInName <username@somedomain.com> -RoleDefinitionName "User Access Administrator" -Scope "/" 
```

## <a name="use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>Utiliser elevateAccess pour donner l’accès client avec l’API REST

Le processus de base comprend les étapes suivantes :

1. Avec REST, appelez *elevateAccess*, qui vous accorde le rôle d’administrateur des accès utilisateur au niveau de l’étendue « / ».

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. Créez une [attribution de rôle](/rest/api/authorization/roleassignments) pour attribuer le rôle de votre choix quelle que soit l’étendue. L’exemple suivant montre les propriétés permettant d’attribuer le rôle de lecteur au niveau de l’étendue « / » :

    ```json
    { 
      "properties": {
        "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
        "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
        "scope": "/"
      },
      "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
    }
    ```

3. En tant qu’administrateur des accès utilisateur, vous pouvez également supprimer des attributions de rôles au niveau de l’étendue « / ».

4. Révoquez vos privilèges d’administrateur des accès utilisateur jusqu’à ce que vous en ayez à nouveau besoin.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>Annuler l’action elevateAccess avec l’API REST

Lorsque vous appelez *elevateAccess*, vous créez une attribution de rôle pour vous-même : pour révoquer ces privilèges, vous devez donc supprimer l’attribution.

1.  Appelez GET roleDefinitions, où roleName = administrateur des accès utilisateur, pour déterminer le GUID de nom du rôle d’administrateur des accès utilisateur.
    ```
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User+Access+Administrator
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Enregistrez le GUID du paramètre *name*, dans ce cas **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**.

2. Vous devez également répertorier l’attribution de rôle pour l’administrateur client à la portée du client. Répertoriez toutes les attributions à la portée du client pour le PrincipalId de l’administrateur client qui a effectué l’appel d’accès élevé. Cela va répertorier toutes les attributions dans le client pour le paramètre ObjectID.

    ```
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Un administrateur locataire ne doit pas avoir beaucoup d’attributions. Si la requête précédente retourne un trop grand nombre d’attributions, vous pouvez aussi interroger toutes les attributions au niveau de la portée du locataire uniquement, puis filtrer les résultats : `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
    
        
    2. Les appels précédents retournent une liste des attributions de rôle. Recherchez l’attribution de rôle dans laquelle la portée est « / » et RoleDefinitionId se termine par le GUID du nom du rôle trouvé à l’étape 1 et PrincipalId correspond au paramètre ObjectId de l’administrateur client. 
    
    Exemple d’attribution de rôle :

        ```json
        {
          "value": [
            {
              "properties": {
                "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                "principalId": "{objectID}",
                "scope": "/",
                "createdOn": "2016-08-17T19:21:16.3422480Z",
                "updatedOn": "2016-08-17T19:21:16.3422480Z",
                "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
                "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
              },
              "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
              "type": "Microsoft.Authorization/roleAssignments",
              "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
            }
          ],
          "nextLink": null
        }
        ```
        
    Ici encore, enregistrez le GUID du paramètre *name*, dans ce cas **e7dd75bc-06f6-4e71-9014-ee96a929d099**.

    3. Enfin, utilisez l’**ID RoleAssignment** mis en surbrillance pour supprimer l’attribution ajoutée par Accès élevé :

    ```
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [gestion du contrôle d’accès en fonction du rôle à l’aide de REST](role-based-access-control-manage-access-rest.md)

- [Gérer les attributions d’accès](role-based-access-control-manage-assignments.md) dans le Portail Azure
