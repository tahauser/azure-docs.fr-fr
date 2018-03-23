---
title: Signaler les données d’utilisation Azure Stack à Azure | Microsoft Docs
description: Découvrez comment configurer les la génération de rapports de données d’utilisation dans Azure Stack.
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
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 29d53f63bf3d551823ca27df04f0e385a92cdec7
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Signaler les données d’utilisation Azure Stack à Azure 

Les données d’utilisation, également appelées « données de consommation », représentent la quantité de ressources utilisées. 

Les systèmes à plusieurs nœuds Azure Stack qui utilisent un modèle de facturation basé sur la consommation doivent communiquer les données d’utilisation à Azure à des fins de facturation.  Les opérateurs Azure Stack doivent configurer leur instance Azure Stack pour communiquer les données d’utilisation à Azure.

> [!NOTE]
> Les rapports d’utilisation des données sont nécessaires pour les utilisateurs des systèmes intégrés Azure Stack qui attribuent des licences sous le modèle de paiement à l’utilisation. Ceci est facultatif pour les clients qui attribuent des licences sous le modèle de capacité (consultez la page [Comment acheter Azure Stack](https://azure.microsoft.com/overview/azure-stack/how-to-buy/ to learn more about pricing in Azure Stack)). Pour les utilisateurs du Kit de développement Azure Stack, les opérateurs Azure Stack peuvent communiquer des données d’utilisation et tester la fonctionnalité. Les utilisateurs ne sont cependant pas facturés pour l’utilisation qu’elles impliquent. 


![flux de facturation](media/azure-stack-usage-reporting/billing-flow.png)

Les données d’utilisation sont envoyées d’Azure Stack à Azure via le pont Azure. Dans Azure, le système de commerce traite les données d’utilisation et génère la facture. Une fois la facture générée, le propriétaire de l’abonnement Azure peut l’afficher et la télécharger à partir du [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions). Pour savoir comment Azure Stack est concédé sous licence, reportez-vous au [document d’empaquetage et de tarification d’Azure Stack](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409).

## <a name="set-up-usage-data-reporting"></a>Configurer la génération de rapports de données d’utilisation

Pour configurer la génération de rapports de données d’utilisation, vous devez [inscrire votre instance Azure Stack auprès d’Azure](azure-stack-register.md). Le composant Azure Bridge d’Azure Stack, qui connecte Azure Stack à Azure et envoie les données d’utilisation, est configuré dans le cadre du processus d’inscription. Les données d’utilisation suivantes sont envoyées d’Azure Stack à Azure :

- **ID du compteur** : ID unique de la ressource consommée.
- **Quantité** : quantité d’utilisation des ressources.
- **Emplacement** : emplacement où la ressource Azure Stack actuelle est déployée.
- **URI de ressource** : URI complet de la ressource pour laquelle l’utilisation est signalée.
- **ID d’abonnement** : ID d’abonnement de l’utilisateur Azure Stack. Il s’agit de l’abonnement (Azure Stack) local.
- **Heure** : heure de début et de fin des données d’utilisation. Il existe un décalage entre le moment où ces ressources sont consommées dans Azure Stack et celui où les données d’utilisation sont signalées au commerce. Azure Stack agrège les données d’utilisation toutes les 24 heures et le signalement de données d’utilisation au pipeline Commerce dans Azure prend plusieurs heures. Par conséquent, l’utilisation qui se produit peu de temps avant minuit peut apparaître dans Azure le jour suivant.

## <a name="generate-usage-data-reporting"></a>Générer des rapports de données d’utilisation

1. Pour tester la génération de rapports de données d’utilisation, créez quelques ressources dans Azure Stack. Par exemple, vous pouvez créer un [compte de stockage](azure-stack-provision-storage-account.md), une [machine virtuelle Windows Server](azure-stack-provision-vm.md) et une machine virtuelle Linux avec des références (SKU) de base et standard pour voir comment l’utilisation des cœurs est signalée. Les données d’utilisation de différents types de ressources sont signalées sous différents compteurs.

2. Laissez vos ressources s’exécuter pendant quelques heures. Les informations d’utilisation sont collectées environ une fois par heure. Après avoir été collectées, ces données sont transmises à Azure et traitées dans le système de commerce Azure. Ce processus peut prendre plusieurs heures.

## <a name="view-usage---csp-subscriptions"></a>Voir l’utilisation : abonnements CSP

Si vous avez inscrit votre service Azure Stack en utilisant un abonnement CSP, vous pouvez voir votre utilisation et votre facturation de la même façon que vous consultez votre consommation Azure. L’utilisation d’Azure Stack sera incluse dans votre facture et dans le fichier de réconciliation, disponible via [l’Espace partenaires](https://partnercenter.microsoft.com/partner/home). Le fichier de réconciliation est mis à jour tous les mois. Si vous avez besoin d’accéder aux informations récentes de l’utilisation d’Azure Stack, vous pouvez utiliser les API de l’Espace partenaires.

   ![Espace partenaires](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>Voir l’utilisation : abonnements Contrat Entreprise

Si vous avez inscrit votre service Azure Stack en utilisant un abonnement Contrat Entreprise, vous pouvez voir votre utilisation et votre facturation dans le [portail EA](https://ea.azure.com/). L’utilisation d’Azure Stack sera incluse dans les téléchargements avancés avec l’utilisation d’Azure, sous la section Rapports du portail EA. 

## <a name="view-usage--other-subscriptions"></a>Voir l’utilisation : autres abonnements

Si vous avez inscrit votre service Azure Stack en utilisant un autre type d’abonnement, par exemple un abonnement avec paiement à l’utilisation, vous pouvez voir l’utilisation et la facturation dans le Centre des comptes Azure. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions) en tant qu’administrateur de compte Azure, puis sélectionnez l’abonnement Azure que vous avez utilisé pour inscrire Azure Stack. Vous pouvez afficher les données d’utilisation Azure Stack et le montant facturé pour chacune des ressources utilisées, comme illustré dans l’image suivante :

   ![flux de facturation](media/azure-stack-usage-reporting/pricing-details.png)

Pour le Kit de développement Azure Stack, les ressources Azure Stack ne sont pas facturées. Par conséquent, le prix affiché est 0,00 $. Quand Azure Stack à plusieurs nœuds est mis à la disposition générale, vous pouvez voir le coût réel de chacune de ces ressources.

## <a name="which-azure-stack-deployments-are-charged"></a>Quels sont les déploiements Azure Stack facturés ?

L’utilisation de ressources est gratuite pour le Kit de développement Azure Stack. Par contre, pour les systèmes multinœuds Azure Stack, les machines virtuelles de charge de travail, les services de stockage et les App Services sont facturés.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Les utilisateurs sont-ils facturés pour les machines virtuelles d’infrastructure ?

Non. Les données d’utilisation pour certaines machines virtuelles du fournisseur de ressources Azure Stack sont communiquées à Azure, mais rien n’est facturé pour ces machines virtuelles ni pour les machines virtuelles créées lors du déploiement pour activer l’infrastructure Azure Stack.  

Les utilisateurs sont facturés seulement pour les machines virtuelles qui s’exécutent sous les abonnements du locataire. Toutes les charges de travail doivent être déployées sous les abonnements du locataire pour être conformes au contrat de licence d’Azure Stack.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>J’ai une licence Windows Server que je veux utiliser sur Azure Stack : comment procéder ?

L’utilisation des licences existantes évite la génération de compteurs d’utilisation. Les licences Windows Server existantes peuvent être utilisées dans Azure Stack, comme décrit dans la section « Using existing software with Azure Stack » du [Microsoft Azure Stack Licensing Guide](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409). Les clients doivent déployer leurs machines virtuelles Windows Server comme décrit dans la rubrique [Azure Hybrid Benefit pour Windows Server](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) pour pouvoir utiliser leurs licences existantes.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Quel abonnement est facturé pour la consommation de ressources ?
L’abonnement qui est fourni lors de [l’inscription d’Azure Stack auprès d’Azure](azure-stack-register.md) est facturé.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Quels types d’abonnements sont pris en charge pour la génération de rapports de données d’utilisation ?

Pour les systèmes multinœuds Azure Stack, les abonnements Contrat Entreprise et CSP sont pris en charge. Pour le Kit de développement Azure Stack, les abonnements Accord Entreprise, Paiement à l’utilisation, CSP et MSDN prennent en charge les rapports de données d’utilisation.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>La génération de rapports de données d’utilisation fonctionne-t-elle avec les clouds souverains ?

Dans le Kit de développement Azure Stack, la génération de rapports d’utilisation de données exige que les abonnements soient créés dans le système Azure global. Les abonnements créés dans l’un des clouds souverains (clouds Azure Government, Azure Germany et Azure China) ne peuvent pas être inscrits auprès d’Azure. Ils ne prennent donc pas en charge les rapports de données d’utilisation.

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>Comment les utilisateurs peuvent-ils identifier les données d’utilisation Azure Stack dans le portail de facturation Azure ?

Les utilisateurs peuvent voir les données d’utilisation Azure Stack dans le fichier des détails d’utilisation. Pour savoir comment obtenir le fichier des détails d’utilisation, reportez-vous à l’article [Télécharger l’utilisation à partir du Centre des comptes Azure (.csv)](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv). Le fichier des détails d’utilisation contient les compteurs Azure Stack qui identifient le stockage et les machines virtuelles Azure Stack. Toutes les ressources utilisées dans Azure Stack sont signalées dans la zone nommée « Azure Stack ».

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Pourquoi l’utilisation signalée dans Azure Stack ne correspond-il pas au rapport généré à partir du Centre des comptes Azure ?

Il existe toujours un délai entre le moment où les données d’utilisation sont communiquées par les API d’utilisation d’Azure Stack et celui où elles sont communiquées par le Centre des comptes Azure. Ce décalage est le temps nécessaire pour charger les données d’utilisation d’Azure Stack vers le commerce Azure. En raison de ce décalage, l’utilisation qui se produit peu de temps avant minuit peut apparaître dans Azure le jour suivant. Si vous utilisez les [API d’utilisation d’Azure Stack](azure-stack-provider-resource-api.md) et que vous comparez les résultats à l’utilisation indiquée dans le portail de facturation Azure, vous pouvez voir une différence.

## <a name="next-steps"></a>Étapes suivantes

* [API d’utilisation du fournisseur](azure-stack-provider-resource-api.md)  
* [API d’utilisation du locataire](azure-stack-tenant-resource-usage-api.md)
* [Forum Aux Questions sur l’utilisation](azure-stack-usage-related-faq.md)
* [Gérer l’utilisation et la facturation en tant que fournisseur de services cloud](azure-stack-add-manage-billing-as-a-csp.md)
