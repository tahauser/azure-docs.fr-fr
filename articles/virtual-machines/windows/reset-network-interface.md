---
title: "Procédure de réinitialisation de l’interface réseau pour les machines virtuelles Windows Azure | Microsoft Docs"
description: "Montre comment réinitialiser l’interface réseau pour une machine virtuelle Windows Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: genlin
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 6bf5c991e8a96cfdcbad971e0f2ea2dfd01f2893
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Comment réinitialiser l’interface réseau pour une machine virtuelle Windows Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Vous ne pouvez pas vous connecter à une machine virtuelle Microsoft Azure Windows après avoir désactivé l’interface réseau (carte réseau) par défaut ou défini manuellement une adresse IP statique pour la carte réseau. Cet article explique comment réinitialiser l’interface réseau pour une machine virtuelle Windows Azure, ce qui résoudra le problème de connexion distante.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]
## <a name="reset-network-interface"></a>Réinitialiser l’interface réseau

### <a name="for-classic-vms"></a>Pour les machines virtuelles Classic

Pour réinitialiser l’interface réseau, procédez comme suit :

1.  Accédez au [portail Azure]( https://ms.portal.azure.com).
2.  Sélectionnez **Machines virtuelles (Classic)**.
3.  Sélectionnez la machine virtuelle concernée.
4.  Sélectionnez **Adresses IP**.
5.  Si l’**attribution d’adresse IP privée** n’est pas **Statique**, choisissez **Statique**.
6.  Modifiez l’**adresse IP** pour indiquer une autre adresse IP, disponible dans le sous-réseau.
7.  Sélectionnez Enregistrer.
8.  La machine virtuelle redémarre afin d’initialiser la nouvelle carte réseau pour le système.
9.  Essayez d’utiliser le protocole RDP sur votre machine. En cas de réussite, vous pouvez redéfinir l’adresse IP privée d’origine si vous le souhaitez. Sinon, vous pouvez conserver cette adresse. 

### <a name="for-vms-deployed-in-resource-group-model"></a>Pour les machines virtuelles déployées dans le modèle de groupe de ressources

1.  Accédez au [portail Azure]( https://ms.portal.azure.com).
2.  Sélectionnez la machine virtuelle concernée.
3.  Sélectionnez **Interfaces réseau**.
4.  Sélectionnez l’interface réseau associée à votre machine.
5.  Sélectionnez **Configurations IP**.
6.  Sélectionnez l’adresse IP. 
7.  Si l’**attribution d’adresse IP privée** n’est pas **Statique**, choisissez **Statique**.
8.  Modifiez l’**adresse IP** pour indiquer une autre adresse IP, disponible dans le sous-réseau.
9. La machine virtuelle redémarre afin d’initialiser la nouvelle carte réseau pour le système.
10. Essayez d’utiliser le protocole RDP sur votre machine. En cas de réussite, vous pouvez redéfinir l’adresse IP privée d’origine si vous le souhaitez. Sinon, vous pouvez conserver cette adresse. 

## <a name="delete-the-unavailable-nics"></a>Supprimer les cartes réseau non disponibles
Une fois que vous pouvez vous connecter directement à la machine à l’aide du bureau à distance, vous devez supprimer les anciennes cartes réseau pour éviter tout problème potentiel :

1.  Ouvrez le Gestionnaire de périphériques.
2.  Sélectionnez **Afficher** > **Afficher les périphériques cachés**.
3.  Sélectionnez **Adaptateurs réseau**. 
4.  Recherchez les adaptateurs nommés « Carte réseau de bus Microsoft Hyper-V ».
5.  Il se peut qu’un adaptateur non disponible apparaisse grisé. Cliquez avec le bouton droit sur l’adaptateur, puis sélectionnez Désinstaller.

    ![Image de la carte réseau](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Désinstallez uniquement les adaptateurs non disponibles qui portent le nom « Carte réseau de bus Microsoft Hyper-V ». Si vous désinstallez un autre adaptateur caché, cela peut entraîner des problèmes supplémentaires.
    >
    >

6.  À présent, tous les adaptateurs indisponibles ont normalement été nettoyés sur votre système.
