---
title: "Collecter des données sur les machines virtuelles Azure | Microsoft Docs"
description: "Découvrez comment activer l’extension de machine virtuelle de l’agent OMS et comment activer la collecte de données à partir de vos machines virtuelles Azure avec Log Analytics."
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
ms.date: 01/04/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: be43701f96a71ad5cd9239c4ec7b3eea7fd6db21
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="collect-data-about-azure-virtual-machines"></a>Collecter des données sur les machines virtuelles Azure
[Azure Log Analytics](log-analytics-overview.md) est capable de collecter des données directement à partir de vos machines virtuelles Azure et des autres ressources de votre environnement dans un référentiel unique pour ensuite procéder à une analyse et à une mise en corrélation détaillées.  Ce guide de démarrage rapide vous montre comment configurer et collecter des données à partir de vos machines virtuelles Azure Linux ou Windows en quelques étapes simples.  
 
En suivant ce guide, vous êtes censé disposer d’une machine virtuelle Azure. Dans le cas contraire, vous pouvez [créer une machine virtuelle Windows](../virtual-machines/windows/quick-create-portal.md) ou [créer une machine virtuelle Linux](../virtual-machines/linux/quick-create-cli.md) en suivant nos guides de démarrage rapide consacrés aux machines virtuelles.

## <a name="log-in-to-azure-portal"></a>Connexion au portail Azure
Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Créer un espace de travail
1. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.<br> ![Portail Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br>  
2. Cliquez sur **Créer**, puis sélectionnez des options pour les éléments suivants :

  * Attribuez un nom au nouvel **Espace de travail OMS**, en l’occurrence *DefaultLAWorkspace*. 
  * Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
  * Pour **Groupe de ressources**, sélectionnez un groupe de ressources existant qui contient une ou plusieurs machines virtuelles Azure.  
  * Sélectionnez l’**Emplacement** dans lequel vos machines virtuelles sont déployées.  Pour en savoir plus, découvrez dans quelles [régions Log Analytics est disponible](https://azure.microsoft.com/regions/services/).
  * Vous avez le choix entre trois **niveaux tarifaires** différents dans Log Analytics, mais pour ce guide de démarrage rapide, vous allez choisir le niveau **gratuit**.  Pour plus d’informations sur les différents niveaux proposés, consultez le [détail des tarifs de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. Après avoir entré les informations requises dans le volet **Espace de travail OMS**, cliquez sur **OK**.  

Pendant que les informations sont vérifiées et l’espace de travail créé, vous pouvez suivre la progression sous **Notifications** dans le menu. 

## <a name="enable-the-log-analytics-vm-extension"></a>Activer l’extension de machine virtuelle Log Analytics
Pour les machines virtuelles Windows et Linux déjà déployées dans Azure, vous devez installer l’agent Log Analytics avec l’extension de machine virtuelle Log Analytics.  L’utilisation de l’extension simplifie le processus d’installation et configure automatiquement l’agent pour qu’il envoie des données à l’espace de travail Log Analytics que vous spécifiez. L’agent est également mis à niveau automatiquement, de façon à ce que vous disposiez des fonctionnalités et correctifs les plus récents.

>[!NOTE]
>L’agent OMS pour Linux ne peut pas être configuré pour envoyer des rapports à plus d’un espace de travail Log Analytics. 

Si vous avez créé un espace de travail dans le cloud Azure Government, vous noterez peut-être la présence de la bannière en haut de votre page de ressource Log Analytics du portail vous invitant à effectuer une mise à niveau.  La mise à niveau n’est pas nécessaire dans le cadre de ce guide de démarrage rapide.<br>

![Avis de mise à niveau Log Analytics dans le portail Azure](media/log-analytics-quick-collect-azurevm/log-analytics-portal-upgradebanner.png).    
1. Dans le portail Azure, cliquez sur **Plus de services** dans l’angle inférieur gauche. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
2. Dans votre liste d’espaces de travail Log Analytics, sélectionnez *DefaultLAWorkspace* créé précédemment.
3. Dans le menu de gauche, sous Sources de données de l’espace de travail, cliquez sur **Machines virtuelles**.  
4. Dans la liste des **Machines virtuelles**, sélectionnez la machine virtuelle sur laquelle vous voulez installer l’agent. Notez que l’**État de la connexion OMS** de la machine virtuelle indique **Non connecté**.
5. Dans les détails de votre machine virtuelle, sélectionnez **Connecter**. L’agent est automatiquement installé et configuré pour votre espace de travail Log Analytics. Ce processus prend quelques minutes durant lesquelles l’**État** est **Connexion en cours**.
6. Une fois l’agent installé et connecté, l’**État de la connexion OMS** est mis à jour et indique **Cet espace de travail**.

## <a name="collect-event-and-performance-data"></a>Collecter les données d’événements et de performances
Log Analytics est capable de collecter les événements des journaux des événements Windows ou du journal Syslog Linux, ainsi que des compteurs de performance dans l’optique de procéder à une analyse à long terme et à générer des rapports pour agir dès lors qu’une condition particulière est détectée.  Pour configurer la collecte des événements du journal système Windows ou du journal Syslog Linux et de plusieurs compteurs de performances courants, commencez par suivre ces étapes.  

### <a name="data-collection-from-windows-vm"></a>Collecte de données à partir d’une machine virtuelle Windows
1. Sélectionnez **Paramètres avancés**.<br> ![Paramètres avancés de Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br> 
3. Sélectionnez **Données**, puis **Journaux des événements Windows**.  
4. Vous pouvez ajouter un journal d’événements en tapant le nom de ce journal.  Tapez **Système**, puis cliquez sur le signe plus **+**.  
5. Dans le tableau, vérifiez les niveaux de gravité **Erreur** et **Avertissement**.   
6. Cliquez sur **Enregistrer** en haut de la page pour enregistrer la configuration.
7. Sélectionnez **Données de performances Windows** pour activer la collecte des compteurs de performances sur un ordinateur Windows. 
8. Quand vous procédez à la configuration initiale des compteurs de performances Windows pour un nouvel espace de travail Log Analytics, la possibilité vous est offerte de créer rapidement plusieurs compteurs courants. Ils s’affichent avec une case à cocher en regard.<br> ![Compteurs de performances Windows par défaut sélectionnés](media/log-analytics-quick-collect-azurevm/windows-perfcounters-default.png).<br> Cliquez sur **Ajouter les compteurs de performances sélectionnés**.  Ils sont ajoutés et prédéfinis avec un intervalle d’échantillonnage de collecte de dix secondes.  
9. Cliquez sur **Enregistrer** en haut de la page pour enregistrer la configuration.

### <a name="data-collection-from-linux-vm"></a>Collecte de données à partir d’une machine virtuelle Linux

1. Sélectionnez **Syslog**.  
2. Vous pouvez ajouter un journal d’événements en tapant le nom de ce journal.  Tapez **Syslog**, puis cliquez sur le signe plus **+**.  
3. Dans le tableau, décochez les niveaux de gravité **Info**, **Avis** et **Débogage**. 
4. Cliquez sur **Enregistrer** en haut de la page pour enregistrer la configuration.
5. Sélectionnez **Données de performances Linux** pour activer la collecte des compteurs de performances sur un ordinateur Linux. 
6. Quand vous procédez à la configuration initiale des compteurs de performances Linux pour un nouvel espace de travail Log Analytics, la possibilité vous est offerte de créer rapidement plusieurs compteurs courants. Ils s’affichent avec une case à cocher en regard.<br> ![Compteurs de performances Windows par défaut sélectionnés](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png).<br> Cliquez sur **Ajouter les compteurs de performances sélectionnés**.  Ils sont ajoutés et prédéfinis avec un intervalle d’échantillonnage de collecte de dix secondes.  
7. Cliquez sur **Enregistrer** en haut de la page pour enregistrer la configuration.

## <a name="view-data-collected"></a>Afficher les données collectées
Maintenant que vous avez activé la collecte de données, exécutons un exemple élémentaire de recherche dans les journaux pour examiner certaines données des machines virtuelles cibles.  

1. Dans le portail Azure, accédez à Log Analytics et sélectionnez l’espace de travail créé précédemment.
2. Cliquez sur la vignette **Recherche dans les journaux**. Ensuite, dans le volet Recherche dans les journaux, dans le champ de requête, tapez `Perf`, puis appuyez sur Entrée ou cliquez sur le bouton de recherche à droite du champ de requête.<br> ![Exemple de requête de recherche dans les journaux Log Analytics](./media/log-analytics-quick-collect-azurevm/log-analytics-portal-perf-query.png)<br> 

   >[!NOTE]
   >Si votre espace de travail est créé dans le cloud Azure Government, vous utilisez la requête `Type=Perf`.  
   >

Par exemple, la requête présentée dans l’image suivante a retourné 78 000 enregistrements de performances.  Vos résultats seront bien moins abondants.<br> ![Résultat de la recherche dans les journaux Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Supprimer des ressources
Quand vous n’en avez plus besoin, supprimez l’espace de travail Log Analytics. Pour ce faire, sélectionnez l’espace de travail Log Analytics que vous avez créé précédemment puis, dans la page de ressource, cliquez sur **Supprimer**.<br> ![Supprimer la ressource Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous collectez des données opérationnelles et de performances à partir de vos machines virtuelles Windows ou Linux, vous pouvez commencer à explorer et à analyser facilement les données que vous collectez *gratuitement* et agir en conséquence.  

Pour savoir comment consulter et analyser les données, passez au didacticiel suivant.   

> [!div class="nextstepaction"]
> [Consulter ou analyser les données dans Log Analytics](log-analytics-tutorial-viewdata.md)
