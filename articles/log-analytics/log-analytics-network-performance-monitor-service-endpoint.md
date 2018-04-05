---
title: Solution Analyseur de performances réseau dans Azure Log Analytics | Microsoft Docs
description: Utilisez la fonctionnalité de gestionnaire de point de terminaison de service Network Performance Monitor pour surveiller la connectivité réseau à n’importe quel point de terminaison qui a un port TCP ouvert.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: b21d711e59ddc762eaf72f49e501d9f324d75105
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="service-endpoint-monitor"></a>Moniteur de points de terminaison de service

Vous pouvez utiliser la fonctionnalité de gestionnaire de point de terminaison de service [Network Performance Monitor](log-analytics-network-performance-monitor.md) pour surveiller la connectivité réseau à n’importe quel point de terminaison qui a un port TCP ouvert. Ces points de terminaison incluent des sites web, des applications SaaS, des applications PaaS et des bases de données SQL. 

Vous pouvez effectuer les fonctions suivantes avec le moniteur de points de terminaison de service : 

- Surveiller la connectivité réseau à vos applications et aux services réseau à partir de plusieurs succursales ou sites. Les applications et services réseau incluent Office 365, Dynamics CRM, les applications métier internes et les bases de données SQL.
- Utiliser des tests intégrés pour surveiller la connectivité réseau vers les points de terminaison Office 365 et Dynamics 365. 
- Déterminer le temps de réponse, la latence du réseau et la perte de paquets rencontrée lors de la connexion au point de terminaison.
- Déterminer si les performances médiocres des applications sont dues au réseau ou à un problème du côté du fournisseur de l’application.
- Identifier les zones réactives sur le réseau qui peuvent être à l’origine des performances médiocres des applications en affichant la latence de chaque tronçon sur une carte topologique.


![Moniteur de points de terminaison de service](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>Configuration 
Pour ouvrir la configuration de Network Performance Monitor, ouvrez la [solution Network Performance Monitor](log-analytics-network-performance-monitor.md) et sélectionnez **Configurer**.

![Configurer Network Performance Monitor](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-operations-management-suite-agents-for-monitoring"></a>Configurer les agents Operations Management Suite pour la surveillance
Activez les règles de pare-feu suivantes sur les nœuds utilisés pour la surveillance afin que la solution puisse détecter la topologie de vos nœuds vers le point de terminaison de service : 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action 
```

### <a name="create-service-endpoint-monitor-tests"></a>Créer des tests de moniteur de points de terminaison de service 

Commencez par créer des tests pour surveiller la connectivité réseau vers les points de terminaison de service.

1. Sélectionnez l’onglet **Moniteur de points de terminaison de service**.
2. Sélectionnez **Ajouter un test**, puis entrez le nom et la description du test. 
3. Sélectionnez le type de test :<br>

    * Sélectionnez **Web** pour surveiller la connectivité à un service qui répond à des requêtes HTTP/S, comme outlook.office365.com ou bing.com.<br>
    * Sélectionnez **Réseau** pour surveiller la connectivité à un service qui répond à des requêtes TCP, mais pas à des requêtes HTTP/S, comme un serveur SQL, un serveur FTP ou un port SSH. 
4. Si vous ne souhaitez pas effectuer de mesures sur le réseau, telles que la latence du réseau, la perte de paquets et la découverte des topologies, désactivez la case pour **effectuer des mesures sur un réseau**. Cochez-la pour profiter pleinement de la fonctionnalité. 
5. Dans le champ **Cible**, entrez l’adresse IP, le nom de domaine complet sur lesquels porte la surveillance de la connectivité réseau.
6. Dans le champ **Numéro de port**, entrez le numéro de port du service cible. 
7. Dans le champ **Fréquence de test**, entrez la fréquence à laquelle exécuter le test. 
8. Sélectionnez les nœuds à partir desquels vous souhaitez surveiller la connectivité réseau du service. 

    >[!NOTE]
    > Pour les nœuds de serveur Windows, la fonctionnalité utilise des requêtes TCP pour effectuer les mesures de réseau. Pour les nœuds de client Windows, la fonctionnalité utilise des requêtes ICMP pour effectuer les mesures de réseau. Dans certains cas, l’application cible bloque les demandes ICMP entrantes lorsque les nœuds sont basés sur le client Windows. La solution ne peut pas effectuer de mesures sur un réseau. Nous vous recommandons d’utiliser des nœuds de serveur Windows dans ce cas. 

9. Si vous ne souhaitez pas créer d’événements d’intégrité pour les éléments sélectionnés, désactivez l’option **Activer la surveillance de l’intégrité sur les cibles couvertes par ce test**. 
10. Choisissez les conditions d’analyse. Vous pouvez définir des seuils personnalisés pour la génération d’événements d’intégrité en entrant des valeurs de seuil. Chaque fois que la valeur d’une condition dépasse son seuil sélectionné pour la paire de réseaux ou sous-réseaux sélectionnée, un événement d’intégrité est généré. 
11. Sélectionnez **Enregistrer** pour enregistrer la configuration. 

    ![Configuration des tests du moniteur de points de terminaison de service](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Procédure pas à pas 

Accédez à la vue du tableau de bord de Network Performance Monitor. La page **Moniteur de points de terminaison de service** fournit un résumé de l’intégrité des différents tests que vous avez créés. 

![Page Moniteur de points de terminaison de service](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

Sélectionnez la vignette pour afficher les détails des tests sur la page **Tests**. Dans la table de gauche, vous pouvez consulter l’intégrité à un moment donné, ainsi que la valeur du temps de réponse du service, la latence du réseau et la perte de paquets pour tous les tests. Utilisez le contrôle de l’enregistreur de l’état du réseau pour afficher l’instantané du réseau à un autre moment passé. Sélectionnez le test à examiner dans la table. Vous pouvez afficher la tendance historique de la perte, la latence et les valeurs de temps de réponse dans les graphiques du volet de droite. Sélectionnez le lien **Détails du test** pour afficher les performances de chaque nœud.

![Tests du moniteur de points de terminaison de service](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

Dans la vue des **nœuds de test**, vous pouvez observer la connectivité réseau de chaque nœud. Sélectionnez le nœud qui présente une détérioration des performances. Il s’agit du nœud sur lequel l’application s’exécute trop lentement.

Déterminez si les performances médiocres de l’application sont dues au réseau ou à un problème émanant du fournisseur de l’application en observant la corrélation entre le temps de réponse de l’application et la latence du réseau. 

* **Problème d’application :** un pic dans le temps de réponse, mais une latence de réseau cohérente suggèrent que le réseau fonctionne correctement et que le problème émane peut-être de l’application. 

    ![Problème d’application du moniteur de point de terminaison de service](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

* **Problème réseau :** un pic dans le temps de réponse accompagné d’un pic correspondant de la latence du réseau suggère que l’augmentation du temps de réponse est peut-être due à une augmentation de la latence du réseau. 

    ![Problème réseau du moniteur de points de terminaison de service](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

Une fois que vous avez déterminé que le problème provient du réseau, sélectionnez la vue **Topologie** pour identifier le tronçon problématique sur la carte topologique. L’image suivante en contient un exemple. 96 des 105 ms de latence totale entre le nœud et le point de terminaison d’application sont dues au tronçon marqué en rouge. Une fois que vous avez identifié le tronçon problématique, vous pouvez effectuer une action corrective. 

![Tests du moniteur de points de terminaison de service](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostics 

Si vous observez une anomalie, procédez comme suit :

* Si le temps de réponse, la perte de connexion réseau et la latence sont indiqués comme étant non applicables, cela peut être dû aux raisons suivantes :

    - L’application est arrêtée.
    - Le nœud utilisé pour la vérification de la connectivité réseau du service est arrêté.
    - La cible entrée dans la configuration de test est incorrecte.
    - Le nœud n’a pas de connectivité réseau.

* Si le temps de réponse est valide, et que la perte de connexion réseau et la latence sont indiqués comme étant non applicables, cela peut être dû aux raisons suivantes :

    - Si le nœud utilisé pour la vérification de la connectivité réseau du service est un ordinateur client de Windows, le service cible bloque les requêtes ICMP ou un pare-feu réseau bloque les requêtes ICMP provenant du nœud d’origine.
    - La case pour **effectuer des mesures sur un réseau** est vide dans la configuration de test. 

* Si le temps de réponse n’est pas applicable, et que la perte de connexion réseau et le temps de latence sont pourtant valides, le service cible n’est peut-être pas une application web. Modifiez la configuration de test et choisissez le type de test **Réseau** au lieu de **Web**. 

* Si l’application est lente, déterminez si les performances médiocres sont dues au réseau ou à un problème du côté du fournisseur de l’application.


## <a name="next-steps"></a>Étapes suivantes
[Rechercher dans les journaux](log-analytics-log-searches.md) pour afficher des enregistrements de données détaillées sur les performances réseau.
