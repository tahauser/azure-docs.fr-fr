---
title: Utiliser des bases de données MySQL en tant que PaaS sur Azure Stack | Microsoft Docs
description: Découvrez comment déployer le fournisseur de ressources MySQL et fournir des bases de données MySQL en tant que service sur Azure Stack.
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
ms.openlocfilehash: 15a1648193555ecc5847170ab65f48dfa4f6417b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Utiliser des bases de données MySQL sur Microsoft Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez déployer un fournisseur de ressources MySQL sur Azure Stack. Après avoir déployé le fournisseur de ressources, vous pouvez créer des serveurs et des bases de données MySQL avec des modèles de déploiement Azure Resource Manager. Vous pouvez également fournir des bases de données MySQL en tant que service. 

Les bases de données MySQL, qui sont courantes sur les sites web, prennent en charge de nombreuses plateformes de site web. Par exemple, après avoir déployé le fournisseur de ressources, vous pouvez créer des sites web WordPress à partir du module complémentaire PaaS (Platform as a Service) Web Apps pour Azure Stack.

Pour déployer le fournisseur MySQL sur un système qui n’a pas accès à Internet, copiez le fichier [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) sur un partage local. Ensuite, indiquez ce nom de partage lorsque vous y êtes invité. Vous devez installer les modules PowerShell Azure et Azure Stack.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>Architecture de l’adaptateur de fournisseur de ressources MySQL Server

Le fournisseur de ressources est constitué de trois composants :

- **La machine virtuelle d’adaptateur de fournisseur de ressources MySQL**, qui est une machine virtuelle Windows exécutant les services de fournisseur.

- **Le fournisseur de ressources proprement dit**, qui traite les demandes d’approvisionnement et expose les ressources de base de données.

- **Les serveurs qui hébergent MySQL Server**, qui fournissent de la capacité pour les bases de données, appelés serveurs d’hébergement.

Cette version ne crée plus d’instances de MySQL. Cela signifie que vous devez créer vous-même et/ou fournir l’accès aux instances de SQL externes. Visitez la [Galerie de démarrage rapide Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) à la recherche d’un exemple de modèle qui peut :
- Créez un serveur MySQL pour vous.
- Téléchargez et déployez un serveur MySQL à partir de la Place de marché Azure.

> [!NOTE]
> Les serveurs d’hébergement installés sur des systèmes intégrés Azure Stack doivent être créés à partir d’un abonnement de locataire. Ils ne peuvent pas être créés à partir de l’abonnement du fournisseur par défaut. Ils doivent être créés à partir du portail du locataire ou à partir d’une session PowerShell avec un nom de connexion approprié. Tous les serveurs d’hébergement sont des machines virtuelles facturables et doivent disposer des licences appropriées. L’administrateur de service peut être le propriétaire de l’abonnement locataire.

### <a name="required-privileges"></a>Privilèges requis
Le compte système doit disposer des privilèges suivants :

1.  Base de données : créer, supprimer
2.  Connexion : créer, définir, supprimer, accorder, révoquer

## <a name="deploy-the-resource-provider"></a>Déployer le fournisseur de ressources

1. Si ce n’est déjà fait, inscrivez votre Kit de développement et téléchargez l’image Windows Server 2016 Datacenter Core à partir de Gestion dans la Place de marché. Vous devez utiliser une image Windows Server 2016 Core. Vous pouvez également utiliser un script pour créer une [image de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Veillez à sélectionner l’option principale.)


2. Connectez-vous à un hôte qui peut accéder à la machine virtuelle de point de terminaison privilégié.

    - Sur les installations du Kit SDK Azure, connectez-vous à l’hôte physique. 
    - Sur les systèmes à plusieurs nœuds, l’hôte doit être un système qui peut accéder au point de terminaison privilégié.
    
    >[!NOTE]
    > Le système sur lequel le script est en cours d’exécution *doit* être un système Windows 10 ou Windows Server 2016 avec la dernière version du runtime .NET installé. Sinon, l’installation échoue. Le kit de développement logiciel Azure Stack répond à ce critère.
    

3. Téléchargez l’adaptateur du fournisseur de ressources MySQL binaire. Exécutez ensuite l’auto-extracteur pour extraire le contenu dans un répertoire temporaire.

    >[!NOTE] 
    > Le fournisseur de ressources possède une build Azure Stack minimale correspondante. Vérifiez que vous téléchargez le binaire correct pour la version d’Azure Stack en cours d’exécution.

    | Build Azure Stack | Programme d’installation de MySQL RP |
    | --- | --- |
    | i1802: 1.0.180302.1 | [MySQL RP version 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | 1712: 1.0.180102.3 ou 1.0.180106.1 (nœuds multiples) | [MySQL RP version 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |
    | 1711: 1.0.171122.1 | [MySQL RP version 1.1.12.0](https://aka.ms/azurestackmysqlrp1711) |
    | 1710: 1.0.171028.1 | [MySQL RP version 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |

4.  Le certificat racine Azure Stack est récupéré à partir du point de terminaison privilégié. Pour le Kit SDK Azure, un certificat auto-signé est créé dans le cadre de ce processus. Pour plusieurs nœuds, vous devez fournir un certificat approprié.

    Si vous devez fournir votre propre certificat, placez un fichier .pfx dans le chemin **DependencyFilesLocalPath**, qui répond aux critères suivants :

    - Soit un certificat générique pour \*.dbadapter.\<region\>.\<external fqdn\> ou un certificat de site unique avec un nom commun de mysqladapter.dbadapter.\<region\>.\<external fqdn\>.

    - Ce certificat doit être fiable. Autrement dit, la chaîne d’approbation doit exister sans exiger de certificats intermédiaires.

    - Il n’existe qu’un seul fichier de certificat unique dans le chemin local DependencyFilesLocalPath.
    
    - Le nom de fichier ne doit pas contenir de caractères spéciaux ou d’espaces.


5. Ouvrez une **nouvelle** console PowerShell avec élévation de privilèges (administratif). Accédez ensuite au répertoire où vous avez extrait les fichiers. Utilisez une nouvelle fenêtre pour éviter les problèmes qui pourraient se produire à cause des modules PowerShell incorrects déjà chargés sur le système.

6. [Installer Azure PowerShell version 1.2.11](azure-stack-powershell-install.md).

7. Exécutez le script `DeployMySqlProvider.ps1`.

Le script effectue les étapes suivantes :

* Téléchargement du fichier binaire de connecteur MySQL (peut être fourni en mode hors connexion).
* Chargement des certificats et des autres artefacts dans un compte de stockage sur Azure Stack.
* Publication des packages de la galerie afin que vous puissiez déployer des bases de données SQL par le biais de la galerie.
* Publication d’un package de galerie pour déployer des serveurs d’hébergement.
* Déploiement d’une machine virtuelle à l’aide de l’image de Windows Server 2016 créée à l’étape 1. Installation également du fournisseur de ressources.
* Inscription d’un enregistrement DNS local mappé à la machine virtuelle de votre fournisseur de ressources.
* Inscription de votre fournisseur de ressources auprès de l’Azure Resource Manager local (locataire et administrateur).


Vous pouvez :
- Spécifiez les paramètres requis sur la ligne de commande.
- Procédez à une exécution sans paramètres, puis entrez ceux-ci lorsque vous y êtes invité.

Voici un exemple que vous pouvez exécuter à partir de l’invite de PowerShell. Veillez à modifier les informations de compte et les mots de passe si nécessaire :


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
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files.
# Find the ERCS01 IP address first, and make sure the certificate
# file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploymysqlproviderps1-parameters"></a>Paramètres de DeployMySqlProvider.ps1
Vous pouvez spécifier ces paramètres dans la ligne de commande. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres obligatoires.

| Nom du paramètre | Description | Commentaire ou valeur par défaut |
| --- | --- | --- |
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ |
| **AzCredential** | Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack. | _Obligatoire_ |
| **VMLocalCredential** | Les informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources MySQL. | _Obligatoire_ |
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ |
| **DependencyFilesLocalPath** | Chemin vers un partage local contenant [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Si vous fournissez un de ces chemins, le fichier de certificat doit également être placé dans ce répertoire. | _Facultatif_ (_obligatoire_ pour les nœuds multiples) |
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _Obligatoire_ |
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 |
| **RetryDuration** | Délai d’attente entre les tentatives, en secondes. | 120 |
| **Désinstaller** | Supprime le fournisseur de ressources et toutes les ressources associées (voir les remarques ci-dessous). | Non  |
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non  |
| **AcceptLicense** | Ignore l’invite à accepter la licence GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Vérifier le déploiement à l’aide du portail Azure Stack

> [!NOTE]
>  Une fois que l’exécution du script d’installation est terminée, vous devez actualiser le portail pour afficher le panneau d’administration.


1. Connectez-vous au portail d’administration en tant qu’administrateur de service.

2. Vérifiez que le déploiement a réussi. Accédez à **Groupes de ressources**, puis sélectionnez le groupe de ressources **system.\<location\>.mysqladapter**. Vérifiez que les quatre déploiements ont réussi.

      ![Vérifier le déploiement du fournisseur de ressources MySQL](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Fournir de la capacité en se connectant à un serveur d’hébergement MySQL

1. Connectez-vous au portail Azure Stack en tant qu’administrateur de service.

2. Sélectionnez **RESSOURCES ADMINISTRATIVES**  >  **Serveurs d’hébergement MySQL**  >  **+Ajouter**.

    Dans le panneau **Serveurs d’hébergement MySQL**, vous pouvez connecter le fournisseur de ressources MySQL Server à des instances réelles de MySQL Server qui font office de backend du fournisseur de ressources.

    ![Serveurs d’hébergement](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Spécifiez les détails de connexion de votre instance de MySQL Server. Veillez à fournir le nom de domaine complet (FQDN) ou une adresse IPv4 valide, et pas le nom court de machine virtuelle. Cette installation ne fournit plus d’instance de MySQL par défaut. La taille fournie aide le fournisseur de ressources à gérer la capacité de la base de données. Elle doit être proche de la capacité physique du serveur de base de données.

    > [!NOTE]
    >Si l’instance MySQL est accessible par le locataire et l’administrateur Azure Resource Manager, elle peut être placée sous contrôle du fournisseur de ressources. L’instance de MySQL *doit* être allouée exclusivement au fournisseur de ressources.

4. À mesure que vous ajoutez des serveurs, vous devez les affecter à une référence (SKU) nouvelle ou existante pour pouvoir différencier les offres de service.
  Par exemple, vous pouvez avoir une instance d’entreprise proposant :
    - Une capacité de base de données
    - La sauvegarde automatique
    - La réservation de serveurs hautes performances pour des services individuels
 

Le nom de la référence SKU doit refléter les propriétés afin que les locataires puissent placer leurs bases de données de manière appropriée. Tous les serveurs d’hébergement d’une référence SKU doivent avoir les mêmes capacités.

![Créer une référence (SKU) MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> Une heure entière peut être nécessaire avant que les références n’apparaissent dans le portail. Vous ne pouvez pas créer une base de données tant que la référence n’a pas été créée.


## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>Créer sa première base de données MySQL pour tester son déploiement


1. Connectez-vous au portail Azure Stack en tant qu’administrateur de service.

2. Sélectionnez **+ Nouveau** > **Données + stockage** > **Base de données MySQL**.

3. Spécifiez les détails de la base de données.

    ![Créer une base de données MySQL test](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Sélectionnez une référence (SKU).

    ![Sélectionner une référence](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Créez un paramètre de connexion. Vous pouvez réutiliser un paramètre de connexion ou en créer un. Ce paramètre contient le nom d’utilisateur et le mot de passe de la base de données.

    ![Créer une connexion de base de données](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    La chaîne de connexion inclut le nom réel du serveur de base de données. Copiez-le à partir du portail.

    ![Obtenir la chaîne de connexion pour la base de données MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> La longueur des noms d’utilisateurs ne doit pas dépasser 32 caractères dans MySQL 5.7. Dans les éditions antérieures, il ne devait pas dépasser 16 caractères.


## <a name="add-capacity"></a>Ajouter de la capacité

Augmentez la capacité en ajoutant des serveurs MySQL dans le portail Azure Stack. Des serveurs supplémentaires peuvent être ajoutés à une nouvelle référence SKU ou à une référence existante. Assurez-vous que les caractéristiques du serveur sont identiques.


## <a name="make-mysql-databases-available-to-tenants"></a>Mettre des bases de données MySQL à la disposition des locataires
Créez des plans et des offres pour mettre les bases de données MySQL à disposition des locataires. Par exemple, ajoutez le service Microsoft.MySqlAdapter, ajoutez un quota, et ainsi de suite.

![Créer des plans et des offres pour inclure des bases de données](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>Mettre à jour le mot de passe d’administration
Vous pouvez modifier le mot de passe en commençant par le modifier sur l’instance du serveur MySQL. Sélectionnez **RESSOURCES ADMINISTRATIVES** > **Serveurs d’hébergement MySQL**. Puis sélectionnez le serveur d’hébergement. Dans le volet **Paramètres**, sélectionnez **Mot de passe**.

![Mettre à jour le mot de passe de l’administrateur](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Mettre à jour l’adaptateur du fournisseur de ressources MySQL (à plusieurs nœuds uniquement, versions 1710 et ultérieures)
Un nouvel adaptateur de fournisseur de ressources SQL peut être publié quand des builds Azure Stack sont mises à jour. Même si l’adaptateur existant continue de fonctionner, nous vous recommandons d’effectuer une mise à jour dès que possible vers la build la plus récente. 

Pour mettre à jour le fournisseur de ressources, vous utilisez le script *UpdateMySQLProvider.ps1*. Le processus est semblable au processus utilisé pour installer un fournisseur de ressources, comme décrit dans la section [Déployer le fournisseur de ressources](#deploy-the-resource-provider) de cet article. Le script est inclus avec le téléchargement du fournisseur de ressources.

Le script *UpdateMySQLProvider.ps1* crée une machine virtuelle avec le dernier code de fournisseur de ressources et migre les paramètres de l’ancienne machine virtuelle vers la nouvelle. Les paramètres qui migrent incluent des informations sur la base de données et le serveur d’hébergement, ainsi que l’enregistrement DNS nécessaire.

Le script exige d’utiliser les mêmes arguments que ceux décrits pour le script DeployMySqlProvider.ps1. Fournissez également le certificat ici. 

Voici un exemple du script *UpdateMySQLProvider.ps1* que vous pouvez exécuter à partir de l’invite PowerShell. Veillez à modifier les informations de compte et les mots de passe si nécessaire : 

> [!NOTE]
> Le processus de mise à jour s’applique uniquement aux systèmes intégrés.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>Paramètres de UpdateMySQLProvider.ps1
Vous pouvez spécifier ces paramètres dans la ligne de commande. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres obligatoires.

| Nom du paramètre | Description | Commentaire ou valeur par défaut |
| --- | --- | --- |
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ |
| **AzCredential** | Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack. | _Obligatoire_ |
| **VMLocalCredential** |Informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources SQL. | _Obligatoire_ |
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ |
| **DependencyFilesLocalPath** | Votre fichier de certificat .pfx doit également être placé dans ce répertoire. | _Facultatif_ (_obligatoire_ pour les nœuds multiples) |
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _Obligatoire_ |
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 |
| **RetryDuration** | Délai d’attente entre les tentatives, en secondes. | 120 |
| **Désinstaller** | Supprimez le fournisseur de ressources et toutes les ressources associées (voir les remarques ci-dessous). | Non  |
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non  |
| **AcceptLicense** | Ignore l’invite à accepter la licence GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="collect-diagnostic-logs"></a>Collecter des journaux de diagnostic
Le fournisseur de ressources MySQL est une machine virtuelle verrouillée. S’il s’avère nécessaire de collecter des journaux à partir de la machine virtuelle, un point de terminaison PowerShell JEA (Just Enough Administration) _DBAdapterDiagnostics_ est fourni à cette fin. Deux commandes sont disponibles via ce point de terminaison :

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
Le fournisseur de ressources MySQL est une machine virtuelle verrouillée. La mise à jour de la sécurité de la machine virtuelle du fournisseur de ressources peut être effectuée par le biais du point de terminaison JEA (Just Enough Administration) PowerShell _DBAdapterMaintenance_.

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

2. Créer une session PowerShell sur le point de terminaison de maintenance de la machine virtuelle de l’adaptateur de fournisseur de ressources MySQL
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


## <a name="remove-the-mysql-resource-provider-adapter"></a>Supprimer l’adaptateur de fournisseur de ressources MySQL

Pour supprimer le fournisseur de ressources, il est essentiel de supprimer d’abord toutes les dépendances.

1. Vérifiez que vous avez le package de déploiement d’origine que vous avez téléchargé pour cette version du fournisseur de ressources.

2. Toutes les bases de données de locataires doivent être supprimées du fournisseur de ressources. (La suppression des bases de données de locataires ne supprime pas les données.) Cette tâche doit être effectuée par les locataires eux-mêmes.

3. Les locataires doivent annuler l’inscription à partir de l’espace de noms.

4. L’administrateur doit supprimer les serveurs d’hébergement de l’adaptateur MySQL.

5. L’administrateur doit supprimer tous les plans qui référencent l’adaptateur MySQL.

6. L’administrateur doit supprimer tous les quotas associés à l’adaptateur MySQL.

7. Réexécutez le script de déploiement avec les éléments suivants :
    - Le paramètre -Désinstaller
    - Les points de terminaison Azure Resource Manager
    - Le DirectoryTenantID
    - Les informations d’identification du compte d’administrateur local du service
