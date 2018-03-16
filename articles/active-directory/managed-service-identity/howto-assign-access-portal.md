---
title: "Attribuer à une identité du service administré un accès à une ressource Azure à l’aide du portail Azure"
description: "Instructions détaillées pour attribuer une identité du service administré à une ressource et un accès à une autre ressource, à l’aide du portail Azure."
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 9120104955aac8ca8a0568e4519c99e1bd786541
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Attribuer à une identité du service administré un accès à une ressource à l’aide du portail Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Après avoir configuré une ressource Azure avec une identité du service administré (MSI), vous pouvez accorder à cette dernière un accès à une autre ressource, tout comme n’importe quel principal de sécurité. Cet article montre comment accorder à l’identité du service administré d’une machine virtuelle ou d’un groupe de machines virtuelles identiques Azure un accès à un compte de stockage Azure, à l’aide du portail Azure.

## <a name="prerequisites"></a>Prérequis


[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Utiliser le contrôle d’accès en fonction du rôle (RBAC) pour attribuer à l’identité du service administré un accès à une autre ressource

Après avoir activé l’identité du service administré sur une ressource Azure, telle qu’une [machine virtuelle Azure](qs-configure-portal-windows-vm.md) ou un [groupe de machines virtuelles identiques Azure](qs-configure-portal-windows-vmss.md) :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure sur lequel vous avez configuré l’identité du service administré.

2. Accédez à la ressource souhaitée sur laquelle vous voulez modifier le contrôle d’accès. Dans cet exemple, nous accordons à une machine virtuelle et à un groupe de machines virtuelles identiques Azure l’accès à un compte de stockage auquel nous accédons.

3. Pour une machine virtuelle Azure, sélectionnez la page **Contrôle d’accès (IAM)** de la ressource, puis sélectionnez **+ Ajouter**. Spécifiez le **rôle**, **attribuez un accès à une machine virtuelle** et spécifiez **l’abonnement** et le **groupe de ressources** correspondants où se situe la ressource. La ressource doit être située sous la zone de critères de recherche. Sélectionnez la ressource puis sélectionnez **Enregistrer**. 

   ![Capture d’écran du contrôle d’accès (IAM)](../media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
   Pour un groupe de machines virtuelles identiques Azure, sélectionnez la page **Contrôle d’accès (IAM)** de la ressource, puis sélectionnez **+ Ajouter**. Spécifiez ensuite le **Rôle**, **Attribuer l’accès à**. Dans la zone de critères de recherche, recherchez votre groupe de machines virtuelles identiques. Sélectionnez la ressource puis sélectionnez **Enregistrer**.
   
   ![Capture d’écran du contrôle d’accès (IAM)](../media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-before.png)  

4. Vous être renvoyé à la page principale **Contrôle d’accès (IAM)**, où s’affiche une nouvelle entrée pour l’identité du service administré de la ressource.

    Machine virtuelle Azure :

   ![Capture d’écran du contrôle d’accès (IAM)](../media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

    Groupe de machines virtuelles identiques Azure :

    ![Capture d’écran du contrôle d’accès (IAM)](../media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-after.png)

## <a name="troubleshooting"></a>Résolution de problèmes

Si l’identité du service administré pour la ressource n’apparaît pas dans la liste des identités disponibles, vérifiez si elle a été activée correctement. Dans notre cas, nous pouvons retourner sur la machine virtuelle Azure et vérifier les points suivants :

- Examinez la page **Configuration** et assurez-vous que la valeur du paramètre **Identité du service administré activé** est **Oui**.
- Examinez la page **Extensions** et vérifiez que l’extension de l’identité du service administré a été correctement déployée (la page **Extensions** n’est pas disponible pour un groupe de machines virtuelles identiques Azure).

En cas d’inexactitude, il est possible que vous deviez redéployer l’identité du service administré sur votre ressource, ou résoudre le problème de déploiement.

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](overview.md).
- Pour activer l’identité du service administré sur une machine virtuelle Azure, consultez [Configurer l’identité du service administré (MSI) d’une machine virtuelle Azure à l’aide du portail Azure](qs-configure-portal-windows-vm.md).
- Pour activer l’identité du service administré sur un groupe de machines virtuelles identiques Azure, consultez [Configurer l’identité du service administré (MSI) d’un groupe de machines virtuelles identiques Azure à l’aide du portail Azure](qs-configure-portal-windows-vmss.md)


