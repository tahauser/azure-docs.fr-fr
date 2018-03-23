---
title: Gérer l’utilisation et la facturation pour Azure Stack comme fournisseur de services cloud | Microsoft Docs
description: Procédure pas à pas d’inscription d’Azure Stack comme fournisseur cloud et d’ajout de clients.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 23e3a675e6a464c92d26df220c8126c970f590a0
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Gérer l’utilisation et la facturation pour Azure Stack comme fournisseur de services cloud 

*S’applique à : systèmes intégrés Azure Stack*

Cet article vous guide tout au long des procédures d’inscription d’Azure Stack comme fournisseur de services cloud (CSP) et d’ajout de clients.

En tant que CSP, vous êtes susceptible d’avoir de nombreux clients différents qui utilisent Azure Stack. Chaque client dispose d’un abonnement CSP dans Azure, et vous devez diriger l’utilisation de votre déploiement Azure Stack vers l’abonnement de chaque utilisateur.

Le diagramme suivant illustre les étapes nécessaires pour choisir votre compte de services partagés et inscrire un compte Azure auprès de ce compte. Une fois cette opération effectuée, vous pouvez intégrer vos clients finaux.

**Procédure d’ajout de suivi de l’utilisation en tant que fournisseur de services cloud (CSP)**

![Processus d’activation de l’utilisation et de la gestion en tant que fournisseur de services cloud.](media\azure-stack-add-manage-billing-as-a-csp\process-add-useage-as-a-csp.png)

## <a name="create-a-csp-or-cspss-subscription"></a>Créer un abonnement CSP ou CSPSS

### <a name="cloud-service-provider-subscription-types"></a>Types d’abonnements de fournisseur de services cloud

Vous devez choisir le type de compte de services partagés que vous utilisez pour Azure Stack. Les types d’abonnements pouvant être utilisés pour l’inscription d’un système Azure Stack multi-locataire sont les suivants :

 - Fournisseur de services cloud 
 - Abonnement Partner Shared Services 

#### <a name="csp-shared-services"></a>CSP Shared Services

Les abonnements CSPSS (Cloud Service Provider Shared Services) sont le choix privilégié pour l’inscription quand un serveur de distribution CSP ou CSP direct exploite Azure Stack.

Les abonnements CSPSS sont associés à un locataire de services partagés. Quand vous inscrivez Azure Stack, vous devez fournir les informations d’identification d’un compte qui est propriétaire de l’abonnement. Le compte que vous utilisez pour inscrire Azure Stack peut être différent du compte administrateur que vous utilisez pour le déploiement ; les deux ne doivent *pas* nécessairement appartenir au même domaine. En d’autres termes, vous pouvez effectuer le déploiement à l’aide du locataire que vous utilisez déjà. Par exemple, vous pouvez utiliser ContosoCSP.onmicrosoft.com, puis effectuer l’inscription à l’aide d’un autre locataire, par exemple IURContosoCSP.onmicrosoft.com. Vous devrez vous rappeler de vous connecter à l’aide de ContosoCSP.onmicrosoft.com quand vous effectuerez l’administration quotidienne d’Azure Stack. Quand vous vous connectez à Azure, utilisez IURContosoCSP.onmicrosoft.com quand vous devez effectuer des opérations d’inscription.

Pour obtenir une description des abonnements CSPSS, ainsi que des instructions sur la création d’un abonnement, reportez-vous à la rubrique suivante : [Ajouter Azure Partner Shared Services](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Abonnements CSP

Les abonnements de fournisseur de services cloud (CSP) sont le choix privilégié pour l’inscription quand un revendeur CSP ou un client final exploite Azure Stack.

## <a name="register-azure-stack"></a>Inscrire Azure Stack

Pour s’inscrire auprès d’Azure Stack, consultez [Inscrire Azure Stack auprès de votre abonnement Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Ajouter un client final

Pour configurer Azure Stack de sorte que quand un nouveau locataire utilise des ressources, leur utilisation soit signalée à son abonnement de fournisseur de services cloud (CSP), consultez [Ajouter un locataire pour l’utilisation et la facturation à Azure Stack](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Facturer les abonnements appropriés

Azure Stack utilise une fonctionnalité appelée « inscription ». Une inscription est un objet, stocké dans Azure, qui documente les abonnements Azure à utiliser pour facturer un système Azure Stack donné. Cette section traite de l’importance de l’inscription.

Grâce à l’inscription, Azure Stack peut :
 - transférer les données d’utilisation d’Azure Stack à Azure Commerce et facturer un abonnement Azure ;
 - signaler l’utilisation de chaque client sur un autre abonnement avec un déploiement Azure Stack multi-locataire. L’architecture multi-locataire permet à Azure Stack de prendre en charge différentes organisations sur la même instance d’Azure Stack.

Pour chaque déploiement Azure Stack, il existe un seul abonnement par défaut et autant d’abonnements de locataires que nécessaire. L’abonnement par défaut est un abonnement Azure qui est facturé s’il n’existe aucun abonnement spécifique au locataire. Il doit être le premier à être inscrit. Pour que la génération de rapports sur l’utilisation de plusieurs locataires fonctionne, l’abonnement doit être un abonnement CSP ou CSPSS.

L’inscription est alors mise à jour avec un abonnement Azure pour chaque locataire qui va utiliser Azure Stack. Les abonnements de locataires doivent être de type CSP et doivent être associés au partenaire qui possède l’abonnement par défaut. En d’autres termes, vous ne pouvez pas inscrire les clients d’une autre personne.

Quand Azure Stack transfère des informations d’utilisation à Azure global, un service d’Azure consulte l’inscription et mappe l’utilisation de chaque locataire à l’abonnement de locataire approprié. Si un locataire n’a pas été inscrit, cette utilisation est placée dans l’abonnement par défaut de l’instance Azure Stack dont elle provient.

Étant donné que les abonnements de locataire sont des abonnements CSP, leur facture est envoyée au partenaire CSP et les informations d’utilisation ne sont pas visibles par le client final.



## <a name="next-steps"></a>Étapes suivantes

 - Pour en savoir plus sur le programme CSP, consultez [Programme des fournisseurs de solutions cloud](https://partnercenter.microsoft.com/en-us/partner/programs).
 - Pour en savoir plus sur la récupération d’informations d’utilisation de ressources à partir d’Azure Stack, consultez [Utilisation et facturation dans Azure Stack](/azure-stack-billing-and-chargeback.md).
