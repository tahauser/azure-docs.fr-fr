---
title: "Bien démarrer avec Azure AD iOS | Microsoft Docs"
description: "Création d’une application iOS qui s’intègre à Azure AD pour la connexion et appelle des API protégées par Azure AD en utilisant OAuth."
services: active-directory
documentationcenter: ios
author: brandwe
manager: mtillman
editor: 
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/30/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: 93df6f5e6c966fdf4909a4c21ebc36bff4283a59
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-ad-ios-getting-started"></a>Bien démarrer avec Azure AD iOS
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Pour les clients iOS qui doivent accéder à des ressources protégées, Azure Active Directory (Azure AD) fournit la bibliothèque d’authentification Active Directory (ADAL). ADAL simplifie le processus utilisé par votre application pour obtenir des jetons d’accès. Pour illustrer sa facilité d’utilisation, nous allons créer, dans cet article, une application de liste de tâches Objective C qui effectue les actions suivantes :

* obtention de jetons d’accès pour appeler l’API Graph Azure AD à l’aide du [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) ;
* recherche, dans un répertoire, d’utilisateurs correspondant à un alias donné ;

Pour générer l’application fonctionnelle complète, vous devez :

1. inscrire votre application auprès d’Azure AD ;
2. installer et configurer la bibliothèque ADAL ;
3. utiliser la bibliothèque ADAL pour obtenir des jetons à partir d’Azure AD.

Pour commencer, téléchargez [la structure de l’application](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Vous avez également besoin d’un client Azure AD dans lequel vous pouvez créer des utilisateurs et inscrire une application. Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](active-directory-howto-tenant.md).


> [!TIP]
> Essayez la préversion de notre nouveau [portail des développeurs](https://identity.microsoft.com/Docs/iOS), qui vous permet de devenir opérationnel avec Azure AD en quelques minutes seulement. Le portail des développeurs vous guide tout au long du processus d’inscription d’une application et d’intégration d’Azure AD à votre code. Une fois que vous aurez terminé, vous disposerez d’une application simple capable d’authentifier les utilisateurs dans votre locataire et d’un serveur principal qui peut accepter des jetons et effectuer une validation. 
> 
> 

## <a name="1-determine-what-your-redirect-uri-is-for-ios"></a>1. Déterminer votre URI de redirection pour iOS
Pour démarrer vos applications en toute sécurité dans certains scénarios d’authentification unique (SSO), vous devez créer un *URI de redirection* dans un format particulier. Un URI de redirection permet de garantir que les jetons sont retournés vers la bonne application qui les a appelés.


Le format iOS d’un URI de redirection est le suivant :

```
<app-scheme>://<bundle-id>
```

* **aap-scheme** : il est enregistré dans votre projet XCode. Cela permet aux autres applications de vous appeler. Vous trouverez cela sous Info.plist -> Types d’URL -> Identificateur d’URL. Vous devez en créer une si vous n’en avez pas encore au moins une configurée.
* **bundle-id** : il s’agit de l’identificateur d’offre groupée se trouvant sous « identité » dans les paramètres de votre projet XCode.

Voici un exemple de code de démarrage rapide : ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2. Inscrire l’application DirectorySearcher
Pour configurer votre application afin d’obtenir des jetons, vous devez d’abord l’inscrire dans votre locataire Azure AD et lui accorder l’autorisation d’accéder à l’API Graph Azure AD :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans la barre supérieure, cliquez sur votre compte. Dans la liste **Répertoire**, choisissez le locataire Active Directory auprès duquel vous voulez inscrire votre application.
3. Cliquez sur **Tous les services** dans le volet de navigation le plus à gauche, puis sélectionnez **Azure Active Directory**.
4. Cliquez sur **Inscriptions des applications**, puis sélectionnez **Ajouter**.
5. Suivez les invites pour créer une **application cliente native**.
  * Le **nom** de l’application donne une description de votre application aux utilisateurs finaux.
  * L’**URI de redirection** est une combinaison de schémas et de chaînes qu’Azure AD utilise pour retourner des réponses de jeton.  Entrez une valeur spécifique à votre application et basée sur les informations d’URI de redirection précédentes.
6. Une fois que vous avez terminé l’inscription, Azure AD affecte un ID d’application unique à votre application.  Copiez cette valeur dans l’onglet de l’application, car vous en aurez besoin dans les sections suivantes.
7. Dans la page **Paramètres**, sélectionnez **Autorisations nécessaires**, puis **Ajouter**. Sélectionnez **Microsoft Graph** comme API, puis ajoutez l’autorisation **Lire les données de l’annuaire** sous **Autorisations déléguées**.  Cette opération configure votre application afin de pouvoir interroger l’API Azure AD Graph concernant les utilisateurs.

## <a name="3-install-and-configure-adal"></a>3. Installer et configurer la bibliothèque ADAL
Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer la bibliothèque ADAL et écrire votre code lié à l’identité.  Pour que la bibliothèque ADAL communique avec Azure AD, vous devez lui fournir des informations sur l’inscription de votre application.

1. Commencez par ajouter la bibliothèque ADAL au projet DirectorySearcher à l’aide de CocoaPods.

    ```
    $ vi Podfile
    ```
2. Ajoutez le code suivant à ce podfile :

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

3. Chargez maintenant le podfile à l’aide de CocoaPods. Cette étape crée un espace de travail XCode que vous allez charger.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

4. Dans le projet de démarrage rapide, ouvrez le fichier plist `settings.plist`.  Remplacez les valeurs des éléments de la section afin qu’elles reflètent les valeurs que vous avez entrées dans le portail Azure. Votre code fait référence à ces valeurs chaque fois qu’il utilise la bibliothèque ADAL.
  * `tenant` est le domaine de votre locataire Azure AD ; par exemple, contoso.onmicrosoft.com.
  * `clientId` est l’ID client de votre application, copié à partir du portail.
  * `redirectUri` est l’URL de redirection que vous avez inscrite dans le portail.

## <a name="4----use-adal-to-get-tokens-from-azure-ad"></a>4.    Utiliser la bibliothèque ADAL pour obtenir des jetons à partir d’Azure AD
Le principe de base de la bibliothèque ADAL consiste simplement à appeler un completionBlock `+(void) getToken : ` chaque fois que votre application a besoin d’un jeton d’accès, et la bibliothèque ADAL s’occupe du reste.  

1. Dans le projet `QuickStart`, ouvrez `GraphAPICaller.m` et recherchez le commentaire `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` vers le haut.  C’est à ce moment-là que vous fournissez à la bibliothèque ADAL, à l’aide d’une méthode CompletionBlock, les coordonnées dont elle a besoin pour communiquer avec Azure AD, et que vous lui indiquez comment mettre en cache des jetons.

    ```ObjC
    +(void) getToken : (BOOL) clearCache
               parent:(UIViewController*) parent
    completionHandler:(void (^) (NSString*, NSError*))completionBlock;
    {
        AppData* data = [AppData getInstance];
        if(data.userItem){
            completionBlock(data.userItem.accessToken, nil);
            return;
        }

        ADAuthenticationError *error;
        authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
        authContext.parentController = parent;
        NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:data.resourceId
                                     clientId:data.clientId
                                  redirectUri:redirectUri
                               promptBehavior:AD_PROMPT_AUTO
                                       userId:data.userItem.userInformation.userId
                        extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                             completionBlock:^(ADAuthenticationResult *result) {

                                  if (result.status != AD_SUCCEEDED)
                                  {
                                     completionBlock(nil, result.error);
                                  }
                                  else
                                  {
                                      data.userItem = result.tokenCacheStoreItem;
                                      completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                                  }
                             }];
    }

    ```

2. Nous devons à présent utiliser ce jeton pour rechercher des utilisateurs dans le graphique. Recherchez le commentaire `// TODO: implement SearchUsersList`. Cette méthode effectue une demande GET auprès de l’API Graph Azure AD pour l’interroger à propos d’utilisateurs dont l’UPN commence par le terme de recherche donné.  Pour interroger l’API Azure AD Graph, vous devez inclure un jeton d’accès (access_token) dans l’en-tête `Authorization` de la demande. C’est là que la bibliothèque ADAL entre en jeu.

    ```ObjC
    +(void) searchUserList:(NSString*)searchString
                    parent:(UIViewController*) parent
          completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
    {
        if (!loadedApplicationSettings)
       {
            [self readApplicationSettings];
        }
        
        AppData* data = [AppData getInstance];

        NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

        [self craftRequest:[self.class trimString:graphURL]
                    parent:parent
         completionHandler:^(NSMutableURLRequest *request, NSError *error) {

             if (error != nil)
             {
                 completionBlock(nil, error);
             }
             else
             {

                 NSOperationQueue *queue = [[NSOperationQueue alloc]init];

                 [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                     if (error == nil && data != nil){

                         NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
                         NSDictionary *keyValuePairs;
                         NSMutableArray* Users = [[NSMutableArray alloc]init];

                         for(int i =0; i < graphDataArray.count; i++)
                         {
                             keyValuePairs = [graphDataArray objectAtIndex:i];

                             User *s = [[User alloc]init];
                             s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                             s.name =[keyValuePairs valueForKey:@"givenName"];

                             [Users addObject:s];
                         }

                         completionBlock(Users, nil);
                     }
                     else
                     {
                         completionBlock(nil, error);
                     }

                }];
             }
         }];

    }

    ```


3. Quand votre application demande un jeton en appelant `getToken(...)`, la bibliothèque ADAL tente de retourner un jeton sans demander à l’utilisateur ses informations d’identification.  Si la bibliothèque ADAL détermine que l’utilisateur doit se connecter pour obtenir un jeton, elle affiche une boîte de dialogue de connexion, récupère les informations d’identification de l’utilisateur, puis retourne un jeton après une authentification réussie.  Si la bibliothèque ADAL ne peut pas retourner un jeton pour une raison quelconque, un `AdalException` est levé.

> [!Note] 
> L’objet `AuthenticationResult` contient un objet `tokenCacheStoreItem` qui peut être utilisé pour collecter les informations dont votre application peut avoir besoin. Dans le démarrage rapide, `tokenCacheStoreItem` est utilisé pour déterminer si l’authentification a déjà été effectuée.
>
>

## <a name="5-build-and-run-the-application"></a>5. Génération et exécution de l’application
Félicitations ! Vous disposez désormais d’une application iOS fonctionnelle pouvant authentifier les utilisateurs, appeler en toute sécurité les API web à l’aide d’OAuth 2.0 et obtenir des informations de base concernant l’utilisateur.  Si vous ne l’avez pas encore fait, il est maintenant temps de remplir votre client avec quelques utilisateurs.  Démarrez votre application de démarrage rapide, puis connectez-vous à l’aide de l’un de ces utilisateurs.  Recherchez d’autres utilisateurs en fonction de leur UPN.  Fermez l’application, puis redémarrez-la.  Notez que la session de l’utilisateur reste intacte.

La bibliothèque ADAL facilite l’intégration de toutes ces fonctionnalités d’identité communes dans votre application.  Elle effectue les tâches ingrates à votre place, comme la gestion du cache, la prise en charge du protocole OAuth, la présentation d’une interface utilisateur à l’utilisateur pour qu’il se connecte et l’actualisation des jetons expirés.  La seule chose que vous devez vraiment connaître est un appel unique d’API : `getToken`.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) est fourni sur [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez à présent aborder d’autres scénarios.  Par exemple :

* [Sécurisation d’une API web Node.js avec Azure AD](active-directory-devquickstarts-webapi-nodejs.md)
* En savoir plus sur l’[activation d’une authentification unique (SSO) entre applications sur iOS à l’aide de la bibliothèque ADAL](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

