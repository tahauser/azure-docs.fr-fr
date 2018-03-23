---
title: Connecter des ordinateurs à l’aide de la passerelle OMS Gateway | Microsoft Docs
description: Connectez vos appareils et les ordinateurs contrôlés par Operations Manager à la passerelle OMS pour leur permettre d’envoyer des données aux services Azure Automation et Log Analytics lorsqu’ils n’ont pas accès à Internet.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: magoedte
ms.openlocfilehash: 10422af5a57fc4da77958f15af5b2db61c023e40
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="connect-computers-without-internet-access-using-the-oms-gateway"></a>Connecter des ordinateurs sans accès Internet à l’aide de la passerelle OMS
Ce document décrit comment configurer la communication avec Azure Automation et Log Analytics à l’aide de la passerelle OMS lorsqu’elle est directement connectée ou lorsque les ordinateurs analysés Operations Manager n’ont pas accès à Internet.  La passerelle OMS, qui est un proxy de transfert HTP prenant en charge le tunneling HTTP à l’aide de la commande HTTP CONNECT, peut collecter des données et les envoyer au service OMS en son nom.  

La passerelle OMS prend en charge :

* Runbooks Workers hybrides Azure Automation  
* Ordinateurs Windows avec Microsoft Monitoring Agent directement connecté à un espace de travail Log Analytics
* Ordinateurs Linux avec Agent OMS pour Linux directement connecté à un espace de travail Log Analytics  
* System Center Operations Manager 2012 SP1 avec UR7, Operations Manager 2012 R2 avec UR3, Operations Manager 2016 et le groupe de gestion Operations Manager version 1801 intégré à Log Analytics.  

Si vos stratégies de sécurité informatique ne permettent pas aux ordinateurs de votre réseau de se connecter à Internet, comme des appareils de point de vente (PDV) ou des serveurs prenant en charge des services informatiques, mais que vous devez les connecter à Azure Automation ou Log Analytics pour les gérer et les surveiller, ils peuvent être configurés pour communiquer directement avec la plateforme OMS pour recevoir la configuration et faire suivre les données en leur nom.  Si ces ordinateurs sont configurés avec l’agent OMS pour se connecter directement à un espace de travail Log Analytics, tous les ordinateurs communiqueront plutôt avec la passerelle OMS.  La passerelle transfère directement les données des agents au service. Elle n’analyse aucune des données en transit.

Lorsqu’un groupe d’administration Operations Manager est intégré à Log Analytics, les serveurs d’administration peuvent être configurés pour se connecter à la passerelle OMS pour recevoir des informations de configuration et envoyer les données collectées en fonction de la solution que vous avez activée.  Les agents Operations Manager envoient certaines données liées à Operations Manager, telles que des alertes, évaluations de la configuration, espaces d’instance et données de capacité au serveur d’administration. D’autres données volumineuses, telles que les journaux IIS, les données de performances et les événements de sécurité, sont envoyées directement à la passerelle OMS.  Si vous avez un ou plusieurs serveurs de passerelle Operations Manager déployés dans une zone DMZ ou un autre réseau isolé pour analyser les systèmes non fiables, ils ne peuvent pas communiquer avec une passerelle OMS.  Les serveurs de passerelle Operations Manager peuvent uniquement générer des rapports sur un serveur d’administration.  Lorsqu’un groupe d’administration Operations Manager est configuré pour communiquer avec la passerelle OMS, les informations de configuration de proxy sont automatiquement distribuées à tous les ordinateurs gérés par agent qui sont configurés pour collecter des données pour Log Analytics, même si le paramètre est vide.    

Pour fournir une haute disponibilité pour les groupes directement connectés ou Operations Management qui communiquent avec Log Analytics via la passerelle, vous pouvez utiliser l’équilibrage de charge au niveau du réseau pour rediriger et distribuer le trafic entre plusieurs serveurs de passerelle.  Si un serveur de passerelle tombe en panne, le trafic est redirigé vers un autre nœud disponible.  

Il est recommandé d’installer l’agent OMS sur l’ordinateur exécutant le logiciel de passerelle OMS pour surveiller la passerelle OMS et analyser les données de performances ou d’événements. En outre, l’agent permet à la passerelle OMS d’identifier les points de terminaison de service dont il a besoin pour communiquer.

Chaque agent doit disposer d’une connexion réseau avec sa passerelle, afin que les agents puissent automatiquement transférer des données vers la passerelle et recevoir des données de cette dernière. L’installation de la passerelle sur un contrôleur de domaine n’est pas recommandée.

Le diagramme suivant affiche le flux de données entre les agents directs et Azure Automation avec Log Analytics utilisant le serveur de passerelle.  La configuration de proxy des agents doit correspondre au même port que celui sur lequel la passerelle OMS est configurée pour communiquer avec le service.  

![diagramme de communication directe entre l’agent et les services](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Le diagramme suivant illustre le flux de données entre un groupe d’administration Operations Manager et Log Analytics.   

![diagramme de communication entre Operations Manager et Log Analytics](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Prérequis


Lorsque vous configurez un ordinateur pour qu’il s’exécute sur la passerelle OMS, cet ordinateur doit disposer des éléments suivants :

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 et Windows Server 2008
* .NET Framework 4.5
* Au minimum un processeur 4 cœurs et 8 Go de mémoire 

### <a name="language-availability"></a>Langues disponibles

La passerelle OMS est disponible dans les langues suivantes :

- Chinois (simplifié)
- Chinois (traditionnel)
- Tchèque
- Néerlandais
- Français
- Français
- Allemand
- Hongrois
- Italien
- Japonais
- Coréen
- Polonais
- Portugais (Brésil)
- Portugais (Portugal)
- Russe
- Espagnol (international)

### <a name="supported-encryption-protocols"></a>Protocoles de chiffrement pris en charge
La passerelle OMS prend uniquement en charge le protocole TLS version 1.0, 1.1 et 1.2.  Elle ne prend pas en charge le protocole SSL.

### <a name="supported-number-of-agent-connections"></a>Nombre de connexion d’agent prises en charge
Le tableau suivant met en lumière le nombre d’agents pris en charge qui communiquent avec un serveur de passerelle.  Cette prise en charge est basée sur des agents qui chargent environ 200 Ko de données toutes les 6 secondes. Le volume de données par agent testé est d’environ 2,7 Go par jour.

|Passerelle |Nombre approximatif d’agents pris en charge|  
|--------|----------------------------------|  
|- Processeur : Intel XEON CPU E5-2660 v3 @ 2.6GHz 2 Cores<br> - Mémoire : 4 Go<br> Bande passante réseau : 1 Go/s| 600|  
|- Processeur : Intel XEON CPU E5-2660 v3 @ 2.6GHz 4 Cores<br> - Mémoire : 8 Go<br> Bande passante réseau : 1 Go/s| 1 000|  

## <a name="download-the-oms-gateway"></a>Télécharger la passerelle OMS

Il existe deux méthodes pour obtenir la toute dernière version du fichier de configuration de la passerelle OMS.

1. Téléchargez-le à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

2. Téléchargez à partir du portail Azure.  Après la connexion au portail Azure :  

   1. Parcourez la liste des services, puis sélectionnez **Log Analytics**.  
   2. Sélectionnez un espace de travail.
   3. Dans le panneau de votre espace de travail, sous **Général**, cliquez sur **Démarrage rapide**.
   4. Sous **Choisir une source de données pour se connecter à l’espace de travail**, cliquez sur **Ordinateurs**.
   5. Dans le panneau **Agent direct**, cliquez sur **Télécharger la passerelle OMS**.<br><br> ![télécharger la passerelle OMS](./media/log-analytics-oms-gateway/download-gateway.png)

or 

   1. Dans le panneau de votre espace de travail sous **Paramètres**, cliquez sur **Paramètres avancés**.
   2. Accédez à **Sources connectées** > **Serveurs Windows** et cliquez sur **Télécharger la passerelle de OMS**.

## <a name="install-the-oms-gateway"></a>Installer la passerelle OMS

Pour installer une passerelle, procédez comme suit.  Si vous avez installé une version antérieure, anciennement appelée *Log Analytics Forwarder*, il sera mis à niveau vers cette version.  

1. Dans le dossier de destination, double-cliquez sur **OMS Gateway.msi**.
2. Sur la page d’**accueil**, cliquez sur **Suivant**.<br><br> ![Assistant d’installation de la passerelle](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. Dans la page du **contrat de licence**, sélectionnez **J’accepte les termes du contrat de licence** pour accepter le CLUF, puis cliquez sur **Suivant**.
4. Sur la page relative au **port et à l’adresse proxy** :
   1. Saisissez le numéro de port TCP à utiliser pour la passerelle. Le programme d’installation configure une règle entrante avec ce numéro de port sur le pare-feu Windows.  La valeur par défaut est 8080.
      Les numéros de port valides sont compris entre 1 et 65535. Si la valeur saisie n’est pas comprise dans cette plage, un message d’erreur s’affiche.
   2. Éventuellement, si le serveur sur lequel la passerelle est installée doit communiquer via un proxy, saisissez l’adresse proxy à laquelle la passerelle doit se connecter. Par exemple : `http://myorgname.corp.contoso.com:80`.  Si le champ est vide, la passerelle essaie de se connecter à Internet directement.  Si votre serveur proxy requiert une authentification, entrez un nom d'utilisateur et un mot de passe.<br><br> ![Configuration du proxy de l’assistant d’installation de la passerelle](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
   3. Cliquez sur **Suivant**.
5. Si vous n’avez pas activé Microsoft Update, la page Microsoft Update s’affiche, et vous pouvez choisir de l’activer. Effectuez une sélection, puis cliquez sur **Suivant**. Sinon, passez à l’étape suivante.
6. Dans la page **Dossier de destination**, conservez le dossier par défaut C:\Program Files\OMS Gateway ou entrez l’emplacement où vous voulez installer la passerelle, puis cliquez sur **Suivant**.
7. Dans la page **Prêt pour l’installation**, cliquez sur **Installer**. Un contrôle de compte utilisateur peut apparaître et demander une autorisation d’installation. Dans ce cas, cliquez sur **Oui**.
8. Une fois l’installation terminée, cliquez sur **Terminer**. Vous pouvez vérifier que le service est en cours d’exécution en ouvrant le composant logiciel enfichable services.msc et en vérifiant que **Passerelle OMS** apparaît dans la liste des services et que son statut est défini sur **Exécution en cours**.<br><br> ![Services – Passerelle OMS](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Configuration de l’équilibrage de la charge réseau 
Vous pouvez configurer la passerelle pour la haute disponibilité à l’aide de l’équilibrage de charge réseau Microsoft ou basé sur le matériel.  L’équilibreur de charge gère le trafic en redirigeant les connexions demandées à partir des agents OMS ou des serveurs d’administration Operations Manager sur ses nœuds. Si un serveur de passerelle tombe en panne, le trafic est redirigé vers d’autres nœuds.

Pour apprendre à concevoir et déployer un cluster d’équilibrage de charge réseau Windows Server 2016, consultez [Équilibrage de charge réseau](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Les étapes suivantes décrivent comment configurer un cluster d’équilibrage de charge réseau Microsoft.  

1.  Connectez-vous au serveur Windows qui est membre du cluster d’équilibrage de charge réseau avec un compte d’administration.  
2.  Ouvrez le Gestionnaire d’équilibrage de charge réseau dans le Gestionnaire de serveur, cliquez sur **Outils**, puis sur **Gestionnaire d’équilibrage de charge réseau**.
3. Pour vous connecter à un serveur de passerelle OMS sur lequel Microsoft Monitoring Agent est installé, faites un clic droit sur l’adresse IP du cluster, puis cliquez sur **Ajouter l’hôte au cluster**.<br><br> ![Gestionnaire d’équilibrage de charge réseau – Ajouter l’hôte au cluster](./media/log-analytics-oms-gateway/nlb02.png)<br> 
4. Entrez l’adresse IP du serveur de passerelle que vous voulez connecter.<br><br> ![Gestionnaire d’équilibrage de charge réseau – Ajouter l’hôte au cluster : Connexion](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-oms-agent-and-operations-manager-management-group"></a>Configurer l’agent OMS et le groupe d’administration Operations Manager
La section suivante contient des étapes expliquant la configuration d’agents OMS connectés directement, d’un groupe d’administration Operations Manager ou Runbook Worker hybride Azure Automation avec la passerelle OMS pour communiquer avec Azure Automation ou Log Analytics.  

Pour comprendre les exigences et la procédure à suivre pour installer l’agent OMS sur des ordinateurs Windows connectés directement à Log Analytics, consultez [Connecter des ordinateurs Windows à Log Analytics](log-analytics-windows-agents.md) ou pour les ordinateurs Linux, consultez [Connecter des ordinateurs Linux à Log Analytics](log-analytics-quick-collect-linux-computer.md).  Pour plus d’informations relatives au traitement de Runbook Worker hybride Automation, consultez [Déployer Runbook Worker hybride](../automation/automation-hybrid-runbook-worker.md).

### <a name="configuring-the-oms-agent-and-operations-manager-to-use-the-oms-gateway-as-a-proxy-server"></a>Configuration de l’agent OMS et d’Operations Manager pour qu’ils utilisent la passerelle OMS comme serveur proxy

### <a name="configure-standalone-oms-agent"></a>Configurer l’agent OMS autonome
Consultez [Configurer les paramètres de pare-feu et de proxy avec Microsoft Monitoring Agent](log-analytics-proxy-firewall.md) pour plus d’informations sur la configuration d’un agent pour qu’il utilise un serveur proxy, dans ce cas, la passerelle.  Si vous avez déployé plusieurs serveurs de passerelle derrière un équilibreur de charge réseau, la configuration de proxy de l’agent OMS est l’adresse IP virtuelle de l’équilibrage de charge réseau :<br><br> ![Propriétés de Microsoft Monitoring Agent – Paramètres de proxy](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager---all-agents-use-the-same-proxy-server"></a>Configurer Operations Manager : tous les agents utilisent le même serveur proxy
Vous configurez Operations Manager pour ajouter le serveur de passerelle.  La configuration du proxy Operations Manager est automatiquement appliquée à tous les agents effectuant un rapport à Operations Manager, même si le paramètre est vide.  

Pour que la passerelle prenne en charge Operations Manager, vous devez disposer des éléments suivants :

* Microsoft Monitoring Agent (version de l’agent : **8.0.10900.0** ou version ultérieure) installé sur le serveur de passerelle et configuré pour des espaces de travail Log Analytics avec lequel vous voulez communiquer.
* La passerelle doit disposer d’une connectivité Internet, ou être connectée à un serveur proxy connecté à Internet.

> [!NOTE]
> Si vous ne spécifiez pas de valeur pour la passerelle, les valeurs vides sont transférées à tous les agents.
> 

S’il s’agit de la première fois que votre groupe d’administration Operations Manager est inscrit sur un espace de travail Log Analytics, l’option pour spécifier la configuration du proxy pour le groupe d’administration n’est pas disponible dans la console Opérations.  Le groupe d’administration doit être correctement inscrit auprès du service avant que cette option soit disponible.  Vous devez mettre à jour la configuration du proxy système à l’aide de Netsh sur le système à partir duquel vous exécutez la console Opérations pour configurer l’intégration et tous les serveurs d’administration du groupe d’administration.  

1. Ouvrez une invite de commande avec élévation de privilèges.
   a. Allez à **Démarrer** et entrez **cmd**.
   b. Cliquez avec le bouton de droite sur **Invite de commande** et sélectionnez Exécuter en tant qu’administrateur**.
2. Saisissez la commande suivante, puis appuyez sur **Entrée** :

    `netsh winhttp set proxy <proxy>:<port>`

À l’issue de l’intégration avec Log Analytics, vous pouvez supprimer la modification en exécutant `netsh winhttp reset proxy`puis, utilisez l’option **Configurer le serveur proxy** dans la console Opérations pour spécifier le serveur de la passerelle OMS. 

1. Ouvrez la console Operations Manager, puis, sous **Operations Management Suite**, cliquez sur **Connexion**, puis sur **Configurer le serveur proxy**.<br><br> ![Operations Manager – Configurer le serveur proxy](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. Sélectionnez **Utiliser un serveur proxy pour accéder à Operations Management Suite** puis saisissez l’adresse IP du serveur de la passerelle OMS ou l’adresse IP virtuelle de l’équilibrage de charge réseau. Vérifiez que vous démarrez avec le préfixe `http://`.<br><br> ![Operations Manager – Adresse du serveur proxy](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. Cliquez sur **Terminer**. Votre groupe d’administration Operations Manager est maintenant configuré pour communiquer via le serveur de passerelle pour le service Log Analytics.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Configurer Operations Manager : des agents spécifiques utilisent le serveur proxy
Pour les environnements complexes ou volumineux, vous pouvez souhaiter que seuls des serveurs spécifiques (ou groupes) utilisent le serveur de passerelle d’OMS.  Pour ces serveurs, vous ne pouvez pas mettre à jour l’agent Operations Manager directement, cette valeur étant remplacée par la valeur globale du groupe d’administration.  Au lieu de cela, vous devez remplacer la règle utilisée pour transférer ces valeurs.  

> [!NOTE] 
> Cette même technique de configuration peut être utilisée pour autoriser l’utilisation de plusieurs serveurs de passerelle OMS dans votre environnement.  Par exemple, vous pouvez exiger que des serveurs de passerelle OMS spécifiques soient spécifiés par région.
>  

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Création**.  
2. Dans l’espace de travail Création, sélectionnez **Règles**, puis cliquez sur **Étendue** dans la barre d’outils Operations Manager. Si ce bouton n’est pas disponible, vérifiez que vous avez bien sélectionné un objet, et non un dossier, dans le volet Analyse. La boîte de dialogue **Objets du pack de gestion de l’étendue** affiche une liste des classes, groupes ou objets couramment ciblés. 
3. Saisissez **Service d’intégrité** dans le champ **Rechercher** et sélectionnez-le dans la liste.  Cliquez sur **OK**.  
4. Recherchez la règle **Règle du paramètre proxy Advisor** et, dans la barre d’outils de la console Operations, cliquez sur **Remplace**, puis pointez sur **Remplacer la règle\D’un objet de classe spécifique : Service d’intégrité**, puis sélectionnez un objet spécifique dans la liste.  Si vous le souhaitez, vous pouvez créer un groupe personnalisé qui contient l’objet de service d’intégrité des serveurs auxquels vous souhaitez appliquer ce remplacement, puis appliquer le remplacement à ce groupe.
5. Dans la boîte de dialogue **Propriétés du remplacement**, cliquez pour placer une coche dans la colonne **Remplacer** en regard du paramètre **WebProxyAddress**.  Dans le champ **Remplacer la valeur**, entrez l’URL du serveur de passerelle OMS pour vérifier que vous démarrez avec le préfixe `http://`.  

    >[!NOTE]
    > Vous n’avez pas besoin d’activer la règle, car elle est déjà gérée automatiquement avec un remplacement contenu dans le pack d’administration Microsoft System Center Advisor Secure Reference Override ciblant le groupe Microsoft System Center Advisor Monitoring Server.
    >   

6. Sélectionnez un pack d’administration dans la liste **Sélectionner un pack d’administration de destination** ou créez un pack de gestion non scellé en cliquant sur **Nouveau**. 
7. Lorsque vous effectuez vos modifications, cliquez sur **OK**. 

### <a name="configure-for-automation-hybrid-workers"></a>Configuration pour les workers hybrides Automation
Si vous avez des Runbook Workers hybrides Automation dans votre environnement, les étapes suivantes fournissent des solutions manuelles de contournement temporaires pour configurer la passerelle afin qu’elle les prenne en charge.

Dans les étapes suivantes, vous devez connaître la région Azure dans laquelle réside le compte Automation. Pour localiser l’emplacement :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Sélectionnez le service Azure Automation.
3. Sélectionnez le compte Azure Automation approprié.
4. Regardez sa région sous **Emplacement**.<br><br> ![Portail Azure – Emplacement du compte Automation](./media/log-analytics-oms-gateway/location.png)  

Utilisez les tableaux suivants pour identifier l’URL pour chaque emplacement :

**URL de service de données d’exécution de la tâche**

| **location** | **URL** |
| --- | --- |
| Centre-Nord des États-Unis |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Europe de l'Ouest |we-jobruntimedata-prod-su1.azure-automation.net |
| États-Unis - partie centrale méridionale |scus-jobruntimedata-prod-su1.azure-automation.net |
| Est des États-Unis 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Canada central |cc-jobruntimedata-prod-su1.azure-automation.net |
| Europe du Nord |ne-jobruntimedata-prod-su1.azure-automation.net |
| Asie du Sud-Est |sea-jobruntimedata-prod-su1.azure-automation.net |
| Inde centrale |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japon |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australie |ase-jobruntimedata-prod-su1.azure-automation.net |

**URL de service de l’agent**

| **location** | **URL** |
| --- | --- |
| Centre-Nord des États-Unis |ncus-agentservice-prod-1.azure-automation.net |
| Europe de l'Ouest |we-agentservice-prod-1.azure-automation.net |
| États-Unis - partie centrale méridionale |scus-agentservice-prod-1.azure-automation.net |
| Est des États-Unis 2 |eus2-agentservice-prod-1.azure-automation.net |
| Canada central |cc-agentservice-prod-1.azure-automation.net |
| Europe du Nord |ne-agentservice-prod-1.azure-automation.net |
| Asie du Sud-Est |sea-agentservice-prod-1.azure-automation.net |
| Inde centrale |cid-agentservice-prod-1.azure-automation.net |
| Japon |jpe-agentservice-prod-1.azure-automation.net |
| Australie |ase-agentservice-prod-1.azure-automation.net |

Si votre ordinateur est automatiquement inscrit en tant que Runbook Worker hybride pour l’application de correctifs à l’aide de la solution de gestion des mises à jour, suivez ces étapes :

1. Ajoutez les URL de service de données d’exécution de la tâche à la liste d’hôtes autorisés sur la passerelle OMS. Par exemple : `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Redémarrez le service de passerelle OMS en utilisant la cmdlet PowerShell suivante : `Restart-Service OMSGatewayService`

Si votre ordinateur est intégré à Azure Automation à l’aide de la cmdlet d’inscription Runbook Worker hybride, suivez ces étapes :

1. Ajoutez l’URL d’enregistrement de service de l’agent à la liste d’hôtes autorisés sur la passerelle OMS. Par exemple : `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Ajoutez les URL de service de données d’exécution de la tâche à la liste d’hôtes autorisés sur la passerelle OMS. Par exemple : `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Redémarrez le service de passerelle OMS.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Applets de commande PowerShell utiles
Les applets de commande peuvent vous aider à effectuer des tâches qui sont nécessaires pour mettre à jour les paramètres de configuration de la passerelle OMS. Avant de les utiliser, veillez à suivre les instructions suivantes :

1. Installez la passerelle OMS (MSI).
2. Ouvrez une fenêtre de console PowerShell.
3. Pour importer le module, saisissez cette commande : `Import-Module OMSGateway`
4. Si aucune erreur ne s’est produite lors des étapes précédentes, cela signifie que le module a été importé, et que les cmdlets peuvent être utilisées. Saisissez `Get-Module OMSGateway`
5. Une fois que vous avez apporté des modifications à l’aide des applets de commande, assurez-vous de redémarrer le service de passerelle.

Si vous obtenez une erreur à l’étape 3, cela signifie que le module n’a pas été importé. L’erreur peut se produire lorsque PowerShell ne trouve pas le module. Vous pouvez le trouver dans le dossier d’installation de la passerelle : *C:\Program File\Microsoft OMS Gateway\PowerShell.*

| **Applet de commande** | **Paramètres** | **Description** | **Exemple** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Clé |Récupère la configuration du service |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Clé (obligatoire) <br> Valeur |Modifie la configuration du service |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Récupère l’adresse du proxy de relais (en amont) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adresse<br> Nom d’utilisateur<br> Mot de passe |Définit l’adresse (et les informations d’identification) du proxy de relais (en amont) |1. Définir un proxy relais et des informations d’identification :<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Définir un proxy relais n’exigeant pas d’authentification : `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Effacer le paramètre de proxy relais :<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Récupère l’hôte actuellement autorisé (uniquement l’hôte autorisé configuré localement, pas les hôtes autorisés téléchargés automatiquement) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Hôte (obligatoire) |Ajoute l’hôte à la liste d’éléments autorisés |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Hôte (obligatoire) |Supprime l’hôte de la liste d’éléments autorisés |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Objet (obligatoire) |Ajoute l’objet du certificat client à la liste d’éléments autorisés |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Objet (obligatoire) |Supprime l’objet du certificat client de la liste d’éléments autorisés |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Récupère les objets de certificat client actuellement autorisés (uniquement les objets configurés localement, pas les objets autorisés téléchargés automatiquement) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Résolution de problèmes
Pour collecter des événements journalisés par la passerelle, vous devez également avoir installé l’agent OMS.<br><br> ![Observateur d’événements – Journal de la passerelle OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**Identifiants et descriptions des événements de la passerelle OMS**

Le tableau suivant montre les identifiants et descriptions d’événements pour les événements du journal de la passerelle OMS.

| **Identifiant** | **Description** |
| --- | --- |
| 400 |Toute erreur d’application ne disposant pas d’un identifiant spécifique |
| 401 |Configuration incorrecte. Par exemple : listenPort = "texte" au lieu d’un nombre entier |
| 402 |Exception lors de l’analyse des messages de liaison TLS |
| 403 |Erreur de mise en réseau. Par exemple : impossible de se connecter au serveur cible |
| 100 |Informations générales |
| 101 |Le service a démarré |
| 102 |Le service s’est arrêté |
| 103 |Réception d’une commande HTTP CONNECT de la part du client |
| 104 |L’élément n’est pas une commande HTTP CONNECT |
| 105 |Le serveur de destination n’est pas dans la liste autorisée ou le port de destination n’est pas un port sécurisé (443) <br> <br> Vérifiez que l’agent MMA sur votre serveur de passerelle et les agents communiquant avec la passerelle sont connectés au même espace de travail Log Analytics. |
| 105 |ERREUR TcpConnection – Certificat client non valide : CN=passerelle <br><br> Assurez-vous que : <br>    <br> &#149; Vous utilisez une passerelle ayant le numéro de version 1.0.395.0 ou supérieur. <br> &#149; L’agent MMA sur votre serveur de passerelle et les agents communiquant avec la passerelle sont connectés au même espace de travail Log Analytics. |
| 106 |La passerelle OMS prend uniquement en charge le protocole TLS 1.0, 1.1 et 1.2.  Elle ne prend pas en charge le protocole SSL. Pour toute version de protocole TLS/SSL non prise en charge, la passerelle OMS génère un ID d’événement 106.|
| 107 |La session TLS a été vérifiée |

**Compteurs de performances à collecter**

Le tableau suivant montre les compteurs de performances disponibles pour la passerelle OMS. Vous pouvez ajouter des compteurs à l’aide de l’Analyseur de performances.

| **Name** | **Description** |
| --- | --- |
| Passerelle OMS/Connexion du client actif |Nombre de connexions du réseau client actif (TCP) |
| Passerelle OMS/Nombre d’erreurs |Nombre d’erreurs |
| Passerelle OMS/Client connecté |Nombre de clients connectés |
| Passerelle OMS/Nombre de rejets |Nombre de rejets dus à des erreurs de validation de TLS |

![Compteurs de performances de la passerelle OMS](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Obtenir de l’aide
Lorsque connecté au portail Azure, vous pouvez créer une demande d’assistance avec la passerelle OMS ou tout autre service Azure ou toute fonctionnalité d’un service.
Pour demander de l’aide, cliquez sur le symbole en forme de point d’interrogation dans le coin supérieur droit du portail, puis cliquez sur **Nouvelle demande de support**. Ensuite, remplissez le nouveau formulaire de demande de support.

![Nouvelle demande de support](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>Étapes suivantes
[Ajoutez des sources de données](log-analytics-data-sources.md) pour collecter des données à partir de vos sources connectées et les stocker dans votre espace de travail Log Analytics.
