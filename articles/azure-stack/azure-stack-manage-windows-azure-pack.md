---
title: "Gérer des machines virtuelles Windows Azure Pack à partir d’Azure Stack | Microsoft Docs"
description: "Découvrez comment gérer des machines virtuelles Windows Azure Pack à partir du portail utilisateur dans Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 213c2792-d404-4b44-8340-235adf3f8f0b
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: a7e4896c84938b392a86f4d9609c4932324c785d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="manage-windows-azure-pack-virtual-machines-from-azure-stack"></a>Gérer des machines virtuelles Windows Azure Pack à partir d’Azure Stack

*S’applique au Kit de développement Azure Stack*

Dans le Kit de développement Azure Stack, vous pouvez activer l’accès aux machines virtuelles du locataire s’exécutant sur Windows Azure Pack à partir du portail utilisateur Azure Stack. Les utilisateurs peuvent utiliser le portail Azure Stack pour gérer leurs réseaux virtuels et machines virtuelles IaaS existants. Ces ressources sont disponibles sur Windows Azure Pack dans les composants SPF (Service Provider Foundation) et VMM (Virtual Machine Manager) sous-jacents. Plus précisément, les utilisateurs peuvent :

* Parcourir les ressources
* Examiner les valeurs de configuration
* Démarrer ou arrêter une machine virtuelle
* Se connecter à une machine virtuelle avec le protocole RDP (Remote Desktop Protocol)
* Créer et gérer des points de contrôle
* Supprimer des réseaux virtuels et des machines virtuelles

Cette fonctionnalité est fournie par le connecteur Windows Azure Pack pour Azure Stack (préversion). Cet article explique comment configurer le connecteur pour un environnement Azure Stack à nœud unique.

Pour cette préversion du connecteur, tenez compte des éléments suivants :
* Utilisez le connecteur Windows Azure Pack uniquement dans des environnements de test (pour Azure Stack et Windows Azure Pack) et non dans des déploiements de production.
* Vous devez avoir Windows Azure Pack Update Rollup (UR) 9.1 ou ultérieur et System Center SPF et VMM UR 9 ou ultérieur.
* Les composants VMM et SPF peuvent être System Center 2012 R2 ou System Center 2016.
* Vous devez effectuer les étapes de configuration à la fois sur Azure Stack et sur Windows Azure Pack.
* Les instructions s’appliquent aux environnements non-CPS (Cloud Platform System).
* Pour passer en revue les problèmes connus, consultez [Dépannage de Microsoft Azure Stack](azure-stack-troubleshooting.md).


## <a name="architecture"></a>Architecture
Le diagramme suivant montre les principaux composants du connecteur Windows Azure Pack.

![Composants du connecteur Windows Azure Pack](media/azure-stack-manage-wap/image1.png)

Notez les informations suivantes :
* Le portail utilisateur Azure Stack accède aux informations de ressources à partir des deux clouds (Azure Stack et Windows Azure Pack).
* L’utilisateur doit disposer d’un compte valide dans les deux environnements.
* Le portail utilisateur Azure Stack doit avoir un accès réseau aux composants qui s’exécutent sur Windows Azure Pack.
* Dans la section **WAP** du diagramme, vous pouvez voir les nouveaux modules de connecteur Windows Azure Pack (connecteur et extension WAP) et l’API locataire Windows Azure Pack avec les composants SPF et VMM existants.


## <a name="identity-management"></a>Gestion des identités
L’API locataire Windows Azure Pack doit approuver le service d’émission de jeton de sécurité (STS) Azure Stack.

Quand un utilisateur se sert du portail Azure Stack pour effectuer une action qui cible des ressources Windows Azure Pack, le portail utilise l’API locataire Windows Azure Pack. C’est pourquoi le jeton d’authentification utilisateur fourni doit provenir d’un service STS approuvé. Consultez le diagramme suivant :

![Diagramme d’authentification du connecteur Windows Azure Pack](media/azure-stack-manage-wap/image2.png)

Dans l’environnement du Kit de développement, Windows Azure Pack et Azure Stack ont des fournisseurs d’identité indépendants. Les utilisateurs qui accèdent à ces deux environnements à partir du portail Azure Stack doivent avoir le même nom d’utilisateur principal (UPN) dans les deux fournisseurs d’identité. Par exemple, le compte *azurestackadmin@azurestack.local* doit également exister dans le service STS pour Windows Azure Pack. Là où les services AD FS ne sont pas configurés pour prendre en charge les relations d’approbation sortante, vous allez établir une approbation des composants Windows Azure Pack (API locataire) sur l’instance Azure Stack des services AD FS.

## <a name="prerequisites"></a>Prérequis

### <a name="download-the-windows-azure-pack-connector"></a>Télécharger le connecteur Windows Azure Pack
Dans le [Centre de téléchargement Microsoft](https://aka.ms/wapconnectorazurestackdlc), téléchargez le fichier .exe et extrayez-le sur votre ordinateur local. Ensuite, copiez le contenu sur un ordinateur pouvant accéder à votre environnement Windows Azure Pack.

### <a name="deployment-option-requirement"></a>Options de déploiement requises
Pour l’intégration à Windows Azure Pack, vous pouvez déployer Azure Stack à l’aide de l’option AD FS ou Azure Active Directory.

### <a name="connectivity-requirements"></a>Connectivité requise
1. À partir de l’ordinateur sur lequel vous accédez au portail utilisateur Azure Stack, vérifiez que vous pouvez accéder au portail de locataire Windows Azure Pack avec le navigateur web.
2. La machine virtuelle AzS-WASP01 sur Azure Stack doit pouvoir se connecter à l’ordinateur du portail de locataire Windows Azure Pack. Utilisez Ping.exe pour vérifier la connectivité réseau. 
3.  Vous devez avoir des certificats valides pour les nouveaux services de connecteur. Ces certificats SSL doivent être émis par une autorité de certification approuvée. Vous ne pouvez pas utiliser de certificats auto-signés. Les certificats SSL doivent être approuvés par Azure Stack (plus spécifiquement la machine virtuelle AzS-WASP01) et par tout autre ordinateur que le locataire est susceptible d’utiliser pour accéder au portail utilisateur Azure Stack.
 
    >[!NOTE]
    Dans la mesure où AzS-WASP01 exécute Windows Server avec l’option d’installation Server Core, vous pouvez utiliser un outil en ligne de commande comme Certutil.ext pour importer le certificat. Vous pouvez par exemple copier le fichier .cer dans c:\temp sur AzS-WASP01, puis exécuter la commande **certutil -addstore "CA" "c:\temp\certname.cer"**.

4.  Pour établir la connectivité RDP aux machines virtuelles du locataire Windows Azure Pack à partir du portail Azure Stack, il faut que l’environnement Windows Azure Pack autorise le trafic Bureau à distance sur les machines virtuelles du locataire.
5.  Pour la connectivité entre les machines virtuelles du locataire Azure Stack et les machines virtuelles du locataire Windows Azure Pack, il faut que leurs adresses IP externes soient routables entre les deux environnements. Cette connectivité peut également inclure l’association d’un serveur DNS pour résoudre les noms des machines virtuelles entre les environnements.

### <a name="iis-requirements"></a>Exigences relatives à IIS
L’extension IIS de réécriture d’URL doit être installée sur l’ordinateur qui héberge le portail de locataire Windows Azure Pack (qui peut être un hôte physique, une machine virtuelle ou plusieurs machines virtuelles). Si elle n’est pas encore installée, vous pouvez la télécharger à partir de [cette page](https://www.iis.net/downloads/microsoft/url-rewrite). Si plusieurs machines virtuelles hébergent le portail du locataire, installez l’extension sur chaque machine virtuelle.

## <a name="configure-azure-stack"></a>Configurer Azure Stack
Avant de configurer le connecteur Windows Azure Pack, vous devez activer le mode multicloud dans le portail utilisateur Azure Stack. Ce mode permet aux utilisateurs de sélectionner le cloud à partir duquel accéder aux ressources.

Pour activer le mode multicloud, vous devez exécuter le script Add-AzurePackConnector.ps1 après le déploiement d’Azure Stack. Le tableau suivant décrit les paramètres du script.


|  Paramètre | Description | Exemple |   
| -------- | ------------- | ------- |  
| AzurePackClouds | URI des connecteurs Windows Azure Pack. Ces URI doivent correspondre aux portails du locataire Windows Azure Pack. | @{CloudName = "AzurePack1"; CloudEndpoint = "https://waptenantportal1:40005"},@{CloudName = "AzurePack2"; CloudEndpoint = "https://waptenantportal2:40005"}<br><br>  (Par défaut, la valeur du port est 40005.) |  
| AzureStackCloudName | Étiquette représentant le cloud Azure Stack local.| "AzureStack" |
| DisableMultiCloud | Commutateur permettant de désactiver le mode multicloud.| N/A |
| | |

Vous pouvez exécuter le script Add-AzurePackConnector.ps1 immédiatement après le déploiement, ou plus tard. Pour exécuter le script immédiatement après le déploiement, utilisez la même session Windows PowerShell que celle où le déploiement d’Azure Stack s’est terminé. Autrement, vous pouvez ouvrir une nouvelle session Windows PowerShell en tant qu’administrateur (connecté en tant que compte azurestackadmin).

1. Exécutez le script Add-AzurePackConnector.ps1 en utilisant les commandes suivantes (avec des valeurs propres à votre environnement). Notez que le script Add-AzurePackConnector vous permet d’ajouter plusieurs points de terminaison du connecteur Windows Azure Pack.
 
 ```powershell
    $cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local", `
    (ConvertTo-SecureString -String "<password>" -AsPlainText -Force))
    $session = New-PSSession -ComputerName 'azs-ercs01' `
     -Credential $cred `
     -ConfigurationName PrivilegedEndpoint `
     -Authentication Credssp

    # Enable Multicloud
    Invoke-Command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
    @{CloudName = "AzurePack_1"; CloudEndpoint = "https://waptenantportal1:40005"},`
    @{CloudName = "AzurePack_2"; CloudEndpoint = "https://waptenantportal2:40005" } `
    -AzureStackCloudName "AzureStack" }

 ```
> [!NOTE]
> Dans la build actuelle, il existe un problème : une fois l’exécution du script Add-AzurePackConnector terminée, celui-ci reste dans une boucle d’interrogation pendant un certain temps (quelques minutes) jusqu’à ce qu’il se termine. Après l’affichage du message **VERBOSE : Étape « Configurer le connecteur Azure Pack » État : « Réussite »**, vous pouvez arrêter le script ou patienter jusqu’à ce qu’il s’arrête de lui-même. Cela ne fait aucune différence, car la configuration a déjà réussi.

2. Prenez note des fichiers de sortie générés par ce script, un pour chaque environnement Windows Azure Pack que vous avez spécifié. Les fichiers se trouvent à l’emplacement suivant : \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput. Ils contiennent les informations nécessaires pour configurer les environnements Windows Azure Pack cibles. Vous passerez ce fichier en tant que paramètre à un script plus loin dans ces instructions. Ce fichier contient les informations suivantes :

    * **AzurePackConnectorEndpoint** : contient le point de terminaison au service du connecteur Windows Azure Pack.
    * **AuthenticationIdentityProviderPartner** : contient la paire de valeurs suivante :
        * Certificat de signature de jeton d’authentification que l’API locataire Windows Azure Pack doit approuver pour accepter les appels à partir de l’extension du portail Azure Stack.

        * Paramètre « Realm » associé au certificat de signature. Par exemple : https://adfs.local.azurestack.global.external/adfs/c1d72562-534e-4aa5-92aa-d65df289a107/.

3.  Accédez au dossier qui contient les fichiers de sortie (\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput), puis copiez les fichiers sur votre ordinateur local. Les fichiers doivent ressembler à ceci : AzurePack-06-27-15-50.txt.

4.  Testez la configuration.

    a. Ouvrez votre navigateur et connectez-vous au portail utilisateur Azure Stack (https://portal.local.azurestack.external/).
    
    b. Une fois que vous vous êtes connecté en tant que locataire et que le portail s’est chargé, des erreurs s’affichent pour signaler qu’il est impossible de récupérer des abonnements ou des extensions à partir du cloud Azure Pack. Cliquez sur **OK** pour fermer ces messages. (Ces messages d’erreur disparaîtront une fois que vous aurez configuré Windows Azure Pack.)

    c. Notez la liste déroulante **Cloud** en haut à gauche du portail.

    ![Sélecteur de cloud dans l’interface utilisateur d’Azure Stack](media/azure-stack-manage-wap/image3.png)

## <a name="configure-windows-azure-pack"></a>Configurer Windows Azure Pack
Pour cette préversion du connecteur uniquement, vous devez configurer Windows Azure Pack manuellement.

>[!IMPORTANT]
Pour cette préversion, utilisez le connecteur Windows Azure Pack uniquement dans des environnements de test, et non dans des déploiements de production.

1.  Installez les fichiers MSI du connecteur sur la machine virtuelle du portail du locataire Windows Azure Pack et installez les certificats. (Si vous avez plusieurs machines virtuelles dans le portail du locataire, vous devez effectuer cette étape sur chaque machine virtuelle.)

    a. Dans l’Explorateur de fichiers, copiez le dossier **WAPConnector** (que vous avez téléchargé précédemment) dans un dossier nommé **c:\temp** sur la machine virtuelle du portail du locataire.

    b. Ouvrez une connexion Console ou RDP à la machine virtuelle du portail du locataire.

    c. Basculez vers le répertoire **c:\temp\wapconnector\setup\scripts** et exécutez le script **Install-Connector.ps1** pour installer trois fichiers MSI. Aucun paramètre n’est nécessaire.

     ```powershell
    cd C:\temp\wapconnector\setup\scripts\

    .\Install-Connector.ps1
    ```
     d. Basculez vers le répertoire **c:\inetpub** et vérifiez que les trois nouveaux sites sont installés :

       * MgmtSvc-Connector

       * MgmtSvc-ConnectorExtension

       * MgmtSvc-ConnectorController

    e. À partir du même dossier **c:\temp\wapconnector\setup\scripts**, exécutez le script **Configure-Certificates.ps1** pour installer les certificats. Par défaut, les certificats utilisés sont les mêmes que ceux qui sont disponibles pour le site Portail du locataire dans Windows Azure Pack. Vérifiez qu’il s’agit d’un certificat valide (approuvé par la machine virtuelle Azure Stack AzS-WASP01 et par tout ordinateur client qui accède au portail Azure Stack). Si ce n’est pas le cas, la communication ne fonctionnera pas. (En guise d’alternative, vous pouvez transmettre explicitement une empreinte numérique du certificat en tant que paramètre à l’aide du paramètre -Thumbprint.)

     ```powershell
        cd C:\temp\wapconnector\setup\scripts\

        .\Configure-Certificates.ps1
    ```

    f. Pour terminer la configuration de ces trois services, exécutez le script **Configure-WapConnector.ps1** pour mettre à jour les paramètres du fichier Web.config.

    |  Paramètre | Description | Exemple |   
    | -------- | ------------- | ------- |  
    | TenantPortalFQDN | Nom de domaine complet du portail du locataire Windows Azure Pack | tenant.contoso.com | 
    | TenantAPIFQDN | Nom de domaine complet de l’API locataire Windows Azure Pack | tenantapi.contoso.com  |
    | AzureStackPortalFQDN | Nom de domaine complet du portail utilisateur Azure Stack | portal.local.azurestack.external |
    | | |
    
     ```powershell
    .\Configure-WapConnector.ps1 -TenantPortalFQDN "tenant.contoso.com" `
         -TenantAPIFQDN "tenantapi.contoso.com" `
         -AzureStackPortalFQDN "portal.local.azurestack.external"
    ```
    g. Si vous avez plusieurs machines virtuelles de portail du locataire, répétez l’étape 1 pour chacune de ces machines virtuelles.

2. Installez le nouveau fichier MSI d’API locataire sur chaque machine virtuelle d’API locataire Windows Azure Pack.

    a. Si un équilibreur de charge est en cours d’utilisation, il est peut-être souhaitable de marquer la machine virtuelle comme étant hors connexion.

    b. Dans l’Explorateur de fichiers, copiez le dossier **WAPConnector** dans un dossier nommé **c:\temp** sur chaque ordinateur d’API locataire.

    c. Copiez le fichier AzurePackConnectorOutput.txt que vous avez enregistré précédemment dans **c:\temp\WAPConnector**.

    d. Ouvrez une connexion Console ou RDP à la machine virtuelle d’API locataire sur laquelle vous avez copié les fichiers.
    
    e. Basculez vers le répertoire **c:\temp\wapconnector\setup\scripts** et exécutez **Update-TenantAPI.ps1**. Cette nouvelle version de l’API locataire WAP présente un changement qui permet d’établir une relation d’approbation non seulement avec le service STS actuel, mais également avec l’instance AD FS dans Azure Stack.

     ```powershell
    cd C:\temp\wapconnector\setup\packages\

    .\Update-TenantAPI.ps1
    ```

    f.  Répétez l’étape 2 sur n’importe quelle autre machine virtuelle exécutant l’API locataire.
3. À partir d’**une seule** des machines virtuelles d’API locataire, exécutez le script Configure-TrustAzureStack.ps1 pour ajouter une relation d’approbation entre l’API locataire et l’instance AD FS sur Azure Stack. Vous devez utiliser un compte disposant d’un accès sysadmin à la base de données Microsoft.MgmtSvc.Store. Le script a les paramètres suivants :

    | Paramètre | Description | Exemple |
    | --------- | ------------| ------- |
   | SqlServer | Nom du serveur SQL qui contient la base de données Microsoft.MgmtSvc.Store. Ce paramètre est obligatoire. | SQLServer | 
   | DataFile | Fichier de sortie généré précédemment lors de la configuration du mode multicloud Azure Stack. Ce paramètre est obligatoire. | AzurePack-06-27-15-50.txt | 
   | PromptForSqlCredential | Indique que le script doit vous inviter de manière interactive à entrer des informations d’identification d’authentification SQL à utiliser lors de la connexion à l’instance SQL Server. Les informations d’identification spécifiées doivent disposer des autorisations suffisantes pour désinstaller des bases de données et des schémas, et pour supprimer des connexions utilisateur. Si vous n’en spécifiez aucune, le script part du principe que le contexte d’utilisateur actuel a accès. | Aucune valeur n’est nécessaire. |
   |  |  |

    Si vous ne connaissez pas le serveur SQL à utiliser, vous pouvez le découvrir. Connectez-vous à l’ordinateur de l’API locataire, utilisez la commande Unprotect-MgmtSvc pour ôter la protection du fichier Web.config de l’API locataire, puis recherchez le nom du serveur dans la chaîne de connexion. N’oubliez pas de réexécuter Protect-MgmtSvc pour protéger le fichier Web.config de l’API locataire.

  ```powershell
  cd C:\temp\wapconnector\setup\scripts\

 .\Configure-TrustAzureStack.ps1 -SqlServer "SQLServer" `
       -DataFile "C:\temp\wapconnector\AzurePackConnectorOutput.txt"
  ```

## <a name="example"></a>Exemple
L’exemple suivant montre un déploiement complet du connecteur Windows Azure Pack sur une configuration Azure Stack à nœud unique et deux installations de Windows Azure Pack Express. (Chaque installation Express se trouve sur un seul ordinateur, avec les exemples de noms *wapcomputer1* et *wapcomputer2*.)

```powershell
# Run the following script on the Azure Stack host
$cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local",`
     (ConvertTo-SecureString -String "p@ssw0rd" -AsPlainText -Force))
$session = New-PSSession -ComputerName 'azs-ercs01' -Credential $cred `
     -ConfigurationName PrivilegedEndpoint -Authentication Credssp
 
# Enable Multicloud
invoke-command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
     @{CloudName = "AzurePack_1"; CloudEndpoint = "https://wapcomputer1.contoso.com:40005"},`
     @{CloudName = "AzurePack_2"; CloudEndpoint = "https://wapcomputer2.contoso.com:40005"}`
     -AzureStackCloudName "AzureStack" }  

```
Téléchargez le fichier .exe à partir du [Centre de téléchargement Microsoft](https://aka.ms/wapconnectorazurestackdlc), extrayez-le et copiez le dossier WAPConnector dans un dossier **c:\temp** sur l’ordinateur Windows Azure Pack. Copiez les fichiers qui ont été générés comme sortie du script précédent (ils se trouvent dans \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) dans le dossier **c:\temp\WAPConnector**. (Les fichiers doivent ressembler à ceci : AzurePack-06-27-15-50.txt.) Ensuite, exécutez le script suivant une fois par instance de Windows Azure Pack :

 ```powershell
# Install Connector components
cd C:\temp\WAPConnector\Setup\Scripts
.\Install-Connector.ps1

# Configure Certificates for the new Connector services
.\Configure-Certificates.ps1

# Configure the Connector services
.\Configure-WapConnector.ps1 -TenantPortalFQDN "wapcomputer1.contoso.com" `
     -TenantAPIFQDN "wapcomputer1.contoso.com" `
     -AzureStackPortalFQDN "portal.local.azurestack.external"

# Install the updated TenantAPI
.\Update-TenantAPI.ps1

# Establish trust with the Azure Stack AD FS
.\Configure-TrustAzureStack.ps1 -SqlServer "wapcomputer1" `
     -DataFile "C:\temp\wapconnector\AzurePack-06-27-15-50.txt" 

```
## <a name="troubleshooting-tips"></a>Conseils de dépannage
1.  Vérifiez la connectivité réseau entre Azure Stack et Windows Azure Pack. Cette connectivité doit exister entre tout ordinateur du locataire qui accède au portail Azure Stack et la machine virtuelle du portail du locataire Windows Azure Pack sur laquelle les nouveaux services de connecteur sont en cours d’exécution.
2.  Vérifiez que tous les noms de domaine complets spécifiés sont corrects.
3.  Vérifiez que les certificats SSL utilisés dans les nouveaux services de connecteur sont approuvés par Azure Stack (plus spécifiquement la machine virtuelle AzS-WASP01) et par tout autre ordinateur que le locataire est susceptible d’utiliser pour accéder au portail utilisateur Azure Stack.
4. Pour plus d’informations sur les problèmes connus, consultez [Dépannage de Microsoft Azure Stack](azure-stack-troubleshooting.md).


## <a name="next-steps"></a>étapes suivantes
[Utilisation des portails administrateur et utilisateur dans Azure Stack](azure-stack-manage-portals.md)
