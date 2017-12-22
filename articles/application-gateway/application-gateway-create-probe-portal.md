---
title: "Créer une sonde personnalisée - Passerelle Application Gateway Azure - Portail Azure | Microsoft Docs"
description: "Découvrez comment créer une sonde personnalisée pour Application Gateway à l’aide du portail"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: davidmu
ms.openlocfilehash: bb77c9b39e1aa89f6411de8ec3b1fca41e954bf2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Créer une sonde personnalisée pour Application Gateway à l’aide du portail

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-create-probe-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

Dans cet article, une sonde personnalisée est ajoutée à une passerelle d’application existante via le portail Azure. Les sondes personnalisées sont utiles pour les applications qui ont une page de contrôle d’intégrité spécifique ou pour les applications qui ne fournissent pas de réponse correcte dans l’application web par défaut.

## <a name="before-you-begin"></a>Avant de commencer

Si vous ne disposez pas déjà d’une passerelle d’application, consultez [Créer une passerelle d’application](application-gateway-create-gateway-portal.md) pour créer une passerelle d’application fonctionnelle.

## <a name="createprobe"></a>Créer la sonde

Les sondes sont configurées via un processus en deux étapes sur le portail. La première étape consiste à créer la sonde. Dans la deuxième étape, vous allez ajouter la sonde aux paramètres http backend de la passerelle d’application.

1. Connectez-vous au [portail Azure](https://portal.azure.com). Si vous ne possédez pas encore de compte, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit d’un mois](https://azure.microsoft.com/free).

1. Dans le volet Favoris du portail Azure, cliquez sur Toutes les ressources. Cliquez sur la passerelle d’application dans le panneau Toutes les ressources. Si l’abonnement que vous avez déjà sélectionné comporte déjà plusieurs ressources, vous pouvez entrer partners.contoso.net dans la zone Filtrer par nom… pour accéder facilement à la passerelle d’application.

1. Cliquez sur **Sondes**, puis cliquez sur le bouton **Ajouter** pour ajouter une sonde.

  ![Ajouter un panneau Sonde contenant toutes les informations][1]

1. Dans le panneau **Ajouter une sonde d’intégrité**, fournissez les informations nécessaires à la sonde, puis cliquez sur **OK** une fois que vous avez terminé.

  |**Paramètre** | **Valeur** | **Détails**|
  |---|---|---|
  |**Nom**|customProbe|Cette valeur est le nom convivial de la sonde à laquelle vous pouvez accéder dans le portail.|
  |**Protocole**|HTTP ou HTTPS | Protocole utilisé par la sonde d’intégrité.|
  |**Hôte**|Par exemple, contoso.com|Cette valeur est le nom d’hôte utilisé pour la sonde. S’applique uniquement lorsque plusieurs sites sont configurés sur Application Gateway, sinon utilisez '127.0.0.1'. Cette valeur est différente du nom d’hôte de la machine virtuelle.|
  |**Chemin d’accès**|/ ou un autre chemin|Reste de l’URL complète de la sonde personnalisée. Un chemin valide commence par « / ». Pour le chemin par défaut de http://contoso.com, utilisez simplement « / ». |
  |**Intervalle (secondes)**|30|Fréquence d’exécution de la sonde pour le contrôle d’intégrité. Il n’est pas recommandé de définir la valeur sur moins de 30 secondes.|
  |**Délai d’expiration (secondes)**|30|Intervalle de temps précédant l’expiration de la sonde. L’intervalle de délai d’attente doit être suffisamment élevé pour qu’un appel HTTP puisse être envoyé afin de garantir que la page d’intégrité backend est disponible.|
  |**Seuil de défaillance sur le plan de l’intégrité**|3|Nombre d’échecs nécessaires pour marquer l’instance comme étant défaillante. Un seuil de 0 signifie qu’en cas d’échec de contrôle d’intégrité, le serveur principal est immédiatement identifié comme défaillant.|

  > [!IMPORTANT]
  > Le nom d’hôte n’est pas identique au nom du serveur. Cette valeur est le nom de l’hôte virtuel en cours d’exécution sur le serveur d’application. La sonde est envoyée à http://(nom d’hôte):(port des paramètres http)/urlPath

## <a name="add-probe-to-the-gateway"></a>Ajouter une sonde à la passerelle

Maintenant que la sonde a été créée, il est temps de l’ajouter à la passerelle. Les paramètres de la sonde sont définis sur les paramètres http principaux de la passerelle d’application.

1. Cliquez sur **Paramètres HTTP** dans la passerelle d’application pour afficher le panneau de configuration, puis cliquez sur les paramètres http backend actuels répertoriés dans la fenêtre.

  ![fenêtre de paramètres https][2]

1. Dans le panneau des paramètres **appGatewayBackEndHttpSettings**, cochez la case **Utiliser la sonde personnalisée**, puis choisissez la sonde créée à la section [Créer la sonde](#createprobe) dans la liste déroulante **Sonde personnalisée**.
Quand vous avez terminé, cliquez sur **Enregistrer** pour appliquer les paramètres.

La sonde par défaut vérifie l’accès par défaut à l’application web. Maintenant qu’une sonde personnalisée a été créée, la passerelle d’application utilise le chemin personnalisé défini pour contrôler l’intégrité des serveurs backend. En fonction des critères qui ont été définis, la passerelle d’application vérifie le chemin spécifié dans la sonde. Si l’appel à host:Port/path ne retourne pas de réponse d’état HTTP 200-399, le serveur est mis hors service après avoir atteint le seuil de défaillance sur le plan de l’intégrité. La détection continue sur l’instance défaillante pour déterminer quand elle sera rétablie. Une fois l’instance ajoutée au pool de serveurs intègre, le trafic recommence à circuler vers elle et la détection de l’instance continue comme d’habitude en fonction des intervalles définis par l’utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment configurer le déchargement SSL avec la passerelle Azure Application Gateway, consultez la [configuration du déchargement SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

