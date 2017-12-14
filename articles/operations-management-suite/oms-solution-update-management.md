---
title: "Solution de gestion des mises à jour dans OMS | Microsoft Docs"
description: "Cet article vise à vous aider à comprendre comment utiliser cette solution pour gérer les mises à jour de vos ordinateurs Windows et Linux."
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: e33ce6f9-d9b0-4a03-b94e-8ddedcc595d2
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: magoedte;eslesar
ms.openlocfilehash: e3d605b12a1db2fca1048be15e7b365e5336f663
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="update-management-solution-in-oms"></a>Solution de gestion des mises à jour dans OMS

![Symbole de gestion des mises à jour](./media/oms-solution-update-management/update-management-symbol.png)

La solution Update Management d’OMS vous permet de gérer les mises à jour de sécurité du système d’exploitation de vos ordinateurs Windows et Linux déployés dans Azure, des environnements locaux ou d’autres fournisseurs de cloud.  Vous pouvez rapidement évaluer l’état des mises à jour disponibles sur tous les ordinateurs d’agent et gérer le processus d’installation des mises à jour requises pour les serveurs.

## <a name="update-management-in-azure-automation"></a>Gestion de mise à jour dans Azure Automation

Vous pouvez activer la gestion de mise à jour pour les machines virtuelles directement depuis votre compte [Azure Automation](../automation/automation-offering-get-started.md).
Pour découvrir comment activer la gestion de mise à jour pour les machines virtuelles depuis votre compte Automation, consultez l’article [Gérer les mises à jour pour plusieurs machines virtuelles](../automation/manage-update-multi.md).


## <a name="solution-overview"></a>Vue d’ensemble de la solution
Les ordinateurs gérés par OMS utilisent les composants suivants pour effectuer l’évaluation et les déploiements de mises à jour :

* Agent OMS pour Windows ou Linux
* PowerShell DSC (Desired State Configuration, configuration d’état souhaité) pour Linux
* Runbook Worker hybride Automation
* Services Microsoft Update ou Windows Server Update pour ordinateurs Windows

Les schémas suivants présentent une vue conceptuelle du comportement et du flux de données de la façon dont la solution évalue et applique des mises à jour de sécurité à tous les ordinateurs Windows Server et Linux dans un espace de travail.    

#### <a name="windows-server"></a>Windows Server
![Flux de processus de gestion des mises à jour Windows Server](media/oms-solution-update-management/update-mgmt-windows-updateworkflow.png)

#### <a name="linux"></a>Linux
![Flux de processus de gestion des mises à jour Linux](media/oms-solution-update-management/update-mgmt-linux-updateworkflow.png)

Une fois que l’ordinateur effectue une analyse de conformité de mise à jour, l’agent OMS transfère les informations en bloc à OMS. Sur un ordinateur Windows, l’analyse de conformité est effectuée toutes les 12 heures par défaut.  En plus de l’analyse planifiée, l’analyse de conformité de mise à jour est lancée dans les 15 minutes si Microsoft Monitoring Agent (MMA) est redémarré, avant l’installation de la mise à jour et après l’installation de la mise à jour.  Avec un ordinateur Linux, l’analyse de conformité est effectuée toutes les 3 heures par défaut, et une analyse de conformité est lancée dans les 15 minutes si l’agent MMA est redémarré.  

Les informations de conformité sont ensuite traitées et résumées dans les tableaux de bord inclus dans la solution ou peuvent être recherchées à l’aide de requêtes définies par l’utilisateur ou prédéfinies.  La solution rapporte l’état de mise à jour de l’ordinateur en fonction de la source avec laquelle synchroniser que vous configurez.  Si l’ordinateur Windows est configuré pour rapporter à WSUS, en fonction de la date de dernière synchronisation de WSUS avec Microsoft Update, les résultats peuvent être différents de ce que Microsoft Updates indique.  C’est également le cas pour les ordinateurs Linux qui sont configurés pour rapporter à un référentiel local par rapport à un référentiel public.   

Vous pouvez déployer et installer des mises à jour logicielles sur des ordinateurs qui nécessitent les mises à jour en créant un déploiement planifié.  Les mises à jour classées comme *facultatives* ne sont pas incluses dans le déploiement pour ordinateurs Windows, uniquement les mises à jour requises.  Le déploiement planifié définit les ordinateurs cibles qui recevront les mises à jour applicables, soit en spécifiant explicitement les ordinateurs ou en sélectionnant un [groupe d’ordinateurs](../log-analytics/log-analytics-computer-groups.md) basés sur des recherches de journaux d’un ensemble spécifique d’ordinateurs.  Vous spécifiez également une planification pour approuver et désigner une période de temps pendant laquelle l’installation des mises à jour est autorisée.  Les mises à jour sont installées par des Runbooks dans Azure Automation.  Vous ne pouvez pas visualiser ces Runbooks, qui ne nécessitent aucune configuration.  Lorsqu’un déploiement de mises à jour est créé, il génère une planification qui démarre un Runbook de mises à jour principal au moment indiqué pour les ordinateurs inclus.  Ce Runbook principal lance un Runbook enfant sur chaque agent qui effectue l’installation des mises à jour obligatoires.       

À la date et l’heure spécifiées dans le déploiement de mises à jour, les ordinateurs cibles exécutent le déploiement en parallèle.  Une analyse est tout d’abord effectuée pour vérifier si les mises à jour sont toujours obligatoires et les installe.  Il est important de noter que, pour les ordinateurs clients WSUS, si les mises à jour ne sont pas approuvées dans WSUS, le déploiement de mises à jour échoue.  Les résultats des mises à jour appliquées sont transmis à OMS pour être traités et résumés dans les tableaux de bord ou en recherchant les événements.     

## <a name="prerequisites"></a>Composants requis
* La solution prend en charge les évaluations de mise à jour sur Windows Server 2008 et les versions ultérieures, ainsi que les déploiements de mises à jour sur Windows Server 2008 R2 SP1 et les versions ultérieures.  Nano Server n’est pas pris en charge.

    > [!NOTE]
    > La prise en charge du déploiement des mises à jour vers Windows Server 2008 R2 SP1 nécessite .NET Framework 4.5 et WMF 5.0 ou une version ultérieure.
    >  
* Les systèmes d’exploitation clients Windows ne sont pas pris en charge.  
* Les agents Windows doivent être configurés pour communiquer avec un serveur WSUS (Windows Server Update Services) ou avoir accès à Microsoft Update.  

    > [!NOTE]
    > L’agent Windows ne peut pas être géré simultanément par System Center Configuration Manager.  
    >
* CentOS 6 (x86/x64) et 7 (x 64)  
* Red Hat Enterprise 6 (x86/x64) et 7 (x 64)  
* SUSE Linux Enterprise Server 11 (x86/x64) et 12 (x64)  
* Ubuntu 12.04 LTS et x86/x64 plus récente   
    > [!NOTE]  
    > Pour éviter que les mises à jour soient appliquées en dehors d’une fenêtre de maintenance sur Ubuntu, reconfigurez le package Unattended-Upgrade pour désactiver les mises à jour automatiques. Pour plus d’informations sur cette configuration, consultez la [rubrique Mises à jour automatiques du Guide du serveur Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* Les agents Linux doivent avoir accès à un référentiel de mise à jour.  

    > [!NOTE]
    > Un agent OMS pour Linux configuré pour rapporter à plusieurs espaces de travail OMS n’est pas pris en charge avec cette solution.  
    >

Pour plus d’informations sur l’installation de l’agent OMS pour Linux et le téléchargement de la dernière version, consultez [Operations Management Suite Agent pour Linux](https://github.com/microsoft/oms-agent-for-linux).  Pour plus d’informations sur l’installation de l’agent OMS pour Windows, consultez [Operations Management Suite Agent pour Windows](../log-analytics/log-analytics-windows-agent.md).  

### <a name="permissions"></a>Autorisations
Pour créer des déploiements de mise à jour, vous devez disposer du rôle de contributeur dans votre compte Automation et votre espace de travail Log Analytics.  

## <a name="solution-components"></a>Composants de la solution
Cette solution se compose des ressources suivantes qui sont ajoutées à votre compte Automation et d’agents directement connectés ou d’un groupe d’administration Operations Manager connecté.

### <a name="management-packs"></a>Packs d’administration
Si votre groupe d’administration System Center Operations Manager est connecté à un espace de travail OMS, les packs d’administration suivants sont installés dans Operations Manager.  Ces packs d’administration sont également installés sur des ordinateurs Windows directement connectés après l’ajout de cette solution. Aucun élément ne doit être configuré ou géré avec ces packs d’administration.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pack d’administration du déploiement des mises à jour

Pour plus d’informations sur la façon dont ces packs d’administration de solution sont mis à jour, consultez [Connecter Operations Manager à Log Analytics](../log-analytics/log-analytics-om-agents.md).

### <a name="hybrid-worker-groups"></a>Groupes de Workers hybrides
Après avoir activé cette solution, tout ordinateur Windows directement connecté à votre espace de travail OMS est automatiquement configuré comme un Runbook Worker hybride pour prendre en charge les Runbooks inclus dans cette solution.  Chaque ordinateur Windows géré par la solution est répertorié dans le panneau des groupes de travail de Runbook hybrides du compte Automation conformément à la convention d’affectation de noms *Hostname FQDN_GUID*.  Vous ne pouvez pas cibler ces groupes avec des Runbooks dans votre compte ; sinon ils échouent. Ces groupes sont uniquement destinés à prendre en charge de la solution de gestion.   

Vous pouvez toutefois ajouter les ordinateurs Windows à un groupe de Runbooks Workers hybrides dans votre compte Automation pour prendre en charge des runbooks Automation à condition d’utiliser le même compte à la fois pour la solution et pour l’appartenance au groupe de Runbooks Workers hybrides.  Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du groupe de Runbooks Workers hybrides.  

## <a name="configuration"></a>Configuration
Procédez comme suit pour ajouter la solution de gestion des mises à jour à votre espace de travail OMS et pour vérifier que les agents rapportent effectivement. Les agents Windows déjà connectés à votre espace de travail sont automatiquement ajoutés, sans aucune configuration supplémentaire.

Vous pouvez déployer la solution à l’aide des méthodes suivantes :

* À partir de la Place de marché Azure, dans le portail Azure, en sélectionnant l’offre Automatisation et contrôle ou la solution Gestion des mises à jour
* À partir de la galerie de solutions d’OMS, dans votre espace de travail OMS

Si vous disposez déjà d’un compte Automation et d’un espace de travail OMS liés dans les mêmes groupes de ressources et région, sélectionnez Automation & Control pour vérifier votre configuration et installer uniquement la solution et la configurer dans les deux services.  Sélectionnez la solution de gestion de mises à jour à partir d’Azure Marketplace pour disposer du même comportement.  Si l’un ou l’autre service n’est pas déployé dans votre abonnement, effectuez les étapes du panneau **Créer une nouvelle solution** et confirmez que vous voulez installer les autres solutions recommandées présélectionnées.  Facultativement, vous pouvez ajouter la solution de gestion de mises à jour à votre espace de travail OMS en effectuant les étapes décrites dans la rubrique [Ajouter une solution OMS](../log-analytics/log-analytics-add-solutions.md) de la galerie de solutions.  

### <a name="confirm-oms-agents-and-operations-manager-management-group-connected-to-oms"></a>Confirmer les agents OMS et le groupe d’administration Operations Manager connectés à OMS

Pour confirmer que l’agent OMS pour Linux et Windows directement connecté communique avec OMS, vous pouvez exécuter la recherche de journal suivante après quelques minutes :

* Linux - `Type=Heartbeat OSType=Linux | top 500000 | dedup SourceComputerId | Sort Computer | display Table`.  

* Windows - `Type=Heartbeat OSType=Windows | top 500000 | dedup SourceComputerId | Sort Computer | display Table`

Sur un ordinateur Windows, vous pouvez observer les informations suivantes pour vérifier la connectivité de l’agent avec OMS :

1.  Ouvrez Microsoft Monitoring Agent dans le Panneau de configuration puis, dans l’onglet **Azure Log Analytics (OMS)**, l’agent affiche un message indiquant : **Microsoft Monitoring Agent est bien connecté au service Microsoft Operations Management Suite**.   
2.  Ouvrez le journal des événements Windows, accédez à **Application and Services Logs\Operations Manager**, puis recherchez l’ID d’événement 3000 et 5002 à partir du connecteur de service source.  Ces événements indiquent que l’ordinateur est enregistré sur l’espace de travail OMS et qu’il reçoit la configuration.  

Si l’agent ne parvient pas à communiquer avec le service OMS et qu’il est configuré pour communiquer avec Internet par le biais d’un pare-feu ou d’un serveur proxy, vérifiez que le pare-feu ou le serveur proxy sont correctement configurés en consultant la [configuration réseau de l’agent Windows](../log-analytics/log-analytics-windows-agent.md) ou la [configuration réseau de l’agent Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Si vos systèmes Linux sont configurés pour communiquer avec un proxy ou une passerelle OMS et que vous intégrez cette solution, mettez à jour les autorisations *proxy.conf* pour accorder au groupe omiuser une autorisation d’accès en lecture sur le fichier en exécutant les commandes suivantes :  
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`  
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`


Les nouveaux agents Linux ajoutés affichent l’état **Mis à jour** après l’exécution d’une évaluation.  Ce processus peut prendre jusqu’à 6 heures.

Pour vérifier qu’un groupe d’administration Operations Manager communique avec OMS, consultez la rubrique [Valider l’intégration entre Operations Manager et OMS](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-oms).

## <a name="data-collection"></a>Collecte des données
### <a name="supported-agents"></a>Agents pris en charge
Le tableau suivant décrit les sources connectées qui sont prises en charge par cette solution.

| Source connectée | Pris en charge | Description |
| --- | --- | --- |
| Agents Windows |Oui |La solution de collecte des informations sur les mises à jour système des agents et lance l’installation des mises à jour obligatoires. |
| Agents Linux |Oui |La solution collecte des informations sur les mises à jour système des agents Linux et lance l’installation des mises à jour obligatoires sur les versions prises en charge. |
| Groupe d’administration d’Operations Manager |Oui |La solution collecte des informations sur les mises à jour système des agents dans un groupe d’administration connecté.<br>Une connexion directe entre l’agent Operations Manager et Log Analytics n’est pas obligatoire. Les données sont transférées du groupe d’administration au référentiel OMS. |
| Compte Azure Storage |Non |Le stockage Azure n’inclut aucune information sur les mises à jour du système. |

### <a name="collection-frequency"></a>Fréquence de collecte
Pour chaque ordinateur Windows géré, une analyse est effectuée deux fois par jour. Les API Windows sont appelées toutes les 15 minutes pour rechercher l’heure de la dernière mise à jour afin de déterminer si l’état a changé et si une analyse de conformité est lancée.  Pour chaque ordinateur Linux géré, une analyse est effectuée toutes les 3 heures.

L’affichage des données mises à jour des ordinateurs gérés dans le tableau de bord peut prendre de 30 minutes à 6 heures.   

## <a name="using-the-solution"></a>Utilisation de la solution
Lorsque vous ajoutez la solution de gestion des mises à jour à votre espace de travail OMS, la mosaïque **Gestion des mises à jour** est ajoutée à votre tableau de bord OMS. Cette mosaïque affiche une valeur et une représentation graphique du nombre d’ordinateurs de votre environnement et leur conformité de mise à jour.<br><br>
![Mosaïque représentant un résumé de la gestion des mises à jour](media/oms-solution-update-management/update-management-summary-tile.png)  


## <a name="viewing-update-assessments"></a>Affichage des évaluations de mises à jour
Cliquez sur la mosaïque **Gestion des mises à jour** pour ouvrir le tableau de bord **Gestion des mises à jour**.<br><br> ![Tableau de bord résumant la gestion des mises à jour](./media/oms-solution-update-management/update-management-dashboard.png)<br>

Ce tableau de bord fournit une description détaillée de l’état de mise à jour classé par type de système d’exploitation et classification de mise à jour : critique, sécurité ou autres (par exemple, une mise à jour de définition). Les résultats de chaque vignette du tableau de bord reflètent uniquement les mises à jour approuvées pour le déploiement, qui est basé sur la source de synchronisation des ordinateurs.   La mosaïque **Déploiements de mises à jour**, lorsqu’elle est sélectionnée, vous redirige vers la page Déploiements de mises à jour dans laquelle vous pouvez consulter les planifications, les déploiements en cours d’exécution, les déploiements terminés ou planifier un nouveau déploiement.  

Vous pouvez exécuter une recherche de journal qui renvoie tous les enregistrements en cliquant sur la mosaïque appropriée ou pour exécuter une requête d’une catégorie et de critères prédéfinis spécifiques, en sélectionner un dans la liste de la colonne **Requêtes de mise à jour courantes**.    

## <a name="installing-updates"></a>Installation des mises à jour
Une fois les mises à jour évaluées pour tous les ordinateurs Linux et Windows dans votre espace de travail, vous pouvez lancer l’installation des mises à jour obligatoires en créant une opération de *déploiement de mises à jour*.  Un déploiement de mises à jour est une installation planifiée de mises à jour obligatoires pour un ou plusieurs ordinateurs.  Vous pouvez spécifier la date et l’heure du déploiement en plus d’un ordinateur ou d’un groupe d’ordinateurs à inclure dans un déploiement.  Pour en savoir plus sur les groupes d’ordinateurs, consultez [Groupes d’ordinateurs dans Log Analytics](../log-analytics/log-analytics-computer-groups.md).  Lorsque vous incluez des groupes d’ordinateurs dans votre déploiement de mises à jour, l’appartenance au groupe n’est évaluée qu’une seule fois au moment de la création de la planification.  Les modifications ultérieures apportées à un groupe ne sont pas répercutées.  Pour contourner ce problème, supprimez le déploiement de mises à jour planifié et recréez-le.

> [!NOTE]
> Les machines virtuelles Windows déployées à partir d’Azure Marketplace sont configurées par défaut pour recevoir des mises à jour automatiques de Windows Update Service.  Ce comportement ne change pas après l’ajout de cette solution ou de machines virtuelles Windows à votre espace de travail.  Si vous n’avez pas géré activement des mises à jour avec cette solution, le comportement par défaut (appliquer automatiquement des mises à jour) s’applique.  

Les machines virtuelles créées à partir des images Red Hat Enterprise Linux (RHEL) à la demande disponibles dans le service Place de marché Azure sont inscrites pour accéder à l’infrastructure [RHUI (Red Hat Update Infrastructure)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) déployée dans Azure.  Toute autre distribution Linux doit être mise à jour à partir du référentiel de fichiers de distributions en ligne en tenant compte de leurs méthodes prises en charge.  

### <a name="viewing-update-deployments"></a>Affichage des déploiements de mises à jour
Cliquez sur la mosaïque **Déploiement de mises à jour** pour afficher la liste des déploiements de mises à jour existants.  Ces déploiements sont regroupés par état : **Planifié**, **Exécution en cours** et **Terminé**.<br><br> ![Page de planification des déploiements de mises à jour](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

Les propriétés affichées pour chaque déploiement de mises à jour sont décrites dans le tableau suivant.

| Propriété | Description |
| --- | --- |
| Nom |Nom du déploiement de mises à jour. |
| Planification |Type de planification.  Les options disponibles sont *une fois*, *hebdomadaire récurrente*, ou *mensuelle récurrente*. |
| Heure de début |Date et heure de début planifiées pour le déploiement de mises à jour. |
| Durée |Nombre de minutes pendant lequel le déploiement de mises à jour est autorisé à s’exécuter.  Si toutes les mises à jour ne sont pas installées pendant cette durée, les mises à jour restantes doivent attendre le prochain déploiement de mises à jour. |
| Serveurs |Nombre d’ordinateurs affectés par le déploiement de mises à jour.  |
| État |État actuel du déploiement de mises à jour.<br><br>Les valeurs possibles sont les suivantes :<br>- Non commencé<br>- Exécution en cours<br>- Terminé |

Sélectionnez un déploiement de mises à jour terminé pour afficher la page de détails, qui inclut les colonnes indiquées dans le tableau suivant.  Ces colonnes ne sont remplies qu’une fois que le déploiement de mises à jour a commencé.<br><br> ![Vue d’ensemble des résultats d’un déploiement de mises à jour](./media/oms-solution-update-management/update-management-deploymentresults-dashboard.png)

| Colonne | Description |
| --- | --- |
| **Vue des ordinateurs** | |
| Ordinateurs Windows |Répertorie le nombre d’ordinateurs Windows concernés par le déploiement de mises à jour, par état.  Cliquez sur un état pour exécuter une recherche de journal qui renvoie l’ensemble des enregistrements de mises à jour présentant cet état pour le déploiement de mises à jour. |
| Ordinateurs Linux |Répertorie le nombre d’ordinateurs Linux concernés par le déploiement de mises à jour, par état.  Cliquez sur un état pour exécuter une recherche de journal qui renvoie l’ensemble des enregistrements de mises à jour présentant cet état pour le déploiement de mises à jour. |
| État de l’installation de l’ordinateur |Répertorie les ordinateurs impliqués dans le déploiement de mises à jour et le pourcentage de mises à jour dont l’installation a abouti. Cliquez sur l’une des entrées pour exécuter une recherche de journal visant à renvoyer toutes les mises à jour manquantes et critiques. |
| **Vue des mises à jour** | |
| Mises à jour Windows |Répertorie les mises à jour Windows incluses dans le déploiement de mises à jour et leur état d’installation pour chaque mise à jour.  Sélectionnez une mise à jour pour exécuter une recherche de journal renvoyant tous les enregistrements de mise à jour pour cette mise à jour spécifique ou cliquez sur l’état pour exécuter une recherche de journal renvoyant tous les enregistrements de mise à jour pour le déploiement. |
| Mises à jour Linux |Répertorie les mises à jour Linux incluses dans le déploiement de mises à jour et leur état d’installation pour chaque mise à jour.  Sélectionnez une mise à jour pour exécuter une recherche de journal renvoyant tous les enregistrements de mise à jour pour cette mise à jour spécifique ou cliquez sur l’état pour exécuter une recherche de journal renvoyant tous les enregistrements de mise à jour pour le déploiement. |

### <a name="creating-an-update-deployment"></a>Création d’un déploiement de mises à jour
Pour créer un déploiement de mises à jour, cliquez sur le bouton **Ajouter** situé sur la partie supérieure de l’écran, afin d’ouvrir la page **New Update Deployment** (Nouveau déploiement de mises à jour).  Vous devez fournir des valeurs pour les propriétés dans le tableau suivant.

| Propriété | Description |
| --- | --- |
| Nom |Nom unique identifiant le déploiement de mises à jour. |
| Time Zone (Fuseau horaire) |Fuseau horaire à utiliser pour l’heure de début. |
| Type de planification | Type de planification.  Les options disponibles sont *une fois*, *hebdomadaire récurrente*, ou *mensuelle récurrente*.  
| Heure de début |Date et heure de début du déploiement de mises à jour. **Remarque :** le délai le plus rapide d’exécution d’un déploiement est de 30 minutes à partir de l’heure actuelle si vous souhaitez déployer immédiatement. |
| Durée |Nombre de minutes pendant lequel le déploiement de mises à jour est autorisé à s’exécuter.  Si toutes les mises à jour ne sont pas installées pendant cette durée, les mises à jour restantes doivent attendre le prochain déploiement de mises à jour. |
| Ordinateurs |Noms d’ordinateurs ou de groupes d’ordinateurs à inclure et cibler dans le déploiement de mises à jour.  Sélectionnez une ou plusieurs entrées dans la liste déroulante. |

<br><br> ![Page Nouveau déploiement de mises à jour](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>Période
Par défaut, l’étendue des données analysées dans la solution de gestion des mises à jour couvre tous les groupes d’administration connectés qui ont été générés au cours de la dernière journée.

Pour modifier l’intervalle de temps des données, sélectionnez l’option **Données basées sur** située en haut du tableau de bord. Vous pouvez sélectionner les enregistrements générés ou mis à jour durant les 7 derniers jours, la journée précédente ou les 6 dernières heures. Vous pouvez également sélectionner **Personnalisé** et spécifier une plage de dates personnalisée.

## <a name="log-analytics-records"></a>Enregistrements Log Analytics
La solution de gestion des mises à jour crée deux types d’enregistrements dans le référentiel OMS.

### <a name="update-records"></a>Enregistrements de mises à jour
Un enregistrement présentant le type **Update** est créé pour chaque mise à jour installée ou requise sur chaque ordinateur. Les propriétés de ces enregistrements sont décrites dans le tableau suivant.

| Propriété | Description |
| --- | --- |
| Type |*Mettre à jour* |
| SourceSystem |Système source ayant approuvé l’installation de la mise à jour.<br>Les valeurs possibles sont les suivantes :<br>- Microsoft Update<br>- Windows Update<br>- SCCM<br>- Serveurs Linux (extraits des gestionnaires de packages) |
| Approved |Indique si l’installation de la mise à jour a été approuvée.<br> Pour les serveurs Linux, cette opération est actuellement considérée comme facultative, car l’application de mises à jour corrective n’est pas gérée par OMS. |
| Classification for Windows |Classification de la mise à jour.<br>Les valeurs possibles sont les suivantes :<br>-    Applications<br>- Mises à jour critiques<br>- Mises à jour de définitions<br>- Packs de fonctionnalités<br>- Mises à jour de sécurité<br>- Service Packs<br>- Correctifs cumulatifs<br>- Mises à jour |
| Classification for Linux |Classification de la mise à jour.<br>Les valeurs possibles sont les suivantes :<br>- Mises à jour critiques<br>- Mises à jour de sécurité<br>- Autres mises à jour |
| Ordinateur |Nom de l’ordinateur. |
| InstallTimeAvailable |Spécifie si l’heure d’installation est disponible à partir d’autres agents ayant installé la même mise à jour. |
| InstallTimePredictionSeconds |Estimation du temps d’installation, reposant sur celui des autres agents ayant installé la même mise à jour (en secondes). |
| KBID |ID de l’article de la Base de connaissances qui décrit la mise à jour. |
| ManagementGroupName |Nom du groupe d'administration pour les agents SCOM.  Pour les autres agents, il s’agit d’AOI-<workspace ID>. |
| MSRCBulletinID |ID du Bulletin de sécurité Microsoft décrivant la mise à jour. |
| MSRCSeverity |Gravité du Bulletin de sécurité Microsoft.<br>Les valeurs possibles sont les suivantes :<br>- Critique<br>- Important<br>- Modéré |
| Facultatif |Spécifie si la mise à jour est facultative. |
| Produit |Nom du produit concerné par la mise à jour.  Cliquez sur **Affichage** pour ouvrir l’article dans un navigateur. |
| PackageSeverity |Niveau de gravité de la vulnérabilité résolue dans cette mise à jour, comme indiqué par les fournisseurs de distributions Linux. |
| PublishDate |Date et heure de l’installation de la mise à jour. |
| RebootBehavior |Indique sir la mise à jour force un redémarrage.<br>Les valeurs possibles sont les suivantes :<br>- canrequestreboot<br>- neverreboots |
| RevisionNumber |Numéro de révision de la mise à jour. |
| SourceComputerId |GUID permettant d’identifier l’ordinateur de manière unique. |
| TimeGenerated |Date et heure de la dernière mise à jour apportée à l’enregistrement. |
| Intitulé |Titre de la mise à jour. |
| UpdateID |GUID permettant d’identifier la mise à jour de manière unique. |
| UpdateState |Indique si la mise à jour est installée sur cet ordinateur.<br>Les valeurs possibles sont les suivantes :<br>- Installé : la mise à jour est installée sur cet ordinateur.<br>- Nécessaire : la mise à jour n’est pas installée et est nécessaire pour cet ordinateur. |

Lorsque vous effectuez une recherche de journal qui renvoie des enregistrements présentant le type **Update**, vous pouvez sélectionner la vue **Mises à jour**, qui affiche un ensemble de mosaïques récapitulant les mises à jour renvoyées par la recherche. Vous pouvez cliquer sur les entrées des mosaïques **Mises à jour manquantes et appliquées** et **Mises à jour obligatoires et facultatives** pour étendre l’affichage à cet ensemble de mises à jour. Sélectionnez la vue **Liste** ou **Table** pour renvoyer des enregistrements individuels.<br>

![Vue Mise à jour de la recherche de journal pour le type d’enregistrement Update](./media/oms-solution-update-management/update-la-view-updates.png)  

Dans la vue **Table**, vous pouvez cliquer sur la valeur **KBID** relative à n’importe quel enregistrement pour ouvrir un navigateur affichant l’article de la Base de connaissances. Cela vous permet de consulter rapidement des détails sur une mise à jour particulière.<br>

![Vue Table de la recherche de journal incluant les mosaïques pour le type d’enregistrement Updates](./media/oms-solution-update-management/update-la-view-table.png)

Dans la vue **Liste**, cliquez sur le lien **Vue** en regard de la valeur KBID pour ouvrir l’article de la Base de connaissances.<br>

![Vue Liste de la recherche de journal incluant les mosaïques pour le type d’enregistrement Mises à jour](./media/oms-solution-update-management/update-la-view-list.png)

### <a name="updatesummary-records"></a>Enregistrements UpdateSummary
Un enregistrement avec un type **UpdateSummary** est créé pour chaque ordinateur agent. Cet enregistrement est mis à jour chaque fois que l’ordinateur est analysé à des fins de mise à jour. Les propriétés des enregistrements **UpdateSummary** sont décrites dans le tableau suivant.

| Propriété | Description |
| --- | --- |
| Type |UpdateSummary |
| SourceSystem |OpsManager |
| Ordinateur |Nom de l’ordinateur. |
| CriticalUpdatesMissing |Nombre de mises à jour critiques manquantes sur l’ordinateur. |
| ManagementGroupName |Nom du groupe d'administration pour les agents SCOM. Pour les autres agents, il s’agit d’AOI-<workspace ID>. |
| NETRuntimeVersion |Version du runtime .NET installé sur l’ordinateur. |
| OldestMissingSecurityUpdateBucket |Compartiment permettant de déterminer le temps écoulé depuis la publication de la plus ancienne mise à jour de sécurité manquante sur cet ordinateur.<br>Les valeurs possibles sont les suivantes :<br>- Antérieur<br>-    Il y a 180 jours<br>- Il y a 150 jours<br>-    Il y a 120 jours<br>- Il y a 90 jours<br>- Il y a 60 jours<br>-    Il y a 30 jours<br>-    Récent |
| OldestMissingSecurityUpdateInDays |Nombre de jours écoulé depuis la publication de la plus ancienne mise à jour de sécurité manquante sur cet ordinateur. |
| OsVersion |Version du système d’exploitation installé sur cet ordinateur. |
| OtherUpdatesMissing |Nombre de mises à jour présentant un autre type manquantes sur l’ordinateur. |
| SecurityUpdatesMissing |Nombre de mises à jour de sécurité manquantes sur l’ordinateur. |
| SourceComputerId |GUID permettant d’identifier l’ordinateur de manière unique. |
| TimeGenerated |Date et heure de la dernière mise à jour apportée à l’enregistrement. |
| TotalUpdatesMissing |Nombre total de mises à jour manquantes sur l’ordinateur. |
| WindowsUpdateAgentVersion |Numéro de version de l’agent Windows Update sur l’ordinateur. |
| WindowsUpdateSetting |Paramètre définissant le mode d’installation des mises à jour importantes adopté par l’ordinateur.<br>Les valeurs possibles sont les suivantes :<br>- Désactivé<br>- Notify before installation (Notifier avant l’installation)<br>- Scheduled installation (Installation planifiée) |
| WSUSServer |URL du serveur WSUS, si l’ordinateur est configuré pour en utiliser un. |

## <a name="sample-log-searches"></a>Exemples de recherches de journaux
Le tableau suivant fournit des exemples de recherches de journaux pour les enregistrements de mises à jour collectés par cette solution.

| Requête | Description |
| --- | --- |
| Type:Update OSType!=Linux UpdateState=Needed Optional=false Approved!=false &#124; measure count() by Computer |Ordinateurs serveur Windows nécessitant des mises à jour |
| Type:Update OSType=Linux UpdateState!="Not needed" &#124; measure count() by Computer |Serveurs Linux nécessitant des mises à jour | 
| Type=Update UpdateState=Needed Optional=false &#124; select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate |Ensemble des ordinateurs avec des mises à jour manquantes |
| Type=Update UpdateState=Needed Optional=false Computer="ORDINATEUR01.contoso.com" &#124; select Computer,Title,KBID,Product,UpdateSeverity,PublishedDate |Mises à jour manquantes pour un ordinateur spécifique (remplacer la valeur par le nom de votre ordinateur)|
| Type=Update UpdateState=Needed Optional=false (Classification="Mises à jour de sécurité" OR Classification="Mises à jour critiques") |Ensemble des ordinateurs avec des mises à jour critiques ou de sécurité manquantes | 
| Type=Update UpdateState=Needed Optional=false (Classification="Mises à jour de sécurité" OR Classification="Mises à jour critiques") Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual &#124; Distinct Computer} &#124; Distinct KBID |Mises à jour critiques ou de sécurité nécessaires sur les machines où des mises à jour ont été appliquées manuellement |
| Type=Event EventLevelName=error Computer IN {Type=Update (Classification="Mises à jour critiques" OR Classification="Mises à jour critiques") UpdateState=Needed Optional=false &#124; Distinct Computer} |Événements d’erreur pour les machines où des mises à jour critiques ou de sécurité obligatoires sont manquantes |
| Type=Update Optional=false Classification="Correctifs cumulatifs" UpdateState=Needed &#124; select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate |Ensemble des ordinateurs avec des correctifs cumulatifs | 
| Type=Update UpdateState=Needed Optional=false &#124; Distinct Title |Mises à jour manquantes distinctes sur tous les ordinateurs | 
| Type:UpdateRunProgress InstallationStatus=failed &#124; measure count() by Computer, Title, UpdateRunName |Ordinateur serveur Windows avec des mises à jour ayant échoué pendant l’exécution d’une mise à jour | 
| Type:UpdateRunProgress InstallationStatus=failed &#124; measure count() by Computer, Product, UpdateRunName |Serveur Linux avec des mises à jour ayant échoué pendant l’exécution d’une mise à jour | 
| Type=UpdateSummary &#124; measure count() by WSUSServer |Appartenance des ordinateurs WSUS | 
| Type=UpdateSummary &#124; measure count() by WindowsUpdateSetting |Configuration de la mise à jour automatique | 
| Type=UpdateSummary WindowsUpdateSetting=Manual |Ordinateurs où la mise à jour automatique est désactivée | 
| Type=Update and OSType=Linux and UpdateState!="Non requis" &#124; measure count() by Computer |Liste de tous les ordinateurs Linux pour lesquels une mise à jour de package est disponible | 
| Type=Update and OSType=Linux and UpdateState!="Non requis" and (Classification="Mises à jour critiques" OR Classification="Mises à jour de sécurité") &#124; measure count() by Computer |Liste de tous les ordinateurs Linux pour lesquels une mise à jour de package corrigeant une vulnérabilité critique ou de sécurité est disponible | 
| Type=Update and OSType=Linux and UpdateState!="Non requis" |Liste de tous les packages pour lesquels une mise à jour est disponible | 
| Type=Update  and OSType=Linux and UpdateState!="Non requis" and (Classification="Mises à jour critiques" OR Classification="Mises à jour de sécurité") |Liste de tous les packages pour lesquels une mise à jour de package corrigeant une vulnérabilité critique ou de sécurité est disponible | 
| Type:UpdateRunProgress &#124; measure Count() by UpdateRunName |Répertorier les déploiements de mises à jour ayant modifié des ordinateurs | 
| Type:UpdateRunProgress UpdateRunName="DeploymentName" &#124; measure Count() by Computer |Ordinateurs mis à jour dans cette mise à jour (remplacer la valeur par le nom de votre déploiement de mises à jour) | 
| Type=Update and OSType=Linux and OSName = Ubuntu &#124; measure count() by Computer |Liste de tous les ordinateurs « Ubuntu » avec une mise à jour disponible |

## <a name="integrate-with-system-center-configuration-manager"></a>Intégrer avec System Center Configuration Manager

Les clients qui ont investi dans System Center Configuration Manager pour gérer des PC, serveurs et autres appareils mobiles s’appuient aussi sur sa puissance et sa maturité pour gérer les mises à jour logicielles dans le cadre de leur cycle de gestion des mises à jour logicielles.

Pour découvrir comment intégrer la solution OMS Update Management avec System Center Configuration Manager, consultez l’article [Intégrer System Center Configuration Manager avec OMS Update Management](../automation/oms-solution-updatemgmt-sccmintegration.md).

## <a name="troubleshooting"></a>Résolution des problèmes

Cette section fournit des informations pour vous aider à résoudre les problèmes liés à la solution de gestion de mises à jour.

### <a name="how-do-i-troubleshoot-onboarding-issues"></a>Comment puis-je résoudre les problèmes d’intégration ?
Si vous rencontrez des problèmes lorsque vous essayez d’intégrer la solution ou une machine virtuelle, recherchez dans le journal d’évènements **Journaux des applications et des services\Operations Manager** les événements avec l’ID d’événement 4502 et le message d’événement contenant **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.  Le tableau suivant met en évidence des messages d’erreur spécifiques et une résolution possible pour chacun d’entre eux.  

| Message | Motif | Solution |   
|----------|----------|----------|  
| Unable to Register Machine for Patch Management,<br>Registration Failed with Exception<br>System.InvalidOperationException: {"Message":"Machine is already<br>registered to a different account. "} (Impossible d’inscrire la machine pour la gestion des correctifs, l’inscription a échoué avec l’exception System.InvalidOperationException : {"Message" :"La machine est déjà inscrite sur un autre compte."} | La machine est déjà intégrée à un autre espace de travail pour Update Management | Éliminez les anciens artefacts en [supprimant le groupe de Runbooks hybrides](../automation/automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)|  
| Unable to  Register Machine for Patch Management,<br>Registration Failed with Exception<br>System.Net.Http.HttpRequestException: An error occurred while sending the request. ---><br>System.Net.WebException: The underlying connection<br>was closed: An unexpected error<br>occurred on a receive. ---> System.ComponentModel.Win32Exception:<br>The client and server cannot communicate,<br>because they do not possess a common algorithm (Impossible d’inscrire la machine pour la gestion des correctifs, l’inscription a échoué avec l’exception System.Net.Http.HttpRequestException : une erreur s’est produite lors de l’envoi de la requête. --->System.Net.WebException : la connexion sous-jacente était fermée : une erreur inattendue s’est produite à la réception. ---> System.ComponentModel.Win32Exception : le client et le serveur ne peuvent pas communiquer car ils n’ont pas d’algorithme en commun) | Proxy/passerelle/pare-feu bloquant la communication | [Passez en revue la configuration requise pour le réseau](../automation/automation-offering-get-started.md#network-planning)|  
| Unable to Register Machine for Patch Management,<br>Registration Failed with Exception<br>Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value. (Impossible d’inscrire la machine pour la gestion des correctifs, l’inscription a échoué avec l’exception Newtonsoft.Json.JsonReaderException : erreur d’analyse de valeur infinie positive.) | Proxy/passerelle/pare-feu bloquant la communication | [Passez en revue la configuration requise pour le réseau](../automation/automation-offering-get-started.md#network-planning)| 
| The certificate presented by the service <wsid>.oms.opinsights.azure.com<br>was not issued by a certificate authority<br>used for Microsoft services. Please contact<br>your network administrator to see if they are running a proxy that intercepts<br>TLS/SSL communication. (Le certificat présenté par le service .oms.opinsights.azure.com n’a pas été émis par une autorité de certification utilisée par les services Microsoft. Veuillez contacter votre administrateur réseau pour déterminer si un proxy en cours d’exécution intercepte la communication TLS/SSL.) |Proxy/passerelle/pare-feu bloquant la communication | [Passez en revue la configuration requise pour le réseau](../automation/automation-offering-get-started.md#network-planning)|  
| Unable to Register Machine for Patch Management,<br>Registration Failed with Exception<br>AgentService.HybridRegistration.<br>PowerShell.Certificates.CertificateCreationException:<br>Failed to create a self-signed certificate. ---><br>System.UnauthorizedAccessException: Access is denied. (Impossible d’inscrire la machine pour la gestion des correctifs, l’inscription a échoué avec l’exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException : échec de la création d’un certificat auto-signé. --->System.UnauthorizedAccessException : accès refusé.) | Échec de génération du certificat auto-signé | Vérifiez que le compte système a<br>un accès en lecture au dossier :<br>**C:\ProgramData\Microsoft\**<br>**Crypto\RSA**|  

### <a name="how-do-i-troubleshoot-update-deployments"></a>Comment résoudre les déploiements de mises à jour ?
Vous pouvez afficher les résultats du runbook chargé du déploiement des mises à jour incluses dans le déploiement de mises à jour planifié dans le panneau Tâches de votre compte Automation lié à l’espace de travail OMS prenant en charge cette solution.  Le runbook **Patch-MicrosoftOMSComputer** est un runbook enfant qui cible un ordinateur géré spécifique. Le flux détaillé présente des informations détaillées sur ce déploiement.  La sortie indique les mises à jour obligatoires applicables, l’état du téléchargement, l’état de l’installation et des détails supplémentaires.<br><br> ![Statut de tâche du déploiement de mises à jour](media/oms-solution-update-management/update-la-patchrunbook-outputstream.png)<br>

Pour plus d’informations, consultez [Sortie et messages de runbook Automation](../automation/automation-runbook-output-and-messages.md).   

## <a name="next-steps"></a>Étapes suivantes
* Utilisez les recherches de journaux de [Log Analytics](../log-analytics/log-analytics-log-searches.md) pour afficher des données détaillées sur les mises à jour.
* [Créez vos propres tableaux de bord](../log-analytics/log-analytics-dashboards.md) affichant la conformité des mises à jour de vos ordinateurs gérés.
* [Créez des alertes](../log-analytics/log-analytics-alerts.md) lorsque des mises à jour critiques sont détectées comme manquantes sur des ordinateurs ou lorsque les mises à jour automatiques sont désactivées sur un ordinateur.  
