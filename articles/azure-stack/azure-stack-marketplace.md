---
title: "Publier un élément de Place de Marché personnalisé dans Azure Stack (opérateur cloud) | Microsoft Docs"
description: "En tant qu’opérateur Azure Stack, découvrez comment publier un élément de Place de Marché personnalisé dans Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: c791708e11b7e9e8bbe046f06233d948d4632c90
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="the-azure-stack-marketplace-overview"></a>Vue d’ensemble de Place de Marché Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Place de Marché est une collection de services, d’applications et de ressources personnalisés pour Azure Stack, tels que des réseaux, des machines virtuelles, du stockage, et ainsi de suite. Les utilisateurs s’y rendent pour créer des ressources et déployer de nouvelles applications. Il s’agit en quelque sorte d’un catalogue où les utilisateurs peuvent choisir les éléments qu’ils souhaitent utiliser. Pour utiliser un élément de Place de Marché, les utilisateurs doivent s’abonner à une offre qui leur accorde l’accès à l’élément.

En tant qu’opérateur Azure Stack, c’est vous qui décidez des éléments à ajouter (publier) dans Place de Marché. Vous pouvez publier des éléments tels que des bases de données, App Services, et ainsi de suite. Cela les rend visibles par tous les utilisateurs. Vous pouvez publier des éléments personnalisés que vous créez. Vous pouvez également publier des éléments à partir d’une [liste croissante d’éléments de Place de Marché Azure](azure-stack-marketplace-azure-items.md). Quand vous publiez un élément sur Place de Marché, les utilisateurs peuvent le voir dans les cinq minutes qui suivent.

Pour ouvrir Marketplace, cliquez sur **Nouveau**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Éléments du Marketplace
Un élément Place de Marché Azure Stack est un service, une application ou une ressource que vos utilisateurs peuvent télécharger et utiliser. Tous les éléments de Place de Marché Azure Stack sont visibles par tous les utilisateurs.

Chaque élément du Marketplace comporte :

* Modèle Azure Resource Manager pour l’approvisionnement de la ressource
* Les métadonnées telles que les chaînes, les icônes et autres supports marketing
* Des informations de mise en forme permettant d’afficher l’élément dans le portail

Chaque élément publié sur Marketplace utilise un format appelé Azure Gallery Package (azpkg). Ajoutez les ressources de déploiement ou d’exécution (par exemple du code, des fichiers zip avec des logiciels, ou des images de machine virtuelle) à Azure Stack séparément, et non dans le cadre de l’élément de Place de Marché. 

## <a name="next-steps"></a>Étapes suivantes
[Créer et publier un élément de Place de Marché](azure-stack-create-and-publish-marketplace-item.md)

