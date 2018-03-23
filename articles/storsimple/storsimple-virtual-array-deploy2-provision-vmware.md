---
title: Approvisionner StorSimple Virtual Array dans VMware | Microsoft Docs
description: Ce deuxième didacticiel de déploiement de StorSimple Virtual Array implique la configuration d'un appareil virtuel dans VMware.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: be737550aa1cff22bc413ee8ce243f8d8588f6a5
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Déploiement de StorSimple Virtual Array - Configuration dans VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel explique comment configurer et vous connecter à StorSimple Virtual Array sur un système hôte exécutant VMware ESXi 5.0, 5.5 ou 6.0. Cet article concerne le déploiement de StorSimple Virtual Array dans le portail Azure, ainsi que dans Microsoft Azure Government Cloud.

Vous aurez besoin de privilèges d’administrateur pour configurer un appareil virtuel et vous y connecter. La configuration initiale peut prendre environ 10 minutes.

## <a name="provisioning-prerequisites"></a>Configuration des composants requis
Vous trouverez ici les conditions requises pour provisionner un appareil virtuel sur un système hôte exécutant VMware ESXi 5.0, 5.5 ou 6.0.

### <a name="for-the-storsimple-device-manager-service"></a>Pour le service StorSimple Device Manager
Avant de commencer, assurez-vous que :

* Vous avez terminé toutes les étapes de la rubrique [Préparation du portail pour StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Vous avez téléchargé l'image de l’appareil virtuel pour Vmware à partir du portail Azure. Pour plus d’informations, consultez l’**Étape 3 : Téléchargement de l’image de l’appareil virtuel** de [Prepare the portal for StorSimple Virtual Array guide](storsimple-virtual-array-deploy1-portal-prep.md) (Guide de préparation du portail pour StorSimple Virtual Array).

### <a name="for-the-storsimple-virtual-device"></a>Pour l’appareil virtuel StorSimple
Avant de déployer un appareil virtuel, assurez-vous que :

* Vous avez accès à un système hôte exécutant Hyper-V (2008 R2 ou version ultérieure) permettant de configurer un appareil.
* Le système hôte est en mesure de dédier les ressources suivantes pour configurer votre appareil virtuel :

  * Un minimum de 4 cœurs.
  * Au moins 8 Go de RAM. Si vous envisagez de configurer le tableau virtuel en tant que serveur de fichiers, 8 Go prennent en charge moins de 2 millions de fichiers. Vous avez besoin de 16 Go de RAM pour prendre en charge 2 à 4 millions de fichiers.
  * Une interface réseau.
  * Un disque virtuel de 500 Go pour les données système.

### <a name="for-the-network-in-datacenter"></a>Pour le réseau dans le centre de données
Avant de commencer, assurez-vous que :

* Vous avez passé en revue la configuration réseau requise pour déployer un appareil virtuel StorSimple et configuré le réseau du centre de données conformément à la configuration requise. 

## <a name="step-by-step-provisioning"></a>Configuration étape par étape
Pour configurer et vous connecter à un appareil virtuel, vous devez effectuer les opérations suivantes :

1. Assurez-vous que le système hôte dispose de suffisamment de ressources pour répondre aux exigences minimales de l’appareil virtuel.
2. Configurez un appareil virtuel dans votre hyperviseur.
3. Démarrez l’appareil virtuel et obtenez l'adresse IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Étape 1 : Vérifier que le système hôte répond aux exigences minimales de l’appareil virtuel
Pour créer un appareil virtuel, vous avez besoin des éléments suivants :

* Un accès à un système hôte exécutant VMware ESXi Server 5.0, 5.5 ou 6.0.
* Un client VMware vSphere sur votre système pour gérer l'hôte ESXi.

  * Un minimum de 4 cœurs.
  * Au moins 8 Go de RAM. Si vous envisagez de configurer le tableau virtuel en tant que serveur de fichiers, 8 Go prennent en charge moins de 2 millions de fichiers. Vous avez besoin de 16 Go de RAM pour prendre en charge 2 à 4 millions de fichiers.
  * Une interface réseau connectée au réseau et capable d’acheminer le trafic vers Internet. La bande passante Internet minimale doit être de 5 Mbits/s pour une utilisation optimale de l'appareil.
  * Un disque virtuel de 500 Go pour les données.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Étape 2 : Configuration d'un appareil virtuel dans l'hyperviseur
Procédez comme suit pour configurer un appareil virtuel dans votre hyperviseur.

1. Copiez l'image de l’appareil virtuel sur votre système. Vous avez téléchargé cette image virtuelle via le portail Azure.

   1. Vérifiez que vous avez téléchargé le dernier fichier d’image. Si vous avez téléchargé l’image précédemment, téléchargez-la à nouveau pour être certain d’avoir l’image la plus récente. La dernière image comprend deux fichiers (au lieu d’un).
   2. Prenez note de l’emplacement où vous avez copié l’image car vous l’utiliserez plus loin dans la procédure.

2. Connectez-vous au serveur ESXi à l’aide du client vSphere. Vous devez disposer de privilèges d’administrateur pour créer une machine virtuelle.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. Dans le client vSphere, dans la section inventaire du volet gauche, sélectionnez le serveur ESXi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Téléchargez le VMDK sur le serveur ESXi. Accédez à l’onglet **Configuration** dans le panneau droit. Sous **Matériel**, sélectionnez **Stockage**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. Dans le panneau droit, sous **Magasins de données**, sélectionnez le magasin de données dans lequel vous voulez charger le fichier VMDK. Le magasin de données doit avoir suffisamment d'espace libre pour les disques du système d'exploitation et des données.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Cliquez avec le bouton droit et sélectionnez **Parcourir les magasins de données**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Une fenêtre **Navigateur du magasin de données** s’ouvre.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Dans la barre d’outils, cliquez sur l’icône ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) pour créer un dossier. Spécifiez et notez le nom du dossier. Vous utiliserez ce nom de dossier plus tard lors de la création d'une machine virtuelle (recommandée). Cliquez sur **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Le nouveau dossier s’affiche dans le panneau gauche du **Navigateur de magasins de données**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Cliquez sur l’icône de chargement ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) et sélectionnez **Charger un fichier**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Recherchez et sélectionnez les fichiers VMDK que vous avez téléchargés. Il existe deux fichiers. Sélectionnez un fichier à télécharger.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Cliquez sur **Ouvrir**. Ceci démarrera le téléchargement du fichier VMDK vers le magasin de données spécifié. Le téléchargement du fichier peut prendre plusieurs minutes.
13. Une fois le téléchargement terminé, le fichier apparaît dans le magasin de données, dans le dossier que vous avez créé.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Vous devez maintenant télécharger le deuxième fichier VMDK dans le même magasin de données.
14. Revenez à la fenêtre du client vSphere. Sélectionnez le serveur ESXi, cliquez avec le bouton droit et choisissez **Nouvelle machine virtuelle**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Une fenêtre **Créer une machine virtuelle** s'affiche. Sur la page **Configuration**, sélectionnez l’option **Personnalisé**. Cliquez sur **Suivant**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Sur la page **Nom et emplacement** , spécifiez le nom de votre machine virtuelle. Ce nom doit correspondre au nom du dossier (recommandé) que vous avez spécifié à l'étape 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Sur la page **Stockage** , sélectionnez le magasin de données que vous souhaitez utiliser pour configurer votre machine virtuelle.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Sur la page **Version de la machine virtuelle**, sélectionnez **Version de la machine virtuelle : 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Sur la page **Système d’exploitation invité**, sélectionnez le **système d’exploitation invité** **Windows**. Pour **Version**, dans la liste déroulante, sélectionnez **Microsoft Windows Server 2012 (64 bits)**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Sur la page **Processeurs**, ajustez le **nombre de sockets virtuels** et le **nombre de cœurs par socket virtuel** afin que le **nombre total de cœurs** soit égal à 4 (ou plus). Cliquez sur **Suivant**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Sur la page **Mémoire** , spécifiez 8 Go (ou plus) de RAM. Cliquez sur **Suivant**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Sur la page **Réseau** , spécifiez le nombre d'interfaces réseau. La configuration minimale nécessite une interface réseau.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Sur la page **Contrôleur SCSI**, acceptez la valeur par défaut **Contrôleur LSI Logic SAS**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Sur la page **Sélectionner un disque**, choisissez **Utiliser un disque virtuel existant**. Cliquez sur **Suivant**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Sur la page **Sélectionner un disque existant** sous **Chemin d’accès du fichier de disque**, cliquez sur **Parcourir**. Cette opération ouvre une boîte de dialogue **Parcourir les magasins de données** . Naviguez jusqu'à l'emplacement où vous avez téléchargé le fichier VMDK. Le magasin de données inclut désormais un seul fichier, car les deux fichiers que vous avez téléchargés au départ ont été fusionnés. Sélectionnez le fichier et cliquez sur **OK**. Cliquez sur **Suivant**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Sur la page **Options avancées**, acceptez la valeur par défaut et cliquez sur **Suivant**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Sur la page **Prêt à finaliser** , passez en revue tous les paramètres associés à la nouvelle machine virtuelle. cochez la case **Modifier les paramètres de la machine virtuelle avant de finaliser**. Cliquez sur **Continuer**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Sur la page **Propriétés des machines virtuelles**, dans l’onglet **Matériel**, localisez le matériel du périphérique. Sélectionnez **Nouveau disque dur**. Cliquez sur **Add**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Une fenêtre **Ajout de matériel** apparaît. Sur la page **Type d’appareil**, sous **Choisir le type d’appareil à ajouter**, sélectionnez **Disque**, puis cliquez sur **Suivant**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Sur la page **Sélectionner un disque**, choisissez **Créer un disque virtuel**. Cliquez sur **Suivant**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Sur la page **Créer un disque**, définissez la **taille du disque** sur 500 Go (ou plus). Bien que 500 Go soit la configuration minimale requise, vous pouvez toujours configurer un disque plus volumineux. Notez que vous ne pouvez pas développer ou réduire le disque une fois mis en service. Pour plus d’informations sur la taille du disque à approvisionner, consultez la section sur le dimensionnement dans le document [Meilleures pratiques](storsimple-ova-best-practices.md). Sous **Configuration du disque**, sélectionnez **Allocation dynamique**. Cliquez sur **Suivant**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Sur la page **Options avancées** , acceptez la valeur par défaut.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Sur la page **Prêt à finaliser** , passez en revue les options de disque. Cliquez sur **Terminer**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Revenez à la page des propriétés de la machine virtuelle. Un nouveau disque dur est ajouté à votre machine virtuelle. Cliquez sur **Terminer**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Sélectionnez votre machine virtuelle dans le volet droit, puis accédez à l’onglet **Résumé** . Passez en revue les paramètres de votre machine virtuelle.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Votre machine virtuelle est désormais configurée. L'étape suivante consiste à mettre cette machine sous tension et à obtenir l'adresse IP.

> [!NOTE]
> Nous vous recommandons de ne pas installer les outils VMware sur votre système Virtual Array (provisionné plus haut). Cette configuration avec les outils VMware installés n’est pas prise en charge.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Étape 3 : Démarrer l’appareil virtuel et obtenir l'adresse IP
Procédez comme suit pour démarrer votre appareil virtuel et vous y connecter.

#### <a name="to-start-the-virtual-device"></a>Pour démarrer l’appareil virtuel
1. Démarrez l’appareil virtuel. Dans le volet gauche du Gestionnaire de Configuration vSphere, sélectionnez votre appareil et cliquez avec le bouton droit pour afficher le menu contextuel. Sélectionnez **Alimentation** puis **Mise sous tension**. Cette opération met votre machine virtuelle sous tension. Vous pouvez afficher l'état de l’opération dans le volet inférieur **Tâches récentes** du client vSphere.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Les étapes de configuration peuvent prendre plusieurs minutes. Une fois l'appareil en cours d'exécution, accédez à l’onglet **Console** . Appuyez sur Ctrl+Alt+Suppr pour vous connecter à l’appareil. Vous pouvez également pointer le curseur sur la fenêtre de la console puis appuyer sur Ctrl+Alt+Inser. L’utilisateur par défaut est *StorSimpleAdmin* et le mot de passe par défaut est *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Pour des raisons de sécurité, le mot de passe d’administrateur réseau expire à la première connexion. Vous êtes invité à modifier le mot de passe.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Entrez un mot de passe contenant au moins 8 caractères. Le mot de passe doit contenir 3 caractères sur 4 en majuscules, minuscules, chiffres et caractères spéciaux. Entrez de nouveau le mot de passe pour le confirmer. Un message vous avertit que le mot de passe a été modifié.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Une fois le mot de passe modifié, l’appareil virtuel peut se réamorcer. Attendez la fin de la tâche. La console Windows PowerShell de l'appareil peut s’afficher avec une barre de progression.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Les étapes 6 à 8 s’appliquent uniquement lors de l’amorçage dans un environnement non DHCP. Si vous êtes dans un environnement DHCP, ignorez ces étapes et passez à l'étape 9. Si vous avez démarré votre appareil dans un environnement non DHCP, vous verrez l’écran suivant.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Ensuite, configurez le réseau.
7. Utilisez la commande `Get-HcsIpAddress` pour répertorier les interfaces réseau activées sur votre appareil virtuel. Si votre appareil possède une seule interface réseau activée, le nom par défaut affecté à cette interface est `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Utilisez l’applet de commande `Set-HcsIpAddress` pour configurer le réseau. Voici un exemple :

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Une fois l'installation initiale terminée et l’appareil démarré, vous verrez le texte de la bannière de l’appareil. Notez l'adresse IP et l'URL affichées dans le texte de la bannière pour gérer l'appareil. Vous utiliserez cette adresse IP pour vous connecter à l'interface utilisateur web de votre appareil virtuel et pour finaliser la configuration et l’inscription locales.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Facultatif) Effectuez cette étape uniquement si vous déployez votre appareil dans Government Cloud. Vous allez maintenant activer le mode FIPS (Federal Information Processing Standard) sur votre appareil. La norme FIPS 140 définit les algorithmes de chiffrement qui sont approuvés pour une utilisation sur les systèmes informatiques du gouvernement fédéral américain dans le but de protéger les données sensibles.

    1. Pour activer le mode FIPS, exécutez l’applet de commande suivante :

        `Enable-HcsFIPSMode`
    2. Après avoir activé le mode FIPS, redémarrez votre appareil pour que les validations de chiffrement prennent effet.

       > [!NOTE]
       > Vous pouvez activer ou désactiver le mode FIPS sur votre appareil. L’alternance entre le mode FIPS et le mode non FIPS n’est pas prise en charge.
       >
       >

Si votre périphérique ne répond pas à la configuration minimale requise, une erreur apparaît dans le texte de bannière (voir ci-dessous). Vous devez modifier la configuration de l'appareil afin qu'il dispose des ressources nécessaires à la configuration minimale. Vous pouvez ensuite redémarrer et vous connecter à l'appareil. Reportez-vous à la configuration minimale requise à l’ [Étape 1 : Vérifier que le système hôte répond aux exigences minimales de l’appareil virtuel](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

SI vous rencontrez une autre erreur durant la configuration initiale effectuée avec l’interface utilisateur web locale, reportez-vous aux workflows suivants :

* Exécutez les tests de diagnostic pour [dépanner la configuration de l’interface utilisateur web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Générez un package de journaux et affichez les fichiers journaux](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Étapes suivantes
* [Configurer StorSimple Virtual Array comme un serveur de fichiers](storsimple-virtual-array-deploy3-fs-setup.md)
* [Configurer StorSimple Virtual Array comme un serveur iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
