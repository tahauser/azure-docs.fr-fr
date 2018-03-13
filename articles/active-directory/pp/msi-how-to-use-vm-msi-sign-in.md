---
title: "Utilisation d’une identité du service administré de machine virtuelle Azure pour se connecter"
description: "Procédure détaillée et exemples concernant l’utilisation d’un principal du service MSI d’une machine virtuelle Azure pour la connexion client par script et l’accès aux ressources."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/05/2018
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c5c1be01947dba8b7f4ef8aa54aa6aedfb191d32
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="sign-in-using-a-vm-user-assigned-managed-service-identity-msi"></a>Connectez-vous à l’aide d’une identité MSI (Managed Service Identity) affectée à l’utilisateur sur une machine virtuelle

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]
Cet article donne des exemples de scripts CLI permettant de se connecter à l’aide d’un principal de service MSI affecté à l’utilisateur ; il offre également de l’aide sur des sujets importants, comme la gestion des erreurs.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Pour utiliser les exemples de l’interface de ligne de commande Azure dans cet article, prenez soin d’installer la dernière version d’[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Tous les scripts d’exemple de cet article supposent que le client de ligne de commande exécute une machine virtuelle avec le paramètre MSI activé. Utilisez la fonctionnalité « Se connecter » de machine virtuelle dans le portail Azure, pour vous connecter à distance à votre machine virtuelle. Pour plus d’informations sur l’activation de l’identité MSI sur une machine virtuelle, consultez [Configurer une identité MSI (Managed Service Identity) d’une machine virtuelle à l’aide de l’interface de ligne de commande Azure](msi-qs-configure-cli-windows-vm.md), ou l’un des autres articles (à l’aide de PowerShell, du portail, d’un modèle ou d’un kit SDK Azure). 
> - Pour éviter les erreurs lors de la connexion et de l’accès aux ressources, l’identité MSI doit comporter au moins l’accès « Lecture » à l’étendue appropriée (la machine virtuelle ou plus) pour autoriser les opérations d’Azure Resource Manager sur la machine virtuelle. Voir [Attribuer à une identité MSI l’accès à une ressource à l’aide de l’interface de ligne de commande Azure](msi-howto-assign-access-cli.md) pour plus de détails.

## <a name="overview"></a>Vue d'ensemble

Une MSI fournit un [principal du service](~/articles/active-directory/develop/active-directory-dev-glossary.md#service-principal-object), qui est [créé lors de l’activation de la MSI](msi-overview.md#how-does-it-work) sur la machine virtuelle. L’accès à des ressources Azure peut être donné au principal du service, et celui-ci peut être utilisé comme identité par des clients de script/ligne de commande pour la connexion et l’accès aux ressources. En règle générale, pour accéder à des ressources sécurisées sous sa propre identité, un client de script doit :  

   - être inscrit et consenti avec Azure AD comme une application cliente web/confidentielle
   - connectez-vous sous son principal du service, à l’aide des informations d’identification de l’application (normalement incorporées dans le script)

Avec la MSI, votre client de script n’a plus besoin de l’effectuer, étant donné qu’il peut se connecter sous le principal du service MSI. 

## <a name="azure-cli"></a>Azure CLI

Le script suivant montre comment :

1. Se connecter à Azure AD sous le principal du service de l’identité MSI affectée à l’utilisateur.  
2. Appeler Azure Resource Manager et obtenir l’emplacement de la région Azure pour une machine virtuelle. CLI prend en charge automatiquement la gestion de l’acquisition/utilisation des jetons pour vous. Veillez à remplacer le nom de votre machine virtuelle par `<VM NAME>`, et l’ID de ressource de l’identité MSI affectée à l’utilisateur par `<MSI ID>`. L’ID de ressource de l’identité MSI est retourné dans la propriété `id` lors de la création d’une identité MSI affectée à l’utilisateur (voir [Configurer une identité MSI (Managed Service Identity) affectée à l’utilisateur pour une machine virtuelle avec Azure CLI](msi-qs-configure-cli-windows-vm.md) pour obtenir des exemples de la commande `az identity create`).

    ```azurecli
    az login --msi –u <MSI ID>
   
    vmLocation=$(az resource list -n <VM NAME> --query [*].location --out tsv)
    echo The VM region location is $vmLocation
    ```

    Exemples de réponses :
   
    ```bash
    user@vmLinux:~$ az login --msi -u /subscriptions/80c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName
    [
      {
        "environmentName": "AzureCloud",
        "id": "90c696ff-5efa-4909-a64d-z1b616f423bl",
        "isDefault": true,
        "name": "MSIResource-/subscriptions/90c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName@50342",
        "state": "Enabled",
        "tenantId": "933a8f0e-ec41-4e69-8ad8-971zc4b533ll",
        "user": {
          "name": "userAssignedIdentity",
          "type": "servicePrincipal"
        }
      }
    ]  
    user@vmLinux:~$ vmLocation=$(az resource list -n vmLinux --query [*].location --out tsv)
    user@vmLinux:~$ echo The VM region location is $vmLocation
    The VM region location is westcentralus
    ```

## <a name="resource-ids-for-azure-services"></a>ID de ressource pour les services Azure

Consultez [Services Azure prenant en charge l’authentification de Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) pour obtenir une liste des ressources qui prennent en charge Azure AD et qui ont été testées avec l’identité du service administré et leur ID de ressources respectif.

## <a name="error-handling-guidance"></a>Conseil de gestion des erreurs 

Les réponses suivantes peuvent indiquer que l’identité MSI n’a pas été correctement configurée :

- CLI : *MSI : Impossible de récupérer un jeton à partir de « http://localhost:50342/oauth2/jeton » avec l’erreur « HTTPConnectionPool (host = 'localhost', port = 50342)* 

Si vous recevez l’une de ces erreurs, revenez à la machine virtuelle Azure dans le [portail Azure](https://portal.azure.com) et :

- Accédez à la page **Configuration** et vérifiez que le paramètre « Identité de service administré » est défini sur « Oui ».
- Accédez à la page **Extensions** et assurez-vous que l’extension de l’identité du service administré est déployée avec succès.

Si ce n’est pas le cas, vous devez réaffecter l’identité MSI à votre ressource ou résoudre le problème de déploiement. Consultez [Configurer l’identité du service administré (MSI) de la machine virtuelle à l’aide d’Azure CLI](msi-qs-configure-cli-windows-vm.md) si vous avez besoin d’aide pour la configuration d’une machine virtuelle.

## <a name="next-steps"></a>étapes suivantes

- Pour activer l’identité MSI sur une machine virtuelle Azure, consultez [Configurer l’identité du service administré (MSI) de la machine virtuelle à l’aide d’Azure CLI](msi-qs-configure-cli-windows-vm.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.








