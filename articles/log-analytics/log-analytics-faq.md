---
title: Log Analytics - FAQ | Microsoft Docs
description: Réponses aux questions fréquemment posées sur le service Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: magoedte
ms.openlocfilehash: 398a62cbba952f35f29c1b1f411a6d5b901d2973
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="log-analytics-faq"></a>FAQ sur Log Analytics
Cette FAQ Microsoft consiste en une liste de questions fréquemment posées au sujet de Log Analytics dans Microsoft Azure. Si vous avez d’autres questions sur Log Analytics, rendez-vous sur le [forum de discussion](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) et publiez vos questions. Lorsqu’une question est fréquemment posée, nous l’ajoutons à cet article pour qu’elle soit facile et rapide à trouver.

## <a name="general"></a>Généralités

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>Q. Log Analytics utilise-t-il le même agent qu’Azure Security Center ?

R. Au début du mois de juin 2017, Azure Security Center a commencé à utiliser Microsoft Monitoring Agent pour collecter et stocker des données. Pour en savoir plus, consultez [Forum aux questions sur la migration de plateforme Azure Security Center](../security-center/security-center-platform-migration-faq.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>Q. Quels sont les contrôles effectués par les solutions AD et SQL Assessment ?

R. La requête suivante comporte une description de tous les contrôles effectués actuellement :

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Les résultats peuvent ensuite être exportés vers Excel pour être examinés.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>Q : Pourquoi vois-je autre chose qu’OMS dans la console System Center Operations Manager ?

R : Selon le correctif cumulatif d’Operations Manager que vous utilisez, vous pouvez voir un nœud pour *System Center Advisor*, *Operational Insights* ou *Log Analytics*.

La mise à jour de la chaîne de texte vers *OMS* est incluse dans un pack d’administration, qui doit être importé manuellement. Pour afficher le texte et les fonctionnalités actuels, suivez les instructions de l’article de la Base de connaissances sur le dernier correctif cumulatif de System Center Operations Manager et actualisez la console.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>Q : Existe-t-il une version locale de Log Analytics ?

R : Non. Log Analytics est un service cloud évolutif qui traite et stocke d’importants volumes de données. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>Q. Comment procéder au dépannage si Log Analytics ne collecte plus de données ?

R : Si vous utilisez le niveau tarifaire gratuit et que vous avez envoyé plus de 500 Mo de données le même jour, la collecte de données s’arrête pour le reste de la journée. La limite quotidienne est la principale raison pour laquelle Log Analytics arrête la collecte de données ou des données semblent manquantes.  

Log Analytics crée un événement de type *Pulsation* et peut être utilisé pour déterminer si la collecte de données s’arrête. 

Exécutez la requête suivante dans la recherche pour vérifier si vous atteignez la limite quotidienne et si des données sont manquantes :`Heartbeat | summarize max(TimeGenerated)`

Pour vérifier un ordinateur spécifique, exécutez la requête suivante : `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

Lorsque la collecte de données s’arrête, en fonction de l’intervalle de temps sélectionné, vous ne verrez pas les enregistrements retournés.   

Le tableau suivant décrit les raisons pour lesquelles la collecte de données s’arrête et suggère une action pour la reprendre :

| Raison pour laquelle la collecte de données s’arrête                       | Pour reprendre la collecte de données |
| -------------------------------------------------- | ----------------  |
| Limite de données gratuites atteinte<sup>1</sup>       | Attendez le mois suivant pour que la collecte redémarre automatiquement ou<br> Passez à un niveau tarifaire payant |
| Abonnement Azure à l’état interrompu pour la raison suivante : <br> Fin de l’essai gratuit <br> Expiration du Pass Azure <br> Limite de dépense mensuelle atteinte (par exemple, sur un abonnement MSDN ou Visual Studio)                          | Passer à un abonnement payant <br> Passer à un abonnement payant <br> Supprimer la limite ou attendre sa réinitialisation |

<sup>1</sup> Si votre espace de travail utilise le niveau tarifaire gratuit, vous êtes limité à 500 Mo de données envoyées au service par jour. Lorsque vous atteignez la limite quotidienne, la collecte de données s’arrête jusqu’au jour suivant. Les données envoyées pendant l’arrêt de la collecte de données ne sont pas indexées et ne sont pas accessibles à la recherche. Lorsque la collecte de données reprend, le traitement se produit uniquement pour les nouvelles données envoyées. 

Log Analytics utilise l’heure UTC ; chaque jour commence à minuit UTC. Si l’espace de travail atteint la limite quotidienne, le traitement reprend à la première heure du jour UTC suivant.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>Q. Comment être informé de l’arrêt de la collecte de données ?

R : Suivez les étapes décrites dans [Créer une règle d’alerte](log-analytics-alerts-creating.md#create-an-alert-rule) pour être averti lorsque la collecte de données s’arrête.

Lorsque vous créez l’alerte d’arrêt de la collecte de données, définissez les valeurs suivantes :
- **Nom** : *Collecte de données arrêtée*
- **Gravité** : *Avertissement*
- **Requête de recherche** sur `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
- **Fenêtre de temps** à *30 minutes*.
- **Fréquence des alertes** toutes les *dix* minutes.
- **Générer l’alerte selon** sur *nombre de résultats*
- **Nombre de résultats** sur *Greater than 0* (Supérieur à 0)

Cette alerte se déclenche lorsque la requête renvoie les résultats uniquement si les pulsations sont absentes pendant plus de 15 minutes.  Utilisez les étapes décrites dans [Ajouter des actions à des règles d’alerte dans Log Analytics](log-analytics-alerts-actions.md) pour configurer une action de messagerie, de webhook ou de runbook pour la règle d’alerte.

## <a name="configuration"></a>Configuration
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>Q. Puis-je modifier le nom du conteneur Blob ou Table utilisé pour lire sur Azure Diagnostics (WAD) ?

R. Non, il n’est pas possible pour le moment de lire des tables ou des conteneurs arbitraires dans le stockage Azure.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>Q. Quelles sont les adresses IP utilisées par le service Log Analytics ? Comment vérifier que le pare-feu autorise uniquement le trafic vers le service Log Analytics ?

R. Le service Log Analytics repose sur Azure. Les adresses IP Log Analytics se trouvent dans les [plages d’adresses IP des centres de données Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653).

Les adresses IP réelles du service Log Analytics changent au fil des déploiements. Les noms DNS autorisés par votre pare-feu sont documentées à l’adresse [Configurer les paramètres de pare-feu et de proxy dans Log Analytics](log-analytics-proxy-firewall.md).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>Q. J’utilise ExpressRoute pour me connecter à Azure. Mon trafic Log Analytics utilise-t-il ma connexion ExpressRoute ?

R. Les différents types de trafic ExpressRoute sont décrits dans la [documentation ExpressRoute](../expressroute/expressroute-faqs.md#supported-services).

Le trafic vers Log Analytics utilise le circuit ExpressRoute d’homologation publique.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>Q. Existe-t-il un moyen simple de déplacer un espace de travail Log Analytics existant vers un autre espace de travail Log Analytics ou un autre abonnement Azure ?

R. La cmdlet `Move-AzureRmResource` permet de déplacer un espace de travail Log Analytics ainsi qu’un compte Automation d’un abonnement Azure à un autre. Pour plus d’informations, consultez [Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Cette modification peut également être effectuée dans le portail Azure.

Vous ne pouvez pas déplacer les données d’un espace de travail Log Analytics vers un autre ou modifier la région de données dans laquelle les données Log Analytics sont stockées.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>Q : Comment ajouter Log Analytics à System Center Operations Manager ?

R : La mise à jour vers le dernier correctif cumulatif et l’importation de packs d’administration permettent de connecter Operations Manager à Log Analytics.

>[!NOTE]
>La connexion d’Operations Manager à Log Analytics n’est disponible que pour System Center Operations Manager 2012 SP1 et les versions ultérieures.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>Q : Comment vérifier qu’un agent est en mesure de communiquer avec Log Analytics ?

R : Pour s’assurer que l’agent peut communiquer avec OMS, accédez à : Panneau de configuration, Security & Settings (Sécurité et paramètres), **Microsoft Monitoring Agent**.

Sous l’onglet **Azure Log Analytics (OMS)** , recherchez une coche verte. Une icône en forme de coche verte confirme que l’agent est en mesure de communiquer avec le service Azure.

Une icône d’avertissement jaune signifie que l’agent rencontre des problèmes de communication avec Log Analytics. L’une des raisons courantes est que le service Microsoft Monitoring Agent s’est arrêté. Utilisez le Gestionnaire de contrôle des services pour redémarrer le service.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>Q : Comment empêcher un agent de communiquer avec Log Analytics ?

R : Dans System Center Operations Manager, supprimez l’ordinateur de la liste des ordinateurs managés par OMS. Operations Manager met à jour la configuration de l’agent pour qu’il ne fasse plus de rapports à Log Analytics. En ce qui concerne les agents connectés directement à Log Analytics, vous pouvez les empêcher de communiquer de la façon suivante : Panneau de configuration, Sécurité et paramètres, **Microsoft Monitoring Agent**.
Sous **Azure Log Analytics (OMS)**, supprimez tous les espaces de travail répertoriés.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>Q : Pourquoi une erreur se produit-elle lorsque j’essaie de déplacer mon espace de travail d’un abonnement Azure vers un autre ?

R : Si vous utilisez le Portail Azure, assurez-vous que seul l’espace de travail est sélectionné pour le déplacement. Ne sélectionnez pas les solutions : elles migreront automatiquement une fois l’espace de travail déplacé. 

Vérifiez que vous disposez de l’autorisation nécessaire dans les deux abonnements Azure.

## <a name="agent-data"></a>Données de l’agent
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>Q. Quelle quantité de données puis-je envoyer via l’agent à Log Analytics ? Existe-t-il une quantité maximale de données par client ?
R. Le forfait gratuit définit une limite quotidienne de 500 Mo par espace de travail. Les forfaits standard et premium ne présentent aucune limite concernant la quantité de données chargées. Comme un service cloud, Log Analytics est conçu pour monter en puissance automatiquement afin de traiter le volume provenant du client, même si cela se chiffre en téraoctets par jour.

L’agent Log Analytics a été conçu pour garantir un faible encombrement. Le volume de données varie selon les solutions activées. Vous trouverez des informations détaillées sur le volume de données ainsi que la répartition par solution sur la page [Utilisation](log-analytics-usage.md).

Pour en savoir plus, vous pouvez lire un [blog client](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) sur le faible encombrement de l’agent OMS.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>Q. Quelle est la quantité de bande passante réseau utilisée par Microsoft Management Agent (MMA) lorsqu’il envoie des données à Log Analytics ?

R. La bande passante est fonction de la quantité de données envoyées. Les données sont compressées à mesure de leur envoi sur le réseau

### <a name="q-how-much-data-is-sent-per-agent"></a>Q. Quelle est la quantité de données envoyées par agent ?

R. La quantité de données envoyées par agent dépend :

* des solutions activées ;
* du nombre de journaux et de compteurs de performances collectés ;
* du volume de données des journaux.

Le niveau tarifaire gratuit est un bon moyen d’intégrer plusieurs serveurs et de mesurer le volume de données classique. L’utilisation globale est indiquée sur la page [Utilisation](log-analytics-usage.md) .

Pour les ordinateurs en mesure d’exécuter l’agent Wire Data, lancez la requête suivante afin de connaître la quantité de données envoyées :

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Étapes suivantes
* [Familiarisez-vous avec Log Analytics](log-analytics-get-started.md) pour en savoir plus sur Log Analytics et être opérationnel en quelques minutes.
