---
title: Activation du chiffrement pour le compte de stockage dans Azure Security Center | Microsoft Docs
description: Ce document vous montre comment implémenter la recommandation d’Azure Security Center **Activation du chiffrement pour le compte de stockage Azure**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: terrylan
ms.openlocfilehash: 82bb201c0b518d0b45e06a1eb25d54f60cb3e028
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="enable-encryption-for-azure-storage-account-in-azure-security-center"></a>Activation du chiffrement pour le compte de stockage Azure dans Azure Security Center | Microsoft Docs
Azure Security Center peut vous recommander d’activer le chiffrement du service Azure Storage pour les données au repos.

SSE (Storage Service Encryption) chiffre les données lorsqu’elles sont écrites dans le stockage Azure et les déchiffre avant récupération.  SSE est actuellement disponible uniquement pour le service BLOB Azure et peut être utilisé pour les objets blob de blocs, les objets blob de pages les objets blob Append.  Pour en savoir plus, consultez [Azure Storage Service Encryption pour les données au repos](../storage/common/storage-service-encryption.md).


> [!Note]
> Après l’activation du chiffrement, seules les nouvelles données sont chiffrées. Tous les objets blob existants dans votre compte de stockage restent non chiffrés. Pour chiffrer les objets blob existants, consultez le [Forum aux questions sur le chiffrement du service de stockage](../storage/common/storage-service-encryption.md#faq-for-storage-service-encryption).
>
>

SSE est uniquement pris en charge sur les comptes de stockage Resource Manager. Les comptes de stockage classiques ne sont actuellement pas pris en charge. Pour comprendre les modèles de déploiement de type classique et Resource Manager, consultez [Modèles de déploiement Azure](../azure-classic-rm.md).

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Ce document n’est pas un guide pas à pas.
>
>

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
1. Dans le panneau **Recommandations**, sélectionnez **Activation du chiffrement pour le compte de stockage Azure**.
   ![Activer le chiffrement pour le compte Azure Storage][1]
2. Le panneau **Activer le chiffrement du stockage** s’ouvre. Ce panneau répertorie les comptes de stockage Azure sur lesquels le chiffrement de stockage est désactivé. Dans cet exemple, sélectionnons **storageacct1**.
   ![Activer le chiffrement du stockage][2]
3. Le panneau **Chiffrement** de **storageacct1** s’ouvre. Sélectionnez **Enabled**.
   ![Panneau Chiffrement][3]
4. Sélectionnez **Enregistrer**.

Vous avez activé le chiffrement de stockage pour **storageacct1**.


## <a name="see-also"></a>Voir aussi
Ce document vous montre comment implémenter la recommandation d’Azure Security Center « Activation du chiffrement pour le compte de stockage Azure ». Pour en savoir plus sur le chiffrement du service Azure Storage, consultez les ressources suivantes :

* [Azure Storage Service Encryption pour les données au repos](../storage/common/storage-service-encryption.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png
