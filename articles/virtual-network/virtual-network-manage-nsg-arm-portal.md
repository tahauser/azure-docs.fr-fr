---
title: "Gérer les groupes de sécurité réseau à partir du portail Azure | Microsoft Docs"
description: "Apprenez à gérer les groupes de sécurité réseau existants à partir du portail Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5d55679d-57da-457c-97dc-1e1973909ee5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.openlocfilehash: e9bcf8a893ff209337f6a5763b631a22f8514e20
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="manage-nsgs-using-the-portal"></a>Gérer les groupes de sécurité réseau à partir du portail

> [!div class="op_single_selector"]
> * [Portail](virtual-network-manage-nsg-arm-portal.md)
> * [PowerShell](virtual-network-manage-nsg-arm-ps.md)
> * [interface de ligne de commande Azure](virtual-network-manage-nsg-arm-cli.md)
>

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement Resource Manager que Microsoft recommande pour la plupart des nouveaux déploiements à la place du modèle de déploiement classique.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Récupérer des informations
Vous pouvez afficher vos groupes de sécurité réseau existants, récupérer des règles pour un groupe de sécurité réseau existant et découvrir quelles sont les ressources associées à un groupe de sécurité réseau.

### <a name="view-existing-nsgs"></a>Afficher les groupes de sécurité réseau existants

Pour afficher tous les groupes de sécurité réseau existants d’un abonnement, procédez comme suit :

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.

2. Cliquez sur **Parcourir >** > **Groupes de sécurité réseau**.

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Consultez la liste des groupes de sécurité réseau dans le panneau **Groupes de sécurité réseau** .

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

### <a name="view-nsgs-in-a-resource-group"></a>Afficher les groupes de sécurité réseau d’un groupe de ressources

Pour afficher la liste des groupes de sécurité réseau dans le groupe de ressources **RG-NSG**, procédez comme suit :

1. Cliquez sur **Groupes de ressources >** > **RG-NSG** > **...**.

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. Dans la liste des ressources, recherchez des éléments qui affichent l’icône du groupe de sécurité réseau, comme indiqué dans le panneau **Ressources** ci-dessous.

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure4.png)

### <a name="list-all-rules-for-an-nsg"></a>Répertorier toutes les règles pour un groupe de sécurité réseau

Pour afficher les règles d’un groupe de sécurité réseau nommé **NSG-FrontEnd**, procédez comme suit :

1. Dans le panneau **Groupes de sécurité réseau** ou le panneau **Ressources** présenté ci-dessus, cliquez sur **NSG-FrontEnd**.

2. Sous l’onglet **Paramètres**, cliquez sur **Règles de sécurité de trafic entrant**.

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. Le panneau **Règles de sécurité de trafic entrant** s’affiche comme illustré ci-dessous.

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. Sous l’onglet **Paramètres**, cliquez sur **Règles de sécurité de trafic sortant** pour afficher les règles de trafic sortant.

    > [!NOTE]
    > Pour afficher les règles par défaut, cliquez sur l’icône **Règles par défaut** en haut du panneau qui affiche les règles.
    >

### <a name="view-nsgs-associations"></a>Afficher les associations de groupes de sécurité réseau

Pour afficher les ressources auxquelles le groupe de sécurité réseau **NSG-FrontEnd** est associé, procédez comme suit :

1. Dans le panneau **Groupes de sécurité réseau** ou le panneau **Ressources** présenté ci-dessus, cliquez sur **NSG-FrontEnd**.

2. Sous l’onglet **Paramètres**, cliquez sur **Sous-réseaux** pour afficher les sous-réseaux associés au groupe de sécurité réseau.

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. Sous l’onglet **Paramètres**, cliquez sur **Interfaces réseau** pour afficher les cartes réseau associées au groupe de sécurité réseau.

## <a name="manage-rules"></a>Gérer les règles
Vous pouvez ajouter des règles à un groupe de sécurité réseau existant, modifier des règles existantes et supprimer des règles.

### <a name="add-a-rule"></a>Ajouter une règle
Pour ajouter une règle autorisant le trafic **entrant** sur le port **443** d’une machine vers le groupe de sécurité réseau **NSG-FrontEnd**, procédez comme suit :

1. Dans le panneau **Groupes de sécurité réseau** ou le panneau **Ressources** présenté ci-dessus, cliquez sur **NSG-FrontEnd**.
2. Sous l’onglet **Paramètres**, cliquez sur **Règles de sécurité de trafic entrant**.
3. Dans le panneau **Règles de sécurité de trafic entrant**, cliquez sur **Ajouter**. Ensuite, dans le panneau **Ajouter une règle de sécurité de trafic entrant**, fournissez les valeurs indiquées ci-dessous, puis cliquez sur **OK**.

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

    Après quelques secondes, notez la nouvelle règle dans le panneau **Règles de sécurité de trafic entrant** .

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Modifier une règle
Pour modifier la règle créée précédemment pour autoriser le trafic entrant en provenance d’**Internet** uniquement, procédez comme suit :

1. Dans le panneau **Groupes de sécurité réseau** ou le panneau **Ressources** présenté ci-dessus, cliquez sur **NSG-FrontEnd**.
2. Sous l’onglet **Paramètres** , cliquez sur la règle créée précédemment.
3. Dans le panneau **allow-https**, modifiez la propriété **Source** comme indiqué ci-dessous, puis cliquez sur **Enregistrer**.

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Supprimer une règle

Pour supprimer la règle créée précédemment, procédez comme suit :

1. Dans le panneau **Groupes de sécurité réseau** ou le panneau **Ressources** présenté ci-dessus, cliquez sur **NSG-FrontEnd**.
2. Sous l’onglet **Paramètres** , cliquez sur la règle créée précédemment.
3. Dans le panneau **allow-https**, cliquez sur **Supprimer**, puis sur **Oui**.

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Gérer les associations
Vous pouvez associer un groupe de sécurité réseau à des cartes réseau et des sous-réseaux. Vous pouvez également dissocier un groupe de sécurité réseau de n’importe quelle ressource à laquelle il est associé.

### <a name="associate-an-nsg-to-a-nic"></a>Associer un groupe de sécurité réseau à une carte réseau
Pour associer le groupe de sécurité réseau **NSG-FrontEnd** à la carte réseau **TestNICWeb1**, procédez comme suit :

1. Dans le panneau **Groupes de sécurité réseau** ou le panneau **Ressources** présenté ci-dessus, cliquez sur **NSG-FrontEnd**.
2. Sous l’onglet **Paramètres**, cliquez sur **Interfaces réseau** > **Associer** > **TestNICWeb1**.

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>Dissocier un groupe de sécurité réseau d’une carte réseau

Pour dissocier le groupe de sécurité réseau **NSG-FrontEnd** de la carte réseau **TestNICWeb1**, procédez comme suit :

1. Dans le portail Azure, cliquez sur **Groupes de ressources >** > **RG-NSG** > **...** > **TestNICWeb1**.

2. Dans le panneau **TestNICWeb1**, cliquez sur **Modifier la sécurité...** > **Aucune**.

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

> [!NOTE]
> Vous pouvez également utiliser ce panneau pour associer la carte réseau à n’importe quel groupe de sécurité réseau existant.
>

### <a name="dissociate-an-nsg-from-a-subnet"></a>Dissocier un groupe de sécurité réseau d’un sous-réseau

Pour dissocier le groupe de sécurité réseau **NSG-FrontEnd** du sous-réseau **FrontEnd**, procédez comme suit :

1. Dans le portail Azure, cliquez sur **Groupes de ressources >** > **RG-NSG** > **...** > **TestVNet**.

2. Dans le panneau **Paramètres**, cliquez sur **Sous-réseaux** > **FrontEnd** > **Groupe de sécurité réseau** > **Aucun**.

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. Dans le panneau **FrontEnd**, cliquez sur **Enregistrer**.

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Association d’un groupe de sécurité réseau à un sous-réseau

Pour associer à nouveau le groupe de sécurité réseau **NSG-FrontEnd** au sous-réseau **FrontEnd**, procédez comme suit :

1. Dans le portail Azure, cliquez sur **Groupes de ressources >** > **RG-NSG** > **...** > **TestVNet**.
2. Dans le panneau **Paramètres**, cliquez sur **Sous-réseaux** > **FrontEnd** > **Groupe de sécurité réseau** > **NSG-FrontEnd**.
3. Dans le panneau **FrontEnd**, cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez également associer un groupe de sécurité réseau à un sous-réseau à partir du panneau **Paramètres** du groupe de sécurité réseau.
>

## <a name="delete-an-nsg"></a>Suppression d'un groupe de sécurité réseau
Vous ne pouvez supprimer un groupe de sécurité réseau que s’il n’est associé à aucune ressource. Pour supprimer un groupe de sécurité réseau, procédez comme suit :

1. Dans le portail Azure, cliquez sur **Groupes de ressources >** > **RG-NSG** > **...** > **NSG-FrontEnd**.
2. Dans le panneau **Paramètres**, cliquez sur **Interfaces réseau**.
3. Si des cartes réseau sont répertoriées, cliquez sur la carte réseau et suivez l’étape 2 de [Dissocier un groupe de sécurité réseau d’une carte réseau](#Dissociate-an-NSG-from-a-NIC).
4. Répétez l’étape 3 pour chaque carte réseau.
5. Dans le panneau **Paramètres**, cliquez sur **Sous-réseaux**.
6. Si des sous-réseaux sont répertoriés, cliquez sur le sous-réseau et suivez les étapes 2 et 3 de [Dissocier un groupe de sécurité réseau d’un sous-réseau](#Dissociate-an-NSG-from-a-subnet).
7. Faites défiler à gauche vers le panneau **NSG-FrontEnd**, puis cliquez sur **Supprimer** > **Oui**.

    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>Étapes suivantes
* [Activez la journalisation](virtual-network-nsg-manage-log.md) des groupes de sécurité réseau.
