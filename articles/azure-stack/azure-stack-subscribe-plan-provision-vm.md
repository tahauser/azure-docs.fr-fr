---
title: "S’abonner à une offre dans Azure Stack | Microsoft Docs"
description: "Créer des abonnements pour des offres dans Azure Stack"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 9b35b497c264fab2b8352eda82fe6d4e1fc274e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Créer des abonnements pour des offres dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Après avoir [créé une offre](azure-stack-create-offer.md), les utilisateurs ont besoin d’un abonnement à cette offre pour pouvoir l’utiliser.   
- En tant qu’opérateur cloud, vous pouvez créer un abonnement pour un utilisateur à partir du portail d’administration.  Les abonnements créés peuvent être destinés à des offres publiques et privées.
- En tant qu’un utilisateur locataire, vous pouvez vous abonner à une offre publique lorsque vous utilisez le portail utilisateur.  

Les sections suivantes fournissent des conseils aux opérateurs cloud concernant la création d’abonnements pour les utilisateurs et l’abonnement à une offre en tant qu’utilisateur.

## <a name="create-a-subscription-as-a-cloud-operator"></a>Créer un abonnement en tant qu’opérateur cloud
Les opérateurs cloud peuvent utiliser le portail d’administration pour créer un abonnement à une offre pour un utilisateur.  Vous pouvez créer des abonnements pour les membres de votre propre locataire d’annuaire.  Lorsque la [mutualisation](azure-stack-enable-multitenancy.md) est activée, vous pouvez également créer des abonnements pour les utilisateurs dans des locataires d’annuaires supplémentaires.

Vous pouvez créer des abonnements destinés à des offres publiques et privées.  Si vous ne voulez pas que vos locataires puissent créer leurs propres abonnements, rendez toutes vos offres privées et créez ensuite des abonnements pour le compte de vos locataires. Cette approche est courante lors de l’intégration d’Azure Stack avec les systèmes externes de catalogue de service ou de facturation.

Après avoir créé un abonnement pour un utilisateur, cet utilisateur peut se connecter au portail utilisateur et constater qu’il est abonné à l’offre.  

### <a name="to-create-the-subscription-for-a-user"></a>Pour créer l’abonnement pour un utilisateur
1.  Dans le portail d’administration, accédez à **Abonnements utilisateur.**
2.  Sélectionnez **Ajouter** pour ouvrir le volet **Nouvel abonnement utilisateur**. Ici, vous spécifiez les détails suivants :  

  a. **Nom d’affichage** : un nom convivial pour identifier l’abonnement qui s’affiche en tant que *nom d’abonnement utilisateur*.

  b. **Utilisateur** : spécifiez un utilisateur d’un locataire d’annuaire disponible pour cet abonnement. Le nom d’utilisateur apparaît en tant que *Propriétaire*.  Le format du nom d’utilisateur dépend de votre solution d’identité. Par exemple :    

     -  **Azure AD:**  *&lt;user1>@&lt;contoso.onmicrosoft.com>*

     -   **AD FS:**  *&lt;user1>@&lt;azurestack.local>*     

  c.    **Locataire d’annuaire** : sélectionnez le locataire d’annuaire auquel appartient le compte d’utilisateur. Si vous n’avez pas activé la mutualisation, seul votre locataire d’annuaire local est disponible.

3.  Sélectionnez **Offre** pour ouvrir le volet **Offre**, puis choisissez une **Offre** pour cet abonnement. Étant donné que vous créez un abonnement pour un utilisateur, vous pouvez sélectionner une offre privée ou publique.

4.  Sélectionnez **Créer** pour créer l’abonnement. Le volet **Abonnements utilisateur** affiche maintenant le nouvel abonnement.  Plus tard, lorsque l’utilisateur se connecte au portail utilisateur, ils peuvent afficher les détails de l’abonnement.

### <a name="to-make-an-add-on-plan-available"></a>Pour rendre un plan additionnel disponible  
Un opérateur cloud peut ajouter à tout moment un plan additionnel à un abonnement créé précédemment :   
1.  Dans le portail d’administration, accédez à **Plus de services** > **Abonnements utilisateur**, puis sélectionnez l’abonnement que vous souhaitez modifier.   

2.  Sélectionnez **Modules complémentaires** > **Ajouter** pour ouvrir le volet **Ajouter un plan**.  

3.  Sélectionnez le plan que vous souhaitez ajouter en tant que module complémentaire.  La console affiche alors les plans associés à l’abonnement.




## <a name="create-a-subscription-as-a-user"></a>Créer un abonnement en tant qu’utilisateur
En tant qu’utilisateur, vous pouvez vous connecter au portail utilisateur pour trouver et vous abonner aux offres publiques et aux plans additionnels à partir de votre locataire d’annuaire (organisation). Lorsque l’environnement de Azure Stack prend en charge la [mutualisation](azure-stack-enable-multitenancy.md), vous pouvez vous abonner à des offres à partir d’un locataire d’annuaire distant.

### <a name="to-subscribe-to-an-offer"></a>S’abonner à une offre
1. [Connectez-vous](azure-stack-connect-azure-stack.md) au portail des utilisateurs Azure Stack (https://portal.local.azurestack.external) et cliquez sur **Obtenir un abonnement**.

   ![Prendre un abonnement](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. Dans le champ **Nom d’affichage**, saisissez un nom pour votre abonnement, cliquez sur **Offre**, cliquez sur l’une des offres du panneau **Choisir une offre**, puis sur **Créer**.

   ![Créer une offre](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. Pour afficher l’abonnement que vous avez créé, cliquez sur **Autres services**, sur **Abonnements**, puis sur votre nouvel abonnement.  

Une fois que vous êtes abonné à une offre, actualisez le portail pour voir les services qui font partie du nouvel abonnement.

### <a name="to-subscribe-to-an-add-on-plan"></a>S’abonner à un plan additionnel
Si une offre possède un plan additionnel, vous pouvez ajouter ce plan à votre abonnement à tout moment.  

1. Dans le portail utilisateur, sélectionnez **Plus de services** > **Abonnements** puis sélectionnez l’abonnement que vous souhaitez modifier.

2. Sélectionnez le bouton **Ajouter un plan**, puis sélectionnez le plan additionnel que vous désirez. Si **Ajouter un plan** n’est pas disponible, cela signifie qu’aucun plan additionnel n’existe pour l’offre associée à cet abonnement.



## <a name="next-steps"></a>Étapes suivantes
[Approvisionner une machine virtuelle](azure-stack-provision-vm.md)
