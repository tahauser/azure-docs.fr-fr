---
title: Utilisation de bases de données SQL sur Azure Stack | Microsoft Docs
description: Découvrez comment déployer des bases de données SQL en tant que service sur Azure Stack et les étapes rapides à suivre pour déployer l’adaptateur de fournisseur de ressources SQL Server.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: 4d2a00f04e5b07aeb3585fb3ab6c8966e0de7e19
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Utiliser des bases de données SQL sur Microsoft Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Utiliser l’adaptateur de fournisseur de ressources SQL Server pour exposer des bases de données SQL en tant que service d’[Azure Stack](azure-stack-poc.md). Une fois le fournisseur de ressources installé et connecté à une ou plusieurs instances de SQL Server, vous et vos utilisateurs pouvez créer :
- des bases de données pour les applications cloud natives ;
- des sites web basés sur SQL ;
- des charges de travail basées sur SQL.
Vous n’êtes pas obligé d’approvisionner à chaque fois une machine virtuelle qui héberge SQL Server.

Le fournisseur de ressources ne prend pas en charge toutes les fonctionnalités de gestion de base de données d’[Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Par exemple, les pools de bases de données élastiques et la possibilité de ralentir ou d’augmenter automatiquement les performances de la base de données ne sont pas disponibles. Toutefois, le fournisseur de ressources prend en charge des opérations CRUD (créer, lire, mettre à jour et supprimer) similaires. L’API n’est pas compatible avec SQL Database.

## <a name="sql-resource-provider-adapter-architecture"></a>Architecture de l’adaptateur de fournisseur de ressources SQL
Le fournisseur de ressources est constitué de trois composants :

- **La machine virtuelle de l’adaptateur de fournisseur de ressources SQL**, qui est une machine virtuelle Windows exécutant les services de fournisseur.
- **Le fournisseur de ressources proprement dit**, qui traite les demandes d’approvisionnement et expose les ressources de base de données.
- **Les serveurs qui hébergent SQL Server**, qui offrent de la capacité pour les bases de données, et qui sont appelés serveurs d’hébergement.

Vous devez créer une (ou plusieurs) instances de SQL Server et/ou fournir un accès aux instances SQL Server externes.

> [!NOTE]
> Les serveurs d’hébergement installés sur des systèmes intégrés Azure Stack doivent être créés à partir d’un abonnement de locataire. Ils ne peuvent pas être créés à partir de l’abonnement du fournisseur par défaut. Ils doivent être créés à partir du portail du locataire ou à partir d’une session PowerShell avec un nom de connexion approprié. Tous les serveurs d’hébergement sont des machines virtuelles facturables et doivent disposer des licences appropriées. L’administrateur de service peut être le propriétaire de l’abonnement locataire.

## <a name="deploy-the-resource-provider"></a>Déployer le fournisseur de ressources

1. Si ce n’est déjà fait, inscrivez votre Kit de développement et téléchargez l’image Windows Server 2016 Datacenter Core à partir de Gestion dans la Place de marché. Vous devez utiliser une image Windows Server 2016 Core. Vous pouvez également utiliser un script pour créer une [image de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Veillez à sélectionner l’option principale.)

2. Connectez-vous à un hôte qui peut accéder à la machine virtuelle de point de terminaison privilégié.

    - Sur les installations du kit de développement Azure Stack, connectez-vous à l’hôte physique.

    - Sur les systèmes à plusieurs nœuds, l’hôte doit être un système qui peut accéder au point de terminaison privilégié.
    
    >[!NOTE]
    > Le système où le script est en cours d’exécution *doit* être un système Windows 10 ou Windows Server 2016 avec la dernière version du runtime .NET installé. Sinon, l’installation échoue. Le kit de développement logiciel Azure Stack répond à ce critère.


3. Téléchargez l’adaptateur du fournisseur de ressources SQL binaire. Exécutez ensuite l’auto-extracteur pour extraire le contenu dans un répertoire temporaire.

    >[!NOTE] 
    > Le fournisseur de ressources possède une build Azure Stack minimale correspondante. Vérifiez que vous téléchargez le binaire correct pour la version d’Azure Stack en cours d’exécution.

    | Build Azure Stack | Programme d’installation du fournisseur de ressources SQL |
    | --- | --- |
    | 1802: 1.0.180302.1 | [SQL RP version 1.1.18.0](https://aka.ms/azurestacksqlrp1802) |
    | 1712: 1.0.180102.3, 1.0.180103.2 ou 1.0.180106.1 (nœuds multiples) | [SQL RP version 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1711: 1.0.171122.1 | [SQL RP version 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1710: 1.0.171028.1 | [SQL RP version 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. Le certificat racine Azure Stack est récupéré à partir du point de terminaison privilégié. Pour le kit de développement logiciel (SDK) Azure Stack, un certificat auto-signé est créé dans le cadre de ce processus. Pour les systèmes intégrés, vous devez fournir un certificat approprié.

   Pour fournir votre propre certificat, placez un fichier .pfx dans **DependencyFilesLocalPath** comme suit :

    - Soit un certificat générique pour \*.dbadapter.\<region\>.\<external fqdn\>, soit un certificat de site unique avec un nom commun de sqladapter.dbadapter.\<region\>.\<external fqdn\>.

    - Ce certificat doit être fiable. Autrement dit, la chaîne d’approbation doit exister sans exiger de certificats intermédiaires.

    - Il ne peut exister qu’un seul fichier de certificat dans le répertoire vers lequel pointe le paramètre DependencyFilesLocalPath.

    - Le nom de fichier ne doit pas contenir de caractères spéciaux.


5. Ouvrez une **nouvelle** console (administrative) PowerShell avec élévation de privilèges et basculez vers le répertoire où vous avez extrait les fichiers. Utilisez une nouvelle fenêtre pour éviter les problèmes qui pourraient se produire à cause des modules PowerShell incorrects déjà chargés sur le système.

6. [Installer Azure PowerShell version 1.2.11](azure-stack-powershell-install.md).

7. Exécutez le script DeploySqlProvider.ps1, qui effectue les étapes suivantes :

    - Chargement des certificats et des autres artefacts dans un compte de stockage sur Azure Stack.
    - Publication des packages de la galerie afin que vous puissiez déployer des bases de données SQL par le biais de la galerie.
    - Publication d’un package de galerie pour déployer des serveurs d’hébergement.
    - Déploiement d’une machine virtuelle à l’aide de l’image de Windows Server 2016 créée à l’étape 1, puis installation du fournisseur de ressources.
    - Inscription d’un enregistrement DNS local mappé à la machine virtuelle de votre fournisseur de ressources.
    - Inscription de votre fournisseur de ressources auprès de l’Azure Resource Manager local (utilisateur et administrateur).
    - Installation éventuelle d’une seule mise à jour Windows pendant l’installation du fournisseur de ressources

8. Nous vous conseillons de télécharger la dernière image Windows Server 2016 Core à partir de la Gestion de la Place de marché. Si vous devez installer une mise à jour, vous pouvez placer un seul package .MSU dans le chemin de dépendance local. Si plusieurs fichiers .MSU sont trouvés, le script échoue.


Voici un exemple que vous pouvez exécuter à partir de l’invite de PowerShell. (Veillez à modifier les informations de compte et les mots de passe si nécessaire.)

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>Paramètres de DeploySqlProvider.ps1
Vous pouvez spécifier ces paramètres dans la ligne de commande. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres obligatoires.

| Nom du paramètre | Description | Commentaire ou valeur par défaut |
| --- | --- | --- |
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ |
| **AzCredential** | Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack. | _Obligatoire_ |
| **VMLocalCredential** | Informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources SQL. | _Obligatoire_ |
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ |
| **DependencyFilesLocalPath** | Votre fichier de certificat .pfx doit également être placé dans ce répertoire. | _Facultatif_ (_obligatoire_ pour les nœuds multiples) |
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _Obligatoire_ |
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 |
| **RetryDuration** | Délai d’attente entre les tentatives, en secondes. | 120 |
| **Désinstaller** | Supprime le fournisseur de ressources et toutes les ressources associées (voir les remarques ci-dessous). | Non  |
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non  |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Vérifier le déploiement à l’aide du portail Azure Stack

> [!NOTE]
>  Une fois que l’exécution du script d’installation est terminée, vous devez actualiser le portail pour afficher le panneau d’administration.


1. Connectez-vous au portail d’administration en tant qu’administrateur de service.

2. Vérifiez que le déploiement a réussi. Accédez aux **Groupes de ressources**. Puis sélectionnez le groupe de ressources **system.\< location\>.sqladapter**. Vérifiez que les quatre déploiements ont réussi.

      ![Vérifiez le déploiement du fournisseur de ressources SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Mettre à jour l’adaptateur du fournisseur de ressources SQL (à plusieurs nœuds uniquement, versions 1710 et ultérieures)
Un nouvel adaptateur de fournisseur de ressources SQL peut être publié quand des builds Azure Stack sont mises à jour. Même si l’adaptateur existant continue de fonctionner, nous vous recommandons d’effectuer une mise à jour dès que possible vers la build la plus récente. Les mises à jour doivent être installées dans l’ordre : vous ne pouvez pas ignorer des versions (voir le tableau à l’étape 3 de la section [Déployer le fournisseur de ressources](#deploy-the-resource-provider)).

Pour mettre à jour le fournisseur de ressources, vous utilisez le script *UpdateSQLProvider.ps1*. Le processus est semblable au processus utilisé pour installer un fournisseur de ressources, comme décrit dans la section [Déployer le fournisseur de ressources](#deploy-the-resource-provider) de cet article. Le script est inclus avec le téléchargement du fournisseur de ressources.

Le script *UpdateSQLProvider.ps1* crée une machine virtuelle avec le dernier code de fournisseur de ressources et migre les paramètres de l’ancienne machine virtuelle vers la nouvelle. Les paramètres qui migrent incluent des informations sur la base de données et le serveur d’hébergement, ainsi que l’enregistrement DNS nécessaire.

Le script exige d’utiliser les mêmes arguments que ceux décrits pour le script DeploySqlProvider.ps1. Fournissez également le certificat ici. 

Nous vous conseillons de télécharger la dernière image Windows Server 2016 Core à partir de la Gestion de la Place de marché. Si vous devez installer une mise à jour, vous pouvez placer un seul package .MSU dans le chemin de dépendance local. Si plusieurs fichiers .MSU sont trouvés, le script échoue.

Voici un exemple du script *UpdateSQLProvider.ps1* que vous pouvez exécuter à partir de l’invite PowerShell. Veillez à modifier les informations de compte et les mots de passe si nécessaire : 

> [!NOTE]
> Le processus de mise à jour s’applique uniquement aux systèmes intégrés.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 parameters
Vous pouvez spécifier ces paramètres dans la ligne de commande. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres obligatoires.

| Nom du paramètre | Description | Commentaire ou valeur par défaut |
| --- | --- | --- |
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ |
| **AzCredential** | Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack. | _Obligatoire_ |
| **VMLocalCredential** | Informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources SQL. | _Obligatoire_ |
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ |
| **DependencyFilesLocalPath** | Votre fichier de certificat .pfx doit également être placé dans ce répertoire. | _Facultatif_ (_obligatoire_ pour les nœuds multiples) |
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _obligatoire_ |
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 |
| **RetryDuration** |Délai d’attente entre les tentatives, en secondes. | 120 |
| **Désinstaller** | Supprime le fournisseur de ressources et toutes les ressources associées (voir les remarques ci-dessous). | Non  |
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non  |


## <a name="collect-diagnostic-logs"></a>Collecter des journaux de diagnostic
Le fournisseur de ressources SQL est une machine virtuelle verrouillée. S’il s’avère nécessaire de collecter des journaux à partir de la machine virtuelle, un point de terminaison PowerShell JEA (Just Enough Administration) _DBAdapterDiagnostics_ est fourni à cette fin. Deux commandes sont disponibles via ce point de terminaison :

* Get-AzsDBAdapterLog : prépare un package zip contenant des journaux de diagnostic de fournisseur de ressources et le place sur le lecteur de l’utilisateur de session. La commande peut être appelée sans paramètres. Elle collecte alors les journaux des quatre dernières heures.
* Remove-AzsDBAdapterLog : nettoie les packages de journaux existants sur la machine virtuelle du fournisseur de ressources.

Un compte d’utilisateur appelé _dbadapterdiag_ est créé pendant le déploiement ou la mise à jour d’un fournisseur de ressources pour se connecter au point de terminaison de diagnostics et extraire les journaux de fournisseur de ressources. Le mot de passe de ce compte est le même que celui fourni pour le compte d’administrateur local pendant le déploiement/la mise à jour.

Pour utiliser ces commandes, vous devez créer une session PowerShell à distance sur la machine virtuelle du fournisseur de ressources, puis appeler la commande. Vous pouvez éventuellement fournir les paramètres FromDate et ToDate. Si vous ne spécifiez pas l’un de ces paramètres, ou les deux, la valeur FromDate est quatre heures avant l’heure actuelle et la valeur ToDate est l’heure actuelle.

Cet exemple de script illustre l’utilisation de ces commandes :

```
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="maintenance-operations-integrated-systems"></a>Opérations de maintenance (systèmes intégrés)
Le fournisseur de ressources SQL est une machine virtuelle verrouillée. La mise à jour de la sécurité de la machine virtuelle du fournisseur de ressources peut être effectuée par le biais du point de terminaison JEA (Just Enough Administration) PowerShell _DBAdapterMaintenance_.

Pour faciliter ces opérations, un script est fourni avec le package d’installation du fournisseur de ressources.

### <a name="update-the-virtual-machine-operating-system"></a>Mettre à jour le système d’exploitation de la machine virtuelle
Il existe plusieurs façons de mettre à jour la machine virtuelle Windows Server :
* Installer le package de fournisseur de ressources le plus récent à l’aide d’une image corrigée de Windows Server 2016 Core
* Installer un package Windows Update pendant l’installation ou la mise à jour du fournisseur de ressources


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>Mettre à jour les définitions Windows Defender de machine virtuelle

Suivez les étapes ci-après pour mettre à jour les définitions Defender :

1. Télécharger la mise à jour des définitions Windows Defender à partir de [Définitions Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)

    Dans cette page, sous « Télécharger et installer les définitions manuellement », télécharger le fichier 64 bits « Antivirus Windows Defender pour Windows 10 et Windows 8.1 » 
    
    Lien direct : https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. Créer une session PowerShell sur le point de terminaison de maintenance de la machine virtuelle de l’adaptateur de fournisseur de ressources SQL
3. Copier le fichier de mise à jour des définitions pour la machine de l’adaptateur de base de données à l’aide de la session du point de terminaison de maintenance
4. Sur la session PowerShell de maintenance, appeler la commande _Update-DBAdapterWindowsDefenderDefinitions_
5. Après l’installation, il est recommandé de supprimer le fichier de mise à jour des définitions utilisé. Vous pouvez le supprimer dans la session de maintenance à l’aide de la commande _Remove-ItemOnUserDrive)_.


Voici un exemple de script pour mettre à jour les définitions Defender (remplacer l’adresse ou le nom de la machine virtuelle par la valeur réelle) :

```
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="remove-the-sql-resource-provider-adapter"></a>Supprimer l’adaptateur du fournisseur de ressources SQL

Pour supprimer le fournisseur de ressources, il est essentiel de supprimer d’abord toutes les dépendances.

1. Vérifiez que vous disposez du package de déploiement d’origine que vous avez téléchargé pour cette version de l’adaptateur du fournisseur de ressources SQL.

2. Toutes les bases de données utilisateur doivent être supprimées du fournisseur de ressources. (La suppression des bases de données utilisateur ne supprime pas les données.) Cette tâche doit être effectuée par les utilisateurs eux-mêmes.

3. L’administrateur doit supprimer les serveurs d’hébergement de l’adaptateur du fournisseur de ressources SQL.

4. L’administrateur doit supprimer tous les plans qui référencent l’adaptateur du fournisseur de ressources SQL.

5. L’administrateur doit supprimer toutes les références SKU et tous les quotas associés à l’adaptateur du fournisseur de ressources SQL.

6. Réexécutez le script de déploiement avec les éléments suivants :
    - Le paramètre -Désinstaller
    - Les points de terminaison Azure Resource Manager
    - Le DirectoryTenantID
    - Les informations d’identification du compte d’administrateur local du service


## <a name="next-steps"></a>Étapes suivantes

[Ajoutez des serveurs d’hébergement](azure-stack-sql-resource-provider-hosting-servers.md) et [créez des bases de données](azure-stack-sql-resource-provider-databases.md).

Essayez d’autres [services PaaS](azure-stack-tools-paas-services.md) comme le [fournisseur de ressources MySQL Server](azure-stack-mysql-resource-provider-deploy.md) et le [fournisseur de ressources App Services](azure-stack-app-service-overview.md).
