---
title: "Créer une règle basée sur un chemin pour une passerelle Application Gateway - Portail Azure | Microsoft Docs"
description: "Découvrez comment créer une règle basée sur le chemin pour une passerelle Application Gateway à l’aide du portail Azure."
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 87bd93bc-e1a6-45db-a226-555948f1feb7
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: b207e7e7bd83e56db68288190c7bedafa8b5b7fa
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-azure-portal"></a>Créer une règle basée sur le chemin pour une passerelle Application Gateway à l’aide du portail Azure

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-create-url-route-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

Grâce au routage basé sur le chemin d’URL, vous pouvez associer des itinéraires basés sur le chemin d’URL de requêtes HTTP. Il vérifie s’il existe une route vers un pool de serveur back-end configuré pour les URL listées dans la passerelle d’application, puis envoie le trafic réseau vers le pool défini. Une utilisation courante du routage basé sur le chemin d’URL consiste à équilibrer la charge des demandes pour différents types de contenu entre différents pools de serveurs principaux.

Les passerelles Application Gateway ont deux types de règles : des règles de base et des règles basées sur un chemin. Le type de règle de base fournit un service de tourniquet pour les pools back-end. En plus de distribuer les tourniquets, les règles basées sur le chemin utilisent également le modèle de chemin d’accès de la requête URL au moment de choisir le pool principal.

## <a name="scenario"></a>Scénario

Le scénario suivant crée une règle basée sur le chemin sur une passerelle Application Gateway existante.
Le scénario suppose que vous avez déjà suivi la procédure dans [Créer une passerelle Application Gateway avec le portail](application-gateway-create-gateway-portal.md).

![Itinéraire d’URL][scenario]

## <a name="createrule"></a>Créer la règle basée sur le chemin

Une règle basée sur le chemin nécessite un écouteur qui lui est propre. Avant de créer la règle, veillez à vérifier que vous disposez d’un écouteur à utiliser.

### <a name="step-1"></a>Étape 1

Accédez au [portail Azure](http://portal.azure.com) et sélectionnez une passerelle Application Gateway existante. Cliquer sur **Règles**.

![Vue d’ensemble d’Application Gateway][1]

### <a name="step-2"></a>Étape 2

Cliquez sur le bouton **Basé sur le chemin** pour ajouter une nouvelle règle basée sur le chemin.

### <a name="step-3"></a>Étape 3 :

Le panneau **Add path-based rule (Ajouter une règle basée sur le chemin)** comporte deux sections. La première section est celle où vous avez défini l’écouteur, le nom de la règle et les paramètres de chemin par défaut. Les paramètres de chemin par défaut sont des itinéraires qui ne relèvent pas de l’itinéraire personnalisé basé sur le chemin. La deuxième section du panneau **Add path-based rule (Ajouter une règle basée sur le chemin)** est celle où vous définissez les règles basées sur le chemin elles-mêmes.

**Paramètres de base**

* **Nom** : nom convivial de la règle accessible dans le portail.
* **Écouteur** : écouteur utilisé pour la règle.
* **Pool principal par défaut** : pool principal à utiliser pour la règle par défaut.
* **Paramètres HTTP par défaut** : paramètres HTTP à utiliser pour la règle par défaut.

**Paramètres de la règle basée sur le chemin**

* **Nom** : nom convivial de la règle basée sur le chemin.
* **Chemins d’accès** - chemin d’accès que la règle recherche pour transférer le trafic.
* **Pool principal** : pool principal à utiliser pour la règle.
* **Paramètre HTTP** : paramètres HTTP à utiliser avec la règle.

> [!IMPORTANT]
> Le paramètre **Chemins d’accès** constitue la liste de modèles de chemin d’accès à utiliser pour la correspondance. Chaque modèle doit commencer par une barre oblique. L’astérisque n’est autorisé qu’à la fin. Exemples valides : /xyz, /xyz*, et /xyz/*.  

![Ajouter un panneau Règle basée sur le chemin contenant toutes les informations][2]

L’ajout d’une règle basée sur le chemin à une passerelle Application Gateway existante est un processus simple via le portail Azure. Après avoir créé une règle basée sur le chemin, vous pouvez la modifier pour inclure des règles supplémentaires. 

![Ajouter des règles supplémentaires basées sur le chemin][3]

Cette étape configure une route basée sur un chemin. Il est important de comprendre que les requêtes ne sont pas réécrites. Au fil de l’arrivée des requêtes, la passerelle Application Gateway examine la requête et, en fonction du modèle d’URL, envoie la requête au pool principal approprié.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment configurer le déchargement SSL avec la passerelle Azure Application Gateway, consultez [Configurer une passerelle Application Gateway pour le déchargement SSL à l’aide du portail Azure](application-gateway-ssl-portal.md).

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png
