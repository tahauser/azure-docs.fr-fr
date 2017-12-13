---
title: "Autoriser l’accès à l’application de conteneurs DC/OS Azure"
description: "Comment autoriser l’accès public aux conteneurs DC/OS dans Azure Container Service."
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: aedc97335a0b9ad00cf653477b62bf530b556900
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="enable-public-access-to-an-azure-container-service-application"></a>Autoriser l’accès public à une application Azure Container Service

Tous les conteneurs DC/OS du [pool d’agent public](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) ACS sont automatiquement exposés à internet. Par défaut, les ports **80**, **443**, **8080** sont ouverts, et tous les conteneurs (publics) à l’écoute sur ces ports sont accessibles. Cet article vous explique comment ouvrir davantage de ports pour vos applications dans Azure Container Service.

## <a name="open-a-port-portal"></a>Ouvrir un port (portail)
Tout d’abord, il faut ouvrir le port désiré.

1. Connectez-vous au portail.
2. Recherchez le groupe de ressources sur lequel vous avez déployé Azure Container Service.
3. Sélectionnez l’équilibrage de charge de l’agent (dont le nom ressemble à **XXXX-agent-lb-XXXX**).
   
    ![Équilibrage de charge dans Azure Container Service](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Cliquez sur **Sondes**, puis sur **Ajouter**.
   
    ![Sondes d’équilibrage de charge dans Azure Container Service](./media/container-service-enable-public-access/add-probe.png)
5. Remplissez le formulaire de sonde et cliquez sur **OK**.
   
   | Champ | Description |
   | --- | --- |
   | Nom |Nom descriptif de la sonde. |
   | Port |Port du conteneur à tester. |
   | Chemin |(En mode HTTP) Le chemin relatif du site à la sonde. HTTPS non pris en charge. |
   | Intervalle |Intervalle de temps entre les tentatives de la sonde, en secondes. |
   | Seuil de défaillance sur le plan de l’intégrité |Nombre de tentatives consécutives effectuées par la sonde avant de considérer que le conteneur est défectueux. |
6. De retour sur les propriétés de l’équilibrage de charge de l’agent, cliquez sur **Règles d’équilibrage de la charge**, puis sur **Ajouter**.
   
    ![Règles d’équilibrage de charge dans Azure Container Service](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Remplissez le formulaire d’équilibrage de charge et cliquez sur **OK**.
   
   | Champ | Description |
   | --- | --- |
   | Nom |Nom descriptif de l’équilibrage de charge. |
   | Port |Port entrant public. |
   | Port principal |Port interne public du conteneur vers lequel le trafic est acheminé. |
   | Pool principal |Les conteneurs de ce pool seront les cibles de cet équilibrage de charge. |
   | Sonde |Sonde utilisée pour déterminer si une cible du **pool principal** est saine. |
   | Persistance de session |Détermine comment le trafic provenant d’un client doit être géré pendant la durée de la session.<br><br>**Aucune** : les demandes successives provenant d’un même client peuvent être gérées par n’importe quel conteneur.<br>**Adresse IP client** : les demandes successives provenant d’une même adresse IP client sont gérées par le même conteneur.<br>**Adresse IP cliente et protocole**: les demandes successives provenant d’une même combinaison d’adresse IP client et de protocole sont gérées par le même conteneur. |
   | Délai d’inactivité |(TCP uniquement) Durée en minutes pendant laquelle maintenir un client TCP/HTTP ouvert sans faire appel à des messages *keep-alive* . |

## <a name="add-a-security-rule-portal"></a>Ajouter une règle de sécurité (portail)
Ensuite, il faut ajouter une règle de sécurité qui achemine le trafic à partir du port ouvert via le pare-feu.

1. Connectez-vous au portail.
2. Recherchez le groupe de ressources sur lequel vous avez déployé Azure Container Service.
3. Sélectionnez le groupe de sécurité réseau de l’agent **public** (dont le nom ressemble à **XXXX-agent-public-nsg-XXXX**).
   
    ![Groupe de sécurité réseau Azure Container Service](./media/container-service-enable-public-access/agent-nsg.png)
4. Sélectionnez les **règles de sécurité de trafic entrant**, puis cliquez sur **Ajouter**.
   
    ![Règles du groupe de sécurité réseau Azure Container Service](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Remplissez la règle de pare-feu pour autoriser votre port public et cliquez sur **OK**.
   
   | Champ | Description |
   | --- | --- |
   | Nom |Nom descriptif de la règle de pare-feu. |
   | Priorité |Rang de priorité de la règle. Plus le numéro de priorité est faible, plus la priorité de la règle est élevée. |
   | Source |Restreindre la plage d’adresses IP entrante pour qu’elle soit autorisée ou interdite par cette règle. Utilisez **Any** (Aucune) pour ne spécifier aucune restriction. |
   | Service |Sélectionner un ensemble de services prédéfinis concerné par cette règle de sécurité. Sinon, utilisez **Personnalisé** pour créer votre propre ensemble de services. |
   | Protocole |Restreindre le trafic basé sur **TCP** ou **UDP**. Utilisez **Any** (Aucune) pour ne spécifier aucune restriction. |
   | Plage de ports |Lorsque le **Service** est **Personnalisé**, spécifie la plage de ports affectée par cette règle. Vous pouvez utiliser un port unique, tel que **80** ou une plage comme **1024-1500**. |
   | Action |Autoriser ou refuser le trafic qui répond aux critères. |

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la différence entre les [agents DC/OS publics et privés](container-service-dcos-agents.md).

En savoir plus sur la [gestion de vos conteneurs DC/OS](container-service-mesos-marathon-ui.md).

