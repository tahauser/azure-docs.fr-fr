---
title: Comment utiliser le plug-in Apache Cordova pour Azure Mobile Apps
description: Comment utiliser le plug-in Apache Cordova pour Azure Mobile Apps
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: 
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: f166d2e533dc49ca7779b45f3dec57a53c22fc40
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Comment utiliser la bibliothèque cliente Apache Cordova pour Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ce guide indique le déroulement de scénarios courants dans le cadre de l’utilisation du dernier [plug-in Apache Cordova pour Azure Mobile Apps]. Si vous ne connaissez pas Azure Mobile Apps, consultez d’abord la section [Démarrage rapide d’Azure Mobile Apps] pour créer un serveur principal, créer une table et télécharger un projet Apache Cordova prédéfini. Dans ce guide, nous nous concentrons sur le plug-in Apache Cordova côté client.

## <a name="supported-platforms"></a>Plateformes prises en charge
Ce kit de développement logiciel (SDK) prend en charge Apache Cordova 6.0.0 et version ultérieure sur les appareils iOS, Android et Windows.  La prise en charge de la plate-forme est la suivante :

* Android API 19-24 (KitKat à Nougat).
* iOS 8.0 et versions ultérieures.
* Windows Phone 8.1.
* Plateforme Windows universelle.

## <a name="Setup"></a>Configuration et conditions préalables
Ce guide part du principe que vous avez créé un serveur principal avec une table. Ce guide suppose que la table a le même schéma que les tables dans ces didacticiels. Ce guide suppose également que vous avez ajouté le plug-in Apache Cordova à votre code.  Si ce n’est pas le cas, vous pouvez l’ajouter à votre projet depuis la ligne de commande :

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Pour plus d’informations sur la création de [votre première application Apache Cordova], consultez la documentation officielle.

## <a name="ionic"></a>Configuration d’une application Ionic v2

Pour configurer correctement un projet Ionic v2, créez d’abord une application de base et ajoutez le plug-in Cordova :

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Ajoutez les lignes suivantes à `app.component.ts` pour créer l’objet client :

```
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Vous pouvez maintenant générer et exécuter le projet dans le navigateur :

```
ionic platform add browser
ionic run browser
```

Le plug-in Azure Mobile Apps Cordova prend en charge les applications Ionic v1 et v2.  Seules les applications Ionic v2 nécessitent la déclaration supplémentaire pour l’objet `WindowsAzure`.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Procédure : authentification des utilisateurs
Azure App Service prend en charge l’authentification et l’autorisation des utilisateurs d’applications par le biais de divers fournisseurs d’identité externes : Facebook, Google, compte Microsoft et Twitter. Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l’identité des utilisateurs authentifiés pour implémenter des règles d’autorisation dans les scripts serveur. Pour plus d'informations, consultez la page [Prise en main de l'authentification] .

Quand vous utilisez l’authentification dans une application Apache Cordova, les plug-ins Cordova suivants doivent être réunis :

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Deux flux d’authentification sont pris en charge : un flux serveur et un flux client.  Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface d'authentification Web du fournisseur. Le flux client permet une intégration approfondie avec les fonctionnalités propres aux appareils, telles que l'authentification unique, car il repose sur des Kits de développement logiciel (SDK) propres aux appareils et aux fournisseurs.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Configurer votre Mobile App Service pour les URL de redirection externes.
Plusieurs types d’applications Apache Cordova utilisent une fonctionnalité de bouclage pour gérer les flux d’interface utilisateur OAuth.  Les flux d’interface utilisateur OAuth posent des problèmes car le service d’authentification sait uniquement comment utiliser votre service par défaut.  Voici quelques exemples de problèmes causés par les flux d’interface utilisateur OAuth :

* L’émulateur Ripple.
* Live recharger avec Ionic.
* Exécution locale du backend mobile
* Exécution du backend mobile dans une autre instance Azure App Service que celle fournissant l’authentification.

Suivez ces instructions pour ajouter vos paramètres régionaux à la configuration :

1. Connectez-vous au [portail Azure]
2. Sélectionnez **Toutes les ressources** ou **App Services**, puis cliquez sur le nom de votre application mobile.
3. Cliquez sur **Outils**
4. Cliquez sur **Explorateur de ressources** dans le menu OBSERVER, puis cliquez sur **Atteindre**.  Une nouvelle fenêtre ou un nouvel onglet s’ouvre.
5. Développez les nœuds **config** et **authsettings** pour votre site dans le volet de navigation de gauche.
6. Cliquez sur **Modifier**
7. Recherchez l’élément "allowedExternalRedirectUrls".  Il peut être défini sur null ou sur un tableau de valeurs.  Remplacez la valeur par la valeur suivante :

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Remplacez les URL par les URL de votre service.  Par exemple, "http://localhost:3000" (pour le service Node.js) ou "http://localhost:4400" (pour le service Ripple).  Il s’agit seulement d’exemples d’URL. Votre situation, y compris pour les services mentionnés dans les exemples, peut être différente.
8. Cliquez sur le bouton **Lecture/Écriture** dans le coin supérieur droit de l’écran.
9. Cliquez sur le bouton vert **PUT** .

Les paramètres sont alors enregistrés.  Ne fermez pas la fenêtre du navigateur avant la fin de l’enregistrement des paramètres.
Ajoutez également ces URL de bouclage aux paramètres de CORS pour votre App Service :

1. Connectez-vous au [portail Azure]
2. Sélectionnez **Toutes les ressources** ou **App Services**, puis cliquez sur le nom de votre application mobile.
3. Le panneau Paramètres s’ouvre automatiquement.  Si ce n’est pas le cas, cliquez sur **Tous les paramètres**.
4. Cliquez sur **CORS** sous le menu de l’API.
5. Entrez l’URL à ajouter dans la zone correspondante et appuyez sur Entrée.
6. Entrez des URL supplémentaires, si nécessaire.
7. Cliquez sur **Enregistrer** pour enregistrer les paramètres.

L’application des nouveaux paramètres prend environ 10 à 15 secondes.

## <a name="register-for-push"></a>Procédure : inscription aux notifications Push
Installez le plug-in [phonegap-plugin-push] pour gérer les notifications Push.  Vous pouvez ajouter ce plugin facilement en exécutant la commande `cordova plugin add` sur la ligne de commande, ou par le biais du programme d’installation de plug-in Git dans Visual Studio.  Le code suivant dans votre application Apache Cordova inscrit votre appareil aux notifications Push :

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Utilisez le Kit de développement logiciel (SDK) Notification Hubs pour envoyer des notifications Push à partir du serveur.  N’envoyez jamais de notifications Push directement depuis les clients. Cela risquerait de déclencher une attaque par déni de service au niveau des concentrateurs de notification ou de PNS.  Le PNS pourrait bannir votre trafic en réponse à ces attaques.

## <a name="more-information"></a>Plus d’informations

Vous pouvez trouver des informations sur les API dans notre [documentation sur les API](http://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[portail Azure]: https://portal.azure.com
[Démarrage rapide d’Azure Mobile Apps]: app-service-mobile-cordova-get-started.md
[Prise en main de l'authentification]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[plug-in Apache Cordova pour Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[votre première application Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx
