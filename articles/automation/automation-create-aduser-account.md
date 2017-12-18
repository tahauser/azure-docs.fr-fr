---
title: "Créer un compte d’utilisateur Azure AD | Microsoft Docs"
description: "Cet article décrit comment créer les informations d’identification d’un compte d’utilisateur Azure AD pour les Runbooks dans Azure Automation pour l’authentification dans Azure et Azure Classic."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
keywords: "utilisateur azure active directory, azure service management, compte d’utilisateur azure ad"
ms.assetid: fcfe266d-b22e-4dfb-8272-adcab09fc0cf
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2017
ms.author: magoedte
ms.openlocfilehash: 700c4419821934daac89025c889b21d8e2ef46b6
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Authentification des Runbooks avec le déploiement Azure Classic et Resource Manager
Cet article décrit les étapes à effectuer pour configurer un compte d’utilisateur Azure AD pour les runbooks Azure Automation en cours d’exécution sur des ressources du modèle de déploiement Azure Classic ou Azure Resource Manager.  Même si cette procédure est toujours prise en charge comme identité d’authentification pour vos Runbooks Azure Resource Manager, il est recommandé d’utiliser un compte d’identification Azure.       

## <a name="create-a-new-azure-active-directory-user"></a>Création d’un nouvel utilisateur Azure Active Directory
1. Connectez-vous au portail Azure Classic en tant qu’administrateur de services fédérés pour l’abonnement Azure que vous souhaitez gérer.
2. Sélectionnez **Active Directory**, puis sélectionnez le nom du répertoire de votre organisation.
3. Sélectionnez l’onglet **Utilisateurs**, puis, dans la zone de commande, sélectionnez **Ajouter un utilisateur**.
4. Sur la page **Dites-nous en plus sur cet utilisateur**, sous **Type d’utilisateur**, sélectionnez **Nouvel utilisateur dans votre organisation**.
5. Entrez un nom d’utilisateur.  
6. Sélectionnez le nom du répertoire associé à votre abonnement Azure sur la page Active Directory.
7. Sur la page **Profil de l’utilisateur**, entrez les nom et prénom de l’utilisateur, un nom convivial et sélectionnez Utilisateur dans la liste **Rôles**.  Ne sélectionnez pas la case à cocher **Activer l’authentification multifacteur**.
8. Notez le nom complet de l’utilisateur et le mot de passe temporaire.
9. Sélectionnez **Paramètres > Administrateurs > Ajouter**.
10. Tapez le nom d’utilisateur complet de l’utilisateur que vous avez créé.
11. Sélectionnez l’abonnement que vous souhaitez gérer.
12. Déconnectez-vous d’Azure et puis reconnectez-vous avec le compte que vous venez de créer. Vous êtes invité à modifier le mot de passe de l’utilisateur.

## <a name="create-an-automation-account-in-azure-classic-portal"></a>Création d’un compte Automation dans le portail Azure Classic
Dans cette section, vous effectuez les étapes suivantes pour créer un compte Azure Automation dans le portail Azure qui sera utilisé avec vos ressources de gestion des Runbooks dans le mode de déploiement Azure Classic.  

> [!NOTE]
> Les comptes Automation créés avec le portail Azure Classic peuvent être gérés par le portail Azure Classic et le portail Azure, et par le jeu de cmdlets correspondant. Une fois que le compte est créé, la façon de créer et de gérer des ressources au sein du compte n'importe pas. Si vous envisagez de continuer à utiliser le portail Azure Classic, vous devez alors l’utiliser au lieu d’utiliser le portail Azure pour créer des comptes Automation.
> 
> 

1. Connectez-vous au portail Azure Classic en tant qu’administrateur de services fédérés pour l’abonnement Azure que vous souhaitez gérer.
2. Sélectionnez **Automation**.
3. Sur la page **Automation**, sélectionnez **Créer un compte Automation**.
4. Dans la boîte de dialogue **Créer un compte Automation**, entrez un nom pour votre nouveau compte Automation et sélectionnez une **région** dans la liste déroulante.  
5. Cliquez sur **OK** pour accepter les paramètres et créer le compte.
6. Une fois le compte créé, il est répertorié sur la page **Automation** .
7. Cliquez sur le compte pour accéder à la page Tableau de bord.  
8. Sur la page Tableau de bord Automation, sélectionnez **Actifs**.
9. Sur la page **Actifs**, sélectionnez l’option **Ajouter des paramètres** située en bas de la page.
10. Sur la page **Ajouter des paramètres**, sélectionnez **Ajouter des informations d’identification**.
11. Sur la page **Définir les informations d’identification**, sélectionnez **Informations d’identification de Windows PowerShell** dans la liste déroulante **Type d’informations d’identification** et entrez un nom pour les informations d’identification.
12. Sur la page **Définir les informations d’identification** suivante, entrez le nom d’utilisateur du compte utilisateur Active Directory créé précédemment dans le champ **Nom d’utilisateur** et le mot de passe dans les champs **Mot de passe** et **Confirmer le mot de passe**. Cliquez sur **OK** pour enregistrer vos modifications.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Création d’un compte Automation dans le portail Azure
Dans cette section, vous effectuez les étapes suivantes pour créer un compte Azure Automation dans le portail Azure qui sera utilisé avec vos ressources de gestion des Runbooks en mode Azure Resource Manager.  

1. Connectez-vous au portail Azure en tant qu’administrateur de service pour l’abonnement Azure que vous souhaitez gérer.
2. Sélectionnez **Comptes Automation**.
3. Dans le panneau Comptes Automation, cliquez sur **Ajouter**.<br><br>![Ajouter un compte Automation](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. Dans le panneau **Ajouter un compte Automation**, entrez le nom de votre nouveau compte Automation dans la zone **Nom**.
5. Si vous disposez de plusieurs abonnements, spécifiez celui du nouveau compte, ainsi qu’un **groupe de ressources** nouveau ou existant et un **emplacement** de centre de données Azure.
6. Sélectionnez la valeur **Yes** dans l’option **Créer un compte d’authentification Azure**, puis cliquez sur le bouton **Créer**.  
   
    > [!NOTE]
    > Si vous choisissez de ne pas créer de compte d’identification en sélectionnant l’option **Non**, un message d’avertissement s’affiche dans le panneau **Ajouter un compte Automation**.  Bien que le compte soit créé avec le rôle de **contributeur** dans l’abonnement, il n’aura pas d’identité d’authentification correspondante au sein de votre service de répertoire d’abonnements et, par conséquent, il n’aura pas accès aux ressources de votre abonnement.  Cela empêchera tous les Runbooks faisant référence à ce compte de pouvoir authentifier et effectuer des tâches sur les ressources Azure Resource Manager.
    > 
    >

    <br>![Avertissement Ajouter un compte Automation](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. Pour suivre la progression de la création du compte Automation, accédez à l’onglet **Notifications** du menu.

Lorsque la création des informations d’identification est terminée, vous devez créer un actif d’informations d’identification pour associer le compte Automation au compte d’utilisateur Active Directory créé précédemment.  N’oubliez pas que nous avons uniquement créé le compte Automation et qu’il n’est pas associé à une identité d’authentification.  Suivez les étapes présentées dans l’article [Ressources d’informations d’identification dans Azure Automation](automation-credentials.md#creating-a-new-credential-asset) et entrez la valeur du **nom d’utilisateur** au format **domaine\utilisateur**.

## <a name="use-the-credential-in-a-runbook"></a>Utilisation des informations d’identification dans un Runbook
Vous pouvez récupérer les informations d’identification dans un Runbook à l’aide de l’activité [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) et les utiliser avec [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) pour vous connecter à votre abonnement Azure. Si les informations d’identification correspondent à un administrateur de plusieurs abonnements Azure, vous devez également utiliser [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) pour spécifier celle qui convient. Cela est illustré dans l’exemple Windows PowerShell ci-dessous qui apparaît en haut de la plupart des Runbooks Azure Automation.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Vous devez répéter ces lignes après tout [point de contrôle](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) dans votre Runbook. Si le Runbook est interrompu, puis reprend avec un autre collaborateur, ce dernier doit s’authentifier à nouveau.

## <a name="next-steps"></a>Étapes suivantes
* Passez en revue les différents types de Runbooks et les étapes pour créer vos propres Runbooks dans l’article [Types de Runbooks Azure Automation](automation-runbook-types.md)

