---
title: "Présentation de la solution d’usine connectée - Azure | Microsoft Docs"
description: "Description de la solution préconfigurée d’usine connectée Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: bd68859e3837f7e5adbe911518631cb7abc2c2ce
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="get-started-with-the-connected-factory-preconfigured-solution"></a>Prise en main de la solution préconfigurée d’usine connectée

Les [solutions préconfigurées][lnk-preconfigured-solutions] d’Azure IoT Suite regroupent plusieurs services Azure IoT pour offrir des solutions de bout en bout permettant d’implémenter des scénarios IoT d’entreprise. La solution préconfigurée d’*usine connectée* se connecte et surveille vos appareils industriels. Vous pouvez utiliser la solution pour analyser le flux de données à partir de vos appareils et pour encourager la productivité et la rentabilité opérationnelle.

Ce didacticiel montre comment configurer la solution préconfigurée d’usine connectée. Il présente également les fonctionnalités de base de la solution préconfigurée. Vous pouvez accéder à la plupart de ces fonctionnalités à partir du *tableau de bord* de solution déployé avec la solution préconfigurée :

![Tableau de bord de solution préconfigurée d’usine connectée][img-cf-home]

Pour suivre ce didacticiel, vous avez besoin d’un compte Azure actif.

> [!NOTE]
> Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la rubrique [Version d’évaluation gratuite d’Azure][lnk_free_trial].

## <a name="provision-the-solution"></a>Déployer la solution

1. Connectez-vous à azureiotsuite.com à l’aide des informations d’identification de votre compte Azure, puis cliquez sur « **+** » pour créer une solution.
2. Cliquez sur **Sélectionner** sur la vignette **Usine connectée**.
3. Saisissez un **Nom de solution** pour votre solution préconfigurée d’usine connectée.
4. Sélectionnez l’**Abonnement** et la **Région** à utiliser pour configurer la solution.
5. Cliquez sur **Créer une solution** pour commencer le processus de déploiement. L’exécution de ce processus prend généralement plusieurs minutes.

### <a name="while-you-wait-for-the-provisioning-process-to-complete"></a>En attendant la fin du processus d’approvisionnement

1. Cliquez sur la vignette de votre solution présentant l’état **Approvisionnement** .
2. Notez les **états d’approvisionnement** à mesure que les services Azure sont déployés dans votre abonnement Azure.
3. Une fois l’approvisionnement terminé, l’état prend la valeur **Prêt**.
4. Cliquez sur la vignette pour visualiser les détails de votre solution dans le volet droit.

> [!NOTE]
> Si vous rencontrez des problèmes lors du déploiement de la solution préconfigurée, consultez les articles [Autorisations sur le site azureiotsuite.com][lnk-permissions] et le [FAQ sur l’usine connectée](iot-suite-faq-cf.md). Si les problèmes persistent, créez un ticket de service sur le [Portail][lnk-portal].

Certains détails de votre solution semblent-ils faire défaut ? Soumettez vos suggestions concernant les fonctionnalités sur [UserVoice](https://feedback.azure.com/forums/321918-azure-iot).

## <a name="scenario-overview"></a>Présentation du scénario

Lorsque vous déployez la solution préconfigurée d’usine connectée, elle est préremplie avec les ressources qui vous permettent de parcourir un scénario industriel courant. Dans ce scénario, plusieurs usines connectées à la solution rapportent les valeurs de données nécessaires pour calculer l’efficacité globale de l’équipement (OEE) et les indicateurs de performance clés (KPI). Les sections suivantes vous montrent comment :

* Surveiller des valeurs d’usine, de lignes de production, OEE de poste et KPI
* Analyser les données de télémétrie générées à partir de ces appareils à l’aide d’Azure Time Series Insights
* Agir sur les alarmes pour résoudre les problèmes

Une fonctionnalité clé de ce scénario est que vous pouvez effectuer toutes ces actions à distance à partir du tableau de bord de solution. Vous n’avez pas besoin d’un accès physique aux appareils.

## <a name="view-the-solution-dashboard"></a>Afficher le tableau de bord de solution

Grâce au tableau de bord de solution, vous pouvez gérer la solution déployée. Il s’agit d’une représentation hiérarchique d’une configuration d’usine globale. Par exemple, vous pouvez afficher l’OEE et les KPI, publier de nouveaux nœuds pour la télémétrie et les alarmes d’action.

1. Une fois que l’approvisionnement est terminé et que la vignette de votre solution préconfigurée indique **Prêt**, choisissez **Lancer** pour ouvrir le portail de votre solution d’usine connectée dans un nouvel onglet.

    ![Lancer la solution préconfigurée][img-launch-solution]

1. Par défaut, le portail de solution affiche le *tableau de bord*. Utilisez le menu sur le côté gauche de la page pour accéder à d’autres zones du portail.

    ![Tableau de bord de solution préconfigurée d’usine connectée][cf-img-menu]

Le tableau de bord affiche les informations suivantes :

* Un panneau **Emplacements d’usine** qui indique l’état, l’emplacement et la configuration de production actuelle dans la solution. Lors de la première exécution de la solution, il existe un nombre d’appareils simulés. La simulation de la ligne de production se compose de trois serveurs OPC UA réels par ligne de production qui effectuent des tâches simulées et partagent des données. Pour plus d’informations sur OPC UA, consultez les [questions fréquentes (FAQ) sur l’usine connectée](iot-suite-faq-cf.md).
* Une **carte** qui affiche l’emplacement de chaque appareil connecté à la solution. La solution peut utiliser l’API Bing Maps pour tracer les informations sur la carte. Si votre abonnement est activé pour l’API Bing Maps Enterprise, cette fonctionnalité est automatiquement utilisée. Sinon, consultez le [FAQ][lnk-faq] pour savoir comment rendre le mappage dynamique.
* Un panneau **Alarmes** qui affiche les alarmes générées lorsqu’une valeur de télémétrie ou d’OEE/KPI dépasse un seuil spécifique.
* Un panneau **Efficacité globale de l’équipement** qui affiche les valeurs OEE pour l’ensemble de l’entreprise, ou l’usine/la ligne de production/le poste que vous visualisez. Cette valeur est agrégée à partir de la vue du poste au niveau de l’entreprise. La données d’OEE et ses éléments constitutifs peuvent être analysés de manière plus approfondie.
* Le panneau **Indicateurs de performance clés** qui affiche le nombre d’unités produites et l’énergie utilisée par l’ensemble de l’entreprise ou l’usine/la ligne de fabrication/le poste que vous visualisez. Ces valeurs est agrégées à partir d’une vue du poste au niveau de l’entreprise.

## <a name="view-factories"></a>Afficher les usines

Le panneau *Emplacements d’usine* vous présente l’emplacement géographique de toutes les usines dans la solution, leur état et la configuration de production actuelle. À partir de la liste des emplacements, vous pouvez accéder aux autres niveaux de la hiérarchie de la solution. Les lignes de la liste sont des liens hypertexte vers des détails des lignes de production à cet emplacement. Il est ensuite possible d’accéder aux détails de la ligne de production et à la vue du poste. Vous pouvez également appliquer un filtre à la liste.

![Usines de la solution préconfigurée d’usine connectée][cf-img-factories]

1. Le **panneau Usines** affiche la liste des usines pour cette solution.

2. La liste des usines montre au départ six usines créées par le processus d’approvisionnement. Vous pouvez ajouter des appareils physiques et simulés supplémentaires à la solution.

3. Pour afficher les détails d’une usine, cliquez sur la ligne dans la liste d’usines.

4. Pour afficher les détails d’une ligne de production, cliquez sur la ligne dans la liste.

5. Pour afficher les nœuds OPC UA publiés d’un poste de la ligne de production, cliquez sur la ligne dans la liste.

6. Pour afficher des détails sur un nœud spécifique du poste, cliquez sur la ligne dans la liste. Cette action lance le panneau de contexte avec des visualisations Time Series Insights. Cliquez sur ces graphiques pour approfondir l’analyse dans l’environnement de l’explorateur Time Series Insights.

## <a name="view-map"></a>Afficher la carte

Si votre abonnement a accès à l’API Bing Maps, la carte *Usines* vous présente l’emplacement géographique et l’état de toutes les usines de la solution. Cliquez sur les emplacements indiqués sur la carte pour accéder aux détails de l’emplacement.

![Carte de la solution préconfigurée d’usine connectée][cf-img-map]

## <a name="view-alarms"></a>Afficher les alarmes

Le panneau **Alarmes** vous présente les alarmes générées en raison d’une valeur rapportée ou d’une valeur d’OEE/KPI calculée dépassant son seuil configuré. Ce panneau affiche les alarmes à chaque niveau de la hiérarchie, de la vue du niveau du poste à la vue globale. Les alarmes contiennent une description de l’alarme, la date, l’heure, l’emplacement et le nombre d’occurrences. Vous pouvez obtenir un aperçu des données à l’origine de l’alarme à l’aide des données Time Series Insights. Les données Time Series Insights sont affichées dans les alarmes, le cas échéant. Si vous êtes un administrateur, vous pouvez effectuer des actions par défaut sur les alarmes telles que :

* Fermez l’alarme.
* Accusez réception de l’alarme.

Vous pouvez facultativement effectuer d’autres actions plus complexes. Par exemple, pour le nœud OPC UA Pressure de l’Assembly, vous pouvez :

* Affichez des informations de support dans une page web dans une nouvelle fenêtre de navigateur.
* Limiter la cause de l’alarme en appelant une méthode OPC UA sur l’appareil.
* Supprimer la disponibilité des actions par défaut.

    ![Alarmes de la solution préconfigurée d’usine connectée][cf-img-alerts]

> [!NOTE]
> Ces alarmes sont générées par des règles qui sont spécifiées dans un fichier de configuration de la solution préconfigurée. Ces règles peuvent générer des alarmes lorsque les données d’OEE ou KPI ou les valeurs de nœud OPC UA dépassent leur seuil configuré.

1. Le **panneau Alarmes** affiche les alarmes générées dans cette solution.

2. Pour afficher les détails d’une alarme, cliquez sur l’alarme dans le panneau des alarmes.

3. Pour approfondir l’analyse des données d’alarme, cliquez sur le graphique dans le panneau des alarmes pour ouvrir l’environnement de l’explorateur Time Series Insights.

4. Pour résoudre l’alarme, plusieurs actions sont disponibles dans le panneau des alarmes. Choisissez l’option appropriée, puis cliquez sur le bouton de commande d’exécution de l’action.

## <a name="view-overall-equipment-efficiency"></a>Afficher l’efficacité globale de l’équipement

L’OEE évalue l’efficacité du processus de fabrication à l’aide de paramètres opérationnels liés à la production clés. L’OEE est une mesure standard du secteur calculée en multipliant le taux de disponibilité, le taux de performance et le taux de qualité : OEE = disponibilité x performance x qualité.

![OEE de la solution préconfigurée d’usine connectée][cf-img-oee]

1. Pour afficher l’OEE d’un quelconque niveau de la hiérarchie, accédez à la vue spécifique souhaitée. L’OEE de cette vue s’affiche dans le panneau avec chacun des éléments qui composent le pourcentage d’OEE.

2. Pour approfondir l’analyse de l’OEE d’un quelconque niveau de données de la hiérarchie, cliquez sur le pourcentage d’OEE, de disponibilité, de performance ou de qualité. Un panneau de contexte s’affiche avec des visualisations Time Series Insights qui présente des données de la dernière heure, des 24 dernières heures et des 7 derniers jours.

    ![Visualisation TSI de la solution préconfigurée d’usine connectée][cf-img-tsi-visualization]

3. Pour approfondir l’analyse des données d’alarme, cliquez sur le graphique dans le panneau des alarmes. Cette action ouvre l’environnement de l’explorateur Time Series Insights.

    ![Explorateur TSI de la solution préconfigurée d’usine connectée][cf-img-tsi-explorer]

## <a name="view-key-performance-indicators"></a>Afficher les indicateurs de performance clés

La solution fournit deux indicateurs de performance clés : *unités par heure* et *énergie utilisée en kWh*.

![KPI de la solution préconfigurée d’usine connectée][cf-img-kpi]

1. Pour afficher les unités par heure ou l’énergie utilisée d’un quelconque niveau de la hiérarchie, accédez à la vue spécifique souhaitée. Les unités par heure et l’énergie utilisée s’affichent dans le panneau.

2. Pour approfondir l’analyse des unités par heure ou de l’énergie utilisée d’un quelconque niveau des données de la hiérarchie, cliquez sur la jauge dans le panneau **Indicateurs de performance clés**. Un panneau de contexte s’affiche avec des visualisations Time Series Insights vous permettant de consulter les données de la dernière heure, des 24 dernières heures et des 7 derniers jours.

## <a name="scenario-review"></a>Analyse du scénario

Dans ce scénario, vous avez surveillé les valeurs OEE et KPI de vos usines dans le tableau de bord. Vous avez ensuite utilisé Time Series Insights pour fournir plus d’informations afin d’approfondir les données de télémétrie OEE et KPI pour pouvoir identifier des anomalies. Vous avez également utilisé le panneau des alarmes pour consulter les problèmes liés à vos usines et vous avez utilisé les actions disponibles pour résoudre l’alarme.

## <a name="other-features"></a>Autres fonctionnalités

Les sections suivantes décrivent quelques fonctionnalités supplémentaires de la solution d’usine connectée qui ne sont pas décrites dans le scénario précédent.

## <a name="apply-filters"></a>Appliquer des filtres

1. Cliquez sur le **Entonnoir** pour afficher la liste des filtres disponibles dans le panneau des emplacements d’usine ou le panneau des alarmes.

2. Le panneau des filtres s’affiche.

    ![Filtres de la solution préconfigurée d’usine connectée][cf-img-alert-filter]

3. Choisissez le filtre nécessaire. Il est également possible d’entrer du texte libre dans les champs du filtre.

4. Le filtre est ensuite appliqué pour vous. L’état du filtre apparaît également dans le tableau de bord sous la forme d’un entonnoir affiché dans les tables d’usines et d’alarmes.

    ![Filtres de la solution préconfigurée d’usine connectée][cf-img-alert-filter-funnel]

    > [!NOTE]
    > Un filtre actif n’affecte pas les valeurs d’OEE et KPI affichées. Il filtre uniquement le contenu de la liste.

5. Pour effacer un filtre, cliquez sur l’entonnoir, puis cliquez sur le filtre dans le panneau de contexte de filtre. Le texte **Tout** s’affiche dans les tables d’usines et d’alarmes.

## <a name="browse-an-opc-ua-server"></a>Parcourir un serveur OPC UA

Lorsque vous déployez la solution préconfigurée, vous configurez automatiquement des serveurs OPC UA simulés auxquels vous pouvez accéder via l’explorateur de solutions. Ces serveurs sont des *serveurs OPC UA simulés*. Les serveurs simulés vous permettent d’expérimenter plus facilement la solution préconfigurée sans avoir à déployer des serveurs physiques réels. Si vous ne souhaitez pas connecter un serveur OPC UA réel à la solution, consultez le didacticiel [Connexion de votre appareil OPC UA à la solution préconfigurée d’usine connectée][lnk-connect-cf].

1. Cliquez sur l’**icône de navigateur** dans la barre de navigation du tableau de bord.

    ![Explorateur de serveur de la solution préconfigurée d’usine connectée][cf-img-server-browser]

2. Choisissez un des serveurs dans la liste prédéfinie. Cette liste affiche les serveurs déployés pour vous dans la solution préconfigurée.

    ![Sélection de serveur de la solution préconfigurée d’usine connectée][cf-img-server-choice]

3. Cliquez sur **Connecter**. Une boîte de dialogue de sécurité s’affiche. Pour la simulation, il est plus sûr de cliquer sur **Continuer**

4. Cliquez sur un des nœuds dans l’arborescence de serveur pour le développer. Les nœuds qui publient des données de télémétrie sont indiqués par une coche.

    ![Arborescence de serveur de la solution préconfigurée d’usine connectée][cf-img-server-tree]

5. Cliquez avec le bouton droit sur un élément pour lire, écrire, publier ou appeler ce nœud. Les actions disponibles dépendent de vos autorisations et des attributs du nœud. L’option de lecture affiche un panneau de contexte indiquant la valeur du nœud spécifique. L’option d’écriture affiche un panneau de contexte dans lequel vous pouvez entrer une nouvelle valeur. L’option d’appel affiche un nœud dans lequel vous pouvez entrer les paramètres de l’appel.

## <a name="publish-a-node"></a>Publier un nœud

Lorsque vous parcourez un *serveur OPC UA simulé*, vous pouvez également choisir de publier de nouveaux nœuds. Vous pouvez analyser les données de télémétrie de ces nœuds dans la solution. Ces *serveurs OPC UA simulés* vous permettent d’expérimenter plus facilement la solution préconfigurée sans déployer des appareils physiques réels.

1. Accédez à un nœud de l’arborescence du navigateur de serveur OPC UA que vous souhaitez publier.

2. Cliquez avec le bouton droit sur le nœud.

3. Choisissez **Publier**.

    ![L’usine connectée publie le nœud][cf-img-publish-node]

4. Un panneau de contexte vous indiquant que la publication a réussi s’affiche. Le nœud s’affiche dans la vue du niveau poste avec une coche.

    ![Réussite de la publication préconfigurée d’usine connectée][cf-img-publish-success]

## <a name="command-and-control"></a>Commande et contrôle

L’usine connectée vous permet de commander et de contrôler vos appareils industriels directement à partir du cloud. Vous pouvez utiliser cette fonctionnalité pour répondre aux alarmes générées par l’appareil. Par exemple, vous pouvez envoyer une commande à l’appareil à partir du cloud. Vous pouvez rechercher les commandes disponibles dans le nœud **StationCommands** dans l’arborescence du navigateur de serveurs OPC UA. Dans ce scénario, vous ouvrez une soupape de décompression sur le poste d’assembly d’une ligne de production à Munich. Pour utiliser la fonctionnalité de commande et de contrôle, vous devez disposer du rôle **Administrateur** pour le déploiement de la solution préconfigurée.

1. Accédez au nœud **StationCommands** dans l’arborescence du navigateur de serveur OPC UA.

2. Choisissez la commande que vous souhaitez utiliser.

3. Cliquez avec le bouton droit sur le nœud.

4. Choisissez **Appel**.

    ![Commande d’appel de la solution préconfigurée d’usine connectée][cf-img-call-command]

5. Un panneau de contexte vous informant sur la méthode que vous allez appeler et sur les détails de paramètre applicables s’affiche.

6. Choisissez **Appel**.

    ![Contexte d’appel de la solution préconfigurée d’usine connectée][cf-img-call-context]

7. Le panneau de contexte est mis à jour pour vous informer que l’appel de méthode a réussi. Vous pouvez vérifier que l’appel a réussi en lisant la valeur du nœud de pression mise à jour suite à l’appel.

    ![Réussite de l’appel de la solution préconfigurée d’usine connectée][cf-img-call-success]

## <a name="behind-the-scenes"></a>Dans les coulisses

Lorsque vous déployez une solution préconfigurée, le processus de déploiement crée plusieurs ressources dans l’abonnement Azure sélectionné. Vous pouvez afficher ces ressources dans le [portail][lnk-portal] Azure. Le processus de déploiement crée un **groupe de ressources** avec un nom basé sur celui que vous avez choisi pour votre solution préconfigurée :

![Solution préconfigurée dans le portail Azure][img-cf-portal]

Vous pouvez afficher les paramètres de chaque ressource en la sélectionnant dans la liste des ressources dans le groupe de ressources.

Vous pouvez également afficher le code source pour la solution préconfigurée. Le code source de la solution préconfigurée d’usine connectée se trouve dans le référentiel GitHub [azure-iot-connected-factory][lnk-cfgithub] :

Lorsque vous avez terminé, vous pouvez supprimer la solution préconfigurée à partir de votre abonnement Azure sur le site [azureiotsuite.com][lnk-azureiotsuite]. Ce site vous permet de supprimer facilement toutes les ressources qui ont été configurées lors de la création de la solution préconfigurée.

> [!NOTE]
> Pour vous assurer que vous supprimez tout ce qui concerne la solution préconfigurée, supprimez cette dernière sur le site [azureiotsuite.com][lnk-azureiotsuite]. Ne supprimez pas le groupe de ressources dans le portail.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez déployé une solution préconfigurée opérationnelle, vous pouvez poursuivre la prise en main d’IoT Suite en lisant les articles suivants :

* [Procédure pas à pas de la solution préconfigurée d’usine connectée][lnk-rm-walkthrough]
* [Connexion de votre appareil à la solution préconfigurée d’usine connectée][lnk-connect-cf]
* [Autorisations sur le site azureiotsuite.com][lnk-permissions]

[img-cf-home]:media/iot-suite-connected-factory-overview/cf-dashboard.png
[img-launch-solution]: media/iot-suite-connected-factory-overview/launch-cf.png
[cf-img-menu]: media/iot-suite-connected-factory-overview/cf-dashboard-menu.png
[cf-img-factories]:media/iot-suite-connected-factory-overview/cf-dashboard-factory.png
[cf-img-map]:media/iot-suite-connected-factory-overview/cf-dashboard-map.png
[cf-img-alerts]:media/iot-suite-connected-factory-overview/cf-dashboard-alerts.png
[cf-img-oee]:media/iot-suite-connected-factory-overview/cf-dashboard-oee.png
[cf-img-kpi]:media/iot-suite-connected-factory-overview/cf-dashboard-kpi.png
[cf-img-tsi-visualization]:media/iot-suite-connected-factory-overview/cf-dashboard-tsi.png
[cf-img-tsi-explorer]:media/iot-suite-connected-factory-overview/tsi-explorer.png
[cf-img-server-browser]: media/iot-suite-connected-factory-overview/cf-dashboard-browser.png
[cf-img-server-choice]: media/iot-suite-connected-factory-overview/cf-select-server.png
[cf-img-server-tree]:media/iot-suite-connected-factory-overview/cf-server-tree.png
[cf-img-publish-node]:media/iot-suite-connected-factory-overview/cf-publish-node1.png
[cf-img-publish-success]:media/iot-suite-connected-factory-overview/cf-publish-success.png
[cf-img-call-command]:media/iot-suite-connected-factory-overview/cf-command-and-control-call.png
[cf-img-call-context]:media/iot-suite-connected-factory-overview/cf-command-and-control-call-button.png
[cf-img-call-success]:media/iot-suite-connected-factory-overview/cf-command-and-control-succeed.png
[img-cf-portal]:media/iot-suite-connected-factory-overview/cf-resource-group.png
[cf-img-alert-filter]:media/iot-suite-connected-factory-overview/cf-filter.png
[cf-img-alert-filter-funnel]:media/iot-suite-connected-factory-overview/cf-filter-funnel.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-portal]: http://portal.azure.com/
[lnk-cfgithub]: https://github.com/Azure/azure-iot-connected-factory
[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-faq]: iot-suite-v1-faq.md