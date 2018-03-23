---
title: Protéger des machines virtuelles déployées sur Azure Stack | Microsoft Docs
description: Instructions sur la façon de protéger les machines virtuelles déployées sur Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: 02get-started-article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: hector.linares
ms.openlocfilehash: e7c437e3310fbf5c921920a3f08ecb8fe1f0d931
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Protéger des machines virtuelles déployées sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Cet article présente les instructions à suivre pour protéger des machines virtuelles utilisateur déployées sur Azure Stack.

Pour assurer une protection contre la perte de données et les temps d’arrêt non planifiés, vous devez implémenter un plan de récupération d’urgence ou de sauvegarde-récupération pour vos données et applications utilisateur. Ce plan est unique pour chaque application, mais suit un cadre établi par la stratégie de récupération d’urgence et de continuité de l’activité de votre organisation. Pour obtenir des pratiques et modèles généraux pour la résilience et la disponibilité de l’application, reportez-vous à [Conception d’applications résilientes pour Azure](https://docs.microsoft.com/azure/architecture/resiliency) dans le centre des architectures Azure.

## <a name="azure-stack-infrastructure-recovery"></a>Récupération de l’infrastructure Azure Stack

Les utilisateurs doivent protéger leurs machines virtuelles séparément des services d’infrastructure d’Azure Stack.

Si le cloud Azure Stack est hors connexion pendant une période prolongée ou définitivement irrécupérable, vous devez avoir un plan en place pour conserver le traitement des requêtes utilisateur de l’application avec un temps d’arrêt minimal. L’opérateur du cloud Azure Stack est chargé de préparer un plan de récupération pour les services et l’infrastructure Azure Stack sous-jacents. Pour plus d’informations, consultez l’article [Récupérer des données suite à une perte catastrophique](https://docs.microsoft.com/azure/azure-stack/azure-stack-backup-recover-data). 

Le plan de récupération pour les services d’infrastructure Azure Stack n’inclut pas la récupération des machines virtuelles utilisateur, des comptes de stockage ou des bases de données. En tant que propriétaire de l’application, vous êtes responsable de l’implémentation d’un plan de récupération pour vos applications et données.
 
## <a name="sourcetarget-combinations"></a>Combinaisons source/cible

Chaque cloud Azure Stack est déployé dans un centre de données. Un environnement distinct est requis afin que vous puissiez récupérer vos applications. L’environnement peut être un cloud Azure Stack distinct dans un autre centre de données ou le cloud public Azure. Vos exigences en matière de confidentialité et de souveraineté des données déterminent l’environnement de récupération pour votre application. Lorsque vous activez la protection pour chaque application, vous avez la possibilité de choisir une option de récupération spécifique pour chacune d’elles. Vous pouvez avoir des applications dans un seul abonnement pour sauvegarder des données dans un autre centre de données. Dans un autre abonnement, vous pouvez répliquer des données dans le cloud public Azure. 
 
Planifiez votre stratégie de récupération d’urgence et de sauvegarde-récupération pour chaque application afin de déterminer la cible pour chaque application. Cela permet à votre organisation de dimensionner correctement la capacité de stockage requise sur site et la consommation du projet dans le cloud public. 

|  | Azure global | Azure Stack déployé dans un centre de données de fournisseur de services cloud et géré par un fournisseur de services cloud | Azure Stack déployé dans un centre de données de client et géré par un client |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Azure Stack déployé dans un centre de données de fournisseur de services cloud et géré par un fournisseur de services cloud** | Les machines virtuelles utilisateur sont déployées sur le compte Azure Stack géré par le fournisseur de services cloud. Les machines virtuelles utilisateur sont restaurées à partir de la sauvegarde ou basculées directement sur Azure. | Le fournisseur de services cloud gère les instances principales et secondaires d’Azure Stack dans ses propres centres de données. Les machines virtuelles utilisateur sont restaurées ou basculées entre les deux instances Azure Stack. | Le fournisseur de services cloud gère Azure Stack dans le site principal. Le centre de données du client est la cible de la restauration ou du basculement. |
| **Azure Stack déployé dans un centre de données de client et géré par un client** | Les machines virtuelles utilisateur sont déployées sur le compte Azure Stack géré par le client. Les machines virtuelles utilisateur sont restaurées à partir de la sauvegarde ou basculées directement sur Azure. | Le client gère les instances principales et secondaires d’Azure Stack dans ses propres centres de données. Les machines virtuelles utilisateur sont restaurées ou basculées entre les deux instances Azure Stack. | Le client gère Azure Stack dans le site principal. Le centre de données du fournisseur de services cloud est la cible de la restauration ou du basculement. |

![Combinaisons source/cible](media\azure-stack-manage-vm-backup\vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Objectifs de récupération d’application

Vous devez quantifier le temps d’arrêt et la perte de données que votre organisation peut tolérer pour chaque application pour déterminer la meilleure approche de protection de vos applications sur Azure Stack. Il est nécessaire de quantifier le temps d’arrêt et la perte de données pour développer et implémenter un plan de récupération avec un impact réduit sur votre organisation. Pour chaque application, prenez en compte ce qui suit :  
 - **Objectif de délai de récupération (RTO)**  
Le RTO est la durée maximale acceptable pendant laquelle une application peut être indisponible après un incident. Si votre objectif RTO est de 90 minutes, vous devez être en mesure de remettre l’application en état de fonctionnement dans les 90 minutes à partir du début d’une panne. Si vous avez un RTO faible, vous pouvez conserver un deuxième déploiement fonctionnant de manière continue en veille, pour vous protéger contre une panne régionale.
 - **Objectif de point de récupération (RPO)**  
Le RPO est la durée maximale de perte de données acceptable pendant une panne. Par exemple, si vous stockez des données dans une base de données unique, sans aucune réplication vers d’autres bases de données, et effectuez des sauvegardes toutes les heures, vous risquez de perdre jusqu’à une heure de données.
 
Les objectifs RTO et RPO sont des exigences de l’entreprise. Procédez à une évaluation des risques pour définir les RTO et RPO de l’application. Une autre métrique commune est le **temps moyen de récupération** (MTTR), qui est la durée moyenne nécessaire à la restauration de l’application après une panne. La métrique MTTR est un fait empirique concernant un système. Si la métrique MTTR dépasse celle de l’objectif RTO, une défaillance du système entraînera une interruption inacceptable des opérations, car il ne sera pas possible de restaurer le système au sein de l’objectif RTO défini.

### <a name="backup-restore"></a>Sauvegarde-restauration

Le schéma de protection le plus courant pour les applications basées sur une machine virtuelle consiste à utiliser un logiciel de sauvegarde. En général, la sauvegarde d’une machine virtuelle inclut le système d’exploitation, la configuration du système d’exploitation, les fichiers binaires d’application et les données d’application. Les sauvegardes sont créées en prenant un cliché instantané des volumes, des disques ou de la machine virtuelle entière. Avec Azure Stack, vous avez la possibilité de sauvegarder à partir du contexte du système d’exploitation invité ou à partir du stockage Azure Stack et des API de calcul. Azure Stack ne prend pas en charge les sauvegardes prises au niveau de l’hyperviseur. 
 
![Sauvegarde-restauration](media\azure-stack-manage-vm-backup\vm_backupdataflow_03.png)
 
La récupération de l’application nécessite la restauration d’une ou de plusieurs machines virtuelles dans le même cloud ou un nouveau cloud. Vous pouvez cibler un cloud dans votre centre de données ou le cloud public. Le choix du cloud cible n’appartient qu’à vous et repose sur vos exigences de confidentialité et de souveraineté des données. 
 
 - RTO : temps d’arrêt mesuré en secondes 
 - RPO : perte de données minimale
 - Topologie du déploiement : actif/passif 

#### <a name="planning-your-backup-strategy"></a>Planification de votre stratégie de sauvegarde

La planification de votre stratégie de sauvegarde et la définition des exigences de mise à l’échelle commencent avec la quantification du nombre d’instances de machine virtuelle qui doivent être protégées. La sauvegarde de toutes les machines virtuelles sur tous les serveurs dans un environnement est une stratégie très courante. Toutefois, avec Azure Stack, certaines machines virtuelles doivent être sauvegardées. Par exemple, les machines virtuelles dans un groupe identique sont considérées comme des ressources éphémères qui peuvent apparaître et disparaître, sans que vous soyez prévenu. Toutes les données durables qui doivent être protégées sont stockées dans un référentiel distinct comme une base de données ou un magasin d’objets. Considérations importantes à prendre en compte pour la sauvegarde de machines virtuelles sur Azure Stack :

 - **Regroupement en catégories**
    - Imaginez un modèle où les utilisateurs choisissent la sauvegarde de machine virtuelle.
    - Définissez un SLA de récupération basé sur la priorité des applications ou sur l’impact sur l’activité.
 - **Mettre à l'échelle**
    - Tenez compte des sauvegardes échelonnées lorsque vous intégrez un grand nombre de nouvelles machines virtuelles (si la sauvegarde est requise).
    - Évaluez les produits de sauvegarde qui peuvent capturer et transmettre des données de sauvegarde de manière efficace pour réduire le contenu de la ressource sur la solution.
    - Évaluez les produits de sauvegarde qui stockent de manière efficace les données de sauvegarde à l’aide de sauvegardes incrémentielles ou différentielles pour réduire le besoin d’extraire des sauvegardes complètes sur toutes les machines virtuelles dans l’environnement.
 - **Restore**
    - Les produits de sauvegarde peuvent restaurer des disques virtuels, des données d’application dans une machine virtuelle existante, ou la ressource de machine virtuelle entière et les disques virtuels associés. Le choix du schéma de restauration dépend de la façon dont vous planifiez de restaurer l’application et aura un impact sur le temps de récupération de l’application. Par exemple, il peut être plus facile de redéployer un serveur SQL à partir d’un modèle, puis de restaurer les bases de données au lieu de restaurer la machine virtuelle entière ou un ensemble de machines virtuelles.


### <a name="replicationmanual-failover"></a>Réplication/basculement manuel

Une autre approche pour prendre en charge la haute disponibilité consiste à répliquer les machines virtuelles de votre application dans un cloud distinct et à compter sur un basculement déclenché manuellement. La réplication du système d’exploitation, des fichiers binaires d’application et des données d’application peut être effectuée au niveau de la machine virtuelle ou du système d’exploitation invité. Gérez le basculement à l’aide de logiciels supplémentaires distincts de l’application.
 
Avec cette approche, l’application est uniquement déployée dans un cloud. La machine virtuelle est ensuite répliquée dans l’autre cloud, dans l’un de vos centres de données ou dans le cloud public. Si un basculement est déclenché, les machines virtuelles secondaires doivent être démarrées dans le deuxième cloud. Dans certains cas, le basculement crée les machines virtuelles et les attache à des disques. Ce processus peut prendre un certain temps, surtout avec une application à plusieurs niveaux dotée d’une séquence de démarrage spécifique. Il est peut-être nécessaire d’exécuter d’autres étapes avant que l’application soit prête pour commencer à traiter les requêtes.

![Réplication-basculement manuel](media\azure-stack-manage-vm-backup\vm_backupdataflow_02.png)

 - RTO : temps d’arrêt en minutes 
 - RPO : perte de données variable
 - Topologie du déploiement : en veille actif/passif
 
### <a name="high-availabilityautomatic-failover"></a>Haute disponibilité/basculement automatique

Pour les applications pour lesquelles votre activité peut tolérer quelques secondes ou minutes de temps d’arrêt et une perte de données minimale, vous devez envisager une configuration de haute disponibilité. Les applications de haute disponibilité sont conçues pour récupérer rapidement et automatiquement après les pannes. Pour les pannes matérielles locales, l’infrastructure Azure Stack implémente la haute disponibilité dans le réseau physique à l’aide de deux commutateurs TOR (Top of Rack). Pour les pannes au niveau du calcul, Azure Stack utilise plusieurs nœuds dans une unité d’échelle. Au niveau de la machine virtuelle, vous pouvez utiliser des groupes identiques en combinaison avec des domaines d’erreur pour vous assurer que les pannes de nœud n’interrompent pas votre application. 
 
En combinaison avec des groupes identiques, votre application doit prendre en charge la haute disponibilité en mode natif ou l’utilisation du logiciel de clustering. Par exemple, Microsoft SQL Server prend en charge la haute disponibilité en mode natif pour les bases de données à l’aide du mode de validation synchrone. Toutefois, si vous pouvez uniquement prendre en charge la réplication asynchrone, quelques données seront perdues. Les applications peuvent également être déployées dans un cluster de basculement où le logiciel de clustering gère le basculement automatique de l’application. 
 
Avec cette approche, l’application n’est active que dans un cloud, mais le logiciel est déployé dans plusieurs clouds. Les autres clouds sont en mode veille, prêts à démarrer l’application lorsque le basculement est déclenché.

 - RTO : temps d’arrêt mesuré en secondes 
 - RPO : perte de données minimale
 - Topologie du déploiement : en veille actif/actif

### <a name="fault-tolerance"></a>Tolérance de panne

La redondance physique d’Azure Stack et la disponibilité du service d’infrastructure protègent uniquement contre les pannes matérielles comme la panne d’un disque, de l’alimentation, d’un port réseau ou d’un nœud. Toutefois, si votre application doit toujours être disponible et ne peut pas perdre de données, vous devez implémenter la tolérance de panne en mode natif dans votre application ou activer la tolérance de panne avec des logiciels supplémentaires. 
 
Tout d’abord, vous devez vous assurer que les machines virtuelles de l’application sont déployées à l’aide de groupes identiques pour les protéger des pannes au niveau du nœud. Pour vous protéger contre la mise hors connexion du cloud, la même application doit déjà être déployée dans un autre cloud, afin qu’elle puisse continuer à traiter les requêtes sans interruption. Ce phénomène est généralement appelé déploiement actif-actif.
 
Gardez à l’esprit que chaque cloud Azure Stack est indépendant des autres ; les clouds sont donc toujours considérés comme actifs du point de vue de l’infrastructure. Dans ce cas, plusieurs instances actives de l’application sont déployées dans un ou plusieurs clouds actifs. 
 
 - RTO : aucun temps d’arrêt
 - RPO : aucune perte de données
 - Topologie du déploiement : actif/actif

### <a name="no-recovery"></a>Aucune récupération

Certaines applications de votre environnement peuvent ne pas avoir besoin d’être protégées contre des temps d’arrêt non planifiés ou une perte de données. Par exemple, les machines virtuelles utilisées pour le développement et le test n’ont généralement pas besoin d’être récupérées. C’est à vous de décider si vous souhaitez protéger une application ou une machine virtuelle spécifique. Azure Stack n’offre pas de sauvegarde ou réplication des machines virtuelles à partir de l’infrastructure sous-jacente. Comme dans Azure, vous devez choisir la protection de chaque machine virtuelle dans chacun de vos abonnements.  
 
 - RTO : irrécupérable 
 - RPO : perte complète des données
 
## <a name="recommended-topologies"></a>Topologies recommandées 

Considérations importantes à prendre en compte pour votre déploiement Azure Stack :

|     | Recommandation | Commentaires |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sauvegarder/restaurer des machines virtuelles vers une cible de sauvegarde externe déjà déployée dans votre centre de données | Recommandé | Tirez parti de l’infrastructure de sauvegarde existante et des capacités opérationnelles. Veillez à dimensionner l’infrastructure de sauvegarde pour qu’elle soit prête à protéger les instances de machine virtuelle supplémentaires. Assurez-vous que l’infrastructure de sauvegarde ne se trouve pas juste à côté de votre source. Vous pouvez restaurer des machines virtuelles du compte Azure Stack source dans une instance secondaire Azure Stack ou Azure. |
| Sauvegarder/restaurer des machines virtuelles vers une cible de sauvegarde externe dédiée à Azure Stack | Recommandé | Vous pouvez acheter une nouvelle infrastructure de sauvegarde ou une infrastructure de sauvegarde dédiée à l’approvisionnement pour Azure Stack. Assurez-vous que l’infrastructure de sauvegarde ne se trouve pas juste à côté de votre source. Vous pouvez restaurer des machines virtuelles du compte Azure Stack source dans une instance secondaire Azure Stack ou Azure. |
| Sauvegarder/restaurer des machines virtuelles directement vers Azure global ou un fournisseur de services fiable | Recommandé | Tant que vous pouvez satisfaire à vos exigences en matière de réglementation et de confidentialité des données, vous pouvez stocker vos sauvegardes dans Azure global ou un fournisseur de services fiable. Dans l’idéal, le fournisseur de services exécute également Azure Stack pour que votre expérience opérationnelle soit cohérente lorsque vous effectuez une restauration. |
| Répliquer/basculer des machines virtuelles vers une instance Azure Stack distincte | Recommandé | Dans le cas d’un basculement, vous devez avoir un deuxième cloud Azure Stack totalement opérationnel afin d’éviter des temps d’arrêt trop longs. |
| Répliquer/basculer des machines virtuelles directement vers Azure ou un fournisseur de services fiable | Recommandé | Tant que vous pouvez satisfaire à vos exigences obligatoires et en matière de confidentialité des données, vous pouvez répliquer vos données dans Azure global ou un fournisseur de services fiable. Dans l’idéal, le fournisseur de services exécute également Azure Stack pour que votre expérience opérationnelle soit cohérente après un basculement. |
| Déployer la cible de sauvegarde sur le même cloud Azure Stack avec vos données d’application | Non recommandé | Évitez de stocker des sauvegardes dans le même cloud Azure Stack. Un temps d’arrêt non planifié du cloud peut vous priver de vos données principales et données de sauvegarde. Si vous choisissez de déployer une cible de sauvegarde en tant qu’appliance virtuelle (à des fins d’optimisation de la sauvegarde et de la restauration), vous devez vous assurer que toutes les données sont copiées en continu dans un emplacement de sauvegarde externe. |
| Déployer l’appliance de sauvegarde physique dans le même rack que celui où la solution Azure Stack est installée | Non pris en charge | À ce jour, vous ne pouvez pas connecter d’autres appareils à des commutateurs TOR qui ne font pas partie de la solution d’origine. |
 
## <a name="next-steps"></a>Étapes suivantes 

Dans cet article, nous avons présenté les instructions à suivre pour protéger des machines virtuelles d’utilisateur déployées sur Azure Stack. Pour plus d’informations sur la façon de protéger vos machines virtuelles à l’aide des services Azure, consultez :
 - [Documentation Sauvegarde Azure](https://docs.microsoft.com/en-us/azure/backup/ ) 
 - [Documentation Site Recovery](https://docs.microsoft.com/en-us/azure/site-recovery/)  
 
Pour en savoir plus sur les produits de partenaires qui offrent une protection de machine virtuelle sur Azure Stack, consultez «[Protecting applications and data on Azure Stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)» (Protéger des applications et des données sur Azure Stack).
