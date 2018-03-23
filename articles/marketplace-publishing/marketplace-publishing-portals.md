---
title: Vue d’ensemble des divers portails nécessaires pour créer une offre sur la Place de marché | Microsoft Docs
description: Vue d'ensemble des divers portails nécessaires pour créer une offre sur Marketplace
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 89ce82b3-c28a-4b0d-b37a-db3112160a4e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: mbaldwin
ms.openlocfilehash: 663d9a01b80b0c41e37037b0cbec10c93511d24c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="portals-you-will-need"></a>Portails dont vous aurez besoin
Avant le lancement du processus de publication d’une offre, nous allons vous présenter les différents portails dont vous aurez besoin. Vous trouverez ci-dessous un résumé des portails (Centre de développement, portail de publication Azure et portail Azure) avec lesquels vous pourrez interagir.                                                                            

## <a name="developer-center"></a>Centre de développement
[http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)

### <a name="description"></a>Description
La création d’un compte auprès du Centre de développement Microsoft est une tâche ponctuelle. Assurez-vous que l’entreprise ne possède pas déjà de compte auprès du Centre de développement avant de tenter d’en créer un. Dans le cadre de ce processus, nous recueillons vos informations bancaires et fiscales, ainsi que l'adresse de votre entreprise.

> [!NOTE]
> Si vous publiez des offres gratuites uniquement (ou offres BYOL (apportez votre propre licence)), nous n'avons pas besoin de vos informations bancaires et fiscales.
> 
> 

### <a name="identityaccount-used"></a>Identité/compte utilisé
Dans l’idéal, il s’agit d’une liste de distribution ou d’un groupe de sécurité (par exemple, azurepublishing@*entreprisepartenaire*.com). Cette liste de distribution ou ce groupe de sécurité **doit** être enregistré en tant que compte Microsoft.

> [!TIP]
> Nous recommandons l'utilisation d'une liste de distribution ou d'un groupe de sécurité, car elle permet de supprimer la dépendance vis-à-vis de toute personne (même si un compte individuel peut également être utilisé).
> 
> 

## <a name="publishing-portal"></a>Portail de publication
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### <a name="description"></a>Description
Ce portail vous permet de développer votre offre et de la publier (marketing, tarification, publication, certification, le cas échéant, etc.).

### <a name="identityaccount-used"></a>Identité/compte utilisé
Le groupe de sécurité ou la liste de distribution ci-dessus est nécessaire pour la première connexion au portail de publication. Par la suite, d’autres utilisateurs pourront être ajoutés en tant que co-administrateurs. Voici comment il est mappé aux données d’inscription du Centre de développement.

## <a name="azure-portal"></a>Portail Azure
[https://portal.azure.com](https://portal.azure.com)

### <a name="description"></a>Description
Il s'agit du portail dans lequel vous pouvez afficher vos offres intermédiaires et publiées sur le Marketplace (applicable aux machines virtuelles, aux modèles de solution et aux services de développement basés sur ARM)

### <a name="identityaccount-used"></a>Identité/compte utilisé
Un ID d’abonnement figurant dans la liste approuvée est nécessaire lors de la création d'une offre intermédiaire depuis le portail de publication. Le même abonnement (avec un nom d'utilisateur et un mot de passe associé) doit être utilisé lors de la connexion à ce portail pour tester l'offre intermédiaire.

## <a name="see-also"></a>Voir aussi
* [Mise en route : publication d'une offre pour Azure Marketplace](marketplace-publishing-getting-started.md)

