---
title: Options de calcul Azure - Cloud Services | Microsoft Docs
description: "Découvrez les options hébergement de calcul Azure et leur fonctionnement : App Service, Cloud Services et Virtual Machines"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 17ecf39128994dad93f017f87f105254f3017230
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="should-i-choose-cloud-services-or-something-else"></a>Dois-je choisir Cloud Services ou un autre service ?
Azure Cloud Services est-il fait pour vous ? Azure propose différents modèles d’hébergement d’applications. Chaque modèle fournit un ensemble différent de services, donc votre choix dépend exactement ce que vous essayez de faire.

[!INCLUDE [compute-table](../../includes/compute-options-table.md)]

<a name="tellmecs"></a>

## <a name="tell-me-about-cloud-services"></a>En savoir plus sur Cloud Services
Cloud Services est un exemple de [platform as a service](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Tout comme [App Service](../app-service/app-service-web-overview.md), cette technologie est conçue pour prendre en charge des applications évolutives, fiables et dont l’exploitation est peu coûteuse. Tout comme App Service, Cloud Services est hébergé sur des machines virtuelles, bien que celui-ci vous permette de mieux les contrôler. Vous pouvez installer votre logiciel sur des machines virtuelles de Cloud Service et vous y connecter à distance.

![cs_diagram](./media/cloud-services-choose-me/diagram.png)

Un contrôle supérieur signifie également une convivialité réduite. Si vous n’avez pas besoin d’options de contrôle supplémentaires, il est généralement plus rapide et plus facile de faire fonctionner une application web dans Web Apps dans App Service qu’avec Cloud Services.

Il existe deux types de rôle de service cloud. La seule différence entre les deux est le mode d’hébergement de votre rôle sur la machine virtuelle.

* **Rôle Web**  
Déploie et héberge automatiquement votre application, via IIS.

* **Rôle de travail**  
Exécute votre application de façon autonome, sans utiliser IIS.

Par exemple, une application simple peut utiliser un rôle web unique, servant un site web. À l’inverse, une application plus complexe peut utiliser un rôle web pour traiter les requêtes entrantes des utilisateurs, puis transmettre ces requêtes à un rôle de travail, à des fins de traitement. (Cette communication pourrait utiliser [Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) ou les [files d’attente Azure](../storage/common/storage-introduction.md).)

Ainsi que le suggère la figure ci-dessus, toutes les machines virtuelles d’une même application s’exécutent dans le même service cloud. Les utilisateurs accèdent à l’application par le biais d’une seule adresse IP publique, avec un équilibrage automatique de la charge des requêtes entre les machines virtuelles de cette application. La plateforme [met à l’échelle et déploie](cloud-services-how-to-scale-portal.md) les machines virtuelles dans une application Cloud Services, afin d’éviter un point unique de défaillance matérielle.

Même si les applications s'exécutent sur des machines virtuelles, il est important de comprendre que Cloud Services fournit l'option PaaS, et non IaaS. Le processus peut être décrit de la manière suivante : avec la technologie IaaS (par exemple avec Azure Virtual Machines), vous commencez par créer et configurer l’environnement dans lequel s’exécutera votre application, puis vous déployez votre application dans cet environnement. Il vous appartient de gérer une grande partie de ce monde, par exemple, en déployant de nouvelles versions corrigées du système d'exploitation dans chaque machine virtuelle. Avec PaaS, en revanche, c'est comme si l'environnement existait déjà. Vous n'avez plus qu'à déployer votre application. La gestion de la plateforme sur laquelle elle s'exécute, y compris le déploiement de nouvelles versions du système d'exploitation, est assurée à votre place.

## <a name="scaling-and-management"></a>Mise à l'échelle et gestion
Avec Cloud Services, vous ne créez pas de machines virtuelles. Au contraire, vous fournissez un fichier de configuration qui indique à Azure le nombre d'instances de chaque type voulu, par exemple, **trois instances de rôle Web** et **deux instances de rôle de travail**, et la plateforme se charge de les créer pour vous.  Vous continuez de choisir [la taille](cloud-services-sizes-specs.md) que doivent avoir ces machines virtuelles de stockage, mais vous ne les créez pas vous-même de manière explicite. Si votre application a besoin de traiter une charge plus importante, vous pouvez demander davantage de machines virtuelles ; Azure créera ces instances. Si la charge diminue, vous pouvez arrêter ces instances et cesser de payer pour elles.

Une application Cloud Services est normalement mise à la disposition des utilisateurs au moyen d'un processus en deux étapes. Un développeur [télécharge d'abord l'application](cloud-services-how-to-create-deploy-portal.md) dans la zone de transit de la plateforme. Lorsqu’il est prêt à activer l’application, il utilise le portail de gestion Azure pour la faire passer de l’étape intermédiaire à l’environnement de production. Le [basculement entre la zone de transit et l'environnement de production](cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production) peut se faire sans temps d'arrêt, ce qui permet de mettre à niveau une application en cours d'exécution vers une nouvelle version sans perturbation pour les utilisateurs.

## <a name="monitoring"></a>Surveillance
Cloud Services fournit également la surveillance. À l’instar d’Azure Virtual Machines, il détecte l’échec d’un serveur physique et redémarre sur un nouvel ordinateur les machines virtuelles qui étaient exécutées sur ce serveur. Mais Cloud Services détecte aussi les échecs des machines virtuelles et des applications, et pas seulement les défaillances matérielles. À la différence des machines virtuelles, ils ont un agent à l'intérieur de chaque rôle Web et de travail, et sont donc en mesure de démarrer de nouvelles instances des machines virtuelles et des applications lorsqu'une défaillance se produit.

La nature PaaS de Cloud Services a également d'autres implications. L'une des principales est le fait que les applications basées sur cette technologie doivent être écrites pour s'exécuter correctement en cas d'échec d'un rôle Web ou de travail. Cela nécessite qu'une application Cloud Services ne conserve pas l'état dans le système de fichiers de ses propres machines virtuelles. Les écritures dans les machines virtuelles Cloud Services ne sont pas persistantes, comme elles le sont dans les machines virtuelles créées avec Azure Virtual Machines ; il n'existe rien de tel qu'un disque de données des machines virtuelles. En revanche, une application Cloud Services doit écrire explicitement tous les états dans la base de données SQL, les objets blob, les tables ou un autre stockage externe. Les applications créées de la sorte sont plus faciles à mettre à l'échelle et davantage résistantes aux défaillances, deux objectifs importants de Cloud Services.

## <a name="next-steps"></a>Étapes suivantes
[Créer une application de service cloud dans .NET](cloud-services-dotnet-get-started.md)  
[Créer une application de service cloud dans Node.js](cloud-services-nodejs-develop-deploy-app.md)  
[Créer une application de service cloud dans PHP](../cloud-services-php-create-web-role.md)  
[Création d’une application de service cloud dans Python](cloud-services-python-ptvs.md)

