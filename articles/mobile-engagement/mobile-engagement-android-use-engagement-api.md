---
title: Comment utiliser l'API Engagement sur Android
description: Dernier SDK Android - Comment utiliser l'API Engagement sur Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 09b62659-82ae-4a55-8784-fca0b6b22eaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 79d0652be227dd6703a35d31409cf8e0d9c59519
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-the-engagement-api-on-android"></a>Comment utiliser l'API Engagement sur Android
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Ce document vient compléter le document [Options de génération de rapports avancés pour le Kit de développement logiciel (SDK) Azure Mobile Engagement pour Android](mobile-engagement-android-advanced-reporting.md). Il fournit des informations détaillées sur l'utilisation de l'API Engagement pour signaler les statistiques de votre application.

N'oubliez pas que si vous voulez qu'Engagement ne crée des rapports que sur les sessions, les activités, les blocages et les informations techniques de votre application, le plus simple est de faire hériter vos sous-classes `Activity` de la classe correspondante `EngagementActivity`.

Si vous souhaitez aller plus loin, par exemple si vous avez besoin de signaler des événements, des erreurs et des travaux spécifiques à l'application, ou si vous devez signaler les activités de votre application d'une manière différente de celle implémentée dans les classes `EngagementActivity`, vous devez utiliser l'API Engagement.

L'API Engagement est fournie par la classe `EngagementAgent` . Une instance de cette classe peut être récupérée en appelant la méthode statique `EngagementAgent.getInstance(Context)` (notez que l'objet `EngagementAgent` retourné est un singleton).

## <a name="engagement-concepts"></a>Concepts liés à Engagement
Les sections qui suivent affinent les [concepts Mobile Engagement](mobile-engagement-concepts.md)courants pour la plateforme Android.

### <a name="session-and-activity"></a>`Session` et `Activity`
Si l’utilisateur reste inactif plus de quelques secondes entre deux *activités*, sa séquence *d’activités* est fractionnée en deux *sessions* distinctes. Ces quelques secondes constituent le « délai d'expiration de session ».

Une *activité* est généralement associée à un écran de l’application, c’est-à-dire que *l’activité* démarre quand l’écran s’affiche et s’arrête quand l’écran est fermé. C’est le cas quand le SDK Engagement est intégré à l’aide des classes `EngagementActivity`.

Mais les *activités* peuvent également être contrôlées manuellement à l'aide de l'API Engagement. Cela permet de fractionner un écran donné en plusieurs sous-parties pour obtenir plus d'informations sur l'utilisation de cet écran (par exemple la fréquence et la durée d'utilisation des boîtes de dialogue dans cet écran).

## <a name="reporting-activities"></a>Rapports d'activités
> [!IMPORTANT]
> Vous n'êtes pas obligé de signaler les activités décrites dans cette section si vous utilisez la classe `EngagementActivity` et ses variantes, comme expliqué dans le document Comment intégrer Engagement à Android.
> 
> 

### <a name="user-starts-a-new-activity"></a>L'utilisateur démarre une nouvelle activité
            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Vous devez appeler `startActivity()` chaque fois que l'activité utilisateur change. Le premier appel à cette fonction démarre une nouvelle session utilisateur.

Le meilleur moment pour appeler cette fonction est à chaque rappel de l'activité `onResume` .

### <a name="user-ends-his-current-activity"></a>L'utilisateur met fin à l'activité en cours
            EngagementAgent.getInstance(this).endActivity();

Vous devez appeler `endActivity()` au moins une fois quand l'utilisateur termine sa dernière activité. Cela indique au SDK Engagement que l'utilisateur est inactif et que la session utilisateur doit être fermée à la fin du délai d'expiration de session (si vous appelez `startActivity()` avant l'expiration de la session, la session est simplement reprise).

Le meilleur moment pour appeler cette fonction est à chaque rappel de l'activité `onPause` .

## <a name="reporting-events"></a>Rapports d'événements
### <a name="session-events"></a>Événements de session
Les événements de session servent généralement à signaler les actions effectuées par un utilisateur lors de sa session.

**Exemple sans données supplémentaires :**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**Exemple avec des données supplémentaires :**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>Événements autonomes
Contrairement aux événements de session, les événements autonomes peuvent se produire en dehors du contexte d'une session.

**Exemple :**

Supposons que vous voulez signaler les événements qui se produisent quand un récepteur de diffusion est déclenché :

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

## <a name="reporting-errors"></a>Rapports d'erreurs
### <a name="session-errors"></a>Erreurs de session
Les erreurs de session servent généralement à signaler les erreurs affectant l'utilisateur lors de sa session.

**Exemple :**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>Erreurs autonomes
Contrairement aux erreurs de session, les erreurs autonomes peuvent se produire en dehors du contexte d'une session.

**Exemple :**

L'exemple suivant montre comment signaler une erreur chaque fois que la mémoire est insuffisante sur le téléphone pendant l'exécution de votre application.

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

## <a name="reporting-jobs"></a>Rapports de travaux
### <a name="example"></a>Exemples
Supposons que vous voulez créer un rapport de la durée de votre processus de connexion :

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>Signaler les erreurs lors d'un travail
Les erreurs peuvent être associées à un travail en cours d'exécution plutôt qu'à la session utilisateur en cours.

**Exemple :**

Supposons que vous voulez signaler une erreur pendant votre connexion :

[...] public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>Rapports d'événements pendant une tâche
Les événements peuvent être associés à un travail en cours d'exécution au lieu de se rapporter à la session utilisateur en cours.

**Exemple :**

Supposons que nous disposons d'un réseau social et que nous utilisons une tâche pour signaler la durée totale pendant laquelle l'utilisateur est connecté au serveur. L'utilisateur peut rester connecté en arrière-plan même quand il utilise une autre application ou que le téléphone est en veille, dans ce cas, il n'y a pas de session.

L'utilisateur peut recevoir des messages de ses amis : il s'agit d'un événement de tâche.

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

## <a name="extra-parameters"></a>Paramètres supplémentaires
Des données arbitraires peuvent être associées à des événements, des erreurs, des activités et des tâches.

Ces données peuvent être structurées, elles utilisent la classe Bundle d'Android (en fait, elles font office de paramètres extras dans les Intents Android). Notez qu'une classe Bundle peut contenir des tableaux ou d'autres instances de classe Bundle.

> [!IMPORTANT]
> Si vous insérez les paramètres parcelables ou sérialisables, vérifiez que leur méthode `toString()` est implémentée de sorte à retourner une chaîne lisible par l'utilisateur. Les classes sérialisables qui contiennent des champs non temporaires non sérialisables entraînent le blocage d'Android à l'appel de `bundle.putSerializable("key",value);`
> 
> [!WARNING]
> Les matrices creuses dans les paramètres extras ne sont pas prises en charge, autrement dit, elles ne sont pas sérialisées sous forme de tableau. Vous devez les convertir en tableaux standard avant de les utiliser dans les paramètres extras.
> 
> 

### <a name="example"></a>Exemples
            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>limites
#### <a name="keys"></a>Clés
Chaque clé dans `Bundle` doit correspondre à l'expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*`

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### <a name="size"></a>Taille
Les paramètres extras sont limités à **1024** caractères par appel (une fois encodés au format JSON par le service Engagement).

Dans l'exemple précédent, le JSON envoyé au serveur fait 58 caractères :

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Rapports d'informations sur l'application
Vous pouvez signaler manuellement les informations de suivi (ou toutes autres informations spécifiques aux applications) à l'aide de la fonction `sendAppInfo()` .

Notez que ces informations peuvent être envoyées de façon incrémentielle : seule la dernière valeur d'une clé donnée sera conservée pour un périphérique donné.

À l'instar des paramètres extras des événements, la classe Bundle est utilisée pour récupérer les informations de l'application. Notez que les tableaux ou les sous-groupes seront considérés comme des chaînes plates (à l'aide de la sérialisation JSON).

### <a name="example"></a>Exemples
Voici un exemple de code pour envoyer des informations sur la date de naissance et le sexe de l'utilisateur :

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>limites
#### <a name="keys"></a>Clés
Chaque clé dans `Bundle` doit correspondre à l'expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*`

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### <a name="size"></a>Taille
Les informations de l'application sont limitées à **1024** caractères par appel (une fois encodées au format JSON par le service Engagement).

Dans l'exemple précédent, le JSON envoyé au serveur fait 44 caractères :

            {"expiration":"2016-12-07","status":"premium"}
