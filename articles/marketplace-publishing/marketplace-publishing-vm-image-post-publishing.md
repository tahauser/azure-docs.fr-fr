---
title: Gérer votre image de machine virtuelle sur la Place de marché Azure | Microsoft Docs
description: Guide détaillé concernant la gestion de votre image de machine virtuelle sur la Place de marché Azure après publication initiale de celle-ci
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: mbaldwin;
ms.openlocfilehash: 19f60de4a6c651885da7ff1fb7612a77f3d0c8f1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Guide de post-production pour les offres de machine virtuelle dans Azure Marketplace
Cet article explique comment vous mettre à jour une offre de machine virtuelle active dans la Place de marché Azure. Il vous guide dans le processus d’ajout d’une ou plusieurs références (SKU) à une offre existante. Il vous guide également dans le processus de suppression d’une offre de machine virtuelle active ou d’une référence (SKU) de la Place de marché.

Une fois qu’une offre ou une référence (SKU) figure dans le [portail Azure](http://portal.azure.com), vous ne pouvez plus modifier les zones de texte suivantes :

* **Identificateur de l’offre** : dans le Portail de publication, accédez à **Machines virtuelles**, puis sélectionnez votre offre. Cliquez ensuite sur **IMAGES DE MACHINE VIRTUELLE** > **Identificateur de l’offre**.
* **Identificateur SKU** : dans le Portail de publication, accédez à **Machines virtuelles**, puis sélectionnez votre offre. Cliquez ensuite sur **Références (SKU)** > **Ajouter une référence (SKU)**.
* **Espace de noms d’éditeur** : dans le Portail de publication, accédez à **Machines virtuelles** > **Procédure pas à pas** > **Parlez-nous de votre entreprise** (sous « Étape 2 Inscrire votre entreprise ») &gt; **Espace de noms d’éditeur** > **Espace de noms**.

Une fois qu’une offre ou une référence (SKU) figure sur la [Place de marché](http://azure.microsoft.com/marketplace), vous ne pouvez plus modifier les zones de texte suivantes :

* **Identificateur de l’offre** : dans le Portail de publication, accédez à **Machines virtuelles**, puis sélectionnez votre offre. Cliquez ensuite sur **IMAGES DE MACHINE VIRTUELLE** > **Identificateur de l’offre**.
* **Identificateur SKU** : dans le Portail de publication, accédez à **Machines virtuelles**, puis sélectionnez votre offre. Cliquez ensuite sur **Références (SKU)** > **Ajouter une référence (SKU)**.
* **Espace de noms d’éditeur** : dans le Portail de publication, accédez à **Machines virtuelles** > **Procédure pas à pas** > **Parlez-nous de votre entreprise** (sous « Étape 2 S’inscrire ») &gt; **Espace de noms d’éditeur** > **Espace de noms**.
* **Ports** : dans le Portail de publication, accédez à **Machines virtuelles**, puis sélectionnez votre offre. Cliquez ensuite sur **IMAGES DE MACHINE VIRTUELLE** > **Ports ouverts**.
* **Modification de la tarification des références (SKU) répertoriées**
* **Modification du modèle de facturation des références (SKU) répertoriées**
* **Suppression des régions de facturation des références répertoriées**
* **Modification du nombre de disques de données des références répertoriées**

## <a name="update-the-technical-details-of-a-sku"></a>Mettre à jour les détails techniques d’une référence (SKU)
Pour ajouter une version à la référence (SKU) répertoriée et republier votre offre, procédez comme suit :

1. Connectez-vous au [Portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **Machines Virtuelles**, puis sélectionnez votre offre.
3. Dans le menu à gauche, cliquez sur l’onglet **IMAGES DE MACHINE VIRTUELLE**.
4. Dans la section **Références (SKU)**, recherchez la référence (SKU) que vous souhaitez mettre à jour.
5. Ajoutez un numéro de version pour la référence (SKU), puis cliquez sur le bouton **+**. La nouvelle version doit être au format X.Y.Z, où X, Y et Z sont des entiers. Les modifications de la version doivent uniquement être incrémentielles.
6. Dans la boîte de dialogue **URL de disque dur virtuel de système d’exploitation**, entrez l’URI de la signature d’accès partagé créée pour le disque dur virtuel du système d’exploitation, puis enregistrez les modifications.

   > [!IMPORTANT]
   > Vous ne pouvez pas incrémenter/décrémenter le nombre de disques de données d’une référence (SKU) répertoriée. Vous devez dans ce cas créer une nouvelle référence. Pour des instructions détaillées, voir la section [Ajouter une référence (SKU) à une offre répertoriée](#add-a-new-sku-under-a-listed-offer).
   >
   >
7. Accédez à l’onglet **PUBLISH** (PUBLIER), puis cliquez sur **PUSH TO STAGING** (POUSSER VERS L’INTERMÉDIAIRE). Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, voir [Tester votre offre de machine virtuelle pour la Place de marché](marketplace-publishing-vm-image-test-in-staging.md).
8. Après avoir testé votre offre dans l’emplacement intermédiaire, accédez à l’onglet **PUBLIER** dans le Portail de publication. Cliquez sur **REQUEST APPROVAL TO PUSH TO PRODUCTION** (DEMANDER UNE APPROBATION POUR ENVOYER EN PRODUCTION) afin de republier votre offre sur la Place de marché.

    ![Images de machine virtuelle](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>Mettre à jour les détails non techniques d’une offre ou d’une référence (SKU)
Vous pouvez mettre à jour les détails non techniques (marketing, informations juridiques, support, catégories) de votre offre active ou de votre référence (SKU) sur la Place de marché.

### <a name="update-the-offer-description-and-logos"></a>Mettre à jour la description et les logos de l’offre
Pour mettre à jour les détails de l’offre et republier celle-ci, procédez comme suit :

1. Connectez-vous au [Portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **Machines Virtuelles**, puis sélectionnez votre offre.
3. Dans le menu à gauche, cliquez sur l’onglet **MARKETING**.
4. Cliquez sur **Anglais (États-Unis)**.
5. Cliquez sur l’onglet **DÉTAILS**. Dans la section **Description**, mettez à jour les informations **TITRE**, **RÉSUMÉ** et **RÉSUMÉ LONG** de l’offre, puis enregistrez les modifications.

   > [!NOTE]
   > Lorsque vous mettez à jour les détails de la référence (SKU), tenez compte des restrictions suivantes : 
   * N’entrez pas deux fois le même texte dans les descriptions de l’offre et de la référence (SKU).
   * N’entrez pas deux fois le même texte pour le titre de la référence (SKU) et le résumé long de l’offre. 
   * N’entrez pas deux fois le même texte pour le titre de la référence (SKU) et le résumé de l’offre.
   >
   >

    ![Détails](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. Dans la section **LOGOS** de l’onglet **DÉTAILS**, mettez à jour les logos. Assurez-vous que les logos respectent les [Instructions de la Place de marché Azure](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).

   > [!NOTE]
   > Une bannière est facultative. Vous pouvez choisir de ne pas charger de bannière. Toutefois, après qu’une bannière a été chargée, il n’existe aucun moyen de la supprimer du Portail de publication. Suivez les [instructions relatives aux bannières](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
   >
   >
7. Accédez à l’onglet **PUBLISH** (PUBLIER), puis cliquez sur **PUSH TO STAGING** (POUSSER VERS L’INTERMÉDIAIRE). Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, voir [Tester votre offre de machine virtuelle pour la Place de marché](marketplace-publishing-vm-image-test-in-staging.md).
8. Après avoir testé votre offre dans l’emplacement intermédiaire, accédez à l’onglet **PUBLIER** dans le Portail de publication. Cliquez sur **REQUEST APPROVAL TO PUSH TO PRODUCTION** (DEMANDER UNE APPROBATION POUR ENVOYER EN PRODUCTION) afin de republier votre offre sur la Place de marché.

    ![Logos](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>Mettre à jour la description de la référence (SKU)
Pour mettre à jour les détails de la référence (SKU) et republier votre offre, procédez comme suit :

1. Connectez-vous au [Portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **Machines Virtuelles**, puis sélectionnez votre offre.
3. Dans le menu à gauche, cliquez sur l’onglet **MARKETING**.
4. Cliquez sur **Anglais (États-Unis)**.
5. Cliquez sur l’onglet **PLANS**. Dans la section **Références (SKU)**, mettez à jour les informations **TITRE**, **RÉSUMÉ** et **DESCRIPTION** de la référence (SKU), puis enregistrez les modifications.

   > [!NOTE]
   > Lorsque vous mettez à jour les détails de la référence (SKU), tenez compte des restrictions suivantes : 
   * N’entrez pas deux fois le même texte dans les descriptions de l’offre et de la référence (SKU). 
   * N’entrez pas deux fois le même texte pour le titre de la référence (SKU) et le résumé long de l’offre. 
   * N’entrez pas deux fois le même texte pour le titre de la référence (SKU) et le résumé de l’offre.
   >
   >
6. Accédez à l’onglet **PUBLISH** (PUBLIER), puis cliquez sur **PUSH TO STAGING** (POUSSER VERS L’INTERMÉDIAIRE). Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, voir [Tester votre offre de machine virtuelle pour la Place de marché](marketplace-publishing-vm-image-test-in-staging.md).
7. Après avoir testé votre offre dans l’emplacement intermédiaire, accédez à l’onglet **PUBLIER** dans le Portail de publication. Cliquez sur **REQUEST APPROVAL TO PUSH TO PRODUCTION** (DEMANDER UNE APPROBATION POUR ENVOYER EN PRODUCTION) afin de republier votre offre sur la Place de marché.

    ![Abonnements](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>Modifier ou ajouter des liens
Pour modifier ou ajouter des liens, puis republier votre offre, procédez comme suit :

1. Connectez-vous au [Portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **Machines Virtuelles**, puis sélectionnez votre offre.
3. Dans le menu à gauche, cliquez sur l’onglet **MARKETING**.
4. Cliquez sur **Anglais (États-Unis)**.
5. Cliquez sur l’onglet **LIENS**.
6. Pour ajouter un lien, dans la section **Liens**, cliquez sur **+ AJOUTER UN LIEN**. Dans la boîte de dialogue **Ajouter un lien**, spécifiez le **TITRE** et l’**URL** du lien, puis enregistrez les modifications. Vous pouvez entrer tout lien contenant des informations pouvant aider les clients.
7. Pour mettre à jour ou supprimer un lien existant, sélectionnez-le, puis cliquez sur le bouton **Modifier** ou **Supprimer**.

   > [!NOTE]
   > Assurez-vous que les liens que vous avez entrés dans cette section fonctionnent correctement, car ils sont validés durant le processus de demande de production.
   >
   >
8. Accédez à l’onglet **PUBLISH** (PUBLIER), puis cliquez sur **PUSH TO STAGING** (POUSSER VERS L’INTERMÉDIAIRE). Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, voir [Tester votre offre de machine virtuelle pour la Place de marché](marketplace-publishing-vm-image-test-in-staging.md).
9. Après avoir testé votre offre dans l’emplacement intermédiaire, accédez à l’onglet **PUBLIER** dans le Portail de publication. Cliquez sur **REQUEST APPROVAL TO PUSH TO PRODUCTION** (DEMANDER UNE APPROBATION POUR ENVOYER EN PRODUCTION) afin de republier votre offre sur la Place de marché.

    ![Liens](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![Ajouter un lien](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>Modifier ou ajouter un exemple d’image
Pour modifier ou ajouter un exemple d’image, puis republier votre offre, procédez comme suit :

> [!NOTE]
> Il n’est possible d’afficher qu’un seul exemple d’image sur la page [https://portal.azure.com](https://portal.azure.com).
>
>

1. Connectez-vous au [Portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **Machines Virtuelles**, puis sélectionnez votre offre.
3. Dans le menu à gauche, cliquez sur l’onglet **MARKETING**.
4. Cliquez sur **Anglais (États-Unis)**.
5. Cliquez sur l’onglet **EXEMPLES D’IMAGES**.
6. Pour ajouter un exemple d’image, dans la section **Exemples d’images**, cliquez sur **CHARGER UNE NOUVELLE IMAGE**, puis enregistrez les modifications.

   > [!NOTE]
   > L’ajout d’un exemple d’image est facultatif.
   >
7. Accédez à l’onglet **PUBLISH** (PUBLIER), puis cliquez sur **PUSH TO STAGING** (POUSSER VERS L’INTERMÉDIAIRE). Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, voir [Tester votre offre de machine virtuelle pour la Place de marché](marketplace-publishing-vm-image-test-in-staging.md).
8. Après avoir testé votre offre dans l’emplacement intermédiaire, accédez à l’onglet **PUBLIER** dans le Portail de publication. Cliquez sur **REQUEST APPROVAL TO PUSH TO PRODUCTION** (DEMANDER UNE APPROBATION POUR ENVOYER EN PRODUCTION) afin de republier votre offre sur la Place de marché.

    ![Exemples d’images](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>Mettre à jour les informations juridiques
Pour mettre à jour les informations juridiques et republier votre offre, procédez comme suit :

1. Connectez-vous au [Portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **Machines Virtuelles**, puis sélectionnez votre offre.
3. Dans le menu à gauche, cliquez sur l’onglet **MARKETING**.
4. Cliquez sur **Anglais (États-Unis)**.
5. Cliquez sur l’onglet **LÉGAL**. Dans la section **Légal**, mettez à jour vos stratégies/conditions d’utilisation. Entrez ou collez les stratégies/conditions d’utilisation dans la boîte de dialogue **CONDITIONS D’UTILISATION**, puis enregistrez les modifications.
6. Le nombre de caractères pour les conditions légales est limité à 1 million.
7. Accédez à l’onglet **PUBLISH** (PUBLIER), puis cliquez sur **PUSH TO STAGING** (POUSSER VERS L’INTERMÉDIAIRE). Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, voir [Tester votre offre de machine virtuelle pour la Place de marché](marketplace-publishing-vm-image-test-in-staging.md).
8. Après avoir testé votre offre dans l’emplacement intermédiaire, accédez à l’onglet **PUBLIER** dans le Portail de publication. Cliquez sur **REQUEST APPROVAL TO PUSH TO PRODUCTION** (DEMANDER UNE APPROBATION POUR ENVOYER EN PRODUCTION) afin de republier votre offre sur la Place de marché.

    ![Informations juridiques](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>Mettre à jour les informations de support
Pour mettre à jour les informations de support et republier votre offre, procédez comme suit :

1. Connectez-vous au [Portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **Machines Virtuelles**, puis sélectionnez votre offre.
3. Dans le menu à gauche, cliquez sur l’onglet **SUPPORT**.
4. Dans la section **Engineering Contact** (Coordonnées Ingénierie), mettez à jour les détails relatifs à la manière de contacter l’équipe d’ingénierie. Ces coordonnées sont uniquement utilisées pour les communications internes entre le partenaire et Microsoft.
5. Dans la section **Service client**, mettez à jour les coordonnées et l’**URL** du service. Ces coordonnées sont uniquement utilisées pour les communications internes entre le partenaire et Microsoft.

   > [!NOTE]
   > Si vous souhaitez offrir uniquement un support par e-mail, entrez un numéro de téléphone factice dans la section **Service client**. Dans ce cas, l’adresse e-mail que vous avez fournie est utilisée à la place du numéro de téléphone.
   >
   >
6. Accédez à l’onglet **PUBLISH** (PUBLIER), puis cliquez sur **PUSH TO STAGING** (POUSSER VERS L’INTERMÉDIAIRE). Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, voir [Tester votre offre de machine virtuelle pour la Place de marché](marketplace-publishing-vm-image-test-in-staging.md).
7. Après avoir testé votre offre dans l’emplacement intermédiaire, accédez à l’onglet **PUBLIER** dans le Portail de publication. Cliquez sur **REQUEST APPROVAL TO PUSH TO PRODUCTION** (DEMANDER UNE APPROBATION POUR ENVOYER EN PRODUCTION) afin de republier votre offre sur la Place de marché.

    ![Support](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>Mettre à jour les catégories
Pour mettre à jour la section des catégories pour votre offre et republier celle-ci, procédez comme suit :

1. Connectez-vous au [Portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **Machines Virtuelles**, puis sélectionnez votre offre.
3. Dans le menu à gauche, cliquez sur l’onglet **CATÉGORIES**.
4. Dans la section **Catégories**, vous pouvez mettre à jour les catégories pour votre offre, puis enregistrer les modifications. Vous pouvez sélectionner jusqu’à cinq catégories dans la galerie Place de marché Azure.
5. Accédez à l’onglet **PUBLISH** (PUBLIER), puis cliquez sur **PUSH TO STAGING** (POUSSER VERS L’INTERMÉDIAIRE). Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, voir [Tester votre offre de machine virtuelle pour la Place de marché](marketplace-publishing-vm-image-test-in-staging.md).
6. Après avoir testé votre offre dans l’emplacement intermédiaire, accédez à l’onglet **PUBLIER** dans le Portail de publication. Cliquez sur **REQUEST APPROVAL TO PUSH TO PRODUCTION** (DEMANDER UNE APPROBATION POUR ENVOYER EN PRODUCTION) afin de republier votre offre sur la Place de marché.

    ![Catégories](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>Ajouter une référence (SKU) à une offre répertoriée
Pour ajouter une référence (SKU) à votre offre active, procédez comme suit :

1. Connectez-vous au [Portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **Machines Virtuelles**, puis sélectionnez votre offre.
3. Dans le menu à gauche, cliquez sur l’onglet **RÉFÉRENCES (SKU)**. Cliquez ensuite sur **Add a SKU** (Ajouter une référence (SKU)). 
4. Dans la boîte de dialogue, entrez un **identificateur de référence (SKU)** en minuscules. Si vous souhaitez publier la nouvelle référence SKU avec un modèle de facturation BYOL, activez la case à cocher **Bring your own license (BYOL) billing model** (modèle de facturation BYOL (apportez votre propre licence)). Autrement, désactivez la case à cocher. Cliquez sur la graduation pour créer une référence (SKU). Si vous n’avez pas choisi le modèle de facturation BYOL, le modèle de facturation est automatiquement défini sur la facturation horaire. Si vous souhaitez la version d’évaluation gratuite de 30 jours pour le modèle de facturation horaire, pour l’option **Is a free trial available?** (Une version d’évaluation gratuite est-elle disponible ?), sélectionnez **Un mois**. Autrement, sélectionnez **Pas de version d’évaluation**. L’option **Is a free trial available?** (Une version d’évaluation gratuite est-elle disponible ?) apparaît uniquement si vous n’avez pas sélectionné BYOL lors de la création de la référence (SKU).

   > [!IMPORTANT]
   > L’option **Masquer cette référence (SKU) à partir du Marketplace, car elle doit toujours être achetée via un modèle de solution** peut être définie sur **Oui** *uniquement* si vous êtes autorisé à publier un modèle de solution. Dans le cas contraire, cette option doit toujours être définie sur **Non**.
   >
   >
4. Dans le menu à gauche, cliquez sur l’onglet **IMAGES DE MACHINE VIRTUELLE**, puis découvrez la référence (SKU) que vous avez créée.
5. Pour obtenir des instructions concernant la configuration de la nouvelle référence (SKU), voir [Obtenir une certification pour votre image de machine virtuelle](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image).
6. Pour ajouter du matériel de marketing à la nouvelle référence (SKU), voir [Fournir du contenu marketing à la Place de marché](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Pour ajouter des informations de tarification pour la nouvelle référence (SKU), voir [Définir vos prix](marketplace-publishing-push-to-staging.md#step-2-set-your-prices).
8. Accédez à l’onglet **PUBLISH** (PUBLIER), puis cliquez sur **PUSH TO STAGING** (POUSSER VERS L’INTERMÉDIAIRE). Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, voir [Tester votre offre de machine virtuelle pour la Place de marché](marketplace-publishing-vm-image-test-in-staging.md).
9. Après avoir testé votre offre dans l’emplacement intermédiaire, accédez à l’onglet **PUBLIER** dans le Portail de publication. Cliquez sur **REQUEST APPROVAL TO PUSH TO PRODUCTION** (DEMANDER UNE APPROBATION POUR ENVOYER EN PRODUCTION) afin de republier votre offre sur la Place de marché.

    ![Références (SKU)](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![Ajoutez une référence (SKU)](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>Modifier le nombre de disques de données pour une référence (SKU) répertoriée
Vous ne pouvez pas incrémenter/décrémenter le nombre de disques de données d’une référence (SKU) répertoriée. Vous devez dans ce cas créer une nouvelle référence. Pour des instructions détaillées, voir la section [Ajouter une référence (SKU) à une offre répertoriée](#add-a-new-sku-under-a-listed-offer).

## <a name="delete-a-listed-offer-from-the-marketplace"></a>Supprimer une offre répertoriée de la Place de marché
Différents aspects doivent être pris en compte en cas de demande de suppression d’une offre active. Pour obtenir des instructions de l’équipe de support concernant la suppression d’une offre répertoriée de la Place de marché, procédez comme suit :

1. Créez un ticket de support dans la page [Créer un incident](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681).

2. Pour **Gestion des offres**, sélectionnez **Type de problème**, puis, pour **Modification d’une offre et/ou d’une référence (SKU) déjà en production**, sélectionnez **Catégorie**.
3. Envoyez la demande.

L’équipe de support vous guide tout au long du processus de suppression de l’offre ou de la référence (SKU).

> [!NOTE]
> Vous pouvez supprimer une offre tant qu’elle est en état Brouillon (pas quand elle est en état Intermédiaire ou Production). Sous l’onglet **HISTORIQUE**, cliquez sur **SUPPRIMER LE BROUILLON**.
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>Supprimer une référence (SKU) de la Place de marché
Pour supprimer une référence (SKU) de la Place de marché, procédez comme suit :

1. Connectez-vous au [Portail de publication](https://publish.windowsazure.com).

2. Accédez à l’onglet **Machines Virtuelles**, puis sélectionnez votre offre.
3. Dans le volet à gauche, cliquez sur l’onglet **Références (SKU)**.
4. Sélectionnez la référence à supprimer, puis cliquez sur le bouton **Supprimer**.
5. Accédez à l’onglet **PUBLIER** dans le Portail de publication. Cliquez sur **REQUEST APPROVAL TO PUSH TO PRODUCTION** (DEMANDER UNE APPROBATION POUR ENVOYER EN PRODUCTION) afin de republier votre offre sur la Place de marché.
6. Une fois l’offre republiée sur la Place de marché, la référence (SKU) est supprimée de la Place de marché et du portail Azure.

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>Supprimer la version actuelle d’une référence (SKU) de la Place de marché
Pour supprimer la version actuelle d’une référence (SKU) de la Place de marché, procédez comme suit : 

1. Connectez-vous au [Portail de publication](https://publish.windowsazure.com).

2. Accédez à l’onglet **Machines Virtuelles**, puis sélectionnez votre offre.
3. Dans le menu à gauche, cliquez sur l’onglet **IMAGES DE MACHINE VIRTUELLE**.
4. Sélectionnez la référence (SKU) dont vous voulez supprimer la version actuelle, puis cliquez sur le bouton **Supprimer**.
5. Accédez à l’onglet **PUBLIER** dans le Portail de publication. Cliquez sur **REQUEST APPROVAL TO PUSH TO PRODUCTION** (DEMANDER UNE APPROBATION POUR ENVOYER EN PRODUCTION) afin de republier votre offre sur la Place de marché.
6. Une fois l’offre republiée sur la Place de marché, la version actuelle de la référence (SKU) répertoriée est supprimée de la Place de marché et du portail Azure. La version précédente de la référence (SKU) est alors restaurée.

## <a name="revert-the-listing-price-to-production-values"></a>Rétablir les prix aux valeurs de production
Pour rétablir les prix aux valeurs de production, procédez comme suit :

1. Connectez-vous au [Portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **Machines Virtuelles**, puis sélectionnez votre offre.
3. Dans le menu à gauche, cliquez sur l’onglet **TARIFICATION**.
4. Sélectionnez la région pour laquelle vous voulez rétablir la tarification.

    ![Régions de tarification](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. Pour les références (SKU) avec modèle de facturation horaire, rétablissez les prix de tous les cœurs tels qu’ils sont en production pour la région sélectionnée. Pour les références (SKU) avec un modèle de facturation BYOL, rendez la référence (SKU) disponible dans la région en activant la case à cocher de la référence (SKU) dans la section **EXTERNALLY-LICENSED (BYOL) SKU AVAILABILITY** (DISPONIBILITÉ DE RÉFÉRENCE (SKU) (BYOL) SOUS LICENCE EXTERNE).

    ![Modèles de tarification](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Cliquez sur **AUTOPRICE OTHER MARKETS BASED ON PRICES IN UNITED STATES** (TARIFICATION AUTOMATIQUE POUR LES AUTRES MARCHÉS EN FONCTION DES PRIX AUX ÉTATS-UNIS).

   > [!NOTE]
   > Le libellé du bouton peut varier selon la région que vous sélectionnez. Étant donné que nous avons sélectionné les États-Unis, le bouton est libellé **AUTOPRICE OTHER MARKETS BASED ON PRICES IN UNITED STATES** (TARIFICATION AUTOMATIQUE POUR LES AUTRES MARCHÉS EN FONCTION DES PRIX AUX ÉTATS-UNIS).
   >
   >

    ![Tarifer automatiquement](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. Dans la page 1 de l’Assistant de tarification automatique, cliquez sur le marché de base, puis sur le bouton **flèche**.

    ![Marché de base](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. Dans la page 2, choisissez les plans de service et les compteurs (noyaux), puis cliquez sur le bouton **flèche**.

    ![Plans de service et compteurs](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. Dans la page 3, cliquez sur **Toggle All** (Basculer tout) pour sélectionner toutes les régions. Vous pouvez aussi activer manuellement les cases à cocher de régions spécifiques. Cliquez sur le bouton **flèche**.

    ![Choisir des marchés](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. Dans la page 4, vérifiez les taux de change, puis cliquez sur **Terminer**. L’Assistant redéfinit la tarification en fonction de vos sélections.

11. Sous l’onglet **PRICING** (TARIFICATION), cliquez sur **VIEW SUMMARY AND CHANGES** (AFFICHER LE RÉSUMÉ ET MES MODIFICATIONS).
    Pour **View Version** (Version d’affichage), sélectionnez **Draft** (Brouillon), puis, pour **Compare with** (Comparer à), sélectionnez **Production**. Si vous ne constatez aucune différence de tarification, cela signifie que la tarification a été rétablie aux valeurs de production.

    ![Afficher le résumé et les modifications](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Accédez à l’onglet **PUBLISH** (PUBLIER), puis cliquez sur **PUSH TO STAGING** (POUSSER VERS L’INTERMÉDIAIRE). Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, voir [Tester votre offre de machine virtuelle pour la Place de marché](marketplace-publishing-vm-image-test-in-staging.md).
13. Après avoir testé votre offre dans l’emplacement intermédiaire, accédez à l’onglet **PUBLIER** dans le Portail de publication. Cliquez sur **REQUEST APPROVAL TO PUSH TO PRODUCTION** (DEMANDER UNE APPROBATION POUR ENVOYER EN PRODUCTION) afin de republier votre offre sur la Place de marché.

## <a name="revert-the-billing-model-to-production-values"></a>Rétablir un modèle de facturation aux valeurs de production
Pour rétablir le modèle de facturation aux valeurs de production, procédez comme suit :

1. Connectez-vous au [Portail de publication](https://publish.windowsazure.com).

2. Accédez à l’onglet **Machines Virtuelles**, puis sélectionnez votre offre.
3. Dans le menu à gauche, cliquez sur l’onglet **RÉFÉRENCES (SKU)**.
4. Cliquez sur le bouton **Modifier** pour rétablir le modèle de facturation. Dans la fenêtre qui s’ouvre, activez ou désactivez la case à cocher **Billing and licensing is done externally from Azure (aka Bring Your Own License)** (La facturation et la gestion des licences sont effectuées hors d’Azure (BYOL (apportez votre propre licence)).

    ![Modifier la facturation](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Procédez de la manière décrite dans la section « Rétablir les prix aux valeurs de production » de cet article.
6. Accédez à l’onglet **PUBLISH** (PUBLIER), puis cliquez sur **PUSH TO STAGING** (POUSSER VERS L’INTERMÉDIAIRE). Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, voir [Tester votre offre de machine virtuelle pour la Place de marché](marketplace-publishing-vm-image-test-in-staging.md).
7. Après avoir testé votre offre dans l’emplacement intermédiaire, accédez à l’onglet **PUBLIER** dans le Portail de publication. Cliquez sur **REQUEST APPROVAL TO PUSH TO PRODUCTION** (DEMANDER UNE APPROBATION POUR ENVOYER EN PRODUCTION) afin de republier votre offre sur la Place de marché.

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>Rétablir le paramètre de visibilité d’une référence (SKU) à la valeur de production
Pour rétablir le paramètre de visibilité d’une référence (SKU) à la valeur de production, procédez comme suit :

1. Connectez-vous au [Portail de publication](https://publish.windowsazure.com).

2. Accédez à l’onglet **Machines Virtuelles**, puis sélectionnez votre offre.
3. Dans le menu à gauche, cliquez sur l’onglet **RÉFÉRENCES (SKU)**.
4. Sélectionnez votre référence (SKU), puis rétablissez le paramètre de visibilité de la référence (SKU) à la valeur de production.

    ![Visibilité](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. Une fois les modifications effectuées, cliquez sur **REQUEST APPROVAL TO PUSH TO PRODUCTION** (DEMANDER UNE APPROBATION POUR ENVOYER EN PRODUCTION) afin de republier votre offre sur la Place de marché.

## <a name="see-also"></a>Voir aussi
* [Comment publier et gérer une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)
* [Présentation de vos rapports de paiement Azure Marketplace](marketplace-publishing-report-payout.md)
* [Affichage et modification de « l’offre incitative revendeurs » des fournisseurs de solutions Cloud dans Azure Marketplace](marketplace-publishing-csp-incentive.md)
* [Comment résoudre les problèmes courants de publication dans Azure Marketplace](marketplace-publishing-support-common-issues.md)
* [Accès au support d'éditeur pour Azure Marketplace](marketplace-publishing-get-publisher-support.md)
* [Créer une image de machine virtuelle locale](marketplace-publishing-vm-image-creation-on-premise.md)
* [Créer une machine virtuelle Windows avec le portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
