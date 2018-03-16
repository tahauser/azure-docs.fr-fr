---
title: "Collecter des données à partir d’ordinateurs Windows locaux avec Azure Log Analytics | Microsoft Docs"
description: "Découvrez comment déployer l’agent Log Analytics pour Windows s’exécutant sur des ordinateurs situés en dehors d’Azure, et activer la collecte de données avec Log Analytics."
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
ms.date: 03/06/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: e04b9ae29191a170b1662a982d7f38426e6cc831
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="collect-data-from-windows-computers-hosted-in-your-environment"></a>Collecter des données à partir d’ordinateurs Windows hébergés dans votre environnement
[Azure Log Analytics](log-analytics-overview.md) est capable de collecter des données directement à partir de vos ordinateurs Windows physiques ou virtuels et d’autres ressources de votre environnement dans un référentiel unique pour ensuite procéder à une analyse et à une mise en corrélation détaillées.  Ce guide de démarrage rapide montre comment configurer et collecter des données à partir de votre ordinateur Windows en quelques étapes simples.  Pour les machines virtuelles Windows Azure, voir la rubrique [Collecter des données sur les machines virtuelles Azure](log-analytics-quick-collect-azurevm.md).  

Pour comprendre les exigences réseau et système nécessaires au déploiement de l’agent Windows, consultez la [configuration requise pour le système d’exploitation Windows](log-analytics-concept-hybrid.md#prerequisites).
 
Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure-portal"></a>Connexion au portail Azure
Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Créer un espace de travail
1. Dans le portail Azure, cliquez sur **Plus de services** dans l’angle inférieur gauche. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.<br><br> ![Portail Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Cliquez sur **Créer**, puis sélectionnez des options pour les éléments suivants :

  * Attribuez un nom au nouvel **Espace de travail OMS**, en l’occurrence *DefaultLAWorkspace*. 
  * Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
  * Dans le champ **Groupe de ressources**, sélectionnez un groupe de ressources existant ou créez-en un en saisissant un nom dans le champ de texte.  
  * Sélectionnez l’**Emplacement** dans lequel vos machines virtuelles sont déployées.  Pour en savoir plus, découvrez dans quelles [régions Log Analytics est disponible](https://azure.microsoft.com/regions/services/).
  * Vous avez le choix entre trois **niveaux tarifaires** différents dans Log Analytics, mais pour ce guide de démarrage rapide, vous allez choisir le niveau **gratuit**.  Pour plus d’informations sur les différents niveaux proposés, consultez le [détail des tarifs de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. Après avoir entré les informations requises dans le volet **Espace de travail OMS**, cliquez sur **OK**.  

Pendant que les informations sont vérifiées et l’espace de travail créé, vous pouvez suivre la progression sous **Notifications** dans le menu. 

## <a name="obtain-workspace-id-and-key"></a>Obtenir l’ID et la clé d’espace de travail
Avant d’installer Microsoft Monitoring Agent pour Windows, vous devez disposer de l’ID et de la clé d’espace de travail pour votre espace de travail Log Analytics.  L’Assistant Installation a besoin de ces informations pour configurer correctement l’agent et s’assurer qu’il peut communiquer avec Log Analytics.  

1. Dans le portail Azure, cliquez sur **Plus de services** dans l’angle inférieur gauche. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
2. Dans votre liste d’espaces de travail Log Analytics, sélectionnez *DefaultLAWorkspace* créé précédemment.
3. Sélectionnez **Paramètres avancés**.<br><br> ![Paramètres avancés de Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Sélectionnez **Sources connectées**, puis **Serveurs Windows**.   
5. Des valeurs figurent à droite d’**ID de l’espace de travail** et de **Clé primaire**. Copiez-collez ces deux valeurs dans votre éditeur favori.   

## <a name="install-the-agent-for-windows"></a>Installer l’agent pour Windows
Les étapes suivantes installent et configurent l’agent pour Log Analytics dans le cloud Azure et Azure Government en utilisant le programme d’installation de Microsoft Monitoring Agent sur votre ordinateur.  

1. Dans la page **Serveurs Windows**, sélectionnez l’option **Télécharger l’agent Windows** correspondant à la version appropriée selon l’architecture du processeur du système d’exploitation Windows.
2. Exécutez le programme d’installation pour installer l’agent sur votre ordinateur.
2. Sur la page d’**accueil**, cliquez sur **Suivant**.
3. Dans la page **Termes du contrat de licence**, lisez les conditions de licence, puis cliquez sur **J’accepte**.
4. Dans la page **Dossier de destination**, modifiez ou conservez le dossier d’installation par défaut, puis cliquez sur **Suivant**.
5. Dans la page **Options d’installation de l’agent**, choisissez de connecter l’agent à Azure Log Analytics (OMS), puis cliquez sur **Suivant**.   
6. Dans la page **Azure Log Analytics**, procédez comme suit :
   1. Collez l’**ID de l’espace de travail** et la **Clé d’espace de travail (Clé primaire)** que vous avez copiés précédemment.  Si l’ordinateur doit rendre compte à un espace de travail Log Analytics dans le cloud Azure Government, dans la liste déroulante **Cloud Azure**, sélectionnez **Azure - Gouvernement des États-Unis**.  
   2. Si l’ordinateur a besoin de communiquer via un serveur proxy avec le service Log Analytics, cliquez sur **Avancé**, puis indiquez l’URL et le numéro de port du serveur proxy.  Si votre serveur proxy requiert une authentification, tapez le nom d’utilisateur et un mot de passe pour vous authentifier auprès du serveur proxy, puis cliquez sur **Suivant**.  
7. Cliquez sur **Suivant** après avoir fourni les paramètres de configuration nécessaires.<br><br> ![Coller l’ID et la clé primaire de l’espace de travail](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Dans la page **Prêt pour l’installation**, passez en revue vos choix, puis cliquez sur **Installer**.
9. Dans la page **Configuration effectuée**, cliquez sur **Terminer**.

Lorsque vous avez terminé, **Microsoft Monitoring Agent** apparaît dans le **Panneau de configuration**. Vous pouvez examiner votre configuration et vérifier que l’agent est connecté à Log Analytics. Une fois connecté, sous l’onglet **Azure Log Analytics (OMS)**, l’agent affiche un message indiquant que **Microsoft Monitoring Agent s’est correctement connecté au service Microsoft Operations Management Suite**.<br><br> ![État de la connexion MMA à Log Analytics](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Collecter les données d’événements et de performances
Log Analytics est capable de collecter les événements des journaux des événements Windows ainsi que des compteurs de performances que vous spécifiez dans l’optique de procéder à une analyse à long terme et de générer des rapports afin de réagir dès qu’une condition particulière est détectée.  Pour configurer la collecte d’événements à partir du journal des événements Windows, ainsi que plusieurs compteurs de performances courants avec lesquels commencer, procédez comme suit.  

1. Dans le portail Azure, cliquez sur **Plus de services** dans l’angle inférieur gauche. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
2. Sélectionnez **Paramètres avancés**.<br><br> ![Paramètres avancés de Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br> 
3. Sélectionnez **Données**, puis **Journaux des événements Windows**.  
4. Vous pouvez ajouter un journal d’événements en tapant le nom de ce journal.  Tapez **Système**, puis cliquez sur le signe plus **+**.  
5. Dans le tableau, vérifiez les niveaux de gravité **Erreur** et **Avertissement**.   
6. Cliquez sur **Enregistrer** en haut de la page pour enregistrer la configuration.
7. Sélectionnez **Données de performances Windows** pour activer la collecte des compteurs de performances sur un ordinateur Windows. 
8. Quand vous procédez à la configuration initiale des compteurs de performances Windows pour un nouvel espace de travail Log Analytics, la possibilité vous est offerte de créer rapidement plusieurs compteurs courants. Ils s’affichent avec une case à cocher en regard.<br> ![Compteurs de performances Windows par défaut sélectionnés](media/log-analytics-quick-collect-azurevm/windows-perfcounters-default.png).<br> Cliquez sur **Ajouter les compteurs de performances sélectionnés**.  Ils sont ajoutés et prédéfinis avec un intervalle d’échantillonnage de collecte de dix secondes.  
9. Cliquez sur **Enregistrer** en haut de la page pour enregistrer la configuration.

## <a name="view-data-collected"></a>Afficher les données collectées
À présent que vous avez activé la collecte de données, exécutons un exemple simple de recherche dans les journaux pour voir certaines données de l’ordinateur cible.  

1. Dans le portail Azure, sous l’espace de travail sélectionné, cliquez sur la vignette **Recherche dans les journaux**.  
2. Dans le volet Recherche dans les journaux, dans le champ de requête, tapez `Perf`, puis appuyez sur Entrée ou cliquez sur le bouton de recherche à droite du champ de requête.<br><br> ![Exemple de requête de recherche dans les journaux Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> Par exemple, la requête présentée dans l’image suivante a retourné 735 enregistrements de performances.<br><br> ![Résultat de la recherche dans les journaux Log Analytics](media/log-analytics-quick-collect-windows-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, vous pouvez supprimer l’agent de l’ordinateur Windows ainsi que l’espace de travail Log Analytics.  

Pour supprimer l’agent, procédez comme suit.

1. Ouvrez le **Panneau de configuration**.
2. Ouvrez **Programmes et fonctionnalités**.
3. Dans **Programmes et fonctionnalités**, sélectionnez **Microsoft Monitoring Agent**, puis cliquez sur **Désinstaller**.

Pour supprimer l’espace de travail, sélectionnez l’espace de travail Log Analytics que vous avez créé précédemment, puis, dans la page de ressource, cliquez sur **Supprimer**.<br><br> ![Supprimer la ressource Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous collectez des données opérationnelles et de performances à partir de votre ordinateur Linux local, vous pouvez commencer aisément à explorer et à analyser les données que vous collectez *gratuitement*, et agir en conséquence.  

Pour savoir comment consulter et analyser les données, passez au didacticiel suivant.   

> [!div class="nextstepaction"]
> [Consulter ou analyser les données dans Log Analytics](log-analytics-tutorial-viewdata.md)
