---
title: Ajouter un nouveau compte client Azure Stack dans Azure Active Directory | Microsoft Docs
description: "Après le déploiement du Kit de développement Microsoft Azure Stack, vous devrez créer au moins un compte utilisateur client pour pouvoir explorer le portail client."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 590426563936c66b1353f769be138759bb53f58c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Ajouter un nouveau compte de locataire Azure Stack dans Azure Active Directory
Après le [déploiement du Kit de développement Azure Stack](azure-stack-run-powershell-script.md), vous aurez besoin d’un compte utilisateur client afin de pouvoir explorer le portail client et tester vos offres et vos plans. Vous pouvez créer un compte client [à l’aide du portail Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) ou [à l’aide de PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Création d’un compte de locataire Azure Stack à l’aide du portail Azure
Pour utiliser le portail Azure, vous devez disposer d’un abonnement Azure.

1. Connectez-vous à [Azure](https://portal.azure.com).
2. Dans la barre de navigation gauche Microsoft Azure, cliquez sur **Active Directory**.
3. Dans la liste des répertoires, cliquez sur le répertoire que vous souhaitez utiliser pour Azure Stack, ou créez-en un.
4. Sur la page de ce répertoire, cliquez sur **Utilisateurs**.
5. Cliquez sur **Add User**.
6. Dans l’assistant **Ajouter un utilisateur**, dans la liste **Type d’utilisateur**, choisissez **Nouvel utilisateur dans votre organisation**.
7. Dans la zone **Nom d’utilisateur** , saisissez le nom de l’utilisateur.
8. Dans l’assistant **@** , sélectionnez l’entrée appropriée.
9. Cliquez sur la flèche Suivant.
10. Dans la page **Profil utilisateur** de l’assistant, tapez un **Prénom**, un **Nom** et un **Nom d’affichage**.
11. Dans la liste **Rôle**, choisissez **Utilisateur**.
12. Cliquez sur la flèche Suivant.
13. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**.
14. Copiez le **Nouveau mot de passe**.
15. Connectez-vous à Microsoft Azure avec le nouveau compte. Modifiez le mot de passe lorsque vous y êtes invité.
16. Connectez-vous à `https://portal.local.azurestack.external` avec le nouveau compte pour afficher le portail client.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Création d’un compte de locataire Azure Stack à l’aide de PowerShell
Si vous n’avez pas d’abonnement Azure, vous ne pouvez pas utiliser le portail Azure pour ajouter un compte utilisateur client. Dans ce cas, vous pouvez utiliser le module Azure Active Directory pour Windows PowerShell à la place.

> [!NOTE]
> Si vous utilisez un compte Microsoft (Live ID) pour déployer le Kit de développement Azure Stack, vous ne pouvez pas utiliser AAD PowerShell pour créer le compte client. 
> 
> 

1. Installez [l’Assistant de connexion Microsoft Online Services pour les professionnels de l’informatique RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Installez le [module Azure Active Directory pour Windows PowerShell (version 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297) et ouvrez-le.
3. Exécutez les applets de commande suivantes :

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Connectez-vous à Microsoft Azure avec le nouveau compte. Modifiez le mot de passe lorsque vous y êtes invité.
2. Connectez-vous à `https://portal.local.azurestack.external` avec le nouveau compte pour afficher le portail client.

