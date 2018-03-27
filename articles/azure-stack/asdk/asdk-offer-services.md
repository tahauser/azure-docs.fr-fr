---
title: Dans ce didacticiel, vous allez créer une offre Azure Stack | Microsoft Docs
description: Découvrez comment créer une offre Azure Stack comprenant des plans et des quotas.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 083b5e20b89f22cb8e523926858fe9ffb1441319
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-offer-azure-stack-iaas-services"></a>Didacticiel : Offrir des services PaaS Azure Stack
En tant qu’un administrateur de cloud Azure Stack, vous pouvez créer des offres auxquelles vos utilisateurs (parfois appelée « locataires ») peuvent s’abonner. Avec leur abonnement, les utilisateurs peuvent utiliser les services Azure Stack.

Ce didacticiel vous montre comment créer une offre pour permettre aux utilisateurs de créer des machines virtuelles basées sur l’image Windows Server Datacenter 2016 de la Place de marché Azure Stack que vous avez chargée lors du [didacticiel précédent](asdk-marketplace-item.md).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une offre
> * Créer un plan
> * Définir les quotas
> * Définir l’offre comme une offre publique

Dans Azure Stack, les services sont fournis aux utilisateurs par le biais d’abonnements, d’offres et de plans. Les utilisateurs peuvent s’abonner à plusieurs offres. Les offres peuvent contenir un ou plusieurs plans, et les plans peuvent contenir un ou plusieurs services, comme le montre le diagramme suivant :

![Abonnements, offres et plans](media/asdk-offer-services/sop.png)

En tant qu’opérateur Azure Stack offrant des services, vous êtes d’abord invité à créer l’offre, puis un plan, et enfin des quotas. Après avoir créé une offre, les utilisateurs d’Azure Stack peuvent ensuite s’abonner aux offres via le portail de l’utilisateur.

## <a name="create-an-offer"></a>Créer une offre
Les **offres** sont des groupes d’un ou plusieurs plans que les opérateurs Azure Stack proposent à l’achat ou à l’abonnement aux utilisateurs.

1. Connectez-vous au [portail Azure Stack](https://adminportal.local.azurestack.external) en tant qu’administrateur de cloud.

2. Cliquez sur **Nouveau** > **Offres + Plans** > **Offre**.

   ![Nouvelle offre](media/asdk-offer-services/new-offer.png)

2. Dans la section **Nouvelle offre**, renseignez le **Nom d’affichage** et le **Nom de la ressource**, puis sélectionnez un **Groupe de ressources** nouveau ou existant. Le nom d’affichage correspond au nom convivial de l’offre que les locataires voient. Seul l’opérateur cloud peut voir le nom de la ressource, qui est utilisé par les administrateurs pour travailler avec une offre comme une ressource Azure Resource Manager.

   ![Nom complet](media/asdk-offer-services/offer-display-name.png)


## <a name="create-a-plan"></a>Créer un plan
Après avoir entré les informations de base de l’offre, vous devez ajouter au moins un plan de base à l’offre. 

Les **plans** permettent aux opérateurs Azure Stack de regrouper des services et leurs quotas associés pour les offrir aux utilisateurs.

1. Cliquez sur **Plans de base**, puis dans la section **Plan**, cliquez sur **Ajouter** pour ajouter un nouveau plan à l’offre.

   ![Ajouter un plan](media/asdk-offer-services/new-plan.png)

2. Dans la section **Nouveau plan**, renseignez le **Nom d’affichage** et le **Nom de la ressource**. Le nom d’affichage correspond au nom convivial du plan, que les locataires voient. Seul l’opérateur cloud peut voir le nom de la ressource, qui est utilisé par les opérateurs de cloud pour travailler avec le plan comme une ressource Azure Resource Manager.

   ![Nom d’affichage du plan](media/asdk-offer-services/plan-display-name.png)

3. Ensuite, sélectionnez les services à inclure dans le plan. Pour offrir des services IaaS, cliquez sur **Services**, sélectionnez **Microsoft.Compute**, **Microsoft.Network** et **Microsoft.Storage**, puis cliquez sur **Sélectionner**.

   ![Services du plan](media/asdk-offer-services/select-services.png)


## <a name="set-quotas"></a>Définir les quotas
Maintenant que l’offre dispose d’un plan qui inclut des services, vous devez définir des quotas pour chacun d’eux. 

Les **quotas** déterminent la quantité de ressources qu’un utilisateur peut consommer pour chaque service inclus dans le plan proposé.

1. Cliquez sur **Quotas**, puis sélectionnez le service pour lequel vous voulez créer un quota. 

   Pour commencer, créez un quota pour le service Calcul. Dans la liste d’espaces de noms, sélectionnez l’espace de noms **Microsoft.Compute**, puis cliquez sur **Créer un quota**.
   
   ![Créer un quota](media/asdk-offer-services/create-quota.png)

2. Dans la section **Créer un quota**, tapez un nom pour le quota, définissez les paramètres souhaités pour le quota, puis cliquez sur **OK**.

   ![Nom du quota](media/asdk-offer-services/quota-properties.png)

3. Sélectionnez le quota que vous avez créé pour le service **Microsoft.Compute**.

   ![Sélectionner un quota](media/asdk-offer-services/set-quota.png)

4. Répétez les étapes 1 à 3 pour les services Réseau et Stockage, puis cliquez sur OK dans la section Quotas. Ensuite, cliquez sur **OK** dans la section **Nouveau plan** pour terminer la configuration du plan. 

   ![Tous les quotas définis](media/asdk-offer-services/all-quotas-set.png)

5. Terminez la création de l’offre en cliquant sur **Créer** dans la section Plan. Une notification s’affiche lorsque l’offre est créée. Elle est alors répertoriée en tant qu’offre disponible.

   ![Offre créée](media/asdk-offer-services/offer-complete.png)

## <a name="set-offer-to-public"></a>Définir l’offre comme une offre publique
Les offres doivent être rendues publiques pour que les utilisateurs les voient lors de la sélection d’une offre à laquelle s’abonner. 

Les offres peuvent être :
- **Public** : visibles pour tous les utilisateurs.
- **Privé** : visibles uniquement par les administrateurs cloud. Utile lors de l’élaboration du plan ou de l’offre, ou si l’administrateur cloud souhaite créer chaque abonnement pour les utilisateurs.
- **Retiré**: ils sont fermés aux nouveaux abonnés. L’administrateur cloud peut utiliser cet état pour empêcher tout abonnement futur, sans que cela affecte les abonnés actuels.

> [!TIP]
> Les modifications apportées à l’offre ne sont pas immédiatement visibles par les utilisateurs. Pour voir les modifications et afficher la nouvelle offre, les utilisateurs pourraient devoir se déconnecter et se reconnecter au [portail de l’utilisateur](https://portal.local.azurestack.external).

Pour rendre la nouvelle offre publique : 

1. Dans le menu du tableau de bord, cliquez sur **Offres**, puis cliquez sur l’offre que vous avez créée.

2. Cliquez sur **Changer l’état**, puis sur **Public**.

   ![État Public](media/asdk-offer-services/set-public.png)

3. L’offre est désormais accessible dans le portail de l’utilisateur Azure Stack.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer une offre
> * Créer un plan
> * Définir les quotas
> * Définir l’offre comme une offre publique

Passez au didacticiel suivant pour savoir comment s’abonner à l’offre que vous venez de créer en tant qu’utilisateur d’Azure Stack.

> [!div class="nextstepaction"]
> [S’abonner à une offre](asdk-subscribe-services.md)