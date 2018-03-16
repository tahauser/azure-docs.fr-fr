---
title: Installation sans assistance du connecteur Azure AD App Proxy | Documents Microsoft
description: "Explique comment effectuer une installation silencieuse du connecteur du Proxy d’application Azure AD pour offrir un accès à distance sécurisé à vos applications locales."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: dbda2448c5bf2b6998d120a082e036d7e0125a1f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Créer un script d’installation sans assistance pour le connecteur de proxy d’application Azure AD

Cette rubrique vous aide à créer un script Windows PowerShell qui active l’installation sans assistance et l’inscription de votre connecteur de proxy d’application Azure AD.

Cette fonctionnalité est utile lorsque vous souhaitez :

* Installer le connecteur sur des serveurs Windows sur lesquels aucune interface utilisateur n’est activée ou auxquels vous ne pouvez pas accéder avec le Bureau à distance
* Installer et inscrire beaucoup de connecteurs à la fois.
* Intégrer l’installation et l’inscription du connecteur dans le cadre d’une autre procédure.
* Créer une image de serveur standard qui contient les exécutables du connecteur, mais qui n’est pas inscrite.

Pour que le [connecteur de proxy d’application](application-proxy-understand-connectors.md) puisse fonctionner, il doit être inscrit auprès de votre annuaire Azure AD à l’aide d’un identifiant d’administrateur global et d’un mot de passe. En général, ces informations sont saisies lors de l’installation du connecteur dans une boîte de dialogue contextuelle, mais vous pouvez utiliser PowerShell pour automatiser ce processus.

Une installation sans assistance comporte deux étapes. Tout d’abord, vous devez installer le connecteur. Ensuite, vous devez inscrire le connecteur auprès d’Azure Active Directory. 

## <a name="install-the-connector"></a>Installation du connecteur
Effectuez les étapes suivantes pour installer le connecteur sans l’inscrire :

1. Ouvrez une invite de commandes.
2. Exécutez la commande suivante, dans laquelle /q signifie « installation silencieuse ». Une installation silencieuse ne vous invite pas à accepter le Contrat de Licence Utilisateur Final.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Inscription du connecteur sur Azure Active Directory
Deux méthodes permettent d’inscrire le connecteur :

* Inscription du connecteur à l’aide d’un objet d’informations d’identification Windows PowerShell
* Inscription du connecteur à l’aide d’un jeton créé hors connexion

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Inscription du connecteur à l’aide d’un objet d’informations d’identification Windows PowerShell
1. Créez un objet d’informations d’identification Windows PowerShell `$cred` qui contient un nom d’utilisateur et un mot de passe d’administration pour votre annuaire. Exécutez la commande suivante, en remplaçant *\<username\>* et *\<password\>* :
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Accédez à **C:\Program Files\Microsoft AAD App Proxy Connector** et exécutez le script suivant en utilisant l’objet `$cred` que vous avez créé :
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Inscription du connecteur à l’aide d’un jeton créé hors connexion
1. Créez un jeton hors connexion à l’aide de la classe AuthenticationContext, en utilisant les valeurs de cet extrait de code :

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }


2. Une fois que vous disposez du jeton, créez une chaîne SecureString en utilisant le jeton :

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Exécuter la commande Windows PowerShell suivante, en remplaçant \<locataire GUID\> avec votre ID de répertoire :

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Étapes suivantes 
* [Publier des applications avec votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
* [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
* [Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)


