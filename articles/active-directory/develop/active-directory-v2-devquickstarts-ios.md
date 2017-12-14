---
title: "Ajoutez la connexion à une application iOS à l’aide du point de terminaison Azure AD v2.0 | Microsoft Docs"
description: "Génération d’une application iOS qui connecte les utilisateurs à l’aide de leur compte Microsoft personnel et de leurs comptes professionnel ou scolaire à l’aide de bibliothèques tierces."
services: active-directory
documentationcenter: 
author: brandwe
manager: mtillman
editor: 
ms.assetid: fd3603c0-42f7-438c-87b5-a52d20d6344b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: 398ddbd004b4a12f4aa79ed64cc85f0e5bc5407a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Ajouter la connexion à une application iOS à l’aide d’une bibliothèque tierce avec l’API Graph utilisant le point de terminaison v2.0
La plateforme d’identité Microsoft utilise des normes ouvertes telles que OAuth2 et OpenID Connect. Les développeurs peuvent utiliser n’importe quelle bibliothèque qu’ils souhaitent intégrer à nos services. Pour aider les développeurs à utiliser notre plateforme avec d’autres bibliothèques, nous avons rédigé quelques procédures pas à pas comme celle-ci pour présenter la configuration des bibliothèques tierces pour se connecter à la plateforme d’identité de Microsoft. La plupart des bibliothèques qui implémentent [la spécification RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) peuvent se connecter à la plateforme Microsoft Identity.

Avec l’application créée par cette procédure pas à pas, les utilisateurs peuvent se connecter à leur organisation, puis rechercher pour d’autres dans leur entreprise à l’aide de l’API Graph.

Si vous découvrez OAuth2 ou OpenID Connect, cet exemple de configuration n’est peut-être pas très parlant pour vous. Nous vous recommandons de lire [Protocoles v2.0 - Flux du Code d’autorisation OAuth 2.0](active-directory-v2-protocols-oauth-code.md) pour l’arrière-plan.

> [!NOTE]
> Certaines fonctionnalités de notre plateforme qui ont une expression dans les normes OAuth2 ou OpenID Connect, comme la gestion de la stratégie d’accès conditionnel et d’Intune, requièrent l’utilisation de nos bibliothèques d’identité Microsoft Azure open source.
> 
> 

Le point de terminaison v2.0 ne prend pas en charge l’intégralité des scénarios et fonctionnalités d’Azure Active Directory.

> [!NOTE]
> Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-code-from-github"></a>Télécharger le code à partir de GitHub
Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2).  Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou la cloner :

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

Vous pouvez aussi simplement télécharger l’exemple et commencer immédiatement :

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Inscription d’une application
Créez une nouvelle application dans le [Portail d’inscription des applications](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez les étapes détaillées dans [Inscription d’une application avec le point de terminaison v2.0](active-directory-v2-app-registration.md).  Veillez à respecter les points suivants :

* Copiez **l’ID d’application** affecté à votre application, vous en aurez besoin rapidement.
* ajouter la plateforme **Mobile** pour votre application ;
* Copiez **l’URI de redirection** provenant du portail. Vous devez utiliser la valeur par défaut de `urn:ietf:wg:oauth:2.0:oob`.

## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Télécharger la bibliothèque tierce NXOAuth2 et créer un espace de travail
Pour cette procédure pas à pas, vous utiliserez OAuth2Client à partir de GitHub, une bibliothèque OAuth2 pour Mac OS X et iOS (Cocoa et Cocoa touch). Cette bibliothèque est basée sur le brouillon 10 de la spécification OAuth2. Elle implémente le profil de l’application native et prend en charge le point de terminaison de l’autorisation de l’utilisateur final. Voici tout ce dont vous aurez besoin pour l’intégration avec la plateforme d’identité Microsoft.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Ajout de la bibliothèque à votre projet à l’aide de CocoaPods
CocoaPods est un gestionnaire de dépendances pour les projets Xcode. Il gère automatiquement les étapes d’installation précédentes.

```
$ vi Podfile
```
1. Ajoutez le code suivant à ce podfile :
   
    ```
     platform :ios, '8.0'
   
     target 'QuickStart' do
   
     pod 'NXOAuth2Client'
   
     end
    ```
2. Chargez le podfile à l’aide de CocoaPods. Cette opération crée un nouvel espace de travail Xcode que vous allez charger.
   
    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Explorer la structure du projet
La structure suivante est définie pour notre projet dans le squelette :

* Une vue principale avec une recherche de noms UPN
* Une vue détaillée pour les données concernant l’utilisateur sélectionné
* Une vue de connexion permettant à un utilisateur de se connecter à l’application pour interroger le graphique

Nous accéderons à différents fichiers dans le squelette pour ajouter l’authentification. D’autres parties du code, comme le code de l’élément visuel, ne sont pas associées à l’identité mais sont fournies pour vous.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Configurer le fichier settings.plst dans la bibliothèque
* Dans le projet de démarrage rapide, ouvrez le fichier `settings.plist` . Remplacez les valeurs des éléments de la section afin qu’elles reflètent les valeurs que vous avez utilisées dans le portail Azure. Votre code se réfère à ces valeurs chaque fois qu’il utilise la bibliothèque d’authentification Active Directory.
  * `clientId` est l’ID client de votre application, copié à partir du portail.
  * `redirectUri` est l’URL de redirection fournie par le portail.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Configurer la bibliothèque NXOAuth2Client dans votre LoginViewController
La bibliothèque NXOAuth2Client requiert des valeurs pour sa configuration. Après avoir effectué cette tâche, vous pouvez utiliser le jeton acquis pour appeler l’API Graph. Étant donné que `LoginView` est appelé chaque fois que nous avons besoin de nous authentifier, il est judicieux de placer les valeurs de configuration dans ce fichier.

* Ajoutons quelques valeurs au fichier `LoginViewController.m` pour définir le contexte pour l’authentification et l’autorisation. Des détails sur les valeurs sont inclus après le code.
  
    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Observons les détails du code.

La première chaîne est pour `scopes`.  La valeur `User.Read` vous permet de lire le profil de base de l’utilisateur connecté.

Plus d’informations sur toutes les étendues disponibles, consultez [Étendues d’autorisation Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Pour `authURL`, `loginURL`, `bhh` et `tokenURL`, utilisez les valeurs indiquées précédemment. Si vous utilisez les bibliothèques d’identité Microsoft Azure open source, nous extrairons ces données pour vous à l’aide de notre point de terminaison des métadonnées. Nous avons effectué le travail d’extraction de ces valeurs pour vous.

La valeur `keychain` est le conteneur qu’utilisera la bibliothèque NXOAuth2Client pour créer un porte-clés pour stocker vos jetons. Si vous souhaitez obtenir l’authentification unique (SSO) entre de nombreuses applications, vous pouvez spécifier le même porte-clés dans chacune de vos applications, ainsi que demander l’utilisation de ce porte-clés dans vos droits Xcode. Cela est expliqué dans la documentation Apple.

Le reste de ces valeurs est requis pour utiliser la bibliothèque et créer des emplacements pour que vous puissiez traiter des valeurs dans le contexte.

### <a name="create-a-url-cache"></a>Créer un cache d’URL
À l’intérieur de `(void)viewDidLoad()`, qui est toujours appelé une fois que la vue est chargée, le code suivant prépare un cache pour notre utilisation.

Ajoutez le code suivant :

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Créer une WebView pour la connexion
Une WebView peut inviter l’utilisateur à utiliser des facteurs supplémentaires comme les SMS (s’ils sont configurés) ou de renvoyer les messages d’erreur à l’utilisateur. Ici, vous allez définir l’affichage web, puis écrire ultérieurement le code pour gérer les rappels qui surviendront dans l’affichage web à partir du service d’identité.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Substituer les méthodes d’affichage web pour gérer l’authentification
Pour indiquer la WebView qui s’affiche lorsqu’un utilisateur doit se connecter comme indiqué précédemment, vous pouvez coller le code suivant.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Écrire du code pour gérer le résultat de la demande OAuth2
Le code suivant gère la valeur redirectURL qui est renvoyée par la WebView. Si l’authentification a échoué, le code va à nouveau être exécuté. En attendant, la bibliothèque indique l’erreur que vous pouvez voir dans la console ou gérer de façon asynchrone.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Définir le contexte OAuth (appelé magasin de comptes)
Ici, vous pouvez appeler `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` sur le magasin de comptes partagé pour chaque service auquel vous souhaitez rendre accessible pour votre application. Le type de compte est une chaîne qui est utilisée comme identificateur pour un certain service. Étant donné que vous accédez à l’API Graph, le code y fait référence en tant que `"myGraphService"`. Ensuite, vous allez configurer un observateur qui vous indique les modifications avec le jeton. Une fois que vous obtenez le jeton, vous renvoyez l’utilisateur vers `masterView`.

```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Configurer la vue principale pour rechercher et afficher les utilisateurs de l’API Graph
Une application à contrôleur de vue principale (MVC) qui affiche les données retournées dans la grille dépasse le cadre de cette procédure pas à pas ; il existe de nombreux didacticiels en ligne pour expliquer comment en créer une. Tout ce code se trouve dans le fichier squelette. Toutefois, vous devez traiter quelques éléments dans cette application MVC :

* Intercepter lorsqu’un utilisateur saisit des données dans le champ de recherche
* Fournir un objet de données vers la vue principale afin d’afficher les résultats dans la grille

Nous effectuerons ces actions ci-dessous.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Ajouter une vérification pour voir si vous êtes connecté
L’application ne fait pas grand-chose si l’utilisateur n’est pas connecté ; par conséquent, il est judicieux de vérifier s’il existe déjà un jeton dans le cache. Si ce n’est pas le cas, vous redirigez vers la vue de connexion pour que l’utilisateur se connecte. Rappelez-vous : la meilleure façon d’effectuer des actions lorsqu’une vue se charge consiste à utiliser la méthode `viewDidLoad()` fournie par Apple.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Mettre à jour la vue de table lors de la réception de données
Lorsque l’API Graph renvoie les données, vous devez afficher ces dernières. Pour plus de simplicité, voici l’ensemble du code permettant de mettre à jour la table. Vous pouvez simplement coller les valeurs appropriées dans votre code réutilisable MVC.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Fournir un moyen d’appeler l’API Graph lorsqu’un utilisateur tape dans le champ de recherche
Lorsqu’un utilisateur tape une recherche dans la zone, vous devez l’indiquer à l’API Graph. La classe `GraphAPICaller` , que vous allez créer dans le code suivant, sépare la fonctionnalité de recherche de la présentation. Pour l’instant, nous allons écrire le code qui envoie tous les caractères de recherche à l’API Graph. Pour ce faire, nous allons fournir une méthode appelée `lookupInGraph`, qui accepte la chaîne que nous souhaitons rechercher.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Écrire une classe d’assistance pour accéder à l’API Graph
Il s’agit de l’essentiel de l’application. Le reste consistait à insérer du code dans le modèle MVC par défaut d’Apple, mais ici vous écrivez du code pour interroger le graphique pendant la saisie de l’utilisateur, puis renvoyez ces données. Voici le code, avec une explication détaillée ensuite.

### <a name="create-a-new-objective-c-header-file"></a>Créer un nouveau fichier d’en-tête Objective C
Nommez le fichier `GraphAPICaller.h`, puis ajoutez le code suivant.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Vous voyez ici qu’une méthode prend une chaîne et retourne un completionBlock. Ce completionBlock, comme vous l’avez peut-être deviné, va mettre à jour la table en fournissant un objet avec des données renseignées en temps réel pendant la recherche de l’utilisateur.

### <a name="create-a-new-objective-c-file"></a>Créer un nouveau fichier Objective C
Nommez le fichier `GraphAPICaller.m`, puis ajoutez la méthode suivante.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


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

```

Examinons cette méthode en détail.

L’essentiel de ce code se trouve dans la méthode `NXOAuth2Request`qui prend les paramètres que vous avez définis à l’intérieur du fichier settings.plist.

La première étape consiste à construire l’appel approprié de l’API Graph. Étant donné que nous appelons `/users`, vous l’indiquez en l’ajoutant (ainsi que sa version) à la ressource API Graph. Il est judicieux de placer ces éléments dans un fichier de paramètres externe, car ceux-ci peuvent changer à mesure que l’API évolue.

```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Ensuite, vous devez spécifier les paramètres que vous allez également fournir à l’appel de l’API Graph. Il est *très important* que vous ne placiez pas les paramètres dans le point de terminaison de ressource, car il est modifié pour tous les caractères non URI lors de l’exécution. Tout code de requête doit être fourni dans les paramètres.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Vous remarquerez peut-être que cela appelle une méthode `convertParamsToDictionary` que vous n’avez pas encore écrite. Faisons-le maintenant à la fin du fichier :

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
Ensuite, nous utiliserons la méthode `NXOAuth2Request` pour obtenir des données de l’API au format JSON.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Enfin, nous verrons comment vous retournez les données vers MasterViewController. Les données nous reviennent sérialisées et doivent être désérialisées et chargées dans un objet que MainViewController peut consommer. A cet effet, le squelette dispose d’un fichier `User.m/h` qui crée un objet Utilisateur. Vos remplissez cet objet utilisateur avec les informations du graphique.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Exécution de l'exemple
Si vous avez utilisé le squelette ou suivi la procédure pas à pas, votre application doit maintenant s’exécuter. Lancez le simulateur et cliquez sur **Connexion** pour utiliser l’application.

## <a name="get-security-updates-for-our-product"></a>Obtenir des mises à jour de sécurité pour notre produit
Nous vous encourageons à activer les notifications lorsque des incidents de sécurité surviennent en vous rendant sur [Security TechCenter](https://technet.microsoft.com/security/dd252948) et en vous abonnant aux alertes d’avis de sécurité.

