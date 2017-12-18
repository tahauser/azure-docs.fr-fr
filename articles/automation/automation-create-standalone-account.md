---
title: "Création d’un compte Azure Automation autonome | Microsoft Docs"
description: "Ce didacticiel vous guide tout au long des procédures de création et de test d’une authentification de principal de sécurité dans Azure Automation. Il est complété par un exemple d’utilisation."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/18/2017
ms.author: magoedte
ms.openlocfilehash: dc4bfa4a94eaa2fb4e0e821c4931dcd1963f3109
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="create-a-standalone-azure-automation-account"></a>Création d’un compte Azure Automation autonome
Cette rubrique montre comment créer un compte Automation à partir du portail Azure si vous souhaitez évaluer et apprendre Azure Automation sans inclure les solutions de gestion supplémentaires ou une intégration avec OMS Log Analytics pour fournir une surveillance avancée des travaux Runbook.  Vous pourrez ajouter ces solutions de gestion ou effectuer une intégration à Log Analytics ultérieurement.  Avec le compte Automation, vous avez la possibilité d’identifier les ressources de gestion des Runbooks dans Azure Resource Manager ou le modèle de déploiement Azure Classic.

Lorsque vous créez un compte Automation dans le portail Azure, il crée automatiquement :

* Un compte d’identification, qui crée un principal du service dans Azure Active Directory, un certificat, et attribue le contrôle d’accès en fonction du rôle (RBAC) Collaborateur, qui est utilisé pour gérer les ressources Resource Manager à l’aide de Runbooks.   
* Un compte d’identification Classic en chargeant un certificat de gestion, qui est utilisé pour gérer les ressources classiques à l’aide de Runbooks.  

Le processus s’en trouve ainsi simplifié, et vous êtes en mesure de commencer rapidement à générer et déployer des Runbooks pour répondre à vos besoins d’automatisation.  

## <a name="permissions-required-to-create-automation-account"></a>Autorisations requises pour créer le compte Automation
Pour créer ou mettre à jour un compte Automation conformément à cette rubrique, vous devez disposer des privilèges spécifiques suivants et des autorisations requises.   
 
* Pour créer un compte Automation, votre compte utilisateur AD doit être ajouté à un rôle disposant d’autorisations équivalentes à celles du rôle Propriétaire pour les ressources Microsoft.Automation, comme indiqué dans l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).  
* Si le paramètre Inscriptions des applications possède la valeur **Oui**, les utilisateurs non administrateurs dans votre locataire Azure AD peuvent [inscrire des applications AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions).  Si le paramètre Inscriptions d’applications est défini sur **Non**, l’utilisateur qui effectue cette action doit être un administrateur général dans Azure AD. 

Si vous n’êtes pas membre de l’instance Active Directory de l’abonnement avant d’être ajouté au rôle Administrateur général/Coadministrateur de l’abonnement, vous êtes ajouté à Active Directory en tant qu’invité. Dans ce cas, vous recevez le message d’avertissement « Vous n’avez pas les autorisations pour créer… » dans le panneau **Ajouter un compte Automation**. Les utilisateurs ayant préalablement reçu le rôle administrateur général/coadministrateur peuvent être supprimés de l’instance Active Directory de l’abonnement, puis rajoutés pour devenir des utilisateurs complets dans Active Directory. Pour vérifier si tel est le cas, dans le volet **Azure Active Directory** du portail Azure, sélectionnez **Utilisateurs et groupes** et **Tous les utilisateurs**, choisissez l’utilisateur concerné, puis sélectionnez **Profil**. La valeur de l’attribut **Type d’utilisateur** sous le profil de l’utilisateur ne doit pas être **Invité**.

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Créer un compte Automation à partir du portail Azure
Suivez les étapes suivantes dans cette section afin de créer un compte Azure Automation dans le portail Azure.    

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
2. Cliquez sur **Nouveau**.<br><br> ![Sélection de l’option Nouveau dans le portail Azure](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Recherchez **Automation**, puis dans les résultats de recherche, sélectionnez **Automation & Control***.<br><br> ![Recherche et sélection d’Automation à partir de la Place de marché](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)<br> 
3. Dans le panneau Comptes Automation, cliquez sur **Ajouter**.<br><br>![Ajouter un compte Automation](media/automation-create-standalone-account/automation-create-automationacct-properties.png)


   > [!NOTE]
   > Si l’avertissement suivant s’affiche dans le panneau **Ajouter un compte Automation**, cela signifie que votre compte n’est ni membre du rôle Administrateurs des abonnements ni coadministrateur de l’abonnement.<br><br>![Avertissement Ajouter un compte Automation](media/automation-create-standalone-account/create-account-without-perms.png)
   > 
   > 
4. Dans le panneau **Ajouter un compte Automation**, entrez le nom de votre nouveau compte Automation dans la zone **Nom**.
5. Si vous disposez de plusieurs abonnements, spécifiez-en un pour le nouveau compte, puis indiquez un **groupe de ressources** nouveau ou existant et un **emplacement** de centre de données Azure.
6. Vérifiez que l’option **Créer un compte d’identification Azure** est bien définie sur la valeur **Oui**, puis cliquez sur le bouton **Créer**.  
   
   > [!NOTE]
   > Si vous choisissez de ne pas créer de compte d’identification en sélectionnant l’option **Non**, un message d’avertissement s’affiche dans le panneau **Ajouter un compte Automation**.  Bien que le compte soit créé dans le portail Azure, il ne possède pas d’identité d’authentification correspondante au sein de votre service d’annuaire d’abonnement classique ou de votre service d’annuaire d’abonnement Gestionnaire des ressources. Par conséquent, il ne peut pas accéder aux ressources dans votre abonnement.  Cela empêche tous les Runbooks faisant référence à ce compte de pouvoir s’authentifier et d’effectuer des tâches sur les ressources de ces modèles de déploiement.
   > 
   > ![Avertissement Ajouter un compte Automation](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)<br>
   > Le rôle Contributeur n’est pas attribué en cas de non-création du principal du service.
   > 

7. Pour suivre la progression de la création du compte Automation, accédez à l’onglet **Notifications** du menu.

### <a name="resources-included"></a>Ressources incluses
Une fois le compte Automation créé, plusieurs ressources vous sont automatiquement créées.  Le tableau ci-dessous récapitule les ressources du compte d’identification.<br>

| Ressource | Description |
| --- | --- |
| Runbook AzureAutomationTutorial |Exemple de Runbook Graphical qui illustre l’authentification à l’aide du compte d’identification et l’accès à l’ensemble des ressources Resource Manager. |
| Runbook AzureAutomationTutorialScript |Exemple de Runbook PowerShell qui illustre l’authentification à l’aide du compte d’identification et l’accès à l’ensemble des ressources Resource Manager. |
| Runbook AzureAutomationTutorialPython2 |Un exemple de runbook Python qui montre comment s’authentifier en utilisant le compte d’identification, puis répertorie les groupes de ressources présents dans l’abonnement spécifié. |
| AzureRunAsCertificate |Ressource de certificat créée automatiquement lors de la création du compte Automation ou à l’aide du script PowerShell ci-dessous pour un compte existant.  Elle vous permet de vous authentifier auprès d’Azure afin de pouvoir gérer les ressources Azure Resource Manager des Runbooks.  Ce certificat a une durée de vie d’un an. |
| AzureRunAsConnection |Ressource de connexion créée automatiquement lors de la création du compte Automation ou à l’aide du script PowerShell ci-dessous pour un compte existant. |

Le tableau ci-dessous récapitule les ressources du compte d’identification Classic.<br>

| Ressource | Description |
| --- | --- |
| Runbook AzureClassicAutomationTutorial |Exemple de Runbook Graphical qui accède à toutes les machines virtuelles Classic d’un abonnement à l’aide du compte d’identification Classic (certificat), puis indique leur nom et leur état. |
| Runbook AzureClassicAutomationTutorialScript |Exemple de Runbook PowerShell qui accède à toutes les machines virtuelles Classic d’un abonnement à l’aide du compte d’identification Classic (certificat), puis indique leur nom et leur état. |
| AzureClassicRunAsCertificate |Ressource de certificat créée automatiquement, qui est utilisée pour l’authentification auprès d’Azure afin que vous puissiez gérer les ressources Azure Classic des Runbooks.  Ce certificat a une durée de vie d’un an. |
| AzureClassicRunAsConnection |Ressource de connexion créée automatiquement, qui est utilisée pour l’authentification auprès d’Azure afin que vous puissiez gérer les ressources Azure Classic des Runbooks. |


## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la création graphique, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md).
* Pour une prise en main des Runbooks PowerShell, consultez [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Pour obtenir des informations sur la prise en main des Runbooks de workflow PowerShell, voir [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md).
* Pour commencer avec les runbooks Python2, consultez [My first Python2 runbook](automation-first-runbook-textual-python2.md) (Mon premier Runbook Python2).
