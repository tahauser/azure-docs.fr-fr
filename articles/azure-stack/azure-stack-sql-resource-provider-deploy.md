---
title: "Utilisation de bases de données SQL sur Azure Stack | Microsoft Docs"
description: "Découvrez comment déployer des bases de données SQL en tant que service sur Azure Stack et les étapes rapides à suivre pour déployer l’adaptateur de fournisseur de ressources SQL Server."
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: JeffGo
ms.openlocfilehash: bf52ed4986b4e0930b57721c0e38bbf748045a36
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
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

Vous devez créer une (ou plusieurs) instances de SQL Server et/ou fournir un accès aux instances SQL Server externes.

## <a name="deploy-the-resource-provider"></a>Déployer le fournisseur de ressources

1. Si ce n’est déjà fait, inscrivez votre Kit de développement et téléchargez l’image Windows Server 2016 Datacenter Core à partir de Gestion dans la Place de marché. Vous devez utiliser une image Windows Server 2016 Core. Vous pouvez également utiliser un script pour créer une [image de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Veillez à sélectionner l’option principale). Le runtime .NET 3.5 n’est plus nécessaire.

2. Connectez-vous à un hôte qui peut accéder à la machine virtuelle de point de terminaison privilégié.

    - Sur les installations du kit de développement Azure Stack, connectez-vous à l’hôte physique.

    - Sur les systèmes à plusieurs nœuds, l’hôte doit être un système qui peut accéder au point de terminaison privilégié.
    
    >[!NOTE]
    > Le système où le script est en cours d’exécution *doit* être un système Windows 10 ou Windows Server 2016 avec la dernière version du runtime .NET installé. Sinon l’installation échouera. Le kit de développement logiciel Azure Stack répond à ce critère.


3. Téléchargez l’adaptateur du fournisseur de ressources SQL binaire. Exécutez ensuite l’auto-extracteur pour extraire le contenu dans un répertoire temporaire.

    >[!NOTE] 
    > La build du fournisseur de ressources correspond aux builds Azure Stack. Vérifiez que vous téléchargez le binaire correct pour la version d’Azure Stack en cours d’exécution.

    | Build Azure Stack | Programme d’installation du fournisseur de ressources SQL |
    | --- | --- |
    |1.0.180102.3, 1.0.180103.2 ou 1.0.180106.1 (nœuds multiples) | [SQL RP version 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1.0.171122.1 | [SQL RP version 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1.0.171028.1 | [SQL RP version 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. Le certificat racine Azure Stack est récupéré à partir du point de terminaison privilégié. Pour le kit de développement logiciel (SDK) Azure Stack, un certificat auto-signé est créé dans le cadre de ce processus. Pour plusieurs nœuds, vous devez fournir un certificat approprié.

   Pour fournir votre propre certificat, placez un fichier .pfx dans **DependencyFilesLocalPath** comme suit :

    - Soit un certificat générique pour \*.dbadapter.\<region\>.\<external fqdn\>, soit un certificat de site unique avec un nom commun de sqladapter.dbadapter.\<region\>.\<external fqdn\>.

    - Ce certificat doit être fiable. Autrement dit, la chaîne d’approbation doit exister sans exiger de certificats intermédiaires.

    - Il n’existe qu’un seul fichier de certificat unique dans le chemin local DependencyFilesLocalPath.

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

> [!NOTE]
> Si l’installation prend plus de 90 minutes, elle risque d’échouer. Dans ce cas, un message d’erreur s’affiche à l’écran ainsi que dans le fichier journal, mais une nouvelle tentative de déploiement est effectuée à partir de l’étape ayant échoué. Les systèmes qui ne répondent pas aux spécifications recommandées en matière de mémoire et de processeur virtuel risquent de ne pas pouvoir déployer le fournisseur de ressources SQL.
>

Voici un exemple que vous pouvez exécuter à partir de l’invite de PowerShell. (Veillez à modifier les informations de compte et les mots de passe si nécessaire.)

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
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
Chaque fois que la version d’Azure Stack est mise à jour, un nouvel adaptateur du fournisseur de ressources SQL est publié. Il est possible que l’adaptateur existant continue à fonctionner. Toutefois, nous recommandons la mise à jour vers la dernière version dès que possible après la mise à jour d’Azure Stack. 

Le processus de mise à jour est similaire au processus d’installation décrit précédemment. Vous créez une machine virtuelle avec le dernier code de fournisseur de ressources. En outre, vous migrez les paramètres vers cette nouvelle instance, y compris les informations sur la base de données et sur le serveur d’hébergement. Vous migrez également l’enregistrement DNS nécessaire.

Utilisez le script UpdateSQLProvider.ps1 script avec les mêmes arguments que ceux décrits précédemment. Vous devez fournir le certificat ici également.

> [!NOTE]
> Ce processus de mise à jour est uniquement prise en charge sur les systèmes à plusieurs nœuds.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

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


## <a name="next-steps"></a>étapes suivantes

[Ajoutez des serveurs d’hébergement](azure-stack-sql-resource-provider-hosting-servers.md) et [créez des bases de données](azure-stack-sql-resource-provider-databases.md).

Essayez d’autres [services PaaS](azure-stack-tools-paas-services.md) comme le [fournisseur de ressources MySQL Server](azure-stack-mysql-resource-provider-deploy.md) et le [fournisseur de ressources App Services](azure-stack-app-service-overview.md).
