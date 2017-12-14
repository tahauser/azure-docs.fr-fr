---
title: "Tâches courantes de gestion de service cloud | Microsoft Docs"
description: "Découvrez comment gérer des services cloud dans le portail Azure. Ces exemples utilisent le portail Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: a06becda42541b22d3e090087dd96cd08c980f44
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="how-to-manage-cloud-services"></a>Gestion des services cloud
Dans la zone **Services cloud** du portail Azure, vous pouvez mettre à jour un rôle de service ou un déploiement, promouvoir un déploiement intermédiaire en déploiement de production, lier des ressources à votre service cloud afin de voir les dépendances de ressources et d’étendre les ressources en même temps, mais aussi supprimer un service cloud ou un déploiement.

Vous trouverez plus d’informations sur la mise à l’échelle de votre service cloud [ici](cloud-services-how-to-scale-portal.md).

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Mise à jour d’un rôle ou d’un déploiement de service cloud
Si vous devez mettre à jour le code de l'application pour votre service cloud, utilisez **Update** dans le panneau de service cloud. Vous pouvez mettre à jour un ou plusieurs rôles. Pour effectuer la mise à jour, vous pouvez charger un nouveau package de service ou un nouveau fichier de configuration de service.

1. Dans le [portail Azure][Azure portal], sélectionnez le service cloud à mettre à jour. Cette étape ouvre le panneau d'instance de service cloud.
2. Dans le panneau, cliquez sur le bouton **Update** .

    ![Bouton Update](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Mettez à jour le déploiement avec un nouveau fichier de package de service (.cspkg) et un fichier de configuration de service (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **éventuellement** à jour l'étiquette de déploiement et le compte de stockage.
5. Si l’un des rôles ne comporte qu’une seule instance, sélectionnez **Déployer même si un ou plusieurs rôles contiennent une seule instance** afin de permettre à la mise à niveau de continuer.

    Azure ne peut garantir 99,95 % de disponibilité du service pendant la mise à jour du service cloud que si chaque rôle dispose d'au moins deux instances de rôle (machines virtuelles). Avec deux instances de rôle, une machine virtuelle traite les demandes du client pendant que l'autre est mise à jour.

6. Cochez la case **Démarrer le déploiement** pour appliquer la mise à jour après le téléchargement du package.
7. Cliquez sur **OK** pour démarrer la mise à jour du service.

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Inversion de déploiements pour faire passer un déploiement intermédiaire en production
Lorsque vous décidez de déployer une nouvelle version d'un service cloud, créez un déploiement intermédiaire et testez la nouvelle version dans l'environnement intermédiaire de votre service cloud. Utilisez **Swap** pour inverser les URL qui permettent d'accéder aux deux déploiements et de faire passer une nouvelle version en production.

Vous pouvez inverser les déploiements à partir de la page **Cloud Services** ou du tableau de bord.

1. Dans le [portail Azure][Azure portal], sélectionnez le service cloud à mettre à jour. Cette étape ouvre le panneau d'instance de service cloud.
2. Dans le panneau, cliquez sur le bouton **Swap** .

    ![Inverser les services cloud](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. L'invite de confirmation suivante s'affiche.

    ![Inverser les services cloud](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Une fois les informations de déploiement vérifiées, cliquez sur **OK** pour inverser les déploiements.

    L'inversion des déploiements se fait rapidement, car la seule chose qui change est l'adresse IP virtuelle (VIP) des déploiements.

    Afin de réduire les coûts liés au calcul, vous pouvez supprimer le déploiement intermédiaire une fois que vous êtes certain que votre déploiement de production se comporte comme vous le souhaitez.

### <a name="common-questions-about-swapping-deployments"></a>Questions courantes sur l’échange de déploiements

**Quelles sont les conditions préalables à l’échange des déploiements ?**

Il existe deux conditions préalables principales pour qu’un échange de déploiements réussisse :

- Si vous souhaitez utiliser une adresse IP statique pour votre emplacement de production, vous devez en réserver une pour votre emplacement intermédiaire également. Sinon, l’échange échoue.

- Toutes les instances de vos rôles doivent être en cours d’exécution pour que vous puissiez effectuer l’échange. Vous pouvez vérifier l’état de vos instances dans le volet d’aperçu du portail Azure. Vous pouvez également utiliser la commande [Get-AzureRole](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0) dans Windows PowerShell.

Notez que les mises à jour du système d’exploitation invité et les opérations de réparation de service peuvent entraîner l’échec du déploiement. Pour plus d’informations, consultez [Résoudre les problèmes de déploiement de service cloud](cloud-services-troubleshoot-deployment-problems.md).

**Un échange implique-t-il un temps d’arrêt pour mon application ? Comment dois-je le gérer ?**

Comme décrit dans la dernière section, un échange de déploiements est généralement rapide puisqu’il s’agit simplement d’une modification de configuration d’Azure Load Balancer. Toutefois, dans certains cas, il peut prendre au moins dix secondes et entraîner des échecs de connexion temporaires. Pour limiter l’impact sur vos clients, envisagez d’implémenter la [logique de nouvelle tentative client](../best-practices-retry-general.md).

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Suppression de déploiements et d’un service cloud
Avant de pouvoir supprimer un service cloud, vous devez supprimer tous les déploiements existants.

Afin de réduire les coûts liés au calcul, vous pouvez supprimer le déploiement intermédiaire une fois que vous êtes certain que votre déploiement de production se comporte comme vous le souhaitez. Vous êtes facturé pour les coûts de calcul des instances de rôle déployées qui ont été arrêtées.

Utiliser la procédure suivante pour supprimer un déploiement ou un service cloud.

1. Dans le [portail Azure][Azure portal], sélectionnez le service cloud à supprimer. Cette étape ouvre le panneau d'instance de service cloud.
2. Dans le panneau, cliquez sur le bouton **Delete** .

    ![Inverser les services cloud](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Vous pouvez supprimer tout le service cloud en cochant **Service cloud et ses déploiements**, ou bien choisir le **déploiement de production** ou le **déploiement intermédiaire**.

    ![Inverser les services cloud](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Cliquez sur le bouton **Delete** en bas de la page.
5. Pour supprimer le service cloud, cliquez sur **Delete cloud service**. Ensuite, à l'invite de confirmation, cliquez sur **Yes**.

> [!NOTE]
> Lorsqu’un service cloud est supprimé et que la surveillance détaillée est configurée, vous devez supprimer manuellement les données de votre compte de stockage. Pour plus d'informations sur les tables de mesures, consultez [cet](cloud-services-how-to-monitor.md) article.


## <a name="how-to-find-more-information-about-failed-deployments"></a>Comment trouver plus d’informations sur les déploiements échoués
Le panneau **d’aperçu** possède une barre d’état en haut. Lorsque vous cliquez sur la barre, un nouveau panneau s’ouvre et affiche toutes les informations d’erreur. Si le déploiement ne contient pas d’erreurs, le panneau informations est vide.

![Inverser les services cloud](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Étapes suivantes
* [Configuration générale de votre service cloud](cloud-services-how-to-configure-portal.md).
* Découvrez comment [déployer un service cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurez un [nom de domaine personnalisé](cloud-services-custom-domain-name-portal.md).
* Configurez des [certificats SSL](cloud-services-configure-ssl-certificate-portal.md).
