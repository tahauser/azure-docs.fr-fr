---
title: Configurer Network Performance Monitor pour les circuits Azure ExpressRoute | Microsoft Docs
description: Configurez la surveillance réseau basée sur le cloud pour les circuits Azure ExpressRoute.
documentationcenter: na
services: expressroute
author: ajaycode
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: agummadi
ms.openlocfilehash: 36cd2f106c39f1a6bdcb6ee33b96209974a06336
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Configurer Network Performance Monitor pour ExpressRoute

Network Performance Monitor (NPM) est une solution de surveillance réseau basée sur le cloud, qui surveille la connectivité entre les déploiements cloud Azure et les déploiements locaux (succursales, etc.). NPM fait partie de Microsoft Operations Management Suite (OMS). NPM offre désormais une extension pour ExpressRoute, qui vous permet de surveiller les performances réseau sur des circuits ExpressRoute configurés pour utiliser l’homologation privée. Lorsque vous configurez NPM pour ExpressRoute, vous pouvez détecter les problèmes de réseau pour les identifier et les éliminer.

Vous pouvez :

* Surveiller les pertes et la latence entre différents réseaux virtuels et définir des alertes

* Surveiller tous les chemins d’accès (y compris les chemins d’accès redondants) sur le réseau

* Résoudre les problèmes réseau temporaires et dans le temps qui sont difficiles à répliquer

* Aider à déterminer un segment spécifique sur le réseau qui est responsable de la dégradation des performances

* Obtenir le débit par réseau virtuel (si vous avez des agents installés dans chaque réseau virtuel)

* Vérifier l’état système ExpressRoute à partir d’un point antérieur dans le temps

## <a name="regions"></a>Régions prises en charge

Vous pouvez surveiller des circuits ExpressRoute dans n’importe quelle partie du monde à l’aide d’un espace de travail hébergé dans une des régions suivantes :

* Europe de l'Ouest
* Centre-Ouest des États-Unis
* Est des États-Unis 
* Asie du Sud-Est 
* Australie du Sud-Est

>[!NOTE]
>La prise en charge de la surveillance des circuits ExpressRoute connectés à des réseaux virtuels dans le cloud Azure Government est prévue pour le deuxième trimestre 2018.   
>

## <a name="workflow"></a>Flux de travail

Des agents de surveillance sont installés sur plusieurs serveurs, en local et sur Azure. Les agents communiquent entre eux mais n’envoient pas de données. Ils envoient des paquets de négociation TCP. La communication entre les agents permet à Azure de mapper la topologie réseau et le chemin d’accès que le trafic peut prendre.

1. Créez un espace de travail NPM dans une des [régions prises en charge](#regions).
2. Installer et configurer des agents logiciels : 
    * Installez des agents de surveillance sur les serveurs locaux et les machines virtuelles Azure.
    * Configurez les paramètres sur les serveurs de l’agent de surveillance pour autoriser les agents de surveillance à communiquer. (Ouvrez les ports du pare-feu, etc.)
3. Configurez des règles de groupe de sécurité réseau pour permettre à l’agent de surveillance installé sur des machines virtuelles Azure de communiquer avec des agents de surveillance locaux.
4. Configurez la surveillance : détection automatique et gestion des réseaux visibles dans NPM.

Si vous utilisez déjà Network Performance Monitor pour surveiller d’autres objets ou services, et si vous avez déjà un espace de travail dans une des régions prises en charge, vous pouvez passer les étapes 1 et 2 et commencer votre configuration à l’étape 3.

## <a name="configure"></a>Étape 1 : Créer un espace de travail

Créez un espace de travail dans l’abonnement où les réseaux virtuels sont liés aux circuits ExpressRoute.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez l’abonnement avec les réseaux virtuels associés à votre circuit ExpressRoute. Ensuite, recherchez « Network Performance Monitor » dans la liste des services de la **Place de marché**. Dans les résultats, cliquez pour ouvrir la page **Network Performance Monitor**.

>[!NOTE]
>Vous pouvez créer un espace de travail ou utiliser un espace de travail existant.  Si vous souhaitez utiliser un espace de travail existant, vous devez vérifier qu’il a été migré vers le nouveau langage de requête. [Plus d’informations...](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-log-search-upgrade)
>

  ![portail](.\media\how-to-npm\3.png)<br><br>
2. En bas de la page **Network Performance Monitor**, cliquez sur **Créer** pour ouvrir la page **Network Performance Monitor - Créer une solution**. Cliquez sur **Espace de travail OMS - Sélectionner un espace de travail** pour ouvrir la page Espaces de travail. Cliquez sur **+ Créer un espace de travail** pour ouvrir la page Espaces de travail.
3. Sur la page **Espace de travail OMS**, sélectionnez **Créer** et configurez les paramètres suivants :

  * Espace de travail OMS : saisissez un nom pour votre espace de travail.
  * Abonnement : si vous possédez plusieurs abonnements, choisissez celui que vous souhaitez associer au nouvel espace de travail.
  * Groupe de ressources : créez un groupe de ressources ou utilisez un groupe existant.
  * Emplacement : vous devez sélectionner une [région prise en charge](#regions).
  * Niveau tarifaire : sélectionnez « Gratuit ».
  
  >[!NOTE]
  >Le circuit ExpressRoute peut se trouver n’importe où dans le monde et pas nécessairement dans la même région que l’espace de travail.
  >
  
  ![espace de travail](.\media\how-to-npm\4.png)<br><br>
4. Cliquez sur **OK** pour enregistrer et déployer le modèle de paramètres. Une fois le modèle validé, cliquez sur **Créer** pour déployer l’espace de travail.
5. Une fois l’espace de travail déployé, accédez à la ressource **NetworkMonitoring(name)** que vous avez créée. Validez les paramètres, puis cliquez sur **La solution nécessite une configuration supplémentaire**.

  ![configuration supplémentaire](.\media\how-to-npm\5.png)

## <a name="agents"></a>Étape 2 : Installer et configurer des agents

### <a name="download"></a>2.1 : Télécharger le fichier de configuration de l’agent

1. Accédez à l’onglet **Paramètres communs** de la page **Configuration de Network Performance Monitor** de votre ressource. Cliquez sur l’agent qui correspond au processeur de votre serveur dans la section **Installer les agents OMS**, puis téléchargez le fichier d’installation.

 
2. Ensuite, copiez et collez **l’ID d’espace de travail** et la **clé primaire** dans le bloc-notes.
3. Dans la section **Configurer les agents OMS pour le monitoring avec le protocole TCP**, téléchargez le script PowerShell. Le script PowerShell vous permet d’ouvrir le port de pare-feu approprié pour les transactions TCP.

  ![Script PowerShell](.\media\how-to-npm\7.png)

### <a name="installagent"></a>2.2 : Installer un agent de monitoring sur chaque serveur de monitoring (sur chaque réseau virtuel dont vous voulez effectuer le monitoring)

Nous vous recommandons d’installer au moins deux agents de chaque côté de la connexion ExpressRoute (par exemple, localement, réseaux virtuels Azure) à des fins de redondance. Pour installer les agents, procédez comme suit :
  
  >[!NOTE]
  >L’agent doit être installé sur Windows Server 2008 SP1 ou version ultérieure. Le monitoring des circuits ExpressRoute à l’aide du système d’exploitation de bureau Windows et du système d’exploitation Linux n’est pas pris en charge. 
  >
  >
  
  >[!NOTE]
  >Les agents SCOM peuvent ne pas être en mesure de détecter de manière cohérente si ils sont hébergés dans Azure.  Nous recommandons de ne pas utiliser les agents SCOM dans des réseaux virtuels Azure pour surveiller ExpressRoute.
  >
  >

1. Exécutez **Installation** pour installer l’agent sur chaque serveur que vous souhaitez utiliser pour la surveillance ExpressRoute. Le serveur que vous utilisez pour la surveillance peut être une machine virtuelle ou locale et doit avoir accès à Internet. Vous devez installer au moins un agent localement, et un agent sur chaque segment de réseau que vous souhaitez surveiller dans Azure.
2. Sur la page d’**accueil**, cliquez sur **Suivant**.
3. Sur la page **Termes du contrat de licence**, lisez les conditions de licence, puis cliquez sur **J’accepte**.
4. Sur la page **Dossier de destination**, modifiez ou conservez le dossier d’installation par défaut, puis cliquez sur **Suivant**.
5. Sur la page **Options d’installation de l’agent**, vous pouvez choisir de connecter l’agent à Azure Log Analytics (OMS) ou Operations Manager. Ou bien, vous pouvez ne rien choisir si vous souhaitez configurer l’agent ultérieurement. Une fois vos sélections effectuées, cliquez sur **Suivant**.

  * Si vous choisissez de vous connecter à **Azure Log Analytics (OMS)**, collez **l’ID de l’espace de travail** et la **clé de l’espace de travail** (clé primaire) que vous avez copiés dans le bloc-notes dans la section précédente. Cliquez ensuite sur **Suivant**.

    ![ID et clé](.\media\how-to-npm\8.png)
  * Si vous choisissez de vous connecter à **Operations Manager**, sur la page **Configuration du groupe d’administration**, saisissez le **nom du groupe d’administration**, le **serveur d’administration** et le **port du serveur d’administration**. Cliquez ensuite sur **Suivant**.

    ![Operations Manager](.\media\how-to-npm\9.png)
  * Sur la page **Compte d’action d’agent**, choisissez le compte **système local** ou un **compte d’ordinateur de domaine ou local**. Cliquez ensuite sur **Suivant**.

    ![Compte](.\media\how-to-npm\10.png)
6. Dans la page **Prêt pour l’installation**, passez en revue vos choix, puis cliquez sur **Installer**.
7. Dans la page **Configuration effectuée**, cliquez sur **Terminer**.
8. Lorsque vous avez terminé, Microsoft Monitoring Agent apparaît dans le Panneau de configuration. Vous pouvez y contrôler votre configuration et vérifier que l’agent est bien connecté à Operational Insights (OMS). Une fois connecté à OMS, l’agent affiche un message indiquant : **Microsoft Monitoring Agent est bien connecté au service Microsoft Operations Management Suite**.

9. Veuillez répéter cette étape pour chaque réseau virtuel dont vous voulez effectuer le monitoring.

### <a name="proxy"></a>2.3 : Configurer des paramètres de proxy (facultatif)

Si vous utilisez un proxy web pour accéder à Internet, procédez comme suit pour configurer les paramètres de proxy pour Microsoft Monitoring Agent. Effectuez ces étapes pour chaque serveur. Si vous devez configurer plusieurs serveurs, utilisez un script pour automatiser ce processus. Si c’est le cas, consultez [Pour configurer les paramètres de proxy de Microsoft Monitoring Agent à l’aide d’un script](../log-analytics/log-analytics-windows-agent.md).

Pour configurer les paramètres de proxy de Microsoft Monitoring Agent dans le Panneau de configuration :

1. Ouvrir le **Panneau de configuration**.
2. Ouvrez **Microsoft Monitoring Agent**.
3. Cliquez sur l'onglet **Paramètres de proxy** .
4. Sélectionnez **Utiliser un serveur proxy** et tapez l’URL et le numéro de port, si nécessaire. Si votre serveur proxy requiert une authentification, tapez le nom d'utilisateur et le mot de passe pour accéder au serveur proxy.

  ![proxy](.\media\how-to-npm\11.png)

### <a name="verifyagent"></a>2.4 : Vérifier la connectivité de l’agent

Vous pouvez facilement vérifier si vos agents communiquent.

1. Sur un serveur avec l’agent de surveillance, ouvrez le **Panneau de configuration**.
2. Ouvrez **Microsoft Monitoring Agent**.
3. Cliquez sur l’onglet **Azure Log Analytics (OMS)**.
4. Dans la colonne **État**, vous devez voir que l’agent est correctement connecté au service Operations Management Suite.

  ![status](.\media\how-to-npm\12.png)

### <a name="firewall"></a>2.5 : Ouvrir les ports de pare-feu sur les serveurs de l’agent de surveillance

Pour utiliser le protocole TCP, vous devez ouvrir des ports de pare-feu pour veiller à ce que les agents de surveillance puissent communiquer.

Vous pouvez exécuter un script PowerShell qui crée les clés de Registre requises par Network Performance Monitor, ainsi que des règles de pare-feu Windows pour autoriser les agents de surveillance à créer des connexions TCP entre eux. Les clés de Registre créées par le script spécifient également s’il faut enregistrer les journaux de débogage et le chemin d’accès des fichiers journaux. Le script définit également le port TCP de l’agent utilisé pour la communication. Les valeurs de ces clés étant définies automatiquement par le script, vous n’avez pas à les modifier manuellement.

Le port 8084 est ouvert par défaut. Vous pouvez utiliser un port personnalisé en ajoutant le paramètre « portNumber » au script. Toutefois, si vous le faites, vous devez spécifier le même port pour tous les serveurs sur lesquels vous exécutez le script.

>[!NOTE]
>Le script PowerShell « EnableRules » configure des règles de pare-feu Windows uniquement sur le serveur exécutant le script. Si vous avez un pare-feu réseau, vous devez vous assurer qu’il autorise le trafic destiné au port TCP que l’Analyseur de performances réseau utilise.
>
>

Sur les serveurs d’agent, ouvrez une fenêtre PowerShell avec des privilèges administratifs. Exécutez le script PowerShell [EnableRules](https://aka.ms/npmpowershellscript) (que vous avez téléchargé précédemment). N’utilisez pas de paramètres.

  ![PowerShell_Script](.\media\how-to-npm\script.png)

## <a name="opennsg"></a>Étape 3 : Configurer les règles du groupe de sécurité réseau

Pour les serveurs d’agent de surveillance se trouvant dans Azure, vous devez configurer les règles du groupe de sécurité réseau (NSG) pour autoriser le trafic TCP sur un port utilisé par NPM pour les transactions synthétiques. Par défaut, il s’agit du port 8084. Cela permet à un agent de surveillance installé sur une machine virtuelle Azure de communiquer avec un agent de surveillance local.

Pour plus d’informations concernant le groupe de sécurité réseau, consultez [Créer des groupes de sécurité réseau à l’aide du portail Azure](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Vérifiez que vous avez installé les agents (l’agent de serveur local et l’agent de serveur Azure) et que vous avez exécuté le script PowerShell avant de continuer avec cette étape.
>
>


## <a name="setupmonitor"></a>Étape 4 : Configurer NPM pour la surveillance ExpressRoute

Après avoir terminé les sections précédentes, vous pouvez configurer le monitoring.

1. Accédez à la vignette de la vue d’ensemble Network Performance Monitor en vous rendant sur la page **All Resources** (Toutes les ressources) et en cliquant sur l’espace de travail NPM mis sur liste verte.

  ![espace de travail npm](.\media\how-to-npm\npm.png)
2. Cliquez sur la vignette de vue d’ensemble **Network Performance Monitor** pour afficher le tableau de bord. Le tableau de bord contient une page ExpressRoute, qui montre que ExpressRoute est dans un état « non configuré ». Cliquez sur **Installation de la fonctionnalité** pour ouvrir la page de configuration de Network Performance Monitor.

  ![installation de fonctionnalité](.\media\how-to-npm\npm2.png)
3. Sur la page de configuration, accédez à l’onglet « Appairages ExpressRoute » situé dans le panneau de gauche. Cliquez sur **Discover now** (Détecter maintenant).

  ![détecter](.\media\how-to-npm\13.png)
4. Une fois la détection terminée, vous voyez des règles pour un nom de circuit et un nom de réseau virtuel uniques. Au départ, ces règles sont désactivées. Activez les règles, puis sélectionnez les agents de surveillance et les valeurs de seuil.

  ![règles](.\media\how-to-npm\14.png)
5. Après l’activation des règles et la sélection des valeurs et des agents que vous souhaitez surveiller, vous devez attendre entre 30 minutes et 1 heure pour que les valeurs commencent à s’ajouter et que les vignettes **Surveillance ExpressRoute** deviennent disponibles. Une fois que vous voyez les vignettes de surveillance, vos circuits ExpressRoute et ressources de connexion sont surveillés par NPM.

  ![vignettes de surveillance](.\media\how-to-npm\15.png)

## <a name="explore"></a>Étape 5 : Afficher des vignettes de surveillance

### <a name="dashboard"></a>Page Network Performance Monitor

La page NPM contient une page pour ExpressRoute qui présente une vue d’ensemble de l’intégrité des circuits et des homologations ExpressRoute.

  ![tableau de bord](.\media\how-to-npm\dashboard.png)

### <a name="circuits"></a>Liste des circuits

Pour afficher une liste de tous les circuits ExpressRoute surveillés, cliquez sur la vignette **Circuits ExpressRoute**. Vous pouvez sélectionner un circuit et afficher son état d’intégrité, des graphiques de tendances pour la perte de paquets, l’utilisation de la bande passante et la latence. Les graphiques sont interactifs. Vous pouvez sélectionner une fenêtre de temps personnalisée pour tracer les graphiques. Vous pouvez faire glisser la souris sur une zone sur le graphique pour zoomer et voir des points de données précis.

  ![circuit_list](.\media\how-to-npm\circuits.png)

#### <a name="trend"></a>Tendances concernant les pertes, la latence et le débit

Les graphiques représentant la bande passante, la latence et la perte sont interactifs. Vous pouvez zoomer sur n’importe quelle section de ces graphiques, à l’aide de contrôles de la souris. Vous pouvez également voir des données de bande passante, de latence et de perte pour d’autres intervalles en cliquant sur l’option **Date/Heure**, située sous le bouton Actions, à gauche.

![tendance](.\media\how-to-npm\16.png)

### <a name="peerings"></a>Liste des homologations

En cliquant sur la vignette **Appairages privés** du tableau de bord, une liste de toutes les connexions à des réseaux virtuels sur l’homologation privée s’affiche. Ici, vous pouvez sélectionner une connexion de réseau virtuel et afficher son état d’intégrité, des graphiques de tendances pour la perte de paquets, l’utilisation de la bande passante et la latence.

  ![liste de circuits](.\media\how-to-npm\peerings.png)

### <a name="topology"></a>Topologie de circuit

Pour afficher la topologie de circuit, cliquez sur la vignette **Topologie**. Vous accédez ainsi à l’affichage de la topologie du circuit ou de l’homologation. Le diagramme de topologie fournit la latence pour chaque segment sur le réseau, et chaque tronçon de couche 3 est représenté par un nœud du diagramme. En cliquant sur un tronçon, vous pouvez obtenir plus de détails sur le tronçon.
Vous pouvez augmenter le niveau de visibilité pour inclure des sauts locaux en déplaçant le curseur sous **Filtres**. Déplacez le curseur vers la droite ou gauche pour augmenter/diminuer le nombre de sauts dans le graphique de la topologie. La latence pour chaque segment est visible, ce qui permet une isolation plus rapide des segments à latence élevée sur votre réseau.

![filtres](.\media\how-to-npm\topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Vue détaillée de la topologie d’un circuit

Cette vue affiche les connexions de réseau virtuel.
![topologie détaillée](.\media\how-to-npm\17.png)
