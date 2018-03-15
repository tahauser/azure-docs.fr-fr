---
title: "Configurer un nom de domaine personnalisé pour une instance de gestion des API Azure | Microsoft Docs"
description: "Cette rubrique explique comment configurer un nom de domaine personnalisé pour votre instance de gestion des API Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 12/14/2017
ms.author: apimpm
ms.openlocfilehash: 96e233a26af95d4373323867046ca01fe1304608
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="configure-a-custom-domain-name"></a>Configuration d’un nom de domaine personnalisé 

Lorsque vous créez une instance de gestion des API (APIM), Azure l’affecte à un sous-domaine d’azure-api.net (par exemple, `apim-service-name.azure-api.net`). Toutefois, vous pouvez également exposer vos points de terminaison APIM via votre propre nom de domaine, par exemple, **contoso.com**. Ce didacticiel explique comment mapper un nom DNS personnalisé existant à des points de terminaison exposés par une instance de gestion des API Azure.

> [!WARNING]
> Les clients qui souhaitent utiliser un épinglage de certificat pour améliorer la sécurité de leurs applications doivent utiliser un nom de domaine personnalisé > et le certificat qu’ils gèrent, pas le certificat par défaut. Les clients qui épinglent le certificat par défaut à la place > prendront une dépendance dure sur les propriétés du certificat qu’ils ne contrôlent pas, ce qui n’est pas une pratique recommandée.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes décrites dans cet article, vous devez disposer des éléments suivants :

+ Un abonnement Azure actif.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Une instance APIM. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).
+ Un nom de domaine personnalisé qui vous appartient. Vous devez vous procurer séparément le nom de domaine personnalisé que vous souhaitez utiliser. De plus, il doit être hébergé sur un serveur DNS. Cette rubrique ne donne aucune instruction sur l’hébergement d’un nom de domaine personnalisé.
+ Vous devez disposer d’un certificat valide et d’une clé publique et privée (.PFX). Le sujet ou l’autre nom du sujet doit correspondre au nom du domaine. Cela permet à APIM d’exposer des URL de manière sécurisée, via SSL.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Utiliser le portail Azure pour définir un nom de domaine personnalisé

1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre instance APIM.
2. Sélectionnez **Domaines personnalisés et SSL**.
    
    Vous pouvez assigner un nom de domaine personnalisé à un certain nombre de points de terminaison. Actuellement, les points de terminaison disponibles sont les suivants : 
    + **Proxy** (valeur par défaut : `<apim-service-name>.azure-api.net`) 
    + **Portail** (valeur par défaut : `<apim-service-name>.portal.azure-api.net`)     
    + **Gestion** (valeur par défaut : `<apim-service-name>.management.azure-api.net`) 
    + **SCM** (valeur par défaut : `<apim-service-name>.scm.azure-api.net`)

    >[!NOTE]
    > Vous pouvez mettre à jour tous les points de terminaison ou certains d’entre eux. En règle générale, les clients mettent à jour les points de terminaison **Proxy** (cette URL est utilisée pour appeler l’API exposée via la gestion des API) et **Portal** (URL du portail des développeurs). Les points de terminaison **Gestion** et **SCM** sont utilisés en interne par les clients APIM. Pour cette raison, ils se voient moins fréquemment assigner un nom de domaine personnalisé.
3. Sélectionnez le point de terminaison que vous souhaitez mettre à jour. 
4. Dans la fenêtre de droite, cliquez sur **Personnalisé**.

    + Dans la zone **Nom de domaine personnalisé**, spécifiez le nom que vous souhaitez utiliser. Par exemple : `api.contoso.com`. <br/>Les noms de domaine avec des caractères génériques (par exemple, *.domaine.com) sont également pris en charge.
    + Dans la zone **Certificat**, spécifiez un fichier .PFX valide à charger. 
    + Si le certificat est associé à un mot de passe, saisissez-le dans le champ **Mot de passe**.
1. Cliquez sur Appliquer.

    >[!NOTE]
    >Le processus d’attribution du certificat peut prendre 15 minutes ou plus, selon la taille du déploiement. La référence SKU de développeur présente des temps d’arrêt, mais pas les références SKU de base ou supérieures.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Étapes suivantes

[Mettre à niveau votre service et le mettre à l’échelle](upgrade-and-scale.md)
