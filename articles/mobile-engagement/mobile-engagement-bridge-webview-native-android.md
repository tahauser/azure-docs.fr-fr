---
title: Créer un pont Android WebView avec le Kit SDK Mobile Engagement Android natif
description: Décrit comment créer un pont entre WebView exécutant Javascript et le Kit SDK Mobile Engagement Android natif
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: cf272f3f-2b09-41b1-b190-944cdca8bba2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 717c0360042b8f2afd9c41f1ee97e64e95a76a2b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>Créer un pont Android WebView avec le Kit SDK Mobile Engagement Android natif
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

> [!div class="op_single_selector"]
> * [Pont Android](mobile-engagement-bridge-webview-native-android.md)
> * [Pont iOS](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Certaines applications mobiles sont conçues comme applications hybrides : le développement de l’application proprement dite s’effectue de manière native dans Android, mais certains ou même tous les écrans sont rendus dans une WebView Android. Vous pouvez quand même utiliser le Kit SDK Mobile Engagement Android dans ces applications. Ce didacticiel explique comment procéder. L’exemple de code ci-dessous est basé sur la documentation Android accessible [ici](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Il décrit comment utiliser cette approche documentée pour obtenir une implémentation semblable pour des méthodes couramment utilisées du Kit SDK Mobile Engagement Android, pour qu’une WebView d’une application hybride puisse également lancer des demandes pour le suivi des événements, des tâches, des erreurs et des informations sur l’application tout en les transférant par le biais de notre Kit SDK Android. 

1. Tout d’abord, vous devez vérifier que vous avez bien effectué notre [didacticiel de prise en main](mobile-engagement-android-get-started.md) pour intégrer le Kit SDK Mobile Engagement Android à votre application hybride. Votre méthode `OnCreate` ressemble alors à ce qui suit.  
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }
2. Vérifiez maintenant que votre application hybride a un écran avec une WebView. Son code est semblable au suivant, dans lequel nous chargeons un fichier HTML local **Sample.html** dans la WebView de la méthode `onCreate` de l’écran. 
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }
   
        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }
3. Maintenant, créez un fichier de pont nommé **WebAppInterface** qui crée un wrapper sur certaines méthodes du Kit SDK Mobile Engagement Android couramment utilisées à l’aide de l’approche `@JavascriptInterface` décrite dans la [documentation Android](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript) :
   
        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
   
        import com.microsoft.azure.engagement.EngagementAgent;
   
        import org.json.JSONArray;
        import org.json.JSONObject;
   
        public class WebAppInterface {
            Context mContext;
   
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
   
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
   
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
   
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
   
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  
4. Une fois que nous avons créé le fichier de pont ci-dessus, nous devons vérifier qu’il est associé à notre WebView. Pour cela, nous devons modifier la méthode `SetWebview` pour qu’elle ressemble à ceci :
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }
5. Dans l’extrait de code ci-dessus, nous avons appelé `addJavascriptInterface` pour associer notre classe de pont à notre WebView, et nous avons aussi créé un handle nommé **EngagementJs** pour appeler les méthodes à partir du fichier de pont. 
6. Maintenant, créez le fichier suivant nommé **Sample.html** dans votre projet dans un dossier nommé **assets** qui est chargé dans la WebView et où nous appellerons les méthodes à partir du fichier de pont.
   
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
                      log('window.onerror: ' + err);
                    }
   
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
   
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
   
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
   
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
   
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
   
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
   
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
   
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>
7. Notez les points suivants concernant le fichier HTML ci-dessus :
   
   * Il contient un ensemble de zones d’entrée où vous pouvez fournir des données à utiliser comme noms pour votre event, job, error et appInfo. Quand vous cliquez sur le bouton à côté, le code Javascript est appelé, et celui-ci appelle les méthodes à partir du fichier de pont pour passer cet appel au Kit SDK Mobile Engagement Android. 
   * Nous associons certaines informations statiques supplémentaire aux événements, aux tâches et même aux erreurs pour illustrer comment cela est réalisable. Ces informations supplémentaires sont envoyées sous forme de chaîne JSON qui, si vous regardez dans le fichier `WebAppInterface`, est analysée et placée dans un `Bundle` Android et transmise lors de l’envoi des événements, tâches et erreurs. 
   * Une tâche Mobile Engagement est démarrée avec le nom que vous spécifiez dans la zone d’entrée, elle s’exécute pendant 10 secondes, puis elle s’arrête. 
   * Une balise ou une appinfo Mobile Engagement est passée avec « customer_name » comme clé statique et la valeur que vous avez entrée comme valeur de balise. 
8. Exécutez l’application. Vous verrez ce qui suit. Maintenant, fournissez un nom pour un événement de test comme celui qui suit et cliquez sur **Send** en dessous. 
   
    ![][1]
9. Maintenant, si vous accédez à l’onglet **Surveiller** de votre application et que vous regardez sous **Événements -> Détails**, vous voyez cet événement et les informations d’application statiques que nous envoyons. 
   
   ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
