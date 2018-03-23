---
title: Utilisation d’une identité du service administré de machine virtuelle Azure pour se connecter
description: Procédure détaillée et exemples concernant l’utilisation d’un principal du service MSI d’une machine virtuelle Azure pour la connexion client par script et l’accès aux ressources.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 4df404bbf56efbc3bb68f006f8aa0c7cdf0e86ac
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>Utilisation d’une identité du service administré de machine virtuelle (MSI) Azure pour se connecter 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Cet article fournit des exemples de script PowerShell et CLI pour la connexion à l’aide d’un principal du service MSI et des conseils sur des sujets importants tels que la gestion des erreurs.

## <a name="prerequisites"></a>Prérequis


[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Si vous envisagez d’utiliser les exemples de Azure PowerShell ou Azure CLI dans cet article, veillez à installer la dernière version de [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) ou bien [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Tous les scripts d’exemple de cet article supposent que le client de ligne de commande exécute une machine virtuelle avec le paramètre MSI activé. Utilisez la fonctionnalité « Se connecter » de machine virtuelle dans le portail Azure, pour vous connecter à distance à votre machine virtuelle. Pour plus d’informations sur l’activation de MSI sur une machine virtuelle, consultez [Configurer une identité du service administré (MSI) d’une machine virtuelle à l’aide du portail Azure](qs-configure-portal-windows-vm.md), ou l’un des autres articles (à l’aide de PowerShell, CLI, d’un modèle ou d’un kit de développement logiciel Azure). 
> - Pour éviter les erreurs lors de l’accès aux ressources, la MSI de la machine virtuelle doit comporter au moins l’accès « Lecture » à l’étendue appropriée (la machine virtuelle ou plus) pour autoriser les opérations de Azure Resource Manager sur la machine virtuelle. Voir [Attribuer à une identité du service administré (MSI) un accès à une ressource à l’aide du portail Azure](howto-assign-access-portal.md) pour plus de détails.

## <a name="overview"></a>Vue d'ensemble

Une MSI fournit un [objet de principal du service](../develop/active-directory-dev-glossary.md#service-principal-object), qui est [créé lors de l’activation de la MSI](overview.md#how-does-it-work) sur la machine virtuelle. Le principal du service peut accorder l’accès aux ressources Azure et être utilisé comme identité par des clients de script/ligne de commande pour la connexion et l’accès aux ressources. En règle générale, pour accéder à des ressources sécurisées sous sa propre identité, un client de script doit :  

   - être inscrit et consenti avec Azure AD comme une application cliente web/confidentielle
   - connectez-vous sous son principal du service, à l’aide des informations d’identification de l’application (normalement incorporées dans le script)

Avec la MSI, votre client de script n’a plus besoin de l’effectuer, étant donné qu’il peut se connecter sous le principal du service MSI. 

## <a name="azure-cli"></a>Azure CLI

Le script suivant montre comment :

1. Se connecter à Azure AD sous le principal du service MSI de la machine virtuelle  
2. Appelez Azure Resource Manager et obtenez l’ID principal du service de la machine virtuelle. CLI prend en charge automatiquement la gestion de l’acquisition/utilisation des jetons pour vous. Veillez à indiquer le nom de votre machine virtuelle pour `<VM-NAME>`.  

   ```azurecli
   az login --msi
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Le script suivant montre comment :

1. Acquérir un jeton d’accès MSI pour la machine virtuelle.  
2. Utiliser le jeton d’accès pour se connecter à Azure AD sous le principal du service MSI correspondant.   
3. Appelez une applet de commande de Azure Resource Manager pour obtenir des informations sur la machine virtuelle. PowerShell prend en charge la gestion de l’utilisation de jeton automatiquement pour vous.  

   ```azurepowershell
   # Get an access token for the MSI
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                                 -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
   $content =$response.Content | ConvertFrom-Json
   $access_token = $content.access_token
   echo "The MSI access token is $access_token"

   # Use the access token to sign in under the MSI service principal. -AccountID can be any string to identify the session.
   Login-AzureRmAccount -AccessToken $access_token -AccountId "MSI@50342"

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>ID de ressource pour les services Azure

Consultez [Services Azure prenant en charge l’authentification de Azure AD](overview.md#azure-services-that-support-azure-ad-authentication) pour obtenir une liste des ressources qui prennent en charge Azure AD et qui ont été testées avec l’identité du service administré et leur ID de ressources respectif.

## <a name="error-handling-guidance"></a>Conseil de gestion des erreurs 

Des réponses telles que les suivantes peuvent indiquer que les MSI de la machine virtuelle n’ont pas été configurées correctement :

- PowerShell : *Invoke-WebRequest : Impossible de se connecter au serveur distant*
- CLI : *MSI : Impossible de récupérer un jeton à partir de « http://localhost:50342/oauth2/jeton » avec l’erreur « HTTPConnectionPool (host = 'localhost', port = 50342)* 

Si vous recevez l’une de ces erreurs, revenez à la machine virtuelle Azure dans le [portail Azure](https://portal.azure.com) et :

- Accédez à la page **Configuration** et vérifiez que le paramètre « Identité de service administré » est défini sur « Oui ».
- Accédez à la page **Extensions** et assurez-vous que l’extension de l’identité du service administré est déployée avec succès.

En cas d’inexactitude, vous devez redéployer l’identité du service administré sur votre ressource, ou résoudre le problème de déploiement. Consultez [Configurer une identité du service administré (MSI) d’une machine virtuelle à l’aide du portail Azure](qs-configure-portal-windows-vm.md) si vous avez besoin d’aide pour la configuration d’une machine virtuelle.

## <a name="related-content"></a>Contenu connexe

- Pour activer l’identité du service administré sur une machine virtuelle Azure, consultez [Configurer l’identité du service administré (MSI) à l’aide de PowerShell](qs-configure-powershell-windows-vm.md), ou [Configurer une identité du service administré (MSI) d’une machine virtuelle à l’aide de Azure CLI](qs-configure-cli-windows-vm.md)

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.








