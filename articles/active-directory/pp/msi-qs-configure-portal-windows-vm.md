---
title: "Configurer l’identité du service administré sur une machine virtuelle Azure à l’aide du portail Azure"
description: "Instructions détaillées sur la configuration de l’identité du service administré (MSI) sur une machine virtuelle Azure, à l’aide du portail Azure."
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
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 37710015904c8112e5d2de504ed5b42895ffb809
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Configurer l’identité du service administré (MSI) d’une machine virtuelle à l’aide du portail Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez apprendre à activer et supprimer l’identité du service administré d’une machine virtuelle Azure, à l’aide du portail Azure.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Activer l’identité du service administré lors de la création d’une machine virtuelle Azure

Au moment de rediger cet article, l’activation de l’identité du service administré lors de la création d’une machine virtuelle dans le portail Azure n’est pas prise en charge. Consultez plutôt l’un des articles de démarrage rapide de création de machine virtuelle suivants pour créer une machine virtuelle :

- [Créer une machine virtuelle Windows avec le portail Azure](~/articles/virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Créer une machine virtuelle Linux avec le portail Azure](~/articles/virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Passez ensuite à la section suivante pour plus d’informations sur l’activation de l’identité du service administré sur la machine virtuelle.

## <a name="enable-msi-on-an-existing-azure-vm"></a>Activer l’identité du service administré sur une machine virtuelle Azure existante

Si vous avez une machine virtuelle qui a été initialement approvisionnée sans identité du service administré :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles ».

2. Accédez à la machine virtuelle souhaitée.

2. Cliquez sur la page « Configuration », activez l’identité du service administré sur la machine virtuelle en sélectionnant « Oui » sous « Identité du service administré », puis cliquez sur **Enregistrer**. Cette opération peut durer 60 secondes ou plus :

   ![Capture d’écran de la page Configuration](~/articles/active-directory/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Supprimer l’identité du service administré d’une machine virtuelle Azure

Si vous disposez d’une machine virtuelle qui ne nécessite plus d’identité du service administré :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles ».

2. Accédez à la machine virtuelle souhaitée.

3. Cliquez sur la page « Configuration », supprimez l’identité du service administré à partir de la machine virtuelle en sélectionnant « Non » sous « Identité du service administré », puis cliquez sur **Enregistrer**. Cette opération peut durer 60 secondes ou plus :

   ![Capture d’écran de la page Configuration](~/articles/active-directory/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](msi-overview.md).

## <a name="next-steps"></a>Étapes suivantes

- À l’aide du portail Azure, accordez à l’identité du service administré d’une machine virtuelle Azure [un accès à une autre ressource Azure](msi-howto-assign-access-portal.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.
