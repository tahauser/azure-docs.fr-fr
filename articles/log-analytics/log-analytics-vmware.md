---
title: Solution Analyse VMware dans Log Analytics | Microsoft Docs
description: "Découvrez comment la solution Analyse VMware peut vous aider à gérer les journaux et à surveiller les hôtes ESXi."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 16516639-cc1e-465c-a22f-022f3be297f1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: magoedte
ms.openlocfilehash: f54d24659ad13aa02462938711482326c5bf763c
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>Solution Analyse VMware (version préliminaire) dans Log Analytics

![Symbole VMware](./media/log-analytics-vmware/vmware-symbol.png)

La solution Analyse VMware dans Log Analytics vous aide à créer une approche centralisée de la journalisation et de l’analyse des journaux VMware volumineux. Cet article décrit comment dépanner, capturer et gérer les hôtes ESXi dans un emplacement unique à l’aide de la solution. La solution vous permet de consulter des données détaillées pour tous vos hôtes ESXi dans un emplacement unique. Vous pouvez voir le nombre, l’état et les tendances des principaux événements des hôtes de machine virtuelle et ESXi, fournis via les journaux d’hôte ESXi. Vous pouvez résoudre des problèmes en consultant des journaux d’hôte ESXi centralisés et en y effectuant des recherches. Et vous pouvez créer des alertes basées sur des requêtes de recherche de journal.

La solution utilise la fonctionnalité syslog native de l’hôte ESXi pour transmettre des données à une machine virtuelle cible, qui dispose de l’agent OMS. Toutefois, la solution n’écrit pas de fichiers dans syslog sur la machine virtuelle cible. L’agent OMS ouvre le port 1514 et l’écoute. Une fois les données reçues, l’agent OMS envoie les données dans Log Analytics.

## <a name="install-and-configure-the-solution"></a>Installer et configurer la solution
Utilisez les informations suivantes pour installer et configurer la solution.

* Ajoutez la solution VMware Monitoring à votre abonnement à l’aide de la procédure décrite dans [Ajouter une solution de gestion](log-analytics-add-solutions.md#add-a-management-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Hôtes VMware ESXi pris en charge
vSphere ESXi Host 5.5 et 6.0

#### <a name="prepare-a-linux-server"></a>Préparer un serveur Linux
Créez une machine virtuelle de système d’exploitation Linux pour recevoir toutes les données Syslog des hôtes ESXi. L’[Agent OMS Linux](log-analytics-linux-agents.md) est le point de regroupement de toutes les données syslog de l’hôte ESXi. Vous pouvez utiliser plusieurs hôtes ESXi pour transférer des journaux à un seul serveur Linux, comme dans l’exemple suivant.  

   ![Flux Syslog](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configurer la collecte Syslog
1. Configurez le transfert de Syslog à VSphere. Pour plus d’informations sur la configuration du transfert de Syslog, voir [Configuration de Syslog sur ESXi 5.x et 6.0 (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Accédez à **Configuration de l’hôte ESXi** > **Logiciel** > **Paramètres avancés** > **Syslog**.
   ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  
2. Dans le champ *Syslog.global.logHost*, ajoutez votre serveur Linux et le numéro de port *1514*. Par exemple, `tcp://hostname:1514` ou `tcp://123.456.789.101:1514`.
3. Ouvrez le pare-feu d’hôte ESXi pour Syslog. **Configuration de l’hôte ESXi** > **Logiciels** > **Profil de sécurité** > **Pare-feu**, puis ouvrez **Propriétés**.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  
4. Vérifiez sur la Console vSphere que ce Syslog est correctement configuré. Vérifiez sur l’hôte ESXI que le port **1514** est configuré.
5. Téléchargez et installez l’Agent OMS pour Linux sur le serveur Linux. Pour plus d’informations, consultez la [documentation de l’Agent OMS pour Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
6. Une fois l’Agent OMS pour Linux installé, accédez au répertoire /etc/opt/microsoft/omsagent/sysconf/omsagent.d, copiez le fichier vmware_esxi.conf vers le répertoire /etc/opt/microsoft/omsagent/conf/omsagent.d, puis modifiez le propriétaire/groupe et les autorisations du fichier. Par exemple : 

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
7. Redémarrez l’Agent OMS pour Linux en exécutant `sudo /opt/microsoft/omsagent/bin/service_control restart`.
8. Testez la connectivité entre le serveur Linux et de l’hôte ESXi en utilisant la commande `nc`sur l’hôte ESXi. Par exemple : 

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

9. Dans le portail Azure, effectuer une recherche de journal pour `VMware_CL`. Quand Log Analytics collecte les données Syslog, il conserve le format Syslog. Dans le portail, certains des champs sont capturés, tel que *Hostname* et *ProcessName*.  

    ![Type](./media/log-analytics-vmware/type.png)  

    Si vos résultats de recherche de vue de journal sont similaires à l’image ci-dessus, vous êtes prêt à utiliser le tableau de bord de la solution VMware Monitoring.  

## <a name="vmware-data-collection-details"></a>Détails sur la collecte de données Linux
La solution Analyse VMware collecte diverses mesures de performances et données de journaux à partir des hôtes ESXi à l’aide des Agents OMS pour Linux que vous avez activés.

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte.

| plateforme | Agent OMS pour Linux | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |Toutes les 3 minutes. |

Le tableau suivant affiche des exemples de champs de données collectés par la solution Analyse VMware :

| Nom du champ | Description |
| --- | --- |
| Device_s |appareils de stockage VMware |
| ESXIFailure_s |types d’échec |
| EventTime_t |heure à laquelle l’événement s’est produit |
| HostName_s |nom d’hôte ESXi |
| Operation_s |créer ou supprimer une machine virtuelle |
| ProcessName_s |nom de l’événement |
| ResourceId_s |nom de l’hôte VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Statut SCSI de VMware |
| SyslogMessage_s |données Syslog |
| UserName_s |utilisateur qui a créé ou supprimé la machine virtuelle |
| VMName_s |nom de la machine virtuelle |
| Ordinateur |ordinateur hôte |
| TimeGenerated |heure à laquelle les données ont été générées |
| DataCenter_s |centre de données VMware |
| StorageLatency_s |latence de stockage (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Présentation de la solution Analyse VMware
La vignette VMware s’affiche dans votre espace de travail Log Analytics. Elle fournit une vue d’ensemble des erreurs. Lorsque vous cliquez sur la vignette, vous accédez à l’affichage du tableau de bord.

![vignette](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Accédez à l’affichage du tableau de bord
Dans l’affichage du tableau de bord **VMware**, les panneaux sont organisés par :

* Nombre d’états d’échec
* Hôte principal par nombre d’événements
* Nombres d’événements principaux
* Activités de machine virtuelle
* Événements de disque de l’hôte ESXi

![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

Cliquez sur n’importe quel panneau pour ouvrir le volet de recherche de Log Analytics qui affiche des informations détaillées spécifiques pour le panneau.

À partir d’ici, vous pouvez modifier la requête de recherche pour l’adapter à un aspect spécifique. Pour plus d’informations sur la création de recherches de journaux, consultez [Trouver des données avec les recherches de journaux dans Log Analytics](log-analytics-log-searches.md).

#### <a name="find-esxi-host-events"></a>Rechercher des événements de l’hôte ESXi
Un seul hôte ESXi génère plusieurs journaux basés sur leurs processus. La solution Analyse VMware les centralise et résume les nombres d’événements. Cette vue centralisée vous permet de comprendre quel hôte ESXi a un volume élevé d’événements ainsi que les événements qui se produisent le plus fréquemment dans votre environnement.

![événement](./media/log-analytics-vmware/events.png)

Vous pouvez approfondir davantage en cliquant sur un hôte ESXi ou un type d’événement.

Lorsque vous cliquez sur un nom d’hôte ESXi, vous voyez les informations de cet hôte ESXi. Si vous souhaitez affiner des résultats avec le type d’événement, ajoutez `“ProcessName_s=EVENT TYPE”` à votre requête de recherche. Vous pouvez sélectionner **ProcessName** dans le filtre de recherche. Cela restreint les informations pour vous.

![explorer](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Rechercher les activités de machine virtuelle élevées
Une machine virtuelle peut être créée et supprimée sur tout hôte ESXi. Il est utile pour un administrateur d’identifier le nombre de machines virtuelles que crée un hôte ESXi. Cela aide ensuite à comprendre la planification des performances et de la capacité. Il est essentiel de suivre les événements d’activité de machine virtuelle lors de la gestion de votre environnement.

![explorer](./media/log-analytics-vmware/vmactivities1.png)

Si vous souhaitez voir d’autres données de création de machine virtuelle hôte ESXi, cliquez sur un nom d’hôte ESXi.

![explorer](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Requêtes de recherche courantes
La solution inclut d’autres requêtes utiles qui peuvent vous aider à gérer vos hôtes ESXi, telles que l’espace de stockage élevé, la latence du stockage et la défaillance de chemin.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![queries](./media/log-analytics-vmware/queries.png)


#### <a name="save-queries"></a>Enregistrer des requêtes
L’enregistrement des requêtes de recherche est une fonctionnalité standard de Log Analytics, qui peut vous aider à conserver toutes les requêtes que vous avez trouvées utiles. Après avoir créé une requête que vous trouvez utile, enregistrez-la en cliquant sur **Favorites**. Vous pouvez réutiliser facilement une requête enregistrée à partir de la page [Mon tableau de bord](log-analytics-dashboards.md) dans laquelle vous pouvez créer vos propres tableaux de bord personnalisés.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Créer des alertes à partir de requêtes
Après avoir créé vos requêtes, vous pouvez les utiliser pour vous avertir quand des événements spécifiques se produisent. Pour plus d’informations sur la création d’alertes, voir [Alertes dans Log Analytics](log-analytics-alerts.md). Pour obtenir des exemples de requêtes d’alerte et d’autres requêtes, voir le billet de blog [Monitor VMware using Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) (Analyser VMware à l’aide Log Analytics).

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Que dois-je faire avec les paramètres d’hôte ESXi ? Quel sera l’impact sur mon environnement actuel ?
La solution utilise le mécanisme de transfert syslog natif de l’hôte ESXi. Vous n’avez pas besoin d’autres logiciels Microsoft sur l’hôte ESXi pour capturer les journaux. L’impact sur votre environnement existant devrait être faible. Toutefois, vous devez absolument définir le transfert syslog, une fonctionnalité d’ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Dois-je redémarrer mon hôte ESXi ?
Non. Ce processus ne nécessite pas de redémarrage. Parfois, vSphere n’actualise pas correctement syslog. Dans ce cas, ouvrez une session sur l’hôte ESXi et rechargez le journal système. Encore une fois, il n’est pas obligatoire de redémarrer l’hôte. Ainsi, ce processus ne perturbe pas votre environnement.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Puis-je augmenter ou diminuer le volume de données de journal envoyé à Log Analytics ?
Oui, vous pouvez. Vous pouvez utiliser les paramètres de niveau de journal hôte ESXi dans vSphere. La collecte des journaux est basée sur le niveau *info*. Ainsi, si vous souhaitez vérifier la création ou la suppression de machines virtuelles, vous devez conserver le niveau *info* sur Hostd. Pour plus d’informations, consultez la [Base de connaissances VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Pourquoi Hostd ne fournit-il pas de données à Log Analytics ? Mon journal est défini sur info.
Il y a un problème avec l’hôte ESXi pour l’horodatage de syslog. Pour plus d’informations, consultez la [Base de connaissances VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Après avoir appliqué la solution de contournement, Hostd devrait fonctionner normalement.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Est-il possible que plusieurs hôtes ESXi transfèrent les données de syslog vers une seule machine virtuelle avec omsagent ?
Oui. Plusieurs hôtes ESXi peuvent transférer des données vers une seule machine virtuelle avec omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Pourquoi ne puis-je pas voir les données échangées avec Log Analytics ?
Il peut y avoir plusieurs raisons :

* L’hôte ESXi ne transmet pas correctement les données vers la machine virtuelle qui exécute omsagent. Pour tester cela, procédez comme suit :

  1. Pour vérifier cela, ouvrez une session sur l’hôte ESXi à l’aide de ssh et exécutez la commande suivante : `nc -z ipaddressofVM 1514`

      Si cela n’a pas réussi, les paramètres de vSphere dans la Configuration avancée ne sont probablement pas corrects. Consultez [Configurer la collecte Syslog](#configure-syslog-collection) pour plus d’informations sur la configuration de l’hôte ESXi pour le transfert de syslog.
  2. Si la connectivité du port syslog est correcte, mais que vous ne voyez toujours pas de données, rechargez syslog sur l’hôte ESXi à l’aide de ssh pour exécuter la commande suivante : ` esxcli system syslog reload`
* La machine virtuelle avec OMS Agent n’est pas configurée correctement. Pour tester cela, procédez comme suit :

  1. Log Analytics écoute le port 1514. Pour vérifier qu’il est ouvert, utilisez la commande suivante : `netstat -a | grep 1514`
  2. Vous devez voir le port `1514/tcp` ouvert. Si ce n’est pas le cas, vérifiez qu’omsagent est correctement installé. Si vous ne voyez pas les informations de port, le port syslog n’est pas ouvert sur la machine virtuelle.

    a. Vérifiez que l’agent OMS s’exécute à l’aide de `ps -ef | grep oms`. S’il n’est pas en cours d’exécution, démarrez le processus en exécutant la commande ` sudo /opt/microsoft/omsagent/bin/service_control start`

    b. Ouvrez le fichier `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` .

    c. Vérifiez que les paramètres de groupe et d’utilisateur sont valides, comme dans : `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

    d. Si le fichier n’existe pas ou que les paramètres de groupe et d’utilisateur sont incorrects, prenez une mesure corrective en [Préparant un serveur Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Étapes suivantes
* Utiliser des [recherches de journal](log-analytics-log-searches.md) dans Log Analytics pour afficher des données détaillées de l’hôte VMware.
* [Créer vos propres tableaux de bord](log-analytics-dashboards.md) affichant des données de l’hôte VMware.
* [Créer des alertes](log-analytics-alerts.md) lorsque des événements d’hôte VMware spécifiques se produisent.
