---
title: "FAQ et problèmes connus liés à l’identité du service administré (MSI) pour Azure Active Directory"
description: "Problèmes connus liés à l’identité du service administré pour Azure Active Directory."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: bd931b220c417f91b47278c82707d38de5c7f65e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>FAQ et problèmes connus liés à l’identité du service administré (MSI) pour Azure Active Directory

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Forum Aux Questions (FAQ)

### <a name="is-there-a-private-preview-available-for-additional-features"></a>Existe-t-il une préversion privée, pour des fonctionnalités supplémentaires ?

Oui. Si vous souhaitez vous inscrire pour la préversion privée, [rendez-vous sur notre page d’inscription](https://aka.ms/azuremsiprivatepreview).

### <a name="does-msi-work-with-azure-cloud-services"></a>MSI est-il compatible avec Azure Cloud Services ?

Non, et nous ne prévoyons pas de rendre possible l’utilisation de MSI dans Azure Cloud Services.

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>L’identité du service administré fonctionne-t-elle avec la bibliothèque d’authentification Active Directory (ADAL) et la bibliothèque d’authentification Microsoft (MSAL) ?

Non, la fonction d’identité du service administré n’est pas encore intégrée aux bibliothèques ADAL et MSAL. Pour plus d’informations sur l’acquisition d’un jeton MSI à l’aide du point de terminaison REST de MSI, voir [Utilisation d’une identité du service administré (MSI) d’une machine virtuelle Azure pour obtenir des jetons](how-to-use-vm-token.md).

### <a name="what-are-the-supported-linux-distributions"></a>Quelles sont les distributions de Linux prises en charge ?

Les distributions Linux suivantes prennent en charge MSI : 

- CoreOS Stable
- CentOS 7.1
- RedHat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

Les autres distributions de Linux ne sont actuellement pas prises en charge, et l’extension peut échouer sur les distributions non prises en charge.

L’extension fonctionne sur CentOS 6.9. Toutefois, en raison de l’absence de prise en charge système dans 6.9, l’extension ne redémarre pas automatiquement en cas d’incident ou d’arrêt. Elle redémarre au redémarrage de la machine virtuelle. Pour redémarrer manuellement l’extension, consultez [Comment redémarrer l’extension MSI ?](#how-do-you-restart-the-msi-extension)

### <a name="how-do-you-restart-the-msi-extension"></a>Comment redémarrer l’extension MSI ?
Sur Windows et certaines versions de Linux, si l’extension s’arrête, la cmdlet suivante peut être utilisée pour redémarrer manuellement l’extension :

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Où : 
- Le type et le nom de l’extension pour Windows est : ManagedIdentityExtensionForWindows
- Le type et le nom de l’extension pour Linux est : ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Problèmes connus

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>Un « script d’automatisation » échoue lors de la tentative d’exportation d’un schéma pour l’extension MSI

Lorsque l’identité du service administré est activée sur une machine virtuelle, l’erreur suivante s’affiche lorsque vous tentez d’utiliser la fonctionnalité « Script d’automatisation » pour la machine virtuelle ou son groupe de ressources :

![Erreur d’exportation de script d’automatisation MSI](../media/msi-known-issues/automation-script-export-error.png)

L’extension de machine virtuelle Identité du service administré ne prend actuellement pas en charge la possibilité d’exporter son schéma vers un modèle de groupe de ressources. Par conséquent, le modèle généré n’affiche pas les paramètres de configuration permettant d’activer l’identité du service administré sur la ressource. Ces sections peuvent être ajoutées manuellement en suivant les exemples dans [Configurer une identité du service administré d’une machine virtuelle à l’aide d’un modèle](qs-configure-template-windows-vm.md).

Lorsque la fonctionnalité d’exportation de schéma sera disponible pour l’extension de machine virtuelle MSI, elle sera répertoriée dans [Exportation des groupes de ressources contenant des extensions de machine virtuelle](../../virtual-machines/windows/extensions-export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Le panneau de configuration n’apparaît pas dans le portail Azure

Si le panneau de configuration de la machine virtuelle n’apparaît pas sur votre machine virtuelle, cela signifie que l’identité du service administré n’a pas encore été activée dans le portail de votre région.  Revérifiez ultérieurement.  Vous pouvez également activer l’identité du service administré pour votre machine virtuelle à l’aide de [PowerShell](qs-configure-powershell-windows-vm.md) ou d’[Azure CLI](qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Impossible d’attribuer un accès aux machines virtuelles dans le panneau de contrôle d’accès (IAM)

Si **Machine virtuelle** n’apparaît pas dans le portail Azure en tant que choix pour **Attribuer un accès** dans **Contrôle d’accès (IAM)** > **Ajouter autorisations**, cela signifie que l’identité du service administré n’a pas encore été activée dans le portail de votre région. Revérifiez ultérieurement.  Vous pouvez toujours sélectionner l’identité du service administré pour l’attribution de rôle en recherchant le principal de service de l’identité du service administré.  Entrez le nom de la machine virtuelle dans le champ **Sélectionner**. Le principal de service s’affiche dans le résultat de la recherche.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>La machine virtuelle ne démarre pas après avoir été déplacée d’un groupe de ressources ou d’un abonnement

Si vous déplacez une machine virtuelle en cours d’exécution, elle continue de s’exécuter pendant le déplacement. Toutefois, si la machine virtuelle est arrêtée et redémarrée après le déplacement, elle ne démarre plus. Ce problème se produit car la machine virtuelle ne met pas à jour la référence de l’identité du service administré et continue de pointer celle de l’ancien groupe de ressources.

**Solution de contournement** 
 
Déclenchez une mise à jour sur la machine virtuelle pour qu’elle obtienne les valeurs correctes de l’identité du service administré. Vous pouvez modifier les propriétés d’une machine virtuelle pour mettre à jour la référence de l’identité du service administré. Par exemple, vous pouvez définir une nouvelle valeur de balise sur la machine virtuelle avec la commande suivante :

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Cette commande définit une nouvelle balise « fixVM » avec une valeur de 1 sur la machine virtuelle. 
 
En définissant cette propriété, la machine virtuelle se met à jour avec l’URI de ressource de l’identité du service administré appropriée. Vous devez maintenant être en mesure de démarrer la machine virtuelle. 
 
Une fois que la machine virtuelle est démarrée, la balise peut être supprimée en utilisant la commande suivante :

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```
