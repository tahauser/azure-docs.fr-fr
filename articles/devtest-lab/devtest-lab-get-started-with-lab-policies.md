---
title: "Gérer les stratégies de laboratoire de base dans Azure DevTest Labs | Microsoft Docs"
description: "Découvrez comment définir certaines des stratégies de base (paramètres) pour un laboratoire dans DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: v-craic
ms.openlocfilehash: 3f5fc5051bb8acd9ae107eeadc2a00e992eb378c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Gérer les stratégies de laboratoire de base dans Azure DevTest Labs

Azure DevTest Labs vous permet de contrôler les coûts et de réduire le gaspillage dans vos laboratoires en gérant les stratégies (paramètres) de chacun d’entre eux. Dans cet article, vous allez découvrir les stratégies en apprenant à définir deux d’entre elles parmi les plus critiques : la limitation du nombre de machines virtuelles pouvant être créées ou revendiquées par un seul utilisateur et la configuration de l’arrêt automatique. Pour savoir comment définir chaque stratégie de laboratoire, consultez [Définir des stratégies de laboratoire dans Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Accès aux stratégies d’un laboratoire dans Azure DevTest Labs
Les étapes suivantes vous guident lors de la configuration des stratégies pour un laboratoire dans Azure DevTest Labs :

Pour afficher (et modifier) les stratégies d’un laboratoire, procédez comme suit :

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.

1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.   

1. Sélectionnez **Configuration et stratégies**.

    ![Volet Paramètres de stratégie](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. Le volet **Configuration et stratégies** contient un menu de paramètres que vous pouvez spécifier. Cet article traite uniquement des paramètres concernant les **machines virtuelles par utilisateur**, **l’arrêt automatique** et le **démarrage automatique**. Pour en savoir plus sur les autres paramètres, consultez [Gérer toutes les stratégies de laboratoire dans Azure DevTest Labs](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Définir les machines virtuelles par utilisateur
La stratégie **Machines virtuelles par utilisateur** vous permet de spécifier le nombre maximal de machines virtuelles pouvant être créées par un utilisateur individuel. Si un utilisateur tente de créer ou de revendiquer une machine virtuelle alors que le nombre limite par utilisateur est atteint, un message d’erreur indique que la machine virtuelle ne peut pas être créée/revendiquée. 

1. Dans le menu **Configuration et stratégies** du laboratoire, sélectionnez **Machines virtuelles par utilisateur**.
   
    ![Machines virtuelles par utilisateur](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Sélectionnez **Oui** pour limiter le nombre de machines virtuelles par utilisateur. Si vous ne souhaitez pas limiter le nombre de machines virtuelles par utilisateur, sélectionnez **Non**. Si vous sélectionnez **Oui**, entrez une valeur numérique indiquant le nombre maximal de machines virtuelles qu’un utilisateur peut créer ou revendiquer. 

1. Sélectionnez **Oui** pour limiter le nombre de machines virtuelles pouvant utiliser un disque SSD. Si vous ne souhaitez pas limiter le nombre de machines virtuelles pouvant utiliser un disque SSD, sélectionnez **Non**. Si vous sélectionnez **Oui**, entrez une valeur indiquant le nombre maximal de machines virtuelles qu’un utilisateur peut créer à l’aide d’un disque SSD. 

1. Sélectionnez **Enregistrer**.

## <a name="set-auto-shutdown"></a>Définir l’arrêt automatique
La stratégie d’arrêt automatique vous permet d’indiquer l’heure à laquelle les machines virtuelles du laboratoire doivent s’arrêter et contribue ainsi à réduire les pertes de laboratoire.

1. Dans le volet **Configuration et stratégies** du lab, sélectionnez **Arrêt automatique**.
   
    ![Arrêt automatique](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Sélectionnez **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.

1. Si vous activez cette stratégie, spécifiez l’heure (et le fuseau horaire) de l’arrêt pour toutes les machines virtuelles du laboratoire actif.

1. Spécifiez **Oui** ou **Non** pour l’option d’envoi de notification 15 minutes avant l’heure d’arrêt automatique spécifiée. Si vous choisissez **Oui**, saisissez un point de terminaison de l’URL de Webhook ou une adresse e-mail spécifiant où vous désirez publier ou envoyer la notification. L’utilisateur reçoit une notification et peut retarder l’arrêt.

   Pour plus d’informations sur les webhooks, consultez [Créer une fonction Azure d’API ou de webhook](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Sélectionnez **Enregistrer**.

Par défaut, une fois activée, cette stratégie s’applique à toutes les machines virtuelles dans le laboratoire en cours. Pour supprimer ce paramètre sur une machine virtuelle spécifique, ouvrez le volet de gestion de la machine virtuelle et modifiez son paramètre **Arrêt automatique**.

## <a name="set-auto-start"></a>Définir le démarrage automatique
La stratégie de démarrage automatique vous permet de spécifier quand les machines virtuelles du laboratoire doivent être démarrées.  

1. Dans le volet **Configuration et stratégies** du lab, sélectionnez **Démarrage automatique**.
   
    ![Démarrage automatique](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Sélectionnez **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.

3. Si vous activez cette stratégie, spécifiez l’heure de démarrage programmée, le fuseau horaire et les jours de la semaine auxquels cette heure s’applique. 

4. Sélectionnez **Enregistrer**.

Une fois activée, cette stratégie n’est pas automatiquement appliquée à toutes les machines virtuelles dans le laboratoire en cours. Pour appliquer ce paramètre à une machine virtuelle existante, ouvrez le volet de gestion de la machine virtuelle et modifiez son paramètre **Démarrage automatique**.

## <a name="next-steps"></a>Étapes suivantes

- [Définir des stratégies de laboratoire dans Azure DevTest Labs](devtest-lab-set-lab-policy.md) - Apprenez à modifier les autres stratégies de laboratoire.
