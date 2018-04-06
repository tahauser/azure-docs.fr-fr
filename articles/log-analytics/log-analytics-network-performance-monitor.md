---
title: Solution Network Performance Monitor dans Azure | Microsoft Docs
description: Network Performance Monitor dans Azure vous aide à analyser les performances de vos réseaux, en temps quasi réel, afin de détecter et localiser d’éventuels goulots d’étranglement affectant les performances réseau.
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
ms.openlocfilehash: 847213a5b8aab908d412ecc9aecec984aec1a9ba
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Solution Network Performance Monitor dans Azure

![Symbole de Network Performance Monitor](./media/log-analytics-network-performance-monitor/npm-symbol.png)


Network Performance Monitor est une solution d’analyse réseau hybride basé sur le cloud qui vous permet d’analyser les performances du réseau entre différents points de votre infrastructure réseau. Vous pouvez également analyser la connectivité réseau avec le service et les points de terminaison d’application ainsi que les performances d’Azure ExpressRoute. 

Network Performance Monitor détecte les problèmes réseau comme le blocage du trafic, les erreurs de routage et autres problèmes que les méthodes d’analyse réseau classiques ne sont pas capables de détecter. La solution génère des alertes et vous informe en cas de dépassement d’un seuil pour une liaison réseau. Il assure également la détection en temps opportun des problèmes de performances réseau et en localise la source en identifiant un segment ou un appareil réseau particuliers. 

Network Performance Monitor offre trois fonctionnalités générales : 

* [Analyseur des performances](log-analytics-network-performance-monitor-performance-monitor.md) : vous permet d’analyser la connectivité réseau dans les déploiements de cloud et les emplacements locaux, plusieurs centres de données ainsi que les filiales et les applications micro-services multiniveaux critiques. Avec l’Analyseur de performances, vous pouvez détecter les problèmes réseau avant que vos utilisateurs ne se plaignent.

* [Moniteur des points de terminaison de service](log-analytics-network-performance-monitor-service-endpoint.md) : vous permet d’analyser la connectivité entre vos utilisateurs et les services qui vous intéressent, de déterminer l’infrastructure présente dans le chemin d’accès et d’identifier l’emplacement des goulots d’étranglement. Découvrez les pannes avant vos utilisateurs et déterminez l’emplacement exact des problèmes sur votre chemin d’accès réseau. 

    Cette fonctionnalité vous permet d’effectuer des tests HTTP, HTTPS, TCP et ICMP afin d’analyser, en temps quasi réel ou dans le temps, la disponibilité et le temps de réponse de votre service. Vous pouvez également analyser la contribution du réseau en perte de paquets et en latence. Avec un mappage de topologie réseau, vous pouvez isoler les ralentissements du réseau. Vous pouvez identifier les points problématiques sur le chemin d’accès réseau entre le nœud et le service, avec les données de latence sur chaque tronçon. Au moyen de tests intégrés, analysez la connectivité réseau vers Office 365 et Dynamics CRM sans aucune configuration préalable. Avec cette fonctionnalité, vous pouvez analyser la connectivité réseau vers n’importe quel point de terminaison compatible TCP, comme des sites web, des applications SaaS, des applications PaaS et des bases de données SQL.

* [Moniteur ExpressRoute](log-analytics-network-performance-monitor-expressroute.md) : analysez la connectivité de bout en bout et les performances entre vos filiales et Azure, le tout sur Azure ExpressRoute.  

Plus d’informations sur les différentes fonctionnalités de prise en charge par [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) sont disponibles en ligne.
 
## <a name="supported-regions"></a>Régions prises en charge
NPM permet d’analyser la connectivité entre les réseaux et les applications dans n’importe quelle partie du monde, à partir d’un espace de travail hébergé dans une des régions suivantes :
* Europe de l'Ouest
* Centre-Ouest des États-Unis
* Est des États-Unis
* Asie du Sud-Est
* Australie du Sud-Est
* Virginie du gouvernement des États-Unis

La liste des régions prises en charge pour le Moniteur ExpressRoute est disponible dans la [documentation](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117#regions).


## <a name="set-up-and-configure"></a>Installer et configurer

### <a name="install-and-configure-agents"></a>Installer et configurer des agents 

Utilisez les procédures de base d’installation des agents décrites dans [Connecter des ordinateurs Windows à Azure Log Analytics](log-analytics-windows-agents.md) et [Connecter Operations Manager à Log Analytics](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Où installer les agents 

* **Analyseur de performances** : installez des agents Operations Management Suite sur au moins un nœud connecté à chaque sous-réseau à partir duquel vous souhaitez analyser la connectivité réseau vers d’autres sous-réseaux.

    Pour analyser un lien réseau, installez des agents sur les deux points de terminaison de cette dernière. Si vous n’êtes pas certain de la topologie de votre réseau, installez les agents sur des serveurs présentant des charges de travail critiques entre lesquels vous souhaitez analyser les performances réseau. Par exemple, si vous souhaitez analyser la connexion réseau entre un serveur web et un serveur exécutant SQL Server, installez un agent sur les deux serveurs. Les agents surveillent la connectivité (liens) réseau entre les hôtes et non les hôtes proprement dits. 

* **Moniteur de points de terminaison de service** : installez un agent Operations Management Suite sur chaque nœud à partir duquel vous souhaitez analyser la connectivité réseau vers le point de terminaison de service. Par exemple, si vous souhaitez analyser la connectivité réseau à Office 365 à partir de vos sites Office étiquetés O1, O2 et O3. Installez l’agent Operations Management Suite sur au moins un nœud dans O1, O2 et O3. 

* **Moniteur d’ExpressRoute**: installez au moins un agent Operations Management Suite dans votre réseau virtuel Azure. De plus, installez au moins un agent dans votre sous-réseau local, connecté via l’homologation privée ExpressRoute.  

### <a name="configure-operations-management-suite-agents-for-monitoring"></a>Configurer les agents Operations Management Suite pour l’analyse 

Network Performance Monitor utilise des transactions synthétiques pour analyser les performances réseau entre les agents sources et cibles. Vous pouvez choisir une analyse effectuée par le biais du protocole TCP et ICMP dans les fonctionnalités de l’Analyseur de performances et du Moniteur de points de terminaison de service. TCP est utilisé pour le Moniteur ExpressRoute. Vérifiez que le pare-feu autorise les communications entre les agents Operations Management Suite utilisés pour l’analyse sur le protocole que vous avez choisi. 

* **Protocole TCP** : si vous choisissez le protocole TCP pour l’analyse, ouvrez le port du pare-feu sur les agents utilisés pour Network Performance Monitor et le Moniteur ExpressRoute afin de vous assurer que les agents peuvent se connecter entre eux. Pour ouvrir le port, exécutez le script EnableRules.ps1 PowerShell sans paramètre dans une fenêtre PowerShell avec des privilèges d’administrateur.

    Le script crée des clés de Registre requises par la solution. Il crée également des règles de pare-feu Windows pour autoriser les agents à créer des connexions TCP entre eux. Les clés de Registre créées par le script spécifient également s’il faut enregistrer les journaux de débogage et le chemin d’accès des fichiers journaux. Le script définit également le port TCP de l’agent utilisé pour la communication. Les valeurs de ces clés sont définies automatiquement par le script. Ne modifiez pas manuellement ces clés. Le port ouvert par défaut est 8084. Vous pouvez utiliser un port personnalisé en ajoutant le paramètre portNumber au script. Utilisez le même port sur tous les ordinateurs exécutant le script. 

    >[!NOTE]
    > Le script configure uniquement le pare-feu Windows en local. Si vous avez un pare-feu réseau, assurez-vous qu’il autorise le trafic destiné au port TCP que Network Performance Monitor utilise.

    >[!NOTE]
    > Vous n’avez pas besoin d’exécuter le script PowerShell EnableRules.ps1 pour le Moniteur de points de terminaison de service.

    

* **Protocole ICMP** : si vous choisissez le protocole ICMP pour l’analyse, activez les règles de pare-feu suivantes afin d’utiliser le protocole ICMP de manière fiable :
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Configuration de la solution 

1. Ajoutez la solution Network Performance Monitor à votre espace de travail à partir de la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Vous pouvez également procéder de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la Galerie de solutions](log-analytics-add-solutions.md). 
2. Ouvrez votre espace de travail Log Analytics et cliquez sur la mosaïque **Vue d’ensemble**. 
3. Sélectionnez la mosaïque **Network Performance Monitor**  avec le message  *La solution nécessite une configuration supplémentaire*.

   ![Vignette Analyseur de performances réseau](media/log-analytics-network-performance-monitor/npm-config.png)

4. Sur la page **Installer**, l’option d’installation des agents Operations Management Suite et de configuration des agents pour l’analyse dans l’affichage **Paramètres communs** apparaît. Comme expliqué précédemment, si des agents Operations Management Suite sont installés et configurés, sélectionnez l’affichage **Installer** pour configurer la fonctionnalité que vous souhaitez utiliser. 

   **Analyseur de performances** : choisissez le protocole qui sera utilisé pour les transactions synthétiques dans la règle Analyseur de performances **Par défaut**, puis sélectionnez **Enregistrer et continuer**. Cette sélection de protocole ne conserve que la règle par défaut générée par le système. Vous devez choisir le protocole chaque fois que vous créez une règle de l’Analyseur de performances explicitement. Vous pouvez toujours passer aux paramètres de règle **Par défaut** dans l’onglet **Analyseur de performances** (qui apparaît une fois que vous avez terminé la configuration du jour 0) et modifier le protocole ultérieurement. Si vous n’êtes pas intéressé par la fonctionnalité de l’Analyseur de performances, vous pouvez désactiver la règle par défaut dans les paramètres de règle **Par défaut** de l’onglet **Analyseur de performances**.

   ![Affichage Analyseur de performances](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
   **Moniteur de points de terminaison de service** : cette fonctionnalité fournit des tests préconfigurés intégrés afin d’analyser la connectivité réseau à Office 365 et Dynamics 365 à partir de vos agents. Choisissez les services Office 365 et Dynamics 365 que vous souhaitez analyser en cochant les cases à côté d’eux. Pour sélectionnez les agents à partir desquels vous souhaitez effectuer une analyse, cliquez sur **Ajouter des agents**. Si vous ne souhaitez pas utiliser cette fonctionnalité ou la configurer ultérieurement, ne choisissez aucune option et sélectionnez **Enregistrer et continuer**.

   ![Affichage Moniteur de points de terminaison de service](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

   **Moniteur ExpressRoute** : sélectionnez **Détecter maintenant** pour détecter toutes les homologations privées ExpressRoute connectées aux réseaux virtuels dans l’abonnement Azure lié à cet espace de travail Azure Log Analytics. 

   >[!NOTE] 
   > La solution ne détecte pour le moment que les homologations privées ExpressRoute. 

   >[!NOTE] 
   > Seules ces homologations privées, connectées aux réseaux virtuels associés à l’abonnement lié à cet espace de travail Log Analytics, sont détectées. Si ExpressRoute est connecté aux réseaux virtuels en dehors de l’abonnement lié à cet espace de travail, créez un espace de travail Log Analytics dans ces abonnements. Utilisez Network Performance Monitor pour analyser ces homologations.

   ![Affichage Moniteur ExpressRoute](media/log-analytics-network-performance-monitor/npm-express-route.png)

   Une fois la détection terminée, les homologations privées détectées sont répertoriées dans une table. 

   ![Page Configuration de Network Performance Monitor](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
L’analyse de ces homologations est initialement à l’état désactivée. Sélectionnez chaque homologation que vous souhaitez analyser et configurez leur analyse à partir de l’affichage des détails sur la droite. Sélectionnez **Enregistrer** pour enregistrer la configuration. Pour en savoir plus, Consultez l’article « Configurer l’analyse ExpressRoute ». 

Une fois l’installation terminée, les données sont renseignées en 30 minutes à une heure. Pendant que la solution agrège les données à partir de votre réseau, le message *La solution nécessite une configuration supplémentaire* apparaît sur la mosaïque **Vue d’ensemble** de Network Performance Monitor. Une fois les données collectées et indexées, la mosaïque **Vue d’ensemble** change et vous informe de l’intégrité de votre réseau dans un résumé. Vous pouvez ensuite modifier l’analyse des nœuds sur lesquels les agents Operations Management Suite sont installés, ainsi que les sous-réseaux détectés à partir de votre environnement.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Modifier les paramètres de surveillance pour les sous-réseaux et les nœuds 

Tous les sous-réseaux pour lesquels au moins un agent a été installé sont répertoriés sous l’onglet  **Sous-réseaux** de la page de configuration. 


Pour activer ou désactiver l’analyse de sous-réseaux spécifiques :

1. cochez ou décochez la case à côté de  **ID de sous-réseau**. Puis, assurez-vous que la case **Utiliser pour l’analyse** est cochée ou décochée, le cas échéant. Vous pouvez activer ou désactiver plusieurs sous-réseaux. Les sous-réseaux désactivés ne sont pas analysés et les agents sont mis à jour pour arrêter l’envoi de commandes ping à d’autres agents. 
2. Choisissez les nœuds que vous souhaitez analyser dans un sous-réseau spécifique. Sélectionnez le sous-réseau dans la liste et déplacez les nœuds requis entre les listes qui contiennent des nœuds non analysés et analysés. Vous pouvez ajouter une description personnalisée au sous-réseau.
3. Sélectionnez **Enregistrer** pour enregistrer la configuration. 

#### <a name="choose-nodes-to-monitor"></a>Choisir les nœuds à surveiller

Tous les nœuds sur lesquels un agent est installé sont répertoriés sous l’onglet **Nœuds**. 

1. Activez ou désactivez les nœuds que vous souhaitez surveiller ou cesser de surveiller. 
2. Sélectionnez **Utiliser pour l’analyse** ou désactivez cette option selon le cas. 
3. Sélectionnez **Enregistrer**. 


Configurez les fonctionnalités que vous souhaitez :

- [Analyseur de performances](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [Moniteur de points de terminaison de service](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [Moniteur ExpressRoute](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Détails sur la collecte de données
Afin de collecter des informations sur la perte et la latence, Network Performance Monitor utilise les paquets du protocole de transfert TCP SYN-SYNACK-ACK lorsque vous sélectionnez TCP pour protocole. Network Performance Monitor utilise ICMP ECHO ICMP ECHO REPLY lorsque vous choisissez ICMP pour protocole. L’itinéraire du rapport des appels de procédure permet également d’obtenir des informations de topologie.

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur la manière dont l’Analyseur de performances réseau collecte des données.

| Plateforme | Agent direct | Agent System Center Operations Manager | Stockage Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Liaisons TCP/Messages ICMP ECHO toutes les 5 secondes, données envoyées toutes les 3 minutes |
 

 
La solution utilise des transactions synthétiques pour évaluer l’intégrité du réseau. Les agents Operations Management Suite installés à différents points du réseau échangent des paquets TCP ou un écho ICMP les uns avec les autres. Le fait que les agents utilisent des paquets TCP ou écho ICMP dépend du protocole sélectionné pour l’analyse. Au cours du processus, les agents détectent, le cas échéant, la durée des boucles et la perte de paquets. Périodiquement, chaque agent effectue également une détermination d’itinéraire d’autres agents afin de trouver tous les itinéraires à tester au sein du réseau. Ces données permettent aux agents de déduire les chiffres relatifs aux pertes de paquets et à la latence du réseau. Les tests sont répétés toutes les cinq secondes. Les données sont agrégées pendant environ trois minutes par les agents avant d’être téléchargées vers le service Log Analytics.



>[!NOTE]
> Bien que les agents communiquent fréquemment entre eux, ils ne génèrent pas beaucoup de trafic réseau lors des tests. Pour déterminer la perte et la latence, les agents s’appuient uniquement sur les paquets du protocole de transfert TCP SYN-SYNACK-ACK. Aucun paquet de données n’est échangé. Pendant ce processus, les agents communiquent entre eux uniquement si nécessaire. La topologie de communication de l’agent est optimisée pour réduire le trafic réseau.

## <a name="use-the-solution"></a>Utiliser la solution 

### <a name="network-performance-monitor-overview-tile"></a>Mosaïque Vue d’ensemble de Network Performance Monitor 

Une fois la solution Network Performance Monitor activée, la mosaïque de la solution sur la page **Vue d’ensemble** fournit une vue d’ensemble rapide de l’intégrité du réseau. 

 ![Mosaïque Vue d’ensemble de Network Performance Monitor](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Tableau de bord de l’Analyseur de performances réseau 

* **Principaux événements d’intégrité du réseau** : cette page fournit une liste des alertes et des événements d’intégrité les plus récents du système, ainsi que le temps qui s’est écoulé depuis l’activation de l’événement. Un événement ou une alerte d’intégrité apparaît chaque fois que la valeur de la métrique choisie (perte, latence, temps de réponse ou utilisation de la bande passante) pour la règle de surveillance dépasse le seuil fixé. 

* **Moniteur d’ExpressRoute** : cette page fournit des résumés d’intégrité pour plusieurs connexions d’homologation ExpressRoute analysées par la solution. La mosaïque **Topologie** indique le nombre de chemins d’accès réseau par le biais des circuits ExpressRoute analysés dans votre réseau. Sélectionnez cette mosaïque pour accéder à l’affichage **Topologie**.

* **Moniteur de point de terminaison de service** : cette page fournit des résumés de l’intégrité pour les différents tests que vous avez créé. La mosaïque **Topologie** indique le nombre de points de terminaison analysés. Sélectionnez cette mosaïque pour accéder à l’affichage **Topologie**.

* **Analyseur de performances** : cette page fournit des résumés d’intégrité pour les liens **Réseau** et les liens **Sous-réseau** analysés par la solution. La mosaïque **Topologie** indique le nombre de chemins d’accès réseau analysés dans votre réseau. Sélectionnez cette mosaïque pour accéder à l’affichage **Topologie**. 

* **Requêtes courantes** : cette page contient une série de requêtes de recherche qui permettent d’extraire directement des données d’analyse du réseau brutes. Vous pouvez utiliser ces requêtes comme point de départ pour créer vos propres requêtes de génération de rapports personnalisés. 

   ![Tableau de bord de l’Analyseur de performances réseau](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Explorer en profondeur 

Vous pouvez sélectionner différents liens du tableau de bord de la solution pour explorer de façon plus approfondie un aspect spécifique. Par exemple, lorsque vous voyez une alerte ou un lien réseau défectueux s’afficher sur le tableau de bord, sélectionnez le pour en savoir plus. Une page répertorie tous les liens de sous-réseau pour un lien réseau en particulier. Vous pouvez voir l’état de la perte, de la latence et d’intégrité de chaque lien de sous-réseau. Vous pouvez déterminer rapidement quel lien de sous-réseau pose problème. Sélectionnez **Afficher les liens de nœud** pour afficher tous les liens de nœud concernant le lien de sous-réseau défectueux. Ensuite, vous pouvez voir les liens de nœud à nœud, et identifier les liens de nœud défectueux. 

Sélectionnez  **Afficher la topologie** pour afficher la topologie tronçon par tronçon des itinéraires entre les nœuds sources et de destination. Les itinéraires défectueux apparaissent en rouge. Vous pouvez afficher la latence apportée par chaque tronçon afin d’identifier rapidement le problème affectant une portion spécifique du réseau.

 

### <a name="network-state-recorder-control"></a>Contrôle de l’Enregistreur de l’état du réseau

Chaque vue affiche une capture instantanée de l’intégrité de votre réseau à un moment précis dans le temps. Par défaut, c’est l’état le plus récent qui s’affiche. La barre située en haut de la page affiche le point dans le temps pour lequel l’état est affiché. Pour afficher une capture instantanée de l’intégrité de votre réseau à un moment précédent, sélectionnez **Actions**. Vous pouvez également activer ou désactiver l’actualisation automatique pour n’importe quelle page pendant que vous consultez le dernier état. 

 ![Enregistreur de l’état du réseau](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Graphiques de tendances 

À chaque niveau exploré, vous pouvez consulter la tendance de la métrique applicable. Il peut s’agir de la perte, de la latence, du temps de réponse ou de l’utilisation de la bande passante. Pour modifier l’intervalle de temps de cette tendance, utilisez le contrôle de l’heure situé en haut du graphique. 

Les graphiques de tendances présentent une perspective historique des performances d’une métrique de performances. Certains problèmes réseau sont temporaires par nature et compliqués à intercepter en consultant simplement l’état actuel du réseau. Des problèmes peuvent apparaître rapidement et disparaître avant que quiconque s’en aperçoive, avant de réapparaître plus tard.
 Ces problèmes temporaires peuvent également être difficiles pour les administrateurs de l’application. Les problèmes apparaissent souvent sous forme d’augmentation inexpliquée du temps de réponse de l’application, même lorsque tous les composants de l’application semblent fonctionner correctement. 

Vous pouvez facilement détecter ces types de problèmes en examinant un graphique de tendances. Le problème apparaît comme un pic soudain de latence du réseau ou de perte de paquets. Vous pouvez étudier le problème à l’aide du contrôle de l’Enregistreur de l’état du réseau pour afficher la capture instantanée et la topologie réseau de ce point dans le temps où le problème s’est produit.

 
![Graphiques de tendances](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mappage de la topologie 

Network Performance Monitor affiche la topologie tronçon par tronçon des itinéraires entre le point de terminaison source et le point de terminaison de destination sur une carte topologique interactive. Pour afficher la carte topologique, sélectionnez la mosaïque **Topologie** du tableau de bord de la solution. Vous pouvez également sélectionner le lien **Afficher la topologie** sur les pages explorées. 

La carte topologique affiche le nombre d’itinéraires existant entre la source et la destination, ainsi que les chemins d’accès qu’empruntent les paquets de données. La latence apportée par chaque tronçon réseau est également visible. Tous les chemins d’accès pour lesquels la latence de chemin d’accès totale est supérieure au seuil (défini dans la règle de surveillance correspondante) sont affichés en rouge. 

Lorsque vous sélectionnez un nœud ou pointez dessus sur la carte topologique, vous pouvez voir les propriétés du nœud, notamment FQDN et l’adresse IP. Sélectionnez un tronçon pour afficher son adresse IP. Vous pouvez identifier le tronçon réseau défectueux en notant la latence apportée par ce dernier. Pour filtrer des itinéraires particuliers, utilisez des filtres du volet Action réductible. Pour simplifier les topologies réseau, masquez les tronçons intermédiaires à l’aide du curseur du volet Action. Vous pouvez effectuer un zoom avant ou arrière sur la carte topologique à l’aide de la roulette de la souris. 

La topologie affichée sur la carte est celle du calque 3 et elle ne contient pas les connexions et appareils du calque 2. 

 
![Mappage de la topologie](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Recherche Log Analytics 

Toutes les données présentées sous forme graphique via le tableau de bord Network Performance Monitor et les pages d’exploration sont également disponibles en mode natif dans une [recherche Log Analytics](log-analytics-log-search-new.md). Vous pouvez effectuer une analyse interactive des données dans le référentiel et mettre en corrélation les données de différentes sources. Vous pouvez également créer des affichages et des alertes personnalisées et exporter les données vers Excel, Power BI ou un lien partageable. La zone  **Requêtes courantes** du tableau de bord comprend des requêtes utiles qui peuvent vous servir de point de départ pour créer vos propres requêtes et rapports. 

 

## <a name="provide-feedback"></a>Fournir des commentaires 

* **UserVoice :** vous pouvez publier vos idées concernant les fonctionnalités de Network Performance Monitor que vous aimeriez voir améliorer. Visitez la [page UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Rejoignez notre cohorte :** nous sommes toujours ravis d’accueillir de nouveaux clients dans notre cohorte. Dans ce cadre, vous pouvez accéder en avant-première aux nouvelles fonctionnalités et avoir l’opportunité de nous aider à améliorer Network Performance Monitor. Si vous souhaitez y participer, répondez à cette  [enquête rapide](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Étapes suivantes 
En savoir plus sur [Analyseur de performances](log-analytics-network-performance-monitor-performance-monitor.md), [Moniteur de points de terminaison de service](log-analytics-network-performance-monitor-performance-monitor.md) et [Moniteur ExpressRoute](log-analytics-network-performance-monitor-expressroute.md). 
