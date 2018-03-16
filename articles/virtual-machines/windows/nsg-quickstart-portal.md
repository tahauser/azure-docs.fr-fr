---
title: "Ouvrir des ports sur une machine virtuelle à l’aide du portail Azure | Microsoft Docs"
description: "Découvrez comment ouvrir un port / créer un point de terminaison sur votre machine virtuelle Windows à l’aide du modèle de déploiement Resource Manager dans le Portail Azure"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 08f0af6ecdb45b263d39c3d2d6442f4ed555e3c3
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2018
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Guide d’ouverture de ports vers une machine virtuelle avec le portail Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Commandes rapides
Vous pouvez également [effectuer ces étapes à l’aide d’Azure PowerShell](nsg-quickstart-powershell.md).

Créez d’abord votre Groupe de sécurité réseau. Sélectionnez un groupe de ressources dans le portail, choisissez **Ajouter**, puis recherchez et sélectionnez **Groupe de sécurité de réseau** :

![Ajouter un groupe de sécurité réseau](./media/nsg-quickstart-portal/add-nsg.png)

Entrez un nom pour votre groupe de sécurité réseau, sélectionnez ou créez un groupe de ressources, et sélectionnez un emplacement. Sélectionnez **Créer** quand vous avez terminé :

![Création d'un groupe de sécurité réseau](./media/nsg-quickstart-portal/create-nsg.png)

Sélectionnez un nouveau groupe de sécurité réseau. Sélectionnez « Règles de sécurité entrantes », puis cliquez sur le bouton **Ajouter** pour créer une règle :

![Ajouter une règle de trafic entrant](./media/nsg-quickstart-portal/add-inbound-rule.png)

Pour créer une règle qui autorise le trafic :

- Sélectionnez le bouton **De base**. Par défaut, la fenêtre **Avancé** fournit des options de configuration supplémentaires, comme la définition d’une plage de ports ou de blocs IP source.
- Choisissez un **Service** courant dans le menu déroulant, tel que *HTTP*. Vous pouvez aussi sélectionner *Personnalisé* pour indiquer un port spécifique à utiliser. 
- Si vous le souhaitez, modifiez la priorité ou le nom. La priorité affecte l’ordre dans lequel les règles sont appliquées (plus la valeur numérique est faible, plus la règle est appliquée précocement).
- Quand vous êtes prêt, sélectionnez **OK** pour créer la règle :

![Créer une règle de trafic entrant](./media/nsg-quickstart-portal/create-inbound-rule.png)

L’étape finale consiste à associer un sous-réseau ou une interface réseau spécifique à votre groupe de sécurité réseau. Associons le groupe de sécurité réseau à un sous-réseau. Sélectionnez **Sous-réseaux**, puis choisissez **Associer** :

![Associer un groupe de sécurité réseau à un sous-réseau](./media/nsg-quickstart-portal/associate-subnet.png)

Sélectionnez votre réseau virtuel, puis sélectionnez le sous-réseau approprié :

![Association d’un groupe de sécurité réseau à un réseau virtuel](./media/nsg-quickstart-portal/select-vnet-subnet.png)

Vous avez maintenant créé un groupe de sécurité réseau, créé une règle de trafic entrant qui autorise le trafic sur le port 80 et l’avez associé à un sous-réseau. Toute machine virtuelle que vous connectez à ce sous-réseau est accessible sur le port 80.

## <a name="more-information-on-network-security-groups"></a>En savoir plus sur les groupes de sécurité réseau
Les commandes rapides vous permettent d’être opérationnel avec le trafic entrant vers votre machine virtuelle. Les groupes de sécurité réseau fournissent un grand nombre de fonctionnalités intéressantes et une granularité pour contrôler l’accès à vos ressources. Découvrez plus d’informations sur la [création d’un groupe de sécurité réseau et de règles de liste de contrôle d’accès ici](../../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Pour les applications Web hautement disponibles, vous devez placer vos machines virtuelles derrière un équilibreur de charge Azure. L’équilibreur de charge répartit le trafic entre les machines virtuelles, avec un groupe de sécurité réseau qui assure le filtrage du trafic. Pour plus d’informations, consultez [Guide pratique pour équilibrer la charge des machines virtuelles Linux dans Azure pour créer une application hautement disponible](tutorial-load-balancer.md).

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous trouverez plus d’informations sur la création d’environnements plus détaillés dans les articles suivants :

* [Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Présentation du groupe de sécurité réseau](../../virtual-network/virtual-networks-nsg.md)