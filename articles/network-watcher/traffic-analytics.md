---
title: Azure Traffic Analytics | Microsoft Docs
description: Découvrez comment analyser les journaux de flux de groupe de sécurité réseau Azure avec Traffic Analytics.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: jdial
ms.openlocfilehash: 9fc44fdd6ce01452ffc2506c599e3d05aa0803e1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="traffic-analytics"></a>Traffic Analytics

Traffic Analytics est une solution cloud qui offre une visibilité de l’activité des utilisateurs et des applications dans vos réseaux cloud. Traffic Analytics analyse les journaux de flux de groupe de sécurité réseau Network Watcher pour fournir des informations sur le flux de trafic dans votre cloud Azure. Avec Traffic Analytics, vous pouvez effectuer les actions suivantes :

- Visualiser l’activité réseau de l’ensemble de vos abonnements Azure et identifier les zones réactives.
- Identifier les menaces de sécurité pour votre réseau et le protéger, avec des informations comme les ports ouverts, les applications qui tentent d’accéder à Internet et les machines virtuelles se connectant à des réseaux non fiables.
- Comprendre les modèles de flux de trafic entre les régions Azure et Internet pour optimiser le déploiement de votre réseau, afin de bénéficier de performances et d’une capacité adéquates.
- Identifier les erreurs de configuration réseau à l’origine d’échecs de connexion dans votre réseau.

## <a name="why-traffic-analytics"></a>Pourquoi Traffic Analytics ?

Il est essentiel de surveiller, gérer et connaître votre propre réseau pour bénéficier de performances, d’une conformité et d’une sécurité optimales. Connaître votre propre environnement est d’une importance capitale si vous souhaitez le protéger et l’optimiser. Vous devez régulièrement connaître l’état actuel du réseau, savoir qui se connecte depuis quel emplacement, quels sont les ports ouverts sur Internet, quel est le comportement réseau attendu et quelles sont les hausses soudaines du trafic.

Les réseaux cloud sont différents des réseaux d’entreprise locaux, où Netflow ou des commutateurs et routeurs dotés d’un protocole équivalent permettent de collecter le trafic réseau IP à mesure qu’il entre dans une interface réseau ou sort de celle-ci. En analysant les données de flux de trafic, vous pouvez créer une analyse du flux de trafic réseau et du volume.

Les réseaux virtuels Azure ont des journaux de flux de groupe de sécurité réseau, qui vous fournissent des informations sur le trafic IP entrant et sortant via un groupe de sécurité réseau associé à chaque interface réseau, machine virtuelle ou sous-réseau. En analysant les journaux de flux de groupe de sécurité réseau bruts et en ajoutant des informations sur la sécurité, la topologie et la géographie, Traffic Analytics peut vous fournir des données sur le flux du trafic dans votre environnement. Traffic Analytics fournit par exemple les informations suivantes : les hôtes qui communiquent le plus, les protocoles d’application qui communiquent le plus, les paires d’hôtes qui communiquent le plus, le trafic autorisé/bloqué, le trafic entrant/sortant, les ports Internet ouverts, les règles qui bloquent le plus, la distribution du trafic par centre de données Azure, le réseau virtuel, les sous-réseaux ou les réseaux non fiables.

## <a name="key-components"></a>Composants clés 

- **Groupe de sécurité réseau** : contient la liste des règles de sécurité qui autorisent ou rejettent le trafic réseau vers les ressources connectées à un réseau virtuel Azure. Les NSG peuvent être associés à des sous-réseaux, à des machines virtuelles spécifiques (Classic) ou à des interfaces réseau (NIC) individuelles attachées à des machines virtuelles (Resource Manager). Pour plus d’informations, consultez [Sécurité du réseau](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Journaux de flux de groupe de sécurité réseau** : ils vous permettent d’afficher des informations relatives au trafic IP entrant et sortant via un groupe de sécurité réseau. Les journaux de flux de groupe de sécurité réseau sont écrits au format json et affichent les flux entrants et sortants en fonction de la règle, la carte réseau à laquelle le flux s’applique, des informations à 5 tuples sur le flux (adresse IP source/de destination, port source/de destination, protocole), ainsi que l’autorisation ou le refus du trafic. Pour plus d’informations sur les journaux de flux de groupe de sécurité réseau, consultez [Présentation de la journalisation des flux pour les groupes de sécurité réseau](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics** : service Azure qui collecte les données de surveillance et stocke les données dans un référentiel central. Ces données peuvent comprendre des événements, des données de performances ou des données personnalisées fournies par le biais de l’API Azure. Une fois collectées, les données sont disponibles pour les fonctions de génération d’alertes, d’analyse et d’exportation. Les applications de surveillance telles que Network Performance Monitor et Traffic Analytics sont conçues avec Log Analytics comme base. Pour plus d’informations, consultez [Présentation de Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Espace de travail Log Analytics** : une instance de Log Analytics où les données appartenant à un compte Azure sont stockées. Pour plus d’informations sur les espaces de travail Log Analytics, consultez [Créer un espace de travail Log Analytics dans le portail Azure](../log-analytics/log-analytics-quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Network Watcher** : un service régional qui vous permet de surveiller et de diagnostiquer l’état au niveau d’un scénario réseau dans Azure. Vous pouvez activer et désactiver les journaux de flux de groupe de sécurité réseau avec Network Watcher. Pour plus d’informations, consultez [Network Watcher](network-watcher-monitoring-overview.md#network-watcher).

## <a name="how-traffic-analytics-works"></a>Fonctionnement de Traffic Analytics 

Traffic Analytics examine les journaux de flux de groupe de sécurité réseau bruts et capture les journaux réduits en agrégeant les flux communs entre l’adresse IP source, l’adresse IP de destination, le port de destination et le protocole similaires. Par exemple, l’hôte 1 (adresse IP : 10.10.10.10) communique avec l’hôte 2 (adresse IP : 10.10.20.10), 100 fois sur une période de 1 heure à l’aide d’un port (par exemple, 80) et d’un protocole (par exemple, http). Le journal réduit comporte une entrée, indiquant que les hôtes 1 et 2 ont communiqué 100 fois sur une période de 1 heure à l’aide du port *80* et du protocole *HTTP*, plutôt que de présenter 100 entrées. Les informations sur la topologie, la sécurité et la géographie viennent compléter les journaux réduits. Ils sont ensuite stockés dans l’espace de travail Log Analytics. L’illustration suivante montre le flux de données :

![Flux de données pour le traitement des journaux de flux de groupe de sécurité réseau](media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>Régions prises en charge

Traffic Analytics est disponible en préversion. Les fonctionnalités de la préversion n’offrent pas le même niveau de disponibilité et de fiabilité que les fonctionnalités de la version générale.  Dans la préversion, vous pouvez utiliser Traffic Analytics pour les groupes de sécurité réseau dans les régions suivantes : États-Unis Centre-Ouest, Est des États-Unis, Est des États-Unis 2, Nord-Centre des États-Unis, Sud-Centre des États-Unis, Centre des États-Unis, Ouest des États-Unis, Ouest des États-Unis 2, Europe de l’Ouest, Europe du Nord, Royaume-Uni Ouest, Royaume-Uni Sud, Est de l’Australie et Sud-Est de l’Australie. L’espace de travail Log Analytics doit se trouver dans la région États-Unis Centre-Ouest, Est des États-Unis, Europe de l’Ouest, Sud-Est de l’Australie ou Royaume-Uni Sud.

## <a name="prerequisites"></a>Prérequis


### <a name="enable-network-watcher"></a>Activer Network Watcher 

Pour analyser le trafic, vous devez disposer d’un service Network Watcher, ou devez [activer un Azure Network Watcher](network-watcher-create.md) dans chaque région hébergeant des groupes de sécurité réseau que vous souhaitez analyser. Traffic Analytics peut être activé pour les groupes de sécurité réseau hébergés dans les [régions prises en charge](#supported-regions).

### <a name="re-register-the-network-resource-provider"></a>Inscrire à nouveau le fournisseur de ressources réseau 

Avant de pouvoir utiliser Traffic Analytics quand la préversion est disponible, vous devez inscrire à nouveau votre fournisseur de ressources réseau. Cliquez sur **Try it** (Essayer) dans la zone de code suivante pour ouvrir Azure Cloud Shell. Cloud Shell vous connecte automatiquement à votre abonnement Azure. Une fois Cloud Shell ouvert, entrez la commande suivante pour inscrire à nouveau le fournisseur de ressources réseau :

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Network"
```

### <a name="select-a-network-security-group"></a>Sélectionner un groupe de sécurité réseau 

Avant d’activer la journalisation de flux de groupe de sécurité réseau, vous devez disposer d’un groupe de sécurité réseau pour lequel journaliser des flux. Si vous n’en avez pas, consultez [Créer des groupes de sécurité réseau à l’aide du portail Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) pour en créer un.

Dans la partie gauche du portail Azure, sélectionnez **Surveiller**, puis **Observateur réseau**, et enfin **Journaux de flux NSG**. Sélectionnez le groupe de sécurité réseau pour lequel vous souhaitez activer un journal de flux de groupe de sécurité réseau, comme indiqué dans l’image suivante :

![Sélection de groupes de sécurité réseau qui nécessitent l’activation du journal de flux de groupe de sécurité réseau](media/traffic-analytics/selection-of-nsgs-that-require- enablement-of-nsg-flow-logging.png)

Si vous essayez d’activer Traffic Analytics pour un groupe de sécurité réseau qui est hébergé dans une région autre que les [régions prises en charge](#supported-regions), une erreur « Introuvable » s’affiche. 

## <a name="enable-flow-log-settings"></a>Activer les paramètres de journal de flux

Avant d’activer les paramètres de journal de flux, vous devez effectuer les tâches suivantes :

Inscrire le fournisseur Azure Insights, s’il n’est pas déjà inscrit dans votre abonnement :

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

Si vous n’avez pas de compte de stockage Azure pour héberger vos journaux de flux de groupe de sécurité réseau, vous devez créer un compte de stockage. Pour ce faire, utilisez la commande qui suit. Avant d’exécuter la commande, remplacez `<replace-with-your-unique-storage-account-name>` par un nom qui n’existe pas dans tous les autres emplacements Azure. Il doit comprendre entre 3 et 24 caractères et uniquement des chiffres et des lettres en minuscules. Vous pouvez également modifier le nom de groupe de ressources, si nécessaire.

```azurepowershell-interactive
New-AzureRmStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Sélectionnez les options suivantes, comme indiqué dans l’image :

1. Sélectionnez *Actif* pour **État**.
2. Sélectionnez un compte de stockage existant dans lequel conserver les journaux de flux. Si vous souhaitez stocker les données indéfiniment, définissez la valeur sur *0*. Des frais de stockage Azure peuvent s’appliquer pour le compte de stockage.
3. Définissez **Rétention** sur le nombre de jours durant lequel vous souhaitez stocker les données.
4. Sélectionnez *Activé* pour **Traffic Analytics Status** (État Traffic Analytics).
5. Sélectionnez un espace de travail Log Analytics (OMS) existant ou cliquez sur **Créer un espace de travail** pour en créer un. Un espace de travail Log Analytics est utilisé par Traffic Analytics pour stocker les données agrégées et indexées qui sont ensuite utilisées pour générer l’analyse. Si vous sélectionnez un espace de travail existant, il doit se trouver dans les [régions prises en charge](#traffic-analytics-supported-regions) et avoir été mis à niveau vers le nouveau langage de requête. Si vous ne souhaitez pas mettre à niveau un espace de travail existant ou si vous ne disposez pas d’un espace de travail dans une région prise en charge, créez-en un. Pour plus d’informations sur les langages de requête, consultez [Mise à niveau Azure Log Analytics avec la nouvelle recherche dans les journaux](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

    L’espace de travail Log Analytics (OMS) qui héberge la solution Traffic Analytics et les groupes de sécurité réseau ne doivent pas nécessairement se trouver dans la même région. Par exemple, Traffic Analytics se trouve peut-être dans un espace de travail de la région Europe de l’Ouest, tandis que vos groupes de sécurité réseau sont hébergés dans les régions Est des États-Unis et Ouest des États-Unis. Plusieurs groupes de sécurité réseau peuvent être configurés dans le même espace de travail.
6. Sélectionnez **Enregistrer**.

    ![Sélection du compte de stockage, de l’espace de travail Log Analytics et activation de Traffic Analytics](media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement.png)

Répétez les étapes précédentes pour les autres groupes de sécurité réseau pour lesquels vous souhaitez activer Traffic Analytics. Les données des journaux de flux étant envoyées à l’espace de travail, assurez-vous que les lois et réglementations locales de votre pays autorisent le stockage de données dans la région où se trouve l’espace de travail.

## <a name="view-traffic-analytics"></a>Afficher Traffic Analytics

Dans la partie gauche du portail, sélectionnez **All services** (Tous les services), puis entrez *Surveiller* dans la zone **Filtrer**. Quand **Surveiller** apparaît dans les résultats de la recherche, sélectionnez cette option. Pour commencer à découvrir Traffic Analytics et ses fonctionnalités, sélectionnez **Observateur réseau**, puis **Analytique du trafic (préversion)**.

![Accès au tableau de bord de Traffic Analytics](media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

La première fois, le tableau de bord peut s’afficher au bout de 30 minutes maximum, car Traffic Analytics doit d’abord agréger suffisamment de données pour générer des informations utiles, avant de pouvoir créer des rapports.

## <a name="usage-scenarios"></a>Scénarios d’utilisation

Certaines des informations que vous souhaitez obtenir une fois Traffic Analytics entièrement configuré sont les suivantes :

### <a name="find-traffic-hotspots"></a>Rechercher les zones réactives du trafic

**Rechercher**

- Quels sont les hôtes qui envoient ou reçoivent le plus de trafic ?
    - Déterminer qui sont les hôtes qui envoient ou reçoivent le plus de trafic peut vous aider à identifier les hôtes qui traitent la plupart du trafic.
    - Vous pouvez évaluer si le volume du trafic est approprié pour un hôte. Le volume du trafic traduit-il un comportement normal ou implique-t-il le besoin de mener des recherches plus approfondies ?
- Quelle est la quantité de trafic entrant/sortant ?
    -   Est-il prévu que l’hôte reçoive davantage de trafic entrant que sortant, ou vice versa ?
- Statistiques du trafic autorisé/bloqué.
    - Pourquoi un hôte autorise-t-il ou bloque-t-il un volume important de trafic ?

    Sélectionnez **Plus de détails**, sous **Hosts with most traffic** (Hôtes avec le plus de trafic), comme illustré dans l’image suivante :

    ![Tableau de bord présentant les détails de l’hôte avec le plus de trafic](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- L’illustration suivante montre les tendances dans le temps pour les cinq principaux hôtes qui communiquent le plus et les détails associés aux flux (autorisé - entrant/sortant et refusé - entrant/sortant) pour une machine virtuelle :

    ![Tendance des cinq principaux hôtes qui communiquent le plus](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Rechercher**

- Quelles sont les paires d’hôtes qui communiquent le plus ?
    - Comportement attendu comme la communication frontend-backend ou comportement anormal comme le trafic backend-Internet.
- Statistiques du trafic autorisé/bloqué
    - Pourquoi un hôte autorise-t-il ou bloque-t-il un volume de trafic important
- Protocole d’application le plus fréquemment utilisé entre les paires d’hôtes qui communiquent le plus :
    - Ces applications sont-elles autorisées sur ce réseau ?
    - Les applications sont-elles configurées correctement ? Utilisent-elles le protocole approprié pour la communication ? Sélectionnez **Plus de détails** sous **Most frequent conversations** (Conversations les plus fréquentes), comme indiqué dans l’image suivante :

        ![Tableau de bord présentant la conversation la plus fréquente](media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- L’illustration suivante montre les tendances dans le temps pour les cinq principales conversations et les détails associés aux flux comme les flux autorisés et refusés entrant et sortant pour une paire de conversation :

    ![Tendance et détails des cinq principales conversations les plus fréquentes](media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Rechercher**

- Quel est le protocole d’application le plus utilisé dans votre environnement, et quelles paires d’hôtes qui communiquent utilisent le plus le protocole d’application ?
    - Ces applications sont-elles autorisées sur ce réseau ?
    - Les applications sont-elles configurées correctement ? Utilisent-elles le protocole approprié pour la communication ? Le comportement attendu est des ports courants, tels que 80 et 443. Pour la communication standard, si des ports inhabituels sont affichés, une modification de la configuration peut être nécessaire. Sélectionnez **Plus de détails** sous **Top application protocols** (Principaux protocoles d’application), dans l’image suivante :

        ![Tableau de bord présentant les principaux protocoles d’application](media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Les images suivantes montrent la tendance dans le temps des cinq principaux protocoles L7 et les détails associés aux flux (par exemple, flux autorisés et refusés) pour un protocole L7 :

    ![Tendance et détails des cinq principaux protocoles de couche 7](media/traffic-analytics/top five-layer-seven-protocols-details-and-trend.png)

    ![Détails du flux pour un protocole d’application dans Recherche dans les journaux](media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Rechercher**

- Tendances d’utilisation de la capacité d’une passerelle VPN dans votre environnement.
    - Chaque référence SKU de VPN autorise une certaine quantité de bande passante. Les passerelles VPN sont-elles sous-exploitées ?
    - Vos passerelles atteignent-elles la capacité définie ? Devez-vous mettre à niveau vers la référence SKU supérieure suivante ?
- Quels sont les hôtes qui communiquent le plus, via quelle passerelle VPN, sur quel port ?
    - Ce modèle est-il normal ? Sélectionnez **Plus de détails**, sous **Top active VPN connections** (Principales connexions VPN actives), comme illustré dans l’image suivante :

        ![Tableau de bord présentant les principales connexions VPN actives](media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- L’illustration suivante montre les tendances dans le temps de l’utilisation de la capacité d’une passerelle VPN Azure et les détails associés aux flux (par exemple, les ports et flux autorisés) :

    ![Détails de flux et tendance d’utilisation de la passerelle VPN](media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Visualiser la distribution du trafic par zone géographique

**Rechercher**

- Distribution du trafic par centre de données comme les principales sources de trafic vers un centre de données, les principaux réseaux non fiables communiquant avec le centre de données et les principaux protocoles d’application qui communiquent.
    - Si vous remarquez plus de charge sur un centre de données, vous pouvez planifier une distribution du trafic efficace.
    - Si des réseaux non fiables communiquent dans le centre de données, corrigez les règles de groupe de sécurité réseau pour les bloquer.

    Sélectionnez **Cliquer ici pour voir la carte géographique dynamique** sous **Traffic distribution across Azure data centers** (Distribution du trafic entre les centres de données Azure), comme illustré dans l’image suivante:

  ![Tableau de bord présentant la distribution du trafic](media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- La carte géographique affiche le ruban supérieur pour sélectionner les paramètres tels que les centres de données (Déployé/Aucun déploiement/Actif/Inactif/Traffic Analytics Enabled (Traffic Analytics activé)/Traffic Analytics Not Enabled(Traffic Analytics désactivé)) et les pays qui contribuent au trafic Inoffensif/Malveillant vers le déploiement actif :

    ![Carte géographique présentant le déploiement actif](media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- La carte géographique montre la distribution du trafic vers un centre de données à partir de pays et continents qui communiquent avec lui, représenté par des lignes bleues (trafic inoffensif) et des lignes rouges (trafic malveillant) :

    ![Vue de la carte géographique présentant la distribution du trafic vers les pays et continents](media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Détails du flux pour la distribution du trafic dans la recherche dans les journaux](media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Visualiser la distribution du trafic par réseaux virtuels

**Rechercher**

- Distribution du trafic par réseau virtuel, topologie, principales sources de trafic vers le réseau virtuel, principaux réseaux non fiables qui communiquent avec le réseau virtuel, principaux protocoles d’application qui communiquent.
    - Savoir quel réseau virtuel communique avec quel réseau virtuel. Si la conversation n’est pas attendue, cela peut être corrigé.
    - Si des réseaux non fiables communiquent avec un réseau virtuel, vous pouvez corriger les règles de groupe de sécurité réseau pour bloquer les réseaux non fiables.
 
    Sélectionnez **Click here to see VNet traffic topology** (Cliquer ici pour voir la topologie du trafic de réseau virtuel) sous **Virtual network distribution** (Distribution de réseau virtuel), comme illustré dans l’image suivante : 

    ![Tableau de bord présentant la distribution de réseau virtuel](media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- La topologie du réseau virtuel affiche le ruban supérieur pour sélectionner les paramètres comme les flux malveillants, les flux actifs, les connexions externes (connexions du réseau virtuel internes/actives/inactives) d’un réseau virtuel.
- La topologie du réseau virtuel montre la distribution du trafic sur un réseau virtuel en ce qui concerne les flux (autorisés/bloqués/entrants/sortants/inoffensifs/malveillants), le protocole d’application et les groupes de sécurité réseau, par exemple :

    ![Topologie du réseau virtuel présentant la distribution du trafic et détails du flux](media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)

    ![Détails du flux pour la distribution du trafic de réseau virtuel dans la recherche dans les journaux](media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Rechercher**

- Distribution du trafic par sous-réseau, topologie, principales sources de trafic vers le sous-réseau, principaux réseaux non fiables qui communiquent avec le sous-réseau et principaux protocoles d’application qui communiquent.
    - Savoir quel sous-réseau communique avec quel sous-réseau. Si vous remarquez des conversations inattendues, vous pouvez corriger votre configuration.
    - Si des réseaux non fiables communiquent avec un sous-réseau, vous pouvez résoudre ce problème en configurant des règles de groupe de sécurité réseau pour bloquer les réseaux non fiables, par exemple.
- La topologie des sous-réseaux affiche le ruban supérieur pour sélectionner les paramètres, tels que le sous-réseau actif/inactif, les connexions externes, les flux actifs et les flux malveillants du sous-réseau.
- La topologie du sous-réseau montre la distribution du trafic sur un réseau virtuel en ce qui concerne les flux (autorisés/bloqués/entrants/sortants/inoffensifs/malveillants), le protocole d’application et les groupes de sécurité réseau, par exemple :

    ![Topologie de sous-réseau présentant la distribution du trafic sur un sous-réseau de réseau virtuel en ce qui concerne les flux](media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Afficher les ports et les machines virtuelles recevant le trafic d’Internet

**Rechercher**

- Quels sont les ports ouverts qui communiquent sur Internet ?
    - Si des ports inattendus sont trouvés ouverts, vous pouvez corriger votre configuration :

        ![Tableau de bord présentant les ports recevant et envoyant du trafic sur Internet](media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

        ![Détails des hôtes et ports de destination Azure](media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Rechercher**

Avez-vous détecté la présence de trafic malveillant dans votre environnement ? D’où provient-il ? Où va-t-il ?

![Détails des flux de trafic malveillant dans la recherche dans les journaux](media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsg-rule-hits"></a>Visualiser les tendances dans les correspondances de règle de groupe de sécurité réseau

**Rechercher**

- Quel groupe de sécurité réseau/quelle règle de sécurité réseau a le plus de correspondances ?
- Quelles sont les principales paires de conversation source et destination par groupe de sécurité réseau ?

    ![Tableau de bord présentant les statistiques de correspondances de groupe de sécurité réseau](media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Les illustrations suivantes montrent les tendances dans le temps des correspondances des règles de groupe de sécurité réseau et les détails du flux source-destination d’un groupe de sécurité réseau :

    ![Présentation des tendances dans le temps des correspondances de règle de groupe de sécurité réseau et principales règles de groupe de sécurité réseau](media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Détails des statistiques des principales règles de groupe de sécurité réseau](media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

Pour obtenir des réponses aux questions fréquemment posées, consultez [Traffic Analytics frequently asked questions](traffic-analytics-faq.md) (Forum aux questions Traffic Analytics).