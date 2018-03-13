---
title: "Planification de la capacité pour les rôles serveur Azure App Service dans Azure Stack | Microsoft Docs"
description: "Planification de la capacité pour les rôles serveur Azure App Service dans Azure Stack"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 8926955d5e0260b5971e07b6988bb21df9980847
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Planification de la capacité pour les rôles serveur Azure App Service dans Azure Stack
*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Pour approvisionner un déploiement prêt pour la production d’Azure App Service sur Azure Stack, vous devez planifier la capacité que vous attendez que le système prenne en charge.  Voici des conseils sur le nombre minimal d’instances et de références SKU de calcul à utiliser pour un déploiement de production.

Vous pouvez planifier votre stratégie de capacité App Service à l’aide de ces instructions. Les versions ultérieures d’Azure Stack offre des options de haute disponibilité pour App Service.

| Rôle serveur App Service | Nombre d’instances minimal recommandé | Référence SKU de calcul recommandée|
| --- | --- | --- |
| Controller | 2 | A1 |
| Serveur frontal | 2 | A1 |
| gestion | 2 | A3 |
| Publisher | 2 | A1 |
| Rôles de travail - partagés | 2 | A1 |
| Rôles de travail - dédiés | 2 par niveau | A1 |

## <a name="controller-role"></a>rôle de contrôleur

**Minimum recommandé** : deux instances de A1 standard

Le contrôleur Azure App Service consomme généralement peu d’UC, de mémoire et de ressources réseau. Toutefois, pour la haute disponibilité, vous devez disposer de deux contrôleurs. Deux contrôleurs sont également le nombre maximal de contrôleurs autorisé. Vous pouvez créer le second contrôleur de sites web directement à partir du programme d’installation lors du déploiement.

## <a name="front-end-role"></a>Rôle frontal

**Minimum recommandé** : deux instances de A1 standard

Le serveur frontal achemine les demandes vers les rôles de travail en fonction de la disponibilité du traitement Web. Pour une haute disponibilité, vous devez disposer de plusieurs serveurs frontaux. À des fins de planification de la capacité, notez que chaque cœur peut traiter approximativement 100 demandes par seconde.

## <a name="management-role"></a>Rôle de gestion

**Minimum recommandé** : deux instances de A3

Le rôle Gestion Azure App Service est responsable des points de terminaison App Service Azure Resource Manager et d’API, des extensions du portail (admin, locataire, portail Functions) et du service des données. Le rôle de serveur de gestion nécessite généralement uniquement 4 Go de RAM environ dans un environnement de production. Toutefois, l’utilisation de l’UC peut augmenter lorsque de nombreuses tâches de gestion (par exemple, la création de sites web) sont effectuées. Pour une haute disponibilité, vous devez disposer de plusieurs serveurs affectés à ce rôle, et au moins deux cœurs par serveur.

## <a name="publisher-role"></a>Rôle de serveur de publication

**Minimum recommandé** : deux instances de A1

Si de nombreux utilisateurs publient simultanément, le rôle de serveur de publication peut faire face à une utilisation élevée du processeur. Pour une haute disponibilité, mettez plusieurs rôles de serveur de publication à disposition.  Le serveur de publication gère uniquement le trafic FTP/FTPS.

## <a name="web-worker-role"></a>Rôle de travail

**Minimum recommandé** : deux instances de A1

Pour une haute disponibilité, vous devez disposer d’au moins quatre rôles de travail, deux pour le mode de site web partagé et deux pour chaque niveau de travail dédié que vous prévoyez de proposer. Les modes de calcul partagé et dédié offrent différents niveaux de service aux locataires. Vous aurez peut-être besoin de rôles de travail supplémentaires si vous avez de nombreux clients :
 - utilisant des niveaux de travail en mode de calcul dédiés (qui sont gourmands en ressources)
 - s’exécutant en mode de calcul partagé.

Après qu’un utilisateur a créé un Plan App Service pour une référence SKU en mode de calcul dédiée, le nombre de rôles de travail spécifiés dans ce Plan App Service ne sera plus disponible pour les utilisateurs.

Pour fournir Azure Functions aux utilisateurs dans le modèle de plan de consommation, vous devez déployer des rôles de travail partagés.

Lorsque vous choisissez le nombre de rôles de travail partagés à utiliser, passez en revue ces considérations :

- **Mémoire** : la mémoire est la ressource la plus critique pour un rôle de travail. Une mémoire insuffisante a un impact sur les performances du site web lorsque la mémoire virtuelle est échangée à partir du disque. Chaque serveur nécessite environ 1,2 Go de RAM pour le système d’exploitation. La RAM au-dessus de ce seuil peut être utilisée pour exécuter des sites web.
- **Pourcentage de sites web actifs** : en général, environ 5 pourcent des applications d’un service Azure App Service sur un déploiement Azure Stack sont actives. Toutefois, le pourcentage d’applications actives à un moment donné peut être supérieur ou inférieur. Avec un taux d’applications actives de 5 %, le nombre maximal d’applications à placer dans un Azure App Service sur un déploiement Azure Stack doit être inférieur à :
    - 20 fois le nombre de sites web actifs (5 x 20 = 100).
- **Encombrement de mémoire moyen** : l’encombrement de mémoire moyen pour les applications observées dans les environnements de production est d’environ 70 Mo. Par conséquent, la mémoire allouée sur l’ensemble des ordinateurs ou machines virtuelles du rôle de travail peut être calculée comme suit :

    *Nombre d’applications approvisionnées * 70 Mo * 5 % - (nombre de rôles de travail * 1 044 Mo)*

   Par exemple, s’il y a 5 000 applications dans un environnement qui exécute 10 rôles de travail, chaque machine virtuelle du rôle de travail doit disposer de 7 060 Mo de RAM :

   5 000 * 70 * 0,05 – (10 * 1 044) = 7 060 (= environ 7 Go)

   Pour plus d’informations sur l’ajout d’instances de travail supplémentaires, consultez [Ajout de rôles de travail supplémentaires](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Rôle de serveur de fichiers

Pour le rôle de serveur de fichiers, vous pouvez utiliser un serveur de fichiers autonome pour le développement et le test. Par exemple, lors du déploiement d’Azure App Service sur le Kit de développement Azure Stack, vous pouvez utiliser ce modèle : https://aka.ms/appsvconmasdkfstemplate. À des fins de production, vous devez utiliser un serveur de fichiers Windows préconfiguré ou un serveur de fichiers préconfiguré autre que Windows.

Dans les environnements de production, le rôle de serveur de fichiers fait face à des E/S de disque intensives. Étant donné qu’il héberge tous les fichiers de contenu et d’application des sites web des utilisateurs, vous devez préconfigurer un des éléments suivants pour ce rôle :
- Un serveur de fichiers Windows
- Un cluster de serveur de fichiers
- Un serveur de fichiers non Windows
- Un cluster de serveur de fichiers
- Un périphérique NAS (Network Attached Storage). Pour plus d’informations, consultez [Approvisionner un serveur de fichiers](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Étapes suivantes

[Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md)
