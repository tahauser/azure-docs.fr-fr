---
title: "Délégation des offres dans Azure Stack | Microsoft Docs"
description: "Découvrez comment placer d’autres personnes en charge de la création d’offres et de l’inscription des utilisateurs."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 157f0207-bddc-42e5-8351-197ec23f9d46
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: 287bc04660664facbe99d2cb80ae6c92e41c4111
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="delegate-offers-in-azure-stack"></a>Déléguer des offres dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

En tant qu’opérateur Azure Stack, vous souhaitez souvent placer d’autres personnes en charge de la création d’offres et de l’inscription des utilisateurs. Par exemple, si vous êtes un fournisseur de services et souhaitez que les revendeurs inscrivent les clients et les gèrent à votre place. Ou si vous faites partie d’un groupe informatique centralisé d’une entreprise, vous pouvez demander aux départements ou filiales d’inscrire les utilisateurs sans votre intervention.

La délégation vous aide dans ces tâches en permettant d’atteindre et de gérer plus d’utilisateurs que vous ne pouvez le faire directement. L’illustration suivante montre un niveau de délégation, mais Azure Stack prend en charge plusieurs niveaux. Les fournisseurs délégués peuvent à leur tour déléguer à d’autres fournisseurs, et ce sur jusqu’à cinq niveaux.

![Niveaux de délégation](media/azure-stack-delegated-provider/image1.png)

Les opérateurs Azure Stack peuvent déléguer la création d’offres et d’utilisateurs à d’autres utilisateurs à l’aide de la fonctionnalité de délégation.

## <a name="roles-and-steps-in-delegation"></a>Rôles et étapes de la délégation
Pour comprendre la délégation, gardez à l’esprit qu’elle comprend trois rôles :

* L’*opérateur cloud Azure Stack* gère l’infrastructure Azure Stack, crée un modèle d’offre et délègue à d’autres la responsabilité de proposer l’offre à leurs utilisateurs.

* Les opérateurs Azure Stack délégués sont appelés *fournisseurs délégués*. Ils peuvent appartenir à d’autres organisations (comme d’autres utilisateurs Azure Active Directory).

* Les *utilisateurs* s’inscrivent aux offres et les utilisent pour gérer leurs charges de travail, la création de machines virtuelles, le stockage des données, etc.

Comme indiqué dans l’illustration suivante, la configuration de la délégation inclut deux étapes.

1. *Identifier les fournisseurs délégués*. Pour cela, vous les abonnez à une offre basée sur un plan qui contient uniquement le service d’abonnements. Les utilisateurs qui s’abonnent à cette offre acquièrent certaines des fonctionnalités de l’opérateur Azure Stack, notamment la possibilité d’étendre les offres et d’y inscrire des utilisateurs.

2. *Déléguer une offre au fournisseur délégué*. Cette offre fonctionne comme un modèle concernant ce que le fournisseur délégué peut offrir. Le fournisseur délégué peut à présent accepter l’offre. Attribuez-lui un nom (mais ne modifiez pas ses services et quotas) et propose l’offre aux clients.

![Créer le fournisseur délégué et lui permettre d’inscrire des utilisateurs](media/azure-stack-delegated-provider/image2.png)

Pour agir en tant que fournisseurs délégués, les utilisateurs doivent établir une relation avec le fournisseur principal. En d’autres termes, ils doivent créer un abonnement. Dans ce scénario, cet abonnement identifie les fournisseurs délégués comme ayant le droit de présenter des offres pour le compte du fournisseur principal.

Une fois que cette relation est établie, l’opérateur Azure Stack peut déléguer une offre au fournisseur délégué. Le fournisseur délégué est désormais en mesure de prendre l’offre, de la renommer (sans en modifier la substance) et de la proposer à ses clients.

Les sections suivantes expliquent comment établir un fournisseur délégué, déléguer une offre et vérifier que les utilisateurs peuvent s’y inscrire.

## <a name="set-up-roles"></a>Configurer les rôles

Pour voir un fournisseur délégué au travail, vous avez besoin de comptes Azure AD supplémentaires en plus de votre compte d’opérateur Azure Stack. Si vous n’avez pas ces deux comptes, créez-les. Les comptes peuvent appartenir à n’importe quel utilisateur de Azure AD et désignent le fournisseur délégué l’utilisateur.

| **Rôle** | **Droits d’organisation** |
| --- | --- |
| Fournisseur délégué |Utilisateur |
| Utilisateur |Utilisateur |

## <a name="identify-the-delegated-providers"></a>Identifier les fournisseurs délégués
1. Connectez-vous en tant qu’opérateur Azure Stack.

2. Créez l’offre qui permet aux utilisateurs de devenir des fournisseurs délégués :
   
   a.  [Créer un plan](azure-stack-create-plan.md).
       Ce plan doit inclure uniquement le service d’abonnements. Cet article utilise un plan appelé **PlanForDelegation**.
   
   b.  [Créer une offre](azure-stack-create-offer.md) basée sur ce plan. Cet article utilise une offre appelée **OfferToDP**.
   
   c.  Une fois la création de l’offre terminée, ajoutez le fournisseur délégué en tant qu’abonné à cette offre. Pour cela, sélectionnez **Abonnements** > **Ajouter** > **Nouvel abonnement client**.
   
   ![Ajouter le fournisseur délégué en tant qu’abonné](media/azure-stack-delegated-provider/image3.png)

> [!NOTE]
> Comme avec toutes les offres Azure Stack, vous avez la possibilité de rendre l’offre publique et de laisser les utilisateurs y souscrire, ou de conserver l’offre comme privée et demander à l’opérateur Azure Stack de gérer l’inscription. Les fournisseurs délégués appartiennent généralement à un petit groupe. Vous souhaitez contrôler qui y est admis, donc le fait de conserver cette offre privée est logique dans la plupart des cas.
> 
> 

## <a name="azure-stack-operator-creates-the-delegated-offer"></a>L’opérateur Azure Stack crée l’offre déléguée

Vous avez désormais établi votre fournisseur délégué. L’étape suivante consiste à créer le plan et l’offre que vous allez déléguer et que vos clients utiliseront. Il est recommandé de définir cette offre telle que vous souhaitez que les clients la voient, car le fournisseur délégué ne sera pas en mesure d’en modifier les plans et les quotas.

1. En tant qu’opérateur Azure Stack, [créez un plan](azure-stack-create-plan.md) et [une offre](azure-stack-create-offer.md) basée sur celui-ci. Cet article utilise une offre appelée **DelegatedOffer**.
   
   > [!NOTE]
   > Cette offre ne doit pas nécessairement être publique. Si vous le voulez, vous pouvez la rendre publique. Mais dans la plupart des cas, vous souhaitez que seuls les fournisseurs délégués puissent y accéder. Une fois que vous déléguez une offre privée comme décrit dans les étapes suivantes, le fournisseur délégué y a accès.
   > 
   > 
1. Déléguez l’offre. Accédez à **DelegatedOffer.** Puis, dans le volet **Paramètres**, sélectionnez **Fournisseurs délégués** > **Ajouter**.

2. Sélectionnez l’abonnement du fournisseur délégué dans la zone de liste déroulante puis cliquez sur **Déléguer**.

> ![Ajouter un fournisseur délégué](media/azure-stack-delegated-provider/image4.png)
> 
> 

## <a name="delegated-provider-customizes-the-offer"></a>Le fournisseur délégué personnalise l’offre

Connectez-vous au portail utilisateur en tant que fournisseur délégué et créez une offre en utilisant l’offre déléguée comme modèle.

1. Sélectionnez **Nouveaux** > **Offres + Plans clients** > **Offre**.

    ![Créer une offre](media/azure-stack-delegated-provider/image5.png)


1. Attribuez un nom à l’offre. Cet article utilise **ResellerOffer**. Sélectionnez l’offre déléguée sur laquelle baser cette offre, puis cliquez sur **Créer**.
   
   ![Attribuer un nom](media/azure-stack-delegated-provider/image6.png)

    >[!NOTE] 
    > Notez la différence par rapport à la création d’offre telle qu’elle est vécue par l’opérateur Azure Stack. Le fournisseur délégué ne construit pas l’offre à partir de plans de base et de plans additionnels. Il peut uniquement choisir parmi des offres qui ont été déléguées, et il ne peut pas modifier ces offres.

1. Publiez l’offre en sélectionnant **Parcourir**, puis **Offres**. Sélectionnez l’offre, puis **Changer l’état**.

2. Le fournisseur délégué expose ces offres via l’URL de son propre portail. Ces offres sont visibles uniquement via le portail délégué. Pour rechercher et modifier cette URL :
   
    a.  Sélectionnez **Parcourir** > **Plus de services** > **Abonnements**. Sélectionnez ensuite l’abonnement du fournisseur délégué. Par exemple, **DPSubscription** > **Propriétés**.
   
    b.  Copiez l’URL du portail vers un autre emplacement, comme le Bloc-notes.
   
    ![Sélectionner l’abonnement du fournisseur délégué](media/azure-stack-delegated-provider/dpportaluri.png)  
   
   Vous avez maintenant créé une offre déléguée en tant que fournisseur délégué. Déconnectez-vous en tant que fournisseur délégué. Fermez la fenêtre de navigateur que vous avez utilisée.

## <a name="sign-up-for-the-offer"></a>S’inscrire à l’offre
1. Dans une nouvelle fenêtre de navigateur, accédez à l’URL du portail délégué que vous avez enregistrée à l’étape précédente. Connectez-vous au portail en tant qu’utilisateur. 
   
   >[!NOTE]
   >Les offres déléguées ne sont pas visibles, à moins d’utiliser le portail délégué. 

2. Dans le tableau de bord, sélectionnez **Prendre un abonnement**. Vous verrez que seules les offres déléguées créées par le fournisseur délégué sont présentées à l’utilisateur :

> ![Afficher et sélectionner des offres](media/azure-stack-delegated-provider/image8.png)
> 
> 

Le processus de délégation de l’offre est terminé. L’utilisateur peut désormais s’inscrire à cette offre en prenant un abonnement à celle-ci.

## <a name="multiple-tier-delegation"></a>Délégation à plusieurs niveaux

La délégation à plusieurs niveaux permet au fournisseur délégué de déléguer l’offre à d’autres entités. Cela permet, par exemple, la création de réseaux de revendeurs plus approfondis, dans lesquels le fournisseur qui gère Azure Stack délègue une offre à un distributeur. Ce distributeur délègue à son tour au revendeur. Azure Stack prend en charge jusqu’à cinq niveaux de délégation.

Pour créer plusieurs niveaux de délégation de l’offre, le fournisseur délégué délègue à son tour l’offre au fournisseur suivant. Le processus pour le fournisseur délégué est similaire à celui pour l’opérateur Azure Stack (consultez [L’opérateur Azure Stack crée l’offre déléguée](#cloud-operator-creates-the-delegated-offer)).

## <a name="next-steps"></a>étapes suivantes
[Approvisionner une machine virtuelle](azure-stack-provision-vm.md)

