---
title: Solution Network Performance Monitor dans Azure | Microsoft Docs
description: "Network Performance Monitor dans Azure vous aide à surveiller les performances de vos réseaux, presque en temps réel, afin de détecter et localiser d’éventuels goulots d’étranglement affectant les performances réseau."
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 399fe552d5c7d9a96cdabc2a1dfafe99635d4a61
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Solution Network Performance Monitor dans Azure

![Symbole de Network Performance Monitor](./media/log-analytics-network-performance-monitor/npm-symbol.png)


Network Performance Monitor (NPM) est une solution de surveillance réseau hybride informatique qui vous permet de surveiller les performances réseau entre différents points de votre infrastructure réseau, la connectivité réseau vers les points de terminaison de service/d’application, et les performances d’Azure ExpressRoute.  

NPM détecte divers problèmes réseau, tels que les pertes de trafic, les erreurs de routage et d’autres problèmes que les méthodes de surveillance réseau classiques ne sont pas en mesure de détecter. La solution génère des alertes, notifie des dépassements de seuil pour une liaison réseau, détecte en temps opportun les problèmes de performances réseau et localise la source de ces problèmes en identifiant un segment ou un appareil réseau spécifique. 

NPM offre trois fonctionnalités étendues : 

[Analyseur de performances](log-analytics-network-performance-monitor-performance-monitor.md) : vous permet de surveiller la connectivité réseau dans les déploiements de cloud et les emplacements locaux, plusieurs centres de données ainsi que les filiales et les applications/micro-services multiniveaux critiques. Avec l’Analyseur de performances, vous pouvez détecter les problèmes réseau avant que vos utilisateurs ne se plaignent.  

[Moniteur de points de terminaison de service](log-analytics-network-performance-monitor-service-endpoint.md) : vous permet de surveiller la connectivité entre vos utilisateurs et les services qui vous intéressent, de déterminer l’infrastructure présente dans le chemin d’accès et d’identifier l’emplacement des goulots d’étranglement. Découvrez les pannes avant vos utilisateurs et déterminez l’emplacement exact des problèmes sur votre chemin d’accès réseau. 

Cette fonctionnalité vous permet d’effectuer des tests HTTP, HTTPS, TCP et ICMP afin de surveiller, presque en temps réel ou dans le temps, la disponibilité et le temps de réponse de votre service, ainsi que la contribution du réseau dans la perte de paquets et la latence. Grâce au mappage de la topologie de réseau, vous pouvez isoler les ralentissements du réseau en identifiant les points problématiques sur le chemin d’accès réseau entre le nœud et le service, avec les données de latence sur chaque tronçon. Au moyen de tests intégrés, surveillez la connectivité réseau vers Office 365 et Dynamics CRM sans aucune configuration préalable. Avec cette fonctionnalité, vous pouvez surveiller la connectivité réseau vers n’importe quel point de terminaison compatible TCP, comme des sites web, le SaaS, des applications PaaS, des bases de données SQL, etc.  

[Moniteur ExpressRoute](log-analytics-network-performance-monitor-expressroute.md) : surveillez la connectivité de bout en bout et les performances entre vos filiales et Azure, le tout sur Azure ExpressRoute.  
 

## <a name="set-up-and-configure"></a>Installer et configurer

### <a name="install-and-configure-agents"></a>Installer et configurer des agents 

Utilisez les procédures de base d’installation des agents décrites dans [Connecter des ordinateurs Windows à Log Analytics](log-analytics-windows-agents.md) et [Connexion d’Operations Manager à Log Analytics](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Où installer les agents 

**Analyseur de performances :** installez des agents OMS sur au moins un nœud connecté à chaque sous-réseau à partir duquel vous souhaitez surveiller la connectivité réseau vers d’autres sous-réseaux.  

Pour surveiller une liaison réseau, vous devez installer des agents sur les deux points de terminaison de cette dernière.  Si vous n’êtes pas certain de la topologie de votre réseau, installez les agents sur des serveurs présentant des charges de travail critiques entre lesquels vous souhaitez analyser les performances réseau. Par exemple, si vous souhaitez surveiller la connexion réseau entre un serveur web et un serveur exécutant SQL Server, vous pouvez installer un agent sur les deux serveurs. Les agents surveillent la connectivité (liaisons) réseau entre les hôtes, et non les hôtes proprement dits. 

**Moniteur de points de terminaison de service :** installez l’agent OMS sur chaque nœud à partir duquel vous souhaitez surveiller la connectivité réseau vers le point de terminaison de service. Par exemple, si vous envisagez de surveiller la connectivité réseau vers Office 365 à partir de votre site Office O1, O2 et O3, installez l’agent OMS sur au moins un nœud dans O1, O2 et O3, respectivement. 

**Moniteur ExpressRoute :** installez au moins un agent OMS dans votre réseau virtuel Azure et au moins un agent dans votre sous-réseau local, connecté au moyen de l’homologation privée ExpressRoute.  

### <a name="configure-oms-agents-for-monitoring"></a>Configurer des agents OMS pour la surveillance  

NPM utilise des transactions synthétiques pour surveiller les performances réseau entre les agents sources et cibles. Cette solution permet de choisir une surveillance effectuée par le biais du protocole TCP ou du protocole ICMP dans les fonctionnalités de l’Analyseur de performances et du Moniteur de points de terminaison de service, tandis que le protocole TCP est utilisé pour le Moniteur ExpressRoute. Vérifiez que le pare-feu autorise les communications entre les agents OMS utilisés pour la surveillance sur le protocole que vous avez choisi.  

**Protocole TCP :** si vous avez choisi le protocole TCP pour la surveillance, ouvrez le port du pare-feu sur les agents utilisés pour les fonctionnalités de l’Analyseur de performances et du Moniteur ExpressRoute afin de vous assurer que les agents peuvent se connecter entre eux. Pour ce faire, exécutez le script PowerShell EnableRules.ps1 sans paramètre dans une fenêtre PowerShell avec des privilèges Administrateur.  

Le script crée les clés de Registre requises par la solution, ainsi que des règles de pare-feu Windows pour autoriser les agents à créer des connexions TCP entre eux. Les clés de Registre créées par le script spécifient également s’il faut enregistrer les journaux de débogage et le chemin d’accès des fichiers journaux. Le script définit également le port TCP de l’agent utilisé pour la communication. Les valeurs de ces clés étant définies automatiquement par le script, vous n’avez pas à les modifier manuellement. Le port ouvert par défaut est 8084. Vous pouvez utiliser un port personnalisé en ajoutant le paramètre portNumber au script. Toutefois, le même port doit être utilisé sur tous les ordinateurs exécutant le script. 

>[!NOTE]
> Le script configure uniquement le pare-feu Windows en local. Si vous avez un pare-feu réseau, vous devez vous assurer qu’il autorise le trafic destiné au port TCP utilisé par NPM 

>[!NOTE]
> Vous n’avez pas besoin d’exécuter le script PowerShell EnableRules.ps1 pour le Moniteur de points de terminaison de service 

 

**Protocole ICMP** : si vous avez choisi le protocole ICMP pour la surveillance, activez les règles de pare-feu suivantes afin d’utiliser le protocole ICMP de manière fiable : 

 
```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```
 

### <a name="configure-the-solution"></a>Configuration de la solution 

1. Ajoutez la solution Analyseur de performances réseau à votre espace de travail depuis la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) ou en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). 
2. Ouvrez votre espace de travail Log Analytics et cliquez sur la vignette **Aperçu**.  
3. Cliquez sur la vignette libellée **Network Performance Monitor** , qui présente le message  *La solution nécessite une configuration supplémentaire*.

    ![Vignette NPM](media/log-analytics-network-performance-monitor/npm-config.png)

3. Sur la page **Installation**, vous pouvez installer des agents OMS et configurer des agents pour la surveillance dans la vue **Paramètres communs**. Comme expliqué précédemment, si vous avez déjà installé et configuré des agents OMS, cliquez sur la vue **Installation** pour configurer la fonctionnalité que vous souhaitez utiliser.  

    **Vue de l’Analyseur de performances** : choisissez le protocole qui sera utilisé pour les transactions synthétiques dans la règle d’analyse de performances par défaut, puis cliquez sur Enregistrer et continuer. Le choix du protocole ne concerne que la règle par défaut générée par le système. Vous devez choisir le protocole chaque fois que vous créez explicitement une règle de l’Analyseur de performances. Vous pouvez toujours passer aux paramètres de règle par défaut dans l’onglet Analyseur de performances (qui apparaît une fois que vous avez terminé la configuration du jour 0) et modifier le protocole ultérieurement. Si vous n’êtes pas intéressé par la fonctionnalité Analyseur de performances, vous pouvez désactiver la règle par défaut dans les paramètres de règle par défaut de l’onglet Analyseur de performances. 

    ![Configuration de NPM](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
    **Vue du Moniteur de points de terminaison de service** : cette fonctionnalité fournit des tests préconfigurés intégrés afin de surveiller la connectivité réseau vers Office 365 et Dynamics 365 à partir de vos agents. Pour sélectionner les services Office 365 et Dynamics 365 que vous souhaitez surveiller, activez la case à cocher située en regard de ces derniers. Sélectionnez les agents à partir desquels vous souhaitez exercer une surveillance en cliquant sur le bouton Ajouter des agents. Si vous ne souhaitez pas utiliser cette fonctionnalité ou préférez la configurer ultérieurement, vous pouvez ignorer cette étape et cliquer directement sur **Enregistrer** et **continuer** sans rien sélectionner.  

    ![Configuration de NPM](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

    **Vue du Moniteur ExpressRoute** : cliquez sur le bouton **Découvrir maintenant** pour détecter toutes les homologations privées ExpressRoute connectées aux réseaux virtuels dans l’abonnement Azure lié à cet espace de travail Log Analytics.  


    >[!NOTE] 
    > La solution ne détecte pour le moment que les homologations privées ExpressRoute. 

    >[!NOTE] 
    > Seules ces homologations privées, qui sont connectées aux réseaux virtuels associés à l’abonnement lié à cet espace de travail Log Analytics, sont détectées. Si votre ExpressRoute est connecté aux réseaux virtuels en dehors de l’abonnement lié à cet espace de travail, vous devez créer un espace de travail Log Analytics dans ces abonnements et utiliser NPM pour surveiller ces homologations. 

    ![Configuration de NPM](media/log-analytics-network-performance-monitor/npm-express-route.png)

    Une fois la détection terminée, les connexions d’homologations privées sont répertoriées dans une table.  

    ![Configuration de NPM](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
    La surveillance de ces homologations est initialement désactivée. Cliquez sur chaque homologation à surveiller et configurez leur surveillance à partir de la vue de détails à droite.  Cliquez sur le bouton Enregistrer pour enregistrer la configuration. Consultez [Configure ExpressRoute monitoring]() (Configurer la surveillance ExpressRoute) pour en savoir plus.  

    Une fois l’installation terminée, les données sont remplies en l’espace de 30 minutes à une heure. Pendant que la solution agrège les données à partir de votre réseau, le message *La solution nécessite une configuration supplémentaire* apparaît sur la vignette d’aperçu NPM. Une fois que les données sont collectées et indexées, la vignette d’aperçu change et vous présente le résumé de l’intégrité de votre réseau. Vous pouvez ensuite choisir de modifier la surveillance des nœuds sur lesquels les agents OMS sont installés, ainsi que les sous-réseaux détectés à partir de votre environnement 

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Modifier les paramètres de surveillance pour les sous-réseaux et les nœuds 

Tous les sous-réseaux pour lesquels au moins un agent a été installé sont répertoriés sous l’onglet Sous-réseaux de la page de configuration. 


Activer ou désactiver la surveillance de sous-réseaux spécifiques 

1. Activez ou désactivez la case située en regard du champ  **ID de sous-réseau** , puis assurez-vous que l’option  **Utiliser pour la surveillance**  est activée ou désactivée, selon le cas. Vous pouvez activer ou désactiver plusieurs sous-réseaux. Les sous-réseaux désactivés ne sont pas analysés, car les agents sont mis à jour pour arrêter l’envoi de commandes ping à d’autres agents. 
2. Choisissez les nœuds que vous souhaitez surveiller pour un sous-réseau spécifique en sélectionnant ce dernier dans la liste, puis en déplaçant les nœuds requis entre les listes de nœuds non surveillés et surveillés. Vous pouvez ajouter une **description personnalisée au** sous-réseau. 
3. Pour enregistrer la configuration, cliquez sur **Enregistrer**. 

#### <a name="choose-nodes-to-monitor"></a>Choisir les nœuds à surveiller

Tous les nœuds sur lesquels un agent est installé sont répertoriés sous l’onglet **Nœuds**. 

1. Activez ou désactivez les nœuds que vous souhaitez surveiller ou cesser de surveiller. 
2. Cliquez sur  **Utiliser pour la surveillance** ou désactivez cette option, selon le cas. 
3. Cliquez sur **Enregistrer**. 


Configurez la ou les fonctionnalités qui vous intéressent : 
- Configurer [Analyseur de performances](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Configurer [Moniteur de points de terminaison de service](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Configurer [Moniteur ExpressRoute](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Détails sur la collecte de données
L’Analyseur de performances réseau utilise des paquets de transfert TCP SYN-SYNACK-ACK si le protocole TCP est choisi, et ICMP ECHO ICMP ECHO REPLY si ICMP est choisi comme protocole pour collecter les informations de latence et de perte. La détermination d’itinéraire permet également d’obtenir des informations de topologie.

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur la manière dont l’Analyseur de performances réseau collecte des données.

| plateforme | Agent direct | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Liaisons TCP/Messages ICMP ECHO toutes les 5 secondes, données envoyées toutes les 3 minutes |
 

 
La solution utilise des transactions synthétiques pour évaluer l’intégrité du réseau. Les agents OMS installés à différents points du réseau échangent des paquets TCP ou un écho ICMP (en fonction du protocole sélectionné pour l’analyse) les uns avec les autres. Au cours du processus, les agents détectent, le cas échéant, la durée des boucles et la perte de paquets. Périodiquement, chaque agent effectue également une détermination d’itinéraire d’autres agents afin de trouver tous les itinéraires à tester au sein du réseau. Ces données permettent aux agents de déduire les chiffres relatifs aux pertes de paquets et à la latence du réseau. Les tests sont répétés toutes les cinq secondes, et les données sont agrégées pendant une période de trois minutes par les agents avant leur chargement vers le service Log Analytics. 



>[!NOTE]
> Bien que les agents communiquent fréquemment entre eux, ils ne génèrent pas beaucoup de trafic réseau lors des tests. Pour déterminer les pertes et la latence, les agents s’appuient uniquement sur les paquets d’établissements de liaisons TCP SYN-SYNACK-ACK. Aucun paquet de données n’est échangé. Durant ce processus, les agents communiquent entre eux uniquement si nécessaire, et la topologie de communication des agents est optimisée pour réduire le trafic réseau.

## <a name="using-the-solution"></a>Utilisation de la solution 

### <a name="npm-overview-tile"></a>Vignette d’aperçu NPM 

Une fois la solution Network Performance Monitor activée, la vignette de la solution sur la page d’aperçu fournit une vue d’ensemble rapide de l’intégrité du réseau. 

 ![Vignette d’aperçu NPM](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Tableau de bord de l’Analyseur de performances réseau 

La page **Principaux événements d’intégrité du réseau** fournit la liste des alertes et des événements d’intégrité les plus récents du système, ainsi que le temps qui s’est écoulé depuis l’activation de l’événement. Un événement ou une alerte d’intégrité apparaît chaque fois que la valeur de la métrique choisie (perte, latence, temps de réponse ou utilisation de la bande passante) pour la règle de surveillance dépasse le seuil fixé. 

La page  **Analyseur de performances** fournit un résumé de l’intégrité des liaisons réseau et des liaisons de sous-réseau surveillées par la solution. La vignette Topologie indique le nombre de chemins d’accès réseau surveillés dans votre réseau. Cliquez sur cette vignette pour accéder directement à la vue Topologie. 

La page  **Moniteur de points de terminaison de service** fournit un résumé de l’intégrité des différents tests que vous avez créés. La vignette Topologie indique le nombre de points de terminaison surveillés. Cliquez sur cette vignette pour accéder directement à la vue Topologie.

La page  **Moniteur ExpressRoute** fournit un résumé de l’intégrité des différentes connexions d’homologation ExpressRoute surveillées par la solution. La vignette Topologie indique le nombre de chemins d’accès réseau par le biais du ou des circuits ExpressRoute surveillés dans votre réseau. Cliquez sur cette vignette pour accéder directement à la vue Topologie.

La page  **Requêtes courantes** contient une série de requêtes de recherche qui permettent d’extraire directement des données de surveillance réseau brutes. Vous pouvez utiliser ces requêtes comme point de départ pour créer vos propres requêtes de génération de rapports personnalisés. 

![Tableau de bord NPM](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Explorer en profondeur 

Vous pouvez cliquer sur différents liens du tableau de bord de la solution pour explorer de façon plus approfondie un aspect spécifique. Par exemple, lorsque vous voyez une alerte ou une liaison réseau défectueuse s’afficher sur le tableau de bord, vous pouvez cliquer dessus pour approfondir vos recherches. Vous êtes alors redirigé vers une page répertoriant tous les liens de sous-réseau pour une liaison réseau particulière. Vous pouvez y voir l’état de perte, de latence et d’intégrité de chaque liaison de sous-réseau, et identifier rapidement les liaisons à l’origine du problème. Vous pouvez ensuite cliquer sur  **Afficher les liens de nœud**  pour voir tous les liens de nœud concernant la liaison de sous-réseau défectueuse. Ensuite, vous pouvez voir les liens de nœud à nœud, et identifier les liens de nœud défectueux. 

Vous pouvez cliquer sur  **Afficher la topologie**  pour afficher la topologie, tronçon par tronçon, des itinéraires entre les nœuds sources et cibles. Les itinéraires défectueux sont indiqués en rouge et vous pouvez afficher la latence apportée par chaque tronçon afin d’identifier rapidement le problème affectant une portion spécifique du réseau. 

 

### <a name="network-state-recorder"></a>Enregistreur de l’état du réseau 

Chaque vue affiche une capture instantanée de l’intégrité de votre réseau à un moment précis dans le temps. Par défaut, c’est l’état le plus récent qui s’affiche. La barre située en haut de la page affiche le point dans le temps pour lequel l’état est affiché. Vous pouvez choisir de revenir en arrière et d’afficher la capture instantanée de l’intégrité de votre réseau en cliquant sur la barre dans Actions. Vous pouvez également activer ou désactiver l’actualisation automatique pour n’importe quelle page pendant que vous consultez le dernier état. 

 ![Enregistreur de l’état du réseau](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Graphiques de tendances 

À chaque niveau que vous explorez, vous pouvez voir la tendance de la métrique applicable (perte, latence, temps de réponse, utilisation de la bande passante). Vous pouvez modifier l’intervalle de temps pour cette tendance à l’aide du contrôle de temps situé en haut du graphique. 

Les graphiques de tendances présentent une perspective historique des performances d’une métrique de performances. Certains problèmes réseau sont temporaires par nature, ce qui complique leur identification en consultant simplement l’état actuel du réseau. En effet, ces problèmes peuvent apparaître rapidement et disparaître avant que quiconque s’en aperçoive, avant de réapparaître par la suite. Ces problèmes temporaires peuvent également compliquer la tâche des administrateurs d’applications, car ils se manifestent souvent sous la forme d’allongements inexpliqués du temps de réponse de celles-ci, même si tous leurs composants semblent s’exécuter sans difficulté. 

Vous pouvez facilement détecter ces types de problèmes en examinant un graphique de tendances où le problème apparaît comme un pic soudain de latence ou de perte de paquets du réseau. Vous pouvez ensuite étudier le problème à l’aide de l’enregistreur de l’état du réseau pour afficher la capture instantanée de réseau et la topologie de ce point dans le temps où le problème s’est produit. 

 
![Graphiques de tendances](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mappage de la topologie 

NPM affiche, tronçon par tronçon, la topologie des itinéraires entre le point de terminaison source et le point de terminaison cible sur une carte topologique interactive. Vous pouvez afficher le mappage de la topologie en cliquant sur la vignette **Topologie** du tableau de bord de la solution ou en cliquant sur le lien **Afficher la topologie** dans les pages d’exploration.  

La carte topologique affiche le nombre d’itinéraires existant entre la source et la destination, ainsi que les chemins d’accès qu’empruntent les paquets de données. La latence apportée par chaque tronçon réseau est également visible. Tous les chemins d’accès pour lesquels la latence de chemin d’accès totale est supérieure au seuil (défini dans la règle de surveillance correspondante) sont affichés en rouge.  

Lorsque vous cliquez sur un nœud ou pointez dessus sur la carte topologique, vous pouvez voir des propriétés du nœud telles que son adresse IP et son nom de domaine complet. Cliquez sur un tronçon pour voir son adresse IP. Vous pouvez identifier le tronçon réseau défectueux en notant la latence apportée par ce dernier. Vous pouvez choisir de filtrer des itinéraires particuliers à l’aide des filtres du volet Actions réductible. Vous pouvez également simplifier les topologies réseau en masquant les tronçons intermédiaires à l’aide du curseur du volet Actions. Vous pouvez effectuer un zoom avant ou arrière sur la carte topologique à l’aide de la roulette de la souris. 

Notez que la topologie cartographiée est celle de la couche 3 et qu’elle ne contient pas les connexions et appareils de la couche 2. 

 
![Mappage de la topologie](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Recherche Log Analytics 

Toutes les données présentées sous forme graphique dans le tableau de bord NPM et les pages d’exploration sont également disponibles en mode natif dans [Recherche Log Analytics](log-analytics-log-search-new.md). Vous pouvez effectuer une analyse interactive des données dans le référentiel, mettre en corrélation les données issues de différentes sources, créer des alertes personnalisées, créer des vues personnalisées et exporter les données vers Excel, PowerBI ou un lien partageable. La zone Requêtes courantes du tableau de bord comprend des requêtes utiles qui peuvent vous servir de point de départ pour créer vos propres requêtes et rapports. 

 

## <a name="provide-feedback"></a>Fournir des commentaires 

**UserVoice** : vous pouvez publier vos idées concernant les fonctions de l’Analyseur de performances réseau que vous aimeriez voir améliorer. Visitez notre  [page UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

**Rejoignez notre cohorte**  : nous sommes toujours ravis d’accueillir de nouveaux clients dans notre cohorte. Dans ce cadre, vous pouvez accéder en avant-première aux nouvelles fonctionnalités et nous aider à améliorer Network Performance Monitor. Si vous souhaitez y participer, répondez à cette  [enquête rapide](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Étapes suivantes 
- En savoir plus sur [Analyseur de performances](log-analytics-network-performance-monitor-performance-monitor.md), [Moniteur de points de terminaison de service](log-analytics-network-performance-monitor-performance-monitor.md) et [Moniteur ExpressRoute](log-analytics-network-performance-monitor-expressroute.md). 
