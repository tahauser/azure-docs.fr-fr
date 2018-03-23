---
title: Disponibilité de SAP HANA sur Azure dans l’ensemble des régions Azure | Microsoft Docs
description: Décrit une vue d’ensemble des considérations sur la disponibilité lors de l’exécution de SAP HANA sur des machines virtuelles Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 052394884f85d527caa88ff6c9464af669f47bb5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-across-azure-regions"></a>Disponibilité de SAP HANA sur Azure dans l’ensemble des régions Azure
Dans cet article, les scénarios concernant la disponibilité de SAP HANA dans différentes régions Azure sont décrits et étudiés. Étant donné que la distance entre des régions Azure séparées est plus importante, des considérations particulières sont mentionnées dans cet article.

## <a name="motivation-to-deploy-across-multiple-azure-regions"></a>Motivation de déploiement dans plusieurs régions Azure
Des régions Azure différentes sont séparées par une plus grande distance. En fonction de la région géopolitique, il faut compter des centaines de kilomètres, voire même des milliers de kilomètres, comme aux États-Unis d’Amérique. En raison de la distance qui sépare les différentes régions Azure, le trafic entre des ressources déployées dans deux régions Azure différentes subit une importance latence à l’aller et au retour. Assez importante pour exclure tout échange de données synchrones entre deux instances SAP HANA sous des charges de travail SAP typique. D’un autre côté, vous vous retrouvez souvent confronté au fait qu’il existe des exigences définies sur la distance entre votre centre de données principal et un centre de données secondaire, afin de fournir la disponibilité en cas de catastrophe naturelle sur une large zone. Comme les ouragans qui ont frappé les Caraïbes et la Floride en septembre et octobre 2017. Il existe au moins une exigence de distance. Dans la plupart des cas rencontrés par les clients, cette définition de distance minimale nécessite que vous désigniez la disponibilité dans les [régions Azure](https://azure.microsoft.com/regions/). Comme la distance entre deux régions Azure est trop importante pour utiliser le mode réplication synchrone de SAP HANA, les exigences quant aux objectifs de délai de récupération et de point de récupération pourraient vous obliger à déployer des configurations de disponibilité dans une seule région, puis à procéder à des déploiements supplémentaires dans une seconde région.

Un autre aspect à prendre en compte dans ces configurations concerne le basculement et la redirection du client. Hypothétiquement, le basculement entre des instances SAP HANA dans deux régions Azure différentes est toujours manuel. Comme le mode de réplication de la réplication du système SAP HANA est défini sur asynchrone, il est possible que les données renseignées dans l’instance SAP HANA principale ne soient pas encore parvenues jusqu’à l’instance SAP HANA secondaire. Par conséquent, le basculement automatique ne peut être autorisé pour les configurations où la réplication est asynchrone. Même avec un basculement contrôlé manuel, comme dans un exercice de basculement, vous devez prendre les mesures nécessaires et vous assurer que toutes les données renseignées dans l’instance principale sont parvenues à l’instance secondaire avant de basculer manuellement à l’autre région Azure.
 
Étant donné que vous fonctionnez avec une plage d’adresses IP différentes dans les réseaux virtuels Azure, déployés dans la deuxième région Azure, vous devez modifier les clients SAP HANA dans leur configuration ou mettre en place des étapes pour modifier la résolution de noms. Ainsi, les clients sont redirigés vers la nouvelle adresse IP secondaire du serveur. L’article SAP sur la [récupération d’une connexion cliente après prise de contrôle](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html) vous fournira plus de détails.   

## <a name="simple-availability-between-two-azure-regions"></a>Disponibilité simple entre deux régions Azure
Dans ce scénario, vous avez décidé de ne mettre en place aucune configuration de disponibilité dans une seule région. Mais vous disposez de la demande pour que la charge de travail soit traitée en cas de catastrophe. Les systèmes de non production sont des cas typiques. Vous pouvez vous permettre d’avoir un système hors service pendant une demi-journée, voire une journée entière. Mais ce système ne doit pas être indisponible pendant 48 heures ou plus. Afin que la configuration soit moins coûteuse, vous exécutez un autre système encore moins important dans la machine virtuelle qui sert de destination, ou vous dimensionnez la machine virtuelle dans une région secondaire plus petite et choisissez de ne pas pré-charger les données. Étant donné que le basculement se fait manuellement, et qu’il comporte de nombreuses étapes pour basculer aussi la pile d’application complète, le temps supplémentaire nécessaire pour mettre hors service, redimensionner et redémarrer la machine virtuelle est acceptable.

> [!NOTE]
> Même sans données pré-chargées dans la cible de réplication du système HANA, il vous faut au moins 64 Go de mémoire, et assez de mémoire pour conserver les données de stockage de lignes de l’instance cible.

![Deux machines virtuelles sur deux régions](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> Dans cette configuration, vous ne pouvez pas fournir un objectif de point de récupération égal à 0, car le mode de réplication du système HANA est asynchrone. Si vous devez fournir un objectif de point de récupération égal à 0, cette configuration ne peut pas être celle de votre choix.

Une petite modification de la configuration consiste à configurer le pré-chargement des données. Toutefois, comme le basculement est une opération manuelle, et que les couches d’application doivent aussi être déplacées dans la deuxième région, le pré-chargement des données peut ne pas être logique. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Combiner la disponibilité dans une région et entre des régions 
Une combinaison de disponibilité à l’intérieur et entre des régions peut être obtenue avec :

- Avec une configuration requise pour RPO = 0 dans une région Azure.
- En n’étant pas prêt ou capable d’avoir des opérations globales de la compagnie, ainsi qu’une région plus importante, qui sont affectées par une catastrophe naturelle majeure. Comme c’était le cas ces dernières années avec quelques ouragans qui ont frappé les Caraïbes.
- Avec des régulations qui exigent des distances entre des sites principaux et secondaires clairement au-delà de ce que les zones de disponibilité Azure peuvent fournir.

 
Dans de tels cas, vous devez configurer ce que SAP HANA nomme [configuration de la réplication de système SAP HANA intermédiaire](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) avec la réplication de système HANA. L’architecture ressemblerait à :

![Trois machines virtuelles sur deux régions](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Cette configuration offre un objectif de point de récupération égal à 0, avec des délais RTO courts au sein de la région principale, et fournit également un objectif de point de récupération descendant en cas de déplacement vers la région secondaire. Les délais RTO dans la région secondaire dépendent de la configuration du pré-chargement des données. Dans de nombreux cas rapportés par les clients, la machine virtuelle dans la région secondaire est utilisée pour exécuter un test du système. En raison de cette utilisation, les données ne peuvent être pré-chargées.

> [!NOTE]
> Comme vous utilisez le mode d’opération logreplay pour la réplication de système HANA du niveau 1 au niveau 2 (réplication synchrone dans la région principale), la réplication entre le niveau 2 et le niveau 3 (réplication vers un site secondaire) ne peut se faire en mode d’opération delta_datashipping. Les informations sur les modes d’opération et sur quelques restrictions sont renseignées par SAP dans [Modes d’opération pour la réplication de système SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Étapes suivantes
Si vous avez besoin d’un guide pas à pas pour installer une telle configuration dans Azure, lisez les articles :

- [Installer la réplication de système SAP HANA sur des machines virtuelles Azure](sap-hana-high-availability.md)
- [Votre SAP sur Azure - Partie 4 - Haute disponibilité pour SAP HANA avec la réplication de système](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
