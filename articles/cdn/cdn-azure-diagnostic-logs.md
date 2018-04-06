---
title: Journaux de diagnostic Azure | Microsoft Docs
description: Le client peut activer l’analyse des journaux pour Azure CDN.
services: cdn
documentationcenter: ''
author: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: v-deasim
ms.openlocfilehash: f9711f9cfaab1ef22da220a773689c95b1103970
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-diagnostic-logs"></a>Journaux de diagnostic Azure

Les journaux de diagnostic Azure permettent d’afficher l’analytique principale et de l’enregistrer dans une ou plusieurs destinations, dont les suivantes :

 - Compte de Stockage Azure
 - Hubs d'événements Azure
 - [Référentiel OMS Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Cette fonctionnalité est disponible pour tous les points de terminaison CDN appartenant à des profils CDN Verizon (Standard et Premium) et Akamai (Standard). 

Les journaux de diagnostic Azure vous permettent d’exporter des métriques d’utilisation de base de votre point de terminaison CDN vers diverses sources afin de pouvoir les utiliser de façon personnalisée. Vous pouvez par exemple exécuter les types suivants d’exportation de données :

- Exporter des données vers un stockage d’objets blob, exporter au format CSV et générer des graphiques dans Excel.
- Exporter des données vers des hubs d’événements et mettre les données en corrélation avec d’autres services Azure.
- Exporter des données vers Log Analytics et afficher les données dans votre propre espace de travail OMS

La figure suivante montre une vue des données au moyen de l’analytique principale CDN classique.

![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Figure 1 : Vue avec l’analytique principale CDN*

Pour plus d’informations sur les journaux de diagnostic, consultez [Journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-azure-portal"></a>Activation de la journalisation avec le portail Azure

Suivez les étapes suivantes pour activer la journalisation avec l’analytique principale CDN :

Connectez-vous au [Portail Azure](http://portal.azure.com). Si vous n’avez pas activé CDN pour votre flux de travail, [activez Azure CDN](cdn-create-new-endpoint.md) avant de continuer.

1. Dans le portail, accédez à **Profil CDN**.
2. Sélectionnez un profil CDN, puis sélectionnez le point de terminaison CDN pour lequel vous souhaitez activer les **journaux de diagnostic**.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Dans la section **Surveillance**, sélectionnez **Journaux de diagnostic**.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Activation de la journalisation avec Stockage Azure
    
1. Pour utiliser le stockage Azure pour stocker les journaux, cliquez sur **Archiver dans un compte de stockage**, puis sur **Analytique principale** et sélectionnez la durée de conservation en jours sous **Rétention (jours)**. Si la valeur zéro est appliquée à la rétention, les journaux sont stockés pour une durée indéfinie. 
2. Attribuez un nom au paramètre, puis cliquez sur **Compte de stockage**. Après avoir sélectionné un compte de stockage, cliquez sur **Enregistrer**.

![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png)

*Figure 2 : activation de la journalisation avec Stockage Azure*

### <a name="logging-with-oms-log-analytics"></a>Journalisation avec OMS Log Analytics

Pour stocker les journaux à l’aide d’OMS Log Analytics, effectuez les étapes suivantes :

1. Dans le panneau **Journaux de diagnostic**, sélectionnez **Envoyer à Log Analytics**. 

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Cliquez sur **Configurer** pour configurer la journalisation Log Analytics. Dans la boîte de dialogue Espaces de travail OMS, vous pouvez sélectionner un espace de travail antérieur ou en créer un.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Cliquez sur **Créer un espace de travail**.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/07_Create-new.png)

4. Entrez un nouveau nom d’espace de travail OMS. Les noms d’espace de travail OMS doivent être uniques et contenir uniquement des lettres, des chiffres et des traits d’union. Les espaces et les traits de soulignement ne sont pas autorisés. 
5. Sélectionnez ensuite un abonnement existant, un groupe de ressources (nouveau ou existant), un emplacement et un niveau tarifaire. Vous pouvez également épingler cette configuration à votre tableau de bord. Cliquez sur **OK** pour achever la configuration.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/08_Workspace-resource.png)

5.  Une fois l’espace de travail créé, la fenêtre des journaux de diagnostic s’affiche de nouveau. Confirmez le nom de votre nouvel espace de travail Log Analytics.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/09_Return-to-logging.png)

    Après avoir effectué la configuration de Log Analytics, vérifiez que vous avez bien sélectionné **CoreAnalytics**.

6. Cliquez sur **Enregistrer**.

7. Pour afficher votre nouvel espace de travail OMS, accédez à votre tableau de bord dans le portail Azure et cliquez sur le nom de votre espace de travail Log Analytics. Cliquez sur la vignette Portail OMS pour afficher votre espace de travail dans le référentiel OMS. 

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    Votre référentiel OMS est maintenant prêt à enregistrer des données. Pour utiliser ces données, vous devez recourir à une [solution OMS](#consuming-oms-log-analytics-data), comme indiqué plus loin dans cet article.

Pour plus d’informations sur les retards des données de journal, consultez [Retards des données de journal](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Activation de la journalisation avec PowerShell

L’exemple suivant montre comment activer les journaux de diagnostic via les applets de commande Azure PowerShell.

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Activation des journaux de diagnostic dans un compte de stockage

Commencez par vous connecter et sélectionner un abonnement :

    Login-AzureRmAccount 

    Select-AzureSubscription -SubscriptionId 


Pour activer les journaux de diagnostic dans un compte de stockage, utilisez cette commande :

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```
Pour activer les journaux de diagnostic dans un espace de travail OMS, utilisez cette commande :

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
```



## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Utilisation des journaux de diagnostic à partir de Stockage Azure
Cette section décrit le schéma de l’analytique principale CDN et leur organisation à l’intérieur d’un compte de stockage Azure, et fournit un exemple de code pour télécharger les journaux dans un fichier CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Utilisation de l’explorateur de stockage Microsoft Azure
Pour pouvoir accéder aux données d’analyse de base à partir du compte de stockage Azure, vous avez d’abord besoin d’un outil pour accéder au contenu dans un compte de stockage. Si plusieurs outils sont disponibles sur le marché, celui que nous recommandons est l’Explorateur de stockage Microsoft Azure. Pour télécharger l’outil, consultez [Explorateur de stockage Azure](http://storageexplorer.com/). Après le téléchargement et l’installation du logiciel, configurez-le pour utiliser le même compte de stockage Azure que celui qui a été configuré en tant que destination pour les journaux de diagnostic CDN.

1.  Ouvrez **l’explorateur de stockage Microsoft Azure**
2.  Localiser le compte de stockage
3.  Accédez au nœud **« Conteneurs d’objets Blob »** sous ce compte de stockage, puis développez le nœud
4.  Sélectionnez le conteneur nommé **« journaux-insights-coreanalytics »** et double-cliquez dessus.
5.  Les résultats s’affichent dans le volet droit, en commençant par le premier niveau, qui ressemble à **« resourceId= »**. Continuez à cliquer jusqu'à ce que vous voyiez le fichier **PT1H.json**. Consultez la remarque ci-dessous pour une explication du chemin d’accès.
6.  Chaque objet blob **PT1H.json** représente les journaux d’analyse pendant une heure pour un point de terminaison CDN spécifique ou son domaine personnalisé.
7.  Le schéma du contenu de ce fichier JSON est décrit dans la section Schéma des journaux Core Analytics


> [!NOTE]
> **Format du chemin d’accès des objets blob**
> 
> Les journaux Core Analytics sont générés toutes les heures et les données sont collectées et stockées dans un objet blob Azure unique sous forme de charge utile JSON. Étant donné que l’outil Explorateur Stockage interprète « / » comme un séparateur de répertoires et affiche la hiérarchie, le chemin d’accès à l’objet blob Azure apparaît sous forme de structure hiérarchique et représente le nom de l’objet blob. Ce nom d’objet blob suit la convention d’affectation de noms suivante : 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Description des champs :**

|value|description|
|-------|---------|
|Identifiant d’abonnement    |ID de l’abonnement Azure au format Guid.|
|Ressource |Nom de groupe   Nom du groupe de ressources auquel les ressources CDN appartiennent.|
|Nom de profil |Nom du profil CDN|
|Nom du point de terminaison |Nom du point de terminaison CDN|
|Year|  Représentation à 4 chiffres de l’année, par exemple, 2017|
|Mois| Représentation à 2 chiffres du mois de l’année. 01 = Janvier ... 12 = Décembre|
|jour|   Représentation à 2 chiffres du jour du mois|
|PT1H.json| Fichier JSON où sont effectivement stockées les données d’analyse|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportation des données de Core Analytics vers un fichier CSV

Pour faciliter l’accès à Core Analytics, nous fournissons un exemple de code pour un outil. Cet outil permet de télécharger les fichiers JSON dans un format de fichier plat avec séparation par virgules, qui peut être utilisé pour créer facilement des graphiques ou autres agrégations.

Voici comment vous pouvez utiliser l’outil :

1.  Visitez le lien GitHub : [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.  Téléchargez le code.
3.  Suivez les instructions pour la compilation et la configuration.
4.  Exécutez l’outil.
5.  Le fichier CSV résultant présente les données d’analyse dans une hiérarchie plate simple.

## <a name="consuming-diagnostics-logs-from-an-oms-log-analytics-repository"></a>Utilisation des journaux de diagnostics à partir d’un référentiel OMS Log Analytics
Log Analytics est un service d’Operations Management Suite (OMS) qui surveille vos environnements cloud et locaux et assure leur disponibilité et leurs performances. Il collecte les données générées par les ressources de votre cloud et de vos environnements locaux et d’autres outils d’analyse pour fournir une analyse sur plusieurs sources. 

Pour utiliser Log Analytics, vous devez [activer la journalisation](#enable-logging-with-azure-storage) dans le référentiel OMS Log Analytics (voir plus haut dans cet article).

### <a name="using-the-oms-repository"></a>Utilisation du référentiel OMS

 Le diagramme suivant illustre l’architecture des entrées et sorties du référentiel :

![Référentiel OMS Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Figure 3 : référentiel OMS Log Analytics*

Vous pouvez afficher les données de plusieurs façons à l’aide des solutions de gestion. Vous pouvez obtenir des solutions de gestion à partir de la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Vous pouvez installer des solutions de gestion à partir de la Place de marché Azure en cliquant sur le lien **Obtenir maintenant** au bas de chaque solution.

### <a name="adding-an-oms-cdn-management-solution"></a>Ajout d’une solution de gestion CDN d’OMS

Suivez ces étapes pour ajouter une solution de gestion :

1.   Si ce n’est pas déjà fait, connectez-vous au portail Azure à l’aide de votre abonnement Azure et accédez à votre tableau de bord.
    ![Tableau de bord Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Dans le panneau **Nouveau** sous **Marketplace**, sélectionnez **Surveillance + gestion**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Dans le panneau **Surveillance + gestion**, cliquez sur **Afficher tout**.

    ![Afficher tout](./media/cdn-diagnostics-log/15_See-all.png)

4.  Recherchez CDN dans la zone de recherche.

    ![Afficher tout](./media/cdn-diagnostics-log/16_Search-for.png)

5.  Sélectionnez **Analytique principale Azure CDN**. 

    ![Afficher tout](./media/cdn-diagnostics-log/17_Core-analytics.png)

6.  Après avoir cliqué sur **Créer**, vous êtes invité à créer un espace de travail OMS ou à en utiliser un existant. 

    ![Afficher tout](./media/cdn-diagnostics-log/18_Adding-solution.png)

7.  Sélectionnez l’espace de travail que vous avez créé. Vous devez ensuite ajouter un compte Automation.

    ![Afficher tout](./media/cdn-diagnostics-log/19_Add-automation.png)

8. L’écran suivant montre le formulaire de compte Automation que vous devez remplir. 

    ![Afficher tout](./media/cdn-diagnostics-log/20_Automation.png)

9. Après avoir créé le compte Automation, vous êtes prêt à ajouter votre solution. Cliquez sur le bouton **Créer** .

    ![Afficher tout](./media/cdn-diagnostics-log/21_Ready.png)

10. Votre solution a maintenant été ajoutée à votre espace de travail. Retournez à votre tableau de bord du portail Azure.

    ![Afficher tout](./media/cdn-diagnostics-log/22_Dashboard.png)

    Cliquez sur l’espace de travail Log Analytics que vous avez créé pour accéder à votre espace de travail. 

11. Cliquez sur la vignette **Portail OMS** pour afficher votre nouvelle solution dans le portail OMS.

    ![Afficher tout](./media/cdn-diagnostics-log/23_workspace.png)

12. Votre portail OMS doit maintenant ressembler à la capture d’écran suivante :

    ![Afficher tout](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Cliquez sur une des vignettes pour afficher plusieurs vues de vos données.

    ![Afficher tout](./media/cdn-diagnostics-log/25_Interior-view.png)

    Vous pouvez faire défiler l’écran vers la gauche ou la droite pour voir d’autres vignettes représentant des vues des données. 

    Vous pouvez cliquer sur une des vignettes pour obtenir des détails supplémentaires sur vos données.

     ![Afficher tout](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Offres et niveaux tarifaires

Vous pouvez voir des offres et des niveaux tarifaires pour les solutions de gestion OMS [ici](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Personnalisation des vues

Vous pouvez personnaliser la vue de vos données à l’aide du **Concepteur de vues**. Pour commencer la conception, accédez à votre espace de travail OMS, puis cliquez sur la vignette **Concepteur de vues**.

![Concepteur de vues](./media/cdn-diagnostics-log/27_Designer.png)

Vous pouvez faire glisser et déplacer les types de graphiques et renseigner le détail des données que vous souhaitez analyser.

![Concepteur de vues](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Retards des données de journal

Retards des données de journal Verizon | Retards des données de journal Akamai
--- | ---
Les données de journal Verizon sont retardées de 1 heure et ont besoin de jusqu'à 2 heures pour commencer à apparaître après l’achèvement de la propagation du point de terminaison. | Les données de journal Akamai sont retardées de 24 heures ; si la création remonte à plus de 24 heures, elles nécessitent jusqu'à 2 heures pour commencer à apparaître. Si elle a été effectuée récemment, il peut falloir jusqu’à 25 heures pour que les journaux commencent à apparaître.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Types de journaux de diagnostic pour l’analytique principale CDN

Nous proposons uniquement les journaux de l’analytique principale, qui contiennent les métriques affichant les statistiques de réponse HTTP et de sortie, comme dans les points de présence/périmètres CDN.

### <a name="core-analytics-metrics-details"></a>Détails des métriques de Core Analytics
Le tableau suivant répertorie les métriques disponibles dans les journaux de Core Analytics. Toutes les métriques ne sont pas disponibles auprès tous les fournisseurs, même si ces différences sont minimes. Le tableau suivant indique également si une métrique donnée est disponible à partir d’un fournisseur particulier. Notez que les métriques sont disponibles uniquement pour les points de terminaison CDN recevant du trafic.


|Métrique                     | Description   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |Nombre total d’occurrences de requêtes pendant cette période| OUI  |OUI   |
| RequestCountHttpStatus2xx |Nombre de toutes les requêtes qui ont abouti à un code HTTP 2xx (par ex. 200, 202)              | OUI  |OUI   |
| RequestCountHttpStatus3xx | Nombre de toutes les requêtes qui ont abouti à un code HTTP 3xx (par ex. 300, 302)              | OUI  |OUI   |
| RequestCountHttpStatus4xx |Nombre de toutes les requêtes qui ont abouti à un code HTTP 4xx (par ex. 400, 404)               | OUI   |OUI   |
| RequestCountHttpStatus5xx | Nombre de toutes les requêtes qui ont abouti à un code HTTP 5xx (par ex. 500, 504)              | OUI  |OUI   |
| RequestCountHttpStatusOthers |  Nombre d’occurrences de tous les autres codes HTTP (en dehors de 2xx-5xx) | OUI  |OUI   |
| RequestCountHttpStatus200 | Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 200              |Non    |OUI   |
| RequestCountHttpStatus206 | Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 206              |Non    |OUI   |
| RequestCountHttpStatus302 | Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 302              |Non    |OUI   |
| RequestCountHttpStatus304 |  Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 304             |Non    |OUI   |
| RequestCountHttpStatus404 | Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 404              |Non    |OUI   |
| RequestCountCacheHit |Nombre de toutes les requêtes qui ont abouti à un accès au cache. La ressource a été traitée directement du point de présence vers le client.               | OUI  |Non    |
| RequestCountCacheMiss | Nombre de toutes les requêtes qui ont abouti à un échec de cache. Cela signifie que la ressource est introuvable sur le point de présence le plus proche du client et a donc été récupérée à l’origine.              |OUI   | Non   |
| RequestCountCacheNoCache | Nombre de requêtes vers une ressource empêchées d’être mises en cache en raison d’une configuration de l’utilisateur sur le périmètre.              |OUI   | Non   |
| RequestCountCacheUncacheable | Nombre de requêtes de ressources empêchées d’être mises en cache par les en-têtes Cache-Control et Expires, qui indiquent qu’elles ne doivent pas être mises en cache sur un point de présence ou par le client HTTP                |OUI   |Non    |
| RequestCountCacheOthers | Nombre de toutes les requêtes avec un état du cache non traité ci-dessus.              |OUI   | Non   |
| EgressTotal | Transfert de données sortantes en Go              |OUI   |OUI   |
| EgressHttpStatus2xx | Transfert de données sortantes * pour les réponses avec des codes d’état HTTP 2xx en Go            |OUI   |Non    |
| EgressHttpStatus3xx | Transfert de données sortantes pour les réponses avec des codes d’état HTTP 3xx en Go              |OUI   |Non    |
| EgressHttpStatus4xx | Transfert de données sortantes pour les réponses avec des codes d’état HTTP 4xx en Go               |OUI   | Non   |
| EgressHttpStatus5xx | Transfert de données sortantes pour les réponses avec des codes d’état HTTP 5xx en Go               |OUI   |  Non  |
| EgressHttpStatusOthers | Transfert de données sortantes pour les réponses avec d’autres codes d’état HTTP en Go                |OUI   |Non    |
| EgressCacheHit |  Transfert de données sortantes pour les réponses qui ont été livrées directement depuis le cache CDN sur les points de présence/périmètres CDN  |OUI   |  Non  |
| EgressCacheMiss | Transfert des données sortantes pour les réponses introuvables sur le serveur le plus proche et récupérées à partir du serveur d’origine              |OUI   |  Non  |
| EgressCacheNoCache | Transferts de données sortants pour les ressources empêchés d’être mis en cache en raison d’une configuration de l’utilisateur sur le périmètre.                |OUI   |Non    |
| EgressCacheUncacheable | Transfert de données sortantes pour les ressources empêchées d’être mises en cache par les en-têtes Cache-Control et/ou Expires. Cela indique qu’elles ne doivent pas être mises en cache sur un point de présence ou par le client HTTP.                   |OUI   | Non   |
| EgressCacheOthers |  Transfère les données sortantes pour d’autres scénarios de cache.             |OUI   | Non   |

* Le transfert de données sortantes fait référence au trafic produit des serveurs POP CDN vers le client.


### <a name="schema-of-the-core-analytics-logs"></a>Schéma des journaux Core Analytics 

Tous les journaux sont stockés au format JSON, et chaque entrée comprend des champs de chaîne conformément au schéma suivant :

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Où « time » représente l’heure de début des limites d’heure pour lesquelles les statistiques sont rapportées. Quand une métrique n’est pas prise en charge par un fournisseur CDN, il y a une valeur null au lieu d’un entier ou d’un double. Cette valeur null indique l’absence de métrique, et diffère de la valeur 0. Il y a un ensemble de ces métriques par domaine configuré sur le point de terminaison.

Exemple de propriétés :

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Journaux de diagnostic Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Core Analytics via le portail supplémentaire Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure OMS Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [API REST Azure Log Analytics](https://docs.microsoft.com/rest/api/loganalytics)







