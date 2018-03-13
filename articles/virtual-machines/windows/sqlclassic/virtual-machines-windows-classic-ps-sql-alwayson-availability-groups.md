---
title: "Configurer le groupe de disponibilité Always On sur une machine virtuelle Azure avec PowerShell | Microsoft Docs"
description: "Ce didacticiel utilise les ressources qui ont été créées avec le modèle de déploiement classique. Vous allez utiliser PowerShell pour créer un groupe de disponibilité Always On dans Azure."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: 
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: fe7384baa6740d316fb5a8ec7b1894f337d88080
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Configurer le groupe de disponibilité Always On sur une machine virtuelle Azure avec PowerShell
> [!div class="op_single_selector"]
> * [Classic : interface utilisateur](../classic/portal-sql-alwayson-availability-groups.md)
> * [Classic : PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Avant de commencer, rappelez-vous que vous pouvez accomplir cette tâche dans le modèle Azure Resource Manager. Nous vous recommandons d’utiliser le modèle Azure Resource Manager pour les nouveaux déploiements. Consultez [Présentation des groupes de disponibilité SQL Server AlwaysOn sur des machines virtuelles Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Pour la plupart des nouveaux déploiements, nous recommandons d’utiliser le modèle Resource Manager. Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique.

Les machines virtuelles Azure permettent aux administrateurs de base de données de réduire le coût d’un système SQL Server haute disponibilité. Ce didacticiel vous indique comment implémenter un groupe de disponibilité en utilisant SQL Server Always On de bout en bout dans un environnement Azure. À la fin du didacticiel, votre solution SQL Server Always On dans Azure comprendra les éléments suivants :

* un réseau virtuel contenant plusieurs sous-réseaux, notamment un sous-réseau frontal et un sous-réseau principal ;
* un contrôleur de domaine avec un domaine Active Directory ;
* deux machines virtuelles SQL Server déployées dans le sous-réseau principal et jointes au domaine Active Directory ;
* un cluster de basculement Windows à trois nœuds avec le modèle de quorum Nœud majoritaire ;
* un groupe de disponibilité avec deux réplicas avec validation synchrone d’une base de données de disponibilité.

Ce scénario est choisi pour sa simplicité, pas pour sa rentabilité ou pour d’autres avantages apportés par Azure. Par exemple, vous pouvez réduire le nombre de machines virtuelles pour un groupe de disponibilité de deux réplicas, afin de faire des économies sur les heures de calcul dans Azure, en utilisant le contrôleur de domaine comme témoin de partage de fichiers du quorum dans un cluster de basculement à deux nœuds. Cette méthode permet de se passer d’une machine virtuelle par rapport à la configuration ci-dessus.

Le but de ce didacticiel est de vous présenter les étapes nécessaires pour installer la solution décrite ci-dessus sans développer les détails de chaque étape. Par conséquent, au lieu de donner les étapes de configuration dans l’interface utilisateur graphique, il utilise le script PowerShell pour vous guider rapidement dans chaque étape. Ce didacticiel part des principes suivants :

* Vous avez déjà un compte Azure avec un abonnement de machine virtuelle.
* Vous avez installé les [cmdlets Azure PowerShell](/powershell/azure/overview).
* Vous disposez déjà d’une connaissance approfondie des groupes de disponibilité Always On pour les solutions locales. Pour plus d’informations, voir [Groupes de disponibilité AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Connexion à votre abonnement Azure et création du réseau virtuel
1. Dans une fenêtre PowerShell sur votre ordinateur local, importez le module Azure, téléchargez un fichier de paramètres de publication sur votre ordinateur, et connectez votre session PowerShell à votre abonnement Azure en important les paramètres de publication téléchargés.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    La commande **Get-AzurePublishSettingsFile** génère automatiquement un certificat de gestion avec Azure et le télécharge sur votre ordinateur. Une fenêtre de navigateur s’ouvre automatiquement et vous êtes invité à saisir les informations d’identification du compte Microsoft de votre abonnement Azure. Le fichier **.publishsettings** téléchargé contient toutes les informations dont vous avez besoin pour gérer votre abonnement Azure. Après avoir enregistré ce fichier dans un répertoire local, importez-le à l’aide de la commande **Import-AzurePublishSettingsFile**.

   > [!NOTE]
   > Le fichier .publishsettings contient les informations d'identification (non codées) servant à gérer vos abonnements et services Azure. Pour des raisons de sécurité, il est recommandé de stocker ce fichier temporairement en dehors de vos répertoires sources (par exemple, dans le dossier Bibliothèques\Documents), puis de le supprimer une fois l’importation terminée. Si un utilisateur malveillant accède au fichier .publishsettings, il peut modifier, créer et supprimer vos services Azure.

2. Définissez une série de variables que vous allez utiliser pour créer votre infrastructure de cloud computing.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Prêtez attention à ce qui suit pour vous assurer que vos commandes fonctionneront plus tard :

   * Les variables **$storageAccountName** et **$dcServiceName** doivent être uniques, car elles sont utilisées pour identifier, respectivement, votre compte de stockage et votre serveur dans le cloud sur Internet.
   * Les noms que vous spécifiez pour les variables **$affinityGroupName** et **$virtualNetworkName** sont indiqués dans le document de configuration du réseau virtuel que vous allez utiliser ultérieurement.
   * **$sqlImageName** spécifie le nom mis à jour de l’image de la machine virtuelle qui contient SQL Server 2012 Service Pack 1 Enterprise Edition.
   * Pour plus de simplicité, **Contoso!000** est le mot de passe qui sera utilisé pendant tout le didacticiel.

3. Créez un groupe d’affinités.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Créez un réseau virtuel en important un fichier de configuration.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Le fichier de configuration contient le document XML suivant. En bref, il spécifie un réseau virtuel nommé **ContosoNET** dans le groupe d’affinités nommé **ContosoAG**. Il possède l’espace d’adressage **10.10.0.0/16** et deux sous-réseaux, **10.10.1.0/24** et **10.10.2.0/24**, qui correspondent respectivement au sous-réseau frontal et au sous-réseau principal. Le sous-réseau frontal est l’endroit où vous pouvez placer des applications clientes telles que Microsoft SharePoint. Le sous-réseau principal est l’endroit où vous allez placer les machines virtuelles SQL Server. Si vous avez modifié les variables **$affinityGroupName** et **$virtualNetworkName** précédemment, vous devez également modifier les noms correspondants ci-dessous.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. Créez un compte de stockage associé au groupe d’affinités que vous avez créé, et définissez-le comme compte de stockage actif dans votre abonnement.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Créez le serveur du contrôleur de domaine dans le nouveau service cloud et dans le groupe à haute disponibilité.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Ces commandes redirigées effectuent les opérations suivantes :

   * **New-AzureVMConfig** crée une configuration de machine virtuelle.
   * **Add-AzureProvisioningConfig** fournit les paramètres de configuration d’un serveur Windows autonome.
   * **Add-AzureDataDisk** ajoute le disque de données que vous allez utiliser pour stocker les données d’Active Directory, avec l’option de mise en cache définie sur None.
   * **New-AzureVM** crée un service cloud et crée la machine virtuelle Azure dans le nouveau service cloud.

7. Attendez que la nouvelle machine virtuelle soit entièrement configurée, puis téléchargez le fichier Bureau à distance dans votre répertoire de travail. Étant donné que la configuration d’une nouvelle machine virtuelle Azure prend beaucoup de temps, la boucle `while` continue d’interroger le nouvelle machine virtuelle jusqu’à ce qu’elle soit prête à être utilisée.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

Le serveur du contrôleur de domaine est maintenant correctement configuré. Ensuite, vous allez configurer le domaine Active Directory sur ce serveur du contrôleur de domaine. Laissez la fenêtre PowerShell ouverte sur votre ordinateur local. Vous la réutiliserez ultérieurement pour créer les deux machines virtuelles SQL Server.

## <a name="configure-the-domain-controller"></a>Configurer le contrôleur de domaine
1. Connectez-vous au serveur du contrôleur de domaine en lançant le fichier Bureau à distance. Utilisez le nom d’utilisateur de l’administrateur AzureAdmin et le mot de passe **Contoso!000** que vous avez spécifiés lors de la création de la machine virtuelle.
2. Ouvrez une fenêtre PowerShell en mode administrateur.
3. Exécutez la commande **DCPROMO.EXE** suivante pour installer le domaine **corp.contoso.com**, avec les répertoires de données sur le lecteur M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Une fois l’exécution de la commande terminée, la machine virtuelle redémarre automatiquement.

4. Reconnectez-vous au serveur du contrôleur de domaine en lançant le fichier Bureau à distance. Cette fois, connectez-vous en tant que **CORP\Administrator**.
5. Ouvrez une fenêtre PowerShell en mode Administrateur et importez le module PowerShell d’Active Directory à l’aide de la commande suivante :

        Import-Module ActiveDirectory

6. Exécutez les commandes suivantes pour ajouter trois utilisateurs au domaine.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** est utilisé pour configurer tout ce qui est associé aux instances de service SQL Server, au cluster de basculement et au groupe de disponibilité. **CORP\SQLSvc1** et **CORP\SQLSvc2** sont utilisés comme comptes de service SQL Server pour les deux machines virtuelles SQL Server.
7. Ensuite, exécutez les commandes suivantes afin d’accorder à **CORP\Install** les autorisations pour créer des objets ordinateur dans le domaine.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Le GUID spécifié ci-dessus est celui du type d’objet ordinateur. Le compte **CORP\Install** nécessite les autorisations **Lire toutes les propriétés** et  **Créer des objets d’ordinateur** pour créer les objets Active Directory du cluster de basculement. L’autorisation **Lire toutes les propriétés** étant déjà accordée à CORP\Install par défaut, vous ne devez donc pas le faire explicitement. Pour plus d’informations sur les autorisations nécessaires pour créer le cluster de basculement, consultez [Failover Cluster Step-by-Step Guide: Configuring Accounts in Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx) (Guide pas à pas du cluster de basculement : configuration de comptes dans Active Directory).

    Maintenant que vous avez fini de configurer Active Directory et les objets utilisateur, vous allez créer deux machines virtuelles SQL Server et les joindre à ce domaine.

## <a name="create-the-sql-server-vms"></a>Création des machines virtuelles SQL Server
1. Continuez à utiliser la fenêtre PowerShell qui est ouverte sur votre ordinateur local. Définissez les variables supplémentaires suivantes :

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    L’adresse IP **10.10.0.4** est généralement affectée à la première machine virtuelle que vous créez dans le sous-réseau **10.10.0.0/16** de votre réseau virtuel Azure. Vous devez vérifier qu’il s’agit bien de l’adresse de votre serveur du contrôleur de domaine en exécutant la commande **IPCONFIG**.
2. Exécutez les commandes redirigées suivantes pour créer la première machine virtuelle dans le cluster de basculement, nommé **ContosoQuorum** :

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Notez bien ce qui suit concernant la commande ci-dessus:

   * **New-AzureVMConfig** crée une configuration de machine virtuelle avec le nom de groupe à haute disponibilité souhaité. Les machines virtuelles suivantes seront créées avec le même nom de groupe à haute disponibilité afin d’être jointes au même groupe à haute disponibilité.
   * **Add-AzureProvisioningConfig** joint la machine virtuelle au domaine Active Directory que vous avez créé.
   * **Set-AzureSubnet** place la machine virtuelle dans le sous-réseau principal.
   * **New-AzureVM** crée un service cloud et crée la machine virtuelle Azure dans le nouveau service cloud. Le paramètre **DnsSettings** spécifie que le serveur DNS des serveurs du nouveau service cloud a l’adresse IP **10.10.0.4**. , qui est celle du serveur du contrôleur de domaine. Ce paramètre est nécessaire pour permettre aux nouvelles machines virtuelles du service cloud de rejoindre correctement le domaine Active Directory. Sans ce paramètre, vous devez définir manuellement les paramètres IPv4 dans votre machine virtuelle pour utiliser le serveur du contrôleur de domaine comme serveur DNS principal après la configuration de la machine virtuelle, puis pour la joindre au domaine Active Directory.
3. Exécutez les commandes redirigées suivantes pour créer les machines virtuelles SQL Server, nommées **ContosoSQL1** et **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Notez bien ce qui suit concernant les commandes ci-dessus :

   * **New-AzureVMConfig** utilise le même nom de groupe à haute disponibilité que le serveur du contrôleur de domaine, et utilise l’image de SQL Server 2012 Service Pack 1 Enterprise Edition dans la galerie de machines virtuelles. Elle définit également le disque du système d’exploitation pour la mise en cache de lecture uniquement (pas pour la mise en cache d’écriture). Nous vous recommandons de migrer les fichiers de base de données sur un disque de données distinct que vous allez joindre à la machine virtuelle et que vous allez configurer sans aucune mise en cache de lecture ou d’écriture. Toutefois, il est conseillé ensuite de supprimer la mise en cache d’écriture sur le disque du système d’exploitation, car vous ne pouvez pas supprimer la mise en cache de lecture sur le disque du système d’exploitation.
   * **Add-AzureProvisioningConfig** joint la machine virtuelle au domaine Active Directory que vous avez créé.
   * **Set-AzureSubnet** place la machine virtuelle dans le sous-réseau principal.
   * **Add-AzureEndpoint** ajoute des points de terminaison d’accès de sorte que les applications clientes puissent accéder à ces instances de service SQL Server sur Internet. Des ports différents sont fournis pour ContosoSQL1 et ContosoSQL2.
   * **New-AzureVM** crée la machine virtuelle SQL Server dans le même service cloud en tant que ContosoQuorum. Vous devez placer les machines virtuelles dans le même service cloud si vous souhaitez qu’elles se trouvent dans le même groupe à haute disponibilité.
4. Attendez que chaque machine virtuelle soit entièrement configurée, puis téléchargez le fichier Bureau à distance de chaque machine virtuelle dans votre répertoire de travail. La boucle `for` effectue des cycles sur les trois nouvelles machines virtuelles et exécute les commandes à l’intérieur des accolades supérieures pour chacune d’entre elles.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    Les machines virtuelles SQL Server sont maintenant configurées et exécutées, mais elles sont installées avec les options par défaut de SQL Server.

## <a name="initialize-the-failover-cluster-vms"></a>Initialisation des machines virtuelles du cluster de basculement
Dans cette section, vous devez modifier les trois serveurs que vous allez utiliser dans le cluster de basculement et l’installation de SQL Server. Plus précisément :

* Tous les serveurs : vous devez installer la fonctionnalité **Clustering avec basculement**.
* Tous les serveurs : vous devez ajouter **CORP\Install** en tant **qu’administrateur** de l’ordinateur.
* ContosoSQL1 et ContosoSQL2 uniquement : vous devez ajouter **CORP\Install** en tant que rôle **sysadmin** dans la base de données par défaut.
* ContosoSQL1 et ContosoSQL2 uniquement : vous devez ajouter **NT AUTHORITY\System** comme nom de connexion, avec les autorisations suivantes :

  * Modifier un groupe de disponibilité
  * Connecter SQL
  * Afficher l’état du serveur
* ContosoSQL1 et ContosoSQL2 uniquement : le protocole **TCP** est déjà activé sur la machine virtuelle SQL Server. Toutefois, vous devez encore ouvrir le pare-feu pour l’accès à distance de SQL Server.

Maintenant, vous êtes prêt à démarrer. En commençant par **ContosoQuorum**, suivez les étapes ci-dessous :

1. Connectez-vous à **ContosoQuorum** en lançant les fichiers Bureau à distance. Utilisez le nom d’utilisateur de l’administrateur **AzureAdmin** et le mot de passe **Contoso!000** que vous avez spécifiés lors de la création des machines virtuelles.
2. Vérifiez que les ordinateurs ont été correctement joints à **corp.contoso.com**.
3. Attendez que l’installation SQL Server termine d’exécuter les tâches automatisées d’initialisation avant de continuer.
4. Ouvrez une fenêtre PowerShell en mode administrateur.
5. Installez la fonctionnalité Windows de Clustering de basculement.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Ajoutez **CORP\Install** en tant qu’administrateur local.

        net localgroup administrators "CORP\Install" /Add
7. Déconnectez-vous de ContosoQuorum. Vous avez terminé avec ce serveur.

        logoff.exe

Ensuite, initialisez **ContosoSQL1** et **ContosoSQL2**. Suivez les étapes ci-dessous, identiques pour les deux machines virtuelles SQL Server.

1. Connectez-vous aux deux machines virtuelles SQL Server en lançant les fichiers Bureau à distance. Utilisez le nom d’utilisateur de l’administrateur **AzureAdmin** et le mot de passe **Contoso!000** que vous avez spécifiés lors de la création des machines virtuelles.
2. Vérifiez que les ordinateurs ont été correctement joints à **corp.contoso.com**.
3. Attendez que l’installation SQL Server termine d’exécuter les tâches automatisées d’initialisation avant de continuer.
4. Ouvrez une fenêtre PowerShell en mode administrateur.
5. Installez la fonctionnalité Windows de Clustering de basculement.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Ajoutez **CORP\Install** en tant qu’administrateur local.

        net localgroup administrators "CORP\Install" /Add
7. Importez le fournisseur PowerShell SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Ajoutez **CORP\Install** en tant que rôle sysadmin pour l’instance SQL Server par défaut.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Ajoutez **NT AUTHORITY\System** comme nom de connexion avec les trois autorisations décrites ci-dessus.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Ouvrez le pare-feu pour l’accès à distance de SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Déconnectez-vous des deux machines virtuelles.

         logoff.exe

Vous pouvez maintenant configurer le groupe de disponibilité. Vous utiliserez le fournisseur PowerShell SQL Server pour effectuer tout le travail sur **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Configuration du groupe de disponibilité
1. Reconnectez-vous à **ContosoSQL1** en lançant les fichiers Bureau à distance. Au lieu de vous connecter en utilisant le compte de l’ordinateur, connectez-vous à l’aide de **CORP\Install**.
2. Ouvrez une fenêtre PowerShell en mode administrateur.
3. Définissez les variables suivantes :

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. Importez le fournisseur PowerShell SQL Server.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Modifiez le compte de service SQL Server pour ContosoSQL1 en CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Modifiez le compte de service SQL Server pour ContosoSQL2 en CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Téléchargez **CreateAzureFailoverCluster.ps1** depuis la page [Create Failover Cluster for Always On Availability Groups in Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) (Créer le cluster de basculement pour les groupes de disponibilité Always On dans une machine virtuelle Azure) dans le répertoire de travail local. Vous utiliserez ce script pour vous aider à créer un cluster de basculement fonctionnel. Pour obtenir des informations importantes sur la façon dont le clustering de basculement Windows interagit avec le réseau Azure, consultez [Haute disponibilité et récupération d’urgence pour SQL Server dans les machines virtuelles Azure](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Passez à votre répertoire de travail et créez le cluster de basculement avec le script téléchargé.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Activez les groupes de disponibilité Always On pour les instances SQL Server par défaut sur **ContosoSQL1** et **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. Créez un répertoire de sauvegarde et accordez des autorisations aux comptes de service SQL Server. Vous utiliserez ce répertoire pour préparer la base de données de disponibilité sur le réplica secondaire.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Créez une base de données sur **ContosoSQL1** nommée **MyDB1**, effectuez une sauvegarde complète et une sauvegarde de fichier journal, puis restaurez-les sur **ContosoSQL2** avec l’option **WITH NORECOVERY**.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Créez les points de terminaison du groupe de disponibilité sur les machines virtuelles SQL Server et définissez les autorisations appropriées sur les points de terminaison.

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. Créez les réplicas de disponibilité.

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. Enfin, créez le groupe de disponibilité et joignez le réplica secondaire au groupe de disponibilité.

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>Étapes suivantes
Vous avez correctement implémenté SQL Server Always On en créant un groupe de disponibilité dans Azure. Pour configurer un écouteur pour ce groupe de disponibilité, consultez [Configurer un écouteur à équilibrage de charge interne pour des groupes de disponibilité Always On dans Azure](../classic/ps-sql-int-listener.md).

Pour en savoir plus sur l’utilisation de SQL Server dans Azure, consultez [Présentation de SQL Server sur les machines virtuelles Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
