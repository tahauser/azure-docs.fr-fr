---
title: Provisionner une machine virtuelle Deep Learning Data Science Virtual Machine dans Azure | Microsoft Docs
description: Configurez et créez une instance Deep Learning Data Science Virtual Machine dans Azure à des fins d’analytique et d’apprentissage automatique.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 75baef65b8a826009cdbaac3a260d021559504da
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Provisionner une machine virtuelle DLVM dans Azure 

La machine virtuelle DLVM (Deep Learning Virtual Machine) est une variante de la célèbre machine virtuelle [DSVM (Data Science Virtual Machine)](http://aka.ms/dsvm). Elle a été spécialement configurée pour faciliter l'utilisation des instances de machine virtuelle basées sur GPU visant à former rapidement des modèles d’apprentissage profond. Elle est prise en charge avec DSVM Windows 2016 ou Ubuntu comme base. La machine virtuelle DLVM partage les mêmes images de machines virtuelles principales, ce qui explique la richesse des outils qui sont disponibles avec la machine virtuelle DSVM. 

La machine virtuelle DLVM contient plusieurs outils d’intelligence artificielle, notamment les éditions GPU des frameworks d’apprentissage profond courants tels que Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 ou Chainer. Elle contient également des outils permettant d’acquérir et de prétraiter des images et des données textuelles, des outils pour la modélisation et le développement de science des données, tels que Microsoft R Server Developer Edition, Anaconda Python, les bloc-notes Jupyter pour Python et R, les IDE pour Python et R et les bases de données SQL, ainsi que de nombreux autres outils d’apprentissage automatique et de science des données. 

## <a name="create-your-deep-learning-virtual-machine"></a>Créer une instance Deep Learning Virtual Machine
Voici les étapes de création d’une instance Deep Learning Virtual Machine : 

1. Accédez à la liste des machines virtuelles présentes sur le [portail Azure](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
).
2. Sélectionnez le bouton **Créer** au bas de l’écran pour accéder à un Assistant.![create-dlvm](./media/dlvm-provision-wizard.PNG)
3. L’Assistant utilisé pour créer l’instance DLVM nécessite les **entrées** de chacune des **quatre étapes** énumérées à droite de cette figure. Voici les entrées nécessaires à la configuration de chacune de ces étapes :
   
   1. **Concepts de base**
      
      1. **Name**(Nom) : nom du serveur Data Science que vous créez.
      2. **Sélectionner le type de système d’exploitation pour l’instance Deep Learning Virtual Machine** : choisissez Windows ou Linux (pour une instance de DSVM ayant une base Windows 2016 ou Ubuntu Linux)
      2. **Nom d’utilisateur**: identifiant de connexion du compte administrateur.
      3. **Mot de passe**: mot de passe du compte administrateur.
      4. **Subscription**(Abonnement) : si vous disposez de plusieurs abonnements, sélectionnez celui qui sera associé à la création et à la facturation de la machine.
      5. **Groupe de ressources** : vous pouvez en créer un nouveau ou utiliser un groupe de ressources Azure **vide** existant dans votre abonnement.
      6. **Location**(Emplacement) : sélectionnez le centre de données qui convient le mieux. Généralement, il s’agit du centre de données qui héberge la plupart de vos données ou du centre de données le plus proche de votre emplacement physique afin d’accélérer l’accès au réseau 
      
> [!NOTE]
> Étant donné que la machine virtuelle DLVM est provisionnée sur des instances de machines virtuelles GPU NC-Series Azure, vous devez choisir un emplacement dans Azure qui contient des GPU. Les régions qui comprennent des machines virtuelles GPU sont les suivantes : **Est des États-Unis, Nord du centre des États-Unis, Sud-Centre des États-Unis, États-Unis de l'Ouest 2, Europe du Nord, Europe de l’Ouest**. Pour obtenir la dernière liste en date, accédez à la [page Disponibilité des produits par région](https://azure.microsoft.com/en-us/regions/services/), puis recherchez **NC-Series** sous **Compute**. 

   2. **Paramètres** : sélectionnez une taille de machine virtuelle GPU NC-Series qui répond à vos exigences fonctionnelles et à votre budget. Créez un compte de stockage pour votre machine virtuelle.  ![dlvm-settings](./media/dlvm-provision-step-2.PNG)
   
   3. **Résumé**: vérifiez que toutes les informations que vous avez saisies sont correctes.
   5. **Acheter** : cliquez sur **Acheter** pour démarrer l’approvisionnement. Les conditions de la transaction vous sont communiquées via un lien. La machine virtuelle n'est pas assortie de frais supplémentaires au-delà du calcul de la taille de serveur que vous avez choisie à l'étape **Taille** . 

> [!NOTE]
> L’approvisionnement prend environ 10 à 20 minutes. L’état de l’approvisionnement est affiché sur le portail Azure.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>Comment accéder à l’instance Deep Learning Virtual Machine

### <a name="windows-edition"></a>Édition Windows
Une fois la machine virtuelle créée, vous pouvez vous y connecter à l’aide du bureau distant en utilisant les informations d’identification du compte administrateur que vous avez configurées dans la section **Paramètres de base** précédente. 

### <a name="linux-edition"></a>Édition Linux

Une fois la machine virtuelle créée, vous pouvez vous y connecter avec SSH. Utilisez les informations d’identification de compte créées dans la section **Paramètres de base** de l’étape 3 de l’interface de l’interpréteur de commandes texte. Sur un client Windows, vous pouvez télécharger un outil client SSH tel que [Putty](http://www.putty.org). Si vous préférez un bureau graphique (système Windows X), vous pouvez utiliser le transfert X11 sur Putty ou installer le client X2Go.

> [!NOTE]
> Lors de tests, le client X2Go a obtenu de meilleures performances que le transfert X11. Nous recommandons d’utiliser le client X2Go pour une interface de bureau graphique.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Installation et configuration du client X2Go
La machine virtuelle DLVM Linux est déjà provisionnée avec le serveur X2Go et elle est prête à accepter des connexions client. Pour vous connecter au bureau graphique de la machine virtuelle Linux, effectuez les opérations suivantes sur votre client :

1. Téléchargez et installez le client X2Go pour votre plateforme cliente sur [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Exécutez le client X2Go et sélectionnez **New Session**(Nouvelle session). Une fenêtre de configuration avec plusieurs onglets s’ouvre. Entrez les paramètres de configuration suivants :
   * **Onglet Session**:
     * **Host**(Hôte) : nom d’hôte ou adresse IP de votre machine virtuelle de science des données Linux.
     * **Login**(Connexion) : nom d’utilisateur sur la machine virtuelle Linux.
     * **SSH Port**(Port SSH) : conservez la valeur par défaut 22.
     * **Type de session** : remplacez la valeur par **XFCE**. Les instances Linux de DSVM prennent uniquement en charge XFCE Desktop.
   * **Onglet Media**(Média) : vous pouvez désactiver l’impression client et la prise en charge du son si vous n’en avez pas besoin.
   * **Shared folders**(Dossiers partagés) : si vous souhaitez que les répertoires de vos ordinateurs clients soient montés sur la machine virtuelle Linux, ajoutez ceux que vous souhaitez partager avec la machine virtuelle sous cet onglet.

Une fois connecté à la machine virtuelle à l’aide du client SSH ou du bureau graphique XFCE par le biais du client X2Go, vous pouvez commencer à utiliser les outils installés et configurés sur la machine virtuelle. Sur XFCE, vous pouvez voir les icônes de bureau et raccourcis du menu d’applications de la plupart des outils.

Une fois votre machine virtuelle créée et approvisionnée, vous pouvez commencer à utiliser les outils qui y sont installés et configurés. Pour la plupart des outils, vous disposez d'icônes de bureau et de vignettes dans le menu de démarrage. 
