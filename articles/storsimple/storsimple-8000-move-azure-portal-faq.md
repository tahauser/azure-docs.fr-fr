---
title: "Questions fréquentes (FAQ) sur le passage du portail Classic au portail Azure | Microsoft Docs"
description: "Fournit des réponses aux questions fréquemment posées sur le passage des appareils StorSimple du portail Classic au portail Azure."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: ac57894e4f180f42f80479d2031f4dd5ddfdb1be
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="move-storsimple-device-manager-service-from-classic-to-azure-portal-frequently-asked-questions-faq"></a>Questions fréquentes (FAQ) sur le passage du service StorSimple Device Manager du portail Classic au portail Azure

## <a name="overview"></a>Vue d’ensemble

Vous trouverez ci-dessous les questions et réponses éventuelles quand votre service StorSimple Device Manager en cours d’exécution dans le portail Azure Classic passe au portail Azure.

Les questions/réponses sont classées dans les catégories suivantes :

* Déplacement d’un service StorSimple Device Manager
* Déplacement des comptes de stockage
* Utilisation des applets de commande Azure Resource Manager
* Utilisation du service StorSimple Data Manager
* Divers

## <a name="moving-storsimple-device-manager-service"></a>Déplacement d’un service StorSimple Device Manager

### <a name="once-i-have-moved-to-azure-portal-can-i-still-create-a-storsimple-manager-service-in-the-classic-portal"></a>Une fois que je suis passé au portail Azure, est-il toujours possible de créer un service StorSimple Manager dans le portail Classic ?

Non. Une fois que vous avez migré votre service StorSimple Manager vers le portail Azure, vous ne pouvez pas créer un service dans le portail Classic. De plus, il faut savoir que [le portail Classic ne sera pas disponible à partir du 8 janvier 2018](https://azure.microsoft.com/updates/azure-portal-updates-for-classic-portal-users). 

### <a name="i-have-multiple-storsimple-managers-running-in-the-classic-portal-can-i-choose-which-ones-to-move-to-the-azure-portal"></a>Je dispose de plusieurs StorSimple Managers en cours d’exécution dans le portail Classic. Puis-je choisir ceux à déplacer vers le portail Azure ?

Non. Vous ne pouvez pas choisir les StorSimple Managers à déplacer vers le portail Azure. Tous les StorSimple Managers inclus dans votre abonnement sont déplacés.


### <a name="i-initiated-the-migration-of-my-service-to-the-new-azure-portal-should-i-delete-the-storsimple-manager-from-the-classic-portal"></a>J’ai lancé la migration de mon service vers le nouveau portail Azure. Dois-je supprimer le StorSimple Manager du portail Classic ? 

Non. N’essayez pas de supprimer un service que vous avez déplacé à partir du portail Classic. Attendez la fin de la migration ; une fois que tous les StorSimple Managers ont été déplacés vers le nouveau portail, vous n’avez à supprimer aucun service du portail Classic. Finalement, le portail Classic est déprécié.

### <a name="can-i-rename-my-storsimple-device-manager-service-in-the-azure-portal"></a>Puis-je renommer mon service StorSimple Device Manager dans le portail Azure ?

Non. Impossible de modifier le nom du service une fois que le service a été créé dans le portail Azure. Le même comportement s’applique également pour les autres entités telles que les appareils, les volumes, les conteneurs de volumes et les stratégies de sauvegarde.

### <a name="can-i-create-the-80108020-storsimple-cloud-appliances-with-azure-resource-manager-deployment-model"></a>Puis-je créer les appliances StorSimple Cloud Appliance 8010/8020 avec le modèle de déploiement Azure Resource Manager ?

Oui. Vous pouvez créer des appliances StorSimple Cloud Appliance 8010/8020 dans le modèle de déploiement Azure Resource Manager. Les machines virtuelles sous-jacentes pour ces appliances cloud sont également dans le modèle de déploiement Resource Manager.

### <a name="when-we-migrate-subscriptions-to-the-azure-portal-will-the-underlying-vms-for-the-storsimple-cloud-appliances-also-migrate-to-azure-resource-management-deployment-model"></a>Quand nous migrons des abonnements vers le portail Azure, les machines virtuelles sous-jacentes pour les appliances StorSimple Cloud Appliance sont-elles également migrées vers le modèle de déploiement de gestion des ressources Azure ?

Le déplacement du service StorSimple est indépendant de la gestion des machines virtuelles pour les appliances StorSimple Cloud Appliance. Vous pouvez gérer entièrement les machines virtuelles pour les appliances StorSimple Cloud Appliance à la fois dans le portail Classic et le portail Azure même aujourd’hui.

### <a name="can-i-manage-existing-classic-80108020-storsimple-cloud-appliance-from-the-azure-portal"></a>Puis-je gérer des appliances StorSimple Cloud Appliance 8010/8020 classiques existantes à partir du portail Azure ?

Oui. Les machines virtuelles associées à des appliances cloud 8010/8020 existantes peuvent être gérées à partir du portail Azure.

Si vous avez créé des appliances StorSimple Cloud Appliance modèle 8010/8020 exécutant Update 3.0 et versions ultérieures, vous n’êtes pas affecté par le déplacement de votre service vers le nouveau portail Azure. Vous devez être en mesure de gérer entièrement vos appliances cloud sans aucun problème. 

Si vous possédez des appliances cloud exécutant des versions antérieures à Update 3.0 dans le portail Classic, vous disposez alors uniquement de fonctionnalités limitées. Pour plus d’informations, accédez à la [liste des opérations non prises en charge pour les appareils exécutant des versions antérieures à Update 3.0](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50).

Vous ne pouvez pas mettre à jour une appliance cloud. Utilisez la version la plus récente du logiciel pour créer une appliance cloud et basculez ensuite les conteneurs de volumes existants vers la nouvelle appliance cloud créée. Pour plus d’informations, accédez à [Basculer vers l’appliance cloud](storsimple-8000-cloud-appliance-u2.md#fail-over-to-the-cloud-appliance)


### <a name="my-storsimple-8000-series-device-is-running-update-20-i-migrated-my-service-to-new-azure-portal-my-device-connected-successfully-but-it-seems-that-i-am-not-able-to-fully-manage-my-device-how-do-i-resolve-this-behavior"></a>Mon appareil de série StorSimple 8000 exécute Update 2.0. J’ai migré mon service vers le nouveau portail Azure. Mon appareil s’est connecté avec succès, mais il semble que je ne suis pas en mesure de le gérer entièrement. Comment résoudre ce problème ?

Le nouveau portail Azure est pris en charge uniquement pour les appareils StorSimple exécutant Update 3.0 et versions ultérieures. Si votre appareil exécutait Update 2.0, vous disposez uniquement de fonctionnalités limitées. Pour plus d’informations, accédez à la [liste des opérations non prises en charge pour les appareils exécutant des versions antérieures à Update 3.0](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50).

Pour gérer complètement votre appareil, installez-y la dernière mise à jour. Pour plus d’informations, accédez à [Installer Update 5](storsimple-8000-install-update-5.md).

### <a name="i-just-moved-my-storsimple-manager-service-to-the-azure-portal-i-am-seeing-some-alerts-related-to-my-device-is-this-behavior-related-to-the-move"></a>Je viens de déplacer mon service StorSimple Manager vers le portail Azure. Je vois des alertes liées à mon appareil. Ce comportement est-il relatif au déplacement ?

Non. Le déplacement proprement dit ne doit pas générer d’erreurs ni d’alertes. Suivez les recommandations des alertes pour les résoudre.

### <a name="i-am-using-a-storsimple-50007000-series-device-are-these-also-supported-in-the-azure-portal"></a>J’utilise un appareil de série StorSimple 5000/7000. Ces appareils sont-ils également pris en charge dans le portail Azure ?

Non. Les appareils de série StorSimple 5000/7000 ne sont pas pris en charge dans le portail Azure.

### <a name="i-am-planning-to-migrate-data-from-storsimple-50007000-series-device-to-a-storsimple-8000-series-device-how-does-moving-a-service-to-azure-portal-impact-my-data-migration"></a>J’ai l’intention de migrer des données à partir d’un appareil de série StorSimple 5000/7000 vers un appareil de série StorSimple 8000. Quel est l’impact du déplacement d’un service vers le portail Azure sur la migration de mes données ? 

Vous pouvez migrer des données à partir de votre appareil de série StorSimple 5000/7000 vers un appareil de série StorSimple 8000 exécuté dans le portail Azure. Pour vous permettre de migrer des données de la série 5000/7000 vers la série 8000, vous devez [enregistrer un ticket de support auprès du support Microsoft](storsimple-8000-contact-microsoft-support.md).


## <a name="moving-subscriptions-storage-accounts-resource-groups"></a>Déplacement des abonnements, comptes de stockage, groupes de ressources

### <a name="can-i-move-storsimple-device-manager-from-one-subscription-to-another-subscription-in-the-azure-portal"></a>Puis-je déplacer StorSimple Device Manager d’un abonnement vers un autre dans le portail Azure ?

Non. Le déplacement du service StorSimple Device Manager d’un abonnement vers un autre n’est pas pris en charge. Vous pouvez effectuer un processus manuel constitué des étapes suivantes :

* Migrez les données en dehors de l’appareil StorSimple.
* Effectuez une réinitialisation aux paramètres d’usine de l’appareil, pour supprimer toutes les données locales sur celui-ci.
* Inscrivez l’appareil dans le nouvel abonnement à un service StorSimple Device Manager.
* Refaites migrer les données sur l’appareil.

### <a name="do-i-have-to-migrate-the-storage-account-to-azure-resource-manager-deployment-model"></a>Dois-je migrer le compte de stockage vers le modèle de déploiement Azure Resource Manager ?

Non. Vos comptes de stockage classiques peuvent être entièrement gérés à partir du portail Azure. Quand vous déplacez votre service StorSimple vers le portail Azure, votre compte de stockage continue de fonctionner comme avant.

### <a name="what-happens-to-the-storage-account-after-the-service-is-migrated-to-the-azure-portal"></a>Qu’arrive-t-il au compte de stockage une fois que le service est migré vers le portail Azure ?

Le déplacement du service n’est pas lié à la gestion du compte de stockage. Les comptes de stockage classiques et Azure Resource Manager peuvent être entièrement gérés au sein du portail Azure. Une fois que vous déplacez votre service vers le portail Azure, votre appareil doit continuer à s’exécuter avec ce compte de stockage et il ne doit y avoir aucun impact sur vos données.

### <a name="can-i-migrate-storsimple-device-manager-from-one-resource-group-to-another"></a>Puis-je migrer StorSimple Device Manager d’un groupe de ressources vers un autre ?

Non. Vous ne pouvez pas déplacer un service StorSimple Device Manager créé avec un groupe de ressources vers un autre groupe de ressources.

## <a name="using-azure-resource-manager-based-cmdlets"></a>Utilisation des applets de commande Azure Resource Manager

### <a name="i-moved-to-the-new-azure-portal-now-my-scripts-based-on-azure-classic-deployment-model-powershell-cmdlets-are-not-working-what-do-i-need-to-do"></a>Je suis passé au nouveau portail Azure. Maintenant, mes scripts basés sur les applets de commande PowerShell du modèle de déploiement Azure Classic ne fonctionnent pas. Que dois-je faire ?

Les applets de commande PowerShell du modèle de déploiement Azure Classic existantes ne sont pas prises en charge dans le portail Azure. Mettez à jour les scripts pour gérer vos appareils via Azure Resource Manager. Pour plus d’informations sur la mise à jour de vos scripts, accédez à [Exemples pour le nouveau portail Azure](https://github.com/anoobbacker/storsimpledevicemgmttools).

### <a name="i-just-moved-to-the-azure-portal-and-needed-to-roll-over-the-service-data-encryption-key-where-is-this-option-in-the-azure-portal"></a>Je viens juste de passer au portail Azure et j’ai besoin de substituer la clé de chiffrement des données de service. Où se trouve cette option dans le portail Azure ?

L’option permettant de substituer la clé de chiffrement des données de service ne figure pas dans le portail Azure. Pour plus d’informations sur la procédure de substitution dans le portail Azure, accédez à [Changer la clé de chiffrement des données de service](storsimple-8000-manage-service.md#change-the-service-data-encryption-key).

### <a name="i-am-using-windows-powershell-for-storsimple-cmdlets-on-the-storsimple-device-to-perform-operations-such-extract-a-support-package-are-these-cmdlets-affected-when-i-move-to-the-new-azure-portal"></a>J’utilise les applets de commande Windows PowerShell pour StorSimple sur l’appareil StorSimple afin d’effectuer des opérations telles que l’extraction d’un package de support. Ces applets de commande sont-elles affectées quand je passe au nouveau portail Azure ?

Non. Avec le déplacement de votre service vers le nouveau portail Azure, il ne doit y avoir aucun impact sur les applets de commande Windows PowerShell pour StorSimple associées à l’appareil StorSimple local (qui n’est pas affecté par le déplacement). Vous pouvez continuer à utiliser ces applets de commande pour créer un package de support sans aucun problème même dans le portail Azure. Seules les applets de commande PowerShell du modèle de déploiement Azure Classic sont affectées par ce déplacement.

## <a name="moving-storsimple-data-manager-service"></a>Déplacement du service StorSimple Data Manager

### <a name="i-am-using-storsimple-data-manager-service-in-classic-azure-portal-how-should-i-proceed-with-this-move"></a>J’utilise le service StorSimple Data Manager dans le portail Azure Classic. Comment dois-je procéder avec ce déplacement ?

Si vous utilisez le service StorSimple Data Manager, vous avez été automatiquement déplacé vers le portail Azure.

## <a name="miscellaneous"></a>Divers

### <a name="i-am-running-storsimple-snapshot-manager-for-my-8000-series-device-is-there-any-impact-on-storsimple-snapshot-manager-when-i-move-to-azure-portal"></a>J’exécute StorSimple Snapshot Manager pour mon appareil de série 8000. Le passage au portail Azure a-t-il un impact sur StorSimple Snapshot Manager ?

Non. Le déplacement de votre service vers le portail Azure n’a aucun impact sur StorSimple Snapshot Manager. Votre appareil et StorSimple Snapshot Manager continuent de fonctionner comme avant.

### <a name="can-i-rename-my-storsimple-device-volume-containers-or-volumes"></a>Puis-je renommer mon appareil, les conteneurs de volumes ou les volumes StorSimple ?

Non. Vous ne pouvez pas renommer des appareils, des volumes, des conteneurs de volumes ni des stratégies de sauvegarde dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [Opérations prises en charge sur les appareils exécutant des versions antérieures à Update 5.0](storsimple-8000-manage-service.md#supported-operations-on-devices-running-versions-prior-to-update-50).



