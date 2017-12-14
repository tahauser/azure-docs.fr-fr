---
title: Surveiller les services et applications Azure avec Grafana | Microsoft Docs
description: "Acheminez les données Azure Monitor et Application Insights en vue de les afficher dans Grafana."
services: monitoring-and-diagnostics
keywords: 
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: article
ms.service: monitoring-and-diagnostics
ms.openlocfilehash: c189e67c481239a8a68f2e2b30d05bb615cfa24e
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="monitor-your-azure-services-in-grafana"></a>Surveiller vos services Azure dans Grafana
Maintenant, vous pouvez aussi surveiller les services et applications Azure à partir de [Grafana](https://grafana.com/) en utilisant le [plug-in Azure Monitor de source de données](https://grafana.com/plugins/grafana-azure-monitor-datasource). Ce plug-in rassemble les données des performances d’application collectées par le kit SDK Application Insights ainsi que les données d’infrastructure fournies par Azure Monitor. Vous pouvez ensuite afficher ces données dans votre tableau de bord Grafana.

Le plug-in est actuellement en préversion.

Utilisez les étapes suivantes pour configurer un serveur Grafana à partir de la Place de marché Azure et générer des tableaux de bord pour les métriques depuis Azure Monitor et Application Insights.

## <a name="set-up-a-grafana-instance"></a>Configurer une instance Grafana
1. Accédez à la Place de marché Azure et choisissez Grafana, de Grafana Labs.

2. Renseignez les noms et les détails. Créez un groupe de ressources. Effectuez le suivi des valeurs que vous choisissez pour le nom d’utilisateur de la machine virtuelle, le mot de passe de la machine virtuelle et le mot de passe d’administrateur du serveur Grafana.  

3. Choisissez une taille de machine virtuelle et un compte de stockage.

4. Configurez les paramètres de configuration du réseau.

5. Affichez le résumé et sélectionnez **Créer** après avoir accepté les conditions d’utilisation.

## <a name="log-in-to-grafana"></a>Se connecter à Grafana
1. Lorsque le déploiement est terminé, sélectionnez **Accéder au groupe de ressources**. Une liste de ressources nouvellement créées s’affiche. 

    ![Objets du groupe de ressources Grafana](.\media\monitor-how-to-grafana\grafana1.png) 

    Si vous sélectionnez le groupe de sécurité réseau (*grafana-nsg* dans ce cas), vous vous apercevez que le port 3000 est utilisé pour accéder au serveur Grafana. 

2. Revenez à la liste de ressources et sélectionnez **Adresse IP publique**. En utilisant les valeurs trouvées sur cet écran, tapez *http://<IP address>:3000* ou *<DNSName>:3000* dans votre navigateur. Une page de connexion doit s’afficher pour le serveur Grafana que vous venez de créer.
    
    ![Écran de connexion Grafana](.\media\monitor-how-to-grafana\grafana2.png) 

3. Connectez-vous avec le nom d’utilisateur en tant qu’*administrateur* et le mot de passe d’administrateur du serveur Grafana que vous avez créé précédemment. 

## <a name="configure-data-source-plugin"></a>Configurer le plug-in de source de données

Lorsque vous êtes correctement connecté, vous devez voir que le plug-in Azure Monitor de source de données est déjà inclus.

![Grafana affiche le plug-in Azure Monitor](.\media\monitor-how-to-grafana\grafana3.png) 

1. Sélectionnez **Add data source** (Ajouter une source de données) pour configurer Azure Monitor et Application Insights. 
    
2. Choisissez un nom pour la source de données et sélectionnez **Azure Monitor** comme source de données dans la liste déroulante.
    
    
## <a name="create-a-service-principal"></a>Créer un principal du service 

Grafana utilise un principal de service Azure Active Directory pour se connecter à des API Azure Monitor et collecter des données de métriques. Vous devez créer un principal de service pour gérer l’accès à vos ressources Azure.

1. Consultez [ces instructions](../azure-resource-manager/resource-group-create-service-principal-portal.md) pour créer un principal de service. Copiez et enregistrez votre ID de locataire, votre ID de client et un secret client.

2. Consultez [Affecter l’application à un rôle](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) pour attribuer le rôle de lecteur à l’application Azure Active Directory.     

3. Si vous utilisez Application Insights, vous pouvez également inclure votre API Application Insights et l’ID de l’application pour collecter les métriques se rapportant à Application Insights. Pour plus d’informations, consultez [Récupération de votre clé API et de votre ID d’application](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

4. Après avoir entré toutes ces informations, sélectionnez **Save** (Enregistrer) pour que Grafana teste l’API. Un message similaire à celui qui suit doit s’afficher.  

    ![Grafana affiche le plug-in Azure Monitor](.\media\monitor-how-to-grafana\grafana4.png) 
    
## <a name="build-a-grafana-dashboard"></a>Créer un tableau de bord Grafana

1. Accédez à l’accueil et sélectionnez **New Dashboard** (Nouveau tableau de bord).

2. Dans le nouveau tableau de bord, sélectionnez le **Graph** (Graphe). Vous pouvez essayer d’autres options de création de graphiques, mais cet article utilise *Graph* (Graphe) comme exemple. 

    ![Nouveau tableau de bord Grafana](.\media\monitor-how-to-grafana\grafana5.png) 

3. Un graphe vide s’affiche dans votre tableau de bord. 

4. Cliquez sur le titre du volet et sélectionnez **Edit** (Modifier) pour entrer les détails des données à tracer dans ce graphe.
    
5. Une fois que vous avez sélectionné toutes les machines virtuelles qui vous intéressent, vous pouvez commencer l’affichage des métriques dans le tableau de bord. 

Voici un tableau de bord simple avec deux graphes. Celui qui est situé à gauche affiche le pourcentage UC de deux machines virtuelles. Le graphe de droite montre les transactions dans un compte de Stockage Azure ventilé par le type API de transaction.
    
![Exemple de deux graphes Grafana](.\media\monitor-how-to-grafana\grafana6.png) 
    

## <a name="optional-create-dashboard-playlists"></a>Facultatif : Créer des sélections de tableau de bord

Une des nombreuses fonctionnalités intéressantes de Grafana repose sur la sélection de tableaux de bord. Vous pouvez créer plusieurs tableaux de bord et les ajouter à une liste en configurant un intervalle pour que chaque tableau de bord s’affiche. Sélectionnez **Play** (Lire) pour voir le cycle des tableaux de bord. Vous pouvez les afficher sur un grand écran mural pour fournir « un tableau » à votre groupe. 
    
![Exemple de sélection Grafana](.\media\monitor-how-to-grafana\grafana7.png) 


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Facultatif : Surveiller vos métriques personnalisés dans le même serveur Grafana

Vous pouvez également installer Telegraf et InfluxDB pour collecter et tracer des métriques à la fois personnalisés ou basés sur agent dans la même instance Grafana. Il existe de nombreux plug-ins de source de données à utiliser pour rassembler ces métriques dans un tableau de bord. 
    
Vous pouvez également réutiliser cette configuration pour inclure des métriques à partir de votre serveur Prometheus. Utilisez le plug-in Prometheus de source de données dans la galerie des plug-ins de Grafana.
    
Voici des articles de référence sur l’utilisation de Telegraf, InfluxDB, Prometheus et Docker
 - [Guide pratique pour surveiller les métriques système avec TICK Stack sur Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Surveiller les métriques de ressources Docker avec Grafana, InfluxDB et Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [Une solution de surveillance pour les hôtes, conteneurs et services en conteneur Docker](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Voici l’image d’un tableau de bord Grafana complet qui comporte des métriques provenant d’Azure Monitor et d’Application Insights.
![Exemple de métriques de Grafana](.\media\monitor-how-to-grafana\grafana8.png) 


## <a name="clean-up-resources"></a>Supprimer des ressources

Vous êtes facturé lorsque les machines virtuelles sont en cours d’exécution, que vous les utilisiez ou non. Pour éviter des frais supplémentaires, nettoyez le groupe de ressources créé dans cet article. 

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources** et sur **Grafana**. 
2. Dans la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **Grafana** dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes
* [Vue d’ensemble des métriques d’Azure Monitor](monitoring-overview-metrics.md)


