---
title: "Chaîne de connexion du magasin d’images Azure Service Fabric | Microsoft Docs"
description: "Comprendre la chaîne de connexion du magasin d’images"
services: service-fabric
documentationcenter: .net
author: alexwun
manager: timlt
editor: 
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 3c34a3851dbb5c5258b3dc0cf35a510f62cbe14e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Comprendre le paramètre ImageStoreConnectionString

Dans une partie de notre documentation, nous mentionnons brièvement l’existence d’un paramètre « ImageStoreConnectionString », sans décrire ce qu’il signifie vraiment. Et, après avoir parcouru un article comme [Déployer et supprimer des applications à l’aide de PowerShell][10], il semble que vous vous contentez de copier/coller la valeur telle qu’elle apparaît dans le manifeste du cluster cible. Le paramètre doit donc être configurable par cluster, mais, lorsque vous créez un cluster avec le [Portail Azure][11], il n’y a pas d’option permettant de configurer ce paramètre, qui est toujours « fabric:ImageStore ». Dans ce cas, quelle est l’utilité de ce paramètre ?

![Manifeste de cluster][img_cm]

Service Fabric était à l’origine une plateforme destinée à la consommation interne de Microsoft par de nombreuses équipes très diverses ; par conséquent, certains de ses aspects sont hautement personnalisables, et notamment le « magasin d’images ». En substance, le magasin d’images est un référentiel enfichable permettant de stocker des packages d’applications. Lorsque votre application est déployée sur un nœud du cluster, ce nœud télécharge le contenu du package de votre application à partir du magasin d’images. ImageStoreConnectionString est un paramètre qui comprend toutes les informations nécessaires pour que les clients et les nœuds trouvent le bon magasin d’images pour un cluster donné.

Il existe actuellement trois genres possibles de fournisseurs de magasins d’images ; les chaînes de connexion correspondantes sont les suivantes :

1. Service de magasin d’images : « fabric:ImageStore »

2. Système de fichiers : « file:[chemin d’accès du système de fichiers] »

3. Stockage Azure : « xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...] »

Le type de fournisseur utilisé en production est le service de magasin d’images, qui est un service système persistant avec état, visible dans Service Fabric Explorer. 

![Service de magasin d’images][img_is]

Le fait d’héberger le magasin d’images dans un service système au sein même du cluster élimine les dépendances externes du référentiel de packages et nous donne davantage de contrôle sur le lieu de stockage. Les améliorations à venir du magasin d’images cibleront probablement le fournisseur de magasin d’images en premier lieu, si ce n’est exclusivement. La chaîne de connexion du fournisseur de service de magasin d’images ne contient aucune information unique, dans la mesure où le client est déjà connecté au cluster cible. Le client a seulement besoin de savoir que les protocoles ciblant le service système doivent être utilisés.

Le fournisseur du système de fichiers est utilisé à la place du service de magasin d’images pour les clusters à boîtier unique locaux pendant le développement, de façon à démarrer le cluster un peu plus vite. La différence est généralement faible, mais c’est une optimisation utile à la plupart des gens lors du développement. Il est également possible de déployer un cluster à boîtier unique local avec les autres types de fournisseurs de stockage, mais il n’y a généralement aucune raison de le faire dans la mesure où le flux de travail de développement / test reste le même quel que soit le fournisseur. Le fournisseur de stockage Azure n’est disponible que pour la prise en charge héritée des anciens clusters ayant été déployés avant l’arrivée du fournisseur de service du magasin d’images.

En outre, ni le fournisseur du système de fichiers, ni le fournisseur de stockage Azure ne doivent être utilisés pour partager un magasin d’images entre plusieurs clusters. En effet, cela provoquerait l’endommagement des données de configuration de cluster, puisque chaque cluster peut écrire des données conflictuelles dans le magasin d’images. Pour partager des packages d’applications provisionnées entre plusieurs clusters, utilisez des fichiers [sfpkg][12], que vous pouvez charger vers n’importe quel magasin externe ayant un URI de téléchargement.

Par conséquent, si ImageStoreConnectionString est configurable, on utilise en général simplement le paramètre par défaut. En cas de publication sur Azure avec Visual Studio, le paramètre est automatiquement défini en conséquence. Pour un déploiement par programmation sur des clusters hébergés dans Azure, la chaîne de connexion est toujours « fabric:ImageStore ». En cas de doute, sa valeur peut toujours être vérifiée en récupérant le manifeste de cluster par [PowerShell](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx) ou [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Les clusters de test et de production locaux doivent également être toujours configurés pour utiliser le fournisseur de service de magasin d’images.

### <a name="next-steps"></a>Étapes suivantes
[Déployer et supprimer des applications avec PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
