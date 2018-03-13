---
title: "Intégration au centre de données Azure Stack - Identité"
description: "Découvrez comment intégrer les services Azure Stack AD FS aux services AD FS de votre centre de données"
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 9e71707f13a14cb4cd0bc9cc7cd7d13dd7bb38e5
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="azure-stack-datacenter-integration---identity"></a>Intégration au centre de données Azure Stack - Identité
Vous pouvez déployer Azure Stack en utilisant Azure Active Directory (Azure AD) ou Active Directory Federation Services (AD FS) en tant que fournisseur d’identité. Vous devez faire le choix avant de déployer Azure Stack. Le déploiement à l’aide d’AD FS est également appelé déploiement d’Azure Stack en mode déconnecté.

Le tableau suivant montre les différences entre ces deux choix d’identité :

||Déconnecté d’Internet|Connecté à Internet|
|---------|---------|---------|
|Facturation|Doit être une capacité<br> Contrat Entreprise (EA) uniquement|Selon la capacité ou paiement à l’utilisation<br>EA ou fournisseur de solutions cloud (CSP)|
|Identité|Doit être AD FS|Azure AD ou AD FS|
|Syndication de Place de marché|Prise en charge<br>Licences BYOL|Prise en charge<br>Licences BYOL|
|Inscription|Recommandé, nécessite un support amovible<br> et un appareil connecté distinct.|Automatisé|
|Correctifs et mises à jour|Requis, nécessite un support amovible<br> et un appareil connecté distinct.|Un package de mise à jour peut être téléchargé directement<br> depuis Internet dans Azure Stack.|

> [!IMPORTANT]
> Vous ne pouvez pas changer de fournisseur d’identité sans redéployer la solution Azure Stack complète.

## <a name="active-directory-federation-services-and-graph"></a>Active Directory Federation Services et Graph

Grâce au déploiement avec AD FS, les identités dans une forêt Active Directory existante peuvent s’authentifier auprès des ressources dans Azure Stack. Cette forêt Active Directory existante nécessite un déploiement d’AD FS pour permettre la création d’une fédération AD FS de confiance.

L’authentification est un composant d’identité. Pour gérer le contrôle d’accès en fonction du rôle (RBAC) dans Azure Stack, le composant Graph doit être configuré. Lorsque l’accès à une ressource est délégué, le composant Graph recherche le compte d’utilisateur dans la forêt Active Directory existante à l’aide du protocole LDAP.

![Architecture AD FS d’Azure Stack](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

Les services AD FS existants représentent le service d'émission de jeton de sécurité du compte qui envoie des demandes aux services AD FS d’Azure Stack (STS ressource). Dans Azure Stack, l’automation crée l’approbation de fournisseur de revendications avec le point de terminaison de métadonnées pour les services AD FS existants.

Sans les services AD FS existants, une partie de confiance doit être configurée. Cette étape n’est pas effectuée par l’automation et doit être configurée par l’opérateur. Le point de terminaison de métadonnées Azure Stack est documenté dans le fichier AzureStackStampDeploymentInfo.JSON, ou via le point de terminaison privilégié en exécutant la commande `Get-AzureStackInfo`.

La configuration de la partie de confiance nécessite également la configuration des règles de transformation de revendication fournies par Microsoft.

Pour la configuration Graph, un compte de service doit être fourni, avec un accès en lecture au Active Directory existant. Ce compte est requis en tant qu’entrée pour permettre à l’automation de gérer les scénarios RBAC.

Pour la dernière étape, un nouveau propriétaire est configuré pour l’abonnement du fournisseur par défaut. Ce compte dispose d’un accès complet à toutes les ressources lorsqu’il est connecté au portail d’administration d’Azure Stack.

Requirements:


|Composant|Prérequis|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Configuration de l’intégration de Graph

Les informations suivantes sont requises en tant qu’entrées pour les paramètres d’automation :


|Paramètre|Description|exemples|
|---------|---------|---------|
|CustomADGlobalCatalog|Nom de domaine complet de la forêt Active Directory cible<br>que vous souhaitez intégrer dans|Contoso.com|
|CustomADAdminCredentials|Un utilisateur avec autorisation de lecture LDAP|YOURDOMAIN\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Créer un compte d’utilisateur dans l’Active Directory existant (facultatif)

Vous pouvez également créer un compte pour le service Graph dans l’Active Directory existant. Effectuez cette étape si vous n’avez pas déjà un compte que vous souhaitez utiliser.

1. Dans l’Active Directory existant, créez le compte d’utilisateur suivant (recommandé) :
   - **Nom d’utilisateur** : graphservice
   - **Mot de passe** : utilisez un mot de passe fort<br>Configurez le mot de passe pour qu’il n’expire jamais.

   Aucune autorisation ou appartenance spéciale n’est requise.

#### <a name="trigger-automation-to-configure-graph"></a>Déclencher l’automatisation pour configurer Graph

Pour cette procédure, utilisez un ordinateur de votre réseau de centre de données qui peut communiquer avec le point de terminaison privilégié dans Azure Stack.

2. Ouvrez une session Windows PowerShell avec élévation de privilèges (exécuter en tant qu’administrateur) et connectez-vous à l’adresse IP du point de terminaison privilégié. Utilisez les informations d’identification de **CloudAdmin** à authentifier.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. Maintenant que vous êtes connecté au point de terminaison privilégié, exécutez la commande suivante : 

   ```powershell
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Lorsque vous y êtes invité, spécifiez les informations d’identification du compte d’utilisateur que vous souhaitez utiliser pour le service Graph (par exemple, graphservice).

   > [!IMPORTANT]
   > Attendez que les informations d’identification apparaissent (Get-Credential n’est pas pris en charge dans le point de terminaison privilégié) et entrez les informations d’identification du compte du service Graph.

#### <a name="graph-protocols-and-ports"></a>Ports et protocoles Graph

Le service Graph d’Azure Stack utilise les protocoles et ports suivants pour communiquer avec l’Active Directory cible :

|type|Port|Protocole|
|---------|---------|---------|
|LDAP|389|TCP et UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Configuration de l’intégration AD FS en téléchargeant des métadonnées de fédération

Les informations suivantes sont nécessaires en entrée pour les paramètres Automation :

|Paramètre|Description|exemples|
|---------|---------|---------|
|CustomAdfsName|Nom du fournisseur de revendications.<cr>Il apparaît sous cette forme sur la page d’accueil AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Lien Métadonnées de fédération|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Déclencher l’automation pour configurer un fournisseur de revendications de confiance dans Azure Stack

Pour cette procédure, utilisez un ordinateur qui peut communiquer avec le point de terminaison privilégié dans Azure Stack. Le certificat utilisé par le compte **STS AD FS** doit être approuvé par Azure Stack.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et connectez-vous au point de terminaison privilégié.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Maintenant que vous êtes connecté au point de terminaison privilégié, exécutez la commande suivante en utilisant les paramètres correspondant à votre environnement :

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Exécutez la commande suivante pour mettre à jour le propriétaire de l’abonnement du fournisseur par défaut, en utilisant les paramètres correspondant à votre environnement :

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Configuration de l’intégration AD FS en fournissant un fichier de métadonnées de fédération

Utilisez cette méthode si l’une des conditions suivantes est remplie :

- La chaîne de certificats est différente pour AD FS par rapport à tous les autres points de terminaison dans Azure Stack.
- Aucune connectivité réseau vers le serveur AD FS existant depuis l’instance AD FS d’Azure Stack.

Les informations suivantes sont nécessaires en entrée pour les paramètres Automation :


|Paramètre|Description|exemples|
|---------|---------|---------|
|CustomAdfsName|Nom du fournisseur de revendications. Il apparaît ainsi dans la page d’accueil AD FS.|Contoso|
|CustomADFSFederationMetadataFile|Fichier de métadonnées de fédération|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|

### <a name="create-federation-metadata-file"></a>Créer un fichier de métadonnées de fédération

Pour la procédure suivante, vous devez utiliser un ordinateur qui dispose d’une connectivité réseau vers le déploiement d’AD FS existant, qui devient le compte STS. En outre, les certificats nécessaires doivent être installés.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges puis exécutez la commande suivante en utilisant les paramètres correspondant à votre environnement :

   ```powershell
   [XML]$Metadata = Invoke-WebRequest -URI https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml -UseBasicParsing

   $Metadata.outerxml|out-file c:\metadata.xml
   ```

2. Copiez le fichier de métadonnées vers un partage accessible depuis le point de terminaison privilégié.


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Déclencher l’automation pour configurer un fournisseur de revendications de confiance dans Azure Stack

Pour cette procédure, utilisez un ordinateur qui peut communiquer avec le point de terminaison privilégié dans Azure Stack.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et connectez-vous au point de terminaison privilégié.

   ```powershell
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Maintenant que vous êtes connecté au point de terminaison privilégié, exécutez la commande suivante en utilisant les paramètres correspondant à votre environnement :

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso – CustomADFSFederationMetadataFile \\share\metadataexample.xml
   ```

3. Exécutez la commande suivante pour mettre à jour le propriétaire de l’abonnement du fournisseur par défaut, en utilisant les paramètres correspondant à votre environnement :

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Configurer la partie de confiance sur le déploiement AD FS existant (compte STS)

Microsoft fournit un script qui configure la partie de confiance, y compris les règles de transformation de revendication. L’utilisation du script est facultative car vous pouvez exécuter les commandes manuellement.

Vous pouvez télécharger le script d’assistance dans les [outils Azure Stack](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) sur GitHub.

Si vous décidez d’exécuter manuellement les commandes, procédez comme suit :

1. Copiez le contenu suivant dans un fichier .txt (par exemple, enregistré en tant que c:\ClaimRules.txt) sur l’instance AD FS de votre centre de données ou membre de la batterie de serveurs :

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname]
   => issue(claim = c);
   ```

2. Pour activer l’authentification basée sur Windows Forms, ouvrez une session Windows PowerShell en tant qu’utilisateur avec élévation de privilèges puis exécutez la commande suivante :

   ```powershell
   Set-AdfsProperties -WIASupportedUserAgents @("MSAuthHost/1.0/In-Domain","MSIPC","Windows Rights Management Client","Kloud")
   ```

3. Pour ajouter la partie de confiance, exécutez la commande Windows PowerShell suivante sur votre instance AD FS ou membre de la batterie de serveurs. Veillez à mettre à jour le point de terminaison AD FS et pointez vers le fichier créé à l’étape 1.

   **Pour AD FS 2016**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone"
   ```

   **Pour AD FS 2012/2012 R2**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true
   ```

   > [!IMPORTANT]
   > Vous devez utiliser le composant logiciel enfichable MMC AD FS pour configurer les règles d’autorisation d’émission si vous utilisez AD FS sous Windows Server 2012 ou 2012 R2.

4. Si vous utilisez Internet Explorer ou Edge pour accéder à Azure Stack, vous devez ignorer les liaisons de jeton. Sinon, les tentatives de connexion échouent. Sur votre instance AD FS ou membre de la batterie de serveurs, exécutez la commande suivante :

   ```powershell
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

5. Pour autoriser l’actualisation des jetons, ouvrez une session Windows PowerShell avec élévation de privilèges et exécutez la commande suivante :

   ```powershell
   Set-ADFSRelyingPartyTrust -TargetName AzureStack -TokenLifeTime 1440
   ```

## <a name="spn-creation"></a>Création du nom principal de service

Il existe plusieurs scénarios qui requièrent l’utilisation d’un nom principal de service (SPN) pour l’authentification. Voici quelques exemples :

- Utilisation de l’interface CLI avec un déploiement AD FS d’Azure Stack
- Pack d’administration System Center pour Azure Stack lors d’un déploiement avec AD FS
- Fournisseurs de ressources dans Azure Stack lors d’un déploiement avec AD FS
- Différentes applications
- Une ouverture de session non interactive est requise

Pour plus d’informations sur la création d’un SPN, consultez [Créer un principal de service pour AD FS](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs).


## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="configuration-rollback"></a>Restauration de la configuration

Si une erreur se produit et laisse l’environnement dans un état vous empêchant de vous authentifier, une option de restauration est disponible.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et exécutez les commandes suivantes :

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Puis exécutez l’applet de commande suivante :

   ```powershell
   Reset-DatacenterIntegationConfiguration
   ```

   Après avoir exécuté l’action de restauration, toutes les modifications de configuration sont restaurées. Seule l’authentification avec l’utilisateur **CloudAdmin** intégré est possible.

   > [!IMPORTANT]
   > Vous devez configurer le propriétaire d’origine de l’abonnement du fournisseur par défaut

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Collecte de journaux supplémentaires

Si une des applets de commande échoue, vous pouvez collecter des journaux supplémentaires à l’aide de l’applet de commande `Get-Azurestacklogs`.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et exécutez les commandes suivantes :

   ```powershell
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Puis exécutez l’applet de commande suivante :

   ```powershell
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>étapes suivantes

[Inscrire Azure Stack](azure-stack-registration.md)
