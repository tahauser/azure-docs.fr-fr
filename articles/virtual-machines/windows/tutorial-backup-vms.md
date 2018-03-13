---
title: Sauvegarde des machines virtuelles Azure Windows | Microsoft Docs
description: "Protégez vos machines virtuelles Windows en les sauvegardant à l’aide de Sauvegarde Azure."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9638c7148d4347bb9cbc9e48022037ee6cb983d7
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="back-up-windows-virtual-machines-in-azure"></a>Sauvegarde de machines virtuelles Windows dans Azure

Vous pouvez protéger vos données en effectuant des sauvegardes à intervalles réguliers. Azure Backup crée des points de récupération stockés dans des coffres de récupération géoredondants. Lorsque vous effectuez une restauration à partir d’un point de récupération, vous pouvez restaurer la machine virtuelle entière ou seulement des fichiers spécifiques. Cet article explique comment restaurer un fichier unique sur une machine virtuelle exécutant Windows Server et IIS. Si vous ne disposez d’aucune machine virtuelle, vous pouvez en créer une à l’aide du [démarrage rapide Windows](quick-create-portal.md). Ce didacticiel vous explique comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une sauvegarde de machine virtuelle
> * Planifier une sauvegarde quotidienne
> * Restaurer un fichier à partir d’une sauvegarde




## <a name="backup-overview"></a>Présentation de la sauvegarde

Lorsque le service Sauvegarde Azure lance une sauvegarde, il déclenche l’extension de sauvegarde pour prendre un instantané à un moment donné. Le service Sauvegarde Azure utilise l’extension _VMSnapshot_. L’extension est installée lors de la première sauvegarde de machine virtuelle si cette machine virtuelle est exécutée. Si la machine virtuelle n’est pas en cours d’exécution, le service Sauvegarde prend un instantané du stockage sous-jacent (car aucune écriture de l’application n’a lieu pendant l’arrêt de la machine virtuelle).

Lorsque de la prise d’un instantané de machines virtuelles Windows, le service Sauvegarde se coordonne avec le service VSS pour obtenir un instantané cohérent des disques de la machine virtuelle. Après que le service Sauvegarde Azure a pris l’instantané, les données sont transférées vers le coffre de sauvegarde. Pour optimiser l’efficacité, le service identifie et transfère uniquement les blocs de données qui ont été modifiés depuis la sauvegarde précédente.

Une fois le transfert de données terminé, l’instantané est supprimé et un point de récupération est créé.


## <a name="create-a-backup"></a>Création d'une sauvegarde
Créez une simple sauvegarde quotidienne planifiée dans un coffre Recovery Services. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche, sélectionnez **Machines virtuelles**. 
3. Dans la liste, sélectionnez la machine virtuelle que vous souhaitez sauvegarder.
4. Dans le panneau de la machine virtuelle, au niveau de la section **Paramètres**, cliquez sur **Sauvegarde**. Le panneau **Activer la sauvegarde** s’ouvre.
5. Dans **Coffre Recovery Services**, cliquez sur **Créer** et fournissez le nom du nouveau coffre. Un coffre est créé dans le même groupe de ressources et au même emplacement que la machine virtuelle.
6. Cliquez sur **Stratégie de sauvegarde**. Pour cet exemple, conservez les valeurs par défaut et cliquez sur **OK**.
7. Dans le panneau **Activer la sauvegarde**, cliquez sur **Activer la sauvegarde**. Cette opération crée une sauvegarde quotidienne selon la planification par défaut.
10. Pour créer un point de récupération initial, dans le panneau **Sauvegarde**, cliquez sur **Sauvegarder maintenant**.
11. Dans le panneau **Sauvegarder maintenant**, cliquez sur l’icône de calendrier. Utilisez le contrôle de calendrier pour sélectionner le dernier jour de conservation de ce point de récupération, puis cliquez sur **Sauvegarder**.
12. Le panneau **Sauvegarde** de votre machine virtuelle affiche le nombre de points de récupération terminés.

    ![Points de récupération](./media/tutorial-backup-vms/backup-complete.png)
    
La première sauvegarde prend environ 20 minutes. Une fois la sauvegarde terminée, passez à l’étape suivante de ce didacticiel.

## <a name="recover-a-file"></a>Récupérer un fichier

Si vous supprimez un fichier ou y apportez des modifications accidentellement, vous pouvez utiliser la récupération de fichier pour restaurer le fichier à partir de votre coffre de sauvegarde. La récupération de fichier utilise un script qui s’exécute sur la machine virtuelle pour monter le point de récupération en tant que lecteur local. Ces lecteurs restent montés pendant 12 heures pour vous permettre de copier des fichiers à partir du point de récupération et de les restaurer sur la machine virtuelle.  

Dans cet exemple, vous découvrirez comment récupérer le fichier image qui est utilisé dans la page web par défaut pour IIS. 

1. Ouvrez un navigateur et connectez-vous à l’adresse IP de la machine virtuelle pour afficher la page IIS par défaut.

    ![Page web IIS par défaut](./media/tutorial-backup-vms/iis-working.png)

2. Connectez-vous à la machine virtuelle.
3. Sur la machine virtuelle, ouvrez **Explorateur de fichiers**, accédez à \inetpub\wwwroot et supprimez le fichier **iisstart.png**.
4. Sur votre ordinateur local, actualisez le navigateur pour confirmer que cette image sur la page IIS par défaut a disparu.

    ![Page web IIS par défaut](./media/tutorial-backup-vms/iis-broken.png)

5. Sur votre ordinateur local, ouvrez un nouvel onglet et accédez au [portail Azure](https://portal.azure.com).
6. Dans le menu de gauche, sélectionnez **Machines virtuelles** et cliquez sur la machine virtuelle dans la liste.
8. Dans le panneau de la machine virtuelle, au niveau de la section **Paramètres**, cliquez sur **Sauvegarde**. Le panneau **Sauvegarde** s’ouvre. 
9. Dans le menu en haut du panneau, sélectionnez **Récupération de fichier**. Le panneau **Récupération de fichier** s’affiche.
10. Dans **Étape 1 : Sélectionner un point de récupération**, sélectionnez un point de récupération dans la liste déroulante.
11. Dans **Étape 2 : Télécharger le script pour parcourir et restaurer des fichiers**, cliquez sur le bouton **Télécharger le fichier exécutable**. Enregistrez le fichier dans votre dossier **Téléchargements**.
12. Sur votre ordinateur local, ouvrez **Explorateur de fichiers** et accédez à votre dossier**Téléchargements**, puis copiez le fichier .exe téléchargé. Le nom de fichier est prédéfini en fonction du nom de votre machine virtuelle. 
13. Sur votre machine virtuelle (via la connexion RDP), collez le fichier .exe sur le bureau de la machine virtuelle. 
14. Accédez au bureau de votre machine virtuelle et double-cliquez sur le fichier .exe. Cela lance une invite de commandes et monte le point de récupération sous forme de partage de fichier, auquel vous pouvez accéder. Après avoir créé le partage, entrez **q** pour fermer l’invite de commandes.
15. Sur votre machine virtuelle, ouvrez **Explorateur de fichiers** et accédez à la lettre de lecteur qui a été utilisée pour le partage de fichiers.
16. Accédez à \inetpub\wwwroot, copiez **iisstart.png** à partir du partage de fichier et collez-le dans \inetpub\wwwroot. Par exemple, copiez F:\inetpub\wwwroot\iisstart.png et collez-le dans c:\inetpub\wwwroot pour récupérer le fichier.
17. Sur votre ordinateur local, ouvrez l’onglet du navigateur avec lequel vous êtes connecté à l’adresse IP de la machine virtuelle affichant la page IIS par défaut. Appuyez sur CTRL + F5 pour actualiser la page du navigateur. Vous devriez maintenant voir que l’image a été restaurée.
18. Sur votre ordinateur local, revenez à l’onglet du navigateur pour afficher le portail Azure, puis dans **Étape 3 : Démonter les disques après la récupération**, cliquez sur le bouton **Démonter les disques**. Si vous avez omis cette étape, la connexion au point de montage est automatiquement fermée après 12 heures. Une fois ces 12 heures écoulées, vous devez télécharger un nouveau script pour créer un nouveau point de montage.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer une sauvegarde de machine virtuelle
> * Planifier une sauvegarde quotidienne
> * Restaurer un fichier à partir d’une sauvegarde

Passez au didacticiel suivant pour en savoir plus sur la surveillance des machines virtuelles.

> [!div class="nextstepaction"]
> [Gouverner les machines virtuelles](tutorial-govern-resources.md)









