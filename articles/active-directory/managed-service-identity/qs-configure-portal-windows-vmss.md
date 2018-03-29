---
title: Configurer l’identité du service administré sur un groupe de machines virtuelles identiques à l’aide du portail Azure
description: Instructions détaillées sur la configuration de l’identité du service administré (MSI) sur une machine virtuelle Azure, à l’aide du portail Azure.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: d9b493203a78aebdfadef15cf53d9cc023bb66f8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="configure-an-azure-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>Configurer l’identité du service administré (MSI) d’un groupe de machines virtuelles identiques à l’aide du portail Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez apprendre à activer et supprimer l’identité du service administré d’un groupe de machines virtuelles identiques Azure, à l’aide du portail Azure.

## <a name="prerequisites"></a>Prérequis


[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-azure-virtual-machine-scale-set"></a>Activer l’identité du service administré lors de la création d’un groupe de machines virtuelles identiques Azure

Au moment de rédiger cet article, l’activation de l’identité du service administré lors de la création d’un groupe de machines virtuelles identiques dans le portail Azure n’est pas prise en charge. À la place, veuillez vous référer au démarrage rapide suivant sur la création d’un groupe de machines virtuelles identiques Azure pour en créer un d’abord :

- [Créer un groupe de machines virtuelles identiques dans le portail Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

Passez ensuite à la section suivante pour plus d’informations sur l’activation de l’identité du service administré sur le groupe de machines virtuelles identiques.

## <a name="enable-msi-on-an-existing-azure-vmms"></a>Activer l’identité du service administré sur un groupe de machines virtuelles identiques Azure existant

Si vous avez un groupe de machines virtuelles identiques qui a été initialement approvisionné sans identité du service administré :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques.

2. Accédez au groupe de machines virtuelles identiques souhaité.

3. Cliquez sur la page **Configuration**, activez l’identité du service administré sur le groupe de machines virtuelles identiques en sélectionnant **Oui** sous « Identité du service administré », puis cliquez sur **Enregistrer**. Cette opération peut durer 60 secondes ou plus :

   ![Capture d’écran de la page Configuration](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Supprimer l’identité du service administré d’un groupe de machines virtuelles identiques Azure

Si vous disposez d’un groupe de machines virtuelles identiques qui ne nécessite plus d’identité du service administré :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur le groupe de machines virtuelles identiques.

2. Accédez au groupe de machines virtuelles identiques souhaité.

3. Cliquez sur la page **Configuration**, supprimez l’identité du service administré sur le groupe de machines virtuelles identiques en sélectionnant **Non** sous **Identité du service administré**, puis cliquez sur **Enregistrer**. Cette opération peut durer 60 secondes ou plus :

   ![Capture d’écran de la page Configuration](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](overview.md).
- À l’aide du portail Azure, accordez à l’identité du service administré d’un groupe de machines virtuelles identiques un [accès à une autre ressource Azure](howto-assign-access-portal.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.
