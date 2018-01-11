---
title: "Utiliser PowerShell pour créer une machine virtuelle avec un serveur de rapports en mode natif | Microsoft Docs"
description: "Cette rubrique explique comment déployer et configurer un serveur de rapports SQL Server Reporting Services en mode natif dans une machine virtuelle Azure. "
services: virtual-machines-windows
documentationcenter: na
author: guyinacube
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 553af55b-d02e-4e32-904c-682bfa20fa0f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: asaxton
ms.openlocfilehash: 0b9f12127276f5aa689c4a1d3a5bf9fe645a0fc7
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Utiliser PowerShell pour créer une machine virtuelle Azure avec un serveur de rapports en mode natif
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

Cette rubrique explique comment déployer et configurer un serveur de rapports SQL Server Reporting Services en mode natif dans une machine virtuelle Azure. Les procédures décrites dans ce document comportent une combinaison d’étapes manuelles permettant de créer la machine virtuelle et un script Windows PowerShell afin de configurer Reporting Services sur la machine virtuelle. Le script de configuration inclut l’ouverture d’un port de pare-feu pour HTTP ou HTTPs.

> [!NOTE]
> Si vous n’avez pas besoin de **HTTPS** sur le serveur de rapports, **ignorez l’étape 2**.
> 
> Après avoir créé la machine virtuelle à l’étape 1, passez à la section Utiliser un script pour configurer le serveur de rapports et HTTP. Le serveur de rapports est prêt à l’emploi après l’exécution du script.

## <a name="prerequisites-and-assumptions"></a>Configuration requise et hypothèses
* **Abonnement Azure**: vérifiez le nombre de mémoires à tores magnétiques disponibles dans votre abonnement Azure. Si vous créez une machine virtuelle de la taille recommandée **A3**, vous devez disposer de **4** mémoires à tores magnétiques. Si vous utilisez une machine virtuelle de taille **A2**, vous devez disposer de **2** mémoires à tores magnétiques.
  
  * Pour vérifier la limite de mémoires à tores magnétiques de votre abonnement, dans le portail Azure, cliquez sur PARAMÈTRES dans le volet de gauche, puis sur UTILISATION dans le menu supérieur.
  * Pour augmenter le quota de mémoires à tores magnétiques, contactez le [support technique Azure](https://azure.microsoft.com/support/options/). Pour plus d’informations sur les tailles de machines virtuelles, consultez la page [Tailles de machines virtuelles pour Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* **Écriture de scripts avec Windows PowerShell**: la rubrique part du principe que vous avez une connaissance de base de Windows PowerShell. Pour plus d’informations sur l’utilisation de Windows PowerShell, consultez les rubriques suivantes :
  
  * [Démarrage de Windows PowerShell sur Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
  * [Prise en main de Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Étape 1 : Approvisionner une machine virtuelle Azure
1. Accédez au portail Azure.
2. Cliquez sur **Machines virtuelles** dans le volet de gauche.
   
    ![microsoft azure virtual machines](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. Cliquez sur **Nouveau**.
   
    ![bouton nouveau](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. Cliquez sur **À partir de la galerie**.
   
    ![nouvelle machine virtuelle à partir de la galerie](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. Cliquez sur **SQL Server 2014 RTM Standard – Windows Server 2012 R2** , puis sur la flèche pour continuer.
   
    ![Suivant](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Si vous avez besoin de la fonctionnalité d’abonnements pilotés par les données de Reporting Services, choisissez **SQL Server 2014 RTM Enterprise – Windows Server 2012 R2**. Pour plus d’informations sur les éditions de SQL Server et les fonctionnalités prises en charge, consultez la page [Fonctionnalités prises en charge par les éditions de SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).
6. Sur la page **Configuration de la machine virtuelle** , renseignez les champs suivants comme indiqué :
   
   * S’il existe plusieurs **DATES DE SORTIE DE LA VERSION**, sélectionnez la version la plus récente.
   * **Nom de la machine virtuelle**: le nom de la machine est également utilisé sur la page de configuration suivante comme nom DNS par défaut du service cloud. Le nom DNS doit être unique dans le service Azure. Envisagez de configurer la machine virtuelle avec un nom qui décrit à quoi elle sert. Par exemple, ssrsnativecloud.
   * **Niveau**: Standard
   * **Taille : A3** est la taille de machine virtuelle recommandée pour les charges de travail SQL Server. Si une machine virtuelle est utilisée uniquement comme serveur de rapports, la taille A2 est suffisante, sauf si la charge de travail du serveur est importante. Pour connaître les informations de tarification des machines virtuelles, consultez la page [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/).
   * **Nouveau nom d’utilisateur**: le nom que vous fournissez est créé en tant qu’administrateur sur la machine virtuelle.
   * **Nouveau mot de passe** et **confirmation**. Ce mot de passe est utilisé pour le nouveau compte Administrateur. Il est recommandé d’utiliser un mot de passe fort.
   * Cliquez sur **Suivant**. ![next](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. Sur la page suivante, renseignez les champs suivants comme indiqué :
   
   * **Service cloud** : sélectionnez **Créer un service cloud**.
   * **Nom DNS du service cloud**: il s’agit du nom DNS public du service cloud associé à la machine virtuelle. Le nom par défaut est le nom que vous avez saisi comme nom de la machine virtuelle. Si, dans les étapes ultérieures de la rubrique, vous créez un certificat SSL approuvé, le nom DNS est utilisé pour la valeur «**Issued to**» du certificat.
   * **Région/Groupe d’affinités/Réseau virtuel**: sélectionnez la région la plus proche de vos utilisateurs finaux.
   * **Compte de stockage**: utilisez un compte de stockage généré automatiquement.
   * **Groupe à haute disponibilité**: Aucun.
   * **POINTS DE TERMINAISON** Conservez les points de terminaison **Bureau à distance** et **PowerShell**, puis ajoutez un point de terminaison HTTP ou HTTPS, selon votre environnement.
     
     * **HTTP** : les ports public et privé par défaut ont le numéro **80**. Notez que si vous utilisez un port privé autre que 80, vous devez modifier **$HTTPport = 80** dans le script HTTP.
     * **HTTPS** : les ports public et privé par défaut sont **443**. Il est considéré comme meilleure pratique de sécurité de modifier le port privé et de configurer le pare-feu et le serveur de rapports pour qu’ils utilisent le port privé. Pour plus d’informations sur les points de terminaison, consultez la page [Comment configurer des points de terminaison sur une machine virtuelle](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Notez que si vous utilisez un port autre que 443, vous devez modifier le paramètre **$HTTPsport = 443** dans le script HTTPS.
   * Cliquez sur suivant. ![Suivant](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. Sur la dernière page de l’Assistant, conservez la valeur par défaut **Installer l’agent de machine virtuelle** sélectionnée. Les étapes décrites dans cette rubrique n’utilisent pas l’agent de machine virtuelle, mais si vous envisagez de conserver cette machine virtuelle, l’agent de machine virtuelle et les extensions vous permettront d’améliorer la machine virtuelle.  Pour plus d’informations, consultez le billet de blog [Agent de machine virtuelle et extensions – 1re partie](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). L’une des extensions par défaut installées et exécutées est l’extension « BGINFO », qui affiche sur le bureau de la machine virtuelle les informations système telles que l’adresse IP interne et l’espace libre sur le disque.
9. Cliquez Terminé . ![OK](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. L’**État** de la machine virtuelle affiché indique **Démarrage (Approvisionnement)** pendant le processus d’approvisionnement, puis **En cours d’exécution** une fois que la machine virtuelle a été approvisionnée et qu’elle est prête à l’emploi.

## <a name="step-2-create-a-server-certificate"></a>Étape 2 : Créer un certificat de serveur
> [!NOTE]
> Si vous n’avez pas besoin de HTTPS sur le serveur de rapports, vous pouvez **ignorer l’étape 2** et accéder à la section **Utiliser un script pour configurer le serveur de rapports et HTTP**. Utilisez le script HTTP pour configurer rapidement le serveur de rapports afin qu’il soit prêt à l’emploi.

Pour utiliser HTTPS sur la machine virtuelle, vous devez disposer d’un certificat SSL approuvé. Selon votre scénario, vous pouvez utiliser l’une des deux méthodes suivantes :

* Un certificat SSL valide émis par une autorité de certification et approuvé par Microsoft. Les certificats racines de l’autorité de certification doivent être distribués via le programme de certificat racine Microsoft. Pour plus d’informations sur ce programme, consultez les pages [Programme de certificat racine SSL Windows et Windows Phone 8 (autorités de certification membres)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) et [Introduction au programme de certificat racine Microsoft](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).
* Un certificat auto-signé. Les certificats auto-signés sont déconseillés pour les environnements de production.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Pour utiliser un certificat créé par une autorité de certification approuvée
1. **Demandez un certificat de serveur pour le site web auprès d’une autorité de certification**. 
   
    Vous pouvez utiliser l’Assistant Certificat de serveur Web pour générer un fichier de demande de certificat (Certreq.txt) à envoyer à une autorité de certification ou générer une demande destinée à une autorité de certification en ligne. Par exemple, les services de certificats Microsoft dans Windows Server 2012. Selon le niveau d’assurance offert par votre certificat de serveur en termes d’identification, l’autorité de certification peut prendre plusieurs jours à plusieurs mois pour approuver votre demande et vous envoyer un fichier de certificat. 
   
    Pour plus d’informations sur la demande d’un certificat de serveur, consultez les rubriques suivantes : 
   
   * Utiliser [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
   * Outils de sécurité pour administrer Windows Server 2012.
     
     [Outils de sécurité pour administrer Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > Le champ **Délivré à** du certificat SSL approuvé doit être le même que le **NOM DNS du service cloud** utilisé pour la nouvelle machine virtuelle.

2. **Installez le certificat de serveur sur le serveur web**. Dans ce cas, le serveur web est la machine virtuelle qui héberge le serveur de rapports, et le site web est créé ultérieurement lors de la configuration de Reporting Services. Pour plus d’informations sur l’installation du certificat de serveur sur le serveur web à l’aide du composant logiciel enfichable MMC Certificat, consultez la page [Installer un certificat de serveur](https://technet.microsoft.com/library/cc740068).
   
    Si vous souhaitez utiliser le script fourni dans cette rubrique pour configurer le serveur de rapports, la valeur **Empreinte numérique** des certificats est requise comme paramètre du script. Consultez la section suivante pour plus d’informations sur la façon d’obtenir l’empreinte numérique du certificat.
3. Attribuez le certificat de serveur au serveur de rapports. L’attribution s’effectue dans la section suivante, lors de la configuration du serveur de rapports.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Pour utiliser le certificat auto-signé Virtual Machines
Un certificat auto-signé a été créé sur la machine virtuelle lors de son approvisionnement. Le nom du certificat est le même que le nom DNS de la machine virtuelle. Pour éviter les erreurs de certificat, il est nécessaire que le certificat soit approuvé sur la machine virtuelle elle-même et aussi par tous les utilisateurs du site.

1. Pour approuver l’autorité de certification racine du certificat sur la machine virtuelle locale, ajoutez le certificat aux **Autorités de certification racines de confiance**. Les instructions suivantes sont un résumé de la procédure requise. Pour obtenir la procédure détaillée d’approbation de l’autorité de certification, consultez la page [Installer un certificat de serveur](https://technet.microsoft.com/library/cc740068).
   
   1. Dans le portail Azure, sélectionnez la machine virtuelle, puis cliquez sur Connecter. Selon la configuration de votre navigateur, vous pouvez être invité à enregistrer un fichier .rdp pour la connexion à la machine virtuelle.
      
       ![se connecter à une machine virtuelle azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilisez le nom de machine virtuelle, le nom d’utilisateur et le mot de passe que vous avez configurés lors de la création de la machine virtuelle. 
      
       Par exemple, dans l’illustration suivante, le nom de la machine virtuelle est **ssrsnativecloud** et le nom d’utilisateur **testuser**.
      
       ![le nom de connexion inclut le nom de la machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Exécutez mmc.exe. Pour plus d’informations, consultez la page [Affichage de certificats à l’aide du composant logiciel enfichable MMC](https://msdn.microsoft.com/library/ms788967.aspx).
   3. Dans le menu **Fichier** de l’application console, ajoutez le composant logiciel enfichable **Certificats**, sélectionnez **Compte d’ordinateur** lorsque vous y êtes invité, puis cliquez sur **Suivant**.
   4. Sélectionnez l’**Ordinateur local** à gérer, puis cliquez sur **Terminer**.
   5. Cliquez sur **OK**, développez les nœuds **Certificats - Personnel**, puis cliquez sur **Certificats**. Le certificat est nommé d’après le nom DNS de la machine virtuelle et se termine par **cloudapp.net**. Cliquez avec le bouton droit sur le nom du certificat, puis cliquez sur **Copier**.
   6. Développez le nœud **Autorités de certification racines de confiance**, puis cliquez avec le bouton droit sur **Certificats** et cliquez sur **Coller**.
   7. Pour valider, double-cliquez sur le nom du certificat sous **Autorités de certification racines de confiance** et vérifiez qu’il n’y a aucune erreur et que votre certificat est présent. Si vous souhaitez utiliser le script HTTPS fourni dans cette rubrique pour configurer le serveur de rapports, la valeur **Empreinte numérique** des certificats est requise comme paramètre du script. **Pour obtenir la valeur d’empreinte numérique**, procédez comme suit. La section [Utiliser un script pour configurer le serveur de rapports et HTTPS](#use-script-to-configure-the-report-server-and-HTTPS)comprend un exemple PowerShell permettant de récupérer l’empreinte numérique.
      
      1. Double-cliquez sur le nom du certificat, par exemple ssrsnativecloud.cloudapp.net.
      2. Cliquez sur l’onglet **Détails** .
      3. Cliquez sur **Empreinte numérique**. La valeur de l’empreinte numérique s’affiche dans le champ de détails, par exemple a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c fb 2f.
      4. Copiez l’empreinte numérique et enregistrez la valeur pour l’utiliser ultérieurement ou modifiez le script maintenant.
      5. (*) Avant d’exécuter le script, supprimez les espaces entre les paires de valeurs. Par exemple, l’empreinte numérique susmentionnée devient ‎a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
      6. Attribuez le certificat de serveur au serveur de rapports. L’attribution s’effectue dans la section suivante, lors de la configuration du serveur de rapports.

Si vous utilisez un certificat SSL auto-signé, le nom du certificat correspond déjà au nom d’hôte de la machine virtuelle. Par conséquent, le DNS de l’ordinateur est déjà enregistré globalement et accessible à partir de n’importe quel client.

## <a name="step-3-configure-the-report-server"></a>Étape 3 : Configurer le serveur de rapports
Cette section vous explique comment configurer la machine virtuelle comme serveur de rapports Reporting Services en mode natif. Vous pouvez utiliser une des méthodes suivantes pour configurer le serveur de rapports :

* Utiliser le script pour configurer le serveur de rapports
* Utiliser le Gestionnaire de configuration pour configurer le serveur de rapports

Pour obtenir la procédure détaillée, consultez la section [Se connecter à la machine virtuelle et démarrer le Gestionnaire de configuration de Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Remarque sur l’authentification :** l’authentification Windows est la méthode d’authentification recommandée et l’authentification de Reporting Services par défaut. Seuls les utilisateurs configurés sur la machine virtuelle peuvent accéder à Reporting Services et être affectés à  des rôles Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Utiliser un script pour configurer le serveur de rapports et HTTP
Pour utiliser le script Windows PowerShell afin de configurer le serveur de rapports, procédez comme suit. La configuration inclut HTTP, pas HTTPS :

1. Dans le portail Azure, sélectionnez la machine virtuelle, puis cliquez sur Connecter. Selon la configuration de votre navigateur, vous pouvez être invité à enregistrer un fichier .rdp pour la connexion à la machine virtuelle.
   
    ![se connecter à une machine virtuelle azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilisez le nom de machine virtuelle, le nom d’utilisateur et le mot de passe que vous avez configurés lors de la création de la machine virtuelle. 
   
    Par exemple, dans l’illustration suivante, le nom de la machine virtuelle est **ssrsnativecloud** et le nom d’utilisateur **testuser**.
   
    ![le nom de connexion inclut le nom de la machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Sur la machine virtuelle, ouvrez **Windows PowerShell ISE** avec des privilèges administratifs. L’environnement d’écriture de scripts intégré PowerShell est installé par défaut sur Windows Server 2012. Nous vous recommandons d’utiliser l’environnement d’écriture de scripts intégré plutôt qu’une fenêtre Windows PowerShell standard afin de pouvoir coller le script dans l’environnement d’écriture de scripts intégré, le modifier, puis l’exécuter.
3. Dans Windows PowerShell ISE, cliquez sur le menu **Affichage**, puis sur **Afficher le volet de script**.
4. Copiez le script suivant et collez-le dans le volet de script Windows PowerShell ISE.
   
        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
   
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
   
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Report Server Configuration Steps
   
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
   
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
   
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
5. Si vous avez créé la machine virtuelle avec un port HTTP autre que 80, modifiez le paramètre $HTTPport = 80.
6. Le script est actuellement configuré pour Reporting Services. Si vous souhaitez exécuter le script pour Reporting Services, remplacez la partie de la version du chemin d’accès à l’espace de noms « v11 » par l’instruction Get-WmiObject.
7. Exécutez le script.

**Validation**: pour vérifier que les fonctions de base du serveur de rapports fonctionnent, consultez la section [Vérifier la configuration](#verify-the-configuration) plus loin dans cette rubrique.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Utiliser un script pour configurer le serveur de rapports et HTTPS
Pour utiliser Windows PowerShell afin de configurer le serveur de rapports, procédez comme suit. La configuration inclut HTTPS, pas HTTP.

1. Dans le portail Azure, sélectionnez la machine virtuelle, puis cliquez sur Connecter. Selon la configuration de votre navigateur, vous pouvez être invité à enregistrer un fichier .rdp pour la connexion à la machine virtuelle.
   
    ![se connecter à une machine virtuelle azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilisez le nom de machine virtuelle, le nom d’utilisateur et le mot de passe que vous avez configurés lors de la création de la machine virtuelle. 
   
    Par exemple, dans l’illustration suivante, le nom de la machine virtuelle est **ssrsnativecloud** et le nom d’utilisateur **testuser**.
   
    ![le nom de connexion inclut le nom de la machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Sur la machine virtuelle, ouvrez **Windows PowerShell ISE** avec des privilèges administratifs. L’environnement d’écriture de scripts intégré PowerShell est installé par défaut sur Windows Server 2012. Nous vous recommandons d’utiliser l’environnement d’écriture de scripts intégré plutôt qu’une fenêtre Windows PowerShell standard afin de pouvoir coller le script dans l’environnement d’écriture de scripts intégré, le modifier, puis l’exécuter.
3. Pour activer les scripts en cours d’exécution, exécutez la commande Windows PowerShell suivante :
   
        Set-ExecutionPolicy RemoteSigned
   
    Vous pouvez ensuite exécuter la commande suivante pour vérifier la stratégie :
   
        Get-ExecutionPolicy
4. Dans **Windows PowerShell ISE**, cliquez sur le menu **Affichage**, puis sur **Afficher le volet de script**.
5. Copiez le script suivant et collez-le dans le volet de script Windows PowerShell ISE.
   
        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
   
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
   
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Reporting Services Report Server Configuration Steps
   
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
   
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
   
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## 3. Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
   
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
   
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
6. Modifiez le paramètre **$certificatehash** dans le script :
   
   * Il s’agit d’un paramètre **obligatoire** . Si vous n’avez pas enregistré la valeur de certificat de la procédure précédente, utilisez une des méthodes suivantes pour copier la valeur de hachage du certificat à partir de l’empreinte numérique de certificats :
     
       Sur la machine virtuelle, ouvrez Windows PowerShell ISE et exécutez la commande suivante :
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       Le résultat ressemble à ce qui suit : Si le script renvoie une ligne vide, par exemple que la machine virtuelle n’a pas de certificat configuré, consultez la section [Utiliser le certificat auto-signé Virtual Machines](#to-use-the-virtual-machines-self-signed-certificate).
     
     OU
   * Sur la machine virtuelle, exécutez mmc.exe, puis ajoutez le composant logiciel enfichable **Certificats** .
   * Sous le nœud **Autorités de certification racines de confiance** , double-cliquez sur le nom du certificat. Si vous utilisez le certificat auto-signé de la machine virtuelle, le certificat est nommé d’après le nom DNS de la machine virtuelle et se termine par **cloudapp.net**.
   * Cliquez sur l’onglet **Détails** .
   * Cliquez sur **Empreinte numérique**. La valeur de l’empreinte numérique s’affiche dans le champ de détails, par exemple af 11 60 b6 4b 28 8d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
   * **Avant d’exécuter le script**, supprimez les espaces entre les paires de valeurs. Par exemple af1160b64b288d890a8212ff6ba9c3664f319048
7. Modifiez le paramètre **$httpsport** : 
   
   * Si vous avez utilisé le port 443 pour le point de terminaison HTTPS, vous n’avez pas besoin de mettre à jour ce paramètre dans le script. Sinon, utilisez la valeur de port que vous avez sélectionnée lors de la configuration du point de terminaison privé HTTPS sur la machine virtuelle.
8. Modifiez le paramètre **$DNSName** : 
   
   * Le script est configuré pour un certificat à caractères génériques $DNSName = "+". Si vous ne souhaitez pas configurer pour une liaison de certificat à caractères génériques, placez $DNSName ="+" en commentaires et activez la ligne suivante, la référence complète $DNSNAme, ##$DNSName="$server.cloudapp.net".
     
       Modifiez la valeur $DNSName si vous ne souhaitez pas utiliser le nom DNS de la machine virtuelle pour Reporting Services. Si vous utilisez le paramètre, le certificat doit également utiliser ce nom, et vous inscrivez ce nom globalement sur un serveur DNS.
9. Le script est actuellement configuré pour Reporting Services. Si vous souhaitez exécuter le script pour Reporting Services, remplacez la partie de la version du chemin d’accès à l’espace de noms « v11 » par l’instruction Get-WmiObject.
10. Exécutez le script.

**Validation**: pour vérifier que les fonctions de base du serveur de rapports fonctionnent, consultez la section [Vérifier la configuration](#verify-the-connection) plus loin dans cette rubrique. Pour vérifier la liaison de certificat, ouvrez une invite de commandes avec des privilèges administratifs, puis exécutez la commande suivante :

    netsh http show sslcert

Le résultat comprend les éléments suivants :

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Utiliser le Gestionnaire de configuration pour configurer le serveur de rapports
Si vous ne souhaitez pas exécuter le script PowerShell pour configurer le serveur de rapports, suivez la procédure décrite dans cette section pour utiliser le Gestionnaire de configuration de Reporting Services en mode natif afin de configurer le serveur de rapports.

1. Dans le portail Azure, sélectionnez la machine virtuelle, puis cliquez sur Connecter. Utilisez le nom d’utilisateur et le mot de passe que vous avez configurés lors de la création de la machine virtuelle.
   
    ![se connecter à une machine virtuelle azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Exécutez Windows Update et installez les mises à jour de la machine virtuelle. Si un redémarrage de la machine virtuelle est nécessaire, redémarrez la machine virtuelle et reconnectez-vous à celle-ci à partir du portail Azure.
3. Dans le menu Démarrer de la machine virtuelle, saisissez **Reporting Services** et ouvrez le **Gestionnaire de configuration de Reporting Services**.
4. Conservez les valeurs par défaut pour **Nom du serveur** et **Instance du serveur de rapports**. Cliquez sur **Connecter**.
5. Dans le volet gauche, cliquez sur **URL du service Web**.
6. Par défaut, Reporting Services est configuré pour le port HTTP 80 avec l’adresse IP « entièrement affectée ». Pour ajouter HTTPS :
   
   1. Dans **Certificat SSL**: sélectionnez le certificat que vous souhaitez utiliser, par exemple [nom de la machine virtuelle].cloudapp.net. Si aucun certificat n’est répertorié, consultez la section **Étape 2 : Créer un certificat de serveur** pour obtenir les instructions d’installation et d’approbation du certificat sur la machine virtuelle.
   2. Sous **Port SSL**: sélectionnez 443. Si vous avez configuré le point de terminaison privé HTTPS dans la machine virtuelle avec un autre port privé, utilisez cette valeur ici.
   3. Cliquez sur **Appliquer** et attendez que l’opération soit terminée.
7. Dans le volet de gauche, cliquez sur **Base de données**.
   
   1. Cliquez sur **Changer la base de données**.
   2. Cliquez sur **Créer une nouvelle base de données de serveur de rapports**, puis sur **Suivant**.
   3. Laissez la valeur par défaut **Nom du serveur** : comme nom de la machine virtuelle et la valeur par défaut **Type d’authentification** sur **Utilisateur actuel** – **Sécurité intégrée**. Cliquez sur **Suivant**.
   4. Laissez la valeur par défaut **Nom de la base de données** sur **ReportServer** et cliquez sur **Suivant**.
   5. Laissez la valeur par défaut **Type d’authentification** sur **Informations d’identification du service** et cliquez sur **Suivant**.
   6. Cliquez sur **Suivant** on the **Suivant** .
   7. Une fois la configuration terminée, cliquez sur **Terminer**.
8. Dans le volet de gauche, cliquez sur **URL du Gestionnaire de rapports**. Laissez la valeur par défaut **Répertoire virtuel** sur **Rapports** et cliquez sur **Appliquer**.
9. Cliquez sur **Quitter** pour fermer le Gestionnaire de configuration de Reporting Services.

## <a name="step-4-open-windows-firewall-port"></a>Étape 4 : Ouvrir le port du pare-feu Windows
> [!NOTE]
> Si vous avez utilisé un des scripts pour configurer le serveur de rapports, vous pouvez ignorer cette section. Le script inclut une étape pour ouvrir le port du pare-feu. La valeur par défaut est le port 80 pour HTTP et le port 443 pour HTTPS.
> 
> 

Pour vous connecter à distance au Gestionnaire de rapports ou au serveur de rapports sur la machine virtuelle, vous devez disposer d’un point de terminaison TCP sur la machine virtuelle. Il est nécessaire d’ouvrir le même port dans le pare-feu de la machine virtuelle. Le point de terminaison a été créé lors de l’approvisionnement de la machine virtuelle.

Cette section fournit des informations de base sur l’ouverture du port du pare-feu. Pour plus d’informations, consultez la page [Configurer un pare-feu pour accéder au serveur de rapports](https://technet.microsoft.com/library/bb934283.aspx)

> [!NOTE]
> Si vous avez utilisé le script pour configurer le serveur de rapports, vous pouvez ignorer cette section. Le script inclut une étape pour ouvrir le port du pare-feu.
> 
> 

Si vous avez configuré un port privé pour HTTPS autre que 443, modifiez le script suivant en conséquence. Pour ouvrir le port **443** sur le pare-feu Windows, procédez comme suit :

1. Ouvrez une fenêtre Windows PowerShell avec des privilèges administratifs.
2. Si vous avez utilisé un port autre que 443 lors de la configuration du point de terminaison HTTPS sur la machine virtuelle, mettez à jour le port dans la commande suivante, puis exécutez la commande :
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. Lorsque la commande est terminée, **OK** s’affiche dans l’invite de commandes.

Pour vérifier que le port est bien ouvert, ouvrez une fenêtre Windows PowerShell et exécutez la commande suivante :

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Vérifier la configuration
Pour vérifier que les fonctions de base du serveur de rapports fonctionnent, ouvrez votre navigateur avec des privilèges administratifs, puis accédez aux URL de serveur de rapports et de Gestionnaire de rapports suivants :

* Sur la machine virtuelle, accédez à l’URL du serveur de rapports :
  
        http://localhost/reportserver
* Sur la machine virtuelle, accédez à l’URL du Gestionnaire de rapports :
  
        http://localhost/Reports
* À partir de votre ordinateur local, accédez au Gestionnaire de rapports **distant** sur la machine virtuelle. Mettez à jour le nom DNS dans l’exemple suivant, comme requis. Lorsque vous êtes invité à entrer un mot de passe, utilisez les informations d’identification d’administrateur que vous avez créées lors de l’approvisionnement de la machine virtuelle. Le nom d’utilisateur est au format [Domaine]\[nom utilisateur], où le domaine correspond au nom de la machine virtuelle, par exemple ssrsnativecloud\testuser. Si vous n’utilisez pas HTTP**S**, supprimez le **s** dans l’URL. Pour plus d’informations sur la création d’utilisateurs supplémentaires sur la machine virtuelle, consultez la section suivante.
  
        https://ssrsnativecloud.cloudapp.net/Reports
* À partir de votre ordinateur local, accédez à l’URL du serveur de rapports distant. Mettez à jour le nom DNS dans l’exemple suivant, comme requis. Si vous n’utilisez pas HTTPS, supprimez le « s » dans l’URL.
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Créer des utilisateurs et attribuer des rôles
Une fois le serveur de rapports configuré et vérifié, il est courant d’effectuer la tâche administrative consistant à créer un ou plusieurs utilisateurs et à affecter des utilisateurs aux rôles Reporting Services. Pour plus d’informations, consultez les liens suivants :

* [Créer un compte d’utilisateur local](https://technet.microsoft.com/library/cc770642.aspx)
* [Accorder à un utilisateur l’accès à un serveur de rapports (Gestionnaire de rapports)](https://msdn.microsoft.com/library/ms156034.aspx)
* [Créer et gérer des attributions de rôles](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Pour créer et publier des rapports sur la machine virtuelle Azure
Le tableau suivant résume certaines des options disponibles pour publier des rapports existants à partir d’un ordinateur local vers le serveur de rapports hébergé sur la machine virtuelle Microsoft Azure :

* **Script RS.exe**: utilisez le script RS.exe pour copier des éléments de rapport à partir du serveur de rapports existant vers la machine virtuelle Microsoft Azure. Pour plus d’informations, consultez la section « Mode natif vers mode natif – Machine virtuelle Microsoft Azure » dans [Exemple de script Reporting Services rs.exe pour migrer le contenu entre des serveurs de rapports](https://msdn.microsoft.com/library/dn531017.aspx).
* **Générateur de rapports**: la machine virtuelle inclut la version un clic du Générateur de rapports Microsoft SQL Server. Pour démarrer le Générateur de rapports la première fois sur la machine virtuelle:
  
  1. Démarrez votre navigateur avec des privilèges administratifs.
  2. Accédez au Gestionnaire de rapports sur la machine virtuelle et cliquez sur **Générateur de rapports** dans le ruban.
     
     Pour plus d’informations, consultez [Installation, désinstallation et prise en charge du Générateur de rapports](https://technet.microsoft.com/library/dd207038.aspx).
* **SQL Server Data Tools : machine virtuelle** : si vous avez créé la machine virtuelle à l’aide de SQL Server 2012, SQL Server Data Tools est installé sur la machine virtuelle et peut être utilisé pour créer des **Projets de serveur de rapports** et des rapports sur la machine virtuelle. SQL Server Data Tools peut publier les rapports vers le serveur de rapports sur la machine virtuelle.
  
    Si vous avez créé la machine virtuelle à l’aide de SQL Server 2014, vous pouvez installer SQL Server Data Tools - Business Intelligence pour Visual Studio. Pour plus d’informations, consultez les liens suivants :
  
  * [Microsoft SQL Server Data Tools - Business Intelligence pour Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)
  * [Microsoft SQL Server Data Tools - Business Intelligence pour Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)
  * [SQL Server Data Tools et SQL Server Business Intelligence (SSDT-BI)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)
* **SQL Server Data Tools : distant** : sur votre ordinateur local, créez un projet Reporting Services contenant des rapports Reporting Services dans SQL Server Data Tools. Configurez le projet pour la connexion à l’URL du service web.
  
    ![propriétés de projet ssdt pour un projet SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **Utiliser le script**: utilisez le script pour copier le contenu du serveur de rapports. Pour plus d’informations, consultez la page [Exemple de script Reporting Services rs.exe pour migrer le contenu entre des serveurs de rapports](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Réduire les coûts si vous n’utilisez pas la machine virtuelle
> [!NOTE]
> Pour réduire les frais associés à vos machines virtuelles Azure lorsque vous ne les utilisez pas, arrêtez la machine virtuelle à partir du portail Azure. Si vous utilisez les options d’alimentation Windows d’une machine virtuelle pour arrêter la machine virtuelle, vous êtes toujours facturé du même montant pour la machine virtuelle. Pour réduire les frais, vous devez arrêter la machine virtuelle dans le portail Azure. Si vous n’avez plus besoin de la machine virtuelle, n’oubliez pas de la supprimer, ainsi que les fichiers .vhd associés, pour éviter des frais de stockage. Pour plus d’informations, consultez la FAQ à la page [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>Informations complémentaires
### <a name="resources"></a>Ressources
* Pour obtenir un contenu similaire relatif au déploiement sur un seul serveur de SQL Server Business Intelligence et SharePoint 2013, consultez la page [Utiliser Windows PowerShell pour créer une machine virtuelle Azure avec SQL Server BI et SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx).
* Pour obtenir un contenu similaire relatif au déploiement sur plusieurs serveurs de SQL Server Business Intelligence et SharePoint 2013, consultez la page [Déployer SQL Server Business Intelligence dans Azure Virtual Machines](https://msdn.microsoft.com/library/dn321998.aspx).
* Pour des informations d’ordre général sur les déploiements de SQL Server Business Intelligence dans Azure Virtual Machines, consultez la page [SQL Server Business Intelligence dans Azure Virtual Machines](virtual-machines-windows-classic-ps-sql-bi.md).
* Pour plus d’informations sur le montant des frais de calcul Azure, consultez l’onglet Virtual Machines de la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Contenu de la communauté
* Pour obtenir des instructions étape par étape sur la création d’un serveur de rapports Reporting Services en mode natif sans utiliser de script, consultez la page [Hébergement de SQL Reporting Services sur une machine virtuelle Azure](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Liens vers d’autres ressources pour SQL Server dans les machines virtuelles Azure
[Vue d’ensemble de SQL Server sur les machines virtuelles Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

