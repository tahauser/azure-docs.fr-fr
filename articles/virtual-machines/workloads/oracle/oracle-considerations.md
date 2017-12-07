---
title: Solutions Oracle sur Microsoft Azure | Microsoft Docs
description: "Découvrez les configurations prises en charge et les limitations des solutions Oracle sur Microsoft Azure."
services: virtual-machines-linux
documentationcenter: 
manager: timlt
author: rickstercdn
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: rclaus
ms.openlocfilehash: 1bc03d15096e7f1d4538d6642a61aaee9bb572f7
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Solutions Oracle et leur déploiement sur Microsoft Azure
Cet article fournit les informations requises pour déployer correctement différentes solutions Oracle sur Microsoft Azure. Ces solutions sont basées sur des images de machine virtuelle publiées par Oracle sur la Place de marché Microsoft Azure. Pour obtenir la liste des images actuellement disponibles, exécutez la commande suivante :
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
Depuis le 6-16-2017, la liste des images est la suivante :
```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Linux            Oracle       6.4                     Oracle:Oracle-Linux:6.4:6.4.20141206                         6.4.20141206
Oracle-Linux            Oracle       6.7                     Oracle:Oracle-Linux:6.7:6.7.20161007                         6.7.20161007
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20161020                         6.8.20161020
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20170406                         6.9.20170406
Oracle-Linux            Oracle       7.0                     Oracle:Oracle-Linux:7.0:7.0.20141217                         7.0.20141217
Oracle-Linux            Oracle       7.2                     Oracle:Oracle-Linux:7.2:7.2.20161020                         7.2.20161020
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20170320                         7.3.20170320
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Ces images sont considérées comme de type BYOL (apportez votre propre licence). Par conséquent, vous serez facturé uniquement pour les frais de calcul, de stockage et de mise en réseau induits par l’exécution d’une machine virtuelle.  Vous êtes supposé disposer des licences appropriées pour utiliser les logiciels Oracle, ainsi que d’un contrat de support avec Oracle. Oracle a garanti la mobilité des licence locale vers Azure. Pour plus d’informations sur la mobilité des licences, voir la note publiée [Oracle et Microsoft](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

Les utilisateurs peuvent également choisir de baser leurs solutions sur une image personnalisée qu’ils créent de toutes pièces dans Azure, ou de charger des images personnalisées à partir de leur environnement local.

## <a name="support-for-jd-edwards"></a>Prise en charge de JD Edwards
Selon la note de prise en charge d’Oracle [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), JD Edwards Enterprise One versions 9.2 et ultérieures est pris en charge sur **toute offre de cloud public** conforme aux `Minimum Technical Requirements` (MTR).  Vous devez créer des images personnalisées répondant à leurs spécifications MTR pour la compatibilité des applications logicielles et des systèmes d’exploitation. 

## <a name="oracle-database-virtual-machine-images"></a>Images de machines virtuelles Oracle Database
Oracle prend en charge l’exécution des éditions Standard d’Oracle DB 12.1 dans Azure sur des images de machine virtuelle basées sur Oracle Linux.  Pour optimiser les performances des charges de travail de production des bases de données Oracle sur Azure, veillez à dimensionner correctement l’image de machine virtuelle et à utiliser des disques gérés (Managed Disks) sauvegardés par Stockage Premium. Pour obtenir des instructions sur la façon d’obtenir rapidement une base de données Oracle opérationnelle dans Azure à l’aide de l’image de machine virtuelle publiée d’Oracle, [essayez de suivre la procédure de démarrage rapide de base de données Oracle](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Options de configuration des disques connectés

Les disques attachés s’appuient sur le service de stockage d’objets blob Microsoft Azure. En théorie, chaque disque standard peut proposer un maximum de 500 entrées/sorties par seconde (IOPS). Notre offre de disque Premium est conseillée pour les charges de travail de base de données hautes performances et peut atteindre jusqu’à 5 000 IOPS par disque. Même si vous pouvez utiliser un seul disque si cela correspond à vos besoins en termes de performances, vous pouvez souvent améliorer les performances d’IOPS effectives si vous utilisez plusieurs disques attachés, répartissez les données de la base de données sur ces disques, puis lancez ASM (Oracle Automatic Storage Management). Pour plus d’informations spécifiques d’Oracle ASM, voir [vue d’ensemble du stockage automatique Oracle](http://www.oracle.com/technetwork/database/index-100339.html). Pour un exemple d’installation et de configuration d’Oracle ASM sur une machine virtuelle Linux Azure, suivez le didacticiel relatif à [l’installation et à la configuration d’Oracle Automated Storage Management](configure-oracle-asm.md).

## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle RAC est conçu pour atténuer les conséquences de la défaillance d’un nœud dans une configuration locale de cluster à plusieurs nœuds. Il s’appuie sur deux technologies locales qui ne sont pas natives d’environnements de cloud public hyperscale : réseau multidiffusion et disque partagé. Si votre solution de base de données nécessite Oracle RAC dans Azure, vous avez besoin d’un logiciel tiers pour activer ces technologies.  Une offre **Microsoft Azure Certified** appelée [FlashGrid Node for Oracle RAC](https://azuremarketplace.microsoft.com/marketplace/apps/flashgrid-inc.flashgrid-racnode?tab=Overview) (publiée par FlashGrid Inc) est disponible sur la Place de marché Azure. Pour plus d’informations sur cette solution et sur son fonctionnement dans Azure, consultez la [page de la solution FlashGrid](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Remarques relatives à la haute disponibilité et à la récupération d’urgence
Lors de l’utilisation de bases de données Oracle dans Azure, vous êtes responsable de l’implémentation d’une solution de haute disponibilité et récupération d’urgence pour éviter tout temps d’arrêt. 

Pour assurer la haute disponibilité et la récupération d’urgence pour Oracle Database Enterprise Edition (sans reposer sur RAC) sur Microsoft Azure, vous pouvez utiliser [Data Guard, Active Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) ou [Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate), en plaçant deux bases de données sur deux machines virtuelles distinctes. Ces deux machines doivent se trouver dans le même [réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/), afin de pouvoir être accessibles l’une à l’autre via l’adresse IP privée persistante.  En outre, nous vous recommandons de placer les machines virtuelles au sein du même groupe à haute disponibilité, afin de permettre à Azure de les placer dans des domaines d’erreur et de mise à niveau distincts.  Si vous souhaitez disposer de la géo-redondance, vous pouvez faire en sorte que ces deux bases de données soient répliquées dans deux régions différentes, et connecter les deux instances avec une passerelle VPN.

Nous proposons un didacticiel « [Implémenter Oracle DataGuard sur Azure](configure-oracle-dataguard.md) », qui vous guide dans la procédure d’installation de base pour évaluer cette solution sur Azure.  

Grâce à Oracle Data Guard, vous pouvez assurer la haute disponibilité du système en plaçant la base de données primaire sur une machine virtuelle et une base de données secondaire (de veille) sur une autre, et en configurant une réplication monodirectionnelle entre ces bases de données. Vous bénéficiez ainsi d’un accès en lecture à la copie de la base de données. Oracle GoldenGate vous permet de configurer une réplication bidirectionnelle entre les deux bases de données. Pour savoir comment configurer une solution de haute disponibilité pour vos bases de données en utilisant ces outils, voir la documentation relative à [Active Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) et [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) sur le site web d’Oracle. Si vous avez besoin d’un accès en lecture et en écriture à la copie de la base de données, vous pouvez utiliser [Oracle Active Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Nous proposons un didacticiel « [Implémenter Oracle GoldenGate sur Azure](configure-oracle-golden-gate.md) », qui vous guide dans la procédure d’installation de base pour évaluer cette solution sur Azure.

Bien qu’une solution de haute disponibilité et récupération d’urgence soit architecturée dans Azure, vous devez veiller à avoir une stratégie de sauvegarde en place pour restaurer votre base de données.  Nous proposons un didacticiel [Sauvegarde et restauration d’une base de données Oracle](oracle-backup-recovery.md), qui vous guide tout au long de la procédure de base pour l’établissement d’une sauvegarde cohérente.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Images de machines virtuelles Oracle WebLogic Server
* **Le clustering est pris en charge sur la version Enterprise Edition uniquement.** Vous disposez d’une licence pour utiliser le clustering WebLogic uniquement lorsque vous utilisez l’édition Enterprise de WebLogic Server. N’utilisez pas le clustering avec WebLogic Server Standard Edition.
* **La multidiffusion UDP n’est pas gérée.** Azure prend en charge la monodiffusion UDP, mais ni la diffusion, ni la multidiffusion. WebLogic Server est capable de s’appuyer sur les fonctionnalités de monodiffusion UDP d’Azure. Pour optimiser les résultats lorsque vous recourez à la monodiffusion UDP, nous vous recommandons de conserver une taille de cluster statique, ou de maintenir ce dernier à une taille maximale de 10 serveurs gérés.
* **WebLogic Server attend des ports publics et privés identiques pour l’accès T3 (par exemple, lorsque vous utilisez Enterprise JavaBeans).** Imaginez un scénario à plusieurs niveaux, dans lequel une application de couche de service (EJB) est en cours d’exécution sur un cluster WebLogic Server comprenant deux serveurs gérés ou plus, au sein d’un réseau virtuel nommé **SLWLS**. Le niveau client se trouve dans un autre sous-réseau du même réseau virtuel, qui exécute un programme Java simple s’efforçant d’appeler Enterprise JavaBeans dans la couche de service. Comme il est nécessaire d’équilibrer la couche de service, un point de terminaison public avec équilibrage de charge doit être créé pour les machines virtuelles du cluster WebLogic Server. Si le port privé que vous spécifiez est différent du port public (par exemple, 7006:7008), une erreur semblable à la suivante se produit :

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   En effet, WebLogic Server attend un port d’équilibreur de charge et un port de serveur géré WebLogic identiques pour tout accès T3 distant. Dans le cas précédent, le client accède au port 7006 (port d’équilibreur de charge) et le serveur managé est à l’écoute sur le port 7008 (port privé). Cette restriction porte uniquement sur l’accès T3, et non sur le protocole HTTP.

   Pour éviter ce problème, utilisez l’une des solutions de contournement suivantes :

  * Utilisez les mêmes numéros de port public et privé pour les points de terminaison avec équilibrage de charge dédiés à l’accès T3.
  * Incluez le paramètre JVM suivant lors du démarrage de WebLogic Server :

         -Dweblogic.rjvm.enableprotocolswitch=true

Pour plus d’informations, voir l’article **860340.1** à l'adresse <http://support.oracle.com>.

* **Limitations relatives à l’équilibrage de charge et au clustering dynamique.** Partons du principe que vous souhaitez utiliser un cluster dynamique dans WebLogic Server et l’exposer en tant que point unique de terminaison public avec équilibrage de charge. Cette opération est possible tant que vous utilisez un numéro de port fixe pour chaque serveur géré (et non affecté de manière dynamique à partir d’une plage de valeurs), et que le nombre de serveurs gérés démarrés reste inférieur à celui des machines surveillées par l’administrateur (en d’autres termes, il faut un seul serveur géré par machine virtuelle). Si, suite à votre configuration, le nombre de serveurs WebLogic démarrés est supérieur à celui des machines virtuelles (c’est-à-dire si plusieurs instances de WebLogic Server partagent la même machine virtuelle), une seule de ces instances de serveurs WebLogic peut être liée à un numéro de port donné à la fois ; les autres instances de la machine virtuelle échouent.

   Si vous configurez le serveur d’administration de façon à affecter automatiquement des numéros de port uniques à ses serveurs managés, aucun équilibrage de charge n’est possible, car Microsoft Azure ne prend pas en charge le mappage d’un port public unique à plusieurs ports privés (opération alors requise pour cette configuration).
* **Exécution de plusieurs instances WebLogic sur une seule machine virtuelle.** Selon les exigences liées au déploiement, peut-être envisagerez-vous d’exécuter plusieurs instances WebLogic Server sur la même machine virtuelle, si la taille de cette dernière est suffisante. Ainsi, sur une machine virtuelle de taille moyenne contenant deux cœurs, vous pouvez opter pour l’exécution de deux instances WebLogic Server. Toutefois, nous vous recommandons malgré tout d’éviter toute introduction de points de défaillance uniques dans votre architecture (situation susceptible de survenir si vous utilisez une seule machine virtuelle exécutant plusieurs instances WebLogic Server). L’utilisation d’un minimum de deux machines virtuelles peut s’avérer plus efficace ; chaque machine virtuelle peut alors exécuter plusieurs instances WebLogic Server. Chacune de ces instances peut continuer de faire partie du même cluster. Toutefois, il n’est actuellement pas possible d’utiliser Microsoft Azure pour équilibrer la charge des points de terminaison exposés par des déploiements WebLogic Server de ce type au sein d’une même machine virtuelle. En effet, l’équilibreur de charge Microsoft Azure requiert la répartition des serveurs avec équilibrage de charge sur différentes machines virtuelles uniques.

## <a name="oracle-jdk-virtual-machine-images"></a>Images de machine virtuelle JDK Oracle
* **Dernières mises à jour de JDK 6 et 7.** Nous recommandons d’utiliser la dernière version publique de Java prise en charge (il s’agit actuellement de la version 8). Cependant, Azure propose également des images de JDK 6 et 7. Ces images sont destinées aux applications héritées qui ne peuvent pas encore être mises à niveau vers la version 8 de JDK. Il se peut que les mises à jour des images JDK précédentes ne soient plus mises à la disposition du public. Toutefois, suite au partenariat entre Microsoft et Oracle, les images de JDK 6 et 7 proposées par Microsoft Azure ont pour objectif de contenir une mise à jour plus récente, non destinée au public, qui est généralement proposée par Oracle à certains clients spécifiques bénéficiant d’un support Oracle. De nouvelles versions des images de JDK seront mises à disposition au fur et à mesure, en parallèle avec les versions de JDK 6 et 7 mises à jour.

   Le JDK disponible dans ces images de JDK 6 et 7, ainsi que les machines virtuelles et les images dérivées, peuvent uniquement être utilisées au sein d’Azure.
* **JDK 64 bits.** Les images de machine virtuelle Oracle WebLogic Server et les images de machine virtuelle Oracle JDK fournies par Microsoft Azure contiennent les versions 64 bits de Windows Server et de JDK.

## <a name="next-steps"></a>Étapes suivantes
Vous avez maintenant une vue d’ensemble des Solutions Oracle actuelles sur Microsoft Azure. L’étape suivante consiste à déployer votre première base de données Oracle sur Azure.
- Pour commencer, essayez de suivre le didacticiel [Créer une base de données Oracle sur Azure](oracle-database-quick-create.md).