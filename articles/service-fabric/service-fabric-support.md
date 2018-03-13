---
title: "En savoir plus sur les options de support d’Azure Service Fabric | Microsoft Docs"
description: Versions de clusters Azure Service Fabric prises en charge et liens pour soumettre des tickets de support
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/26/2018
ms.author: pkc
ms.openlocfilehash: 970f1ff59560beaf898f22eb4e701088a6d5902a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-service-fabric-support-options"></a>Options de support d’Azure Service Fabric

Pour assurer un support adapté des clusters Service Fabric sur lesquels vous exécutez vos charges de travail d’applications, nous avons mis différentes options à votre disposition. Vous pourrez choisir les options appropriées selon le niveau de support nécessaire et la gravité du problème. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Signaler des problèmes de production ou demander un support payant pour Azure

Pour signaler des problèmes sur votre cluster Service Fabric déployé sur Azure, ouvrez un ticket de support [sur le portail Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ou le [portail de support Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Pour en savoir plus :
 
- [Support Microsoft pour Azure](https://azure.microsoft.com/en-us/support/plans/?b=16.44).
- [Support Premier Microsoft](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Signaler des problèmes de production ou demander un support payant pour des clusters Service Fabric autonomes

Pour signaler des problèmes sur votre cluster Service Fabric déployé en local ou sur d’autres clouds, ouvrez un ticket de support professionnel sur le [portail de support Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Pour en savoir plus :

- [Support professionnel Microsoft local](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Support Premier Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Signaler des problèmes avec Azure Service Fabric 
Nous avons défini un référentiel GitHub pour signaler les problèmes de Service Fabric.  De plus, nous surveillons activement les forums suivants.

### <a name="github-repo"></a>Référentiel GitHub 
Signaler des problèmes avec Azure Service Fabric sur le [Référentiel Git des problèmes de Service Fabric](https://github.com/Azure/service-fabric-issues). Ce référentiel est destiné au signalement et au suivi des problèmes rencontrés avec Azure Service Fabric, ainsi qu’aux demandes de petites fonctionnalités. **Ne pas l’utiliser pour signaler des problèmes de site en ligne**.

### <a name="stackoverflow-and-msdn-forums"></a>Forums StackOverflow et MSDN
La [Balise Service Fabric sur StackOverflow][stackoverflow] et le [Forum Service Fabric sur MSDN][msdn-forum] sont recommandés pour poser des questions sur le fonctionnement de la plateforme et la réalisation de certaines tâches par son intermédiaire.

### <a name="azure-feedback-forum"></a>Forum de commentaires Azure
Le [Forum de commentaires Azure pour Service Fabric][uservoice-forum] est le meilleur endroit pour envoyer vos idées de fonctionnalités majeures pour le produit. Nous examinons en effet les requêtes les plus populaires dans le cadre de notre planification à moyen ou long terme. Nous vous encourageons à chercher l’appui de la communauté pour vos suggestions.


## <a name="supported-service-fabric-versions"></a>Versions de Service Fabric prises en charge.

Veillez à ce que votre cluster exécute toujours une version prise en charge de Service Fabric. Lorsque nous annonçons le lancement d’une nouvelle version de Service Fabric, la fin de la prise en charge de la version précédente est signalée 60 jours minimum après la date de lancement. Les nouvelles versions sont annoncées [sur le blog de l’équipe Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Consultez les documents suivants pour savoir plus en détails comment faire en sorte que votre cluster exécute toujours une version prise en charge de Service Fabric.

- [Mettre à niveau la version de Service Fabric sur un cluster Azure](service-fabric-cluster-upgrade.md)
- [Mettre à niveau la version de Service Fabric sur un cluster de serveurs Windows autonome](service-fabric-cluster-upgrade-windows-server.md)
 
Voici la liste des versions de Service Fabric prises en charge et la date associée de fin de prise en charge.

| **Runtime Service Fabric dans le cluster** | **Version de cluster à partir de laquelle une mise à niveau directe est possible** |**Kit de développement logiciel compatible / Versions de package NuGet** | **Date de fin de prise en charge** |
| --- | --- |--- | --- |
| Toutes les versions de clusters antérieures à 5.3.121 | 5.1.158* |Inférieure ou égale à la version 2.3 |20 janvier 2017 |
| 5.3.* | 5.1.158.* |Inférieure ou égale à la version 2.3 |24 février 2017 |
| 5.4.* | 5.1.158.* |Inférieure ou égale à la version 2.4 |10 mai 2017       |
| 5.5.* | 5.4.164.* |Inférieure ou égale à la version 2.5 |10 août 2017    |
| 5.6.* | 5.4.164.* |Inférieure ou égale à la version 2.6 |13 octobre 2017   |
| 5.7.* | 5.4.164.* |Inférieure ou égale à la version 2.7 |15 décembre 2017  |
| 6.0.* | 5.6.205.* |Inférieure ou égale à la version 2.8 |30 mars 2018     | 
| 6.1.* | 5.7.221.* |Inférieure ou égale à la version 3.0 |Version actuelle ; par conséquent, pas de date de fin |

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Versions préliminaires de Service Fabric – non pris en charge pour la production.
De temps à autre, nous publions des versions dotées de fonctionnalités significatives, sur lesquelles nous souhaitons avoir un retour et qui sont publiées sous forme de version préliminaire. Ces versions préliminaires doivent uniquement être utilisées à des fins de test. Votre cluster de production doit toujours exécuter une version de Service Fabric prise en charge et stable. Les versions préliminaires commencent toujours par un numéro de version majeure et mineure : 255. Par exemple, la version 255.255.5703.949 de Service Fabric ne doit être utilisée que dans les clusters de test. Il s’agit d’une version préliminaire. Ces versions préliminaires sont également annoncées sur le [blog de l’équipe Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) et s’accompagnent d’informations sur les fonctionnalités incluses.

Il n’existe aucune option de support technique payant pour ces versions préliminaires. Utilisez l’une des options répertoriées dans la rubrique [Signaler des problèmes Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) pour poser des questions ou laisser un commentaire.

## <a name="next-steps"></a>Étapes suivantes

- [Mettre à niveau la version de Service Fabric sur un cluster Azure](service-fabric-cluster-upgrade.md)
- [Mettre à niveau la version de Service Fabric sur un cluster de serveurs Windows autonome](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples
