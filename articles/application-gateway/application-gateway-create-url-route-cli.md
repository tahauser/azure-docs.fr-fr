---
title: "Créer une passerelle d’application à l’aide des règles de routage d’URL - Azure CLI 2.0 | Microsoft Docs"
description: "Cette page fournit des instructions pour créer et configurer une passerelle d’application à l’aide des règles de routage d’URL."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: 10d01d5d80e2d111d6b39598eed3612f80162b23
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing-with-azure-cli-20"></a>Créer une passerelle d’application à l’aide du routage basé sur le chemin avec Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-create-url-route-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

Grâce au routage basé sur le chemin d’URL, vous pouvez associer des itinéraires basés sur le chemin d’URL de requêtes HTTP. Il vérifie s’il existe une route vers un pool de serveur back-end configuré pour les URL listées dans la passerelle d’application, puis envoie le trafic réseau vers le pool défini. Une utilisation courante du routage basé sur le chemin d’URL consiste à équilibrer la charge des demandes pour différents types de contenu entre différents pools de serveurs principaux.

La passerelle Azure Application Gateway a deux types de règles : des règles de base et des règles basées sur un chemin. Le type de règle de base fournit un service de tourniquet pour les pools back-end. En plus de distribuer les tourniquets, les règles basées sur le chemin utilisent également le modèle de chemin d’accès de la requête URL pour choisir le pool back-end approprié.

## <a name="scenario"></a>Scénario

Dans l’exemple suivant, une passerelle d’application gère le trafic pour contoso.com avec deux pools de serveurs back-end : un pool de serveurs par défaut et un pool de serveurs d’images.

Les requêtes pour http://contoso.com/image* sont routées vers le pool de serveurs d’images (**imagesBackendPool**). Si le modèle de chemin d’accès ne correspond pas, la passerelle d’application sélectionne le pool de serveurs par défaut (**appGatewayBackendPool**).

![Itinéraire d’URL](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="sign-in-to-azure"></a>Connexion à Azure

Ouvrez l’**Invite de commandes Microsoft Azure**et connectez-vous :

```azurecli
az login -u "username"
```

> [!NOTE]
> Vous pouvez également utiliser `az login` sans le commutateur destiné à la connexion de l’appareil et qui nécessite la saisie d’un code sur aka.ms/devicelogin.

Après avoir entré la commande précédente, vous recevez un code. Accédez à https://aka.ms/devicelogin dans un navigateur pour continuer le processus de connexion.

![Invite de commande affichant le code de l’appareil][1]

Dans le navigateur, entrez le code que vous avez reçu. Vous êtes redirigé vers une page de connexion.

![Page de navigateur pour la saisie du code][2]

Entrez le code pour vous connecter, puis fermez le navigateur pour continuer.

![Connexion réussie][3]

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>Ajouter une règle basée sur le chemin à une passerelle d’application existante

Les étapes suivantes vous expliquent comment ajouter une règle basée sur le chemin d’accès à une passerelle d’application existante.
### <a name="create-a-new-back-end-pool"></a>Créer un pool principal

Configurez le paramètre de passerelle d’application **imagesBackendPool** pour le trafic à charge équilibrée dans le pool back-end. Dans cet exemple, vous configurez différents paramètres pour le nouveau pool principal. Chaque pool back-end peut avoir son propre paramètre. Les règles basées sur le chemin d’accès utilisent les paramètres HTTP du serveur back-end pour router le trafic vers les membres appropriés du pool back-end. Ils déterminent le protocole et le port utilisés lors de l’envoi du trafic vers les membres du pool back-end. Les paramètres HTTP du serveur principal déterminent également les sessions basées sur les cookies.  Si elle est activée, l’affinité de session basée sur les cookies envoie le trafic vers le même serveur back-end que les requêtes précédentes pour chaque paquet.

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port-for-an-application-gateway"></a>Créer un port frontal pour une passerelle d’application

L’objet de configuration du port frontal est utilisé par un écouteur pour définir le port écouté par la passerelle d’application pour connaître le trafic sur l’écouteur.

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>Créer un écouteur

Cette étape configure l’écouteur pour l’adresse IP publique et le port utilisé pour recevoir le trafic réseau entrant. L’exemple suivant utilise la configuration de l’adresse IP frontale configurée précédemment, la configuration de port frontal et un protocole (http ou https, qui respectent la casse), et il configure l’écouteur. Dans cet exemple, l’écouteur écoute le trafic HTTP sur le port 82 sur l’adresse IP publique créée précédemment dans ce scénario.

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>Créer le mappage du chemin d’accès d’URL

Cette étape configure le chemin d’accès de l’URL relatif utilisé par la passerelle d’application pour définir le mappage entre le chemin et le pool back-end affecté pour gérer le trafic entrant.

> [!IMPORTANT]
> Chaque chemin d’accès doit commencer par le signe « / » et le seul endroit où un astérisque est autorisé est à la fin. /xyz, /xyz* ou /xyz/* sont des exemples valides. La chaîne transmise à l’outil de correspondance de chemin n’inclut pas de texte après le premier signe ? ou #. De plus, ces caractères ne sont pas autorisés. 

L’exemple suivant crée une règle pour le chemin d’accès /images/* acheminant le trafic vers le pool back-end **imagesBackendPool**. Cette règle garantit que le trafic de chaque ensemble d’URL est acheminé vers le pool back-end. Par exemple, http://adatum.com/images/figure1.jpg accède à **imagesBackendPool**. Si le chemin ne correspond à aucune des règles de chemins prédéfinies, la configuration de mappage des chemins de règles configure également un pool d’adresses principal par défaut. Par exemple, http://adatum.com/shoppingcart/test.html accède à **pool1**, car il est défini en tant que pool par défaut pour le trafic sans correspondance.

```azurecli-interactive
az network application-gateway url-path-map create \
--gateway-name AdatumAppGateway \
--name imagespathmap \
--paths /images/* \
--resource-group myresourcegroup2 \
--address-pool imagesBackendPool \
--default-address-pool appGatewayBackendPool \
--default-http-settings appGatewayBackendHttpSettings \
--http-settings appGatewayBackendHttpSettings \
--rule-name images
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déchargement SSL (Secure Sockets Layer), consultez [Configurer une passerelle d’application pour le déchargement SSL](application-gateway-ssl-cli.md).


[scenario]: ./media/application-gateway-create-url-route-cli/scenario.png
[1]: ./media/application-gateway-create-url-route-cli/figure1.png
[2]: ./media/application-gateway-create-url-route-cli/figure2.png
[3]: ./media/application-gateway-create-url-route-cli/figure3.png
