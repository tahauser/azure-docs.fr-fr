---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d4075f644ed1ce42479e35544f0e1442488f78eb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans le coin supérieur gauche, cliquez sur **Créer une ressource** > **Calcul** > **Windows Server 2016 Datacenter**.

    ![Accéder aux images de machines virtuelles Azure sur le portail](./media/virtual-machines-common-portal-create-fqdn/marketplace-new.png)

3. Dans le centre de données Windows Server 2016 Datacenter, sélectionnez le modèle de déploiement Classic. Cliquez sur Créer.

    ![Capture d’écran affichant les images de machines virtuelles Azure disponibles dans le portail](./media/virtual-machines-common-portal-create-fqdn/deployment-classic-model.png)

## <a name="1-basics-blade"></a>1. Panneau Informations de base

Le panneau Informations de base demande les informations d’administration de la machine virtuelle.

1. Entrez le **Nom** de la machine virtuelle. Dans l’exemple, le nom de la machine virtuelle est _HeroVM_. Le nom doit comporter 1 à 15 caractères et il ne peut pas contenir de caractères spéciaux.

2. Entrez un **Nom d’utilisateur** et un **Mot de passe** fort qui servent à créer un compte local sur la machine virtuelle. Le compte local est utilisé pour se connecter à la machine virtuelle et la gérer. Dans l’exemple, le nom d’utilisateur est _azureuser_.

 Le mot de passe doit compter 8 à 123 caractères et répondre à trois des quatre conditions suivantes : un caractère minuscule, un caractère majuscule, un chiffre et un caractère spécial. En savoir plus sur les [conditions requises pour les noms d’utilisateur et les mots de passe](../articles/virtual-machines/windows/faq.md).

3. **L’Abonnement** est facultatif. Le paramètre le plus courant est « Paiement à l’utilisation ».

4. Sélectionnez un **groupe de ressources** existant ou tapez le nom d’un nouveau groupe. Dans l’exemple, le nom du groupe de ressources est _HeroVMRG_.

5. Sélectionnez **l’Emplacement** du centre de données Azure où vous souhaitez exécuter la machine virtuelle. Dans l’exemple, l’emplacement est **États-Unis de l’Est**.

6. Lorsque vous avez terminé, cliquez sur **Suivant** pour passer au panneau suivant.

    ![Capture d’écran affichant les paramètres dans le panneau Informations de base pour la configuration d’une machine virtuelle Azure](./media/virtual-machines-common-portal-create-fqdn/basics-blade-classic.png)

## <a name="2-size-blade"></a>2. Panneau Taille

Le panneau Taille identifie les informations de configuration de la machine virtuelle et liste les différentes options, notamment le système d’exploitation, le nombre de processeurs, le type de stockage disque et les coûts d’utilisation mensuels estimés.  

Choisissez une taille de machine virtuelle, puis cliquez sur **Sélectionner** pour continuer. Dans l’exemple, la taille de machine virtuelle est _DS1_\__V2 Standard_.

  ![Capture d’écran du panneau Taille affichant les tailles de machine virtuelle Azure que vous pouvez sélectionner](./media/virtual-machines-common-portal-create-fqdn/vm-size-classic.png)


## <a name="3-settings-blade"></a>3. Panneau Paramètres

Le panneau Paramètres demande des options de stockage et de réseau. Vous pouvez accepter les paramètres par défaut. Azure crée les entrées appropriées si nécessaire.

Si vous avez sélectionné une taille de machine virtuelle qui le prend en charge, vous pouvez essayer le Stockage Premium Azure en sélectionnant Premium (SSD) sous Type de disque.

Une fois les modifications terminées, cliquez sur **OK**.

## <a name="4-summary-blade"></a>4. Panneau Résumé

Le panneau Résumé liste les paramètres spécifiés dans les panneaux précédents. Cliquez sur **OK** lorsque vous êtes prêt à créer l’image.

 ![Rapport du panneau Résumé donnant les paramètres spécifiés de la machine virtuelle](./media/virtual-machines-common-portal-create-fqdn/summary-blade-classic.png)

Une fois la machine virtuelle créée, le portail la liste sous **Toutes les ressources**, et affiche une vignette de la machine virtuelle sur le tableau de bord. Le service cloud et le compte de stockage correspondants sont également créés et listés. La machine virtuelle et le service cloud sont démarrés automatiquement et leur statut est répertorié comme **En cours d'exécution**.

 ![Configurer l’agent de machine virtuelle et les points de terminaison de la machine virtuelle](./media/virtual-machines-common-portal-create-fqdn/portal-with-new-vm.png)
