---
title: "Connexion d’Operations Manager à Log Analytics | Microsoft Docs"
description: "Pour conserver vos investissements existants dans System Center Operations Manager et utiliser des fonctionnalités étendues avec Log Analytics, vous pouvez intégrer Operations Manager à votre espace de travail OMS."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2017
ms.author: magoedte
ms.openlocfilehash: 6db47c7baa0a345a32d26d56e843acd0204ae50b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="connect-operations-manager-to-log-analytics"></a>Connexion d’Operations Manager à Log Analytics
Pour conserver vos investissements existants dans System Center Operations Manager et utiliser des fonctionnalités étendues avec Log Analytics, vous pouvez intégrer Operations Manager à votre espace de travail OMS.  Cela vous permet de tirer parti des possibilités qu’offre OMS, tout en continuant à utiliser Operations Manager pour :

* Continuer à surveiller l’intégrité de vos services informatiques avec Operations Manager
* Conserver l’intégration avec vos solutions ITSM pour la gestion des incidents et des problèmes
* Gérer le cycle de vie des agents déployés sur des machines virtuelles IaaS, qu’elles soient locales ou dans le cloud public, et que vous surveillez avec Operations Manager

L’intégration à System Center Operations Manager enrichit votre stratégie d’opérations de service en utilisant la vitesse et l’efficacité d’OMS pour la collecte, le stockage et l’analyse des données d’Operations Manager.  OMS seconde votre processus existant de gestion des problèmes. Il vous aide ainsi à mettre en corrélation et à identifier les erreurs et les expositions répétées.   La flexibilité du moteur de recherche en matière d’examen des performances, des événements et des données d’alerte, ainsi que les tableaux de bord complets et les fonctions de création de rapports permettant d’exposer ces données de manière utile, illustrent la puissance qu’OMS apporte à Operations Manager.

Les agents envoyant des rapports au groupe d’administration Operations Manager collectent des données de vos serveurs, basées sur les sources de données et solutions Log Analytics que vous avez activées dans votre abonnement OMS.  En fonction de la solution que vous avez activée, les données de ces solutions sont soit envoyées directement à partir d’un serveur de gestion Operations Manager au service web OMS, ou, en raison du volume de données collecté sur le système géré par l’agent, envoyées directement à partir de l’agent au service web OMS. Le serveur de gestion transfère directement les données OMS au service web OMS. Celles-ci ne sont jamais écrites dans la base de données OperationsManager ou OperationsManagerDW.  Lorsqu’un serveur de gestion perd la connexion au service web OMS, il met en cache les données localement jusqu’à ce que la communication avec OMS soit rétablie.  Si le serveur de gestion est hors ligne en raison d’une maintenance planifiée ou d’une interruption non planifiée, un autre serveur de gestion du groupe d’administration assure la connectivité avec OMS.  

Le diagramme suivant représente la connexion entre les serveurs et agents d’administration dans un groupe d’administration System Center Operations Manager et OMS, notamment la direction et les ports.   

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

Si vos stratégies de sécurité n’autorisent pas les ordinateurs sur votre réseau à se connecter à Internet, les serveurs d’administration peuvent être configurés pour se connecter à la passerelle OMS afin de recevoir des informations de configuration et d’envoyer les données collectées en fonction de la solution que vous avez activée.  Pour plus d’informations et pour savoir comment configurer votre groupe d’administration Operations Manager pour communiquer via une passerelle OMS avec le service OMS, consultez [Connecter des ordinateurs à OMS en utilisant la passerelle OMS](log-analytics-oms-gateway.md).  

## <a name="system-requirements"></a>Conditions requises pour le système
Avant de commencer, prenez connaissance des informations suivantes pour vérifier que les conditions préalables sont remplies.

* OMS prend uniquement en charge Operations Manager 2016, Operations Manager 2012 SP1 UR6 et versions supérieures et Operations Manager 2012 R2 UR2 et versions supérieures.  La prise en charge du proxy a été ajoutée dans Operations Manager 2012 SP1 UR7 et Operations Manager 2012 R2 UR3.
* Tous les agents Operations Manager doivent répondre aux exigences en matière de prise en charge. Vérifiez que chaque agent est au niveau minimum de mise à jour ; sinon, le trafic de l’agent Windows échouera, entraînant de nombreuses erreurs qui risquent de saturer le journal des événements Operations Manager.
* Un abonnement OMS.  Pour plus d’informations, consultez [Prise en main de Log Analytics](log-analytics-get-started.md).

### <a name="network"></a>Réseau
Les informations ci-dessous indiquent les informations de configuration de proxy et de pare-feu requises pour permettre à l’agent Operations Manager, aux serveurs d’administration et à la console Operations de communiquer avec OMS.  Le trafic provenant de chaque composant sort de votre réseau et se dirige vers le service OMS.     

|Ressource | Numéro de port| Ignorer l’inspection HTTP|  
|---------|------|-----------------------|  
|**Agent**|||  
|\*.ods.opinsights.azure.com| 443 |Oui|  
|\*.oms.opinsights.azure.com| 443|Oui|  
|\*.blob.core.windows.net| 443|Oui|  
|\*.azure-automation.net| 443|Oui|  
|**Serveur d’administration**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Oui|  
|\*.ods.opinsights.azure.com| 443| Oui|  
|*.azure-automation.net | 443| Oui|  
|**Console Operations Manager pour OMS**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 et 443||  
|\*.microsoft.com| 80 et 443||  
|\*.microsoftonline.com| 80 et 443||  
|\*.mms.microsoft.com| 80 et 443||  
|login.windows.net| 80 et 443||  


## <a name="connecting-operations-manager-to-oms"></a>Connexion d’Operations Manager à OMS
Suivez les étapes ci-après pour configurer votre groupe d’administration Operations Manager de façon à ce qu’il se connecte à l’un de vos espaces de travail OMS.

1. Dans la console Operations Manager, sélectionnez l’espace de travail **Administration** .
2. Développez le nœud Operations Management Suite, puis cliquez sur **Connexion**.
3. Cliquez sur le lien **S’inscrire auprès de Operations Management Suite** .
4. Dans la page **Assistant Intégration d’Operations Management Suite : authentification**, entrez l’adresse e-mail ou le numéro de téléphone et le mot de passe du compte d’administrateur associés à votre abonnement OMS, puis cliquez sur **Se connecter**.
5. Une fois authentifié, vous êtes invité à sélectionner votre espace de travail OMS dans la page **Assistant Intégration d’Operations Management Suite : sélectionner un espace de travail**.  Si vous avez plusieurs espaces de travail, sélectionnez celui que vous souhaitez inscrire auprès du groupe d’administration d’Operations Manager dans la liste déroulante, puis cliquez sur **Suivant**.
   
   > [!NOTE]
   > Operations Manager prend uniquement en charge un espace de travail OMS à la fois. La connexion et les ordinateurs enregistrés sur OMS avec l’espace de travail précédent sont supprimés d’OMS.
   > 
   > 
6. Dans la page **Assistant Intégration d’Operations Management Suite : résumé**, vérifiez vos paramètres. S’ils sont corrects, cliquez sur **Créer**.
7. Dans la page **Assistant Intégration d’Operations Management Suite : fin**, cliquez sur **Fermer**.

### <a name="add-agent-managed-computers"></a>Ajout d’ordinateurs gérés par des agents
La configuration de l’intégration avec votre espace de travail OMS établit uniquement une connexion à OMS, sans qu’aucune donnée soit collectée par les agents générant des rapports pour votre groupe d’administration. Les données seront uniquement collectées lorsque vous aurez configuré les ordinateurs gérés par des agents qui seront chargés de collecter les données pour Log Analytics. Vous pouvez sélectionner les objets d’ordinateur individuellement ou sélectionner un groupe qui contient des objets d’ordinateur Windows. Vous ne pouvez pas sélectionner un groupe qui contient des instances d’une autre classe, telles que des disques logiques ou des bases de données SQL.

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Administration** .
2. Développez le nœud Operations Management Suite, puis cliquez sur **Connexion**.
3. Cliquez sur le lien **Ajouter un ordinateur/groupe** sous l’en-tête Actions dans la partie droite du volet.
4. Vous pouvez rechercher des ordinateurs ou des groupes surveillés par Operations Manager dans la boîte de dialogue **Recherche d’ordinateurs**. Sélectionnez les ordinateurs ou groupes à intégrer à OMS, cliquez sur **Ajouter**, puis sur **OK**.

Vous pouvez afficher les ordinateurs et les groupes configurés pour collecter des données à partir du nœud Ordinateurs gérés, sous Operations Management Suite, dans l’espace de travail **Administration** de la console Opérateur.  De là, vous pouvez ajouter ou supprimer des ordinateurs et des groupes selon les besoins.

### <a name="configure-oms-proxy-settings-in-the-operations-console"></a>Configuration des paramètres de proxy OMS dans la console Operations
Si un serveur proxy interne se trouve entre le groupe d’administration et le service web OMS, procédez comme suit.  Ces paramètres sont gérés de manière centralisée à partir du groupe d’administration et distribués aux systèmes gérés par des agents qui sont inclus dans l’étendue de collecte des données pour OMS.  Cette méthode présente des avantages lorsque certaines solutions contournent le serveur de gestion et envoient les données directement au service web OMS.

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Administration** .
2. Développez Operations Management Suite, puis cliquez sur **Connexions**.
3. Dans la vue Connexion à OMS, cliquez sur **Configurer le serveur proxy**.
4. Dans la page **Assistant Operations Management Suite : serveur proxy**, sélectionnez **Utiliser un serveur proxy pour accéder à Operations Management Suite**, puis tapez l’URL avec le numéro de port (par exemple, http://corpproxy:80), puis cliquez sur **Terminer**.

Si votre serveur proxy requiert une authentification, procédez comme suit pour configurer les informations d’identification et les paramètres qui doivent se propager sur les ordinateurs gérés qui envoient des rapports à OMS dans le groupe d’administration.

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Administration** .
2. Sous **Configuration d’identification**, sélectionnez **Profils**.
3. Ouvrez le profil **Proxy d'identification de System Center Advisor** .
4. Dans l’Assistant Profil d’identification, cliquez sur Ajouter pour utiliser un compte d’identification. Vous pouvez créer un [compte d’identification](https://technet.microsoft.com/library/hh321655.aspx) ou utiliser un compte existant. Ce compte doit disposer des autorisations suffisantes pour franchir le serveur proxy.
5. Pour définir le compte à gérer, choisissez **Une classe, un groupe ou un objet sélectionné(e)**, cliquez sur **Sélectionner...** puis sur **Groupe...** Pour ouvrir la boîte de dialogue **Recherche de groupes**.
6. Recherchez le **groupe Microsoft System Center Advisor Monitoring Server**, puis sélectionnez-le.  Une fois le groupe sélectionné, cliquez sur **OK** pour fermer la zone **Recherche de groupes**.
7. Cliquez sur **OK** pour fermer la zone **Ajouter un compte d’identification**.
8. Cliquez sur **Enregistrer** pour fermer l’Assistant et enregistrer vos modifications.

Une fois que la connexion est créée et que vous avez configuré les agents qui collectent et envoient les données à OMS, la configuration suivante est appliquée au groupe d’administration (l’ordre peut varier) :

* Le compte d’identification **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** est créé.  Il est associé au profil **Blob du profil d’identification de Microsoft System Center Advisor** et cible deux classes : **Collection Server** et **Operations Manager Management Group**.
* Deux connecteurs sont créés.  Le premier se nomme **Microsoft.SystemCenter.Advisor.DataConnector**. Il est automatiquement configuré avec un abonnement qui transfère toutes les alertes générées par les instances de toutes les classes du groupe d’administration à OMS Log Analytics. Le second connecteur, **Advisor Connector**, est chargé de communiquer avec le service web OMS et de partager les données.
* Les agents et les groupes sélectionnés pour la collecte de données dans le groupe d’administration sont ajoutés au **groupe Microsoft System Center Advisor Monitoring Server**.

## <a name="management-pack-updates"></a>Mises à jour du pack d’administration
Une fois que la configuration est terminée, le groupe d’administration Operations Manager établit une connexion avec le service OMS.  Le serveur d’administration est synchronisé au service web et reçoit des informations de configuration mises à jour sous la forme de packs d’administration pour les solutions que vous avez activées et qui s’intègrent à Operations Manager.   Operations Manager recherche des mises à jour de ces packs d’administration, puis les télécharge et les importe automatiquement lorsqu’elles sont disponibles.  Deux règles principales contrôlent ce processus :

* **Microsoft.SystemCenter.Advisor.MPUpdate** : met à jour les packs d’administration OMS de base. S’exécute toutes les 12 heures par défaut.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** : met à jour les packs d’administration de solution activés dans votre espace de travail. Par défaut, elle s’exécute toutes les cinq (5) minutes.

Vous pouvez désactiver ces deux règles pour empêcher le téléchargement automatique ou modifier la fréquence de la synchronisation du serveur d’administration avec OMS pour déterminer si un nouveau pack d’administration est disponible et doit être téléchargé.  Suivez les étapes de la [procédure de remplacement d’une règle ou d’une analyse](https://technet.microsoft.com/library/hh212869.aspx) pour modifier la valeur en secondes du paramètre **Fréquence** afin de changer la fréquence de synchronisation, ou pour modifier le paramètre **Activé** afin de désactiver les règles.  Pour le remplacement, ciblez tous les objets de la classe Groupe d’administration Operations Manager.

Si vous souhaitez continuer à suivre votre processus habituel de contrôle des modifications pour contrôler les versions du pack d’administration dans votre groupe d’administration de production, vous pouvez désactiver les règles et les activer à des heures auxquelles les mises à jour sont autorisées. Si votre environnement dispose d’un groupe d’administration du développement ou de l’assurance qualité connecté à Internet, vous pouvez configurer ce groupe d’administration avec un espace de travail OMS de façon à ce qu’il prenne en charge ce scénario.  Ainsi, vous pourrez examiner et évaluer les versions itératives des packs d’administration OMS avant de les introduire dans votre groupe d’administration de production.

## <a name="switch-an-operations-manager-group-to-a-new-oms-workspace"></a>Basculer un groupe Operations Manager vers un nouvel espace de travail OMS
1. Connectez-vous à votre abonnement OMS et créez un espace de travail dans [Microsoft Operations Management Suite](http://oms.microsoft.com/).
2. Ouvrez la console Operations Manager à l’aide d’un compte qui est membre du rôle Operations Manager Administrators, puis sélectionnez l’espace de travail **Administration** .
3. Développez Operations Management Suite, puis sélectionnez **Connexions**.
4. Sélectionnez le lien **Reconfigurer Operation Management Suite** au milieu du volet.
5. Dans la page **Assistant Intégration d’Operations Management Suite** , entrez l’adresse e-mail ou le numéro de téléphone et le mot de passe du compte d’administrateur associé à votre nouvel espace de travail OMS.
   
   > [!NOTE]
   > La page **Assistant Intégration d’Operations Management Suite : sélectionner un espace de travail** indique l’espace de travail existant en cours d’utilisation.
   > 
   > 

## <a name="validate-operations-manager-integration-with-oms"></a>Valider l’intégration entre Operations Manager et OMS
Vous pouvez vérifier que votre intégration entre OMS et Operations Manager a réussi de plusieurs manières.

### <a name="to-confirm-integration-from-the-oms-portal"></a>Pour confirmer l’intégration à partir du portail OMS
1. Dans le portail OMS, cliquez sur la vignette **Paramètres**.
2. Sélectionnez **Sources connectées**.
3. Dans la table, sous la section System Center Operations Manager, vous devriez voir le nom du groupe d’administration avec le nombre d’agents et son état lors de la dernière réception de données.
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)
4. Notez la valeur **ID de l’espace de travail** sous la partie gauche de la page Paramètres.  Vous la validez par rapport à votre groupe d’administration Operations Manager ci-dessous.  

### <a name="to-confirm-integration-from-the-operations-console"></a>Pour confirmer l’intégration à partir de la console Operations
1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Administration** .
2. Sélectionnez **Packs d’administration**, puis, dans la zone de texte **Rechercher**, tapez **Advisor** ou **Intelligence**.
3. En fonction des solutions activées, le pack d’administration correspondant apparaît dans les résultats de recherche.  Par exemple, si vous avez activé la solution de gestion des alertes, le pack d’administration Gestion des alertes Microsoft System Center Advisor figure dans la liste.
4. À partir de la vue **Analyse**, accédez à la vue **Operations Management Suite\État d’intégrité**.  Sélectionnez un serveur d’administration sous le volet **État du serveur d’administration**. Ensuite, dans le volet **Affichage Détails**, vérifiez que la valeur de la propriété **URI du service d’authentification** correspond à l’ID de l’espace de travail OMS.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-oms"></a>Supprimer l’intégration à OMS
Si l’intégration entre votre groupe d’administration Operations Manager et votre espace de travail OMS est désormais inutile, vous devez effectuer plusieurs étapes pour supprimer correctement la connexion et la configuration dans le groupe d’administration. Dans la procédure suivante, vous mettez à jour votre espace de travail OMS en supprimant la référence de votre groupe d’administration, vous supprimez les connecteurs OMS, puis vous supprimez les packs d’administration prenant en charge OMS.   

Les packs d’administration pour les solutions que vous avez activées qui s’intègrent avec Operations Manager et les packs d’administration requis pour prendre en charge l’intégration au service OMS ne peuvent pas être facilement supprimés du groupe d’administration.  Ce comportement est dû au fait que certains des packs d’administration OMS ont des dépendances avec d’autres packs d’administration associés.  Pour supprimer les packs d’administration qui ont une dépendance vis-à-vis d’autres packs d’administration, téléchargez le script [Supprimer un pack d’administration avec des dépendances](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) à partir du centre de scripts TechNet.  

1. Ouvrez l’interface de commande de Microsoft Operations Manager à l’aide d’un compte qui est membre du rôle Administrateurs Operations Manager.
   
    > [!WARNING]
    > Avant de continuer, vérifiez que les noms des packs d’administration personnalisés ne contiennent pas « Advisor » ou « IntelligencePack » ; sinon, les étapes suivantes les supprimeront du groupe d’administration.
    > 

2. À l’invite de l’interpréteur de commandes, tapez `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
3. Ensuite, tapez `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
4. Pour supprimer les packs d’administration restants qui ont une dépendance sur les autres packs d’administration System Center Advisor, utilisez le script *RecursiveRemove.ps1* que vous avez téléchargé à partir du centre de scripts TechNet.  
 
    > [!NOTE]
    > Ne supprimez pas les packs d’administration Microsoft System Center Advisor ou Microsoft System Center Advisor Internal.  
    >  

5. Ouvrez la console Opérateur Operations Manager à l’aide d’un compte qui est membre du rôle Administrateurs Operations Manager.
6. Sous **Administration**, sélectionnez le nœud **Packs d’administration**. Ensuite, dans la zone **Rechercher**, tapez **Advisor** et vérifiez que les packs d’administration suivants sont toujours importés dans votre groupe d’administration :
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal
7. Dans le portail OMS, cliquez sur la vignette **Paramètres**.
8. Sélectionnez **Sources connectées**.
9. Dans le tableau sous la section System Center Operations Manager, le nom du groupe d’administration à supprimer de l’espace de travail doit s’afficher.  Sous la colonne **Dernières données**, cliquez sur **Supprimer**.  
   
    > [!NOTE]
    > Le lien **Supprimer** ne sera pas disponible avant 14 jours si aucune activité n’est détectée à partir du groupe d’administration connecté.  
    > 

10. Une fenêtre s’affiche pour vous demander de confirmer la suppression.  Cliquez sur **Oui** pour continuer. 

Pour supprimer les deux connecteurs (Microsoft.SystemCenter.Advisor.DataConnector et Advisor Connector), enregistrez le script PowerShell ci-dessous sur votre ordinateur et exécutez-le en suivant les exemples ci-dessous :

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> Si l’ordinateur à partir duquel vous exécutez ce script n’est pas un serveur d’administration, vous devez installer l’interpréteur de commandes Operations Manager selon la version de votre groupe d’administration.
> 
> 

```
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

Si, par la suite, vous souhaitez reconnecter votre groupe d’administration à un espace de travail OMS, vous devrez réimporter le fichier de pack d’administration `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` à partir du dernier correctif cumulatif appliqué à votre groupe d’administration.  Ce fichier se trouve dans le dossier `%ProgramFiles%\Microsoft System Center 2012` ou `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Étapes suivantes
Reportez-vous à [Ajoutez des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md) pour ajouter des fonctionnalités et collecter des données.


