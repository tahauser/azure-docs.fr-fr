---
title: "Solution Analyseur de performances réseau dans Azure Log Analytics | Microsoft Docs"
description: "L’Analyseur de performances réseau d’Azure Log Analytics vous aide à surveiller les performances de vos réseaux en quasi temps réel afin de détecter et de localiser d’éventuels goulots d’étranglement affectant les performances réseau."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: banders
ms.openlocfilehash: d5d5ec1b524fa455c8d2231c7c16fd7942f713c4
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="network-performance-monitor-solution-in-log-analytics"></a>Solution Analyseur de performances réseau dans Log Analytics

![Symbole de Network Performance Monitor](./media/log-analytics-network-performance-monitor/npm-symbol.png)

Ce document décrit l’installation et l’utilisation de la solution Analyseur de performances réseau de Log Analytics, qui vous aide à surveiller les performances de vos réseaux en quasi temps réel afin de détecter et de localiser d’éventuels goulots d’étranglement affectant les performances réseau. La solution Analyseur de performances réseau vous permet de surveiller la perte et la latence entre deux réseaux, sous-réseaux ou serveurs. L’Analyseur de performances réseau détecte divers problèmes réseau, tels que des pertes de trafic, des erreurs de routage et d’autres problèmes que les méthodes de surveillance réseau classiques ne sont pas en mesure de détecter. L’Analyseur de performances réseau génère des alertes et des notifications en cas de dépassement d’un seuil pour une liaison réseau. Le système peut apprendre ces seuils automatiquement, et vous pouvez également les configurer pour utiliser des règles d’alerte personnalisées. L’Analyseur de performances réseau détecte en temps opportun les problèmes de performances réseau, et en localise la source en identifiant un segment ou un appareil réseau particuliers.

Vous pouvez détecter les problèmes de réseau avec le tableau de bord de la solution qui affiche des informations récapitulatives sur le réseau, dont des événements récents affectant l’intégrité réseau, des liaisons réseau défectueuses, et des liens de sous-réseau confrontés à une latence et a des pertes de paquets élevées. Vous pouvez examiner de près une liaison de réseau pour voir l’état d’intégrité actuel des liens de sous-réseau ainsi que des liens de nœud à nœud. Vous pouvez également afficher la tendance historique de perte et de latence au niveau du réseau et des sous-réseaux, ou de nœud à nœud. Vous pouvez détecter des problèmes réseau temporaires en affichant des graphiques de tendance historique de pertes de paquets et de latence, et localiser les goulots d’étranglement du réseau sur une carte topologique. Le graphique topologique interactif permet de visualiser les itinéraires réseau tronçon par tronçon, et de déterminer la source du problème. Comme dans d’autres solutions, vous pouvez utiliser la fonction de recherche de journal à diverses fins d’analyse, pour créer des rapports personnalisés basés sur les données collectées par l’Analyseur de performances réseau.

La solution utilise des transactions synthétiques en tant que mécanisme principal pour détecter des erreurs réseau. Par conséquent, vous pouvez l’utiliser sans vous soucier du fournisseur ou du modèle d’un appareil réseau spécifique. Elle opère indifféremment dans des environnements locaux, cloud (IaaS) et hybrides. La solution détecte automatiquement la topologie du réseau et les divers itinéraires au sein de celui-ci.

Les produits de surveillance réseau classiques se concentrent sur la surveillance de l’intégrité des appareils réseau (routeurs, commutateurs, etc.), mais ne fournissent pas d’informations sur la qualité réelle de la connectivité réseau entre deux points, contrairement à l’Analyseur de performances réseau.

### <a name="using-the-solution-standalone"></a>Utilisation de la solution en mode autonome
Si vous souhaitez surveiller la qualité des connexions réseau entre charges de travail critiques, réseaux, centres de données ou sites de bureau, vous pouvez utiliser la solution Analyseur de performances réseau en mode autonome pour surveiller l’intégrité de la connectivité entre les éléments suivants :

* plusieurs centres de données ou sites de bureau connectés via un réseau public ou privé ;
* charges de travail critiques exécutant des applications métier ;
* services cloud publics, tels que Microsoft Azure ou Amazon Web Services (AWS), et réseaux locaux, si vous disposez d’IaaS (machine virtuelle) et de passerelles configurées pour permettre la communication entre réseaux locaux et réseaux cloud ;
* réseaux Azure et locaux lorsque vous utilisez ExpressRoute.

### <a name="using-the-solution-with-other-networking-tools"></a>Utilisation de la solution avec d’autres outils de mise en réseau
Si vous souhaitez surveiller une application métier, vous pouvez utiliser la solution Analyseur de performances réseau en complément d’autres outils réseau. Un réseau lent peut ralentir l’exécution des applications, et l’Analyseur de performances réseau peut vous aider à étudier les problèmes de performances d’application dus à des problèmes de réseau sous-jacents. Étant donné que la solution ne requiert pas d’accès aux appareils réseau, l’administrateur de l’application ne dépend pas d’une équipe réseau pour lui fournir des informations sur la façon dont le réseau affecte les applications.

Par ailleurs, si vous investissez déjà dans d’autres outils d’analyse de réseau, la solution peut compléter ceux-ci, car les solutions traditionnelles d’analyse réseau ne fournissent pas d’informations en rapport avec des mesures de performances réseau de bout en bout, telles que les pertes et la latence.  La solution Analyseur de performances réseau peut aider à combler cette lacune.

## <a name="installing-and-configuring-agents-for-the-solution"></a>Installation et configuration des agents pour la solution
Utilisez les procédures de base d’installation des agents décrites dans [Connecter des ordinateurs Windows à Log Analytics](log-analytics-windows-agent.md) et [Connexion d’Operations Manager à Log Analytics](log-analytics-om-agents.md).

> [!NOTE]
> Vous devez installer au moins 2 agents afin de disposer de suffisamment de données pour détecter et analyser vos ressources réseau. Autrement, la solution reste en état de configuration jusqu’à ce que vous installiez et configuriez des agents supplémentaires.
>
>

### <a name="where-to-install-the-agents"></a>Où installer les agents
Avant d’installer des agents, réfléchissez à la topologie de votre réseau et aux parties de celui-ci que vous souhaitez analyser. Nous vous recommandons d’installer plusieurs agents pour chaque sous-réseau à surveiller. En d’autres termes, pour chaque sous-réseau à analyser, choisissez au moins deux serveurs ou machines virtuelles sur lesquels installer l’agent.

Si vous n’êtes pas certain de la topologie de votre réseau, installez les agents sur des serveurs présentant des charges de travail critiques là où vous souhaitez analyser les performances du réseau. Par exemple, il se peut que vous vouliez suivre une connexion réseau entre un serveur web et un serveur exécutant SQL Server. Dans ce cas, vous devez installer un agent sur les deux serveurs.

Les agents surveillent la connectivité (les liaisons) réseau entre les hôtes, pas les hôtes proprement dits. Par conséquent, pour surveiller une liaison réseau, vous devez installer des agents sur les deux points de terminaison de celle-ci.

### <a name="configure-agents"></a>Configurer les agents

Si vous avez l’intention d’utiliser le protocole ICMP pour les transactions synthétiques, vous devez activer les règles de pare-feu suivantes pour pouvoir utiliser ICMP de manière fiable :

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow
```


Si vous envisagez d’utiliser le protocole TCP, vous devez ouvrir des ports de pare-feu pour ces ordinateurs afin de veiller à ce que les agents puissent communiquer. Vous devez télécharger et exécuter le [script PowerShell EnableRules.ps1](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) sans paramètre dans une fenêtre PowerShell avec des privilèges d’administrateur.

Le script crée les clés de Registre requises par l’Analyseur de performances réseau, ainsi que des règles de pare-feu Windows pour autoriser les agents à créer des connexions TCP entre eux. Les clés de Registre créées par le script spécifient également s’il faut enregistrer les journaux de débogage et le chemin d’accès des fichiers journaux. Le script définit également le port TCP de l’agent utilisé pour la communication. Les valeurs de ces clés étant définies automatiquement par le script, vous n’avez pas à les modifier manuellement.

Le port ouvert par défaut est 8084. Vous pouvez utiliser un port personnalisé en ajoutant le paramètre `portNumber` au script. Toutefois, le même port doit être utilisé sur tous les ordinateurs exécutant le script.

> [!NOTE]
> Le script EnableRules.ps1 configure des règles de pare-feu Windows uniquement sur l’ordinateur exécutant le script. Si vous avez un pare-feu réseau, vous devez vous assurer qu’il autorise le trafic destiné au port TCP que l’Analyseur de performances réseau utilise.
>
>

## <a name="configuring-the-solution"></a>Configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

1. La solution Analyseur de performances réseau acquiert des données à partir d’ordinateurs exécutant Windows Server 2008 SP 1 ou version ultérieure, ou Windows 7 SP1 ou version ultérieure, qui répondent aux mêmes exigences que Microsoft Monitoring Agent (MMA). Des agents de la solution Analyseur de performances réseau peuvent également s’exécuter sur les systèmes d’exploitation clients/de bureau Windows (Windows 10, Windows 8.1, Windows 8 et Windows 7).
    >[!NOTE]
    >Les agents des systèmes d’exploitation serveur Windows prennent en charge les protocoles de transaction synthétique TCP et ICMP. Les agents des systèmes d’exploitation client Windows prennent en charge uniquement le protocole de transaction synthétique ICMP.

2. Ajoutez la solution Analyseur de performances réseau à votre espace de travail depuis la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) ou en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).<br><br> ![Symbole de l’Analyseur de performances réseau](./media/log-analytics-network-performance-monitor/npm-symbol.png)  
3. Dans le portail OMS, vous voyez une nouvelle vignette libellée **Analyseur de performances réseau**, avec le message *La solution nécessite une configuration supplémentaire*. Cliquez sur la vignette pour accéder à l’onglet **Déploiement** et sélectionnez le protocole à utiliser pour effectuer les transactions synthétiques pour la surveillance de votre réseau.  Consultez [Choisir le bon protocole : ICMP ou TCP](#choose-the-right-protocol-icmp-or-tcp) pour vous aider à choisir le protocole adapté à votre réseau.<br><br> ![La solution nécessite de choisir le protocole](media/log-analytics-network-performance-monitor/log-analytics-netmon-perf-welcome.png)<br><br>

4. Après avoir choisi le protocole, vous êtes redirigé vers la page **Vue d’ensemble de la solution OMS**. Pendant que la solution agrège les données à partir de votre réseau, la vignette de vue d’ensemble de Network Performance Monitor affiche le message *Agrégation des données en cours*.<br><br> ![La solution agrège les données](media/log-analytics-network-performance-monitor/log-analytics-netmon-tile-status-01.png)<br><br>
5. Une fois les données collectées et indexées, la vignette de vue d’ensemble change et vous indique que vous devez effectuer une configuration supplémentaire.<br><br> ![La vignette de la solution nécessite une configuration supplémentaire](media/log-analytics-network-performance-monitor/log-analytics-netmon-tile-status-02.png)<br><br>
6. Cliquez sur la vignette et démarrez la configuration de la solution en suivant les étapes ci-dessous.

### <a name="create-new-networks"></a>Créer des réseaux
Dans Network Performance Monitor, un réseau est un conteneur logique de sous-réseaux. Vous pouvez créer un réseau avec un nom convivial et lui ajouter des sous-réseaux selon votre logique métier. Par exemple, vous pouvez créer un réseau nommé *London* et ajouter tous les sous-réseaux de votre centre de données de Londres, ou un réseau nommé *ContosoFrontEnd* et ajouter tous les sous-réseaux alimentant le serveur frontal de votre application nommée Contoso sur ce réseau.
Dans la page de configuration, vous voyez un réseau nommé **Par défaut** dans l’onglet Réseaux. Si vous n’avez pas créé de réseau, tous les sous-réseaux détectés automatiquement sont placés dans le réseau Par défaut.
Chaque fois que vous créez un réseau, vous y ajoutez un sous-réseau qui est retiré du réseau Par défaut. Si vous supprimez un réseau, toutes ses sous-réseaux sont automatiquement restitués au réseau Par défaut.
En d’autres termes, le réseau Par défaut joue le rôle de conteneur pour tous les sous-réseaux non contenus dans un réseau défini par un utilisateur. Vous ne pouvez pas modifier ou supprimer le réseau Par défaut. Il reste toujours dans le système. En revanche, vous pouvez créer autant de réseaux personnalisés que nécessaire.
Dans la plupart des cas, les sous-réseaux de votre organisation sont organisés en plusieurs réseaux, et vous devez créer un ou plusieurs réseaux pour regrouper vos sous-réseaux selon votre logique métier.

#### <a name="to-create-a-new-network"></a>Pour créer un réseau
1. Cliquez sur **Ajouter un réseau**, puis tapez le nom et la description du réseau.
2. Sélectionnez un ou plusieurs sous-réseaux, puis cliquez sur **Ajouter**.
3. Pour enregistrer la configuration, cliquez sur **Enregistrer**.<br><br> ![Ajouter un réseau](./media/log-analytics-network-performance-monitor/npm-add-network.png)

### <a name="wait-for-data-aggregation"></a>Attendre l’agrégation de données
Après que vous avez enregistré la configuration pour la première fois, la solution commence à collecter des informations sur les pertes de paquets et la latence du réseau entre les nœuds où des agents sont installés. Ce processus peut prendre du temps, parfois plus de 30 minutes. Dans cet état, la vignette Analyseur de performances réseau dans la page d’aperçu affiche le message *Agrégation des données en cours*.

![Agrégation des données en cours](./media/log-analytics-network-performance-monitor/npm-aggregation.png)

Une fois les données chargées, la vignette Analyseur de performances réseau mise à jour affiche des données.

![Vignette Analyseur de performances réseau](./media/log-analytics-network-performance-monitor/npm-tile.png)

Cliquez sur la vignette pour afficher le tableau de bord de l’Analyseur de performances réseau.

![Tableau de bord de l’Analyseur de performances réseau](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="edit-monitoring-settings-for-subnets"></a>Modifier les paramètres d’analyse pour les sous-réseaux
Tous les sous-réseaux où au moins un agent a été installé sont répertoriés sous l’onglet **Sous-réseaux** dans la page de configuration.

#### <a name="to-enable-or-disable-monitoring-for-particular-subnetworks"></a>Pour activer ou désactiver l’analyse de sous-réseaux particuliers
1. Activez ou désactivez la case en regard de **ID de sous-réseau**, puis assurez-vous que l’option **Utiliser pour la surveillance** est activée ou désactivée de façon appropriée. Vous pouvez activer ou désactiver plusieurs sous-réseaux. Les sous-réseaux désactivés ne sont pas analysés, car les agents seront mis à jour pour arrêter l’envoi de commandes ping à d’autres agents.
2. Choisissez les nœuds que vous souhaitez surveiller pour un sous-réseau particulier en sélectionnant celui-ci dans la liste, puis en déplaçant les nœuds requis entre les listes de nœuds non analysés et analysés.
   Vous pouvez ajouter une **description** personnalisée au sous-réseau si vous le souhaitez.
3. Pour enregistrer la configuration, cliquez sur **Enregistrer**.<br><br> ![Modifier un sous-réseau](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>Choisir les nœuds à surveiller
Tous les nœuds sur lesquels un agent est installé sont répertoriés sous l’onglet **Nœuds**.

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>Pour activer ou désactiver l’analyse de nœuds
1. Activez ou désactivez les nœuds que vous souhaitez surveiller ou cesser de surveiller.
2. Cliquez sur **Utiliser pour la surveillance** ou désactivez cette option selon le cas.
3. Cliquez sur **Enregistrer**.<br><br> ![Activer l’analyse du nœud](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)

### <a name="set-monitoring-rules"></a>Définir les règles d’analyse
Network Performance Monitor génère des événements d’intégrité lorsque le seuil des performances des connexions réseau entre 2 sous-réseaux ou 2 réseaux est dépassé. Le système peut apprendre ces seuils automatiquement, et vous pouvez également indiquer des seuils personnalisés.
Le système crée automatiquement une règle par défaut. Celle-ci crée un événement d’intégrité chaque fois qu’une perte ou une latence entre une paire quelconque de liaisons réseau/sous-réseau dépasse le seuil appris par le système. Cela permet à la solution de surveiller votre infrastructure réseau tant que vous n’avez pas créé explicitement de règles de surveillance. Si la règle par défaut est activée, tous les nœuds assurent la surveillance en envoyant des transactions synthétiques à tous les autres nœuds que vous avez activés. La règle par défaut est utile sur les réseaux de petite taille, par exemple, si vous avez un petit nombre de serveurs qui exécutent un microservice et que vous souhaitez vous assurer que tous les serveurs sont connectés les uns aux autres.

>[!NOTE]
>Il est recommandé de désactiver la règle par défaut et de créer des règles de surveillance personnalisées, en particulier pour les réseaux importants dans lesquels vous utilisez un grand nombre de nœuds pour la surveillance. Cela limitera le trafic généré par la solution et vous aidera à organiser la surveillance de votre réseau.

Créez des règles de surveillance personnalisées selon votre logique métier. Par exemple, si vous souhaitez surveiller les performances de la connectivité réseau de 2 bureaux au siège social, regroupez tous les sous-réseaux du bureau 1 dans le réseau O1, tous les sous-réseaux du bureau 2 dans le réseau O2 et tous les sous-réseaux du siège social dans le réseau H. Créez deux règles de surveillance : une entre O1 et H et l’autre entre O2 et H.


#### <a name="to-create-custom-monitoring-rules"></a>Pour créer des règles d’analyse personnalisées
1. Cliquez sur **Ajouter une règle** sous l’onglet **Surveiller**, puis entrez le nom et la description de la règle.
2. Sélectionnez la paire de liaisons réseau ou sous-réseau à surveiller dans les listes.
3. Commencez par sélectionner le réseau contenant le(s) premier(s) sous-réseau(x) d’intérêt dans la liste déroulante de réseau, puis sélectionnez le(s) sous-réseau(x) dans la liste déroulante de sous-réseaux correspondante.
   Sélectionnez **Tous les sous-réseaux** si vous souhaitez analyser tous les sous-réseaux dans une liaison réseau. Sélectionnez de la même manière les autres sous-réseaux d’intérêt. Vous pouvez également cliquer sur **Ajouter une Exception** pour exclure l’analyse de liens de sous-réseau spécifiques de la sélection que vous avez faite.
4. [Choisissez entre le protocole ICMP et le protocole TCP](#choose-the-right-protocol-icmp-or-tcp) pour l’exécution des transactions synthétiques.
5. Si vous ne souhaitez pas créer d’événements d’intégrité pour les éléments que vous avez sélectionnés, désactivez l’option **Activer la surveillance de l’intégrité sur les liens couverts par cette règle**.
6. Choisissez les conditions d’analyse.
   Vous pouvez définir des seuils personnalisés pour la génération d’événements d’intégrité en tapant des valeurs de seuil. Chaque fois que la valeur d’une condition dépasse son seuil sélectionné pour la paire de réseaux/sous-réseaux sélectionnée, un événement d’intégrité est généré.
7. Pour enregistrer la configuration, cliquez sur **Enregistrer**.<br><br> ![Créer un règle d’analyse personnalisée](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)

Vous pouvez intégrer la règle d’analyse que vous avez enregistrée à Gestion des alertes en cliquant sur **Créer une alerte**. Une règle d’alerte est créée automatiquement avec la requête de recherche et d’autres paramètres requis renseignés automatiquement. En utilisant une règle d’alerte, vous pouvez recevoir des alertes par e-mail en plus des alertes existant dans la solution Analyseur de performances réseau. Les alertes peuvent également déclencher des actions correctives avec les runbooks ou elles peuvent s’intégrer aux solutions existantes de gestion des services à l’aide des webhooks. Vous pouvez cliquer sur **Manage Alert (Gérer une alerte)** pour modifier les paramètres d’alerte.

### <a name="choose-the-right-protocol-icmp-or-tcp"></a>Choisir le bon protocole : ICMP ou TCP

Le Moniteur de performances réseau utilise des transactions synthétiques pour calculer les mesures de performances réseau comme la perte de paquets et la latence des liaisons. Pour mieux comprendre, prenez un agent NPM connecté à une extrémité d’une liaison réseau. Cet agent NPM envoie des paquets de sonde à un second agent NPM connecté à une autre extrémité du réseau. Le second agent répond avec des paquets de réponse. Ce processus est répété plusieurs fois. En mesurant le nombre de réponses et le temps nécessaire pour recevoir chaque réponse, le premier agent NPM évalue la latence des liaisons et les rejets de paquet.

Le format, la taille et la séquence de ces paquets sont déterminés par le protocole choisi au moment de la création des règles de surveillance. En fonction du protocole des paquets, les appareils réseau intermédiaires (routeurs, commutateurs, etc.) peuvent traiter ces paquets différemment. Le protocole que vous choisissez a donc une incidence sur la précision des résultats. Il détermine également si des étapes manuelles sont nécessaires après le déploiement de la solution NPM.

Pour l’exécution des transactions synthétiques, NPM vous offre le choix entre le protocole ICMP et le protocole TCP.
Si vous choisissez ICMP quand vous créez une règle de transaction synthétique, les agents NPM utilisent des messages ICMP ECHO pour calculer la latence du réseau et la perte de paquets. ICMP ECHO utilise le même message que celui envoyé par l’utilitaire Ping traditionnel. Quand vous utilisez le protocole TCP, les agents NPM envoient un paquet TCP SYN sur le réseau. Ceci est suivi par l’établissement d’une liaison TCP et la suppression de la connexion à l’aide de paquets RST.

#### <a name="points-to-consider-before-choosing-the-protocol"></a>Éléments à prendre en considération dans le choix du protocole
Avant de choisir un protocole, tenez compte des informations suivantes :

##### <a name="discovering-multiple-network-routes"></a>Détection de plusieurs routages réseau
TCP est plus précis dans le cadre de la détection de plusieurs routages et nécessite moins d’agents dans chaque sous-réseau. Par exemple, un ou deux agents utilisant TCP peuvent détecter tous les chemins redondants entre les sous-réseaux. En revanche, plusieurs agents utilisant ICMP sont nécessaires pour obtenir des résultats similaires. Avec ICMP, si vous avez *N* routages entre deux sous-réseaux, il vous faut plus de 5*N* agents dans un sous-réseau source ou de destination.

##### <a name="accuracy-of-results"></a>Précision des résultats
Les routeurs et les commutateurs ont tendance à accorder aux paquets ICMP ECHO une priorité inférieure à celle des paquets TCP. Dans certaines situations, quand les appareils réseau sont lourdement chargés, les données obtenues par TCP reflètent plus fidèlement la perte et la latence constatées par les applications. Cela vient du fait que la plupart du trafic des applications passe par TCP. Dans de tels cas, ICMP offre de moins bons résultats que TCP.

##### <a name="firewall-configuration"></a>Configuration du pare-feu
Le protocole TCP nécessite l’envoi des paquets TCP à un port de destination. Le port par défaut utilisé par les agents NPM est le port 8084. Vous pouvez toutefois le modifier au moment de la configuration des agents. Vous devez donc vérifier que vos pare-feu réseau ou règles NSG (dans Azure) autorisent le trafic sur le port. Vous devez également veiller à ce que le pare-feu local sur les ordinateurs où les agents sont installés est configuré pour autoriser le trafic sur ce port.

Vous pouvez utiliser des scripts PowerShell pour configurer des règles de pare-feu sur les ordinateurs Windows, mais vous devez configurer manuellement votre pare-feu réseau.

Contrairement à TCP, le protocole ICMP n’utilise pas de port. Dans la plupart des scénarios d’entreprise, le trafic ICMP est autorisé à franchir les pare-feu pour vous permettre d’utiliser des outils de diagnostic réseau comme l’utilitaire Ping. Si un test Ping effectué entre deux ordinateurs aboutit, vous pouvez utiliser le protocole ICMP sans avoir à configurer les pare-feu manuellement.

> [!NOTE]
> Certains pare-feu peuvent bloquer le protocole ICMP, ce qui peut entraîner la création d’un grand nombre d’événements dans votre système de gestion des événements et des informations de sécurité lors de la retransmission. Assurez-vous que le protocole que vous choisissez n’est pas bloqué par un pare-feu réseau/NSG. Si c’est le cas, NPM ne sera pas en mesure de surveiller le segment de réseau.  De ce fait, nous vous recommandons d’utiliser le protocole TCP pour la surveillance. Vous devez utiliser le protocole ICMP dans les cas où vous ne pouvez pas utiliser le protocole TCP, par exemple lorsque :
> * Vous utilisez des nœuds basés sur un client Windows, étant donné que les sockets bruts du protocole TCP ne sont pas autorisés dans le client Windows
> * Votre pare-feu réseau/NSG bloque le protocole TCP


#### <a name="how-to-switch-the-protocol"></a>Comment changer de protocole

Si vous avez choisi ICMP durant le déploiement, vous pouvez basculer vers TCP à tout moment en modifiant la règle de surveillance par défaut.

##### <a name="to-edit-the-default-monitoring-rule"></a>Pour modifier la règle de surveillance par défaut
1.  Accédez à **Performances réseau** > **Moniteur** > **Configurer** > **Moniteur**, puis cliquez sur **Règle par défaut**.
2.  Faites défiler la page jusqu’à la section **Protocole** et sélectionnez le protocole à utiliser.
3.  Cliquez sur **Enregistrer** pour appliquer le paramètre.

Même si la règle par défaut utilise un protocole spécifique, vous pouvez créer des règles avec un autre protocole. Vous pouvez même créer une combinaison de règles : certaines règles utilisant ICMP et d’autres TCP.


## <a name="data-collection-details"></a>Détails sur la collecte de données
L’Analyseur de performances réseau utilise des paquets de transfert TCP SYN-SYNACK-ACK si le protocole TCP est choisi, et ICMP ECHO ICMP ECHO REPLY si ICMP est choisi comme protocole pour collecter les informations de latence et de perte. La détermination d’itinéraire permet également d’obtenir des informations de topologie.

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur la manière dont l’Analyseur de performances réseau collecte des données.

| plateforme | Agent direct | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Liaisons TCP/Messages ICMP ECHO toutes les 5 secondes, données envoyées toutes les 3 minutes |

La solution utilise des transactions synthétiques pour évaluer l’intégrité du réseau. Les agents OMS installés à différents points du réseau échangent des paquets TCP ou un écho ICMP (en fonction du protocole sélectionné pour l’analyse) les uns avec les autres. Au cours du processus, les agents détectent, le cas échéant, la durée des boucles et la perte de paquets. Périodiquement, chaque agent effectue également une détermination d’itinéraire d’autres agents afin de trouver tous les itinéraires à tester au sein du réseau. Ces données permettent aux agents de déduire les chiffres relatifs aux pertes de paquets et à la latence du réseau. Les tests sont répétés toutes les cinq secondes, et les données sont agrégées pendant une période de trois minutes par les agents avant leur chargement vers le service Log Analytics.

> [!NOTE]
> Bien que les agents communiquent fréquemment entre eux, ils ne génèrent pas beaucoup de trafic réseau lors des tests. Pour déterminer les pertes et la latence, les agents s’appuient uniquement sur les paquets d’établissements de liaisons TCP SYN-SYNACK-ACK. Aucun paquet de données n’est échangé. Durant ce processus, les agents communiquent entre eux uniquement si nécessaire, et la topologie de communication des agents est optimisée pour réduire le trafic réseau.
>
>

## <a name="using-the-solution"></a>Utilisation de la solution
Cette section explique toutes les fonctions du tableau de bord et comment les utiliser.

### <a name="solution-overview-tile"></a>Vignette Vue d’ensemble de la solution
Une fois la solution Analyseur de performances réseau activée, la vignette de la solution sur la page d’aperçu d’OMS fournit une vue d’ensemble rapide de l’intégrité du réseau. Elle affiche un graphique en anneau indiquant le nombre de liens de sous-réseau intègres et non intègres. Lorsque vous cliquez sur la vignette, le tableau de bord de la solution s’ouvre.

![Vignette Analyseur de performances réseau](./media/log-analytics-network-performance-monitor/npm-tile.png)

### <a name="network-performance-monitor-solution-dashboard"></a>Tableau de bord de la solution l’Analyseur de performances réseau
Le panneau **Résumé de réseau** affiche un récapitulatif des réseaux, avec leurs tailles relatives. Il est suivi de vignettes affichant le nombre total de liaisons réseau, de liens de sous-réseau et de chemins d’accès dans le système (un chemin d’accès est constitué des adresses IP des deux ordinateurs hôtes avec agents, et de tous les tronçons les reliant entre eux).

Le panneau **Principaux événements d’intégrité du réseau** fournit la liste des alertes et événements d’intégrité les plus récents du système, ainsi que le temps depuis lequel l’événement est actif. Une alerte ou un événement d’intégrité sont générés chaque fois que les pertes de paquets ou la latence d’une liaison réseau ou de sous-réseau dépasse un seuil.

Le panneau **Principaux liens réseau défectueux** affiche la liste des liens réseau non intègres. Il s’agit des liaisons réseau actuellement confrontées à un ou plusieurs événements d’intégrité indésirables.

Les panneaux **Liens de sous-réseau avec la perte la plus élevée** et **Liens de sous-réseau avec la latence la plus élevée** affichent les liens de sous-réseau les plus importants en relation respectivement avec la perte de paquets et la latence. Une latence élevée ou des pertes de paquets pourraient être attendues sur certaines liaisons réseau. Celles-ci figurent dans les listes des dix liaisons principales, mais ne sont pas marquées comme défectueuses.

Le panneau **Requêtes courantes** contient une série de requêtes de recherche qui permettent d’extraire directement des données d’analyse de réseau brutes. Vous pouvez utiliser ces requêtes comme point de départ pour créer vos propres requêtes de génération de rapports personnalisés.

![Tableau de bord de l’Analyseur de performances réseau](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="drill-down-for-depth"></a>Explorer en profondeur
Vous pouvez cliquer sur différents liens du tableau de bord de la solution pour explorer de façon plus approfondie un aspect spécifique. Par exemple, lorsque vous voyez une alerte ou une liaison réseau défectueuse s’afficher sur le tableau de bord, vous pouvez cliquer dessus pour approfondir vos recherches. Vous êtes alors redirigé vers une page répertoriant tous les liens de sous-réseau pour une liaison réseau particulière. Vous pouvez y voir l’état de perte, de latence et d’intégrité de chaque lien de sous-réseau, et identifier rapidement les liens à l’origine du problème. Vous pouvez ensuite cliquer sur **Afficher les liens de nœud** pour voir tous les liens de nœud pour le lien de sous-réseau défectueux. Ensuite, vous pouvez voir les liens de nœud à nœud, et identifier les liens de nœud défectueux.

Vous pouvez cliquer sur **Afficher la topologie** pour afficher la topologie, tronçon par tronçon, des itinéraires entre les nœuds source et de destination. Les itinéraires ou tronçons défectueux s’affichent en rouge pour vous permettre d’identifier rapidement un problème affectant une portion spécifique du réseau.

![Explorer les données en détail](./media/log-analytics-network-performance-monitor/npm-drill.png)

### <a name="network-state-recorder"></a>Enregistreur de l’état du réseau

Chaque vue affiche une capture instantanée de l’intégrité de votre réseau à un moment précis dans le temps. Par défaut, c’est l’état le plus récent qui s’affiche. La barre située en haut de la page affiche le point dans le temps pour lequel l’état est affiché. Vous pouvez choisir de revenir en arrière et d’afficher la capture instantanée de l’intégrité de votre réseau en cliquant sur la barre dans **Actions**. Vous pouvez également activer ou désactiver l’actualisation automatique pour n’importe quelle page pendant que vous consultez le dernier état.

![état du réseau](./media/log-analytics-network-performance-monitor/network-state.png)

#### <a name="trend-charts"></a>Graphiques de tendances
À chaque niveau exploré, vous pouvez consulter la tendance des pertes et de la latence pour une liaison réseau. Des graphiques de tendances sont également disponibles pour les liens de sous-réseau et de nœud. Vous pouvez modifier l’intervalle de temps à tracer sur le graphique à l’aide du contrôle de temps en haut de celui-ci.

Les graphiques de tendances présentent une perspective historique des performances d’une liaison réseau. Certains problèmes réseau sont temporaires par nature, ce qui complique leur identification en consultant simplement l’état actuel du réseau. En effet, ces problèmes peuvent apparaître rapidement et disparaître avant que quiconque s’en aperçoive, avant de réapparaître par la suite. Ces problèmes temporaires peuvent également compliquer la tâche des administrateurs d’applications, car ils se manifestent souvent sous la forme d’allongements inexpliqués du temps de réponse de celles-ci, même si tous leurs composants semblent s’exécuter sans difficulté.

Vous pouvez facilement détecter ces types de problèmes en examinant un graphique de tendance où le problème apparaît comme un pic soudain de latence ou de perte de paquets du réseau.

![Graphique de tendances](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>Carte topologique tronçon par tronçon
L’Analyseur de performances réseau affiche, tronçon par tronçon, la topologie des itinéraires entre deux nœuds sur une carte topologique interactive. Vous pouvez afficher la carte topologique en sélectionnant un lien de nœud, puis en cliquant sur **Afficher la topologie**. Vous pouvez également afficher la carte topologique en cliquant sur la vignette **Chemins** du tableau de bord. Lorsque vous cliquez sur la vignette **Chemins** du tableau de bord, vous devez sélectionner les nœuds source et de destination dans le volet de gauche, puis sur cliquer sur **Traçage** pour tracer les itinéraires entre les nœuds.

La carte topologique affiche le nombre d’itinéraires existant entre les deux nœuds, et les chemins d’accès qu’empruntent les paquets de données. Les goulots d’étranglement des performances réseau sont marqués en rouge sur la carte topologique. Vous pouvez localiser une connexion réseau ou un appareil réseau défectueux en examinant les éléments colorés en rouge sur la carte topologique.

Lorsque vous cliquez sur un nœud ou pointez dessus sur la carte topologique, vous pouvez voir des propriétés du nœud telles que son adresse IP et son nom de domaine complet. Cliquez sur un tronçon pour voir son adresse IP. Vous pouvez choisir de filtrer des itinéraires particuliers à l’aide des filtres du volet Actions réductible. Vous pouvez également simplifier les topologies réseau en masquant les sauts intermédiaires à l’aide du curseur du volet Actions. Vous pouvez effectuer un zoom avant ou arrière sur la carte topologique à l’aide de la roulette de la souris.

Notez que la topologie cartographiée est celle de la couche 3 et qu’elle ne contient pas les connexions et appareils de la couche 2.

![Carte topologique tronçon par tronçon](./media/log-analytics-network-performance-monitor/npm-topology.png)

#### <a name="fault-localization"></a>Localisation des erreurs
L’Analyseur de performances réseau est en mesure de trouver les goulots d’étranglement sur le réseau sans se connecter aux appareils réseau. Sur la base des données collectées à partir du réseau et en appliquant des algorithmes avancés au graphe réseau, l’Analyseur de performances réseau effectue une estimation probabiliste des parties du réseau les plus susceptibles d’être à la source du problème.

Cette approche est utile pour déterminer les goulots d’étranglement du réseau lorsque l’accès aux tronçons est indisponible, car elle ne requiert pas de données collectées à partir d’appareils réseau tels que des routeurs ou des commutateurs. Elle est également utile lorsque les tronçons entre deux nœuds ne sont pas sous votre contrôle administratif. Par exemple, les tronçons peuvent être des routeurs ISP.

### <a name="log-analytics-search"></a>Recherche Log Analytics
Toutes les données présentées sous forme graphique via le tableau de bord de l’Analyseur de performances réseau et les pages d’exploration sont également disponibles en mode natif dans une recherche Log Analytics. Vous pouvez interroger les données à l’aide du langage de requête de recherche, et créer des rapports personnalisés en exportant les données vers Excel ou Power BI. Le panneau **Requêtes courantes** dans le tableau de bord comprend des requêtes utiles qui peuvent vous servir de point de départ pour créer vos propres requêtes et rapports.

![Requêtes de recherche](./media/log-analytics-network-performance-monitor/npm-queries.png)

## <a name="investigate-the-root-cause-of-a-health-alert"></a>Rechercher la cause première d’une alerte d’intégrité
À présent que vous savez ce qu’est le moniteur de performances réseau, voyons comment identifier simplement la cause première d’un événement d’intégrité.

1. La page Vue d’ensemble fournit une capture instantanée de l’intégrité de votre réseau via la vignette **Moniteur de performances réseau**. Vous pouvez constater que sur les 6 liens de sous-réseau analysés, 2 sont défectueux. Cela mérite d’être examiné. Cliquez sur la vignette pour afficher le tableau de bord de la solution.<br><br> ![Vignette Moniteur de performances réseau](./media/log-analytics-network-performance-monitor/npm-investigation01.png)  
2. Dans l’exemple d’image ci-dessous, vous remarquerez qu’il existe un événement d’intégrité dans un lien réseau qui n’est pas intègre. Vous décidez d’analyser le problème et cliquez sur le lien réseau **DMZ2-DMZ1** pour déterminer la cause du problème.<br><br> ![Exemple de liaison réseau défectueuse](./media/log-analytics-network-performance-monitor/npm-investigation02.png)  
3. La page détaillée affiche tous les liens de sous-réseau du lien réseau **DMZ2-DMZ1**. Vous pouvez remarquer que, pour les deux liens le sous-réseau, la latence a dépassé le seuil au-delà duquel la liaison réseau est jugée défectueuse. Vous pouvez également voir les tendances de latence des deux liens de sous-réseau. Le contrôle de sélection du temps dans le graphe vous permet de vous concentrer sur la plage de temps requise. Vous pouvez voir l’heure à laquelle la latence a atteint son pic. Vous pouvez ensuite effectuer une recherche dans les journaux correspondant à cette période pour étudier le problème. Cliquez sur **Afficher les liens de nœud** pour consulter des détails supplémentaires.<br><br> ![Exemple de liens de sous-réseau défectueux](./media/log-analytics-network-performance-monitor/npm-investigation03.png) 
4. Comme la page précédente, la page détaillée relative au lien de sous-réseau concerné répertorie les liens de nœud qui le composent. Vous pouvez effectuer ici des actions similaires à celles que vous avez faites à l’étape précédente. Cliquez sur **Afficher la topologie** pour afficher la topologie entre les 2 nœuds.<br><br> ![Exemple de liens de nœud défectueux](./media/log-analytics-network-performance-monitor/npm-investigation04.png)  
5. Tous les chemins entre les 2 nœuds sélectionnés sont représentés dans la carte topologique. Vous pouvez visualiser, tronçon par tronçon, la topologie des itinéraires entre deux nœuds sur la carte topologique. Vous obtenez ainsi une vision claire du nombre d’itinéraires existant entre les nœuds et des chemins qu’empruntent les paquets de données. Les goulots d’étranglement des performances du réseau sont marqués en rouge. Vous pouvez localiser une connexion réseau ou un appareil réseau défectueux en examinant les éléments colorés en rouge sur la carte topologique.<br><br> ![Exemple d’affichage topologique défectueux](./media/log-analytics-network-performance-monitor/npm-investigation05.png)  
6. Vous pouvez consulter les pertes, la latence et le nombre de sauts de chaque chemin dans le volet **Actions**. Utilisez la barre de défilement pour afficher les détails de ces chemins défectueux.  Utilisez les filtres pour sélectionner les chemins d’accès avec le saut défectueux afin de tracer uniquement la topologie des chemins d’accès sélectionnés. Vous pouvez utiliser la roulette de votre souris pour effectuer un zoom avant ou arrière sur la carte topologique.

   Dans l’image ci-dessous, vous pouvez voir clairement la cause première des aspects problématiques d’une section spécifique du réseau en examinant les chemins et les tronçons marqués de rouge. Un clic sur un nœud dans la carte topologique révèle les propriétés du nœud, dont son nom de domaine complet et son adresse IP. Un clic sur un tronçon affiche l’adresse IP de celui-ci.<br><br> ![topologie défectueuse : exemple de détails d’un chemin](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="provide-feedback"></a>Fournir des commentaires

- **UserVoice** : vous pouvez publier vos idées concernant les fonctions de l’Analyseur de performances réseau que vous aimeriez voir améliorer. Visitez notre [page UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring).
- **Rejoignez notre cohorte** : nous sommes toujours ravis d’accueillir de nouveaux clients dans notre cohorte. Dans ce cadre, vous pouvez accéder en avant-première aux nouvelles fonctionnalités et nous aider à améliorer l’Analyseur de performances réseau. Si vous souhaitez y participer, répondez à cette [enquête rapide](https://aka.ms/npmcohort).

## <a name="next-steps"></a>Étapes suivantes
* [Rechercher dans les journaux](log-analytics-log-searches.md) pour afficher des enregistrements de données détaillées sur les performances réseau.
