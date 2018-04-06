---
title: Azure Traffic Manager - Méthodes de routage du trafic | Microsoft Docs
description: Cet article va vous aider à comprendre les différentes méthodes de routage du trafic utilisées par Traffic Manager.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: timlt
editor: ''
ms.assetid: db1efbf6-6762-4c7a-ac99-675d4eeb54d0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2017
ms.author: kumud
ms.openlocfilehash: c9bd9b4913e38ed5c1f7f4ec8ee7e3210fa3be8f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="traffic-manager-routing-methods"></a>Méthodes de routage de Traffic Manager

Azure Traffic Manager prend en charge quatre méthodes de routage du trafic pour déterminer comment router le trafic réseau vers les différents points de terminaison de service. Traffic Manager applique la méthode de routage du trafic à chaque requête DNS qu’il reçoit. La méthode de routage du trafic détermine le point de terminaison retourné dans la réponse DNS.

Quatre méthodes de routage du trafic sont disponibles dans Traffic Manager :

* **[Priorité ](#priority):** sélectionnez **Priority** (Priorité) si vous souhaitez utiliser un point de terminaison de service principal pour tout le trafic et disposer de sauvegardes au cas où les points de terminaison principaux ou de sauvegarde ne sont pas disponibles.
* **[Pondération ](#weighted):** sélectionnez **Weighted** (Pondéré) si vous souhaitez distribuer le trafic entrant sur un ensemble de points de terminaison, soit uniformément, soit en fonction du poids que vous définissez.
* **[Performances ](#performance):** sélectionnez **Performance** quand vos points de terminaison se trouvent sur des emplacements géographiques différents et que vous souhaitez que les utilisateurs finaux utilisent le point de terminaison « le plus proche » en termes de latence réseau la plus faible.
* **[Géographique ](#geographic):** sélectionnez **Geographic** (Géographique) pour diriger les utilisateurs vers des points de terminaison spécifiques (Azure, externes ou imbriqués) selon l’emplacement géographique dont leur requête DNS provient. Cette option permet aux clients Traffic Manager de mettre en œuvre des scénarios où il est important de connaître la région géographique des utilisateurs et de router leur trafic en fonction de celle-ci. Exemples : respect des obligations en matière de souveraineté des données, localisation de contenu et d’expérience utilisateur, mesure du trafic en provenance de différentes régions.

Tous les profils Traffic Manager incluent une surveillance de l’intégrité des points de terminaison et un basculement de point de terminaison automatique. (pour plus d’informations, voir la rubrique relative à la [surveillance des points de terminaison avec Traffic Manager](traffic-manager-monitoring.md)) ; Un profil Traffic Manager donné ne peut utiliser qu’une seule méthode de routage du trafic. Vous pouvez sélectionner une méthode de routage du trafic différente pour votre profil à tout moment. Les modifications sont appliquées dans la minute, sans aucun temps d’arrêt. Les méthodes de routage du trafic peuvent être combinées dans des profils Traffic Manager imbriqués. Une imbrication permet de créer des configurations de routage du trafic sophistiquées et flexibles répondant aux besoins d’applications complexes plus importantes. Pour plus d’informations, consultez [Profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).

## <a name = "priority"></a>Méthode de routage du trafic basé sur la priorité

Souvent, une organisation souhaite assurer la fiabilité de ses services en déployant un ou plusieurs services de sauvegarde utilisables en cas de défaillance du service principal. La méthode de routage du trafic « Priorité » permet aux clients Azure d’implémenter facilement ce modèle de basculement.

![Méthode de routage du trafic « Priorité » d’Azure Traffic Manager][1]

Le profil Traffic Manager contient une liste hiérarchisée de points de terminaison de service. Par défaut, Traffic Manager envoie tout le trafic vers le point de terminaison principal (priorité la plus élevée). Si le point de terminaison principal n’est pas disponible, Traffic Manager route le trafic vers le deuxième point de terminaison. Si les points de terminaison de type principal et secondaire ne sont pas disponibles, le trafic est envoyé vers le troisième, et ainsi de suite. La disponibilité du point de terminaison est basée sur l’état configuré (activé ou désactivé) et la surveillance du point de terminaison en cours.

### <a name="configuring-endpoints"></a>Configuration de points de terminaison

Avec Azure Resource Manager, vous configurez la priorité de point de terminaison explicitement à l’aide de la propriété « priority » pour chaque point de terminaison. Cette propriété est une valeur comprise entre 1 et 1000. Plus la valeur est basse, plus la priorité est élevée. Des points de terminaison ne peuvent pas partager des valeurs de priorité. La définition de la propriété est facultative. En cas d’omission, une priorité par défaut basée sur l’ordre du point de terminaison est utilisée.

##<a name = "weighted"></a>Méthode de routage du trafic basé sur la pondération
La méthode de routage du trafic « Pondéré » vous permet de répartir le trafic uniformément ou d’utiliser une pondération prédéfinie.

![Méthode de routage du trafic « Pondéré » d’Azure Traffic Manager][2]

Dans la méthode de routage du trafic Pondéré, vous affectez une pondération à chaque point de terminaison dans la configuration du profil Traffic Manager. La pondération est un entier compris entre 1 et 1000. Ce paramètre est facultatif. En cas d’omission, Traffic Manager utilise la pondération par défaut « 1 ». Plus le poids est élevé, plus la priorité est forte.

Pour chaque requête DNS reçue, Traffic Manager choisit au hasard un point de terminaison disponible. La probabilité de choisir un point de terminaison dépend des pondérations assignées à tous les points de terminaison disponibles. L’utilisation de la même pondération pour tous les points de terminaison produit une distribution uniforme du trafic. L’utilisation de pondérations supérieures ou inférieures sur certains points de terminaison a pour effet que ceux-ci sont retournés plus ou moins fréquemment dans les réponses DNS.

La méthode pondérée permet des scénarios utiles :

* Mise à niveau progressive d’une application : allouez un pourcentage de trafic à acheminer vers un nouveau point de terminaison, puis augmentez progressivement le trafic au fil du temps jusqu’à 100 %.
* Migration d’application vers Azure : créez un profil avec des points de terminaison Azure et externes. Ajustez la pondération des points de terminaison pour privilégier les nouveaux points de terminaison.
* Extension du cloud pour une capacité supplémentaire : étendez rapidement un déploiement local dans le cloud en le plaçant derrière un profil Traffic Manager. Quand vous avez besoin d'une capacité supplémentaire dans le cloud, vous pouvez ajouter ou activer des points de terminaison supplémentaires et spécifier quelle partie du trafic est destinée à chaque point de terminaison.

Le portail Azure Resource Manager prend en charge la configuration de routage du trafic Pondéré.  Vous pouvez configurer les pondérations à l’aide des versions Resource Manager d’Azure PowerShell, de l’interface Azure CLI et des API REST.

Il est important de comprendre que les réponses DNS sont mises en cache par les clients et les serveurs DNS récursifs que les clients utilisent pour résoudre les noms DNS. Cette mise en cache peut avoir une incidence sur les distributions de trafic pondérées. Lorsque le nombre de clients et de serveurs DNS récursifs est élevé, la distribution du trafic fonctionne comme prévu. En revanche, si le nombre de clients ou de serveurs DNS récursifs est peu élevé, la mise en cache peut fausser sensiblement la distribution du trafic.

Les cas d’utilisation courants sont les suivants :

* les environnements de développement et de test ;
* les communications d’application à application ;
* les applications destinées à une base étroite d’utilisateurs qui partagent une infrastructure DNS récursive commune (par exemple, des employés d’une entreprise se connectant via un proxy).

Ces effets de mise en cache DNS sont communs à tous les systèmes de routage du trafic basés sur DNS, pas seulement à Azure Traffic Manager. Dans certains cas, la solution peut constituer à effacer le cache DNS de manière explicite. Dans d’autres cas, une autre méthode de routage du trafic peut être plus appropriée.

## <a name = "performance"></a>Méthode de routage du trafic basé sur les performances

Un déploiement de points de terminaison en deux emplacements ou plus dans le monde peut améliorer la réactivité de nombreuses applications en routant le trafic vers l’emplacement « le plus proche » de vous. La méthode de routage du trafic « Performance » offre cette possibilité.

![Méthode de routage du trafic « Performance » d’Azure Traffic Manager][3]

Le point de terminaison « le plus proche » n’est pas nécessairement le plus proche en termes de distance géographique. Au lieu de cela, la méthode de routage du trafic « Performance » détermine le point de terminaison le plus proche en mesurant le temps de réponse (ou latence) du réseau. Traffic Manager tient à jour une Table de latence Internet pour suivre le temps d’aller-retour entre les plages d’adresses IP et chaque centre de données Azure.

Traffic Manager recherche l’adresse IP source de la demande DNS entrante dans la Table de latence Internet. Traffic Manager choisit un point de terminaison disponible dans le centre de données Azure dont la latence est la plus faible pour la plage d’adresses IP, puis renvoie ce point de terminaison dans la réponse DNS.

Comme expliqué dans [Fonctionnement de Traffic Manager](traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager ne reçoit pas de requêtes DNS provenant directement de clients. Au lieu de cela, les requêtes DNS proviennent du service DNS récursif que les clients sont configurés pour utiliser. Par conséquent, l’adresse IP utilisée pour déterminer le point de terminaison « le plus proche » n’est pas l’adresse IP du client, mais celle du service DNS récursif. Dans la pratique, cette adresse IP est un bon proxy pour le client.


Traffic Manager met régulièrement à jour la Table de latence Internet pour refléter les modifications de l’Internet global et des nouvelles régions Azure. Toutefois, les performances des applications varient en fonction des variations en temps réel de la charge sur Internet. Le routage du trafic Performance ne surveille pas la charge sur un point de terminaison de service donné. En revanche, si un point de terminaison devient indisponible, Traffic Manager ne l’inclut pas dans les réponses aux requêtes DNS.


Points à noter :

* Si votre profil contient plusieurs points de terminaison situés dans la même région Azure, Traffic Manager répartit uniformément le trafic entre les points de terminaison disponibles dans cette région. Si vous préférez une distribution de trafic différente au sein d’une région, vous pouvez utiliser des [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).
* Si tous les points de terminaison activés dans la région Azure la plus proche sont défectueux, Traffic Manager déplace le trafic vers les points de terminaison dans la région Azure la plus proche suivante. Si vous souhaitez définir une séquence de basculement préférée, utilisez des [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).
* Si vous utilisez la méthode de routage du trafic basé sur les performances avec des points de terminaison externes ou des points de terminaison imbriqués, vous devez spécifier l’emplacement de ces points de terminaison. Choisissez la région Azure la plus proche de votre déploiement. Ces emplacements sont les valeurs prises en charge par la Table de latence Internet.
* L’algorithme qui choisit le point de terminaison est déterministe. Des requêtes DNS répétées à partir du même client sont dirigées vers le même point de terminaison. En règle générale, les clients utilisent des serveurs DNS récursifs différents quand ils se déplacent. Le client peut être routé vers un point de terminaison différent. Le routage peut également être affecté par des mises à jour de la Table de latence Internet. Par conséquent, la méthode de routage du trafic Performance ne garantit pas qu’un client est toujours routé vers le même point de terminaison.
* Lorsque la Table de latence Internet change, il se peut que certains clients soient dirigés vers un point de terminaison différent. La précision de ce changement de routage varie en fonction des données de latence en cours. Ces mises à jour sont essentielles pour conserver l’exactitude du routage du trafic « Performance », car Internet évolue en permanence.

## <a name = "geographic"></a>Méthode de routage du trafic Geographic (Géographique)

Les profils Traffic Manager peuvent être configurés de manière à utiliser la méthode de routage Geographic (Géographique) pour diriger les utilisateurs vers des points de terminaison spécifiques (Azure, externes ou imbriqués) selon l’emplacement géographique dont leur requête DNS provient. Cette option permet aux clients Traffic Manager de mettre en œuvre des scénarios où il est important de connaître la région géographique des utilisateurs et de router leur trafic en fonction de celle-ci. Exemples : respect des obligations en matière de souveraineté des données, localisation de contenu et d’expérience utilisateur, mesure du trafic en provenance de différentes régions.
Lorsqu’un profil est configuré pour le routage géographique, un ensemble de régions géographiques doit être affecté à chaque point de terminaison associé à ce profil. Une région géographique peut présenter les niveaux de granularité suivants : 
- World (Monde) : n’importe quelle région
- Regional Grouping (Regroupement régional) : Afrique, Moyen-Orient, Australie/Pacifique, etc. 
- Country/Region (Pays/Région) : Irlande, Pérou, Hong Kong (R.A.S.), etc. 
- State/Province (État/Province) : États-Unis-Californie, Australie-Queensland, Canada-Alberta, etc. (Remarque : ce niveau de granularité est pris en charge uniquement pour les états/provinces de l’Australie, du Canada, du Royaume-Uni et des États-Unis.)

Lorsqu’une région ou un ensemble de régions est affecté à un point de terminaison, toutes les demandes provenant de ces régions sont routées exclusivement vers ce point de terminaison. Traffic Manager utilise l’adresse IP source de la requête DNS pour déterminer la région où l’utilisateur effectue sa requête. Il s’agit en général de l’adresse IP du programme de résolution DNS local qui effectue la requête pour le compte de l’utilisateur.  

![Méthode de routage du trafic « Geographic » (Géographique) d’Azure Traffic Manager](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager analyse l’adresse IP source de la requête DNS afin de déterminer la région géographique d’origine de cette dernière. Il examine ensuite s’il existe un point de terminaison auquel cette région géographique est mappée. Cette recherche commence au niveau de granularité le plus bas (State/Province [État/Province] où il est pris en charge, Pays/Région ailleurs) et remonte jusqu’au niveau le plus élevé, à savoir **World** (Monde). La première correspondance trouvée à l’aide de ce balayage est désignée en tant que point de terminaison à renvoyer dans la réponse à la requête. En cas de correspondance avec un point de terminaison de type imbriqué, un point de terminaison de ce profil enfant est renvoyé en fonction de sa méthode de routage. Les points suivants s’appliquent à ce comportement :

- Une zone géographique peut être mappée à un seul point de terminaison dans un profil Traffic Manager lorsque le type de routage est défini sur Geographic (Géographique). Cela garantit un routage déterministe du trafic des utilisateurs et permet aux clients de mettre en œuvre des scénarios nécessitant des limites géographiques claires.
- Si la région d’un utilisateur correspond au mappage géographique de deux points de terminaison différents, Traffic Manager sélectionne le point de terminaison présentant le niveau de granularité le plus bas et n’envisage pas la possibilité de router les demandes provenant de cette région vers l’autre point de terminaison. Par exemple, considérons un profil présentant le type de routage Geographic (Géographique) et deux points de terminaison, Endpoint1 et Endpoint2. Endpoint1 est configuré pour recevoir le trafic provenant d’Irlande et Endpoint2 est configuré pour recevoir le trafic provenant d’Europe. Une demande provenant d’Irlande est systématiquement routée vers le point de terminaison Endpoint1.
- Comme une région peut être mappée à un seul point de terminaison, Traffic Manager renvoie ce point de terminaison, qu’il soit intègre ou non.

    >[!IMPORTANT]
    >Il est vivement recommandé aux clients utilisant la méthode de routage Geographic (Géographique) de l’associer avec les points de terminaison de type imbriqué qui présentent des profils enfants contenant au moins deux points de terminaison.
- Si une correspondance de point de terminaison est trouvée et que ce point de terminaison se trouve à l’état **Arrêté**, Traffic Manager renvoie une réponse NODATA. Dans ce cas, aucune autre recherche n’est effectuée plus haut dans la hiérarchie de la région géographique. Ce comportement s’applique également aux points de terminaison de type imbriqué lorsque le profil enfant se trouve à l’état **Arrêté** ou **Désactivé**.
- Si un point de terminaison se trouve à l’état **Désactivé**, il n’est pas inclus dans le processus de mise en correspondance avec la région. Ce comportement s’applique également aux points de terminaison de type imbriqué lorsque le point de terminaison se trouve à l’état **Désactivé**.
- Si une requête provient d’une région géographique qui n’a aucun mappage dans ce profil, Traffic Manager renvoie une réponse NODATA. Par conséquent, il est vivement recommandé aux clients d’utiliser le routage géographique avec un point de terminaison, idéalement de type imbriqué avec au moins deux points de terminaison dans le profil enfant, auquel la région **World** (Monde) est affectée. Cela garantit également que les adresses IP qui ne sont mappées à aucune région sont gérées.

Comme expliqué dans [Fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md), Traffic Manager ne reçoit pas de requêtes DNS provenant directement de clients. Au lieu de cela, les requêtes DNS proviennent du service DNS récursif que les clients sont configurés pour utiliser. Par conséquent, l’adresse IP utilisée pour déterminer la région n’est pas l’adresse IP du client, mais celle du service DNS récursif. Dans la pratique, cette adresse IP est un bon proxy pour le client.


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment développer des applications à haute disponibilité à l’aide de la [surveillance de points de terminaison Traffic Manager](traffic-manager-monitoring.md)

En savoir plus sur la [création d’un profil Traffic Manager](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png



