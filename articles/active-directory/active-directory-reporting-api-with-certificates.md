---
title: "Obtenir des données à l’aide de l’API Génération de rapports Azure AD avec des certificats | Microsoft Docs"
description: "Explique comment utiliser l’API Génération de rapports Azure AD avec les informations d’identification de certificat pour obtenir des données à partir de répertoires sans intervention de l’utilisateur."
services: active-directory
documentationcenter: 
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/08/2017
ms.author: ramical
ms.openlocfilehash: 4900e47084256ad6c85886f7ba363399678da9aa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="get-data-using-the-azure-ad-reporting-api-with-certificates"></a>Obtenir des données à l’aide de l’API Génération de rapports Azure AD avec des certificats
Cet article explique comment utiliser l’API Génération de rapports Azure AD avec les informations d’identification de certificat pour obtenir des données à partir de répertoires sans intervention de l’utilisateur. 

## <a name="use-the-azure-ad-reporting-api"></a>Utiliser l’API Génération de rapports Azure AD 
L’API Génération de rapports Azure AD exige que vous effectuiez les opérations suivantes :
 *  Installation des composants requis
 *  Définition du certificat dans votre application
 *  Obtention d’un jeton d’accès
 *  Utilisation du jeton d’accès pour appeler l’API Graph

Pour plus d’informations sur le code source, consultez [Leverage Report API Module](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils) (Utiliser le module d’API Génération de rapports). 

### <a name="install-prerequisites"></a>Installation des composants requis
Vous devez avoir le module AzureADUtils et Azure AD PowerShell V2 installés.

1. Téléchargez et installez Azure AD Powershell V2 en suivant les instructions dans [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md).
2. Téléchargez le module Azure AD Utils à partir de [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1). 
  Ce module fournit plusieurs applets de commande d’utilitaire, notamment :
   * La dernière version d’ADAL à l’aide de Nuget
   * Les jetons d’accès d’utilisateur, de clés d’application et de certificats à l’aide d’ADAL
   * L’API Graph gérant les résultats paginés

**Pour installer le module Azure AD Utils :**

1. Créez un répertoire pour enregistrer le module d’utilitaires (par exemple, c:\azureAD) et téléchargez le module à partir de GitHub.
2. Ouvrez une session PowerShell et accédez au répertoire que vous venez de créer. 
3. Importez le module et installez-le dans le chemin d’accès du module PowerShell à l’aide de l’applet de commande Install-AzureADUtilsModule. 

La session doit ressembler à ce qui suit :

  ![Windows PowerShell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

### <a name="set-the-certificate-in-your-app"></a>Définition du certificat dans votre application
1. Si vous disposez déjà d’une application, récupérez son ID d’objet à partir du portail Azure. 

  ![Portail Azure](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. Ouvrez une session PowerShell et connectez-vous à Azure AD à l’aide de l’applet de commande Connect-AzureAD.

  ![Portail Azure](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. Utilisez l’applet de commande New-AzureADApplicationCertificateCredential d’AzureADUtils pour y ajouter des informations d’identification de certificat. 

>[!Note]
>Vous devez fournir l’ID d’objet de l’application que vous avez récupéré précédemment, ainsi que l’objet de certificat (récupérez-le à l’aide du lecteur Cert :).
>


  ![Portail Azure](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
### <a name="get-an-access-token"></a>Obtention d’un jeton d’accès

Pour obtenir un jeton d’accès, utilisez l’applet de commande Get-AzureADGraphAPIAccessTokenFromCert de AzureADUtils. 

>[!NOTE]
>Vous devez utiliser l’ID d’application plutôt que l’ID d’objet que vous avez utilisé dans la dernière section.
>

 ![Portail Azure](./media/active-directory-report-api-with-certificates/application-id.png)

### <a name="use-the-access-token-to-call-the-graph-api"></a>Utilisation du jeton d’accès pour appeler l’API Graph

Vous pouvez maintenant créer le script. Voici un exemple d’utilisation de l’applet de commande Invoke-AzureADGraphAPIQuery à partir du module AzureADUtils. Cette applet de commande gère les résultats composés de plusieurs pages et les envoie ensuite dans le pipeline PowerShell. 

 ![Portail Azure](./media/active-directory-report-api-with-certificates/script-completed.png)

Vous êtes maintenant prêt à exporter vers un fichier CSV et enregistrer dans un système SIEM. Vous pouvez également encapsuler votre script dans une tâche planifiée pour obtenir régulièrement des données Azure AD à partir de votre client sans avoir à stocker des clés d’application dans le code source. 

## <a name="next-steps"></a>Étapes suivantes
[Principes de base de la gestion des identités Azure](https://docs.microsoft.com/azure/active-directory/fundamentals-identity)<br>



