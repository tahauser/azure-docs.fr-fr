---
title: "Dépanner une machine virtuelle Azure à l’aide de la virtualisation imbriquée dans Azure | Microsoft Docs"
description: "Guide pratique pour dépanner une machine virtuelle Azure posant problème à l’aide de la virtualisation imbriquée dans Azure"
services: virtual-machines-windows
documentationcenter: 
author: glimoli
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: genli
ms.openlocfilehash: b50d139f62ef773fb5b9bb2638e40f8154e80979
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Dépanner une machine virtuelle Azure à l’aide de la virtualisation imbriquée dans Azure

Cet article explique comment créer un environnement de virtualisation imbriquée dans Microsoft Azure pour pouvoir ensuite monter le disque de la machine virtuelle à dépanner sur l’hôte Hyper-V (machine virtuelle de récupération).

## <a name="prerequisite"></a>Configuration requise

Pour monter la machine virtuelle posant problème, la machine virtuelle de récupération doit remplir les prérequis suivants :

-   La machine virtuelle de récupération doit se trouver au même emplacement que la machine virtuelle posant problème.

-   La machine virtuelle de récupération doit se trouver dans le même groupe de ressources que la machine virtuelle posant problème.

-   La machine virtuelle de récupération doit utiliser le même type de compte de stockage (Standard ou Premium) que la machine virtuelle posant problème.

## <a name="step-1-create-a-recovery-vm-and-install-hyper-v-role"></a>Étape 1 : Créer une machine virtuelle de récupération et installer le rôle Hyper-V

1.  Créez une machine virtuelle de récupération :

    -  Système d’exploitation : Windows Server 2016 Datacenter

    -  Taille : n’importe quelle machine de la gamme V3 avec au moins deux cœurs prenant en charge la virtualisation imbriquée. Pour plus d’informations, consultez le blog [Introducing the new Dv3 and Ev3 VM sizes](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Même emplacement, même compte de stockage et même groupe de ressources que la machine virtuelle posant problème.

    -  Même type de stockage (Standard ou Premium) que la machine virtuelle posant problème.

2.  Après avoir créé la machine virtuelle de récupération, établissez une connexion Bureau à distance à cette machine virtuelle.

3.  Dans le Gestionnaire de serveur, sélectionnez **Gérer** > **Ajouter des rôles et des fonctionnalités**.

4.  Dans la section **Type d’installation**, sélectionnez **Installation basée sur un rôle ou une fonctionnalité**.

5.  Dans la section **Sélectionner le serveur de destination**, vérifiez que la machine virtuelle de récupération est sélectionnée.

6.  Sélectionnez le **rôle Hyper-V** > **Ajouter des fonctionnalités**.

7.  Sélectionnez **Suivant** dans la section **Fonctionnalités**.

8.  Si un commutateur virtuel est disponible, sélectionnez-le. Sinon, sélectionnez **Suivant**.

9.  Dans la section **Migration**, sélectionnez **Suivant**.

10. Dans la section **Emplacements par défaut**, sélectionnez **Suivant**.

11. Cochez la case pour redémarrer automatiquement le serveur si nécessaire.

12. Sélectionnez **Installer**.

13. Autorisez le serveur à installer le rôle Hyper-V. Cette opération prend quelques minutes. Le serveur redémarre ensuite automatiquement.

## <a name="step-2-create-the-problem-vm-on-the-recovery-vms-hyper-v-server"></a>Étape 2 : Créer la machine virtuelle posant problème sur le serveur Hyper-V de la machine virtuelle de récupération

1.  Enregistrez le nom du disque sur la machine virtuelle posant problème, puis supprimez cette machine virtuelle. Veillez à conserver tous les disques attachés. 

2.  Attachez le disque du système d’exploitation de la machine virtuelle posant problème comme disque de données de la machine virtuelle de récupération.

    1.  Après avoir supprimé la machine virtuelle posant problème, accédez à la machine virtuelle de récupération.

    2.  Sélectionnez **Disques**, puis **Ajouter un disque de données**.

    3.  Sélectionnez le disque de la machine virtuelle posant problème, puis sélectionnez **Enregistrer**.

3.  Une fois que vous avez attaché le disque, établissez une connexion Bureau à distance à la machine virtuelle de récupération.

4.  Ouvrez Gestion des disques (diskmgmt.msc). Vérifiez que le disque de la machine virtuelle posant problème est défini sur **Hors connexion**.

5.  Ouvrez le Gestionnaire Hyper-V : dans **Gestionnaire de serveur**, sélectionnez le **rôle Hyper-V**. Cliquez avec le bouton droit sur le serveur, puis sélectionnez **Gestionnaire Hyper-V**.

6.  Dans le Gestionnaire Hyper-V, cliquez avec le bouton droit sur la machine virtuelle de récupération, puis sélectionnez **Nouvelle** > **Machine virtuelle** > **Suivant**.

7.  Tapez un nom pour la machine virtuelle, puis sélectionnez **Suivant**.

8.  Sélectionnez **Génération 1**.

9.  Définissez la mémoire de démarrage à 1 024 Mo ou plus.

10. Le cas échéant, sélectionnez le commutateur réseau Hyper-V qui a été créé. Sinon, passez à la page suivante.

11. Sélectionnez **Attacher un disque dur virtuel ultérieurement**.

    ![image de l’option Attacher un disque dur virtuel ultérieurement](./media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Sélectionnez **Terminer** quand la machine virtuelle a été créée.

13. Cliquez avec le bouton droit sur la machine virtuelle que vous avez créée, puis sélectionnez **Paramètres**.

14. Sélectionnez **Contrôleur IDE 0**, sélectionnez **Disque dur**, puis cliquez sur **Ajouter**.

    ![image de l’ajout d’un nouveau disque dur](./media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. Dans **Disque dur physique**, sélectionnez le disque de la machine virtuelle posant problème que vous avez attaché à la machine virtuelle Azure. Si vous ne voyez pas de disques répertoriés, vérifiez si le disque est défini sur Hors connexion à l’aide de la fonction Gestion des disques.

    ![image du montage du disque](./media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Sélectionnez **Apply** (Appliquer), puis **OK**.

18. Double-cliquez sur la machine virtuelle, puis démarrez-la.

19. Vous pouvez maintenant utiliser la machine virtuelle en tant que machine virtuelle locale. Vous pouvez effectuer les étapes de dépannage nécessaires.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Étape 3 : Recréer votre machine virtuelle Azure dans Azure

1.  Après avoir remis en ligne la machine virtuelle, arrêtez la machine virtuelle dans le Gestionnaire Hyper-V.

2.  Accédez au [portail Azure](https://portal.azure.com) et sélectionnez Machine virtuelle de récupération > Disques, puis copiez le nom du disque. Vous aurez besoin de ce nom à l’étape suivante. Détachez le disque monté de la machine virtuelle de récupération.

3.  Accédez à **Toutes les ressources**, recherchez le nom du disque, puis sélectionnez le disque.

     ![image de la recherche du disque](./media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Cliquez sur **Créer une machine virtuelle**.

     ![image de la création de la machine virtuelle à partir du disque](./media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Vous pouvez également utiliser Azure PowerShell pour créer la machine virtuelle à partir du disque. Pour plus d’informations, consultez [Créer la machine virtuelle à partir d’un disque existant à l’aide de PowerShell](create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes pour vous connecter à votre machine virtuelle, consultez [Dépannage d’une connexion Bureau à distance à une machine virtuelle Azure](troubleshoot-rdp-connection.md). Pour résoudre les problèmes liés à l’accès aux applications exécutées sur votre machine virtuelle, consultez [Résoudre les problèmes de connectivité des applications sur une machine virtuelle Windows](troubleshoot-app-connection.md).
