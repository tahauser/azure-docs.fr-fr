---
title: Mettre des machines virtuelles à la disposition de vos utilisateurs Azure Stack | Microsoft Docs
description: Didacticiel pour rendre des machines virtuelles disponibles sur Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: af97f32736959f8ebf8f3c4fbca400d6b0c41f3e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>Mettre des machines virtuelles à la disposition de vos utilisateurs Azure Stack
En tant qu’un administrateur de cloud Azure Stack, vous pouvez créer des offres auxquelles vos utilisateurs (parfois appelée « locataires ») peuvent s’abonner. Avec leur abonnement, les utilisateurs peuvent utiliser les services Azure Stack.

Cet article vous explique comment créer une offre, puis la tester. Pour le test, vous allez vous connecter au portail en tant qu’utilisateur, vous abonner à l’offre, puis créer une machine virtuelle à l’aide de l’abonnement.

Contenu :

> [!div class="checklist"]
> * Créer une offre
> * Ajouter une image
> * Tester l’offre


Dans Azure Stack, les services sont fournis aux utilisateurs par le biais d’abonnements, d’offres et de plans. Les utilisateurs peuvent s’abonner à plusieurs offres. Les offres peuvent contenir un ou plusieurs plans, et les plans peuvent contenir un ou plusieurs services.

![Abonnements, offres et plans](media/azure-stack-key-features/image4.png)

Pour plus d’informations, consultez [Fonctionnalités et concepts clés d’Azure Stack](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Créer une offre

Il est maintenant possible d’effectuer toutes les étapes de préparation pour vos utilisateurs. Quand vous démarrez le processus, vous êtes d’abord invité à créer l’offre, puis un plan, et enfin des quotas.

3. **Créer une offre**

   Les offres sont des groupes d’un ou plusieurs plans que les fournisseurs proposent à l’achat ou à l’abonnement aux utilisateurs.

   a. [Connectez-vous](azure-stack-connect-azure-stack.md) au portail en tant qu’administrateur cloud, puis cliquez sur **Nouveau** > **Offres + Plans** > **Offre**.
   ![Nouvelle offre](media/azure-stack-tutorial-tenant-vm/image01.png)

   b. Dans la section **Nouvelle offre**, renseignez le **Nom d’affichage** et le **Nom de la ressource**, puis sélectionnez un **Groupe de ressources** nouveau ou existant. Le nom d’affichage correspond au nom convivial de l’offre. Seul l’opérateur cloud peut voir le nom de la ressource. Il s’agit du nom que les administrateurs utilisent pour gérer l’offre en tant que ressource Azure Resource Manager.

   ![Nom complet](media/azure-stack-tutorial-tenant-vm/image02.png)

   c. Cliquez sur **Plans de base**, puis dans la section **Plan**, cliquez sur **Ajouter** pour ajouter un nouveau plan à l’offre.

   ![Ajouter un plan](media/azure-stack-tutorial-tenant-vm/image03.png)

   d. Dans la section **Nouveau plan**, renseignez le **Nom d’affichage** et le **Nom de la ressource**. Le nom d’affichage correspond au nom convivial du plan, que les locataires voient. Seul l’opérateur cloud peut voir le nom de la ressource. Il s’agit du nom que les opérateurs cloud utilisent pour gérer le plan comme une ressource Azure Resource Manager.

   ![Nom d’affichage du plan](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. Cliquez sur **Services**, sélectionnez **Microsoft.Compute**, **Microsoft.Network** et **Microsoft.Storage**, puis cliquez sur **Sélectionner**.

   ![Services du plan](media/azure-stack-tutorial-tenant-vm/image05.png)

   f. Cliquez sur **Quotas**, puis sélectionnez le premier service pour lequel vous voulez créer un quota. Pour un quota IaaS, effectuez les étapes suivantes pour les services Calcul, Réseau et Stockage.

   Dans cet exemple, nous créons d’abord un quota pour le service Calcul. Dans la liste d’espaces de noms, sélectionnez l’espace de noms **Microsoft.Compute**, puis cliquez sur **Créer un quota**.
   
   ![Créer un quota](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. Dans la section **Créer un quota**, tapez un nom pour le quota, définissez les paramètres souhaités pour le quota, puis cliquez sur **OK**.

   ![Nom du quota](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. À présent, pour **Microsoft.Compute**, sélectionnez le quota que vous avez créé.

   ![Sélectionner un quota](media/azure-stack-tutorial-tenant-vm/image08.png)

   Répétez ces étapes pour les services Réseau et Stockage, puis cliquez sur **OK** dans la section **Quotas**.

   i. Cliquez sur **OK** dans la section **Nouveau plan**.

   j. Dans la section **Plan**, sélectionnez le nouveau plan, puis cliquez sur **Sélectionner**.

   k. Dans la section **Nouvelle offre**, cliquez sur **Créer**. Une fois l’offre créée, une notification s’affiche.

   l. Dans le menu du tableau de bord, cliquez sur **Offres**, puis cliquez sur l’offre que vous avez créée.

   m. Cliquez sur **Changer l’état**, puis sur **Public**.

   ![État Public](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Ajouter une image

Avant de pouvoir approvisionner des machines virtuelles, vous devez ajouter une image à la Place de marché Azure Stack. Vous pouvez ajouter l’image de votre choix, notamment des images Linux, à partir de la Place de marché Azure.

Si vous êtes dans un scénario connecté et que vous avez inscrit votre instance Azure Stack auprès d’Azure, vous pouvez télécharger l’image de machine virtuelle Windows Server 2016 à partir de la Place de marché Azure en suivant la procédure décrite dans la rubrique [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack](azure-stack-download-azure-marketplace-item.md).

Pour plus d’informations sur l’ajout de différents éléments à la Place de marché, consultez [Place de marché Azure Stack](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Tester l’offre

Maintenant que vous avez créé une offre, vous pouvez le tester. Connectez-vous en tant qu’utilisateur et abonnez-vous à l’offre, puis ajoutez une machine virtuelle.

1. **S’abonner à une offre**

   Vous pouvez maintenant vous connecter au portail en tant qu’utilisateur pour vous abonner à une offre.

   a. Connectez-vous au portail utilisateur en tant qu’utilisateur et cliquez sur **Obtenir un abonnement**.
   - Pour un système intégré, l’URL varie en fonction de la région et du nom de domaine externe de votre opérateur, et son format est https://portal.&lt;*région*&gt;.&lt;*FQDN*&gt;.
   - Si vous utilisez le Kit de développement Azure Stack, l’adresse du portail est https://portal.local.azurestack.external.

   ![Prendre un abonnement](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. Dans le champ **Nom d’affichage**, tapez un nom pour votre abonnement, cliquez sur **Offre**, cliquez sur l’une des offres de la section **Choisir une offre**, puis cliquez sur **Créer**.

   ![Créer une offre](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Pour afficher l’abonnement que vous avez créé, cliquez sur **Autres services**, sur **Abonnements**, puis sur votre nouvel abonnement.  

   Une fois que vous êtes abonné à une offre, actualisez le portail pour voir les services qui font partie du nouvel abonnement.

2. **Approvisionner une machine virtuelle**

   Vous pouvez maintenant vous connecter au portail en tant qu’utilisateur pour approvisionner une machine virtuelle à l’aide de l’abonnement. 

   a. Connectez-vous au portail utilisateur en tant qu’utilisateur.
      - Pour un système intégré, l’URL varie en fonction de la région et du nom de domaine externe de votre opérateur, et son format est https://portal.&lt;*région*&gt;.&lt;*FQDN*&gt;.
   - Si vous utilisez le Kit de développement Azure Stack, l’adresse du portail est https://portal.local.azurestack.external.

   b.  Sur le tableau de bord, cliquez sur **Nouveau** > **Calcul** > **Windows Server 2016 Datacenter Evaluation**, puis cliquez sur **Créer**.

   c. Dans la section **Bases**, tapez un **Nom**, un **Nom d’utilisateur** et un **Mot de passe**, choisissez un **Abonnement**, créez un **Groupe de ressources** (ou sélectionnez un groupe existant), puis cliquez sur **OK**.

   d. Dans la section **Choisir une taille**, cliquez sur **A1 Standard**, puis cliquez sur **Sélectionner**.  

   e. Dans la section **Paramètres**, cliquez sur **Réseau virtuel**. Dans la section**Choisir un réseau virtuel**, cliquez sur **Créer nouveau**. Dans la section **Créer un réseau virtuel**, acceptez toutes les valeurs par défaut, puis cliquez sur **OK**. Dans la section **Paramètres**, cliquez sur **OK**.

   ![Création d’un réseau virtuel](media/azure-stack-provision-vm/image04.png)

   f. Dans la section **Résumé**, cliquez sur **OK** pour créer la machine virtuelle.  

   g. Pour voir la nouvelle machine virtuelle, cliquez sur **Toutes les ressources**, puis recherchez la machine virtuelle et cliquez sur son nom.

    ![Toutes les ressources](media/azure-stack-provision-vm/image06.png)

Ce que vous avez appris dans ce didacticiel :

> [!div class="checklist"]
> * Créer une offre
> * Ajouter une image
> * Tester l’offre

> [!div class="nextstepaction"]
> [Mettre des applications web, mobiles et API à la disposition de vos utilisateurs Azure Stack](azure-stack-tutorial-app-service.md)