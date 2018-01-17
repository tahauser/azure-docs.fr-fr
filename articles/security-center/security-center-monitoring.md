---
title: "Surveillance de la sécurité dans Azure Security Center | Microsoft Docs"
description: "Cet article a pour but de vous aider à vous familiariser avec les fonctionnalités de surveillance d’Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.author: yurid
ms.openlocfilehash: 04f2dd3dcaa44a243cb9620ab8192e15aae82a01
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="security-health-monitoring-in-azure-security-center"></a>Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure
Cet article a pour but de vous aider à utiliser les fonctionnalités de surveillance d’Azure Security Center, afin de contrôler la conformité aux stratégies.

## <a name="what-is-security-health-monitoring"></a>Qu’est-ce que la surveillance de l’intégrité de la sécurité ?
Nous pensons souvent que la surveillance consiste à veiller et à attendre qu’un événement se produise pour y répondre. Ici, la surveillance de la sécurité fait référence à une stratégie proactive qui audite vos ressources afin d’identifier les systèmes qui ne répondent pas aux normes organisationnelles ou aux meilleures pratiques.

## <a name="monitoring-security-health"></a>Surveillance de l’intégrité de la sécurité
Une fois que vous avez activé les [stratégies de sécurité](security-center-policies.md) pour les ressources d’un abonnement, Azure Security Center analyse la sécurité de vos ressources afin d’identifier les vulnérabilités potentielles. Les informations sur la configuration du réseau sont instantanément disponibles. Selon le nombre de machines virtuelles et d’ordinateurs sur lesquels est installé l’agent, il se peut que la collecte d’informations sur la configuration des ordinateurs et des machines virtuelles (statut des mises à jour de sécurité et configuration du système d’exploitation) ne soit disponible qu’au bout d’une heure, voire plus. Vous pouvez visualiser l’état de sécurité de vos ressources et les problèmes éventuels dans la section **Prévention**. Vous pouvez également afficher une liste de ces problèmes dans la mosaïque **Recommandations** .

Pour plus d’informations sur la façon d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).

Dans la section **Prévention**, vous pouvez surveiller l’état de sécurité de vos ressources. Dans l’exemple suivant, vous pouvez voir dans la vignette de chaque ressource (Compute, Mise en réseau, Stockage et données et Application) le nombre total de problèmes qui ont été identifiés.

![Mosaïque Intégrité de la sécurité des ressources](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute"></a>Suivre les calculs
Lorsque vous cliquez sur vignette **Compute**, trois onglets s’affichent :

- **Vue d’ensemble** : recommandations relatives à la surveillance.
- **Machines virtuelles et ordinateurs** : répertorie l’ensemble des machines virtuelles, des ordinateurs et de leurs états de sécurité actuels.
- **Services cloud** : liste de tous les rôles web et de travail contrôlés par Security Center.

![Mise à jour système manquante par machine virtuelle](./media/security-center-monitoring/security-center-monitoring-fig1-sep2017.png)

Chaque onglet peut contenir plusieurs sections, et dans chaque section vous pouvez sélectionner une option afin d’afficher plus de détails sur les étapes recommandées pour résoudre le problème en question.

#### <a name="monitoring-recommendations"></a>Recommandations concernant la surveillance
Cette section indique le nombre total de machines virtuelles et d’ordinateurs qui ont été initialisés pour l’approvisionnement automatique et leurs états actuels. Cet exemple comprend une recommandation, **Problèmes d’intégrité de l’agent d’analyse**.  Sélectionnez cette recommandation.

![Problèmes d’intégrité de l’agent de surveillance](./media/security-center-monitoring/security-center-monitoring-fig1-new003-2017.png)

**Problèmes d’intégrité de l’agent de surveillance** s’ouvre. Les machines virtuelles et les ordinateurs que le Security Center ne peut pas surveiller correctement s’affichent ici. Pour plus d’informations, sélectionnez une machine virtuelle ou un ordinateur. **MONITORING STATE** fournit une raison pour laquelle Security Center ne peut pas surveiller. Consultez le [guide de dépannage de Security Center](security-center-troubleshooting-guide.md#monitoring-agent-health-issues) pour obtenir une liste de valeurs, de descriptions et d’étapes de résolution de **MONITORING STATE**.

#### <a name="recommendations"></a>Recommandations
Cette section contient un ensemble de [recommandations pour chacune des machines virtuelles et chacun des ordinateurs](security-center-virtual-machine-recommendations.md) surveillés par Azure Security Center. La première colonne indique la recommandation. La deuxième colonne indique le nombre total de machines virtuelles et d’ordinateurs qui sont affectés par cette recommandation. La troisième colonne indique la gravité du problème, comme illustré dans la capture d’écran suivante :

![Recommandations pour machines virtuelles](./media/security-center-monitoring/security-center-monitoring-fig2-sep2017.png)

> [!NOTE]
> Seules les machines virtuelles ayant au moins un point de terminaison public sont affichées dans la section relative à **l’intégrité de la mise en réseau**, dans la liste **Topologie de mise en réseau**.
>

Chaque recommandation dispose d’un ensemble d’actions pouvant être effectuées après avoir cliqué dessus. Par exemple, une liste s’affiche si vous cliquez sur **Mises à jour système manquantes**. Elle indique les machines virtuelles et des ordinateurs ayant des correctifs manquants et la gravité de la mise à jour manquante, comme illustré dans la capture d’écran suivante :

![Mise à jour système manquante pour les machines virtuelles](./media/security-center-monitoring/security-center-monitoring-fig9-sep2017.png)

La section **Mises à jour système manquantes** contient un résumé des mises à jour critiques dans un format graphique, l’un pour Windows et l’autre pour Linux. La deuxième partie contient un tableau avec les informations suivantes :

* **NOM** : nom de la mise à jour manquante.
* **NO. DE MACHINES VIRTUELLES ET D’ORDINATEURS** : nombre total de machines virtuelles et d’ordinateurs sur lesquels cette mise à jour n’a pas été installée.
* **ÉTAT**: état actuel de la recommandation :
  * **Ouverte** : la recommandation n’a pas encore été prise en compte.
  * **En cours** : la recommandation est actuellement appliquée à ces ressources ; aucune action de votre part n’est nécessaire.
  * **Résolue** : la recommandation a déjà été achevée. (Une fois problème résolu, l’entrée a été grisée).
* **GRAVITÉ**: donne le niveau de gravité de chaque recommandation :
  * **Élevée** : existence d’une vulnérabilité sur une ressource importante (application, machine virtuelle, groupe de sécurité réseau). Le problème doit être analysé.
  * **Moyenne** : certaines étapes supplémentaires sont nécessaires pour terminer un processus ou éliminer une vulnérabilité.
  * **Faible**: existence d’une vulnérabilité devant être prise en compte, mais qui ne nécessite aucune attention immédiate. Par défaut, les recommandations de niveau Faible ne sont pas affichées, mais vous pouvez filtrer les recommandations pour les faire apparaître.

Pour afficher des informations détaillées sur les recommandations, cliquez sur le nom de la mise à jour manquante dans la liste.

![Mise à jour système manquante pour une machine virtuelle spécifique](./media/security-center-monitoring/security-center-monitoring-fig4-sep2017.png)

> [!NOTE]
> Les recommandations de sécurité ci-dessus sont les mêmes que celles figurant dans l’option **Recommandations**. Pour plus d’informations sur la résolution des recommandations, consultez l’article [Implémentation des recommandations de sécurité dans le Centre de sécurité Azure](security-center-recommendations.md). Cela s’applique non seulement aux machines virtuelles et aux ordinateurs, mais aussi à toutes les ressources qui sont disponibles dans la vignette **Intégrité des ressources**.
>

#### <a name="vms--computers-section"></a>Section Machines virtuelles et ordinateurs
La section Machines virtuelles et ordinateurs vous fournit une vue d’ensemble de toutes les recommandations relatives aux machines virtuelles et à l’ordinateur. Chaque colonne représente un ensemble de recommandations, comme illustré dans la capture d’écran suivante :

![Vue d’ensemble des machines virtuelles et des recommandations](./media/security-center-monitoring/security-center-monitoring-fig5-sep2017.png)

Quatre types d’icônes sont représentés dans cette liste, comme expliqué ici :

![icon1](./media/security-center-monitoring/security-center-monitoring-icon1.png) Ordinateur ne relevant pas d’Azure.

![icon2](./media/security-center-monitoring/security-center-monitoring-icon2.png) Machine virtuelle Azure Resource Manager.

![icon3](./media/security-center-monitoring/security-center-monitoring-icon3.png) Machine virtuelle Azure Classic.

![icon4](./media/security-center-monitoring/security-center-monitoring-icon4.png) Machines virtuelles identifiées uniquement à partir de l’espace de travail qui fait partie de l’abonnement affiché. Cela inclut les machines virtuelles d’autres abonnements qui se rapportent à l’espace de travail associé à cet abonnement, ainsi que les machines virtuelles qui ont été installées avec l’agent SCOM Direct et qui ne possèdent pas d’ID de ressource.

L’icône qui s’affiche sous chaque recommandation vous aide à identifier rapidement la machine virtuelle et l’ordinateur qui requièrent votre attention, ainsi que le type de recommandation. Vous pouvez également utiliser l’option **Filtre** pour sélectionner les options qui s’afficheront sur cet écran.

![Filtrer](./media/security-center-monitoring/security-center-monitoring-fig6-sep2017.png)

Dans l’exemple précédent, une machine virtuelle a une recommandation critique concernant la protection de point de terminaison. Pour obtenir plus d’informations sur la machine virtuelle, cliquez dessus :

![Informations détaillées sur la sécurité de la machine virtuelle](./media/security-center-monitoring/security-center-monitoring-fig7-sep2017.png)

Ce panneau comporte des informations détaillées sur la sécurité de la machine virtuelle ou de l’ordinateur. L’action recommandée et le niveau de gravité de chaque problème sont affichés en bas.

#### <a name="cloud-services-section"></a>Section Services cloud
Pour les services cloud, une recommandation est créée lorsque la version du système d’exploitation est obsolète, comme illustré dans la capture d’écran suivante :

![État d’intégrité des services cloud](./media/security-center-monitoring/security-center-monitoring-fig1-new006-2017.png)

Si vous avez une recommandation (qui n’est pas le cas dans l’exemple précédent), vous devez suivre les étapes de la recommandation pour mettre à jour la version du système d’exploitation. Lorsqu’une mise à jour est disponible, vous recevez une alerte (rouge ou orange en fonction de la gravité du problème). Lorsque vous cliquez sur cette alerte dans les lignes WebRole1 (exécute Windows Server avec votre application web automatiquement déployée sur IIS) ou WorkerRole1 (exécute Windows Server avec votre application web automatiquement déployée sur IIS), vous obtenez des informations détaillées sur cette recommandation, comme illustré dans la capture d’écran suivante :

![Détails du service cloud](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

Pour voir une explication plus normative concernant cette recommandation, cliquez sur **Mettre à jour la version du système d’exploitation** sous la colonne **DESCRIPTION**.

![Recommandations de services cloud](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

### <a name="monitor-virtual-networks"></a>Surveillance des réseaux virtuels
Lorsque vous cliquez sur la mosaïque **Mise en réseau**, le panneau **Mise en réseau** s’ouvre et affiche des informations détaillées, comme illustré dans la capture d’écran suivante :

![Panneau Mise en réseau](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>Recommandations pour la mise en réseau
À l’instar des informations relatives à l’intégrité des ressources de la machine virtuelle, une liste récapitulative des problèmes s’affiche en haut, et une liste de réseaux surveillés s’affiche en bas.

La section de répartition des états du réseau répertorie les problèmes de sécurité potentiels et propose des [recommandations](security-center-network-recommendations.md)pour leur résolution. Voici des exemples de problèmes potentiels :

* Absence d’installation d’un pare-feu de nouvelle génération
* Non-activation des groupes de sécurité réseau
* Non-activation des groupes de sécurité réseau sur les machines virtuelles
* Restriction de l’accès externe via le point de terminaison externe public
* Intégrité des points de terminaison exposés à Internet

Lorsque vous cliquez sur une recommandation, des informations détaillées s’affichent, comme illustré dans l’exemple suivant :

![Détails relatifs à une recommandation dans la section de mise en réseau](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

Dans cet exemple, le panneau **Configurer les groupes de sécurité réseau manquants pour les sous-réseaux** contient une liste de sous-réseaux et de machines virtuelles ne disposant pas de la protection des groupes de sécurité réseau. Si vous cliquez sur le sous-réseau auquel vous souhaitez appliquer le groupe de sécurité réseau, le panneau **Choisir un groupe de sécurité réseau**s’affiche. Ici, vous pouvez sélectionner le groupe de sécurité réseau le mieux adapté au sous-réseau, ou créer un groupe de sécurité réseau.

#### <a name="internet-facing-endpoints-section"></a>Section des points de terminaison accessibles sur Internet
La section **Points de terminaison accessibles sur Internet** présente les machines virtuelles qui sont actuellement configurées avec un point de terminaison accessible sur Internet, ainsi que leur état actuel.

![Les machines virtuelles configurées avec un point de terminaison accessible sur Internet et son état](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

Ce tableau indique le nom du point de terminaison qui représente la machine virtuelle, l’adresse IP Internet et l’état de gravité actuel du groupe de sécurité réseau et du pare-feu nouvelle génération. Le tableau est trié par niveau de gravité :

* Rouge (en haut) : priorité élevée ; doivent être traités immédiatement
* Orange : priorité moyenne ; doivent être traités dès que possible
* Vert (le dernier) : état d’intégrité

#### <a name="networking-topology-section"></a>Section de topologie de mise en réseau
La section **Topologie de mise en réseau** contient une vue hiérarchique des ressources, comme illustré dans la capture d’écran suivante :

![Vue hiérarchique des ressources dans la section Topologie de mise en réseau](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

Ce tableau est trié (machines virtuelles et sous-réseaux) par niveau de gravité :

* Rouge (en haut) : priorité élevée ; doivent être traités immédiatement
* Orange : priorité moyenne ; doivent être traités dès que possible
* Vert (le dernier) : état d’intégrité

Dans cette topologie, le premier niveau se décompose comme suit : [réseaux virtuels](../virtual-network/virtual-networks-overview.md), [passerelles de réseau virtuel](/vpn-gateway/vpn-gateway-site-to-site-create.md) et [réseaux virtuels (classiques)](/virtual-network/virtual-networks-create-vnet-classic-pportal.md). Le deuxième niveau comprend des sous-réseaux et le troisième niveau regroupe les machines virtuelles appartenant à ces sous-réseaux. La colonne de droite présente l’état actuel du groupe de sécurité réseau pour ces ressources, comme illustré dans l’exemple suivant :

![État du groupe de sécurité réseau dans la section Topologie de mise en réseau](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

La partie inférieure de ce panneau regroupe les recommandations pour cette machine virtuelle, similaires celles décrites ci-dessus. Vous pouvez cliquer sur une recommandation pour en savoir plus, ou appliquer la configuration ou le contrôle de sécurité nécessaire.

### <a name="monitor-storage--data"></a>Analyse de Stockage et données

Lorsque vous cliquez sur **Stockage et données** dans la section **Prévention**, le panneau **Ressources de données** s’ouvre et affiche des recommandations pour SQL et le stockage. Il contient également des [recommandations](security-center-sql-service-recommendations.md) pour l’état général de la base de données. Pour plus d’informations sur le chiffrement du stockage, consultez [Enable encryption for Azure storage account in Azure Security Center (Activer le chiffrement pour le compte de stockage Azure dans Azure Security Center)](security-center-enable-encryption-for-storage-account.md).

![Ressources de données](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Sous **Recommandations SQL**, vous pouvez cliquer sur une recommandation et obtenir des informations sur les actions permettant de résoudre un problème. L’exemple suivant montre le détail de la recommandation **Database Auditing & Threat detection on SQL databases (Audit de base de données et détection des menaces sur les bases de données SQL)**.

![Détails relatifs à une recommandation SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Le panneau **Activer l’audit et la détection de menaces sur les bases de données SQL** contient les informations suivantes :

* Une liste des bases de données SQL.
* Le serveur sur lequel elles se trouvent.
* Des informations indiquant si ce paramètre a été hérité du serveur ou s’il est unique dans cette base de données.
* L’état actuel du problème.
* Le niveau de gravité du problème.

Lorsque vous cliquez sur la base de données pour suivre cette recommandation, le panneau **Audit et détection des menaces** s’ouvre, comme illustré dans la capture d’écran suivante.

![Audit et détection des menaces](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Pour activer l’audit, sélectionnez **ACTIVÉ** sous **Audit**.

### <a name="monitor-applications"></a>Surveillance des applications

Si votre charge de travail Azure comprend des applications situées sur des [machines virtuelles (créées via Azure Resource Manager)](../azure-resource-manager/resource-manager-deployment-model.md) comportant des ports web exposés (ports TCP 80 et 443), Azure Security Center peut les surveiller pour identifier les problèmes de sécurité potentiels et recommander des étapes de résolution. Lorsque vous cliquez sur la vignette **Applications**, le panneau **Applications** s’ouvre et affiche un ensemble de recommandations dans la section **Recommandations relatives aux applications**. Il affiche également la répartition des applications par hôte, adresse IP/domaine et indique si une solution WAF est installée :

![État de sécurité des applications](./media/security-center-monitoring/security-center-monitoring-fig8-sep2017.png)

Comme pour les cas précédents, vous pouvez cliquer sur la recommandation pour afficher des informations détaillées sur le problème et la procédure à suivre pour le résoudre. L’exemple illustré ci-dessus représente une application ayant été identifiée comme une application web non sécurisée. Lorsque vous sélectionnez l’application considérée comme non sécurisée, l’option suivante est disponible :

![Détails](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

Cette liste répertorie toutes les recommandations pour cette application. Lorsque vous cliquez sur la recommandation **Ajouter un pare-feu d’application web**, le panneau **Ajouter un pare-feu d’application web** s’ouvre et affiche des options vous permettant d’installer le pare-feu d’application web (WAF) d’un partenaire, comme illustré dans la capture d’écran suivante.

![Boîte de dialogue Ajouter un pare-feu d’applications web](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## <a name="see-also"></a>Voir aussi
Dans cet article, vous avez vu comment utiliser les fonctionnalités de surveillance d’Azure Security Center. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des paramètres de sécurité dans Azure Security Center.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.
