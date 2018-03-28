---
title: Comment configurer des appareils hybrides joints à Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer des appareils hybrides joints à Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 34d1ba2e1e84c268442d47d8865d3e3bebb53e53
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-configure-hybrid-azure-active-directory-joined-devices"></a>Comment configurer des appareils hybrides joints à Azure Active Directory

La fonction de gestion des appareils intégrée à Azure Active Directory (Azure AD) vous permet de vous assurer que vos utilisateurs accèdent à vos ressources à partir d’appareils qui répondent à vos normes de conformité et de sécurité. Pour plus d’informations, voir [Présentation de la gestion des appareils dans Azure Active Directory](device-management-introduction.md).

Si vous disposez d’un environnement Active Directory local et que vous souhaitez lier à Azure AD vos appareils joints à un domaine, vous pouvez y parvenir en configurant simplement des appareils hybrides joints à Azure AD. Cette rubrique vous présente les étapes de cette configuration. 


## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer à configurer des appareils hybrides joints à Azure AD dans votre environnement, vous devez vous familiariser avec les scénarios pris en charge et avec les contraintes.  

Si vous comptez sur [l’outil de préparation système (Sysprep)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc721940(v=ws.10)), assurez-vous que vous créez des images à partir d’une installation de Windows qui n’a pas été encore inscrite auprès d’Azure AD.

Tous les appareils joints à un domaine qui exécutent Mise à jour anniversaire Windows 10 et Windows Server 2016 s’inscrivent automatiquement auprès d’Azure AD lors du redémarrage des appareils ou de la connexion des utilisateurs une fois les étapes de configuration mentionnées ci-dessous terminées. **Si ce comportement d’inscription automatique n’est pas recommandé, ou si un lancement contrôlé est souhaité**, suivez tout d’abord les instructions de la section « Étape 4 : Contrôle du déploiement et du lancement » ci-dessous pour activer ou désactiver le lancement automatique de manière sélective avant de suivre les autres étapes de configuration.  

Pour améliorer la lisibilité des descriptions, cet article utilise les termes suivants : 

- **Appareils Windows actuels** : ce terme désigne les appareils joints à un domaine qui exécutent Windows 10 ou Windows Server 2016.
- **Appareils Windows de bas niveau** : ce terme fait référence à tous les appareils Windows joints à un domaine **pris en charge** qui n’exécutent ni Windows 10 ni Windows Server 2016.  

### <a name="windows-current-devices"></a>Appareils Windows actuels

- Pour les appareils qui fonctionnent avec le système d’exploitation d’ordinateur Windows, la version prise en charge est la Mise à jour anniversaire Windows 10 (version 1607) ou une version ultérieure. 
- L’inscription des appareils Windows actuels **est** prise en charge dans les environnements non fédérés tels que les configurations de synchronisation du hachage de mot de passe.  


### <a name="windows-down-level-devices"></a>Appareils Windows de bas niveau

- Les appareils Windows de bas niveau pris en charge sont les suivants :
    - Windows 8.1
    - Windows 7
    - Windows Server 2012 R2
    - Windows Server 2012
    - Windows Server 2008 R2
- L’inscription d’appareils de bas niveau Windows **est** prise en charge dans les environnements non fédérés via l’authentification unique transparente [Authentification unique transparente d’Azure Active Directory](https://aka.ms/hybrid/sso).
- L’inscription des appareils Windows de bas niveau **n’est pas** prise en charge pour les appareils utilisant des profils d’itinérance. Si vous vous appuyez sur l’itinérance de profils ou de paramètres, utilisez Windows 10.



## <a name="prerequisites"></a>Prérequis


Avant de commencer à activer des appareils hybrides joints à Azure AD dans votre organisation, vérifiez que :

- Vous avez une version à jour d’Azure AD Connect.

- Azure AD Connect a synchronisé les objets ordinateurs des appareils qui deviendront hybrides et joints à Azure AD. Si les objets ordinateurs appartiennent à des unités d’organisation (UO), celles-ci doivent être également configurées du point de vue de la synchronisation dans Azure AD Connect.

  

Azure AD Connect :

- Conserve l’association entre le compte d’ordinateur dans votre service Active Directory (AD) local et l’objet appareil dans Azure AD. 
- Permet d’utiliser d’autres fonctionnalités liées aux appareils telles que Windows Hello Entreprise.

Assurez-vous que les URL suivantes sont accessibles à partir d’ordinateurs au sein du réseau de votre organisation pour l’inscription d’ordinateurs à Azure AD :

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com

- https://device.login.microsoftonline.com

Si votre organisation requiert un accès à Internet via un proxy sortant, elle doit implémenter la détection automatique de proxy web (WPAD) pour permettre aux ordinateurs Windows 10 de s’inscrire à Azure AD.


## <a name="configuration-steps"></a>Configuration

Vous pouvez configurer des appareils hybrides joints à Azure AD pour différents types de plateformes d’appareils Windows. Cet article inclut les étapes requises pour tous les scénarios de configuration classiques.  

Pour obtenir une vue d’ensemble des étapes requises par votre scénario, utilisez le tableau ci-après :  



| Étapes                                      | Appareils Windows actuels et synchronisation du hachage de mot de passe | Appareils Windows actuels et fédération | Appareils Windows de bas niveau |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Étape 1 : Configuration du point de connexion de service | ![Vérification][1]                            | ![Vérification][1]                    | ![Vérification][1]        |
| Étape 2 : Configuration de l’émission de revendications           |                                        | ![Vérification][1]                    | ![Vérification][1]        |
| Étape 3 : Activation d’appareils non-Windows 10      |                                        |                                | ![Vérification][1]        |
| Étape 4 : Contrôle du déploiement et du lancement     | ![Vérification][1]                            | ![Vérification][1]                    | ![Vérification][1]        |
| Étape 5 : Vérification des appareils joints          | ![Vérification][1]                            | ![Vérification][1]                    | ![Vérification][1]        |



## <a name="step-1-configure-service-connection-point"></a>Étape 1 : Configuration du point de connexion de service

Vos appareils utilisent l’objet point de connexion de service (SCP) lors de l’inscription pour détecter les informations de client Azure AD. Dans votre service Active Directory (AD) local, l’objet SCP des appareils hybrides joints à Azure AD doit exister dans la partition de contexte d’appellation de configuration de la forêt de l’ordinateur. Il n’existe qu’un seul contexte d’appellation de configuration par forêt. Dans une configuration Active Directory à forêts multiples, le point de connexion de service doit exister dans toutes les forêts qui contiennent des ordinateurs joints à un domaine.

Pour récupérer le contexte d’appellation de configuration de votre forêt, vous pouvez utiliser l’applet de commande [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx).  

Pour une forêt avec le nom de domaine Active Directory *fabrikam.com*, le contexte d’appellation de configuration est le suivant :

`CN=Configuration,DC=fabrikam,DC=com`

Dans votre forêt, l’objet SCP pour l’inscription automatique des appareils joints à un domaine se trouve à l’emplacement suivant :  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Selon la façon dont vous avez déployé Azure AD Connect, il est possible que l’objet SCP ait déjà été configuré.
Vous pouvez vérifier l’existence de l’objet et récupérer les valeurs de détection à l’aide du script Windows PowerShell suivant : 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

La sortie de **$scp.Keywords** présente les informations de client Azure AD, par exemple :

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Si le point de connexion de service n’existe pas, vous pouvez le créer en exécutant l’applet de commande `Initialize-ADSyncDomainJoinedComputerSync` sur votre serveur Azure AD Connect. Des informations d’identification d’administrateur d’entreprise sont requises pour exécuter cette applet de commande.  
Cette applet de commande :

- Crée le point de connexion de service dans la forêt Active Directory à laquelle Azure AD Connect est connecté. 
- Vous demande de spécifier le paramètre `AdConnectorAccount`. Il s’agit du compte configuré en tant que compte de connecteur Active Directory dans Azure AD Connect. 


Le script ci-après présente un exemple d’utilisation de l’applet de commande. Dans ce script, la chaîne `$aadAdminCred = Get-Credential` exige que vous tapiez un nom d’utilisateur. Vous devez indiquer le nom d’utilisateur au format de nom d’utilisateur principal (UPN) (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

L’applet de commande `Initialize-ADSyncDomainJoinedComputerSync` :

- utilise le module Active Directory PowerShell et les outils AD DS, qui s’appuient sur les services Web Active Directory s’exécutant sur un contrôleur de domaine. Les services Web Active Directory sont pris en charge sur les contrôleurs de domaine exécutant Windows Server 2008 R2 et les versions ultérieures.
- Est pris en charge seulement par le **module MSOnline PowerShell version 1.1.166.0**. Pour télécharger ce module, utilisez ce [lien](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Si les outils AD DS ne sont pas installés, l’opération `Initialize-ADSyncDomainJoinedComputerSync` échoue.  Les outils AD DS peuvent être installés via Gestionnaire de serveur, sous Fonctionnalités - Outils d'administration de serveur distant - Outils d’administration de rôles.

Pour les contrôleurs de domaine exécutant Windows Server 2008 ou des versions antérieures, utilisez le script ci-après pour créer le point de connexion de service.

Dans une configuration à forêts multiples, vous devez utiliser le script ci-dessous pour créer le point de connexion de service dans chacune des forêts contenant des ordinateurs :
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your AD configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC

    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()


## <a name="step-2-setup-issuance-of-claims"></a>Étape 2 : Configuration de l’émission de revendications

Dans une configuration Azure AD fédérée, les appareils s’appuient sur les services de fédération Active Directory (AD FS) ou sur un service de fédération local tiers pour s’authentifier auprès d’Azure AD. Les appareils s’authentifient pour obtenir un jeton d’accès afin de s’inscrire auprès du service Azure Active Directory Device Registration Service (Azure DRS).

Les appareils Windows actuels s’authentifient à l’aide de l’authentification Windows intégrée auprès d’un point de terminaison WS-Trust actif (version 1.3 ou 2005) hébergé par le service de fédération local.

> [!NOTE]
> En cas d’utilisation d’AD FS, il est nécessaire d’activer **adfs/services/trust/13/windowstransport** ou **adfs/services/trust/2005/windowstransport**. Si vous utilisez le proxy d’authentification web, vérifiez également que ce point de terminaison est publié via le proxy. Vous pouvez visualiser les points de terminaison qui sont activés par le biais de la console de gestion AD FS sous **Service > Points de terminaison**.
>
>Si vous n’utilisez pas AD FS en tant que service de fédération local, suivez les instructions de votre fournisseur pour vous assurer que celui-ci prend en charge les points de terminaison WS-Trust 1.3 ou 2005 et que ces derniers sont publiés par le biais du fichier Metadata Exchange (MEX).

Pour que l’inscription d’appareils puisse s’effectuer, les revendications ci-après doivent exister dans le jeton reçu par Azure DRS. Azure DRS crée un objet appareil dans Azure AD avec certaines de ces informations, qu’Azure AD Connect utilise ensuite pour associer l’objet appareil nouvellement créé au compte d’ordinateur local.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Si vous disposez de plusieurs noms de domaine vérifiés, vous devez fournir la revendication ci-après pour les ordinateurs :

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Si vous émettez déjà une revendication ImmutableID (par exemple, un ID de connexion alternatif), vous devez fournir une seule revendication correspondante pour les ordinateurs :

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

Dans les sections ci-après, vous trouverez des informations concernant :
 
- Les valeurs requises pour chaque revendication
- L’aspect d’une définition dans AD FS

La définition vous permet de vérifier si les valeurs sont présentes ou si vous devez les créer.

> [!NOTE]
> Si vous n’utilisez pas AD FS pour votre serveur de fédération local, suivez les instructions de votre fournisseur afin de créer la configuration appropriée pour l’émission de ces revendications.

### <a name="issue-account-type-claim"></a>Émission de la revendication du type de compte

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** : cette revendication doit contenir la valeur **DJ**, qui identifie l’appareil en tant qu’ordinateur joint à un domaine. Dans AD FS, vous pouvez ajouter une règle de transformation d’émission ressemblant à ceci :

    @RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Émission de la valeur ObjectGUID du compte d’ordinateur local

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** : cette revendication doit contenir la valeur **objectGUID** du compte d’ordinateur local. Dans AD FS, vous pouvez ajouter une règle de transformation d’émission ressemblant à ceci :

    @RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Émission de la valeur objectSID du compte d’ordinateur local

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** : cette revendication doit contenir la valeur **objectSid** du compte d’ordinateur local. Dans AD FS, vous pouvez ajouter une règle de transformation d’émission ressemblant à ceci :

    @RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Émission de la valeur issuerID pour l’ordinateur s’il existe plusieurs noms de domaine vérifiés dans Azure AD

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** : cette revendication doit contenir l’URI (Uniform Resource Identifier) des noms de domaine vérifiés qui se connectent avec le service de fédération local (AD FS ou tiers) émettant le jeton. Dans AD FS, vous pouvez ajouter des règles de transformation d’émission qui ressemblent à celles ci-dessous dans l’ordre indiqué après les règles mentionnées ci-dessus. Notez qu’il doit exister une règle régissant l’émission explicite de la règle pour les utilisateurs. Dans les règles ci-dessous, une première règle est ajoutée afin d’identifier l’authentification d’un utilisateur plutôt que d’un ordinateur.

    @RuleName = "Issue account type with the value User when its not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://<verified-domain-name>/adfs/services/trust/"
    );


Dans la revendication ci-dessus,

- `<verified-domain-name>` est un espace réservé que vous devez remplacer par l’un de vos noms de domaine vérifiés dans Azure AD. Par exemple, Value = "http://contoso.com/adfs/services/trust/".



Pour plus d’informations sur la vérification du domaine, consultez [Ajouter un nom de domaine personnalisé à Azure Active Directory](active-directory-domains-add-azure-portal.md).  
Pour obtenir une liste de vos domaines d’entreprise vérifiés, vous pouvez utiliser l’applet de commande [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0). 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Émission de la valeur ImmutableID pour l’ordinateur s’il en existe une pour les utilisateurs (par exemple, définition d’un ID de connexion alternatif)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** : cette revendication doit contenir une valeur valide pour les ordinateurs. Dans AD FS, vous pouvez créer une règle de transformation d’émission comme suit :

    @RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Script d’assistance pour la création des règles de transformation d’émission AD FS

Le script ci-après vous aide à créer les règles de transformation d’émission décrites ci-dessus.

    $multipleVerifiedDomainNames = $false
    $immutableIDAlreadyIssuedforUsers = $false
    $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains
    
    $rule1 = '@RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );'

    $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'

    $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);'

    $rule4 = ''
    if ($multipleVerifiedDomainNames -eq $true) {
    $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
    );'
    }

    $rule5 = ''
    if ($immutableIDAlreadyIssuedforUsers -eq $true) {
    $rule5 = '@RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'
    }

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

### <a name="remarks"></a>Remarques 

- Ce script ajoute les règles aux règles existantes. N’exécutez pas le script à deux reprises, car l’ensemble de règles serait alors ajouté deux fois. Avant de réexécuter le script, assurez-vous qu’il n’existe aucune règle correspondante pour ces revendications (sous les conditions associées).

- Si vous disposez de plusieurs noms de domaine vérifiés (comme indiqué dans le portail Azure AD ou par le biais de l’applet de commande Get-MsolDomains), définissez l’élément **$multipleVerifiedDomainNames** du script sur la valeur **$true**. Veillez également à supprimer toute revendication issuerid existante pouvant avoir été créée par Azure AD Connect ou par d’autres moyens. Voici un exemple de cette règle :


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Si vous avez déjà émis une revendication **ImmutableID** pour les comptes d’utilisateurs, définissez l’élément **$immutableIDAlreadyIssuedforUsers** du script sur la valeur **$true**.

## <a name="step-3-enable-windows-down-level-devices"></a>Étape 3 : Activation des appareils Windows de bas niveau

Si certains de vos appareils joints à un domaine sont des appareils Windows de bas niveau, vous devez :

- Définir une stratégie dans Azure AD pour permettre aux utilisateurs d’inscrire des appareils
 
- Configurer votre service de fédération local pour l’émission de revendications afin de prendre en charge **l’authentification Windows intégrée (IWA)** pour l’inscription des appareils
 
- Ajouter le point de terminaison d’authentification d’appareil Azure AD aux zones Intranet local afin d’éviter les invites de certificat lors de l’authentification des appareils

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Définir une stratégie dans Azure AD pour permettre aux utilisateurs d’inscrire des appareils

Pour inscrire des appareils Windows de bas niveau, vous devez vous assurer que le paramètre permettant aux utilisateurs d’inscrire des appareils dans Azure AD est défini. Dans le Portail Azure, ce paramètre est disponible à l’emplacement suivant :

`Azure Active Directory > Users and groups > Device settings`
    
La stratégie ci-après doit être définie sur la valeur **Tous** : **Les utilisateurs peuvent inscrire leurs appareils sur Azure AD**.

![Inscrire des appareils](./media/active-directory-conditional-access-automatic-device-registration-setup/23.png)


### <a name="configure-on-premises-federation-service"></a>Configurer le service de fédération local 

Votre service de fédération local doit prendre en charge l’émission des revendications **authenticationmehod** et **wiaormultiauthn** lors de la réception d’une demande d’authentification auprès de la partie de confiance Azure AD qui contient un paramètre resouce_params avec une valeur encodée comme indiqué ci-dessous :

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Lorsqu’une telle demande est reçue, le service de fédération local doit authentifier l’utilisateur à l’aide de l’authentification Windows intégrée et, en cas de réussite, doit émettre les deux revendications suivantes :

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

Dans AD FS, vous devez ajouter une règle de transformation d’émission qui est transmise directement par le biais de la méthode d’authentification.  

**Pour ajouter cette règle :**

1. Dans la console de gestion AD FS, accédez à `AD FS > Trust Relationships > Relying Party Trusts`.
2. Cliquez avec le bouton droit sur l’objet d’approbation de partie de confiance de la plateforme d’identité Microsoft Office 365 et sélectionnez **Modifier les règles de revendication**.
3. Sous l’onglet **Règles de transformation d’émission**, sélectionnez **Ajouter une règle**.
4. Sélectionnez **Envoyer les revendications en utilisant une règle personnalisée** dans la liste de modèles **Règle de revendication**.
5. Sélectionnez **Suivant**.
6. Dans la zone **Nom de la règle de revendication**, tapez **Règle de revendication de méthode d’authentification**.
7. Dans la zone **Règle de revendication**, tapez la règle suivante :

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. Sur votre serveur de fédération, tapez la commande PowerShell ci-dessous après avoir remplacé **\<RPObjectName\>** par le nom d’objet de partie de confiance de votre objet Approbation de partie de confiance Azure AD. Cet objet est généralement nommé **plateforme d’identité Microsoft Office 365**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Ajouter le point de terminaison d’authentification d’appareil Azure AD aux zones Intranet local

Pour éviter les invites de certificat lorsque les utilisateurs des appareils inscrits s’authentifient auprès d’Azure AD, vous pouvez transmettre une stratégie à vos appareils joints à un domaine pour ajouter l’URL ci-après à la zone Intranet local dans Internet Explorer :

`https://device.login.microsoftonline.com`

## <a name="step-4-control-deployment-and-rollout"></a>Étape 4 : Contrôle du déploiement et du lancement

Une fois que vous avez exécuté les étapes requises, les appareils joints à un domaine sont prêts à joindre automatiquement Azure AD :

- Tous les appareils joints à un domaine qui exécutent Mise à jour anniversaire Windows 10 et Windows Server 2016 s’inscrivent automatiquement auprès d’Azure AD lors du redémarrage des appareils ou de la connexion des utilisateurs. 

- Les nouveaux appareils s’inscrivent auprès d’Azure AD au moment de leur redémarrage une fois l’opération de jonction de domaine effectuée.

- Les appareils qui ont fait précédemment l’objet d’une inscription auprès d’Azure AD (par exemple pour Intune) passent à l’état *« Joint au domaine, Enregistré avec AAD »*. Cependant, un certain temps est nécessaire pour que ce processus soit effectué sur tous les appareils, en raison du flux normal de l’activité du domaine et des utilisateurs.

### <a name="remarks"></a>Remarques

- Vous pouvez utiliser un objet de stratégie de groupe pour contrôler le déploiement de l’inscription automatique des ordinateurs Windows 10 et Windows Server 2016 joints à un domaine. **Si vous ne souhaitez pas que ces appareils soient automatiquement inscrits auprès d’Azure AD ou si vous souhaitez contrôler leur inscription**, vous devez déployer la stratégie de groupe. Pour cela, désactivez l’inscription automatique pour tous les appareils, puis effectuez les étapes de configuration. Une fois la configuration terminée, et lorsque vous êtes prêt à effectuer les tests, vous devez déployer la stratégie de groupe en activant l’inscription automatique uniquement pour les appareils de test, puis pour les appareils de votre choix.

- La mise à jour de novembre 2015 de Windows 10 effectue automatiquement la jonction à Azure AD **uniquement** si l’objet de stratégie de groupe de lancement est défini.

- Pour un lancement des ordinateurs Windows de bas niveau, vous pouvez déployer un [package Windows Installer](#windows-installer-packages-for-non-windows-10-computers) sur les ordinateurs que vous sélectionnez.

- Si vous transmettez l’objet de stratégie de groupe à des appareils Windows 8.1 joints à un domaine, une tentative de jonction sera effectuée ; toutefois, il est recommandé d’utiliser le [package Windows Installer](#windows-installer-packages-for-non-windows-10-computers) pour joindre tous vos appareils Windows de bas niveau. 

### <a name="create-a-group-policy-object"></a>Créer un objet de stratégie de groupe 

Pour contrôler le lancement des ordinateurs Windows actuels, vous devez déployer l’objet de stratégie de groupe **Enregistrer les ordinateurs appartenant à un domaine en tant qu’appareils** sur les appareils que vous souhaitez inscrire. Par exemple, vous pouvez déployer la stratégie sur un groupe de sécurité ou sur une unité organisationnelle.

**Pour configurer la stratégie :**

1. Ouvrez **Gestionnaire de serveur**, puis accédez à `Tools > Group Policy Management`.
2. Accédez au nœud de domaine qui correspond au domaine dans lequel vous souhaitez activer l’inscription automatique d’ordinateurs Windows actuels.
3. Cliquez avec le bouton droit sur **Objets de stratégie de groupe**, puis sélectionnez **Nouveau**.
4. Entrez un nom pour votre objet de stratégie de groupe. Par exemple, *jonction Azure AD hybride. 
5. Cliquez sur **OK**.
6. Cliquez avec le bouton droit sur votre nouvel objet de stratégie de groupe, puis sélectionnez **Modifier**.
7. Accédez à **Configuration ordinateur** > **Stratégies** > **Modèles d’administration** > **Composants Windows** > **Enregistrement d’appareil**. 
8. Cliquez avec le bouton droit sur **Enregistrer les ordinateurs appartenant à un domaine en tant qu’appareils**, puis sélectionnez **Modifier**.
   
   > [!NOTE]
   > Ce modèle de stratégie de groupe a été renommé par rapport aux versions précédentes de la Console de gestion des stratégies de groupe. Si vous utilisez une version antérieure de la console, accédez à `Computer Configuration > Policies > Administrative Templates > Windows Components > Workplace Join > Automatically workplace join client computers`. 

7. Sélectionnez **Activé**, puis cliquez sur **Appliquer**. Vous devez sélectionner **Désactivé** si vous souhaitez que la stratégie bloque l’inscription automatique auprès d’Azure AD des appareils contrôlés par cette stratégie de groupe.

8. Cliquez sur **OK**.
9. Liez l’objet de stratégie de groupe à un emplacement de votre choix. Par exemple, vous pouvez le lier à une unité organisationnelle spécifique. Vous pouvez également le lier à un groupe de sécurité spécifique d’ordinateurs qui rejoignent automatiquement Azure AD. Pour définir cette stratégie pour tous les ordinateurs Windows 10 ou Windows Server 2016 joints à un domaine de votre organisation, liez l’objet de stratégie de groupe au domaine.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Packages Windows Installer pour les ordinateurs autres que Windows 10

Pour joindre des ordinateurs Windows de bas niveau joints à un domaine dans un environnement fédéré, vous pouvez télécharger et installer ce package Windows Installer (.msi) à partir du Centre de téléchargement au niveau de la page [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/en-us/download/details.aspx?id=53554) (Jonction Microsoft Workplace pour les ordinateurs non Windows 10).

Vous pouvez déployer le package à l’aide d’un système de distribution de logiciels comme System Center Configuration Manager. Le package prend en charge les options d’installation en mode silencieux standard avec le paramètre *quiet*. System Center Configuration Manager Current Branch offre des avantages supplémentaires par rapport aux versions précédentes, comme la possibilité d’effectuer le suivi des inscriptions terminées. Pour plus d’informations, consultez l’article [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager).

Le programme d’installation crée une tâche planifiée sur le système, qui s’exécute dans le contexte de l’utilisateur. La tâche est déclenchée lorsque l’utilisateur se connecte à Windows. La tâche assure la jonction de l’appareil en mode silencieux à Azure AD avec les informations d’identification de l’utilisateur après l’avoir authentifié à l’aide de l’authentification Windows intégrée. Pour visualiser la tâche planifiée, sélectionnez sur l’appareil **Microsoft** > **Rattacher à l’espace de travail**, puis accédez à la bibliothèque du Planificateur de tâches.

## <a name="step-5-verify-joined-devices"></a>Étape 5 : Vérification des appareils joints

Vous pouvez vérifier les appareils qui ont été correctement joints dans votre organisation en utilisant l’applet de commande [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) dans le [module Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

La sortie de cette applet de commande affiche les appareils qui sont enregistrés et joints à Azure AD. Pour obtenir tous les appareils, utilisez le paramètre **-All**, puis filtrez-les à l’aide de la propriété **deviceTrustType**. Les appareils joints à un domaine présentent la valeur **Joint au domaine**.

## <a name="next-steps"></a>Étapes suivantes

* [Présentation de la gestion des appareils dans Azure Active Directory](device-management-introduction.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
