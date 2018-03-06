---
title: "Envoi de notifications push vers des applications Chrome avec Azure Notification Hubs | Microsoft Docs"
description: Apprenez comment utiliser Azure Notification Hubs pour envoyer des notifications push vers une application Chrome.
services: notification-hubs
keywords: notifications push mobiles, notifications push, notification push, notifications push chrome
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 376348c3af0ff97162f1b2ed865a5edd47f52546
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Envoi de notifications push vers des applications Chrome avec Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Cet article vous montre comment utiliser Azure Notification Hubs pour envoyer des notifications push vers une application Chrome affichée dans le contexte du navigateur Google Chrome. Dans ce didacticiel, vous allez créer une application Chrome qui reçoit des notifications push à l’aide de [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/). 

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).
> 
> 

Ce didacticiel vous familiarise avec les étapes de base pour activer les notifications Push :

* [Activer Google Cloud Messaging](#register)
* [Configuration de votre hub de notification](#configure-hub)
* [Connexion de votre application Chrome au hub de notification](#connect-app)
* [Envoi d’une notification push à votre application Chrome](#send)
* [Fonctionnalités et capacités supplémentaires](#next-steps)

> [!NOTE]
> Les notifications push d’application Chrome ne sont pas des notifications de navigateur générique : ils sont spécifiques au modèle d’extensibilité de navigateur (voir [vue d’ensemble des applications Chrome] pour plus d’informations). Outre le navigateur de bureau, les applications Chrome s’exécutent sur mobile (Android et iOS) par le biais d’Apache Cordova. Pour plus d’informations, consultez [Applications Chrome sur mobile].
> 
> 

La configuration de GCM et d’Azure Notification Hubs est identique à celle pour Android, étant donné que [Google Cloud Messaging for Chrome] a été déconseillé et qu’il prend désormais en charge les appareils Android et les instances Chrome.

## <a id="register"></a>Activer Google Cloud Messaging
1. Accédez au site web [Google Cloud Console] , connectez-vous avec vos informations d’identification de compte Google, puis cliquez sur le bouton **Create Project** . Saisissez un nom de projet adéquat sous **Project Name** et cliquez sur le bouton **Create**.
   
    ![Console Google Cloud - créer le projet][1]
2. Notez le **Project Number** du projet que vous avez créé, affiché sur la page **Projects**. Vous allez utiliser le numéro du projet comme **GCM Sender ID** dans l’application Chrome pour votre inscription auprès de GCM.
   
    ![Console Google Cloud - numéro de projet][2]
3. Dans le volet gauche, cliquez sur **APIs & auth**, puis faites défiler la page et cliquez sur la touche de bascule pour activer **Google Cloud Messaging for Android**. Vous n’avez pas besoin d’activer **Google Cloud Messaging for Chrome**.
   
    ![Console Google Cloud - clé de serveur][3]
4. Dans le volet gauche, cliquez sur **Credentials** > **Create New Key** > **Server Key** > **Create**.
   
    ![Console Google Cloud - Informations d’identification][4]
5. Notez la valeur **API Key**du serveur. Vous allez utiliser cette valeur pour configurer le hub de notifications dans la section suivante afin de lui permettre d’envoyer des notifications push à GCM.
   
    ![Console Google Cloud - clé d’API][5]

## <a id="configure-hub"></a>Configuration de votre hub de notification
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

6.   Dans la page **Paramètres**, sélectionnez **Notification Services**, puis **Google (GCM)**. Entrez la clé d’API et enregistrez.

        ![Azure Notification Hubs - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

## <a id="connect-app"></a>Connexion de votre application Chrome au hub de notification
Votre hub de notification est à présent configuré pour GCM, et vous disposez des chaînes de connexion nécessaires pour inscrire votre application pour la réception et l’envoi de notifications push. LK

### <a name="create-a-new-chrome-app"></a>Création d’une application Chrome
L’exemple suivant repose sur un [exemple GCM d’application Chrome] et utilise la méthode recommandée pour créer une application Chrome. Cette section présente les étapes spécifiques à Azure Notification Hubs. 

> [!NOTE]
> Nous vous recommandons de télécharger la source de cette application Chrome depuis l’ [exemple de hub de notification d’application Chrome]. 

L’application Chrome est créée à l’aide de JavaScript et vous pouvez utiliser vos éditeurs favoris pour la création. L’image suivante montre à quoi ressemble l’application Chrome :

![Application Google Chrome][15]

1. Créez un dossier et nommez-le `ChromePushApp`. Le nom est arbitraire : Si vous lui donnez un autre nom, assurez-vous que vous remplacez le chemin d’accès dans les segments de code requis.
2. Téléchargez la [bibliothèque crypto-js] dans le dossier que vous avez créé au cours de la deuxième étape. Ce dossier contient deux sous-dossiers : `components` et `rollups`.
3. Créez un fichier `manifest.json` . Toutes les applications Chrome sont sauvegardées dans un fichier manifeste qui contient les métadonnées de l’application et, plus important encore, toutes les autorisations qui sont accordées à l’application lorsque l’utilisateur l’installe.
   
        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }
   
    Notez l’élément `permissions`, qui spécifie que cette application Chrome peut recevoir des notifications push de GCM. Il doit également spécifier l’URI Azure Notification Hubs où l’application Chrome effectue un appel REST pour l’inscription.
    Cet exemple utilise un fichier icône, `gcm_128.png`, que vous trouvez à la source réutilisée à partir de l’exemple GCM d’origine. Vous pouvez le remplacer par une image qui correspond aux [critères d’icône](https://developer.chrome.com/apps/manifest/icons).
4. Créez un fichier appelé `background.js` en utilisant le code suivant :
   
        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }
   
        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.
   
          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);
   
          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }
   
        var registerWindowCreated = false;
   
        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {
   
            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }
   
        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);
   
        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);
   
    Ce fichier affiche le code HTML de la fenêtre de l’application Chrome (**register.html**) et définit également le gestionnaire **messageReceived** pour gérer les notifications push entrantes.
5. Créez un fichier appelé `register.html` qui définit l’interface utilisateur de l’application Chrome. 
   
   > [!NOTE]
   > Cet exemple utilise **CryptoJS v3.1.2**. Si vous avez téléchargé une autre version de la bibliothèque, assurez-vous que vous remplacez correctement la version dans le chemin d’accès `src` .
   > 
   > 
   
        <html>
   
        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>
   
        <body>
   
        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>
   
        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>
   
        <br/>
   
        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>
   
        <br/>
        <br/>
   
        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>
   
        </body>
   
        </html>
6. Créez un fichier appelé `register.js` avec le code de cette étape. Ce fichier spécifie le script derrière `register.html`. Les applications Chrome n’autorisent pas l’exécution inline. Vous devez donc créer un script de sauvegarde distinct pour votre interface utilisateur.
   
        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";
   
        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }
   
        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }
   
          document.getElementById("console").innerHTML = currentStatus  + status;
        }
   
        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);
   
          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }
   
        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;
   
          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }
   
          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;
   
          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }
   
        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }
   
        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";
   
          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });
   
          originalUri = endpoint + hubName;
        }
   
        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration
   
          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;
   
          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);
   
          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }
   
        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";
   
          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);
   
          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();
   
          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };
   
          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }
   
          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");
   
          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }
   
    Le script comporte les points suivants :
   
   * **window.onload** définit les événements de clic des deux boutons de l’interface utilisateur. Le premier gestionnaire d’événements avec clic de bouton permet l’inscription sur GCM, l’autre utilise l’ID d’inscription renvoyé après l’inscription sur GCM pour étendre l’inscription à Azure Notification Hubs.
   * **updateLog** correspond à la fonction qui permet les informations du journal de code. 
   * **registerWithGCM** est le premier gestionnaire avec clic de bouton qui effectue l’appel `chrome.gcm.register` vers GCM afin d’enregistrer cette instance d’application Chrome.
   * **registerCallback** est la fonction de rappel appelée lors du renvoi de l’appel d’inscription GCM.
   * **registerWithNH** est le deuxième gestionnaire de clic de bouton qui s’enregistre avec Notification Hubs. Il obtient `hubName` et `connectionString` (définis par l’utilisateur) et crée l’appel d’API REST de l’inscription à Notification Hubs.
   * **splitConnectionString** et **generateSaSToken** sont des assistances qui représentent l’implémentation JavaScript du processus de création d’un jeton SaS qui doit être utilisé dans tous les appels d’API REST. Pour plus d’informations, consultez la page [Concepts courants](http://msdn.microsoft.com/library/dn495627.aspx).
   * **sendNHRegistrationRequest** est la fonction qui effectue un appel REST HTTP vers Azure Notification Hubs.
   * **registrationPayload** définit la charge utile XML d’enregistrement. Pour plus d’informations, voir [Création de l’API REST NH d’inscription]. Vous devez mettre à jour l’ID d’inscription avec la valeur reçue de GCM.
   * **client** est une instance de **XMLHttpRequest** dont se sert l’application pour exécuter la requête HTTP POST. Mettez à jour l’en-tête `Authorization` avec `sasToken`. La réussite de cet appel enregistre cette instance de l’application Chrome auprès d’Azure Notification Hubs.

La structure générale de dossier pour ce projet doit ressembler à ceci : ![Application Google Chrome - structure de dossier][21]

### <a name="set-up-and-test-your-chrome-app"></a>Installation et test de votre application Chrome
1. Ouvrez votre navigateur Chrome. Ouvrez la page **Extensions** de Chrome et activez **Mode développeur**.
   
    ![Google Chrome - activer le Mode développeur][16]
2. Cliquez sur **Load unpacked extension** et accédez au dossier où vous avez créé les fichiers. Vous pouvez également utiliser **Chrome Apps & Extensions Developer Tool**. Cet outil est lui-même une application Chrome (installée à partir de Chrome Web Store) et fournit des fonctionnalités avancées de débogage pour votre développement d’applications Chrome.
   
    ![Google Chrome - charger l’extension décompressée][17]
3. Si votre application Chrome est créée sans erreur, elle s’affiche.
   
    ![Google Chrome - affichage de l’application Chrome][18]
4. Entrez le **Project Number** obtenu précédemment depuis la **Google Cloud Console** dans le champ Sender ID et cliquez sur **Register with GCM**. Vous devriez obtenir le message vous indiquant que l’ **inscription auprès de GCM a réussi.**
   
    ![Google Chrome - personnalisation de l’application Chrome][19]
5. Entrez le nom de votre hub de notification dans le champ **Notification Hub Name** et la valeur **DefaultListenSharedAccessSignature** obtenue précédemment depuis le portail, puis cliquez sur **Register with Azure Notification Hub**. Vous devriez obtenir le message vous indiquant que l’ **inscription à Notification Hub a réussi** , ainsi que les détails de la réponse à l’inscription qui contient l’ID d’inscription à Azure Notification Hubs.
   
    ![Google Chrome - spécifier les détails de Notification Hub][20]  

## <a name="send"></a>Envoi d’une notification à votre application Chrome
À des fins de test, envoyez des notifications push Chrome à l’aide d’une application de console .NET. 

> [!NOTE]
> Vous pouvez envoyer des notifications push en utilisant Notification Hubs à partir d’un serveur principal utilisant l’<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a> publique. Découvrez le [portail de documentation](https://azure.microsoft.com/documentation/services/notification-hubs/) pour plus d’exemples interplateformes.
> 
> 

1. Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau**, puis **Projet**. Sous **Visual C#**, cliquez sur **Windows** et **Application Console**, puis cliquez sur **OK**.  Dans cette étape, un projet d’application console est créé.
2. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Console du Gestionnaire de package**. La console du gestionnaire de package s’affiche dans la fenêtre du bas.
3. Dans la fenêtre de console, exécutez la commande suivante :
   
        Install-Package Microsoft.Azure.NotificationHubs
   
       This adds a reference to the Azure Service Bus SDK with the <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet package</a>.
4. Ouvrez `Program.cs` et ajoutez l’instruction `using` suivante :
   
        using Microsoft.Azure.NotificationHubs;
5. Dans la classe `Program` , ajoutez la méthode suivante :
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }
   
       Make sure to replace the `<hub name>` placeholder with the name of the notification hub that appears in the [portal](https://portal.azure.com) in your Notification Hub blade. Also, replace the connection string placeholder with the connection string called `DefaultFullSharedAccessSignature` that you obtained in the notification hub configuration section.
   
   > [!NOTE]
   > Veillez à utiliser la chaîne de connexion avec un accès **Total**, et non un accès **Écouter**. La clé de connexion d’ **écoute** n’accorde pas de notification d’envoi de notifications push.
   > 
   > 
6. Ajoutez les appels suivants à la méthode `Main` :
   
         SendNotificationAsync();
         Console.ReadLine();
7. Assurez-vous que Chrome fonctionne bien et exécutez l’application de console.
8. Vous devriez voir le message de notification suivant sur votre bureau.
   
    ![Google Chrome - notification][13]
9. Vous pouvez également voir toutes vos notifications à l’aide de la fenêtre de notifications de Chrome sur la barre des tâches (dans Windows) lorsque Chrome est en cours d’exécution.
   
    ![Google Chrome - liste de notification](./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png)

> [!NOTE]
> Vous n’avez pas besoin d’exécuter l’application Chrome ou de l’ouvrir dans le navigateur (bien que le navigateur Chrome lui-même doive être en cours d’exécution). Vous obtenez également une vue consolidée de toutes vos notifications dans la fenêtre notifications de Chrome.
> 
> 

## <a name="next-steps"> </a>Étapes suivantes
En savoir plus sur Notification Hubs dans la [Vue d’ensemble de Notification Hubs].

Pour cibler les utilisateurs spécifiques, reportez-vous au didacticiel [Notification des utilisateurs via Azure Notification Hubs] . 

Pour segmenter vos utilisateurs par groupes d’intérêt, vous pouvez suivre le didacticiel [Dernières nouvelles via Azure Notification Hubs] .

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[exemple de hub de notification d’application Chrome]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Google Cloud Console]: http://cloud.google.com/console
[Vue d’ensemble de Notification Hubs]: notification-hubs-push-notification-overview.md
[vue d’ensemble des applications Chrome]: https://developer.chrome.com/apps/about_apps
[exemple GCM d’application Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Applications Chrome sur mobile]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Création de l’API REST NH d’inscription]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[bibliothèque crypto-js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Notification des utilisateurs via Azure Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Dernières nouvelles via Azure Notification Hubs]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
