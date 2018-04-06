---
title: Surveiller et résoudre les problèmes d’Azure Site Recovery | Microsoft Docs
description: Surveiller et résoudre les opérations et les problèmes de réplication d’Azure Site Recovery à l’aide du portail
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: bsiva
ms.openlocfilehash: b357a3231dac6dfa54cb02fe921baf771c0880f4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="monitoring-and-troubleshooting-azure-site-recovery"></a>Surveillance et résolution des problèmes d’Azure Site Recovery

Dans cet article, vous allez apprendre à utiliser les fonctionnalités intégrées de surveillance et de résolution des problèmes d’Azure Site Recovery. Découvrez comment :
> [!div class="checklist"]
> - Utiliser le tableau de bord d’Azure Site Recovery (page Vue d’ensemble du coffre)
> - Surveiller et résoudre les problèmes de réplication
> - Surveiller les travaux et opérations d’Azure Site Recovery
> - S’abonner aux notifications par courrier électronique

## <a name="using-the-azure-site-recovery-dashboard"></a>Utilisation du tableau de bord d’Azure Site Recovery

Le tableau de bord d’Azure Site Recovery sur la page Vue d’ensemble du coffre regroupe toutes les informations de surveillance du coffre dans un seul emplacement. Démarrez avec le tableau de bord du coffre et obtenez davantage de détails en parcourant les différentes parties du tableau de bord. Les principales parties du tableau de bord d’Azure Site Recovery sont les suivantes :

### <a name="1-switch-between-azure-backup-and-azure-site-recovery-dashboards"></a>1. Basculer entre les tableaux de bord d’Azure Backup et d’Azure Site Recovery

Le bouton bascule en haut de la page Vue d’ensemble permet de basculer entre les pages du tableau de bord d’Azure Site Recovery et du tableau de bord d’Azure Backup. Une fois effectuée, cette sélection est mémorisée et devient le paramètre par défaut lors de votre prochaine ouverture de la page Vue d’ensemble du coffre. Sélectionnez l’option Site Recovery pour afficher le tableau de bord de Site Recovery. 

Les différentes parties du tableau de bord d’Azure Site Recovery s’actualisent automatiquement toutes les 10 minutes, pour afficher les dernières informations disponibles.

![Fonctionnalités de surveillance dans la page Vue d’ensemble d’Azure Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png)

### <a name="2-replicated-items"></a>2. Éléments répliqués

Dans le tableau de bord, la section des éléments répliqués présente une vue d’ensemble de l’intégrité de la réplication des serveurs protégés dans le coffre. 

<table>
<tr>
    <td>Healthy</td>
    <td>La réplication progresse normalement pour ces serveurs et aucun avertissement ou erreur n’a été détectée.</td>
</tr>
<tr>
    <td>Avertissement </td>
    <td>Un ou plusieurs avertissements, qui peuvent avoir un impact sur la réplication ou indiquer que la réplication ne se déroule pas normalement, ont été détectés pour ces serveurs.</td>
</tr>
<tr>
    <td>Critique</td>
    <td>Une ou plusieurs erreurs de réplication critiques ont été détectées pour ces serveurs. Ces erreurs indiquent que la réplication est soit bloquée, soit évolue moins vite que le rythme de modification des données pour ces serveurs.</td>
</tr>
<tr>
    <td>Non applicable</td>
    <td>Serveurs qui ne devraient pas être en cours de réplication, comme des serveurs qui ont subi un basculement.</td>
</tr>
</table>

Pour afficher la liste des serveurs protégés filtrés par intégrité de la réplication, cliquez sur la description d’intégrité de la réplication en regard de l’anneau. Le lien qui permet de tout afficher, à côté du titre de la section est un raccourci vers la page des éléments répliqués du coffre. Utilisez-le pour afficher la liste de tous les serveurs dans le coffre.

### <a name="3-failover-test-success"></a>3. Réussite du test de basculement

Dans le tableau de bord, la section Réussite du test de basculement présente une répartition des machines virtuelles dans le coffre en fonction de l’état du test de basculement. 

<table>
<tr>
    <td>Test recommandé</td>
    <td>Machines virtuelles qui n’ont pas subi un basculement de test réussi depuis qu’elle sont dans un état protégé.</td>
</tr>
<tr>
    <td>Effectué</td>
    <td>Machines virtuelles qui ont connu un ou plusieurs tests de basculement réussis.</td>
</tr>
<tr>
    <td>Non applicable</td>
    <td>Machines virtuelles actuellement non éligibles à un test de basculement. Exemples : serveurs basculés, serveurs dont la réplication initiale est en cours, serveurs pour lesquels un basculement est en cours, serveurs pour lesquels un test de basculement est déjà en cours.</td>
</tr>
</table>

Cliquez sur l’état du test de basculement en regard de l’anneau pour afficher la liste des serveurs protégés en fonction de l’état de leur test de basculement.
 
> [!IMPORTANT]
> En général, il est recommandé d’effectuer un test de basculement sur vos serveurs protégés au moins une fois tous les six mois. Un test de basculement n’interrompt pas le fonctionnement de vos serveurs et de vos applications dans un environnement isolé et vous permet d’évaluer le niveau de préparation de votre entreprise à la continuité des activités.

 Un test de basculement sur un serveur ou un plan de récupération est considéré comme réussi uniquement à l’issue des opérations de basculement et de nettoyage.

### <a name="4-configuration-issues"></a>4. Problèmes de configuration

La section des problèmes de configuration affiche la liste des problèmes qui peuvent affecter votre capacité à réussir le basculement des machines virtuelles. Les classes de problèmes répertoriées dans cette section sont les suivantes :
 - **Configurations manquantes :** serveurs protégés n’ayant pas les configurations nécessaires, comme un réseau de récupération ou un groupe de ressources de récupération.
 - **Ressources manquantes :** ressources de cible/de récupération configurées introuvables ou indisponibles dans l’abonnement. Par exemple, la ressource a été supprimée ou migrée vers un autre abonnement ou un autre groupe de ressources. Les configurations de cible/de récupération suivantes sont recherchées : groupe de ressources de la cible, réseau virtuel et sous-réseau de la cible, compte de stockage de la cible/des journaux, groupe à haute disponibilité de la cible, adresse IP de la cible.
 - **Quota d’abonnement :** le quota de ressources disponibles dans l’abonnement est comparé au solde requis pour faire basculer toutes les machines virtuelles dans le coffre. Si le solde disponible est insuffisant, un rapport est généré. Les quotas des ressources Azure suivantes sont analysés : nombre de cœurs de machine virtuelle, nombre de cœurs de famille de machines virtuelles, nombre de cartes d’interface réseau.
 - **Mises à jour logicielles :** disponibilité des nouvelles mises à jour logicielles, versions logicielles dont la date d’expiration approche.

Les problèmes de configuration (autres que la disponibilité des mises à jour logicielles) sont détectés par une validation périodique qui s’exécute toutes les 12 heures par défaut. Vous pouvez forcer l’exécution immédiate de l’opération de validation en cliquant sur l’icône d’actualisation en regard de l’intitulé de la section *Problèmes de configuration*.

Cliquez sur les liens pour obtenir plus d’informations sur les problèmes répertoriés et les machines virtuelles concernées. Pour les problèmes ayant un impact sur certaines machines virtuelles, vous pouvez obtenir plus de détails en cliquant sur le lien **Nécessite une attention** situé sous la colonne des configurations cibles pour la machine virtuelle. Les détails incluent des recommandations sur la correction des problèmes détectés.

### <a name="5-error-summary"></a>5. Résumé des erreurs

La section Résumé des erreurs montre les erreurs de réplication actives qui peuvent avoir un impact sur la réplication des serveurs dans le coffre, ainsi que le nombre d’entités concernés par chaque erreur.

Les erreurs de réplication des serveurs dans un état critique ou d’avertissement de réplication figurent dans le tableau de résumé des erreurs. 

- Les erreurs ayant un impact sur les composants d’infrastructure locaux tels que la non-réception d’une pulsation du fournisseur Azure Site Recovery en cours d’exécution sur le serveur de configuration local, le serveur VMM ou l’hôte Hyper-V, sont répertoriées au début du résumé des erreurs
- Les erreurs de réplication qui ont un impact sur les serveurs protégés apparaissent ensuite. Le tableau de résumé des erreurs est trié par ordre décroissant de la gravité des erreurs, puis par ordre décroissant du nombre de serveurs concernés.
 

> [!NOTE]
> 
>  Un même serveur peut présenter plusieurs erreurs de réplication. Si plusieurs erreurs sont détectées sur un serveur, le serveur apparaît dans chaque liste d’erreurs correspondante. Une fois que le problème sous-jacent à l’origine de l’erreur est corrigé, les paramètres de réplication s’améliorent et l’erreur est résolue sur la machine virtuelle.
>
> > [!TIP]
> Le nombre de serveurs concernés est une information qui permet de comprendre si un même problème sous-jacent peut concerner plusieurs serveurs. Par exemple, un problème réseau peut avoir un impact sur tous les serveurs qui répliquent des données depuis un site local vers Azure. Dans ce cas, la résolution d’un problème sous-jacent permet clairement de corriger la réplication sur plusieurs serveurs.
>

### <a name="6-infrastructure-view"></a>6. Vue de l’infrastructure

La vue de l’infrastructure affiche une représentation visuelle des composants d’infrastructure impliqués dans la réplication. Elle indique également l’intégrité de la connectivité entre les différents serveurs et entre les serveurs et les services Azure impliqués dans la réplication. 

Une ligne verte indique que la connexion est opérationnelle, tandis qu’une ligne rouge avec l’icône d’erreur superposée signale une ou plusieurs erreur ayant un impact sur la connectivité entre les composants impliqués. Placez le pointeur de la souris sur l’icône d’erreur pour afficher l’erreur et le nombre d’entités concernées. 

Cliquez sur l’icône d’erreur pour obtenir la liste filtrée des entités concernées par la ou les erreurs.

![Vue d’infrastructure de Site Recovery (coffre)](media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

> [!TIP]
> Assurez-vous que les composants de l’infrastructure locale (serveur de configuration, serveurs de traitement supplémentaires, machines virtuelles VMware en cours de réplication, hôtes Hyper-V, serveurs VMM) exécutent la dernière version du logiciel d’Azure Site Recovery. Pour pouvoir utiliser toutes les fonctionnalités de la vue d’infrastructure, vous devez exécuter le [Correctif cumulatif 22](https://support.microsoft.com/help/4072852) ou version ultérieure d’Azure Site Recovery

Pour utiliser la vue d’infrastructure, sélectionnez le scénario de réplication appropriée (machines virtuelles Azure, machines virtuelles/physiques VMware ou Hyper-V) selon votre environnement source. La vue d’infrastructure présentée dans la page Vue d’ensemble est une vue agrégée du coffre. Vous pouvez analyser plus finement chaque composants en cliquant sur les cases.

Une vue d’infrastructure limitée à une seule machine en cours de réplication est disponible dans la page Vue d’ensemble des éléments répliqués. Pour accéder à la page Vue d’ensemble d’un serveur en cours de réplication, accédez à la section Éléments répliqués du menu du coffre et sélectionnez le serveur concerné.

### <a name="infrastructure-view---faq"></a>Vue d’infrastructure - FAQ

**Q.** Pourquoi la vue d’infrastructure de ma machine virtuelle ne s’affiche-t-elle pas ? </br>
**A.** La fonctionnalité de vue d’infrastructure n’est disponible que pour les machines virtuelles qui sont répliquées vers Azure. Elle n’est actuellement pas disponible pour les machines virtuelles qui se répliquent entre des sites locaux.

**Q.** Pourquoi le nombre de machines virtuelles dans la vue d’infrastructure du coffre diffère-t-il du nombre total affiché dans l’anneau des éléments répliqués ?</br>
**A.** La vue d’infrastructure du coffre est limitée par les scénarios de réplication. Seules les machines virtuelles participant au scénario de réplication sélectionné sont incluses dans le nombre de machines virtuelles affichées dans la vue d’infrastructure. De plus, pour le scénario sélectionné, seules les machines virtuelles qui sont actuellement configurées pour répliquer sur Azure sont également incluses dans le nombre de machines virtuelles affichées dans la vue d’infrastructure (par exemple, parmi les machines virtuelles basculées, celles qui se répliquent sur un site local ne figurent pas dans la vue d’infrastructure).

**Q.** Pourquoi le nombre d’éléments répliqués affichés dans la partie des éléments principaux sur la page Vue d’ensemble diffère-t-il du nombre total d’éléments répliqués indiqués dans le graphique en anneau sur le tableau de bord ?</br>
**A.** Seules les machines virtuelles sur lesquelles la réplication initiale est terminée sont incluses dans le nombre indiqué dans la section des éléments principaux. Le nombre total d’éléments répliqués inclut toutes les machines virtuelles dans le coffre, y compris les serveurs dont la réplication initiale est en cours.

**Q.** Pour quels scénarios de réplication la vue d’infrastructure est-elle disponible ? </br>
**A.**
>[!div class="mx-tdBreakAll"]
>|Scénario de réplication  | État de la machine virtuelle  | Vue d’infrastructure disponible  |
>|---------|---------|---------|
>|Machines virtuelles en cours de réplication entre deux sites locaux     | -        | Non       |
>|Tous     | Basculement         |  Non        |
>|Machines virtuelles en cours de réplication entre deux régions Azure     | Réplication initiale en cours ou protégée         | OUI         |
>|Machines virtuelles VMware virtual en cours de réplication sur Azure     | Réplication initiale en cours ou protégée        | OUI        |
>|Machines virtuelles VMware virtual en cours de réplication sur Azure     | Machines virtuelles basculées en cours de réplication sur un site VMware local         | Non         |
>|Machines virtuelles Hyper-V en cours de réplication sur Azure     | Réplication initiale en cours ou protégée        | OUI       |
>|Machines virtuelles Hyper-V en cours de réplication sur Azure     | Basculement/restauration automatique en cours        |  Non        |


### <a name="7-recovery-plans"></a>7. Plans de récupération

La section Plans de récupération indique le nombre de plans de récupération dans le coffre. Cliquez sur le numéro pour afficher la liste des plans de récupération, créer ou modifier des plans de récupération. 

### <a name="8-jobs"></a>8. Tâches

Les travaux d’Azure Site Recovery suivent l’état des opérations d’Azure Site Recovery. La plupart des opérations dans Azure Site Recovery s’exécutent de façon asynchrone, avec un travail de suivi utilisé pour suivre l’état d’avancement de l’opération.  Pour savoir comment surveiller l’état d’une opération, consultez la section [Surveiller les travaux/opérations d’Azure Site Recovery](#monitor-azure-site-recovery-jobsoperations).

La section Travaux du tableau de bord fournit les informations suivantes :

<table>
<tr>
    <td>Échec</td>
    <td>Travaux d’Azure Site Recovery ayant échoué dans les dernières 24 heures</td>
</tr>
<tr>
    <td>En cours</td>
    <td>Travaux d’Azure Site Recovery en cours</td>
</tr>
<tr>
    <td>En attente d’entrée</td>
    <td>Travaux d’Azure Site Recovery en attente d’une entrée de l’utilisateur.</td>
</tr>
</table>

Le lien Afficher tout en regard de l’en-tête de section est un raccourci vers la page de liste de tâches.

## <a name="monitor-and-troubleshoot-replication-issues"></a>Surveiller et résoudre les problèmes de réplication

Outre les informations disponibles dans la page du tableau de bord du coffre, vous pouvez obtenir des informations supplémentaires et des informations de dépannage dans la page de liste des machines virtuelles et la page des détails de la machine virtuelle. Vous pouvez afficher la liste des machines virtuelles protégées dans le coffre en sélectionnant l’option **Éléments répliqués** dans le menu du coffre. L’autre solution consiste à afficher la liste filtrée des éléments protégés en cliquant sur l’un des raccourcis limités disponibles sur la page du tableau de bord du coffre.

![Vue de la liste des éléments répliqués dans Azure Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

L’option de filtrage dans la page des éléments répliqués vous permet d’appliquer différents filtres tels que l’intégrité de réplication et la stratégie de réplication. 

L’option de sélection de colonnes vous permet de spécifier les colonnes supplémentaires à afficher comme RPO, les problèmes de configuration de cible et les erreurs de réplication. Vous pouvez lancer des opérations sur une machine virtuelle ou afficher les erreurs qui ont un impact sur la machine virtuelle en cliquant avec le bouton droit sur une ligne de la liste des machines.

Pour obtenir des informations plus précises, sélectionnez une machine virtuelle en cliquant dessus. La page des détails de la machine virtuelle s’ouvre. La page Vue d’ensemble sous les détails de la machine virtuelle contient un tableau de bord où vous trouverez des informations supplémentaires sur la machine. 

La page Vue d’ensemble de la machine en cours de réplication indique les informations suivantes :
- RPO (objectif de point de récupération) : RPO actuel de la machine virtuelle et heure du dernier calcul du RPO.
- Derniers points de récupération disponibles pour la machine
- Problèmes de configuration qui peuvent avoir un impact sur la préparation du basculement de la machine. Cliquez sur le lien pour obtenir plus d’informations.
- Détails de l’erreur : liste des erreurs de réplication actuellement observées sur la machine, ainsi que les causes possibles et les solutions recommandées
- Événements : liste chronologique des événements récents ayant un impact sur la machine. Si la colonne Détails de l’erreur indique les erreurs actuellement observables sur la machine, la colonne Événements est un enregistrement historique de différents événements qui peuvent avoir un impact sur la machine, notamment les erreurs susceptibles d’avoir été constatées sur la machine.
- Vue d’infrastructure des machines en cours de réplication sur Azure

![Vue d’ensemble/détails des éléments répliqués d’Azure Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

Le menu Action en haut de la page fournit des options permettant d’effectuer diverses opérations comme un test de basculement sur la machine virtuelle. Le bouton Détails de l’erreur dans le menu Action permet d’afficher toutes les erreurs actives, notamment les erreurs de réplication, les problèmes de configuration et les avertissements relatives aux meilleures de configuration pour la machine virtuelle.

> [!TIP]
> En quoi l’objectif de point de récupération (ou RPO) est-il différent du dernier point de récupération disponible ?
> 
>Azure Site Recovery utilise un processus asynchrone en plusieurs étapes pour répliquer des machines virtuelles sur Azure. À l’avant-dernière étape de la réplication, les modifications récentes apportées à la machine virtuelle, ainsi que les métadonnées, sont copiées dans un compte de stockage de cache/du journal. Une fois que ces modifications, ainsi que l’étiquette identifiant un point récupérable, sont écrites dans le compte de stockage de la région cible, Azure Site Recovery dispose des informations nécessaires afin de générer un point récupérable pour la machine virtuelle. À ce stade, le RPO a été atteint pour les modifications téléchargées jusqu’à présent au compte de stockage. En d’autres termes, le RPO de la machine virtuelle, à ce stade exprimé en unités de temps, est égal au temps écoulé depuis l’horodatage correspondant au point récupérable.
>
>Le service Azure Site Recovery fonctionne en arrière-plan, récupère les données téléchargées à partir du compte de stockage et les applique aux disques de réplica créés pour la machine virtuelle. Ensuite, il génère un point de récupération et le fournit pour effectuer la récupération lors du basculement. Le dernier point de récupération disponible indique l’horodatage correspondant au dernier point de récupération déjà traité et appliqué aux disques de réplica.
>> [!WARNING]
> Une horloge asymétrique ou une heure système incorrecte sur la machine source en cours de réplication ou les serveurs d’infrastructure locaux fausse la valeur de RPO calculée. Pour garantir la précision des valeurs de RPO, vérifiez que l’horloge système sur les serveurs impliqués dans la réplication est exacte. 
>

## <a name="monitor-azure-site-recovery-jobsoperations"></a>Surveiller les travaux et opérations d’Azure Site Recovery

Azure Site Recovery exécute les opérations spécifiées de manière asynchrone. Les exemples d’opérations que vous pouvez effectuer sont activer la réplication, créer le plan de récupération, tester le basculement, mettre à jour les paramètres de réplication, etc. Chacun de ces opérations a un travail correspondant créé pour assurer le suivi et l’audit de l’opération. L’objet de travail dispose de toutes les informations nécessaires pour suivre l’état et la progression de l’opération. Vous pouvez suivre l’état des différentes opérations Azure Site Recovery du votre, dans la page Travaux. 

Pour voir la liste des travaux Azure Site Recovery du coffre, consultez la section **Surveillance et rapports** du menu du coffre et sélectionnez Travaux > Travaux Site Recovery. Sélectionnez un travail dans la liste des travaux sur la page, en cliquant dessus pour obtenir plus d’informations sur le travail spécifié. Si un travail n’a pas abouti ou comporte des erreurs, vous pouvez obtenir plus d’informations sur l’erreur et la correction possible, en cliquant sur le bouton Détails de l’erreur en haut de la page Détails du travail (également accessible dans la page Travaux en cliquant avec le bouton droit de la souris sur le travail ayant échoué). Vous pouvez utiliser l’option de filtrage dans le menu Action en haut de la page des travaux pour filtrer la liste selon un critère spécifique et utiliser le bouton Exporter pour exporter les détails des travaux sélectionnés vers Excel. Vous pouvez également accéder à la liste des tâches à partir du raccourci disponible sur la page du tableau de bord d’Azure Site Recovery. 

 Pour les opérations que vous effectuez à partir du portail Azure, le suivi du travail créé et de son état sont également disponibles dans la section des notifications (icône de cloche en haut à droite) du portail Azure.

## <a name="subscribe-to-email-notifications"></a>S’abonner aux notifications par courrier électronique

La fonctionnalité intégrée de notification par courrier électronique vous permet de vous abonner pour recevoir des notifications sur les événements critiques par courrier électronique. Si vous vous abonnez, des notifications par courrier électronique sont envoyées pour les événements suivants :
- Intégrité de la réplication d’une machine qui se dégrade au point de devenir critique.
- Aucune connectivité entre les composants d’infrastructure locaux et le service Azure Site Recovery. La connectivité au service Site Recovery à partir des composants d’infrastructure locaux, comme le serveur de configuration (VMware) ou System Center Virtual Machine Manager(Hyper-V) inscrit dans le coffre est détectée par un mécanisme de pulsation.
- Échecs des opérations de basculement le cas échéant.

Pour recevoir des notifications par courrier électronique sur Azure Site Recovery, accédez à la section **Surveillance et rapports** du menu du coffre et :
1. Sélectionnez Alertes et événements > Événements Azure Site Recovery.
2. Sélectionnez « Notifications par courrier électronique » dans le menu en haut de la page d’événements ouverte.
3. Utilisez l’Assistant de notification par courrier électronique pour activer ou désactiver les notifications par courrier électronique et sélectionner les destinataires. Vous pouvez spécifier que tous les administrateurs d’abonnement reçoivent des notifications et/ou fournissent une liste d’adresses de messagerie auxquelles envoyer les notifications. 
