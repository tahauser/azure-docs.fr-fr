---
title: "Avant de déployer App Service sur Azure Stack | Microsoft Docs"
description: "Étapes à effectuer avant de déployer App Service sur Azure Stack"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: d6962bf2ffbf731a4aa301e663c7c7d3428080d4
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Avant de commencer avec App Service sur Azure Stack

Azure App Service sur Azure Stack comporte un nombre d’étapes qui doivent être effectuées avant le déploiement :

- Télécharger les scripts d’assistance d’Azure App Service sur Azure Stack
- Haute disponibilité
- Certificats requis pour Azure App Service sur Azure Stack
- Préparer le serveur de fichiers
- Préparer SQL Server
- Créer une application Azure Active Directory
- Créer une application de services de fédération Active Directory (AD FS)

## <a name="download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts"></a>Télécharger le programme d’installation et les scripts helper d’Azure App Service sur Azure Stack

1. Téléchargez les [scripts d’assistance au déploiement App Service sur Azure Stack](https://aka.ms/appsvconmashelpers).
2. Téléchargez le [programme d’installation d’App Service sur Azure Stack](https://aka.ms/appsvconmasinstaller).
3. Extrayez les fichiers à partir du fichier zip des scripts d’assistance. La structure de fichiers et de dossier suivante s’affiche :
  - Common.ps1
  - Create-AADIdentityApp.ps1
  - Create-ADFSIdentityApp.ps1
  - Create-AppServiceCerts.ps1
  - Get-AzureStackRootCert.ps1
  - Remove-AppService.ps1
  - Modules
    - GraphAPI.psm1
    
## <a name="high-availability"></a>Haute disponibilité

Azure App Service n’est actuellement pas capable d’offrir une haute disponibilité sur Azure Stack, car ce dernier ne déploie des charges de travail que dans un seul domaine par défaut.

Pour préparer une haute disponibilité sur Azure Stack, veillez à déployer le serveur de fichier et SQL Server nécessaires dans une configuration hautement disponible. Lorsqu’Azure Stack prend en charge plusieurs domaines par défaut, nous vous guiderons dans l’activation d’Azure App Service sur Azure Stack dans une configuration hautement disponible.


## <a name="certificates-required-for-azure-app-service-on-azure-stack"></a>Certificats requis pour Azure App Service sur Azure Stack

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Certificats requis pour le Kit de développement Azure Stack

Ce premier script fonctionne avec l’autorité de certification Azure Stack pour créer quatre certificats dont App Service a besoin.

| Nom de fichier | Utilisation |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Certificat SSL par défaut d’App Service |
| Api.appservice.local.azurestack.external.pfx | Certificat SSL par défaut de l’API App Service |
| ftp.appservice.local.azurestack.external.pfx | Certificat SSL par défaut d’App Service Publisher |
| Sso.appservice.local.azurestack.external.pfx | Certificat d’application d’identité App Service |

Exécutez le script sur l’hôte du Kit de développement Azure Stack et vérifiez que vous exécutez PowerShell en tant que azurestack\CloudAdmin.

1. Dans une session PowerShell exécutée en tant que azurestack\AzureStackAdmin, exécutez le script Create-AppServiceCerts.ps1 à partir du dossier où vous avez extrait les scripts d’assistance. Le script crée quatre certificats, dans le même dossier que le script des certificats de création, qui sont requis par App Service.
2. Entrez un mot de passe pour sécuriser les fichiers .pfx et prenez-en note. Vous devez l’entrer dans le programme d’installation d’App Service sur Azure Stack.

#### <a name="create-appservicecertsps1-parameters"></a>Paramètres Create-AppServiceCerts.ps1

| Paramètre | Obligatoire ou facultatif | Valeur par défaut | Description |
| --- | --- | --- | --- |
| pfxPassword | Requis | Null | Mot de passe utilisé pour protéger la clé privée du certificat |
| DomainName | Requis | local.azurestack.external | Suffixe de la région et du domaine Azure Stack |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Certificats requis pour un déploiement de production d’Azure App Service sur Azure Stack

Pour utiliser le fournisseur de ressources en production, vous devez fournir les quatre certificats suivants :

#### <a name="default-domain-certificate"></a>Certificat de domaine par défaut

Le certificat de domaine par défaut est placé sur le rôle de serveur frontal. Il est utilisé par les applications utilisateur pour les demandes de domaine par défaut ou de caractères génériques au Azure App Service. Le certificat est également utilisé pour les opérations de contrôle de code source (KUDU).

Le certificat doit être au format .pfx et doit être un certificat générique de deux objets. Cela permet à la fois au domaine par défaut et au point de terminaison scm pour les opérations de contrôle de code source d’être couverts par un seul certificat.

| Format | Exemple |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certificat d’API

Le certificat d’API est placé sur le rôle de gestion et est utilisé par le fournisseur de ressources pour sécuriser les appels d’api. Le certificat de publication doit contenir un objet qui correspond à l’entrée DNS de l’API :

| Format | Exemple |
| --- | --- |
| api.appservice.\<région\>.\<nom_domaine\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Certificat de publication

Le certificat du rôle de serveur de publication sécurise le trafic FTPS des propriétaires d’applications lorsqu’ils chargent du contenu.  Le certificat de publication doit contenir un objet qui correspond à l’entrée DNS FTPS.

| Format | Exemple |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certificat d’identité

Le certificat de l’application de l’identité permet :
- l’intégration entre le répertoire AAD/ADFS, Azure Stack et App Service pour prendre en charge l’intégration avec le fournisseur de ressources de calcul
- Scénarios d’authentification unique pour les outils de développement avancés dans Azure App Service sur Azure Stack.
Le certificat d’identité doit contenir un objet qui correspond au format suivant :

| Format | Exemple |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

### <a name="extract-the-azure-stack-azure-resource-manager-root-certificate"></a>Extraire le certificat racine Azure Resource Manager d’Azure Stack

Dans une session PowerShell exécutée en tant que azurestack\CloudAdmin, exécutez le script Get-AzureStackRootCert.ps1 à partir du dossier où vous avez extrait les scripts helper. Le script crée quatre certificats, dans le même dossier que le script des certificats de création, qui sont requis par App Service.

| Paramètre Get-AzureStackRootCert.ps1 | Obligatoire ou facultatif | Valeur par défaut | Description |
| --- | --- | --- | --- |
| PrivelegedEndpoint | Requis | AzS-ERCS01 | Point de terminaison privilégié. |
| CloudAdminCredential | Requis | AzureStack\CloudAdmin | Informations d’identification du compte de domaine d’administrateur cloud d’Azure Stack |


## <a name="prepare-the-file-server"></a>Préparer le serveur de fichiers

Azure App Service requiert l’utilisation d’un serveur de fichiers. Pour les déploiements de production, le serveur de fichiers doit être configuré en haute disponibilité et capable de gérer les défaillances.

Pour une utilisation avec les déploiements du Kit de développement Azure Stack uniquement, vous pouvez utiliser cet exemple de modèle de déploiement Azure Resource Manager pour déployer un serveur de fichiers configuré avec un seul nœud : https://aka.ms/appsvconmasdkfstemplate. Le serveur de fichiers à nœud unique sera dans un groupe de travail.

### <a name="provision-groups-and-accounts-in-active-directory"></a>Approvisionner des groupes et des comptes dans Active Directory


1. Créez les groupes de sécurité globaux Active Directory suivants :
    - FileShareOwners
    - FileShareUsers
2. Créez les comptes Active Directory suivants en tant que comptes du service :
    - FileShareOwner
    - FileShareUser

    En ce qui concerne les meilleures pratiques en termes de sécurité, les utilisateurs de ces comptes (et pour tous les rôles Web) doivent être distincts les uns des autres et détenir des noms d’utilisateur et des mots de passe sécurisés.
    Définissez les mots de passe avec les conditions suivantes :
     - Activez **Le mot de passe n’expire jamais**.
     - Activez **L’utilisateur ne peut pas changer de mot de passe**.
     - Désactivez **L’utilisateur doit changer de mot de passe à la prochaine ouverture de session**.
3. Ajoutez les comptes aux appartenances aux groupes comme suit :
    - Ajoutez **FileShareOwner** au groupe **FileShareOwners**.
    - Ajoutez **FileShareUser** au groupe **FileShareUsers**.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Approvisionner des groupes et des comptes dans un groupe de travail

>[!NOTE]
> Exécutez toutes les commandes suivantes quand vous configurez le serveur de fichiers dans une session d’invite de commandes administrateur.  **N’utilisez PAS PowerShell.**

Lorsque vous utilisez le modèle Azure Resource Manager ci-dessus, les utilisateurs sont d’ores et déjà créés.

1. Exécutez les commandes suivantes pour créer les comptes FileShareOwner et FileShareUser. Remplacez <password> par vos propres valeurs.
``` DOS
net user FileShareOwner <password> /add /expires:never /passwordchg:no
net user FileShareUser <password> /add /expires:never /passwordchg:no
```
2. Définissez les mots de passe pour les comptes de sorte qu’ils n’expirent jamais en exécutant les commandes WMIC suivantes :
``` DOS
WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
```
3. Créez les groupes locaux FileShareUsers et FileShareOwners et ajoutez-y les comptes lors de la première étape.
``` DOS
net localgroup FileShareUsers /add
net localgroup FileShareUsers FileShareUser /add
net localgroup FileShareOwners /add
net localgroup FileShareOwners FileShareOwner /add
```

### <a name="provision-the-content-share"></a>Approvisionner le partage de contenu

Le partage de contenu contient le contenu du site web du locataire. La procédure d’approvisionnement de partage de contenu sur un seul serveur de fichiers est identique pour les environnements Active Directory et Groupe de travail, mais diffère pour un cluster de basculement dans Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-ad-or-workgroup"></a>Approvisionner le partage de contenu sur un seul serveur de fichiers (AD ou Groupe de travail)

Exécutez les commandes suivantes dans une invite de commandes avec élévation de privilèges sur un serveur de fichiers unique. Remplacez la valeur pour < C:\WebSites > par les chemins d’accès correspondants dans votre environnement.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="to-enable-winrm-add-the-fileshareowners-group-to-the-local-administrators-group"></a>Pour activer WinRM, ajoutez le groupe FileShareOwners au groupe Administrateurs local

Pour le bon fonctionnement de Windows Remote Management, vous devez ajouter le groupe FileShareOwners au groupe Administrateurs local.

#### <a name="active-directory"></a>Active Directory

Exécutez les commandes suivantes dans une invite de commandes avec élévation de privilèges sur le serveur de fichiers ou sur chaque nœud de cluster de basculement du serveur de fichiers. Remplacez la valeur de <DOMAIN> par le nom de domaine que vous souhaitez utiliser.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Groupe de travail

Exécutez la commande suivante dans une invite de commandes avec élévation de privilèges sur le serveur de fichiers.

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>Configurer le contrôle d’accès aux partages

Exécutez les commandes suivantes dans une invite de commandes avec élévation de privilèges sur le serveur de fichiers ou sur le nœud de cluster de basculement du serveur de fichiers, qui est actuellement le propriétaire de la ressource de cluster. Remplacez les valeurs en italiques par les valeurs spécifiques de votre environnement.

#### <a name="active-directory"></a>Active Directory
```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Groupe de travail
```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server"></a>Préparer le serveur SQL Server

Pour qu’Azure App Service sur Azure Stack héberge et contrôle les bases de données, vous devez préparer un serveur SQL Server pour stocker les bases de données d’Azure App Service.

Pour une utilisation avec le Kit de développement Azure Stack, vous pouvez utiliser SQL Express 2014 SP2 ou une version ultérieure.

Pour des raisons de production et de haute disponibilité, vous devez utiliser une version complète de SQL Express 2014 SP2 ou une version ultérieure, activer l’authentification en mode mixte et déployer une [configuration hautement disponible](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Azure App Service sur Azure Stack SQL Server doit être accessible à partir de tous les rôles App Service. SQL Server peut être déployé au sein d’un abonnement fournisseur par défaut dans Azure Stack. Vous pouvez aussi vous servir d’une infrastructure existante au sein de votre organisation (tant qu’il existe une connectivité avec Azure Stack). Si vous utilisez une image de la Place de marché Azure, pensez à configurer en fonction le pare-feu. 

Pour tous les rôles SQL Server, vous pouvez utiliser une instance par défaut ou une instance nommée. Toutefois, si vous utilisez une instance nommée, assurez-vous de démarrer manuellement le service SQL Browser et d’ouvrir le port 1434.

## <a name="create-an-azure-active-directory-application"></a>Créer une application Azure Active Directory

Approvisionner un principal du service Azure AD pour prendre en charge les éléments suivants :
- Intégration d’un groupe de machines virtuelles identiques sur les niveaux Worker.
- Authentification unique pour le portail Azure Functions et les outils de développement avancés.

Ces étapes s’appliquent uniquement aux environnements Azure Stack sécurisés avec Azure AD.

Les administrateurs doivent configurer l’authentification unique pour :
- Activer les outils de développement avancés dans App Service (Kudu).
- Activer l’utilisation de l’expérience du portail Azure Functions.

Procédez comme suit :

1. Ouvrez une instance PowerShell en tant que azurestack\Azurestackadmin.
2. Accédez à l’emplacement où les scripts ont été téléchargés et extraits dans [l’étape des prérequis](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Installez Azure Stack PowerShell](azure-stack-powershell-install.md).
4. Exécutez le script **Create-AADIdentityApp.ps1**. Lorsque vous êtes invité à entrer votre ID de locataire Azure AD, entrez l’ID de locataire Azure AD que vous utilisez pour votre déploiement Azure Stack, par exemple, myazurestack.onmicrosoft.com.
5. Dans la fenêtre **Informations d’identification**, entrez votre compte administrateur et votre mot de passe pour le service Azure AD. Cliquez sur **OK**.
6. Entrez le chemin d’accès au fichier du certificat et le mot de passe du certificat pour le [certificat créé précédemment](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Le certificat par défaut créé pour cette étape est **sso.appservice.local.azurestack.external.pfx**.
7. Le script crée une application dans le locataire Azure AD. Notez l’ID d’application qui est retourné dans la sortie PowerShell. Vous avez besoin de ces informations lors de l’installation.
8. Ouvrez une nouvelle fenêtre de navigateur et connectez-vous au portail Azure (portal.azure.com) en tant que **Administrateur du service Azure Active Directory**.
9. Ouvrez le fournisseur de ressources Azure AD.
10. Cliquez sur **Inscriptions des applications**.
11. Recherchez **l’ID d’application** retourné à l’étape 7. Une application App Service est répertoriée.
12. Cliquez sur **Application** dans la liste
13. Cliquez sur **Autorisations requises** > **Accorder des autorisations** > **Oui**.

| Create-AADIdentityApp.ps1  paramètre | Obligatoire ou facultatif | Valeur par défaut | Description |
| --- | --- | --- | --- |
| DirectoryTenantName | Requis | Null | ID de locataire Azure AD. Fournir le GUID ou une chaîne, par exemple, myazureaaddirectory.onmicrosoft.com |
| AdminArmEndpoint | Requis | Null | Point de terminaison Azure Resource Manager d’administration, par exemple adminmanagement.local.azurestack.external |
| TenantARMEndpoint | Requis | Null | Point de terminaison Azure Resource Manager du locataire, par exemple management.local.azurestack.external |
| AzureStackAdminCredential | Requis | Null | Informations d’identification de l’administrateur du service Azure AD |
| CertificateFilePath | Requis | Null | Chemin d’accès au fichier de certificat d’application d’identité généré précédemment. |
| CertificatePassword | Requis | Null | Mot de passe utilisé pour protéger la clé privée du certificat. |

## <a name="create-an-active-directory-federation-services-application"></a>Créer une application de services de fédération Active Directory (AD FS)

Pour les environnements Azure Stack sécurisés par AD FS, vous devez configurer un principal du service AD FS pour prendre en charge les éléments suivants :
- Intégration d’un groupe de machines virtuelles identiques sur les niveaux Worker.
- Authentification unique pour le portail Azure Functions et les outils de développement avancés.

Les administrateurs doivent configurer l’authentification unique pour :
- Configurer un principal du service pour l’intégration d’un groupe de machines virtuelles identiques sur les niveaux Worker.
- Activer les outils de développement avancés dans App Service (Kudu).
- Activer l’utilisation de l’expérience du portail Azure Functions.

Procédez comme suit :

1. Ouvrez une instance PowerShell en tant que azurestack\azurestackadmin.
2. Accédez à l’emplacement où les scripts ont été téléchargés et extraits dans [l’étape des prérequis](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Installez Azure Stack PowerShell](azure-stack-powershell-install.md).
4.  Exécutez le script **Create-ADFSIdentityApp.ps1**.
5.  Dans la fenêtre **Informations d’identification**, entrez votre compte administrateur et votre mot de passe pour le cloud AD FS. Cliquez sur **OK**.
6.  Entrez le chemin d’accès au fichier du certificat et le mot de passe du certificat pour le [certificat créé précédemment](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Le certificat par défaut créé pour cette étape est sso.appservice.local.azurestack.external.pfx.

| Create-ADFSIdentityApp.ps1  paramètre | Obligatoire ou facultatif | Valeur par défaut | Description |
| --- | --- | --- | --- |
| AdminArmEndpoint | Requis | Null | Point de terminaison Azure Resource Manager d’administrateur. Par exemple : adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Requis | Null | Point de terminaison privilégié. Par exemple, AzS-ERCS01. |
| CloudAdminCredential | Requis | Null | Information d’identification du compte de domaine cloudadmin d’Azure Stack. Par exemple : Azurestack\CloudAdmin. |
| CertificateFilePath | Requis | Null | Chemin d’accès au fichier PFX du certificat d’application d’identité. |
| CertificatePassword | Requis | Null | Mot de passe utilisé pour protéger la clé privée du certificat. |


## <a name="next-steps"></a>Étapes suivantes

[Installer le fournisseur de ressources App Service](azure-stack-app-service-deploy.md).
