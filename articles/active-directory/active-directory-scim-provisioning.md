---
title: Automatiser l’approvisionnement des applications avec SCIM dans Azure Active Directory | Microsoft Docs
description: Azure Active Directory peut configurer automatiquement les utilisateurs et les groupes sur une application ou un magasin d’identités avec en façade un service web avec l’interface définie dans Spécification du protocole SCIM.
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: mtillman
editor: ''
ms.assetid: 4d86f3dc-e2d3-4bde-81a3-4a0e092551c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.openlocfilehash: 3b7f2f104046313e7d60cea4ef296f265d204aec
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="using-system-for-cross-domain-identity-management-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Utilisation du protocole SCIM (System for Cross-Domain Identity Management) pour configurer automatiquement des utilisateurs et groupes d’Azure Active Directory dans des applications

## <a name="overview"></a>Vue d'ensemble
Azure Active Directory (Azure AD) peut affecter automatiquement des utilisateurs et des groupes à une application ou un magasin d’identités avec en façade un service Web avec l’interface définie dans la [Spécification du protocole SCIM (System for Cross-Domain Identity Management) 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). Azure Active Directory peut envoyer des requêtes pour créer, modifier ou supprimer des utilisateurs et groupes attribués au service web. Le service web peut ensuite traduire ces requêtes en opérations sur le magasin d’identités cible. 

![][0]
*Figure 1 : approvisionnement d’Azure Active Directory à un magasin d’identités par le biais d’un service web*

Cette fonctionnalité peut être utilisée conjointement avec la fonctionnalité « apportez votre propre application » dans Azure AD afin d’activer l’attribution d’utilisateurs à authentification unique et automatique pour des applications qui fournissent ou ont comme façade un service web SCIM.

Il existe deux cas d’utilisation de SCIM dans Azure Active Directory :

* **Approvisionnement d’utilisateurs et de groupes pour les applications qui prennent en charge SCIM** : les applications qui prennent en charge SCIM 2.0 et utilisent les jetons de porteur OAuth d’Azure AD pour l’authentification fonctionnent avec Azure AD sans configuration préalable.
* **Créer votre propre solution d’approvisionnement pour les applications qui prennent en charge d’autres approvisionnements basés sur une API** : pour les applications autres que SCIM, vous pouvez créer un point de terminaison SCIM pour la translation entre le point de terminaison SCIM d’Azure AD et l’API prise en charge par l’application pour l’approvisionnement des utilisateurs. Pour vous aider à développer un point de terminaison SCIM, nous fournissons des bibliothèques CLI (Common Language Infrastructure), ainsi que des exemples de code qui vous indiquent comment fournir un point de terminaison SCIM et convertir les messages SCIM.  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Approvisionnement d’utilisateurs et de groupes pour les applications qui prennent en charge SCIM
Azure AD peut être configuré pour attribuer automatiquement des utilisateurs et des groupes aux applications qui implémentent un service web [SCIM 2 (System for Cross-domain Identity Management)](https://tools.ietf.org/html/draft-ietf-scim-api-19) et acceptent des jetons de porteur OAuth pour l’authentification. Dans la spécification SCIM 2.0, applications doivent satisfaire aux conditions suivantes :

* Prend en charge la création d'utilisateurs et/ou de groupes, conformément à la section 3.3 du protocole SCIM.  
* Prend en charge la modification d'utilisateurs et/ou de groupes avec des demandes de correctifs, conformément à la section 3.5.2 du protocole SCIM.  
* Prend en charge la récupération d'une ressource connue, conformément à la section 3.4.1 du protocole SCIM.  
* Prend en charge l'exécution de requêtes d'utilisateurs et/ou de groupes, conformément à la section 3.4.2 du protocole SCIM.  Par défaut, les utilisateurs sont interrogés par externalId et les groupes sont interrogés par displayName.  
* Prend en charge l'exécution de requêtes d'utilisateurs par ID et par gestion, conformément à la section 3.4.2 du protocole SCIM.  
* Prend en charge l'exécution de requêtes de groupes par ID et par membre, conformément à la section 3.4.2 du protocole SCIM.  
* Accepte les jetons de porteur OAuth pour l'autorisation, conformément à la section 2.1 du protocole SCIM.

Vérifiez avec votre fournisseur d’application ou dans la documentation du fournisseur de votre application la conformité à ces exigences.

### <a name="getting-started"></a>Prise en main
Les applications qui prennent en charge le profil SCIM décrit dans cet article peuvent être connectées à Azure Active Directory à l’aide de la fonctionnalité « application ne figurant pas dans la galerie » dans la galerie d’applications Azure AD. Une fois connecté, Azure AD exécute toutes les 20 minutes un processus de synchronisation au cours duquel il interroge le point de terminaison de SCIM de l’application pour les utilisateurs et les groupes assignés, et les crée ou les modifie en fonction des détails de l’attribution.

**Pour connecter une application qui prend en charge SCIM :**

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
2. Accédez à **Azure Active Directory > Applications d’entreprise, puis sélectionnez **Nouvelle application > Toutes > Application ne figurant pas dans la galerie**.
3. Entrez un nom pour votre application, puis cliquez sur l’icône **Ajouter** pour créer un objet d’application.
    
  ![][1]
  *Figure 2 : galerie d’applications Azure AD*
    
4. Sur l’écran qui apparaît, sélectionnez l’onglet **Approvisionnement** dans la colonne de gauche.
5. Dans le menu **Mode d’approvisionnement**, sélectionnez **Automatique**.
    
  ![][2]
  *Figure 3 : configuration de l’approvisionnement dans le portail Azure*
    
6. Dans le champ **URL du locataire**, entrez l’URL du point de terminaison SCIM de l’application. Par exemple : https://api.contoso.com/scim/v2/
7. Si le point de terminaison SCIM requiert un jeton de porteur OAuth d’un émetteur autre qu’Azure AD, copiez le jeton de porteur OAuth requis dans le champ facultatif **Secret Token** (Jeton secret). Si ce champ est laissé vide, Azure AD inclut un jeton de porteur OAuth émis par Azure AD avec chaque requête. Les applications qui utilisent Azure AD comme fournisseur d’identité peuvent valider ce jeton émis par Azure AD.
8. Cliquez sur le bouton **Tester la connexion** pour qu’Azure Active Directory tente de se connecter au point de terminaison SCIM. Si les tentatives échouent, des informations d’erreur s’affichent.  
9. Si la tentative de connexion à l’application réussit, cliquez sur **Enregistrer** pour enregistrer les informations d’identification d’administrateur.
10. Dans la section **Mappages**, il existe deux ensembles sélectionnables de mappages d’attributs : un pour les objets utilisateur et un pour les objets de groupe. Sélectionnez chacun d’eux pour consulter les attributs qui sont synchronisés entre Azure Active Directory et votre application. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les utilisateurs et les groupes dans votre application pour les opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

    >[!NOTE]
    >Vous pouvez éventuellement désactiver la synchronisation des objets de groupe en désactivant le mappage « Groupes ». 

11. Sous **Paramètres**, le champ **Étendue** définit les utilisateurs et/ou les groupes qui sont synchronisés. Si vous sélectionnez « Sync only assigned users and groups » (Synchroniser uniquement les utilisateurs et les groupes attribués) (recommandé), seuls les utilisateurs et les groupes attribués seront synchronisés dans l’onglet **Utilisateurs et groupes**.
12. Une fois votre configuration terminée, modifiez l’**état d’approvisionnement** en **Activé**.
13. Cliquez sur **Enregistrer** pour démarrer le service d’approvisionnement Azure AD. 
14. Si vous synchronisez uniquement les utilisateurs et les groupes attribués (recommandé), veillez à sélectionner l’onglet **Utilisateurs et groupes** et à attribuer les utilisateurs et/ou groupes que vous souhaitez synchroniser.

Une fois la synchronisation initiale démarrée, vous pouvez utiliser l’onglet **Journaux d’audit** pour surveiller la progression. Il indique toutes les actions effectuées par le service d’approvisionnement de votre application. Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

>[!NOTE]
>La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent environ toutes les 20 minutes, tant que le service est en cours d’exécution. 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>Création de votre propre solution d’attribution pour n’importe quelle application
En créant un service Web SCIM doté d'une interface avec Azure Active Directory, vous pouvez activer l'authentification unique et l'attribution d'utilisateurs d'automatique pour pratiquement n'importe quelle application qui fournit une API d'attribution d'utilisateurs REST ou SOAP.

Fonctionnement :

1. Azure AD fournit une bibliothèque Common Language Infrastructure nommée [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Les développeurs et les intégrateurs système peuvent utiliser cette bibliothèque pour créer et déployer un point de terminaison de service web SCIM capable de connecter Azure AD au magasin d’identités d’application d’une application.
2. Les mappages sont mis en œuvre dans le service web pour mapper le schéma utilisateur normalisé et le protocole requis par l’application.
3. L’URL de point de terminaison est inscrite dans Azure AD dans le cadre d’une application personnalisée dans la galerie d’applications.
4. Les utilisateurs et les groupes sont affectés à cette application dans Azure AD. Lors de l’affectation, ils sont placés dans une file d’attente pour être synchronisés avec l’application cible. Le processus de gestion de la file d’attente qui gère la file d’attente toutes les 20 minutes.

### <a name="code-samples"></a>Exemples de code
Pour faciliter ce processus, un ensemble d' [exemples de code](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) qui crée un point de terminaison de service Web SCIM et illustre l'attribution automatique est fourni. Exemple : un fournisseur qui gère un fichier avec des lignes de valeurs séparées par des virgules représentant des utilisateurs et des groupes.  L’autre exemple est un fournisseur qui opère sur le service de gestion des identités et des accès des services web Amazon.  

**Composants requis**

* Visual Studio 2013 ou une version ultérieure
* [Kit de développement logiciel (SDK) Azure pour .NET](https://azure.microsoft.com/downloads/)
* Machine Windows qui prend en charge l’infrastructure ASP.NET 4.5 à utiliser comme point de terminaison SCIM. Cet ordinateur doit être accessible à partir du cloud
* [Dans un abonnement Azure avec une version d’évaluation ou sous licence d’Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* L'exemple d'Amazon AWS exige des bibliothèques du [kit d'outils AWS pour Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Pour plus d’informations, consultez le fichier README inclus dans l’exemple.

### <a name="getting-started"></a>Mise en route
Le moyen le plus simple d’implémenter un point de terminaison SCIM qui peut accepter des demandes de configuration d’Azure AD consiste à générer et déployer l’exemple de code qui génère les utilisateurs configurés dans un fichier de valeurs séparées par des virgules (CSV).

**Pour créer un exemple de point de terminaison SCIM :**

1. Téléchargez le package d'exemple de code à l’adresse [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Décompressez le package et placez-le sur votre machine Windows à un emplacement tel que C:\AzureAD-BYOA-Provisioning-Samples\.
3. Dans ce dossier, lancez la solution FileProvisioningAgent dans Visual Studio.
4. Sélectionnez **Outils > Gestionnaire de Package de bibliothèque > Console du Gestionnaire de Package**, puis exécutez les commandes suivantes afin que le projet FileProvisioningAgent résolve les références de la solution :
  ```` 
   Install-Package Microsoft.SystemForCrossDomainIdentityManagement
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   Install-Package Microsoft.Owin.Diagnostics
   Install-Package Microsoft.Owin.Host.SystemWeb
  ````
5. Générez le projet FileProvisioningAgent.
6. Lancez l'application d'invite de commandes de Windows (en tant qu'administrateur) et utilisez la commande **cd** pour modifier le répertoire en votre dossier **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug**.
7. Exécutez la commande suivante en remplaçant <adresse-ip> par l’adresse IP ou le nom de domaine de l’ordinateur Windows :
  ````   
   FileAgnt.exe http://<ip-address>:9000 TargetFile.csv
  ````
8. Dans Windows, sous **Paramètres Windows > Paramètres Internet & réseau**, sélectionnez **Pare-feu Windows > Paramètres avancés**, et créez une **règle de trafic entrant** qui autorise l’accès entrant au port 9000.
9. Si l’ordinateur Windows est placé derrière un routeur, ce dernier doit être configuré pour exécuter une traduction NAT entre son port 9000 présenté à Internet et le port 9000 sur l’ordinateur Windows. Cela est obligatoire si l’on veut qu’Azure AD puisse accéder à ce point de terminaison dans le cloud.

**Pour enregistrer le point de terminaison SCIM exemple dans Azure AD :**

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
2. Accédez à **Azure Active Directory > Applications d’entreprise, puis sélectionnez **Nouvelle application > Toutes > Application ne figurant pas dans la galerie**.
3. Entrez un nom pour votre application, puis cliquez sur l’icône **Ajouter** pour créer un objet d’application. L’objet d’application créé doit représenter l’application cible que vous devez configurer et sur laquelle mettre en œuvre l’authentification unique. Il ne s’agit pas d’un simple point de terminaison SCIM.
4. Sur l’écran qui apparaît, sélectionnez l’onglet **Approvisionnement** dans la colonne de gauche.
5. Dans le menu **Mode d’approvisionnement**, sélectionnez **Automatique**.
    
  ![][2]
  *Figure 4 : configuration de l’approvisionnement dans le portail Azure*
    
6. Dans le champ **URL du locataire**, saisissez l’URL côté Internet et le port de votre point de terminaison SCIM. Le résultat pourrait être http://testmachine.contoso.com:9000 ou http://<adresse-ip>:9000/, où <adresse-ip> est l’adresse IP Internet exposée.  
7. Si le point de terminaison SCIM requiert un jeton de porteur OAuth d’un émetteur autre qu’Azure AD, copiez le jeton de porteur OAuth requis dans le champ facultatif **Secret Token** (Jeton secret). Si ce champ est laissé vide, Azure AD inclura un jeton de porteur OAuth émis par Azure AD avec chaque requête. Les applications qui utilisent Azure AD comme fournisseur d’identité peuvent valider ce jeton émis par Azure AD.
8. Cliquez sur le bouton **Tester la connexion** pour qu’Azure Active Directory tente de se connecter au point de terminaison SCIM. Si les tentatives échouent, des informations d’erreur s’affichent.  
9. Si la tentative de connexion à l’application réussit, cliquez sur **Enregistrer** pour enregistrer les informations d’identification d’administrateur.
10. Dans la section **Mappages**, il existe deux ensembles sélectionnables de mappages d’attributs : un pour les objets utilisateur et un pour les objets de groupe. Sélectionnez chacun d’eux pour consulter les attributs qui sont synchronisés entre Azure Active Directory et votre application. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les utilisateurs et les groupes dans votre application pour les opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.
11. Sous **Paramètres**, le champ **Étendue** définit les utilisateurs et/ou les groupes qui sont synchronisés. Si vous sélectionnez « Sync only assigned users and groups » (Synchroniser uniquement les utilisateurs et les groupes attribués) (recommandé), seuls les utilisateurs et les groupes attribués seront synchronisés dans l’onglet **Utilisateurs et groupes**.
12. Une fois votre configuration terminée, modifiez l’**état d’approvisionnement** en **Activé**.
13. Cliquez sur **Enregistrer** pour démarrer le service d’approvisionnement Azure AD. 
14. Si vous synchronisez uniquement les utilisateurs et les groupes attribués (recommandé), veillez à sélectionner l’onglet **Utilisateurs et groupes** et à attribuer les utilisateurs et/ou groupes que vous souhaitez synchroniser.

Une fois la synchronisation initiale démarrée, vous pouvez utiliser l’onglet **Journaux d’audit** pour surveiller la progression. Il indique toutes les actions effectuées par le service d’approvisionnement de votre application. Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

La dernière étape de vérification de l’exemple consiste à ouvrir le fichier TargetFile.csv dans le dossier \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug sur votre machine Windows. Une fois le processus de déploiement exécuté, ce fichier affiche les détails de tous les utilisateurs et groupes affectés et configurés.

### <a name="development-libraries"></a>Bibliothèques de développement
Pour développer un service web conforme à la spécification SCIM, commencez par vous familiariser avec les bibliothèques suivantes fournies par Microsoft afin d’accélérer le processus de développement : 

1. Les bibliothèques CLI (Common Language Infrastructure) sont proposées pour une utilisation avec les langages basés sur cette infrastructure, notamment C#. Une de ces bibliothèques, [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), déclare une interface, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, indiquée dans l’illustration suivante : un développeur qui utilise les bibliothèques doit mettre en œuvre cette interface avec une classe pouvant être référencée de manière générale comme fournisseur. Les bibliothèques permettent au développeur de déployer un service web conforme à la spécification SCIM. Le service web peut être soit hébergé dans Internet Information Services, soit dans n’importe quel assembly Common Language Infrastructure exécutable. La requête est convertie en appels aux méthodes du fournisseur, qui doivent être programmées par le développeur pour fonctionner sur un magasin d’identités.
  
  ![][3]
  
2. [Des gestionnaires de routeur Express](http://expressjs.com/guide/routing.html) sont disponibles pour les objets de requête node.js d’analyse représentant des appels (comme défini par la spécification SCIM) envoyés à un service web node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Création d’un point de terminaison SCIM personnalisé
Grâce aux bibliothèques CLI, les développeurs utilisant ces bibliothèques peuvent héberger leurs services dans un assembly Common Language Infrastructure exécutable ou au sein des services Internet (IIS). Voici un exemple de code pour l'hébergement d'un service dans un assembly exécutable, à l'adresse http://localhost:9000 : 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Ce service doit avoir une adresse HTTP et un certificat d’authentification émanant du serveur dont l’autorité de certification racine est l’une des opérations suivantes : 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Un certificat d’authentification serveur peut être associé à un port sur un hôte Windows à l’aide de l’utilitaire d’environnement réseau : 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Ici, la valeur fournie pour l’argument certhash est l’empreinte numérique du certificat, tandis que la valeur fournie pour l’argument appid est un identificateur global unique arbitraire.  

Pour héberger le service dans Internet Information Services, un développeur crée un assembly de bibliothèque de code CLI avec une classe nommée Démarrage dans l’espace de noms par défaut de l’assembly.  Voici un exemple de classe de ce type : 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Gestion de l’authentification du point de terminaison
Les demandes d’Azure Active Directory incluent un jeton de support OAuth 2.0.   Tout service qui reçoit la demande doit authentifier l’émetteur comme étant Azure Active Directory pour le compte du client Azure Active Directory pour accéder au service web du graphique Azure Active Directory.  Dans le jeton, l’émetteur est identifié par une revendication iss, comme "iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  Dans cet exemple, l'adresse de base de la valeur de revendication, https://sts.windows.net, identifie Azure Active Directory en tant qu'émetteur, tandis que le segment d'adresse relative, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, est un identificateur unique du locataire Azure Active Directory au nom duquel le jeton a été émis.  Si le jeton a été émis pour accéder au service web du graphique Azure Active Directory, l’identificateur de ce service, 00000002-0000-0000-c000-000000000000, doit être dans la valeur de revendication du jeton AD.  

Les développeurs qui utilisent les bibliothèques CLI fournies par Microsoft pour la création d’un service SCIM peuvent authentifier les demandes d’Azure Active Directory à l’aide du package Microsoft.Owin.Security.ActiveDirectory en exécutant les opérations suivantes : 

1. Dans un fournisseur, implémentez la propriété Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior lui faisant retourner une méthode à appeler à chaque démarrage du service : 

  ````
    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }
  ````

2. Ajoutez le code suivant à cette méthode pour que toute demande à l’un des points de terminaison de service authentifié comme portant un jeton émis par Azure Active Directory pour le compte d’un client spécifié puisse accéder au service web du graphique d’Azure AD : 

  ````
    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }
  ````


## <a name="user-and-group-schema"></a>Schéma des utilisateurs et des groupes
Azure Active Directory peut configurer deux types de ressources pour les services web SCIM.  Ces types de ressources sont des utilisateurs et des groupes.  

Les ressources de l'utilisateur sont identifiées par l'identificateur de schéma urn:ietf:params:scim:schemas:extension:enterprise:2.0:User inclus dans cette spécification de protocole : http://tools.ietf.org/html/draft-ietf-scim-core-schema .  Le mappage par défaut des attributs utilisateurs dans Azure Active Directory sur les attributs des ressources urn:ietf:params:scim:schemas:extension:enterprise:2.0:User est fourni dans le tableau 1 ci-dessous.  

Les ressources du groupe sont identifiées par l’identificateur de schéma, http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Le tableau 2 ci-dessous montre le mappage par défaut des attributs de groupes dans Azure Active Directory sur les attributs de ressources http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  

### <a name="table-1-default-user-attribute-mapping"></a>Tableau 1 : Mappage d’attributs utilisateur par défaut
| Utilisateur Azure Active Directory | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User |
| --- | --- |
| IsSoftDeleted |active |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickName |externalId |
| manager |manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| objectId |id |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tableau 2 : Mappage d’attribut par défaut
| Groupe Azure Active Directory | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| displayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickName |displayName |
| membres |membres |
| objectId |id |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="user-provisioning-and-de-provisioning"></a>Approvisionnement et annulation d’approvisionnement utilisateur
L’illustration suivante contient les messages qu’Azure Active Directory envoie à un service SCIM pour gérer le cycle de vie d’un utilisateur dans un autre magasin d’identités. Le diagramme montre également comment un service SCIM implémenté à l’aide des bibliothèques CLI fournies par Microsoft traduit ces requêtes pour en faire des appels aux méthodes d’un fournisseur.  

![][4]
*Figure 5 : séquence d’approvisionnement et d’annulation de l’approvisionnement d’utilisateurs*

1. Azure Active Directory interroge le service pour trouver un utilisateur avec une valeur d’attribut externalId correspondant à la valeur d’attribut mailNickname d’un utilisateur dans Azure AD. La requête est exprimée dans le protocole HTTP (Hypertext Transfer Protocol) comme dans cet exemple, jyoung étant un exemple de mailNickname d’utilisateur dans Azure Active Directory : 
  ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
  ````
  Si le service a été créé à l’aide des bibliothèques Common Language Infrastructure fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel de méthode de requête du fournisseur de service.  Voici la signature de cette méthode : 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
  ````
  Voici la définition de l’interface Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters : 
  ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }

    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }
  ````
  Dans l’exemple suivant de requête pour un utilisateur avec une valeur d’attribut externalId donnée, les valeurs des arguments transmis à la méthode de requête sont : 
  * parameters.AlternateFilters.Count: 1
  * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
  * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
  * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. Si la réponse à une requête du service web pour un utilisateur avec une valeur d’attribut externalId correspondant à la valeur d’attribut mailNickname d’un utilisateur ne renvoie aucun utilisateur, Azure Active Directory demande que le service approvisionne un utilisateur correspondant à celui d’Azure Active Directory.  Voici un exemple de requête : 
  ````
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
  ````
  Les bibliothèques Common Language Infrastructure fournies par Microsoft pour l’implémentation de services SCIM transforment la requête en appel à la méthode de création du fournisseur du service.  Cette méthode de création a sa signature : 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
  ````
  Dans le cas d’une demande d’approvisionnement d’utilisateur, la valeur de l’argument de la ressource est une instance de Microsoft.SystemForCrossDomainIdentityManagement. La classe Core2EnterpriseUser, définie dans la bibliothèque Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Si la demande d’approvisionnement de l’utilisateur aboutit, l’implémentation de la méthode renvoie une instance de Microsoft.SystemForCrossDomainIdentityManagement. La classe Core2EnterpriseUser, avec la valeur de la propriété d’identificateur définie sur l’identificateur unique du nouvel utilisateur venant d’être approvisionné.  

3. Pour mettre à jour un utilisateur qui existe dans un magasin d’identités avec SCIM frontal, Azure Active Directory continue en demandant au service l’état actuel de cet utilisateur avec une requête de type : 
  ````
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Si le service a été créé à l’aide des bibliothèques Common Language Infrastructure fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel de méthode de récupération du fournisseur de service.  Voici la signature de la méthode de récupération : 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
  ````
  Dans le cas d’une requête servant à récupérer l’état actuel d’un utilisateur, les valeurs des propriétés de l’objet fourni comme valeur d’argument des paramètres sont les suivantes : 
  
  * Identificateur "54D382A4-2050-4C03-94D1-E769F1D15682"
  * SchemaIdentifier : "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. Si un attribut de référence doit être mis à jour, Azure Active Directory interroge le service pour déterminer si la valeur actuelle de l’attribut de référence dans le magasin d’identités avec le service frontal correspond ou non déjà à la valeur de cet attribut dans Azure Active Directory. Pour les utilisateurs, le seul attribut dont la valeur actuelle est interrogée de cette manière est l’attribut manager. Voici un exemple de requête visant à déterminer si l’attribut manager d’un objet utilisateur particulier a actuellement une certaine valeur : 
  ````
    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...
  ````
  La valeur du paramètre de requête d’attributs, id, signifie que si un objet utilisateur correspondant à l’expression fournie en tant que valeur du paramètre de requête de filtre existe, le service doit répondre par une ressource urn:ietf:params:scim:schemas:core:2.0:User ou une ressource urn:ietf:params:scim:schemas:extension:enterprise:2.0:User incluant uniquement la valeur de cet ID d’attribut de ressource.  La valeur de l’attribut **id** est connue du demandeur. Elle est incluse dans la valeur du paramètre de requête de filtre ; le but de la demande consiste à demander la représentation minimale d’une ressource satisfaisant à l’expression du filtre en tant qu’indication de l’existence ou la non-existence d’un tel objet.   

  Si le service a été créé à l’aide des bibliothèques Common Language Infrastructure fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel de méthode de requête du fournisseur de service. La valeur des propriétés de l’objet fourni en tant que valeur d’argument des paramètres est la suivante : 
  
  * parameters.AlternateFilters.Count: 2
  * parameters.AlternateFilters.ElementAt(x).AttributePath: "id"
  * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager
  * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
  * parameters.RequestedAttributePaths.ElementAt(0): "id"
  * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Ici, la valeur x de l’index peut être 0 et la valeur y de l’index peut être 1, ou la valeur x peut être 1 et la valeur y peut être 0, selon l’ordre des expressions de paramètre de requête du filtre.   

5. Voici un exemple de requête d’Azure Active Directory à un service SCIM pour mettre à jour un utilisateur : 
  ````
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
  ````
  Les bibliothèques Microsoft Common Language Infrastructure pour l’implémentation de services SCIM convertissent la requête en appel à la méthode de mise à jour du fournisseur du service. Voici la signature de la méthode de mise à jour : 
  ````
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }

    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }

      public string Value
      { get; set; }
    }
  ````
    Dans le cas d’une demande de mise à jour d’un utilisateur, l’objet fourni comme valeur d’argument de correctif a les valeurs de propriété suivantes : 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
  * (PatchRequest as PatchRequest2).Operations.Count: 1
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

6. Pour annuler l’approvisionnement d’un utilisateur à partir d’un magasin d’identités avec un service SCIM frontal, Azure AD envoie une demande similaire à celle-ci : 
  ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Si le service a été créé à l’aide des bibliothèques Common Language Infrastructure fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel de méthode Delete du fournisseur de service.   Cette méthode a sa signature : 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
  ````
  L’objet fourni comme valeur d’argument resourceIdentifier a ces valeurs de propriété dans le cas d’une demande d’annulation d’approvisionnement d’un utilisateur : 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="group-provisioning-and-de-provisioning"></a>Approvisionnement et annulation d’approvisionnement d’un groupe
L’illustration suivante contient les messages qu’Azure AD envoie à un service SCIM pour gérer le cycle de vie d’un groupe dans un autre magasin d’identités.  Ces messages diffèrent des messages appartenant aux utilisateurs de trois manières : 

* Le schéma d’une ressource de groupe est identifié ainsi : `http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group`.  
* Les demandes d’extraction de groupes précisent que l’attribut des membres doit être exclu des ressources fournies en réponse à la demande.  
* Les requêtes permettant de déterminer si un attribut de référence a une certaine valeur sont des requêtes sur les attributs membres.  

![][5]
*Figure 6 : séquence d’approvisionnement et d’annulation de l’approvisionnement d’un groupe*

## <a name="related-articles"></a>Articles connexes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Automatiser l’approvisionnement/le déprovisionnement des utilisateurs pour les applications SaaS](active-directory-saas-app-provisioning.md)
* [Personnalisation des mappages d’attributs pour l’approvisionnement des utilisateurs](active-directory-saas-customizing-attribute-mappings.md)
* [Écriture d’expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](active-directory-saas-scoping-filters.md)
* [Notifications d’approvisionnement de comptes](active-directory-saas-account-provisioning-notifications.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[0]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[1]: ./media/active-directory-scim-provisioning/scim-figure-2a.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2b.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
