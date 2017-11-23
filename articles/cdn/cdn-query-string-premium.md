---
title: "Contrôle du comportement de mise en cache du CDN Azure avec des chaînes de requête - Premium | Microsoft Docs"
description: "La mise en cache des chaînes de requête CDN Azure contrôle la manière dont les fichiers doivent être mis en cache lorsqu’ils contiennent des chaînes de requête."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: ba9c28f0e6df25b101b45edf836d0b95056cbc6f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings---premium"></a>Contrôler le comportement de mise en cache d’Azure Content Delivery Network à l’aide de chaînes de requête - Premium
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [CDN Azure Premium fourni par Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Vue d'ensemble
Avec Azure Content Delivery Network (CDN), vous pouvez contrôler la manière dont les fichiers sont mis en cache pour une requête web qui contient une chaîne de requête. Dans une requête web contenant une chaîne de requête, la chaîne de requête représente la partie de la demande qui survient après le caractère `?`. Une chaîne de requête peut contenir un ou plusieurs paramètres séparés par un caractère `&`. Par exemple, `http://www.domain.com/content.mov?data1=true&data2=false`. S’il existe plusieurs paramètres de chaîne de requête dans une demande, l’ordre des paramètres n’a pas d’importance. 

> [!IMPORTANT]
> Les produits CDN standard et premium proposent les mêmes fonctionnalités de mise en cache des chaînes de requête, mais l’interface utilisateur est différente.  Cet article décrit l’interface d’**Azure CDN Premium fourni par Verizon**. Pour la mise en cache des chaînes de requête avec le **CDN Azure Standard fourni par Akamai** et le **CDN Azure Standard fourni par Verizon**, consultez [Contrôle du comportement de mise en cache des demandes CDN avec des chaînes de requête](cdn-query-string.md).
>

Trois modes de chaîne de requête sont disponibles :

- **cache standard** : mode par défaut. Dans ce mode, le nœud de périmètre CDN transmet les chaînes de requête, du demandeur à l’origine de la première demande, et met en cache la ressource. Toutes les demandes ultérieures pour la ressource, qui sont traitées à partir du nœud de périmètre, ignorent les chaînes de requête jusqu’à l’arrivée à expiration de la ressource mise en cache.
- **no-cache**: dans ce mode, les demandes avec des chaînes de requête ne sont pas mises en cache au niveau du nœud de périmètre CDN. Le nœud de périmètre récupère l’élément multimédia directement à partir de l’origine et le transmet au demandeur avec chaque demande.
- **cache unique** : dans ce mode, chaque demande contenant une URL unique, y compris la chaîne de requête, est traitée comme une ressource unique avec son propre cache. Par exemple, la réponse depuis l’origine d’une demande pour `example.ashx?q=test1` est mise en cache au niveau du nœud de périmètre et retournée pour les caches suivants avec la même chaîne de requête. Une demande pour `example.ashx?q=test2` est mise en cache en tant que ressource distincte avec son propre paramètre de durée de vie.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Modification des paramètres de mise en cache des chaînes de requête pour les profils CDN Premium
1. Ouvrez un profil CDN, puis cliquez sur **Gérer**.
   
    ![Bouton Gérer du profil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Le portail de gestion CDN s'ouvre.
2. Pointez sur l’onglet **HTTP volumineux**, puis pointez sur le menu volant **Paramètres de cache**. Cliquez sur **Mise en cache des chaînes de requête**.
   
    Les options de mise en cache des chaînes de requête s'affichent.
   
    ![Options de mise en cache des chaînes de requête CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Sélectionnez un mode de chaîne de requête, puis cliquez sur **Mettre à jour**.

> [!IMPORTANT]
> La propagation de l’inscription dans CDN prenant un certain temps, la modification des paramètres des chaînes mises en cache peut ne pas être visible immédiatement. Pour les profils **Azure CDN Premium fourni par Verizon**, la propagation s’effectue généralement dans un délai de 90 minutes, mais elle peut prendre plus de temps dans certains cas.
 

