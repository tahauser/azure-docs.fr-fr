---
title: Créer un pont iOS WebView avec le Kit SDK Mobile Engagement iOS natif
description: Décrit comment créer un pont entre WebView exécutant Javascript et le kit SDK Mobile Engagement iOS natif
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: e1d6ff6f-cd67-4131-96eb-c3d6318de752
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 07b2b8be80c090ce533f31cc28910f3ce7b91f73
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Créer un pont iOS WebView avec le Kit SDK Mobile Engagement iOS natif
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

> [!div class="op_single_selector"]
> * [Pont Android](mobile-engagement-bridge-webview-native-android.md)
> * [Pont iOS](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Certaines applications mobiles sont conçues comme applications hybrides : le développement de l’application proprement dite s’effectue de manière native dans iOS Objective-C, mais certains ou même tous les écrans sont rendus dans une WebView iOS. Vous pouvez toujours utiliser le Kit SDK Mobile Engagement iOS dans ces applications. Ce didacticiel explique comment procéder. 

Il existe deux approches, mais aucune n’est documentée :

* La première est décrite dans cet [article](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip). Elle nécessite l’inscription d’un `UIWebViewDelegate` sur votre WebView et l’interception puis l’annulation immédiate d’une modification de l’emplacement effectuée dans Javascript. 
* La deuxième approche est basée sur cette [session WWDC 2013](https://developer.apple.com/videos/play/wwdc2013/615). Elle est plus claire que la première, c’est pourquoi nous allons la suivre dans ce guide. Notez que cette approche fonctionne uniquement sur iOS7 et versions ultérieures. 

Suivez les étapes ci-dessous pour l’exemple de pont iOS :

1. Tout d’abord, vous devez vérifier que vous avez bien effectué notre [didacticiel de prise en main](mobile-engagement-ios-get-started.md) pour intégrer le Kit SDK Mobile Engagement iOS à votre application hybride. Si vous le souhaitez, vous pouvez activer la journalisation de test comme suit, pour voir les méthodes du Kit SDK à mesure que nous les déclenchons à partir de la WebView. 
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
             [EngagementAgent setTestLogEnabled:YES];
           ....
        }
2. Vérifiez maintenant que votre application hybride a un écran avec une WebView. Vous pouvez l’ajouter au `Main.storyboard` de l’application. 
3. Associez cette WebView à votre **ViewController** en faisant glisser la WebView de la scène de contrôleur d’affichage vers l’écran de modification `ViewController.h`, et en la plaçant juste en dessous de la ligne `@interface`. 
4. Après cela, une boîte de dialogue vous invite à fournir un nom. Tapez **webView**. Le fichier `ViewController.h` doit ressembler à ceci :
   
        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
   
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
   
        @end
5. Nous mettrons à jour le fichier `ViewController.m` ultérieurement. Tout d’abord, nous allons créer le fichier de pont qui crée un wrapper sur certaines méthodes du Kit SDK Mobile Engagement iOS couramment utilisées. Créez un fichier d’en-tête nommé **EngagementJsExports.h** qui utilise le mécanisme `JSExport` décrit dans la [session](https://developer.apple.com/videos/play/wwdc2013/615) mentionnée précédemment pour exposer les méthodes iOS natives. 
   
        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
   
        @protocol EngagementJsExports <JSExport>
   
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
   
        @end
   
        @interface EngagementJs : NSObject <EngagementJsExports>
   
        @end
6. Nous allons ensuite créer la deuxième partie du fichier de pont. Créez un fichier nommé **EngagementJsExports.m** qui contiendra l’implémentation créant les wrappers réels en appelant les méthodes du Kit SDK Mobile Engagement iOS. Notez également que nous analysons le `extras` qui est passé à partir du code Javascript de la WebView et que nous le mettons dans un objet `NSMutableDictionary` à passer avec les appels de méthode du Kit SDK Engagement.  
   
        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
   
        @implementation EngagementJs
   
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
   
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
   
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
   
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
   
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
   
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
   
           return extras;
        }
   
        @end
7. Maintenant, nous revenons à **ViewController.m** et nous le mettons à jour avec le code suivant : 
   
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
   
        @interface ViewController ()
   
        @end
   
        @implementation ViewController
   
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
   
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
   
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
   
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
   
           context[@"EngagementJs"] = [EngagementJs class];
        }
   
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
   
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
   
        @end
8. Notez les points suivants concernant le fichier **ViewController.m** :
   
   * Dans la méthode `loadWebView` , nous chargeons un fichier HTML local nommé **LocalPage.html** , dont nous allons maintenant examiner le code. 
   * Dans la méthode `webViewDidFinishLoad`, nous prenons le `JsContext` et nous l’associons à notre classe wrapper. Cela va nous permettre d’appeler nos méthodes du Kit SDK de wrapper **EngagementJs** à partir de la WebView. 
9. Créez un fichier nommé **LocalPage.html** avec le code suivant :
   
        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
   
               <script type="text/javascript">
   
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
   
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
   
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
   
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
   
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
   
           </head>
           <body>
               <h1>Bridge Tester</h1>
   
               <div id='engagement'>
   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
   
               </div>
           </body>
        </html>
10. Notez les points suivants concernant le fichier HTML ci-dessus :
    
    * Il contient un ensemble de zones d’entrée où vous pouvez fournir des données à utiliser comme noms pour votre event, job, error et appInfo. Quand vous cliquez sur le bouton en regard, le code Javascript est appelé, et celui-ci appelle les méthodes à partir du fichier de pont pour passer cet appel au kit SDK Mobile Engagement iOS. 
    * Nous associons certaines informations statiques supplémentaire aux événements, aux tâches et même aux erreurs pour illustrer comment cela est réalisable. Ces informations supplémentaires sont envoyées sous forme de chaîne JSON qui, si vous regardez dans le fichier `EngagementJsExports.m` , est analysée et transmise lors de l’envoi des événements, tâches et erreurs. 
    * Une tâche Mobile Engagement est démarrée avec le nom que vous spécifiez dans la zone d’entrée, elle s’exécute pendant 10 secondes, puis elle s’arrête. 
    * Une balise ou une appinfo Mobile Engagement est passée avec « customer_name » comme clé statique et la valeur que vous avez entrée comme valeur de balise. 
11. Exécutez l’application. Vous verrez ce qui suit. Maintenant, fournissez un nom pour un événement de test comme celui qui suit et cliquez sur **Send** à côté. 
    
     ![][1]
12. Maintenant, si vous accédez à l’onglet **Surveiller** de votre application et que vous regardez sous **Événements -> Détails**, vous voyez cet événement et les informations d’application statiques que nous envoyons. 
    
    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
