---
title: "Fonctionnalité Analyseur de performances de la solution Network Performance Monitor dans Azure Log Analytics | Microsoft Docs"
description: "La fonctionnalité Analyseur de performances de Network Performance Monitor vous permet de surveiller la connectivité réseau en différents points du réseau, tels que les déploiements de cloud et les emplacements locaux, plusieurs centres de données et les succursales, les applications/micro-services multiniveaux critiques."
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
ms.openlocfilehash: a90ab3bc857b704d9d94daf96d17611844abb1a6
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="network-performance-monitor-solution---performance-monitoring"></a>Solution Network Performance Monitor - Analyseur de performances réseau

La fonctionnalité Analyseur de performances de [Network Performance Monitor](log-analytics-network-performance-monitor.md) vous permet de surveiller la connectivité réseau en différents points du réseau, tels que les déploiements de cloud et les emplacements locaux, plusieurs centres de données et les succursales, les applications/micro-services multiniveaux critiques. Avec l’Analyseur de performances réseau, vous pouvez détecter les problèmes réseau avant que vos utilisateurs ne se plaignent. Les avantages clés sont les suivants : 

- Surveiller les pertes et la latence entre différents sous-réseaux et définir des alertes 
- Surveiller tous les chemins d’accès (y compris les chemins d’accès redondants) sur le réseau 
- Résoudre les problèmes réseau temporaires et ponctuels qui sont difficiles à répliquer 
- Déterminer le segment spécifique sur le réseau qui est responsable de la dégradation des performances 
- Surveiller l’intégrité du réseau, sans SNMP 


![Network Performance Monitor](media/log-analytics-network-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Configuration
Pour ouvrir la configuration de Network Performance Monitor, ouvrez la [solution Network Performance Monitor](log-analytics-network-performance-monitor.md) et cliquez sur le bouton **Configurer**.

![Configurer Network Performance Monitor](media/log-analytics-network-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Créer des réseaux

Dans NPM, un réseau est un conteneur logique de sous-réseaux. Cela vous permet d’organiser la surveillance de votre infrastructure réseau en fonction de vos besoins de surveillance. Vous pouvez créer un réseau avec un nom convivial et lui ajouter des sous-réseaux selon votre logique métier. Par exemple, vous pouvez créer un réseau nommé London et ajouter tous les sous-réseaux de votre centre de données de Londres, ou un réseau nommé *ContosoFrontEnd* et ajouter tous les sous-réseaux alimentant le serveur frontal de votre application nommée Contoso sur ce réseau. La solution crée automatiquement un réseau par défaut qui contient tous les sous-réseaux détectés dans votre environnement. Chaque fois que vous créez un réseau, vous y ajoutez un sous-réseau qui est retiré du réseau Par défaut. Si vous supprimez un réseau, toutes ses sous-réseaux sont automatiquement restitués au réseau Par défaut. En d’autres termes, le réseau Par défaut joue le rôle de conteneur pour tous les sous-réseaux non contenus dans un réseau défini par un utilisateur. Vous ne pouvez pas modifier ou supprimer le réseau Par défaut. Il reste toujours dans le système. En revanche, vous pouvez créer autant de réseaux personnalisés que nécessaire. Dans la plupart des cas, les sous-réseaux de votre organisation sont organisés en plusieurs réseaux, et vous devez créer un ou plusieurs réseaux pour regrouper vos sous-réseaux conformément à votre logique métier.

Pour créer un réseau :


1. Cliquez sur l’onglet **Réseaux**.
1. Cliquez sur  **Ajouter un réseau**, puis tapez le nom et la description du réseau. 
2. Sélectionnez un ou plusieurs sous-réseaux, puis cliquez sur **Ajouter**. 
3. Pour enregistrer la configuration, cliquez sur **Enregistrer**. 


### <a name="create-monitoring-rules"></a>Créer des règles de surveillance 

L’Analyseur de performances génère des événements d’intégrité lorsque le seuil des performances des connexions réseau entre deux sous-réseaux ou deux réseaux est dépassé. Le système peut apprendre ces seuils automatiquement, et vous pouvez également indiquer des seuils personnalisés. Le système crée automatiquement une règle par défaut. Celle-ci crée un événement d’intégrité chaque fois qu’une perte ou une latence entre une paire quelconque de liaisons réseau/sous-réseau dépasse le seuil appris par le système. Cela permet à la solution de surveiller votre infrastructure réseau tant que vous n’avez pas créé explicitement de règles de surveillance. Si la **règle par défaut** est activée, tous les nœuds assurent la surveillance en envoyant des transactions synthétiques à tous les autres nœuds que vous avez activés. La règle par défaut est utile sur les réseaux de petite taille, par exemple, si vous avez un petit nombre de serveurs qui exécutent un microservice et que vous souhaitez vous assurer que tous les serveurs sont connectés les uns aux autres. 

>[!NOTE]
> Il est recommandé de désactiver la **règle par défaut** et de créer des règles de surveillance personnalisées, en particulier pour les réseaux importants dans lesquels vous utilisez un grand nombre de nœuds pour la surveillance. Cela limitera le trafic généré par la solution et vous aidera à organiser la surveillance de votre réseau. 

Créez des règles de surveillance selon votre logique métier. Par exemple, si vous souhaitez surveiller les performances de la connectivité réseau de 2 bureaux au siège social, regroupez tous les sous-réseaux du bureau 1 dans le réseau O1, tous les sous-réseaux du bureau 2 dans le réseau O2 et tous les sous-réseaux du siège social dans le réseau H. Créez deux règles de surveillance : une entre O1 et H et l’autre entre O2 et H. 

Pour créer des règles de surveillance personnalisées :

1. Cliquez sur **Ajouter une règle** sous l’onglet **Surveiller**, puis entrez le nom et la description de la règle. 
2. Sélectionnez la paire de liaisons réseau ou sous-réseau à surveiller dans les listes. 
3. Commencez par sélectionner le réseau contenant le(s) premier(s) sous-réseau(x) d’intérêt dans la liste déroulante de réseau, puis sélectionnez le(s) sous-réseau(x) dans la liste déroulante de sous-réseaux correspondante. Sélectionnez **Tous les sous-réseaux** si vous souhaitez analyser tous les sous-réseaux dans une liaison réseau. Sélectionnez de la même manière les autres sous-réseaux d’intérêt. Vous pouvez également cliquer sur **Ajouter une Exception** pour exclure l’analyse de liens de sous-réseau spécifiques de la sélection que vous avez faite. 
4. Choisissez entre le protocole ICMP et le protocole TCP pour l’exécution des transactions synthétiques. 
5. Si vous ne souhaitez pas créer d’événements d’intégrité pour les éléments que vous avez sélectionnés, désactivez l’option **Activer la surveillance de l’intégrité sur les liens couverts par cette règle**. 
6. Choisissez les conditions d’analyse. Vous pouvez définir des seuils personnalisés pour la génération d’événements d’intégrité en tapant des valeurs de seuil. Chaque fois que la valeur d’une condition dépasse son seuil sélectionné pour la paire de réseaux/sous-réseaux sélectionnée, un événement d’intégrité est généré. 
7. Pour enregistrer la configuration, cliquez sur **Enregistrer**. 

Vous pouvez intégrer la règle d’analyse que vous avez enregistrée à Gestion des alertes en cliquant sur **Créer une alerte**. Une règle d’alerte est créée automatiquement avec la requête de recherche et d’autres paramètres requis renseignés automatiquement. En utilisant une règle d’alerte, vous pouvez recevoir des alertes par e-mail en plus des alertes existant dans la solution Analyseur de performances réseau. Les alertes peuvent également déclencher des actions correctives avec les runbooks ou elles peuvent s’intégrer aux solutions existantes de gestion des services à l’aide des webhooks. Vous pouvez cliquer sur **Manage Alert (Gérer une alerte)** pour modifier les paramètres d’alerte. 

Vous pouvez maintenant créer d’autres règles pour l’Analyseur de performances ou accéder au tableau de bord de la solution pour commencer à utiliser la fonctionnalité 

### <a name="choose-the-protocol"></a>Choisir le protocole

Le Moniteur de performances réseau utilise des transactions synthétiques pour calculer les mesures de performances réseau comme la perte de paquets et la latence des liaisons. Pour mieux comprendre, prenez un agent NPM connecté à une extrémité d’une liaison réseau. Cet agent NPM envoie des paquets de sonde à un second agent NPM connecté à une autre extrémité du réseau. Le second agent répond avec des paquets de réponse. Ce processus est répété plusieurs fois. En mesurant le nombre de réponses et le temps nécessaire pour recevoir chaque réponse, le premier agent NPM évalue la latence des liaisons et les rejets de paquet. 

Le format, la taille et la séquence de ces paquets sont déterminés par le protocole choisi au moment de la création des règles de surveillance. En fonction du protocole des paquets, les appareils réseau intermédiaires (routeurs, commutateurs, etc.) peuvent traiter ces paquets différemment. Le protocole que vous choisissez a donc une incidence sur la précision des résultats. Il détermine également si des étapes manuelles sont nécessaires après le déploiement de la solution NPM. 

Pour l’exécution des transactions synthétiques, NPM vous offre le choix entre le protocole ICMP et le protocole TCP. Si vous choisissez ICMP quand vous créez une règle de transaction synthétique, les agents NPM utilisent des messages ICMP ECHO pour calculer la latence du réseau et la perte de paquets. ICMP ECHO utilise le même message que celui envoyé par l’utilitaire Ping traditionnel. Quand vous utilisez le protocole TCP, les agents NPM envoient un paquet TCP SYN sur le réseau. Ceci est suivi par l’établissement d’une liaison TCP et la suppression de la connexion à l’aide de paquets RST. 

Avant de choisir un protocole, tenez compte des informations suivantes : 

**Découverte de plusieurs routages réseau.**  TCP est plus précis dans le cadre de la détection de plusieurs routages et nécessite moins d’agents dans chaque sous-réseau. Par exemple, un ou deux agents utilisant TCP peuvent détecter tous les chemins redondants entre les sous-réseaux. En revanche, plusieurs agents utilisant ICMP sont nécessaires pour obtenir des résultats similaires. Avec ICMP, si vous avez un certain nombre de routages entre deux sous-réseaux, il vous faut plus de 5N agents dans un sous-réseau source ou de destination. 

**Précision des résultats.** Les routeurs et les commutateurs ont tendance à accorder aux paquets ICMP ECHO une priorité inférieure à celle des paquets TCP. Dans certaines situations, quand les appareils réseau sont lourdement chargés, les données obtenues par TCP reflètent plus fidèlement la perte et la latence constatées par les applications. Cela vient du fait que la plupart du trafic des applications passe par TCP. Dans de tels cas, ICMP offre de moins bons résultats que TCP. 

**Configuration du pare-feu.** Le protocole TCP nécessite l’envoi des paquets TCP à un port de destination. Le port par défaut utilisé par les agents NPM est le port 8084. Vous pouvez toutefois le modifier au moment de la configuration des agents. Vous devez donc vérifier que vos pare-feu réseau ou règles NSG (dans Azure) autorisent le trafic sur le port. Vous devez également veiller à ce que le pare-feu local sur les ordinateurs où les agents sont installés est configuré pour autoriser le trafic sur ce port. Vous pouvez utiliser des scripts PowerShell pour configurer des règles de pare-feu sur les ordinateurs Windows, mais vous devez configurer manuellement votre pare-feu réseau. Contrairement à TCP, le protocole ICMP n’utilise pas de port. Dans la plupart des scénarios d’entreprise, le trafic ICMP est autorisé à franchir les pare-feu pour vous permettre d’utiliser des outils de diagnostic réseau comme l’utilitaire Ping. Si un test Ping effectué entre deux ordinateurs aboutit, vous pouvez utiliser le protocole ICMP sans avoir à configurer les pare-feu manuellement. 

>[!NOTE] 
> Certains pare-feu peuvent bloquer le protocole ICMP, ce qui peut entraîner la création d’un grand nombre d’événements dans votre système de gestion des événements et des informations de sécurité lors de la retransmission. Assurez-vous que le protocole que vous choisissez n’est pas bloqué par un pare-feu réseau/NSG. Si c’est le cas, NPM n’est pas en mesure de surveiller le segment de réseau. De ce fait, nous vous recommandons d’utiliser le protocole TCP pour la surveillance. Vous devez utiliser le protocole ICMP dans les cas où vous ne pouvez pas utiliser le protocole TCP, par exemple lorsque : 
>
> - Vous utilisez des nœuds basés sur un client Windows, étant donné que les sockets bruts du protocole TCP ne sont pas autorisés dans le client Windows.
> - Votre pare-feu réseau/NSG bloque le protocole TCP
> - Comment changer de protocole 

Si vous avez choisi ICMP durant le déploiement, vous pouvez basculer vers TCP à tout moment en modifiant la règle de surveillance par défaut :

1. Accédez à **Performances réseau** > **Moniteur** > **Configurer** > **Moniteur**, puis cliquez sur  **Règle par défaut**. 
2. Faites défiler la page jusqu’à la section **Protocole** et sélectionnez le protocole à utiliser. 
3. Cliquez sur **Enregistrer** pour appliquer le paramètre. 

Même si la règle par défaut utilise un protocole spécifique, vous pouvez créer des règles avec un autre protocole. Vous pouvez même créer une combinaison de règles : certaines règles utilisant ICMP et d’autres TCP. 

## <a name="walkthrough"></a>Procédure pas à pas 

À présent que vous savez ce qu’est l’Analyseur de performances, voyons comment identifier simplement la cause première d’un événement d’intégrité.  

Dans le tableau de bord de la solution, vous pouvez constater qu’il existe un événement d’intégrité : un lien réseau n’est pas intègre. Vous décidez d’examiner le problème et cliquez sur la vignette **Liens réseau en cours de surveillance**.

Dans la page d’exploration, vous observez que le lien réseau **DMZ2-DMZ1** n’est pas intègre. Vous cliquez sur le lien **Afficher les liens de sous-réseau** de ce lien réseau. 


La page détaillée affiche tous les liens de sous-réseau du lien réseau **DMZ2-DMZ1**. Vous pouvez remarquer que, pour les deux liens le sous-réseau, la latence a dépassé le seuil au-delà duquel la liaison réseau est jugée défectueuse. Vous pouvez également voir les tendances de latence des deux liens de sous-réseau. Le contrôle de sélection du temps dans le graphe vous permet de vous concentrer sur la plage de temps requise. Vous pouvez voir l’heure à laquelle la latence a atteint son pic. Vous pouvez ensuite effectuer une recherche dans les journaux correspondant à cette période pour étudier le problème. Cliquez sur **Afficher les liens de nœud** pour consulter des détails supplémentaires. 
 
 ![Liens de sous-réseau](media/log-analytics-network-performance-monitor/subnetwork-links.png) 

Comme la page précédente, la page détaillée relative au lien de sous-réseau concerné répertorie les liens de nœud qui le composent. Vous pouvez effectuer ici des actions similaires à celles que vous avez faites à l’étape précédente. Cliquez sur **Afficher la topologie** pour afficher la topologie entre les deux nœuds. 
 
 ![Liens de nœud](media/log-analytics-network-performance-monitor/node-links.png) 

Tous les chemins entre les deux nœuds sélectionnés sont représentés dans la carte topologique. Vous pouvez visualiser, tronçon par tronçon, la topologie des itinéraires entre deux nœuds sur la carte topologique. Vous obtenez ainsi une vision claire du nombre d’itinéraires existant entre les nœuds et des chemins qu’empruntent les paquets de données. Les goulots d’étranglement des performances du réseau sont marqués en rouge. Vous pouvez localiser une connexion réseau ou un appareil réseau défectueux en examinant les éléments colorés en rouge sur la carte topologique. 

 ![Tableau de bord des topologies](media/log-analytics-network-performance-monitor/topology-dashboard.png) 

Vous pouvez consulter les pertes, la latence et le nombre de sauts de chaque chemin dans le volet **Actions**. Utilisez la barre de défilement pour afficher les détails de ces chemins défectueux. Utilisez les filtres pour sélectionner les chemins d’accès avec le saut défectueux afin de tracer uniquement la topologie des chemins d’accès sélectionnés. Vous pouvez utiliser la roulette de votre souris pour effectuer un zoom avant ou arrière sur la carte topologique. 

Dans l’image ci-dessous, vous pouvez voir clairement la cause première des aspects problématiques d’une section spécifique du réseau en examinant les chemins et les tronçons marqués de rouge. Un clic sur un nœud dans la carte topologique révèle les propriétés du nœud, dont son nom de domaine complet et son adresse IP. Un clic sur un tronçon affiche l’adresse IP de celui-ci. 
 
![Tableau de bord des topologies](media/log-analytics-network-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Étapes suivantes
* [Rechercher dans les journaux](log-analytics-log-searches.md) pour afficher des enregistrements de données détaillées sur les performances réseau.
