---
title: "Contrôler le comportement de mise en cache d’Azure Content Delivery Network avec des chaînes de requête | Microsoft Docs"
description: "La mise en cache des chaînes de requête Azure CDN contrôle la manière dont les fichiers doivent être mis en cache lorsqu’ils contiennent des chaînes de requête."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 9ffd05a0eb4d976dc40a1c5d45fd22ebf9bd4db1
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings"></a>Contrôler le comportement de mise en cache d’Azure Content Delivery Network à l’aide de chaînes de requête
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [CDN Azure Premium fourni par Verizon](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Vue d'ensemble
Avec Azure Content Delivery Network (CDN), vous pouvez contrôler la manière dont les fichiers sont mis en cache pour une requête web qui contient une chaîne de requête. Dans une requête web contenant une chaîne de requête, la chaîne de requête représente la partie de la demande qui apparaît après le point d’interrogation (?). Une chaîne de requête peut contenir une ou plusieurs paires clé-valeur où le nom du champ et sa valeur sont séparés par un signe égal (=). Chaque paire clé-valeur est séparée par une esperluette (&). Par exemple, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. S’il existe plusieurs paires clé-valeur dans la chaîne de requête d’une demande, leur ordre n’a pas d’importance. 

> [!IMPORTANT]
> Les produits CDN standard et premium proposent les mêmes fonctionnalités de mise en cache des chaînes de requête, mais l’interface utilisateur est différente.  Cet article décrit l’interface **Azure CDN Standard fourni par Akamai** et **Azure CDN Standard fourni par Verizon**. Pour la mise en cache des chaînes de requête avec le **CDN Azure Premium fourni par Verizon**, consultez [Contrôle du comportement de mise en cache des demandes CDN avec des chaînes de requête – Premium](cdn-query-string-premium.md).

Trois modes de chaîne de requête sont disponibles :

- **Ignorer les chaînes de requête** : mode par défaut. Dans ce mode, le nœud de périmètre CDN transmet les chaînes de requête, du demandeur à l’origine de la première demande, et met en cache la ressource. Toutes les demandes ultérieures pour la ressource, qui sont traitées à partir du nœud de périmètre, ignorent les chaînes de requête jusqu’à l’arrivée à expiration de la ressource mise en cache.
- **Contourner la mise en cache des chaînes de requête** : dans ce mode, les demandes avec des chaînes de requête ne sont pas mises en cache au niveau du nœud de périmètre CDN. Le nœud de périmètre récupère l’élément multimédia directement à partir de l’origine et le transmet au demandeur avec chaque demande.
- **Mettre en cache chaque URL unique** : dans ce mode, chaque demande contenant une URL unique, y compris la chaîne de requête, est traitée comme une ressource unique avec son propre cache. Par exemple, la réponse depuis l’origine d’une demande pour `example.ashx?q=test1` est mise en cache au niveau du nœud de périmètre et retournée pour les caches suivants avec la même chaîne de requête. Une demande pour `example.ashx?q=test2` est mise en cache en tant que ressource distincte avec son propre paramètre de durée de vie.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Modification des paramètres de mise en cache des chaînes de requête pour les profils CDN Standard
1. Ouvrez un profil CDN, puis sélectionnez le point de terminaison CDN que vous souhaitez gérer.
   
   ![Points de terminaison du profil CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. Dans le volet gauche, sous Paramètres, cliquez sur **Règles de mise en cache**.
   
    ![Bouton Règles de mise en cache CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Dans la liste **Comportement de mise en cache des chaînes de requête**, sélectionnez un mode de chaîne de requête, puis cliquez sur **Enregistrer**.
   
   ![Options de mise en cache des chaînes de requête CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> La propagation de l’inscription dans CDN prenant un certain temps, la modification des paramètres des chaînes mises en cache peut ne pas être visible immédiatement. Pour les profils du **CDN Azure fourni par Akamai** , la propagation s’effectue généralement dans un délai d’une minute. Pour les profils du **CDN Azure fourni par Verizon**, la propagation s’effectue généralement dans un délai de 90 minutes, mais elle peut prendre plus de temps dans certains cas.


