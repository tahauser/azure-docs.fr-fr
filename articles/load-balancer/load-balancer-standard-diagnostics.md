---
title: Niveau Standard d’Azure Load Balancer | Microsoft Docs
description: Utilisation des métriques et des informations d’intégrité disponibles pour les diagnostics d’Azure Load Balancer Standard
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: Kumud
ms.openlocfilehash: 1d39cdc13e69740dc99e67f935b60db218536044
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Métriques et diagnostics d’intégrité du Load Balancer Standard

Le niveau Standard d’Azure Load Balancer fournit les fonctionnalités de diagnostic suivantes pour vos ressources :
* Métriques multidimensionnelles : le niveau Standard de Load Balancer offre de nouvelles fonctionnalités de diagnostic multidimensionnel pour les configurations Load Balancer public et interne. Cela vous permet de surveiller, gérer et résoudre les problèmes de vos ressources Load Balancer.

* Intégrité des ressources : la page Load Balancer dans le portail Azure et la page Intégrité des ressources (sous Monitor) indiquent l’intégrité des ressources pour la configuration Load Balancer public du niveau Standard du Load Balancer.

Cet article propose une présentation rapide de ces fonctionnalités et comment les utiliser pour le niveau Standard de Load Balancer.

## <a name = "MultiDimensionalMetrics"></a>Métriques multidimensionnelles

Azure Load Balancer fournit les nouvelles métriques multidimensionnelles via Nouvelles métriques Azure (préversion) dans le portail et vous permet d’obtenir des informations de diagnostic en temps réel sur vos ressources Load Balancer. 

Les métriques suivantes sont disponibles pour différentes configurations de niveau Standard Load Balancer (LB) actuelles :

| Métrique | Type de ressource | Description | Agrégation recommandée |
| --- | --- | --- | --- |
| Disponibilité d’adresse IP virtuelle (disponibilité de chemin d’accès de données) | LB public | Le niveau Standard de Load Balancer teste en continu le chemin de données d’une région vers le serveur frontal Load Balancer, jusqu’à la pile SDN qui prend en charge votre machine virtuelle. Tant que les instances saines restent, la mesure suit le même chemin que le trafic à charge équilibrée de vos applications. Le chemin de données utilisé par vos clients est également validé. La mesure est invisible pour votre application et n’interfère pas avec les autres opérations.| Moyenne |
| Disponibilité DIP (état de la sonde d’intégrité) |  LB public et interne | Le niveau Standard de Load Balancer utilise un service de détection d’intégrité distribué qui surveille l’intégrité du point de terminaison de votre application en fonction de vos paramètres de configuration. Cette métrique fournit un agrégat ou une vue filtrée par point de terminaison de chaque point de terminaison d’instance dans le pool Load Balancer.  Vous pouvez observer comment Load Balancer voit l’intégrité de votre application comme indiqué par votre configuration de sonde d’intégrité. |  Moyenne |
| Paquets SYN |  LB public | Le niveau Standard de Load Balancer ne termine pas les connexions TCP et n’interagit pas avec les flux de paquets UDP et TCP. Les flux et leurs établissements de liaisons sont toujours entre la source et l’instance de machine virtuelle. Pour mieux résoudre les problèmes posés par vos scénarios de protocole TCP, vous pouvez utiliser les compteurs de paquets SYN pour comprendre le nombre de tentatives de connexion TCP effectuées. La métrique indique le nombre de paquets SYN TCP reçus.| Moyenne |
| Connexions SNAT |  LB public |Le niveau Standard de Load Balancer indique le nombre de flux sortants usurpés sur le serveur frontal d’adresse IP public. Les ports SNAT constituent une ressource qui n’est pas inépuisable. Cette métrique peut donner une idée de l’importance du rôle joué par SNAT dans votre application pour les flux sortants.  Les compteurs relatifs aux flux SNAT sortants réussis et mis en échec sont indiqués et peuvent être utilisés pour comprendre l’intégrité de vos flux sortants et résoudre les problèmes associés.| Moyenne |
| Compteurs d’octets |  LB public et interne | Le niveau Standard de Load Balancer indique les données traitées par serveur frontal.| Moyenne |
| Compteurs de paquets |  LB public et interne | Le niveau Standard de Load Balancer indique les paquets traités par serveur frontal.| Moyenne |

### <a name="view-load-balancer-metrics-in-the-portal"></a>Afficher les métriques Load Balancer dans le portail

Le portail Azure présente les métriques Load Balancer via la page Métriques (préversion), qui est disponible dans la page de ressource Load Balancer pour une ressource Load Balancer spécifique, ainsi que dans la page Azure Monitor. 

Pour afficher les métriques pour vos ressources de niveau Standard de Load Balancer, accédez à Métriques (préversion) dans l’un de ces emplacements, sélectionnez le type de métrique (ressource Load Balancer dans la page Monitor) dans la liste déroulante, définissez le type d’agrégation approprié et, facultativement, configurez le filtrage et le regroupement nécessaires. Vous pourrez alors consulter la vue d’historique des métriques dans les conditions indiquées.

![Métriques (préversion) pour le niveau Standard de Load Balancer](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*Figure - Disponibilité DIP / Métrique d’état de la sonde d’intégrité pour Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Récupérer les métriques multidimensionnelles par programme via des API

Le guide de l’API pour récupérer les définitions et valeurs de métrique multidimensionnelle est disponible dans [Monitoring REST API Walkthrouh > Multi-Dimensional API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api) (Procédure de surveillance de l’API REST > API multidimensionnelle)

### <a name = "DiagnosticScenarios"></a>Scénarios de diagnostic courants et vues recommandées

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Le chemin d’accès de données est-il opérationnel et disponible pour l’adresse IP virtuelle de mon Load Balancer ?

La méthode de disponibilité d’adresse IP virtuelle décrit l’intégrité du chemin d’accès de données dans la région vers l’hôte de calcul où se trouvent vos machines virtuelles. Elle reflète l’intégrité de l’infrastructure Azure. Vous pouvez utiliser cette métrique pour :
- Surveiller la disponibilité externe de votre service
- Aller en profondeur et savoir si la plateforme sur laquelle votre service est déployé est intègre ou si votre système d’exploitation invité ou instance d’application est intègre
- Déterminez si un événement est associé à votre service ou au plan de données sous-jacent. Ne confondez pas avec l’état de la sonde d’intégrité (« disponibilité DIP »).

Pour obtenir la disponibilité d’adresse IP virtuelle pour vos ressources de niveau Standard de Load Balancer :
- Assurez-vous que la ressource Load Balancer correcte est sélectionnée. 
- Sélectionnez **VIP Availability** (Disponibilité d’adresse IP virtuelle) dans la liste déroulante **Métrique**. 
- Sélectionnez **Moy** pour **Agrégation**. 
- De plus, ajoutez un filtre sur l’adresse IP virtuelle ou le port d’adresse IP virtuelle comme dimension avec l’adresse IP de serveur frontal ou le port de serveur frontal requis, puis regroupez par la dimension sélectionnée.

![Détection d’adresse IP virtuelle](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figure - Détails de la détection d’adresse IP virtuelle du Load Balancer*

La métrique est générée par une mesure intrabande active. Service de détection dans la région d’origine du trafic pour cette mesure, activé dès que vous créez un déploiement avec un serveur frontal public et exécuté jusqu’à ce que vous supprimiez le serveur frontal. 

>[!NOTE]
>Les serveurs frontaux internes ne sont pas pris en charge pour le moment. 

Un paquet correspondant au serveur frontal et à la règle de votre déploiement est généré régulièrement. Il traverse la région entre la source et l’hôte sur lequel se trouve une machine virtuelle du pool principal. L’infrastructure de Load Balancer effectue les mêmes opérations d’équilibrage de charge et de conversion que pour tout autre trafic. Cette sonde est intrabande sur votre point de terminaison équilibré en charge. Lorsque que la sonde arrive sur l’ordinateur de calcul sur lequel se trouve une machine virtuelle intègre du pool principal, l’hôte de calcul génère une réponse au service de détection. Votre machine virtuelle ne voit pas ce trafic.
La disponibilité d’adresse IP virtuelle échoue pour les raisons suivantes :
- Il ne reste plus de machines virtuelles intègres du pool principal dans votre déploiement. 
- Une panne d’infrastructure ayant provoqué l’échec de la disponibilité d’adresse IP virtuelle s’est produite.

Vous pouvez utiliser la [métrique de disponibilité d’adresse IP virtuelle avec l’état de la sonde d’intégrité à des fins de diagnostic](https://aka.ms/lbdiagnostics#vipavailabilityandhealthprobes).

Utilisez une agrégation **Moyenne** pour la plupart des scénarios.

#### <a name="are-the-backend-instances-for-my-vip-responding-to-probes"></a>Les instances de serveur principal pour mon adresse IP virtuelle répondent-elles aux sondes ?

La métrique d’état de la sonde d’intégrité indique l’intégrité de votre déploiement d’application tel que vous l’avez configuré lors de la configuration de la sonde d’intégrité de Load Balancer. Load Balancer utilise l’état de la sonde d’intégrité pour déterminer où envoyer les nouveaux flux. Les sondes d’intégrité proviennent d’une adresse d’infrastructure Azure et sont visibles dans le système d’exploitation invité de la machine virtuelle.

Pour obtenir la disponibilité DIP pour vos ressources de niveau Standard de Load Balancer,
- Sélectionnez la métrique **DIP Availability** (Disponibilité DIP) avec le type d’agrégation **Moy**. 
- Appliquez un filtre sur l’adresse IP virtuelle ou le port requis (ou les deux).

![Disponibilité DIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*Figure - Disponibilité VIP pour Load Balancer*

Les sondes d’intégrité échouent pour les raisons suivantes :
- Si vous configurez une sonde d’intégrité pour un port qui n’écoute pas ou ne répond pas ou avec un protocole incorrect. Si votre service utilise des règles DSR (adresse IP flottante), vous devez vous assurer que votre service écoute sur l’adresse IP de la configuration IP de la carte d’interface réseau, et pas seulement sur la boucle configurée avec l’adresse IP de serveur frontal.
- Si votre sonde n’est pas autorisée par le Groupe de sécurité réseau, le pare-feu du système d’exploitation invité de la machine virtuelle ou les filtres de la couche Application.

Utilisez une agrégation **Moyenne** pour la plupart des scénarios.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Comment faire pour vérifier les statistiques de ma connexion sortante ? 

La métrique des connexions SNAT indique le volume de réussites et d’échecs (pour les [flux sortants](https://aka.ms/lboutbound)).

Un volume de connexions ayant échoué supérieur à zéro indique un épuisement du port SNAT. Vous devez examiner en détail et déterminer les causes de ces échecs. L’épuisement du port SNAT se traduit par des problèmes d’établissement de [flux sortants](https://aka.ms/lboutbound). Consultez l’article sur les connexions sortantes pour comprendre les scénarios, les mécanismes au travail et comment atténuer/éviter l’épuisement du port SNAT. 

Pour obtenir des statistiques de connexion SNAT,
- Sélectionnez le type de métrique **SNAT Connections** (Connexions SNAT) et l’agrégation **Somme**. 
- Regroupez par **État de la connexion** pour le nombre de connexions SNAT ayant réussi et échoué représenté par les différentes lignes. 

![Connexion SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Figure - Nombre de connexions SNAT pour Load Balancer*


#### <a name="how-do-i-check-inbound--outbound-connection-attempts-for-my-service"></a>Comment faire pour vérifier les tentatives de connexions entrantes/sortants pour mon service ?

La métrique de paquets SYN indique le volume de paquets TCP SYN, qui ont été reçus ou qui ont été envoyés (pour les [flux sortants](https://aka.ms/lboutbound)) associés à un serveur frontal donné. Cette méthode peut être utilisée pour comprendre les tentatives de connexions TCP vers votre service.
Utilisez une agrégation totale pour la plupart des scénarios.

![Connexion SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figure - Nombre de SYN pour Load Balancer*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Comment faire pour vérifier la consommation de ma bande passante réseau ? 

La métrique de compteurs d’octets/de paquets indique le volume d’octets et de paquets envoyés ou reçus par votre service par serveur frontal.
Utilisez une agrégation **Totale** pour la plupart des scénarios.

Pour obtenir les statistiques de nombre d’octets ou de paquets
- Sélectionnez le type de métrique **Bytes Count** (Nombre d’octets) et/ou **Packet Count** (Nombre de paquets) avec l’agrégation **Moy**. 
- Appliquez un filtre sur l’adresse IP d’un serveur frontal, le port de serveur frontal ou l’adresse IP ou le port principal concerné. 
- Ou obtenez des statistiques globales pour la ressource Load Balancer sans aucun filtrage.


Exemples de vues de métriques avec différentes configurations -

![Nombre d’octets](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figure - Nombre d’octets pour Load Balancer*

#### <a name = "vipavailabilityandhealthprobes"></a>Comment faire pour diagnostiquer mon déploiement Load Balancer ?

La combinaison des métriques de disponibilité d’adresse IP virtuelle et de sondes d’intégrité sur un même graphique peut vous permettre de déterminer où rechercher le problème et de le résoudre. Vous pouvez vous assurer qu’Azure fonctionne correctement et l’utiliser pour déterminer si la configuration ou l’application est la cause première.

Vous pouvez utiliser des métriques de sonde d’intégrité pour comprendre comment Azure affiche l’intégrité de votre déploiement en fonction de la configuration que vous avez fournie. Examiner les sondes d’intégrité est toujours une première étape très utile dans l’analyse ou la détermination d’une cause.

Vous pouvez aller plus loin et utiliser des métriques de disponibilité d’adresse IP virtuelle pour savoir comment Azure affiche l’intégrité du plan de données sous-jacent responsable de votre déploiement. Lorsque vous combinez les deux métriques, vous pouvez déterminer où l’erreur peut se situer comme illustré dans cet exemple :

![Diagnostics d’adresse IP virtuelle](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Figure - Combinaison des métriques de disponibilité DIP et d’adresse IP virtuelle*

Le graphique affiche les informations suivantes :
- L’infrastructure elle-même était intègre, l’infrastructure hébergeant vos machines virtuelles était accessible et plusieurs machines virtuelles ont été placées dans le serveur principal. Ceci est illustré par le trait bleu pour la disponibilité d’adresse IP virtuelle, à 100 %. 
- Toutefois, l’état de la sonde d’intégrité (disponibilité DIP) est à 0 % au début du graphique comme indiqué par le trait orange. La zone dans le cercle rouge indique où les sondes d’intégrité (disponibilité DIP) sont devenues intègres, et à quel point le déploiement du client a été en mesure d’accepter de nouveaux flux.

Le graphique a permis au client de dépanner lui-même le déploiement sans devoir deviner ou demander de l’aide sur d’autres problèmes. Le service du client n’était pas disponible, car les sondes d’intégrité ont échoué en raison d’une configuration incorrecte ou de l’échec d’une application.

### <a name = "Limitations"></a>Limitations

- La disponibilité de l’adresse IP virtuelle est actuellement disponible pour les serveurs frontaux publics uniquement.

## <a name = "ResourceHealth"></a>État d’intégrité des ressources

L’état d’intégrité des ressources de niveau Standard de Load Balancer est indiqué dans la page **Intégrité des ressources** existante sous **Monitor > État du service**.

>[!NOTE]
>L’intégrité des ressources pour Load Balancer est actuellement disponible pour la configuration publique de niveau de Standard de Load Balancer uniquement. Les ressources Load Balancer internes ou la référence SKU de base des ressources Load Balancer n’indiquent pas l’intégrité des ressources.

Pour afficher l’intégrité de vos ressources de niveau Standard de Load Balancer public,
 - Accédez à **Monitor > État du service**.

  ![Port Monitor](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Figure - État du service sur Azure Monitor*

 - Sélectionnez **Intégrité des ressources** et vérifiez que l’**ID d’abonnement** et le **Type de ressource = Load Balancer** correct sont sélectionnés.

  ![État d’intégrité des ressources](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Figure - Sélectionner une ressource pour la vue d’intégrité*

 - Cliquez sur la ressource Load Balancer dans la liste pour afficher son état d’intégrité historique.

    ![État d’intégrité de Load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Figure - Vue d’intégrité de ressource Load Balancer*
 
Le tableau suivant répertorie les divers états d’intégrité de ressource et leurs descriptions. 

| État d’intégrité des ressources | Description |
| --- | --- |
| Disponible | Votre ressource de niveau Standard de Load Balancer public est intègre et disponible |
| Non disponible | Votre ressource de niveau Standard de Load Balancer public n’est pas intègre. Diagnostiquez l’intégrité via Azure Monitor > Métriques. (*Cela peut également signifier que la ressource n’est pas un niveau Standard de Load Balancer public*) |
| Unknown | L’intégrité des ressources de votre niveau Standard de Load Balancer public n’a pas encore été mise à jour. (*Cela peut également signifier que la ressource n’est pas un niveau Standard de Load Balancer public*)  |

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [référence Standard de Load Balancer](load-balancer-standard-overview.md)
- En savoir plus sur [connectivité sortante de Load Balancer](https://aka.ms/lboutbound)


