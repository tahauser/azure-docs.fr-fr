---
title: "Mettre à l’échelle des actions de nœud d’unité dans Azure Stack | Microsoft Docs"
description: "Découvrez comment afficher l’état du nœud et utiliser les actions de mise sous tension, de mise hors tension, de vidage et de reprise sur un système intégré Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: dbb68b10-c721-4188-aa07-584d0cd63138
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: mabrigg
ms.openlocfilehash: 55cc0eb3cc187d87e0d2ae96e2433cb9682ab370
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Mettre à l’échelle des actions de nœud d’unité dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit comment afficher l’état d’une unité d’échelle et de ses nœuds associés et comment utiliser les actions de nœud disponibles. Les actions de nœud incluent la mise sous tension, la mise hors tension, le vidage, la reprise et la réparation. En règle générale, vous utilisez ces actions de nœud au cours du remplacement de champ de parties ou pour les scénarios de récupération de nœud.

> [!Important]  
> Toutes les actions de nœud décrites dans cet article ne doivent cibler qu’un seul nœud à la fois.


## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>Afficher l’état d’une unité d’échelle et de ses nœuds

Dans le portail d’administration, vous pouvez facilement afficher l’état d’une unité d’échelle et de ses nœuds associés.

Pour afficher l’état d’une unité d’échelle :

1. Dans la vignette **Gestion des régions**, sélectionnez la région.
2. Sur la gauche, sous **Ressources d’infrastructure**, sélectionnez **Unités d’échelle**.
3. Dans les résultats, sélectionnez l’unité d’échelle.
 
Ici, vous pouvez afficher les informations suivantes :

- Nom de la région
- Type de système
- Total des cœurs logiques
- Quantité totale de mémoire
- Liste des nœuds individuels et leur état (en cours d’exécution ou arrêté)

![Vignette d’unité de mise à l’échelle indiquant l’état En cours d’exécution pour chaque nœud](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>Affichage des informations relatives à un nœud d’unité d’échelle

Si vous sélectionnez un nœud individuel, vous pouvez afficher les informations suivantes :

- Nom de la région
- Modèle de serveur
- Adresse IP du contrôleur de gestion de la carte de base (BMC)
- État de fonctionnement
- Nombre total de cœurs
- Quantité totale de mémoire
 
![Vignette d’unité de mise à l’échelle indiquant l’état En cours d’exécution pour chaque nœud](media/azure-stack-node-actions/NodeActions.PNG)

Vous pouvez également effectuer des actions de nœud d’unité d’échelle à partir d’ici.

## <a name="scale-unit-node-actions"></a>Actions de nœud d’unité d’échelle

Lorsque vous affichez des informations relatives à un nœud d’unité d’échelle, vous pouvez également effectuer des actions de nœud telles que :

- La mise sous et hors tension
- Le vidage, la reprise
- La réparation

L’état de fonctionnement du nœud détermine les options disponibles.

### <a name="power-off"></a>Mise hors tension

L’action de **mise hors tension** désactive le nœud. Cela revient à appuyer sur le bouton d’alimentation. Cela n’envoie **pas** de signal d’arrêt au système d’exploitation. Pour les opérations planifiées de mise hors tension, assurez-vous d’avoir tout d’abord vidé l’unité d’échelle.

Cette action est généralement utilisée lorsqu’un nœud est dans un état suspendu et ne répond plus aux demandes.

> [!Important] 
> Cette fonctionnalité est uniquement disponible au moyen de PowerShell. Elle sera de nouveau disponible dans le portail d’administration de Azure Stack ultérieurement.


Pour exécuter l’action de mise hors tension via PowerShell :

````PowerShell
  Stop-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
```` 

Dans le cas peu probable où la mise hors tension ne fonctionnerait pas, utilisez l’interface web du BMC.

### <a name="power-on"></a>Mise sous tension

L’action de **mise sous tension** active le nœud. Cela revient à appuyer sur le bouton d’alimentation. 

> [!Important] 
> Cette fonctionnalité est uniquement disponible au moyen de PowerShell. Elle sera de nouveau disponible dans le portail d’administration de Azure Stack ultérieurement.

Pour exécuter l’action de mise sous tension via PowerShell :

````PowerShell
  Start-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
````

Dans le cas peu probable où la mise sous tension ne fonctionnerait pas, utilisez l’interface web du BMC.

### <a name="drain"></a>Vidage

L’action de **vidage** évacue toutes les charges de travail actives en les répartissant sur les nœuds restants dans cette unité d’échelle spécifique.

Cette action est généralement utilisée au cours du remplacement de champs de parties, telles que le remplacement d’un nœud complet.

> [!IMPORTANT]
> Assurez-vous de vider un nœud uniquement au cours de la période de maintenance planifiée, où les utilisateurs ont été notifiés. Sous certaines conditions, les charges de travail actives peuvent subir des interruptions.

Pour exécuter l’action de vidage via PowerShell :

  ````PowerShell
  Disable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Reprendre

L’action de **reprise** reprend un nœud vidé et le marque comme actif pour le positionnement de la charge de travail. Les charges de travail antérieures en cours d’exécution sur le nœud ne se restaurent pas automatiquement. Si vous videz un nœud, puis le mettez hors tension, ce dernier ne sera pas marqué comme actif pour le positionnement d’une charge de travail lorsque vous le remettrez sous tension. Lorsque vous êtes prêt, vous devez utiliser l’action de reprise pour marquer le nœud comme étant actif.

Pour exécuter l’action de reprise via PowerShell :

  ````PowerShell
  Enable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Réparation

L’action de **réparation** répare un nœud. À utiliser uniquement pour un des scénarios suivants :

- Remplacement de nœud complet (avec ou sans nouveaux disques de données)
- Après la défaillance et le remplacement d’un composant matériel (Si cela est conseillé dans la documentation sur les unités remplaçables sur site.

> [!IMPORTANT]
> Consultez la documentation sur les unités remplaçables sur site de votre fabricant de matériel OEM pour connaître les étapes exactes relatives au remplacement d’un nœuds ou de composants matériels individuels. La documentation sur les unités remplaçables sur site indique s’il faut exécuter l’action de réparation après le remplacement d’un composant matériel.  

Lorsque vous exécutez l’action de réparation, vous devez spécifier l’adresse IP du BMC. 

Pour exécuter l’action de réparation via PowerShell :

  ````PowerShell
  Repair-AzsScaleUnitNode -Region <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````


