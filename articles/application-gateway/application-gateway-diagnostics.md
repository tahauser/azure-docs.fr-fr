---
title: Surveiller les journaux d’accès, les journaux des performances, l’intégrité du serveur principal, ainsi que les métriques d’Application Gateway | Microsoft Docs
description: Découvrez comment activer et gérer les journaux d’accès et les journaux des performances pour Application Gateway
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: tysonn
tags: azure-resource-manager
ms.assetid: 300628b8-8e3d-40ab-b294-3ecc5e48ef98
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/23/2018
ms.author: amitsriva
ms.openlocfilehash: 885ae8b97175cac4cd29793eb0a935e81d54d0e4
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Intégrité du serveur principal, journaux de diagnostic et métriques pour la passerelle Application Gateway

À l’aide de la passerelle Azure Application Gateway, vous pouvez surveiller les ressources des manières suivantes :

* [Intégrité du serveur principal](#back-end-health) : Application Gateway permet de surveiller l’intégrité des serveurs dans les pools principaux au moyen du portail Azure et de Powershell. Vous pouvez également accéder à l’intégrité des pools principaux via les journaux de diagnostic des performances.

* [Journaux](#diagnostic-logging) : les journaux permettent d’enregistrer ou d’utiliser les performances, les accès et les autres données à partir d’une ressource à des fins de surveillance.

* [Mesures](#metrics) : la passerelle Application Gateway possède actuellement une métrique. Cette métrique mesure le débit de la passerelle Application Gateway en octets par seconde.

## <a name="back-end-health"></a>Intégrité du serveur principal

La passerelle Application Gateway permet de surveiller l’intégrité des membres individuels des pools principaux au moyen du portail, de PowerShell et de l’interface de ligne de commande (CLI). Une synthèse de l’intégrité agrégée des pools principaux est également accessible via les journaux de diagnostic des performances. 

Le rapport d’intégrité du serveur principal reflète les résultats de la sonde d’intégrité de la passerelle Application Gateway sur les instances de serveur principal. Si la détection réussit et que le trafic peut être orienté vers le serveur principal, ce dernier est considéré comme intègre. Sinon, il est considéré comme défaillant.

> [!IMPORTANT]
> Si le sous-réseau Application Gateway comporte un groupe de sécurité réseau (NSG), ouvrez les plages de ports 65503-65534 sur ce sous-réseau pour permettre l’arrivée du trafic entrant. Ces ports sont requis pour permettre à l’API relative à l’intégrité du serveur principal de fonctionner correctement.


### <a name="view-back-end-health-through-the-portal"></a>Affichage de l’intégrité du serveur principal via le portail

Dans le portail, l’intégrité du serveur principal est indiquée automatiquement. Dans une passerelle Application Gateway existante, sélectionnez **Analyse** > **Intégrité du serveur principal**. 

Chaque membre du pool principal est répertorié sur cette page (qu’il s’agisse d’une carte réseau, d’un IP ou d’un nom de domaine complet). Le nom du pool principal, le port, le nom et l’état d’intégrité des paramètres HTTP principaux sont affichés. Les valeurs valides de l’état d’intégrité sont **Healthy** (Intègre), **Unhealthy** (Défaillant) et **Unknown** (Inconnu).

> [!NOTE]
> Si l’état d’intégrité du serveur principal affiché est **Unknown** (Inconnu), assurez-vous que l’accès au serveur principal n’est pas bloqué par une règle de groupe de sécurité réseau, un itinéraire défini par l’utilisateur ou un DNS personnalisé dans le réseau virtuel.

![Intégrité du serveur principal][10]

### <a name="view-back-end-health-through-powershell"></a>Affichage de l’intégrité du serveur principal via PowerShell

Le code PowerShell suivant montre comment afficher l’intégrité du serveur principal en utilisant l’applet de commande `Get-AzureRmApplicationGatewayBackendHealth` :

```powershell
Get-AzureRmApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli-20"></a>Affichage de l’intégrité du serveur principal via Azure CLI 2.0

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Résultats

L’extrait de code suivant montre un exemple de la réponse :

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>Journaux de diagnostic

Vous pouvez utiliser différents types de journaux dans Azure pour gérer les passerelles Application Gateway et résoudre les problèmes associés. Vous pouvez accéder à certains de ces journaux via le portail. Tous les journaux peuvent être extraits à partir d’un stockage Blob Azure et affichés dans différents outils, comme [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel et PowerBI. Pour en savoir plus sur les différents types de journaux, consultez la liste suivante :

* **Journal d’activité** : vous pouvez utiliser le [Journal d’activité Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anciennement journaux des opérations et journaux d’audit) pour afficher toutes les opérations soumises à votre abonnement Azure, ainsi que leur état. Les entrées du journal d’activité sont recueillies par défaut et vous pouvez les afficher dans le Portail Azure.
* **Journaux d’accès** : vous pouvez utiliser ce journal pour afficher les modèles d’accès Application Gateway et analyser des informations importantes, notamment l’adresse IP de l’appelant, l’URL demandée, la latence de réponse, le code de retour, les octets d’entrée et de sortie. Un journal d’accès est collecté toutes les 300 secondes. Ce journal contient un enregistrement par instance Application Gateway. L’instance de la passerelle Application Gateway peut être identifiée par la propriété instanceId.
* **Journaux de performances** : vous pouvez utiliser ce journal pour afficher les performances des instances de la passerelle Application Gateway. Ce journal capture des informations sur les performances de chaque instance, notamment le nombre total de requêtes traitées, le débit en octets, le nombre total de requêtes présentées, le nombre de requêtes ayant échoué, le nombre d’instances du serveur principal intègres et défectueuses. Le journal des performances est collecté toutes les 60 secondes.
* **Journaux du pare-feu** : vous pouvez utiliser ce journal pour afficher les requêtes consignées via le mode de détection ou de prévention d’une passerelle Application Gateway configuré avec un pare-feu d’applications web.

> [!NOTE]
> Les journaux ne sont disponibles que pour les ressources déployées dans le modèle de déploiement Azure Resource Manager. Vous ne pouvez pas les utiliser pour les ressources utilisant le modèle de déploiement classique. Pour mieux comprendre ces deux modèles, consultez l’article [Présentation du déploiement de Resource Manager et du déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md) .

Pour stocker vos journaux, vous disposez de trois options :

* **Compte de stockage** : les comptes de stockage conviennent parfaitement aux journaux lorsqu’ils sont stockés pour une durée plus longue et consultés lorsque nécessaire.
* **Concentrateurs d’événements** : les concentrateurs d’événements constituent une excellente solution pour l’intégration avec d’autres outils SEIM (Security Information and Event Management) afin de recevoir des alertes sur vos ressources.
* **Log Analytics** : Log Analytics convient parfaitement pour la surveillance en temps réel générale de votre application ou la recherche de tendances.

### <a name="enable-logging-through-powershell"></a>Activation de la journalisation avec PowerShell

La journalisation d’activité est automatiquement activée pour chaque ressource Resource Manager. Vous devez activer la journalisation de l’accès et des performances pour commencer à collecter les données disponibles dans ces journaux. Pour activer la journalisation, utilisez les étapes suivantes :

1. Notez l’ID de ressource de votre compte de stockage, où les données de journalisation sont stockées. Cette valeur a le format suivant : /abonnements/\<subscriptionId\>/resourceGroups/\<nom du groupe de ressources\>/providers/Microsoft.Storage/storageAccounts/\<nom du compte de stockage\>. Vous pouvez utiliser n’importe quel compte de stockage dans votre abonnement. Vous pouvez utiliser le portail Azure pour rechercher ces informations.

    ![Portail : ID de ressource du compte de stockage](./media/application-gateway-diagnostics/diagnostics1.png)

2. Notez l’ID de ressource de votre passerelle Application Gateway pour laquelle la journalisation est activée. Cette valeur a le format suivant : /abonnements/\<subscriptionId\>/resourceGroups/\<nom du groupe de ressources\>/providers/Microsoft.Network/applicationGateways/\<nom de la passerelle Application Gateway\>. Vous pouvez utiliser le portail pour rechercher ces informations.

    ![Portail : ID de ressource de la passerelle Application Gateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Activez la journalisation des diagnostics à l’aide de l’applet de commande PowerShell suivante :

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Les journaux d’activité ne nécessitent pas de compte de stockage distinct. L’utilisation du stockage pour la journalisation de l’accès et des performances occasionne des frais de service.

### <a name="enable-logging-through-the-azure-portal"></a>Activation de la journalisation avec le portail Azure

1. Dans le portail Azure, recherchez votre ressource, puis cliquez sur **Journaux de diagnostic**.

   Pour Application Gateway, trois journaux d’audit sont disponibles :

   * Journal d’accès
   * Journal des performances
   * Journal du pare-feu

2. Cliquez sur **Activer les diagnostics** pour démarrer la collecte de données.

   ![Activation des diagnostics][1]

3. Le panneau **Paramètres de diagnostic** contient les paramètres des journaux de diagnostic. Dans cet exemple, Log Analytics stocke les journaux. Cliquez sur **Configurer** sous **Log Analytics** pour configurer votre espace de travail. Vous pouvez également utiliser des concentrateurs d’événements et un compte de stockage pour enregistrer les journaux de diagnostic.

   ![Démarrage du processus de configuration][2]

4. Choisissez un espace de travail Log Analytics existant ou créez en un nouveau. Cet exemple utilise un espace de travail existant.

   ![Options d’espaces de travail Log Analytics][3]

5. Confirmez les paramètres et cliquez sur **Enregistrer**.

   ![Panneau des paramètres de diagnostic avec des sélections][4]

### <a name="activity-log"></a>Journal d’activité

Par défaut, Azure génère le journal d’activité. Les journaux sont conservés pendant 90 jours dans la banque de journaux d’événements d’Azure. Pour en savoir plus sur ces journaux, lisez l’article [Affichage des événements et du journal d’activité](../monitoring-and-diagnostics/insights-debugging-with-events.md).

### <a name="access-log"></a>Journal d’accès

Le journal d’accès n’est généré que si vous l’avez activé sur chaque instance Application Gateway, comme détaillé dans les étapes précédentes. Les données sont stockées dans le compte de stockage spécifié lors de l’activation de la journalisation. Chaque accès à la passerelle Application Gateway est journalisé au format JSON, comme indiqué dans l’exemple suivant :


|Valeur  |Description  |
|---------|---------|
|instanceId     | Instance Application Gateway ayant traité la requête.        |
|clientIP     | Adresse IP d’origine de la requête.        |
|clientPort     | Port d’origine de la requête.       |
|httpMethod     | Méthode HTTP utilisée par la requête.       |
|requestUri     | URI de la requête reçue.        |
|RequestQuery     | **Acheminée par le serveur** : instance de pool principal à laquelle la requête a été envoyée.</br>**X-AzureApplicationGateway-journal-ID** : ID de corrélation utilisé pour la requête. Peut être utilisée pour résoudre les problèmes de trafic sur les serveurs principaux. </br>**ÉTAT DU SERVEUR** : code de réponse HTTP reçu par Application Gateway à partir du serveur principal.       |
|UserAgent     | Agent utilisateur de l’en-tête de requête HTTP.        |
|httpStatus     | Code d’état HTTP renvoyé au client à partir de d’Application Gateway.       |
|httpVersion     | Version HTTP de la requête.        |
|receivedBytes     | Taille du paquet reçu, en octets.        |
|sentBytes| Taille du paquet envoyé, en octets.|
|timeTaken| Durée (en millisecondes) nécessaire pour le traitement d’une requête et l’envoi de la réponse. Elle est calculée en fonction de l’intervalle entre le moment où Application Gateway reçoit le premier octet d’une requête HTTP et le moment où l’opération d’envoi d’une réponse se termine. Il est important de noter que le champ Time-Taken inclut généralement l’heure à laquelle la requête et les paquets de réponse circulent sur le réseau. |
|sslEnabled| Détermine si la communication avec les pools principaux utilisait SSL. Les valeurs valides sont On (Activé) et Off (Désactivé).|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off"
    }
}
```

### <a name="performance-log"></a>Journal des performances

Le journal des performances n’est généré que si vous l’avez activé sur chaque instance Application Gateway, comme détaillé dans les étapes précédentes. Les données sont stockées dans le compte de stockage spécifié lors de l’activation de la journalisation. Les données du journal des performances sont générées par intervalles d’1 minute. Les données suivantes sont enregistrées :


|Valeur  |Description  |
|---------|---------|
|instanceId     |  Instance Application Gateway pour laquelle les données des performances sont générées. Pour une passerelle Application Gateway à plusieurs instances, il y a une ligne par instance.        |
|healthyHostCount     | Nombre d’hôtes intègres dans le pool principal.        |
|unHealthyHostCount     | Nombre d’hôtes défaillants dans le pool principal.        |
|requestCount     | Nombre de requêtes traitées.        |
|latency | Latence (en millisecondes) des requêtes à partir de l’instance vers le serveur principal qui traite les requêtes. |
|failedRequestCount| Nombre d’échecs de requêtes.|
|throughput| Débit moyen depuis le dernier journal, mesuré en octets par seconde.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> La latence est calculée à partir de l’heure de la réception du premier octet de la requête HTTP jusqu’à l’heure de l'envoi du dernier octet de la réponse HTTP. Il s’agit de la somme du temps de traitement d’Application Gateway et du coût du réseau pour le serveur principal, ainsi que le temps pris par le serveur principal pour traiter la requête.

### <a name="firewall-log"></a>Journal du pare-feu

Le journal du pare-feu n’est généré que si vous l’avez activé sur chaque passerelle Application Gateway, comme détaillé dans les étapes précédentes. Ce fichier journal nécessite également que ce pare-feu d’applications web soit configuré sur une passerelle Application Gateway. Les données sont stockées dans le compte de stockage spécifié lors de l’activation de la journalisation. Les données suivantes sont enregistrées :


|Valeur  |Description  |
|---------|---------|
|instanceId     | Instance Application Gateway pour laquelle les données du pare-feu sont générées. Pour une passerelle Application Gateway à plusieurs instances, il y a une ligne par instance.         |
|clientIp     |   Adresse IP d’origine de la requête.      |
|clientPort     |  Port d’origine de la requête.       |
|requestUri     | URL de la requête reçue.       |
|ruleSetType     | Type d’ensemble de règles. La valeur disponible est OWASP.        |
|ruleSetVersion     | Version d’ensemble de règles utilisée. Les valeurs disponibles sont 2.2.9 et 3.0.     |
|ruleId     | ID de règle de l’événement de déclenchement.        |
|Message     | Message convivial pour l’événement de déclenchement. La section Détails vous fournit plus d’informations.        |
|action     |  Action effectuée sur la requête. Les valeurs disponibles sont bloquées et autorisées.      |
|site     | Site pour lequel le journal a été généré. Actuellement, seul Global est répertorié car les règles sont globales.|
|détails     | Détails de l’événement de déclenchement.        |
|details.message     | Description de la règle.        |
|details.data     | Données spécifiques trouvées dans la requête correspondant à la règle.         |
|details.file     | Fichier de configuration qui contenait la règle.        |
|details.line     | Numéro de ligne dans le fichier de configuration ayant déclenché l’événement.       |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>Afficher et analyser le journal d’activité

Vous pouvez afficher et analyser les données du journal d’activité en utilisant l’une des méthodes suivantes :

* **Outils Azure** : récupérez les informations à partir du journal d’activité au moyen d’Azure PowerShell, d’Azure CLI, de l’API REST Azure ou du Portail Azure. Des instructions pas à pas pour chaque méthode sont détaillées dans l’article [Opérations d’activité avec Resource Manager](../azure-resource-manager/resource-group-audit.md).
* **Power BI** : si vous n’avez pas encore de compte [Power BI](https://powerbi.microsoft.com/pricing) , vous pouvez l’essayer gratuitement. À l’aide du [pack de contenus des journaux d’activité Azure pour Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), vous pouvez analyser vos données avec des tableaux de bord préconfigurés à utiliser en l’état ou à personnaliser.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Affichage et analyse des journaux d’accès, des performances et du pare-feu

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) peut collecter les fichiers du compteur et du journal d’événements à partir de votre compte de stockage d’objets Blob. Il inclut des visualisations et des fonctionnalités puissantes de recherche pour analyser vos journaux.

Vous pouvez également vous connecter à votre compte de stockage et récupérer les entrées de journal JSON pour les journaux d’accès et des performances. Après avoir téléchargé les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, PowerBI ou tout autre outil de visualisation de données.

> [!TIP]
> Si vous savez utiliser Visual Studio et les concepts de base de la modification des valeurs de constantes et variables en C#, vous pouvez utiliser les [outils de convertisseur de journaux](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles dans GitHub.
> 
> 

## <a name="metrics"></a>Mesures

Les mesures représentent une fonctionnalité de certaines ressources Azure, vous permettant d’afficher les compteurs de performances dans le portail. Pour Application Gateway, les métriques suivantes sont disponibles :

- Connexions courantes
- Demandes ayant échoué
- Nombre d’hôtes intègres
- État de la réponse
- Throughput
- Total de requêtes
- Nombre d’hôtes non intègres

Accédez à une passerelle d’applications, sous **Analyse** cliquez sur **Métriques**. Pour afficher les valeurs disponibles, sélectionnez la liste déroulante **MÉTRIQUE**.

Dans l’image suivante, consultez un exemple avec trois métriques affichées pour les 30 dernières minutes :

[![](media/application-gateway-diagnostics/figure5.png "Affichage des métriques")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Pour afficher une liste actuelle des métriques, consultez [Mesures prises en charge avec Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

### <a name="alert-rules"></a>Règles d'alerte

Vous pouvez démarrer des règles d’alerte en fonction des métriques d’une ressource. Par exemple, une alerte peut appeler un webhook ou envoyer un e-mail à un administrateur si le débit de la passerelle Application Gateway est au-dessus ou en dessous d’un seuil pour une période spécifiée.

L’exemple suivant vous guide dans la création d’une règle d’alerte qui envoie un e-mail à un administrateur lorsqu’un seuil de débit est dépassé :

1. Cliquez sur **Ajouter une alerte Métrique** pour ouvrir le panneau **Ajouter une règle**. Ce panneau est également accessible à partir du panneau Métriques.

   ![Bouton Ajouter une alerte Métrique][6]

2. Dans le panneau **Ajouter une règle**, remplissez les sections Nom, Condition et Notifier, puis cliquez sur **OK**.

   * Dans le sélecteur **Condition**, sélectionnez une des 4 valeurs : **Supérieur à**, **Supérieur ou égal à**, **Inférieur à** ou **Inférieur ou égal à**.

   * Dans le sélecteur **Période**, sélectionnez une période allant de 5 minutes à 6 heures.

   * Si vous sélectionnez **Envoyer des e-mails aux propriétaires, contributeurs et lecteurs** , l’e-mail peut être dynamiquement basé sur les utilisateurs qui ont accès à cette ressource. Dans le cas contraire, vous pouvez fournir une liste d’utilisateurs séparée par des virgules dans la zone **Adresse(s) de messagerie d’administrateur(s) supplémentaire(s)** .

   ![Panneau Ajouter une règle][7]

Si le seuil est dépassé, un e-mail similaire à celui de l’image suivante vous est envoyé :

![E-mail en cas de dépassement de seuil][8]

Une liste d’alertes apparaît une fois que vous avez créé une alerte Métrique. Elle fournit une vue d’ensemble de toutes les règles d’alerte.

![Liste d’alertes et de règles][9]

Pour en savoir plus sur les notifications d’alerte, consultez [Réception de notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Pour en savoir plus sur les webhooks et sur la façon de les utiliser avec des alertes, consultez [Configurer un webhook sur une alerte de métrique Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="next-steps"></a>Étapes suivantes

* Visualisation du compteur et des journaux des événements à l’aide de [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).
* Billet de blog sur la [visualisation de votre journal d’activité Azure avec Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
* Billet de blog sur [l’affichage et l’analyse des journaux d’activité Azure dans Power BI](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
