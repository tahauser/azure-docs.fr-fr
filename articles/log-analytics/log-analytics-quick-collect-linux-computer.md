---
title: "Collecter des données à partir d’ordinateurs Linux locaux avec Azure Log Analytics | Microsoft Docs"
description: "Découvrez comment déployer l’agent Log Analytics pour Linux et activez la collecte de données à partir de ce système d’exploitation avec Log Analytics."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/11/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: bc7c7ea1a01ad784ae53090f1ae0edb042b4f07f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="collect-data-from-linux-computer-hosted-in-your-environment"></a>Collecter des données à partir d’un ordinateur Linux hébergé dans votre environnement
[Azure Log Analytics](log-analytics-overview.md) est capable de collecter des données directement à partir de votre ordinateur Linux physique ou virtuel et d’autres ressources de votre environnement dans un référentiel unique pour ensuite procéder à une analyse et à une mise en corrélation détaillées.  Ce guide de démarrage rapide montre comment configurer et collecter des données à partir de votre ordinateur Linux en quelques étapes simples.  Pour les machines virtuelles Linux Azure, voir la rubrique [Collecter des données sur les machines virtuelles Azure](log-analytics-quick-collect-azurevm.md).  

Afin de comprendre le réseau et la configuration requise pour le déploiement de l’agent Linux, consultez la section traitant de la [configuration requise pour le système d’exploitation Linux](log-analytics-concept-hybrid.md#prerequisites).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure-portal"></a>Connexion au portail Azure
Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Créer un espace de travail
1. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.<br><br> ![Portail Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Cliquez sur **Créer**, puis sélectionnez des options pour les éléments suivants :

  * Attribuez un nom au nouvel **Espace de travail OMS**, en l’occurrence *DefaultLAWorkspace*. 
  * Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
  * Pour **Groupe de ressources**, sélectionnez un groupe de ressources existant qui contient une ou plusieurs machines virtuelles Azure.  
  * Sélectionnez l’**Emplacement** dans lequel vos machines virtuelles sont déployées.  Pour en savoir plus, découvrez dans quelles [régions Log Analytics est disponible](https://azure.microsoft.com/regions/services/).
  * Vous avez le choix entre trois **niveaux tarifaires** différents dans Log Analytics, mais pour ce guide de démarrage rapide, vous allez choisir le niveau **gratuit**.  Pour plus d’informations sur les différents niveaux proposés, consultez le [détail des tarifs de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. Après avoir entré les informations requises dans le volet **Espace de travail OMS**, cliquez sur **OK**.  

Pendant que les informations sont vérifiées et l’espace de travail créé, vous pouvez suivre la progression sous **Notifications** dans le menu. 

## <a name="obtain-workspace-id-and-key"></a>Obtenir l’ID et la clé d’espace de travail
Avant d’installer l’agent OMS pour Linux, vous devez disposer de l’ID et de la clé d’espace de travail pour votre espace de travail Log Analytics.  Le script du wrapper de l’agent a besoin de ces informations pour configurer correctement l’agent et s’assurer qu’il peut communiquer avec Log Analytics.  

1. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
2. Dans votre liste d’espaces de travail Log Analytics, sélectionnez *DefaultLAWorkspace* créé précédemment.
3. Sélectionnez **Paramètres avancés**.<br><br> ![Paramètres avancés de Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Sélectionnez **Sources connectées**, puis **Serveurs Linux**.   
5. Des valeurs figurent à droite d’**ID de l’espace de travail** et de **Clé primaire**. Copiez-collez ces deux valeurs dans votre éditeur favori.   

## <a name="install-the-agent-for-linux"></a>Installation de l’agent pour Linux
Les étapes suivantes configurent le programme d’installation de l’agent pour Log Analytics dans Azure et dans le cloud Azure Government.  

>[!NOTE]
>L’agent OMS pour Linux ne peut pas être configuré pour envoyer des rapports à plus d’un espace de travail Log Analytics.  

Si votre ordinateur Linux a besoin d’un serveur proxy pour communiquer avec Log Analytics, la configuration du proxy peut être spécifiée sur la ligne de commande en incluant `-p [protocol://][user:password@]proxyhost[:port]`.  La propriété *proxyhost* accepte un nom de domaine complet ou l’adresse IP du serveur proxy. 

Par exemple : `https://user01:password@proxy01.contoso.com:30443`

1. Pour configurer l’ordinateur Linux en vue d’une connexion à Log Analytics, exécutez la commande suivante en fournissant l’ID de l’espace de travail et la clé primaire copiés précédemment.  La commande suivante télécharge l’agent, valide sa somme de contrôle et l’installe. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    La commande suivante inclut le paramètre proxy `-p` et un exemple de syntaxe.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Pour configurer l’ordinateur Linux en vue d’une connexion à Log Analytics dans le cloud Azure Government, exécutez la commande suivante en fournissant l’ID de l’espace de travail et la clé primaire copiés précédemment.  La commande suivante télécharge l’agent, valide sa somme de contrôle et l’installe. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    La commande suivante inclut le paramètre proxy `-p` et un exemple de syntaxe.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Redémarrez l’agent en exécutant la commande suivante : 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Collecter les données d’événements et de performances
Log Analytics est capable de collecter les événements de Syslog Linux ainsi que des compteurs de performances que vous spécifiez dans l’optique de procéder à une analyse à long terme et de générer des rapports afin de réagir dès qu’une condition particulière est détectée.  Pour configurer la collecte d’événements à partir de Syslog Linux, ainsi que plusieurs compteurs de performances courants avec lesquels commencer, procédez comme suit.  

1. Sélectionnez **Syslog**.  
2. Vous pouvez ajouter un journal d’événements en tapant le nom de ce journal.  Tapez **Syslog**, puis cliquez sur le signe plus **+**.  
3. Dans le tableau, décochez les niveaux de gravité **Info**, **Avis** et **Débogage**. 
4. Cliquez sur **Enregistrer** en haut de la page pour enregistrer la configuration.
5. Sélectionnez **Données de performances Linux** pour activer la collecte des compteurs de performances sur un ordinateur Linux. 
6. Quand vous procédez à la configuration initiale des compteurs de performances Linux pour un nouvel espace de travail Log Analytics, la possibilité vous est offerte de créer rapidement plusieurs compteurs courants. Ils s’affichent avec une case à cocher en regard.<br><br> ![Compteurs de performances Windows par défaut sélectionnés](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png)<br> Cliquez sur **Ajouter les compteurs de performances sélectionnés**.  Ils sont ajoutés et prédéfinis avec un intervalle d’échantillonnage de collecte de dix secondes.  
7. Cliquez sur **Enregistrer** en haut de la page pour enregistrer la configuration.

## <a name="view-data-collected"></a>Afficher les données collectées
À présent que vous avez activé la collecte de données, exécutons un exemple simple de recherche dans les journaux pour voir certaines données de l’ordinateur cible.  

1. Dans le portail Azure, accédez à Log Analytics et sélectionnez l’espace de travail créé précédemment.
2. Cliquez sur la vignette **Recherche dans les journaux**. Ensuite, dans le volet Recherche dans les journaux, dans le champ de requête, tapez `Perf`, puis appuyez sur Entrée ou cliquez sur le bouton de recherche à droite du champ de requête.<br><br> ![Exemple de requête de recherche dans les journaux Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> Par exemple, la requête présentée dans l’image suivante a retourné 735 enregistrements de performances.<br><br> ![Résultat de la recherche dans les journaux Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, vous pouvez supprimer l’agent de l’ordinateur Linux ainsi que l’espace de travail Log Analytics.  

Pour supprimer l’agent, exécutez la commande suivante sur l’ordinateur Linux.  L’argument *--purge* supprime complètement l’agent et sa configuration.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Pour supprimer l’espace de travail, sélectionnez l’espace de travail Log Analytics que vous avez créé précédemment, puis, dans la page de ressource, cliquez sur **Supprimer**.<br><br> ![Supprimer la ressource Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous collectez des données opérationnelles et de performances à partir de votre ordinateur Linux local, vous pouvez commencer aisément à explorer et à analyser les données que vous collectez *gratuitement*, et agir en conséquence.  

Pour savoir comment consulter et analyser les données, passez au didacticiel suivant.   

> [!div class="nextstepaction"]
> [Consulter ou analyser les données dans Log Analytics](log-analytics-tutorial-viewdata.md)
