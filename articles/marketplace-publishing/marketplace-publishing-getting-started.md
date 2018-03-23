---
title: Présentation de la procédure de création et de déploiement d’une offre dans le Marketplace | Microsoft Docs
description: Découvrez les étapes nécessaires pour devenir un développeur Microsoft approuvé, et pour créer et déployer une image de machine virtuelle, un modèle, un service de données ou un service de développement dans la Place de marché Azure.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: mbaldwin
ms.openlocfilehash: 02df78de48c8466f3488ef5f8346e1852b4e3ded
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
> [!NOTE]
> Cette documentation n’est plus à jour et n’est pas correcte. Pour obtenir des conseils sur la publication d’une offre sur la Place de Marché Azure, accédez plutôt au [Guide vendeur](https://docs.microsoft.com/azure/marketplace/seller-guide/cloud-partner-portal-seller-guide) de la Place de Marché Azure.

# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Publier et gérer une offre dans la Place de marché Azure
Cet article a pour but d’aider les développeurs à créer, déployer et gérer leurs solutions répertoriées sur la Place de marché Azure afin de la proposer à d’autres clients et partenaires Azure.

## <a name="marketplace-publishing"></a>Publication sur la Place de marché
En tant qu’éditeur Azure, vous pouvez distribuer et vendre votre solution ou votre service innovant à d’autres développeurs, éditeurs de logiciels indépendants et professionnels de l’informatique dans la Place de marché Azure. Sur la Place de marché, vous pouvez atteindre les clients qui souhaitent développer rapidement leurs applications cloud et solutions mobiles. Si votre solution s’adresse aux utilisateurs professionnels, vous pouvez opter pour la Place de marché [AppSource](http://appsource.microsoft.com).


## <a name="supported-types-of-solutions"></a>Types de solutions pris en charge
La première étape en tant qu’éditeur est de définir le type de solution offert par votre entreprise. La Place de marché prend en charge les types d’offres suivants :

|Type de solution|Machine virtuelle|Modèle de solution|
|---|---|---|
|**Définition**|Images préconfigurées dans lesquelles un système d’exploitation et une ou plusieurs applications sont entièrement installés. Une image de machine virtuelle fournit les informations nécessaires pour créer et déployer des machines virtuelles dans le service Azure Virtual Machines.|Structure de données capable de référencer un ou plusieurs services Azure distincts, y compris des services publiés par d’autres vendeurs. Les abonnés Azure peuvent l’utiliser pour déployer une ou plusieurs offres d’une seule façon coordonnée.|
|**Exemple**|En tant qu’éditeur Azure, vous avez créé et validé une machine virtuelle avec un service de base de données innovant. Les autres abonnés Azure souhaitent acquérir et déployer cette machine virtuelle dans leurs environnements de service cloud.|En tant qu’éditeur Azure, vous avez regroupé un ensemble de services sur Azure qui accélère le déploiement de services cloud avec un équilibrage de charge, une sécurité renforcée et une haute disponibilité. Les autres abonnés Azure peuvent gagner du temps en achetant le modèle de solution qui répond à leurs objectifs. Ils n’ont plus besoin de localiser, d’acheter, de déployer et de configurer manuellement des services Azure identiques ou similaires.|

> [!NOTE]
> Certaines étapes sont partagées entre les différents types de solutions tandis que d’autres sont propres à chaque type de solution. Cet article présente brièvement la procédure que vous devez suivre pour tous les types de solutions.

## <a name="publish-a-solution"></a>Publier une application
![Nommer, inscrire, publier](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>Nommer votre solution pour une pré-approbation
Pour publier une [solution](https://createopportunity.azurewebsites.net) de machine virtuelle sur la Place de marché, remplissez le **formulaire de proposition de solution** sur Microsoft Azure Certified.

>[!NOTE]
> Si vous travaillez avec un responsable de compte partenaire ou un responsable partenaire DX, demandez-lui de recommander votre solution pour le programme de certification Azure Certified. Vous pouvez également accéder à la page web [Microsoft Azure Certified](http://createopportunity.azurewebsites.net) et remplir le formulaire de demande. Entrez l’adresse de courrier de votre responsable de compte ou responsable partenaire DX dans le champ **Microsoft Sponsor Contact** (Coordonnées du sponsor Microsoft).

Si vous répondez aux critères d’éligibilité des [Politiques concernant la participation à la Place de marché Azure](http://go.microsoft.com/fwlink/?LinkID=526833) et que votre application est approuvée, nous travaillerons à vos côtés pour intégrer votre solution à la Place de marché.

### <a name="register-your-account-as-a-microsoft-seller"></a>Inscrire votre compte en tant que vendeur Microsoft
Inscrivez votre compte Microsoft en tant que [compte Microsoft Developer](marketplace-publishing-accounts-creation-registration.md).

### <a name="publish-your-solution"></a>Publier votre solution
Pour publier une solution sur la Place de marché, procédez comme suit :
1. Remplissez les conditions préalables non techniques.

    a. Remplissez [les conditions préalables non techniques](marketplace-publishing-pre-requisites.md).

    b. Remplissez [les conditions préalables techniques liées aux machines virtuelles](marketplace-publishing-vm-image-creation-prerequisites.md).

    c. Remplissez [les conditions préalables techniques liées au modèle de solution](marketplace-publishing-solution-template-creation-prerequisites.md).

2. Créez votre offre.

    a. Créez une offre de [machine virtuelle](marketplace-publishing-vm-image-creation.md).

    b. Créez votre offre de [modèle de solution](marketplace-publishing-solution-template-creation.md).

3. Créez votre offre de [contenu marketing](marketplace-publishing-push-to-staging.md).

4. Testez votre offre dans un environnement intermédiaire.

    a. Testez votre offre de machine virtuelle dans un [environnement intermédiaire](marketplace-publishing-vm-image-test-in-staging.md).

    b. Testez votre offre de modèle de solution dans un [environnement intermédiaire](marketplace-publishing-solution-template-test-in-staging.md).

5. Déployez votre offre sur la [Place de marché](marketplace-publishing-push-to-production.md).


### <a name="create-and-manage-a-virtual-machine-image"></a>Créer et gérer une image de machine virtuelle
Créez et gérez une image de machine virtuelle à l’aide des ressources suivantes :
* Créez une image de machine virtuelle [locale](marketplace-publishing-vm-image-creation-on-premise.md).
* Créez une machine virtuelle exécutant [Windows dans le portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Créez une machine virtuelle exécutant [Linux dans le portail Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Résolvez les problèmes courants rencontrés lors de la [création du disque dur virtuel](marketplace-publishing-vm-image-creation-troubleshooting.md).

## <a name="manage-your-solution"></a>Gérer votre solution
Gérez votre solution à l’aide des ressources suivantes :
* [Lire le guide de post-production pour les offres de machine virtuelle](marketplace-publishing-vm-image-post-publishing.md)
* [Mettre à jour les détails non techniques d’une offre ou d’une référence (SKU)](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [Mettre à jour les détails techniques d’une offre ou d’une référence (SKU)](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [Ajouter une nouvelle référence (SKU) à une offre répertoriée](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [Modifier le nombre de disques de données pour une référence (SKU) répertoriée](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [Supprimer une offre répertoriée de la Place de Marché](marketplace-publishing-vm-image-post-publishing.md)
* [Supprimer une référence (SKU) de la Place de Marché](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [Supprimer la version actuelle d’une référence (SKU) de la Place de Marché](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [Restaurer des prix aux valeurs de production](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [Restaurer un modèle de facturation aux valeurs de production](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [Rétablir le paramètre de visibilité d’une référence (SKU) répertoriée à la valeur de production](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)

## <a name="additional-resources"></a>Ressources supplémentaires
[Configurer Azure PowerShell](marketplace-publishing-powershell-setup.md)
