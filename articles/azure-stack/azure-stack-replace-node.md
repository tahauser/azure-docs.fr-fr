---
title: "Remplacer un nœud d’unité d’échelle sur un système intégré Azure Stack | Microsoft Docs"
description: "Découvrez comment remplacer un nœud d’unité d’échelle physique sur un système intégré Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: 468af385833395963ef8acad905b99a9b7e6b8fa
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Remplacer un nœud d’unité d’échelle sur un système intégré Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit la procédure générale permettant de remplacer un ordinateur physique (également appelé *nœud d’unité d’échelle*) sur un système intégré Azure Stack. Les étapes de remplacement de nœud d’unité d’échelle varient en fonction de votre fournisseur de matériel OEM. Pour obtenir des instructions détaillées propres à votre système, consultez la documentation FRU (Field Replaceable Unit) de votre fournisseur.

L’organigramme suivant illustre la procédure générale de la FRU pour remplacer un nœud d’unité d’échelle dans son ensemble.

![Organigramme du processus de remplacement d’un nœud](media/azure-stack-replace-node/replacenodeflow.png)

* Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel.

## <a name="review-alert-information"></a>Examiner les informations sur l’alerte

Si un nœud d’unité d’échelle est arrêté, vous recevrez les alertes critiques suivantes :

- Nœud non connecté au contrôleur de réseau
- Nœud inaccessible pour la sélection élective d’ordinateur virtuel
- Nœud d’unité d’échelle hors ligne

![Liste des alertes en cas d’arrêt d’unité d’échelle](media/azure-stack-replace-node/nodedownalerts.png)

Si vous ouvrez l’alerte **Nœud d’unité d’échelle hors ligne**, la description de l’alerte contient le nœud d’unité d’échelle qui est inaccessible. Vous pouvez également recevoir des alertes supplémentaires dans la solution d’analyse propre à l’OEM qui est en cours d’exécution sur l’ordinateur hôte du cycle de vie du matériel.

![Détails de l’alerte de nœud hors ligne](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Procédure de remplacement d’un nœud d’unité d’échelle

Les étapes suivantes fournissent une vue d’ensemble de la procédure de remplacement d’un nœud d’unité d’échelle. Pour obtenir des instructions détaillées propres à votre système, consultez la documentation FRU (Field Replaceable Unit) de votre fournisseur OEM. Ne suivez pas ces étapes sans consulter la documentation fournie par votre fournisseur OEM.

1. Utilisez l’option [Drainer](azure-stack-node-actions.md#scale-unit-node-actions) pour placer le nœud d’unité d’échelle en mode maintenance. Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel.

   > [!NOTE]
   > Dans tous les cas, un seul nœud peut être purgé et mis hors tension en même temps sans endommager l’espace de stockage direct S2D.

2. Si le nœud est toujours sous tension, utilisez l’option [Mettre hors tension](azure-stack-node-actions.md#scale-unit-node-actions). Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel.
 
   > [!NOTE]
   > Dans le cas peu probable où la mise hors tension ne fonctionnerait pas, utilisez l’interface web du contrôleur de gestion de la carte de base (BMC).

1. Remplacez l’ordinateur physique. En règle générale, votre fournisseur de matériel OEM se charge de cette opération.
2. Utilisez l’action [Réparer](azure-stack-node-actions.md#scale-unit-node-actions) pour ajouter le nouvel ordinateur physique à l’unité d’échelle.
3. Utilisez le point de terminaison privilégié pour [vérifier l’état de réparation du disque virtuel](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Avec de nouveaux lecteurs de données, une opération de réparation de stockage complète peut prendre plusieurs heures en fonction de la charge du système et de l’espace utilisé.
4. Une fois la réparation terminée, vérifiez que toutes les alertes actives ont été automatiquement fermées.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le remplacement d’un disque physique échangeable à chaud, voir [Remplacer un disque](azure-stack-replace-disk.md). 
- Pour plus d’informations sur le remplacement d’un composant matériel non échangeable à chaud, consultez [Remplacer un composant matériel](azure-stack-replace-component.md).
