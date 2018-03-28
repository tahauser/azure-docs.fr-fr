---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 95a78df20f5bed07213dfa3cc2c9b35e283f54e7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
La famille de machines virtuelles de série B vous permet de choisir la taille de machine virtuelle vous offrant les performances de base nécessaires à votre charge de travail, avec la possibilité d’étendre jusqu’à 100 % les performances d’un processeur virtuel Intel® Broadwell E5-2673 v4 2.3 GHz ou Intel® Haswell 2.4 GHz E5-2673 v3.

Les machines virtuelles de la série B sont idéales pour les charges de travail ne nécessitant pas en permanence les performances complètes du processeur, comme les serveurs web, les petites bases de données et les environnements de test et de développement. Ces charges de travail ont généralement des exigences modulables en termes de performances. La série B vous offre la possibilité d’acheter une taille de machine virtuelle aux performances de base. Ainsi, l’instance de machine virtuelle génère des crédits lorsqu’elle n’utilise pas la totalité de ses performances. Dès que la machine virtuelle a cumulé des crédits, elle peut étendre ses performances en utilisant jusqu’à 100 % du processeur virtuel lorsque l’application requiert des performances de processeur plus élevées.

La série B est disponible dans les six tailles de machines virtuelles suivantes :

| Taille          | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Perf. du processeur de base de machine virtuelle | Perf. du processeur max. de machine virtuelle | Crédits cumulés/heure | Crédits cumulés max. |
|---------------|--------|-------------|----------------|--------------------------------|---------------------------|-----------------------|--------------------|
| Standard_B1s  | 1      | 1           | 4              | 10%                            | 100 %                      | 6.                     | 144                |
| Standard_B1ms | 1      | 2           | 4              | 20%                            | 100 %                      | 12                    | 288                |
| Standard_B2s  | 2      | 4           | 8              | 40%                            | 200%                      | 24                    | 576                |
| Standard_B2ms | 2      | 8           | 16             | 60 %                            | 200%                      | 36                    | 864                |
| Standard_B4ms | 4      | 16          | 32             | 90%                            | 400%                      | 54                    | 1296               |
| Standard_B8ms | 8      | 32          | 64             | 135%                           | 800%                      | 81                    | 1944               |




## <a name="q--a"></a>Questions et réponses 

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>Q : Comment obtenir 135 % des performances de base d’une machine virtuelle ?
**R** : Ce pourcentage est réparti sur les 8 processeurs virtuels qui composent la taille de la machine virtuelle. Par exemple, si votre application utilise 4 des 8 cœurs travaillant au traitement par lots et que chacun de ces 4 processeurs virtuels est utilisé à 30 %, la quantité totale des performances du processeur de la machine virtuelle serait égale à 120 %.  Ce qui signifie que votre machine virtuelle générerait un crédit temps basé sur le delta de 15 % à partir de vos performances de base.  Cela signifie également que lorsque vous disposez de crédits, cette même machine virtuelle peut utiliser la totalité des 8 processeurs virtuels pour obtenir une performance de processeur maximale de 800 %.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>Q : Comment puis-je surveiller mes soldes de crédit et de consommation ?
**R** : Nous allons présenter 2 nouvelles mesures dans les semaines à venir. La mesure **Credit** vous permettra d’afficher les crédits cumulés par votre machine virtuelle et la mesure **ConsumedCredit** d’afficher le nombre de crédits de processeur utilisés par votre machine virtuelle.    Ces mesures figurent sur le volet des mesures du portail ou sont visibles par programme via les API Azure Monitor.

Pour en savoir plus sur l’accès aux données de mesure pour Azure, consultez [Vue d’ensemble des mesures dans Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>Q : Comment les crédits sont-ils cumulés ?
**R** : Les taux de cumul et d’utilisation de la machine virtuelle sont définis pour qu’une machine virtuelle s’exécutant exactement à son niveau de performances de base ne génère aucun cumul net ou n’utilise aucun crédit.  Une machine virtuelle connaît une augmentation nette de ses crédits chaque fois qu’elle s’exécute sous son niveau de performances de base, et une diminution nette de ses crédits chaque fois qu’elle utilise le processeur à un niveau plus élevé de performances.

**Exemple** : Je déploie une machine virtuelle à l’aide de la taille B1ms pour ma petite application de base de données de pointage des présences. Cette taille permet à mon application d’utiliser jusqu’à 20 % d’un processeur virtuel qui est considéré comme étant ma base, soit 0,2 crédit par minute utilisable ou cumulable. 

Mon application est occupée en début et fin de journée de travail de mes employés, soit entre 7 h 00 et 9 h 00 et 16 h 00 et 18 h 00. Pendant les 20 heures restantes de la journée, mon application est en général en veille et n’utilise que 10 % du processeur virtuel. Pendant les heures creuses, je cumule 0,2 crédit par minute et utilise uniquement 0,1 crédit par minute. Ainsi, ma machine virtuelle cumule 0,1 x 60, soit 6 crédits par heure.  Pendant les 20 heures creuses, je cumule 120 crédits.  

Pendant les heures de pointe, mon application utilise en moyenne 60 % du processeur virtuel. Je cumule toujours 0,2 crédit par minute, mais j’utilise 0,6 crédit par minute pour un coût net de 0,4 crédit par minute ou 0,4 x 60 = 24 crédits par heure. Je dispose de 4 heures d’utilisation de pointe par jour, ce qui coûte 4 x 24 = 96 crédits.

Si je prends les 120 crédits cumulés lors des heures creuses et que je soustrais les 96 crédits utilisés pendant mes heures de pointe, je cumule 24 crédits supplémentaires par jour utilisables pour d’autres pics d’activité.


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>Q : La série B prend-elle en charge les disques de données de stockage Premium ?
**R** : Oui, la série B prend en charge les disques de données de stockage Premium ?   
    


    

    
