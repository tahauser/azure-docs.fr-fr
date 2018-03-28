---
title: Création d’une image de machine virtuelle pour Azure Marketplace | Microsoft Docs
description: Instructions détaillées sur la façon de créer une image de machine virtuelle pour Azure Marketplace et en permettre la commercialisation auprès des autres utilisateurs.
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: mbaldwin
ms.openlocfilehash: f7b19066ca3fa156456766ff82afeadadc6b1efa
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>Guide à la création d’une image de machine virtuelle pour Azure Marketplace
Cet article, **étape 2**, vous guide dans la préparation des disques durs virtuels (VHD) que vous allez déployer dans Azure Marketplace. Vos disques durs virtuels constituent le fondement de votre référence SKU. Le processus varie selon que vous fournissez une référence SKU Linux ou Windows. Cet article aborde ces deux scénarios. Ce processus peut être exécuté parallèlement à la procédure de [création de compte et d’enregistrement][link-acct-creation].

## <a name="1-define-offers-and-skus"></a>1. Définir les offres et les références SKU
Dans cette section, vous apprenez à définir les offres et leurs références SKU associées.

Une offre constitue le « parent » des références SKU associées. Vous pouvez proposer plusieurs offres. Il vous revient de définir les modalités de structuration de vos offres. Lorsqu’une offre est déployée dans un environnement intermédiaire, les références SKU associées le sont également. Choisissez avec soin vos identificateurs SKU, car ceux-ci apparaissent dans l’URL.

* Azure.com : http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}
* Portail Azure en préversion : https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}  

Une référence SKU désigne le nom commercial d’une image de machine virtuelle. Une image de machine virtuelle contient un disque de système d’exploitation et aucun ou plusieurs disques de données. Il s’agit en fait du profil de stockage complet d’une machine virtuelle. Chaque disque a un disque dur virtuel est nécessaire par disque. Même les disques de données vides ont besoin de la création d’un disque dur virtuel.

Quel que soit le système d’exploitation que vous utilisez, ajoutez uniquement le nombre minimal de disques de données requis par la référence SKU. Les clients ne suppriment pas les disques qui font partie d’une image lors du déploiement, mais peuvent toujours ajouter des disques pendant ou après le déploiement le cas échéant.

> [!IMPORTANT]
> **Ne modifiez pas le nombre de disques dans une nouvelle version de l’image.** Si vous devez reconfigurer les disques de données dans l’image, définissez une nouvelle référence SKU. La publication d’une nouvelle version de l’image avec un nombre de disques différent peut potentiellement rompre le nouveau déploiement basé sur la nouvelle version de l’image en cas de mise à l’échelle automatique, de déploiements automatiques de solutions via des modèles ARM et autres scénarios.
>
>

### <a name="11-add-an-offer"></a>1.1 Ajouter une offre
1. Connectez-vous au [Portail de publication][link-pubportal] à l’aide de votre compte de vendeur.
2. Accédez à l’onglet **Machines virtuelles** du Portail de publication. Dans le champ de saisie qui s’affiche, entrez le nom de votre offre. Le nom de l’offre correspond généralement au nom du produit ou service que vous prévoyez de vendre dans Azure Marketplace.
3. Sélectionnez **Créer**.

### <a name="12-define-a-sku"></a>1.2 Définir une référence SKU
Une fois que vous avez ajouté une offre, vous devez définir/identifier vos références SKU. Vous pouvez proposer plusieurs offres, chaque offre étant elle-même associée à plusieurs références SKU. Lorsqu’une offre est déployée dans un environnement intermédiaire, les références SKU associées le sont également.

1. **Ajoutez une référence SKU.** La référence SKU requiert un identificateur qui est utilisé dans l’URL. L’identificateur doit être unique dans votre profil de publication. Il n’y a pas de risque de conflit avec les identificateurs d’autres éditeurs.

   > [!NOTE]
   > Les identificateurs de l’offre et de la référence SKU s’afficheront dans l’URL de l’offre sur Marketplace.
   >
   >
2. **Ajoutez une description de votre référence SKU.** Des descriptions résumées sont visibles pour les clients, donc, vous devez les rendre facilement lisibles. Ces informations ne doivent pas être verrouillées jusqu’à la phase « Envoi vers staging »/ intermédiaire.
3. Si vous utilisez des références SKU basées sur Windows, suivez les liens suggérés pour acquérir les versions approuvées de Windows Server.

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2. Créer un disque dur virtuel compatible avec Azure (Linux)
Cette section se concerne sur les meilleures pratiques pour la création d’une image de machine virtuelle Linux pour Azure Marketplace. Pour une procédure pas à pas, consultez la documentation suivante : [Créer un disque dur virtuel compatible avec Azure (Linux)](../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>3. Créer un disque dur virtuel compatible avec Azure (Windows)
Cette section décrit la procédure de création d’une référence SKU basée sur Windows Server pour Azure Marketplace.

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1 Vérifier que vous utilisez les disques durs virtuels de base corrects
Le disque dur virtuel de système d’exploitation pour votre image de machine virtuelle doit être basé sur une image de base approuvée par Azure contenant Windows Server ou SQL Server.

Pour commencer, créez une machine virtuelle à partir d’une des images suivantes, situées dans le [portail Microsoft Azure][link-azure-portal] :

* Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
* SQL Server 2014 ([Enterprise][link-sql-2014-ent], [Standard][link-sql-2014-std], [Web][link-sql-2014-web])
* SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent], [Standard][link-sql-2012-std], [Web][link-sql-2012-web])

Ces liens sont également disponibles dans le Portail de publication sous la page des références SKU.

> [!TIP]
> Si vous utilisez le portail Azure actuel ou PowerShell, les images Windows Server publiées à compter du 8 septembre 2014 sont approuvées.
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2 Créer votre machine virtuelle Windows
À partir du Portail Microsoft Azure, vous pouvez créer votre machine virtuelle sur la base d’une image de base approuvée en suivant quelques étapes simples. Voici une vue d’ensemble du processus.

1. À partir de la page des images de base, sélectionnez **Créer une machine virtuelle** pour accéder au nouveau [Portail Microsoft Azure][link-azure-portal].

    ![drawing][img-acom-1]
2. Connectez-vous au portail en utilisant le compte Microsoft et mot de passe de l’abonnement Azure que vous voulez utiliser.
3. Suivez les invites pour créer une machine virtuelle à l’aide de l’image de base que vous avez sélectionnée. Vous devez fournir un nom d’hôte (nom de l’ordinateur), un nom d’utilisateur (administrateur inscrit) et un mot de passe correspondant à la machine virtuelle.

    ![drawing][img-portal-vm-create]
4. Sélectionnez la taille de la machine virtuelle à déployer :

    a.    Si vous envisagez de développer le disque dur virtuel localement, la taille n’a pas d’importance. Vous pouvez utiliser une des machines virtuelles plus petites.

    b.    Si vous envisagez de développer l’image dans Azure, vous pouvez utiliser les tailles de machine virtuelle recommandées pour l’image sélectionnée.

    c.    Pour plus d’informations sur la tarification, consultez le sélecteur de **niveaux tarifaires recommandés** inclus dans le portail. Celui-ci indique les trois tailles recommandées fournies par l’éditeur (dans ce cas, l’éditeur est Microsoft).

    ![drawing][img-portal-vm-size]
5. Définissez les propriétés :

    a.    Pour accélérer le déploiement, vous pouvez conserver les valeurs par défaut des propriétés qui figurent sous **Configuration facultative** et **Groupe de ressources**.

    b.    Sous **Compte de stockage**, vous pouvez éventuellement sélectionner le compte de stockage du disque dur virtuel.

    c.    Sous **Groupe de ressources**, vous pouvez éventuellement sélectionner le groupe logique dans lequel placer la machine virtuelle.
6. Sélectionnez l’ **emplacement** de déploiement :

    a.    Si vous envisagez de développer le disque dur virtuel localement, l’emplacement n’a pas d’importance, car vous téléchargerez l’image sur Azure ultérieurement.

    b.    Si vous envisagez de développer l’image dans Azure, vous pouvez utiliser dès le départ une des régions Microsoft Azure basées aux États-Unis. Ceci permet d’accélérer le processus de copie du disque dur virtuel effectué automatiquement par Microsoft lorsque vous soumettez votre image à des fins de certification.

    ![drawing][img-portal-vm-location]
7. Cliquez sur **Créer**. La machine virtuelle commence à se déployer. L’opération ne dure que quelques minutes. Vous pouvez ensuite commencer à créer l’image pour votre référence SKU.

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3 Développer votre disque dur virtuel dans le cloud
Il est vivement recommandé de développer votre disque dur virtuel dans le cloud à l’aide du protocole RDP (Remote Desktop Protocol). Vous pouvez vous connecter au bureau à distance à l’aide des nom d’utilisateur et mot de passe spécifiés pendant l’approvisionnement.

> [!IMPORTANT]
> **N’utilisez pas de disques gérés.** La machine virtuelle utilisée pour développer le disque dur virtuel vers le cloud ne doit pas être basée sur des disques gérés, car actuellement elle ne prend pas en charge la création d’une image à partir de ces disques.
> La création de la machine virtuelle dans la fonctionnalité facultative change les paramètres par défaut pour les disques gérés.

> Si vous développez votre disque dur virtuel sur site (ce qui n’est pas recommandé), consultez la page [Création d’une image de machine virtuelle sur site](marketplace-publishing-vm-image-creation-on-premise.md). Il est inutile de télécharger votre disque dur virtuel si vous développez dans le cloud.
>
>

**Se connecter via RDP à l’aide du [Portail Microsoft Azure][link-azure-portal]**

1. Sélectionnez **Tous les services** > **Machines virtuelles**.
2. Le panneau Machines virtuelles s’ouvre. Assurez-vous que la machine virtuelle à laquelle vous voulez vous connecter est en cours d’exécution, puis sélectionnez-la dans la liste des machines virtuelles déployées.
3. Un panneau s’ouvre, qui décrit la machine virtuelle sélectionnée. Dans la partie supérieure, cliquez sur **Se connecter**.
4. Vous êtes invité à entrer les nom d’utilisateur et mot de passe que vous avez spécifiés lors du déploiement.

**Se connecter via RDP à l’aide de PowerShell**

Pour télécharger un fichier de bureau à distance sur une machine locale, utilisez l’[applet de commande Get-AzureRemoteDesktopFile][link-technet-2]. Pour utiliser cette applet de commande, vous devez connaître les noms du service et de la machine virtuelle. Si vous avez créé la machine virtuelle à partir du [Portail Microsoft Azure][link-azure-portal], ces informations sont disponibles sous Propriétés de la machine virtuelle :

1. Dans le Portail Microsoft Azure, sélectionnez **Tous les services** > **Machines virtuelles**.
2. Le panneau Machines virtuelles s’ouvre. Sélectionnez la machine virtuelle que vous avez déployée.
3. Un panneau s’ouvre, qui décrit la machine virtuelle sélectionnée.
4. Cliquez sur **Propriétés**.
5. La première portion du nom de domaine est le nom du service. Le nom d’hôte est celui de la machine virtuelle.

    ![drawing][img-portal-vm-rdp]
6. L’applet de commande qui permet de télécharger le fichier RDP pour la machine virtuelle créée sur le Bureau local de l’administrateur se présente comme suit.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Des informations supplémentaires sont disponibles sur RDP dans l’article [Se connecter à une machine virtuelle Azure avec RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)sur MSDN.

**Configurer une machine virtuelle et créer votre référence SKU**

Une fois le disque dur virtuel de système d’exploitation téléchargé, utilisez Hyper-V et configurez une machine virtuelle pour commencer à créer votre référence SKU. L’article TechNet [Installer le rôle Hyper-V et configurer un ordinateur virtuel](http://technet.microsoft.com/library/hh846766.aspx)décrit la procédure détaillée.

### <a name="34-choose-the-correct-vhd-size"></a>3.4 Choisir la taille du disque dur virtuel
Le disque dur virtuel de système d’exploitation Windows de votre image de machine virtuelle doit être créé en tant que disque dur virtuel au format fixe de 128 Go.  

Si la taille physique est inférieure à 128 Go, le disque dur virtuel doit être fragmenté. Les images Windows et SQL Server de base fournies satisfont déjà les exigences requises alors, ne modifiez pas le format ou la taille du disque dur virtuel obtenu.  

Les disques de données peuvent avoir une taille maximale de 1 To. Lorsque vous choisissez la taille du disque, rappelez-vous que les clients ne peuvent pas redimensionner les disques durs virtuels dans une image lors du déploiement. Ce type de disque dur virtuel doit être créé comme disque dur virtuel au format fixe. Ils doivent également être fragmentés. Les disques de données peuvent être vides ou contenir des données.

### <a name="35-install-the-latest-windows-patches"></a>3.5 Installer les derniers correctifs Windows
Les images de base contiennent les derniers correctifs jusqu’à leur date de publication. Avant de publier le disque dur virtuel de système d’exploitation que vous avez créé, vérifiez que Windows Update a été exécuté et que les dernières mises à jour de sécurité critiques et importantes ont été installées.

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3.6 Effectuer les autres tâches de configuration et de planification utiles
Si d’autres étapes de configuration sont requises, vous pouvez utiliser une tâche planifiée exécutée au démarrage pour apporter des modifications finales à la machine virtuelle après que celle-ci a été déployée :

* Il est recommandé de supprimer automatiquement la tâche suite à une exécution réussie.
* Aucune configuration ne doit utiliser de lecteurs autres que C ou D, car ceux-ci sont les deux seuls qui lecteurs qui existent à coup sûr. Le lecteur C est le disque du système d’exploitation et le lecteur D est le disque local temporaire.

### <a name="37-generalize-the-image"></a>3.7 Généraliser l’image
Toutes les images dans Azure Marketplace doivent être réutilisables de façon générale. En d’autres termes, le disque dur virtuel de système d’exploitation doit être généralisé :

* Pour Windows, l’image doit être préparée avec « sysprepped » et les opérations de configuration effectuées doivent toutes prendre en charge la commande **sysprep** .
* Vous pouvez exécuter la commande qui suit à partir du répertoire % windir%\System32\Sysprep.

        sysprep.exe /generalize /oobe /shutdown

  Des instructions sur la préparation du système d’exploitation avec Sysprep sont fournies à l’étape de l’article MSDN intitulée [Création et téléchargement d’un disque dur virtuel Windows Server dans Azure](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="4-deploy-a-vm-from-your-vhds"></a>4. Déployer une machine virtuelle à partir de vos disques durs virtuels
Une fois les disques durs virtuels (disque dur virtuel de système d’exploitation généralisé, et zéro ou plusieurs disques durs virtuels de disque de données) téléchargés sur un compte de stockage Azure, vous pouvez les inscrire en tant qu’image de machine virtuelle d’utilisateur. Vous pouvez tester cette image. Veuillez noter que, comme votre disque dur virtuel de système d’exploitation est généralisé, vous ne pouvez pas déployer directement la machine virtuelle en fournissant l’URL du disque dur virtuel.

Pour en savoir plus sur les images de machine virtuelle, consultez les articles de blog suivants :

* [VM Image](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [VM Image PowerShell How To](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [À propos d’images de machines virtuelles dans Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-cli"></a>Configurer les outils nécessaires, PowerShell et l’interface de ligne de commande Azure
* [Guide pratique pour configurer PowerShell](/powershell/azure/overview)
* [Guide pratique pour configurer l’interface de ligne de commande Azure](../cli-install-nodejs.md)

### <a name="41-create-a-user-vm-image"></a>4.1 Créer une image de machine virtuelle d’utilisateur
#### <a name="capture-vm"></a>Capturer la machine virtuelle
Consultez les liens ci-dessous pour obtenir des conseils sur la capture de la machine virtuelle à l’aide de l’API, de PowerShell ou de l’interface de ligne de commande Azure.

* [API](https://msdn.microsoft.com/library/mt163560.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [interface de ligne de commande Azure](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>Généraliser une image
Consultez les liens ci-dessous pour obtenir des conseils sur la capture de la machine virtuelle à l’aide de l’API, de PowerShell ou de l’interface de ligne de commande Azure.

* [API](https://msdn.microsoft.com/library/mt269439.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [interface de ligne de commande Azure](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 Déployer une machine virtuelle à partir d’une image de machine virtuelle d’utilisateur
Pour déployer une machine virtuelle à partir d’une image de machine virtuelle d’utilisateur, vous pouvez utiliser le [Portail Azure](https://manage.windowsazure.com) ou PowerShell actuel.

**Déployer une machine virtuelle à partir du portail Azure actuel**

1. Accédez à **Nouveau** > **Calcul** > **Machine virtuelle** > **De la galerie**.

2. Accédez à **Mes images**, puis sélectionnez l’image de machine virtuelle à partir de laquelle déployer une machine virtuelle :

   1. Sélectionnez celle-ci avec soin, car l’affichage **Mes images** répertorie les images de système d’exploitation et les images de machine virtuelle.
   2. L’examen du nombre de disques peut vous aider à déterminer le type d’image que vous déployez, car la majorité des images de machine virtuelle inclut plusieurs disques. Il est toutefois toujours possible d’avoir une image de machine virtuelle avec un seul disque de système d’exploitation dont le **nombre de disques** est défini sur 1.

      ![drawing][img-manage-vm-select]
3. Suivez les étapes de l’Assistant de création de machine virtuelle en spécifiant le nom de la machine virtuelle, sa taille, son emplacement, ainsi que les nom d’utilisateur et mot de passe.

**Déployer une machine virtuelle à partir de vos disques durs virtuels**

Pour déployer une grande machine virtuelle, vous pouvez utiliser les applets de commande suivantes à partir de l’image de machine virtuelle que vous venez de créer.

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> Pour obtenir une assistance supplémentaire, consultez [Troubleshooting common issues encountered during VHD creation] \(Résolution des problèmes courants rencontrés durant la création du disque dur virtuel).
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5. Obtenir une certification pour votre image de machine virtuelle
L’étape suivante de la préparation de votre image de machine virtuelle pour Azure Marketplace consiste à certifier celle-ci.

Dans le cadre de ce processus, vous allez exécuter un outil de certification spécial, télécharger les résultats de la vérification dans le conteneur Azure dans lequel se trouvent vos disques durs virtuels, ajouter une offre, définir votre référence SKU et soumettre votre image de machine virtuelle pour certification.

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1 Téléchargez exécutez l’outil « Certification Test Tool for Azure Certified »
L’outil de certification s’exécute sur une machine virtuelle, configurée à partir de votre image de machine virtuelle d’utilisateur, afin de garantir que l’image de machine virtuelle est compatible avec Microsoft Azure. Celui-ci vérifie que les recommandations et exigences relatives à la préparation de votre disque dur virtuel sont respectées. L’outil génère un rapport de compatibilité, qui doit être téléchargé sur le portail de publication lors de la demande de certification.

L’outil de certification peut être utilisé avec les machines virtuelles Windows et Linux. Il se connecte aux machines virtuelles Windows via PowerShell, et se connecte aux machines virtuelles Linux via SSH.Net :

1. Commencez par télécharger l’outil de certification sur le [site de téléchargement Microsoft][link-msft-download].
2. Ouvrez l’outil de certification, puis cliquez sur le bouton **Start New Test** (Commencer un nouveau test).
3. Dans l’écran **Test Information** (Informations sur le test), entrez un nom pour la série de tests.
4. Indiquez si votre machine virtuelle est exécutée sous Linux ou Windows. Selon la valeur choisie, sélectionnez les options suivantes.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**Connexion de l’outil de certification Tool à une image de machine virtuelle Linux**
1. Sélectionnez le mode d’authentification SSH : mot de passe ou fichier de clé.
2. Si vous utilisez l’authentification basée sur le mot de passe, entrez le nom du système de nom de domaine (DNS), le nom d’utilisateur et le mot de passe.
3. Si vous utilisez l’authentification par fichier de clé, saisissez le nom DNS, le nom d’utilisateur et l’emplacement de la clé privée.

   ![Authentification de l’image de machine virtuelle Linux par mot de passe][img-cert-vm-pswd-lnx]

   ![Authentification de l’image de machine virtuelle Linux par fichier de clé][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Connecter l’outil de certification à une image de machine virtuelle sous Windows**
1. Saisissez le nom de domaine (DNS) complet de la machine virtuelle (par exemple, MyVMName.Cloudapp.net).
2. Saisissez le nom d’utilisateur et le mot de passe.

   ![Authentification de l’image de machine virtuelle Windows par mot de passe][img-cert-vm-pswd-win]

Après avoir sélectionné les options correctes pour votre image de machine virtuelle Linux ou Windows, cliquez sur **Test Connection** (Tester la connexion) pour vérifier que SSH.Net ou PowerShell a une connexion valide pour les tests. Une fois la connexion établie, sélectionnez **Suivant** pour démarrer le test.

Une fois le test terminé, vous recevrez les résultats (Succès/Échec/Avertissement) pour chaque élément du test.

![Scénarios de test pour une image de machine virtuelle Linux][img-cert-vm-test-lnx]

![Scénarios de test pour une image de machine virtuelle Windows][img-cert-vm-test-win]

Si l’un des tests échoue, votre image ne sera pas certifiée. Dans de cas, passez en revue la configuration requise et apportez éventuellement les modifications nécessaires.

Après le test automatisé, vous devez fournir des informations supplémentaires sur votre image de machine virtuelle via un écran de questionnaire.  Répondez aux questions, puis sélectionnez **Suivant**.

![Questionnaire de l’outil de certification][img-cert-vm-questionnaire]

![Questionnaire de l’outil de certification][img-cert-vm-questionnaire-2]

Après avoir répondu au questionnaire, vous pouvez fournir des informations supplémentaires, telles que les informations d’accès SSH pour l’image de machine virtuelle Linux, et expliquer les raisons de n’importe quel échec d’évaluation. Vous pouvez télécharger les résultats des tests et les fichiers journaux pour les scénarios de test exécutés, ainsi que vos réponses au questionnaire. Enregistrez les résultats dans le même conteneur que vos disques durs virtuels.

![Enregistrer les résultats du test de certification][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 Obtenir l’URI de signature d’accès partagé pour vos images de machine virtuelle
Dans le cadre du processus de publication, vous devrez spécifier les URI (identificateur de ressource uniforme) qui permettent d’accéder aux disques durs virtuels que vous avez créés pour votre référence SKU. Microsoft doit accéder à ces disques durs virtuels pendant le processus de certification. Par conséquent, vous devez créer un URI de signature d’accès partagé pour chaque disque dur virtuel. Cet URI doit être saisi dans l’onglet **Images** du Portail de publication.

L’URI de signature d’accès partagé créé doit respecter les exigences suivantes :

Remarque : les instructions suivantes s’appliquent uniquement aux disques non gérés qui sont le seul type pris en charge.

* Lors de la génération des URI de signature d’accès partagé pour vos disques durs virtuels, les autorisations de liste et de lecture seule sont suffisantes. Ne fournissez pas d’accès en écriture ou en suppression.
* La durée d’accès doit être d’au minimum trois (3) semaines ouvrées à compter de la date de création de l’URI de signature d’accès partagé.
* Afin de préserver l’heure UTC, sélectionnez le jour précédant la date actuelle. Par exemple, si la date actuelle est le 6 octobre 2014, sélectionnez 5/10/2014.

L’URL SAS peut être générée de plusieurs manières pour partager votre disque dur virtuel sur Azure Marketplace.
Voici les 3 outils recommandés :

1.  Explorateur de stockage Azure
2.  Explorateur de stockage Microsoft
3.  Azure CLI

**Azure Storage Explorer (recommandé pour les utilisateurs Windows)**

Voici les étapes pour générer des URL SAS à l’aide d’Azure Storage Explorer

1. Téléchargez [Azure Storage Explorer 6 Preview 3](https://azurestorageexplorer.codeplex.com/) à partir de CodePlex. Accédez à [Azure Storage Explorer 6 Preview](https://azurestorageexplorer.codeplex.com/), puis cliquez sur **« Téléchargements »**.

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. Téléchargez [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) et installez après avoir décompressé l’archive.

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. Après l’installation, ouvrez l’application.
4. Cliquez sur **Ajouter un compte**.

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. Spécifiez le nom du compte de stockage, la clé du compte de stockage et le domaine des points de terminaison de stockage. Il s’agit du compte de stockage dans votre abonnement Azure pour lequel vous avez conservé votre disque dur virtuel sur le portail Azure.

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. Une fois qu’Azure Storage Explorer est connecté à votre compte de stockage spécifique, il démarrera pour afficher tout le contenu du compte de stockage. Sélectionnez le conteneur dans lequel vous avez copié le fichier de disque dur virtuel du système d’exploitation (et les disques de données, s’ils s’appliquent à votre scénario).

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. Après avoir sélectionné le conteneur d’objets Blobs, Azure Storage Explorer démarrera en affichant les fichiers associés au conteneur. Sélectionnez le fichier image (.vhd) qui doit être soumis.

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.  Après avoir sélectionné le fichier .vhd dans le conteneur, cliquez sur l’onglet **Security** (Sécurité).

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.  Dans la boîte de dialogue **Blob Container Security** (Sécurité du conteneur d’objets blob), conservez les valeurs par défaut de l’onglet **Access Level** (Niveau d’accès), puis cliquez sur l’onglet **Shared Access Signatures** (Signatures d’accès partagé).

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10. Suivez les étapes ci-dessous pour générer une signature d’accès partagé URI de l’image .vhd :

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a. **Accès autorisé à partir de** : afin de préserver l’heure UTC, sélectionnez le jour précédant la date actuelle. Par exemple, si la date actuelle est le 6 octobre 2014, sélectionnez 5/10/2014.

    b. **Access permitted to** (Accès autorisé jusqu’à) : sélectionnez une date située au moins 3 semaines après la date **Access permitted from** (Accès autorisé à partir de).

    c. **Actions permitted** (Actions autorisées) : sélectionnez les autorisations **List** (Lister) et **Read** (Lire).

    d. Si vous avez correctement sélectionné votre fichier .vhd, ce dernier apparaît dans **Blob name to access** (Nom d’objet Blob auquel accéder) avec l’extension .vhd.

    e. Cliquez sur **Generate Signature**(Générer la signature).

    f. Dans l’ **URI de signature d’accès partagé ainsi généré de ce conteneur**, vérifiez les points suivants comme indiqué ci-dessus :

       - Assurez-vous que votre nom de fichier d’image et le **« .vhd »** sont dans l’URI.
       - À la fin de la signature, assurez-vous que **« =rl »** s’affiche. Cela montre qu’un accès en lecture seule et de liste a été fourni avec succès.
       - Au milieu de la signature, assurez-vous que **« sr=c »** s’affiche. Cela montre que vous avez l’accès de niveau conteneur

11. Pour vous assurer que l’URI de signature d’accès partagé fonctionne, cliquez sur **Test in Browser**(Test dans le navigateur). Le téléchargement doit normalement démarrer.

12. Copiez l’URI de signature d’accès partagé. Cet URI doit être copié dans le Portail de publication.

13. Répétez les étapes 6 à 10 pour chaque disque dur virtuel de la référence SKU.

**Explorateur de stockage Microsoft Azure (Windows/MAC/Linux)**

Voici les étapes pour générer des URL SAS à l’aide de Microsoft Azure Storage Explorer

1.  Téléchargez l’Explorateur Stockage Microsoft Azure à partir du site web [http://storageexplorer.com/](http://storageexplorer.com/). Accédez à [Microsoft Azure Storage Explorer](http://storageexplorer.com/releasenotes.html) et cliquez sur **Download for Windows**.

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.  Après l’installation, ouvrez l’application.

3.  Cliquez sur **Ajouter un compte**.

4.  Configurez Microsoft Azure Storage Explorer pour votre abonnement en vous connectant à votre compte

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.  Accédez au compte de stockage et sélectionnez le conteneur

6.  Sélectionnez **« Obtenir la signature de partage d’accès... »** en cliquant avec le bouton droit de la souris sur le **conteneur**

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.  Date de début, date d’expiration et autorisations de la mise à jour comme suit

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.  **Start Time** (Date de début) : Afin de préserver l’heure UTC, sélectionnez le jour précédant la date actuelle. Par exemple, si la date actuelle est le 6 octobre 2014, sélectionnez 5/10/2014.

    b.  **Date d’expiration :** sélectionnez une date au moins 3 semaines après la **Date de début**.

    c.  **Permissions** (Autorisations) : sélectionnez les autorisations **List** (Lister) et **Read** (Lire)

8.  Copiez l’URI de signature d’accès partagé du conteneur

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    L’URL SAS générée représente le niveau de conteneur, et nous devons maintenant y ajouter le nom du disque dur virtuel.

    Format d’URL SAS de niveau de conteneur :`https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Insérez le nom du disque dur virtuel après le nom du conteneur dans l’URL SAS comme indiqué ci-dessous`https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Exemple :

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd est le nom du disque dur virtuel, alors l’URL SAS du disque dur virtuel sera `https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - Assurez-vous que votre nom de fichier d’image et le **« .vhd »** sont dans l’URI.
    - Au milieu de la signature, assurez-vous que **« sp=rl »** s’affiche. Cela montre qu’un accès en lecture seule et de liste a été fourni avec succès.
    - Au milieu de la signature, assurez-vous que **« sr=c »** s’affiche. Cela montre que vous avez l’accès de niveau conteneur

9.  Pour vous assurer que l’URI de signature d’accès partagé fonctionne, testez-la dans le navigateur. Le téléchargement doit normalement démarrer

10. Copiez l’URI de signature d’accès partagé. Cet URI doit être copié dans le Portail de publication.

11. Répétez ces étapes pour chaque disque dur virtuel de la référence SKU.

**Interface de ligne de commande Azure (méthode recommandée pour l’intégration continue/non Windows)**

Voici les étapes pour générer des URL SAS à l’aide de l’interface de ligne de commande Azure

1.  Téléchargez l’interface de ligne de commande Microsoft Azure [ici](https://azure.microsoft.com/en-in/documentation/articles/xplat-cli-install/). Vous y trouverez également les différents liens pour **[Windows](http://aka.ms/webpi-azure-cli)** et  **[Mac OS](http://aka.ms/mac-azure-cli)**.

2.  Après le téléchargement, veuillez effectuer l’installation

3.  Créez un fichier PowerShell (ou tout autre script exécutable) avec le code suivant et enregistrez-le en local

          $conn="DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<Storage Account Key>"
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl <Permission End Date> -c $conn --start <Permission Start Date>  

    Mettez à jour les paramètres suivants

    a. **`<StorageAccountName>`** : indiquez le nom de votre compte de stockage

    b. **`<Storage Account Key>`** : indiquez la clé de votre compte de stockage

    c. **`<Permission Start Date>`** : afin de préserver l’heure UTC, sélectionnez le jour précédant la date actuelle. Par exemple, si la date actuelle est le 26 octobre 2016, la valeur doit être 25/10/2016. Si vous utilisez Azure CLI 2.0 (commande az), indiquez la date et l’heure dans les champs Date de début et Date de fin, par exemple : 10-25-2016T00:00:00Z.

    d. **`<Permission End Date>`** : sélectionnez une date au moins 3 semaines après la **Date de début**. La valeur doit être **02/11/2016**. Si vous utilisez Azure CLI 2.0 (commande az), indiquez la date et l’heure dans les champs Date de début et Date de fin, par exemple : 11-02-2016T00:00:00Z.

    Voici l’exemple de code après la mise à jour des bons paramètres

          $conn="DefaultEndpointsProtocol=https;AccountName=st20151;AccountKey=TIQE5QWMKHpT5q2VnF1bb+NUV7NVMY2xmzVx1rdgIVsw7h0pcI5nMM6+DVFO65i4bQevx21dmrflA91r0Vh2Yw=="
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl 11/02/2016 -c $conn --start 10/25/2016  

4.  Ouvrez l’éditeur PowerShell avec le mode « Exécuter en tant qu’administrateur » et ouvrez le fichier de l’étape 3. Vous pouvez utiliser tout éditeur de script disponible dans votre système d’exploitation.

5.  Exécutez le script, il vous fournira l’URL SAS pour l’accès au niveau conteneur

    La sortie de la signature SAS suivra. Copiez la partie en surbrillance dans un bloc-notes

    ![drawing](media/marketplace-publishing-vm-image-creation/img5.2_16.png)

6.  Maintenant, vous obtenez l’URL SAS au niveau conteneur et vous devez lui ajouter le nom du disque dur virtuel.

    URL SAS de niveau de conteneur

    `https://st20151.blob.core.windows.net/vhds?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

7.  Insérez le nom du disque dur virtuel après le nom du conteneur dans l’URL SAS comme indiqué ci-dessous`https://st20151.blob.core.windows.net/vhds/<VHDName>?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    Exemple :

    TestRGVM201631920152.vhd est le nom du disque dur virtuel, alors l’URL SAS du disque dur virtuel sera

    `https://st20151.blob.core.windows.net/vhds/ TestRGVM201631920152.vhd?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    - Assurez-vous que votre nom de fichier d’image et le « .vhd » sont dans l’URI.
    -   Au milieu de la signature, assurez-vous que « sp=rl » s’affiche. Cela montre qu’un accès en lecture seule et de liste a été fourni avec succès.
    -   Au milieu de la signature, assurez-vous que « sr=c » s’affiche. Cela montre que vous avez l’accès de niveau conteneur

8.  Pour vous assurer que l’URI de signature d’accès partagé fonctionne, testez-la dans le navigateur. Le téléchargement doit normalement démarrer

9.  Copiez l’URI de signature d’accès partagé. Cet URI doit être copié dans le Portail de publication.

10. Répétez ces étapes pour chaque disque dur virtuel de la référence SKU.


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3 Fournir des informations sur l’image de machine virtuelle et demander une certification dans le Portail de publication
Une fois que vous avez créé votre offre et votre référence SKU, vous devez renseigner les détails de l’image associés à la référence en question.

1. Accédez au [Portail de publication][link-pubportal] et connectez-vous à votre compte de vendeur.
2. Sélectionnez l’onglet **VM Images** (Images de machine virtuelle)
3. L’identificateur figurant en haut de la page est en fait l’identificateur de l’offre et non l’identificateur de la référence SKU.
4. Renseignez les propriétés sous la section **SKU** (Références SKU).
5. Sous **Famille de système d’exploitation**, sélectionnez le type de système d’exploitation associé au disque dur virtuel du système d’exploitation.
6. Sous **Système d’exploitation**, décrivez le système d’exploitation. Envisagez un format du type Famille de systèmes d’exploitation, Type, Version et Mises à jour, Par exemple, « Windows Server Datacenter 2014 R2 ».
7. Sélectionnez jusqu’à six tailles de machine virtuelle recommandées. Ces recommandations sont présentées à l’utilisateur dans le panneau Niveau tarifaire du portail de gestion Azure s’il décide d’acheter et de déployer votre image. **Il s’agit uniquement de recommandations. Le client peut sélectionner n’importe quelle taille de machine virtuelle prenant en charge les disques spécifiés dans votre image.**
8. Indiquez la version. Le champ version encapsule une version sémantique pour identifier le produit et ses mises à jour :
   * Les versions doivent être au format X.Y.Z, où X, Y et Z sont des entiers.
   * Les images contenues dans des références SKU différentes peuvent avoir différentes versions majeures et mineures.
   * Les versions au sein d’une même référence SKU ne doivent impliquer que des modifications incrémentielles, qui augmentent la version du correctif (Z de X.Y.Z).
9. Dans la zone **URL de disque dur virtuel de système d’exploitation** , saisissez la signature d’accès partagé créée pour le disque dur virtuel du système d’exploitation.
10. Si des disques de données sont associés à cette référence SKU, sélectionnez le numéro d’unité logique que vous voulez utiliser pour le montage de ce disque de données lors du déploiement.
11. Dans la zone **URL de disque dur virtuel LUN X** entrez la signature d’accès partagé créée pour les données du premier disque dur virtuel.

    ![drawing](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>Problèmes communs avec les URL SAS et résolution

|Problème|Message d’échec|Correctif|Lien vers la documentation|
|---|---|---|---|
|Échec lors de la copie d’images : « ? » est introuvable dans l’URL SAS|Échec : copie d’images. Impossible de télécharger l’objet blob avec l’URI SAS fournie.|Mettez à jour l’URL SAS avec les outils recommandés|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Échec lors de la copie d’images : les paramètres « st » et « se » ne sont pas présents dans l’URL SAS|Échec : copie d’images. Impossible de télécharger l’objet blob avec l’URI SAS fournie.|Mettez à jour l’URL SAS en ajoutant les dates de début et de fin|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Échec lors de la copie d’images : « sp=rl » n’est pas présent dans l’URL SAS|Échec : copie d’images. Impossible de télécharger l’objet blob avec l’URI SAS fournie|Mettez à jour l’URL SAS avec les autorisations définies sur « Lecture » et « Liste »|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Échec lors de la copie d’images : l’URL SAS contient des espaces blancs dans le nom du disque dur virtuel|Échec : copie d’images. Impossible de télécharger l’objet blob avec l’URI SAS fournie.|Mettez à jour l’URL SAS sans espaces blancs|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Échec lors de la copie d’images : erreur d’autorisation d’URL SAS|Échec : copie d’images. Impossible de télécharger l’objet blob en raison d’une erreur d’autorisation|Générez à nouveau l’URL SAS|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Échec lors de la copie d’images : les paramètres « st » et « se » de l’URL de la signature d’accès partagé ne possèdent pas de spécification complète de date-heure|Échec : copie d’images. Impossible de télécharger l’objet blob en raison d’une URL de signature d’accès partagé incorrecte |Les paramètres Date de début et Date de fin (« st », « se ») de l’URL de la signature d’accès partagé doivent impérativement comporter une spécification complète de date-heure, par exemple 11-02-2017T00:00:00Z, et par seulement la date ou les versions raccourcies de l’heure. Il est possible de rencontrer ce scénario dans Azure CLI 2.0 (commande az). Veillez à indiquer la spécification complète de date-heure et à régénérer l’URL de la signature d’accès partagé.|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|

## <a name="next-step"></a>Étapes suivantes

Une fois que vous avez terminé avec les détails de la référence SKU, vous pouvez consulter le [Guide de contenu marketing Azure Marketplace][link-pushstaging]. Dans cette étape du processus de publication, vous devez fournir le contenu marketing, la tarification et d’autres informations nécessaires avant l’ **étape 3 : test de votre machine virtuelle en mode intermédiaire**, où vous testez divers scénarios de cas d’utilisation avant de déployer l’offre sur Azure Marketplace pour une visibilité publique et l’achat.  

## <a name="see-also"></a>Voir aussi
* [Mise en route : publication d’une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)

[img-acom-1]:media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]:media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]:media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]:media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]:media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]:media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-manage-vm-new]:media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]:media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]:media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]:marketplace-publishing-push-to-staging.md
[link-github-waagent]:https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]:https://azurestorageexplorer.codeplex.com/
[link-azure-2]:../storage/blobs/storage-dotnet-shared-access-signature-part-2.md
[link-azure-1]:../storage/common/storage-dotnet-shared-access-signature-part-1.md
[link-msft-download]:http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]:https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]:https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]:https://portal.azure.com
[link-pubportal]:https://publish.windowsazure.com
[link-sql-2014-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/
