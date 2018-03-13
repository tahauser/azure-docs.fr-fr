---
title: "Gérer les stratégies de laboratoire dans Azure DevTest Labs | Microsoft Docs"
description: "Apprenez à définir des stratégies de laboratoire telles que les tailles de machine virtuelle, le nombre maximal de machines virtuelles par utilisateur et l’arrêt automatique."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: v-craic
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c2b71fa5ec2935a25b5fb37770dfb5163a286ded
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Gérer toutes les stratégies d’un laboratoire dans Azure DevTest Labs

Azure DevTest Labs vous permet de contrôler les coûts et de réduire le gaspillage dans vos laboratoires en gérant les stratégies (paramètres) de chacun d’entre eux. Cet article décrit étape par étape comment définir chaque stratégie.  

## <a name="set-allowed-virtual-machine-sizes"></a>Définir les tailles de machine virtuelle autorisées
La stratégie pour définir les tailles de machine virtuelle autorisées vous permet de spécifier les tailles de machine virtuelle autorisées dans le laboratoire et contribue ainsi à réduire les pertes de laboratoire. Si cette stratégie est activée, seules les tailles de machine virtuelle de cette liste peuvent être utilisées pour créer des machines virtuelles.

1. Dans le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), sélectionnez un lab, puis **Configuration and policies** (Configuration et stratégies).

    ![Accéder à la configuration et aux stratégies du lab](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. Dans le volet **Configuration et stratégies** du lab, sélectionnez **Tailles de machine virtuelle autorisées**.
   
    ![Tailles de machine virtuelle autorisées](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Sélectionnez **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.

1. Si vous activez cette stratégie, sélectionnez une ou plusieurs tailles de machine virtuelle pouvant être créées dans votre laboratoire.

1. Sélectionnez **Enregistrer**.

## <a name="set-virtual-machines-per-user"></a>Définir les machines virtuelles par utilisateur
La stratégie **Machines virtuelles par utilisateur** vous permet de spécifier le nombre maximal de machines virtuelles pouvant être créées par un utilisateur individuel. Si un utilisateur tente de créer ou de revendiquer une machine virtuelle alors que le nombre limite par utilisateur est atteint, un message d’erreur indique que la machine virtuelle ne peut pas être créée/revendiquée. 

1. Dans le volet **Configuration et stratégies** du lab, sélectionnez **Machines virtuelles par utilisateur**.
   
    ![Machines virtuelles par utilisateur](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Sélectionnez **Oui** pour limiter le nombre de machines virtuelles par utilisateur. Si vous ne souhaitez pas limiter le nombre de machines virtuelles par utilisateur, sélectionnez **Non**. Si vous sélectionnez **Oui**, entrez une valeur numérique indiquant le nombre maximal de machines virtuelles qu’un utilisateur peut créer ou revendiquer. 

1. Sélectionnez **Oui** pour limiter le nombre de machines virtuelles pouvant utiliser un disque SSD. Si vous ne souhaitez pas limiter le nombre de machines virtuelles pouvant utiliser un disque SSD, sélectionnez **Non**. Si vous sélectionnez **Oui**, entrez une valeur indiquant le nombre maximal de machines virtuelles qu’un utilisateur peut créer à l’aide d’un disque SSD. 

1. Sélectionnez **Enregistrer**.

## <a name="set-virtual-machines-per-lab"></a>Définir les machines virtuelles par laboratoire
La stratégie **Machines virtuelles par lab** vous permet de spécifier le nombre maximal de machines virtuelles pouvant être créées pour le lab actuel. Si un utilisateur tente de créer une machine virtuelle alors que le nombre limite par laboratoire est atteint, un message d’erreur indique que la machine virtuelle ne peut pas être créée. 

1. Dans le volet **Configuration et stratégies** du lab, sélectionnez **Machines virtuelles par lab**.
   
    ![Machines virtuelles par laboratoire](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Sélectionnez **Oui** pour limiter le nombre de machines virtuelles par laboratoire. Si vous ne souhaitez pas limiter le nombre de machines virtuelles par laboratoire, sélectionnez **Non**. Si vous sélectionnez **Oui**, entrez une valeur numérique indiquant le nombre maximal de machines virtuelles qu’un utilisateur peut créer ou revendiquer. 

1. Sélectionnez **Oui** pour limiter le nombre de machines virtuelles pouvant utiliser un disque SSD. Si vous ne souhaitez pas limiter le nombre de machines virtuelles pouvant utiliser un disque SSD, sélectionnez **Non**. Si vous sélectionnez **Oui**, entrez une valeur indiquant le nombre maximal de machines virtuelles qu’un utilisateur peut créer à l’aide d’un disque SSD. 

1. Sélectionnez **Enregistrer**.

## <a name="set-auto-shutdown"></a>Définir l’arrêt automatique
La stratégie d’arrêt automatique vous permet d’indiquer l’heure à laquelle les machines virtuelles du lab doivent s’arrêter et contribue ainsi à réduire les pertes du lab.

1. Dans le volet **Configuration et stratégies** du lab, sélectionnez **Arrêt automatique**.
   
    ![Arrêt automatique](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Sélectionnez **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.

1. Si vous activez cette stratégie, spécifiez l’heure (et le fuseau horaire) de l’arrêt pour toutes les machines virtuelles du laboratoire actif.

1. Spécifiez **Oui** ou **Non** pour l’option d’envoi de notification 15 minutes avant l’heure d’arrêt automatique spécifiée. Si vous choisissez **Oui**, saisissez un point de terminaison de l’URL de Webhook ou une adresse e-mail spécifiant où vous désirez publier ou envoyer la notification. L’utilisateur reçoit une notification et peut retarder l’arrêt.

   Pour plus d’informations sur les webhooks, consultez [Créer une fonction Azure d’API ou de webhook](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Sélectionnez **Enregistrer**.

Par défaut, une fois activée, cette stratégie s’applique à toutes les machines virtuelles dans le laboratoire en cours. Pour supprimer ce paramètre sur une machine virtuelle spécifique, ouvrez le volet de gestion de la machine virtuelle et modifiez son paramètre **Arrêt automatique**.

## <a name="set-auto-start"></a>Définir le démarrage automatique
La stratégie de démarrage automatique vous permet de spécifier quand les machines virtuelles du lab actuel doivent être démarrées.  

1. Dans le volet **Configuration et stratégies** du lab, sélectionnez **Démarrage automatique**.
   
    ![Démarrage automatique](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Sélectionnez **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.

3. Si vous activez cette stratégie, spécifiez l’heure de démarrage programmée, le fuseau horaire et les jours de la semaine auxquels cette heure s’applique. 

4. Sélectionnez **Enregistrer**.

Une fois activée, cette stratégie n’est pas automatiquement appliquée à toutes les machines virtuelles dans le laboratoire en cours. Pour appliquer ce paramètre à une machine virtuelle spécifique, ouvrez le volet de gestion de la machine virtuelle et modifiez son paramètre **Démarrage automatique**.

## <a name="set-expiration-date"></a>Définir une date d’expiration
Lorsque vous [créez la machine virtuelle](devtest-lab-add-vm.md), vous pouvez définir une date d’expiration. Dans **Paramètres avancés**, choisissez l’icône de calendrier pour spécifier la date à laquelle la machine virtuelle est automatiquement supprimée. Par défaut, la machine virtuelle n’arrive jamais à expiration.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez défini et appliqué les différents paramètres de stratégies des machines virtuelles pour votre laboratoire, voici ce que vous pouvez essayer de faire :

* [Comprendre les adresses IP partagées](devtest-lab-shared-ip.md) : explique comment les adresses IP partagées sont utilisées dans DevTest Labs pour réduire le nombre d’adresses IP publiques requises pour se connecter aux machines virtuelles de votre laboratoire.
* [Configurer la gestion des coûts](devtest-lab-configure-cost-management.md) : montre comment utiliser le graphique **Tendance des coûts mensuels estimés**  
  pour afficher le coût estimé à ce jour pour le mois en cours et le coût projeté pour la fin du mois.
* [Créer une image personnalisée](devtest-lab-create-template.md) : quand vous créez une machine virtuelle, vous spécifiez une base, qui peut être soit une image personnalisée, soit une image Marketplace. Cet article explique comment créer une image personnalisée à partir d’un fichier VHD.
* [Configurer des images Marketplace](devtest-lab-configure-marketplace-images.md) : Azure DevTest Labs prend en charge la création de machines virtuelles basées sur des images Azure Marketplace. Cet article explique comment spécifier, le cas échéant, les images Azure Marketplace pouvant être utilisées lors de la création de machines virtuelles dans un laboratoire.
* [Créer une machine virtuelle dans un laboratoire](devtest-lab-add-vm.md) : montre comment créer une machine virtuelle à partir d’une image de base (personnalisée ou Place de marché) et comment utiliser des artefacts dans votre machine virtuelle.

