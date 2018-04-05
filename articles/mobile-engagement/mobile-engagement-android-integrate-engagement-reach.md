---
title: Intégration du SDK Android d'Azure Mobile Engagement
description: Dernières mises à jour et procédures du SDK Android pour Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 9ec3fab3-35ec-458e-bf41-6cdd69e3fa44
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 06/27/2016
ms.author: piyushjo
ms.openlocfilehash: 15e71d8aeb8c8060161ade4262e0be0a093b5650
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-reach-on-android"></a>Comment intégrer le module de couverture Engagement sur Android
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

> [!IMPORTANT]
> Vous devez suivre la procédure d'intégration décrite dans le document « Comment intégrer Engagement sur Android » avant de suivre ce guide.
> 
> 

## <a name="standard-integration"></a>Intégration standard

Copiez les fichiers de ressources du module de couverture du SDK dans votre projet :

* Copiez les fichiers qui se trouvent dans le dossier `res/layout` fourni avec le SDK dans le dossier `res/layout` de votre application.
* Copiez les fichiers qui se trouvent dans le dossier `res/drawable` fourni avec le SDK dans le dossier `res/drawable` de votre application.

Modifiez le fichier `AndroidManifest.xml` :

* Ajoutez la section suivante (entre les balises `<application>` et `</application>`) :
  
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
              <category android:name="android.intent.category.DEFAULT" />
              <data android:mimeType="text/plain" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
              <category android:name="android.intent.category.DEFAULT" />
              <data android:mimeType="text/html" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
              <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
              <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
          </activity>
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
              <action android:name="android.intent.action.BOOT_COMPLETED"/>
              <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
              <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
          </receiver>
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
              <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
          </receiver>
* Vous avez besoin de cette autorisation pour afficher de nouveau les notifications système qui n'étaient pas activées au moment du démarrage (sinon elles seront conservées sur disque, mais ne s'afficheront plus).
  
          <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
* Spécifiez une icône utilisée pour les notifications (les notifications dans l'application et les notifications système) en copiant et en modifiant la section suivante (entre les balises `<application>` et `</application>`) :
  
          <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [!IMPORTANT]
> Cette section est **obligatoire** si vous prévoyez d'utiliser les notifications système lors de la création de campagnes de couverture. Android empêche les notifications système sans icône de s'afficher. Si vous omettez cette section, vos utilisateurs finaux ne pourront donc pas les recevoir.
> 
> 

* Si vous créez des campagnes avec notifications système qui utilisent BigPicture, vous devez ajouter les autorisations suivantes (après la balise `</application>` ) si elles sont absentes :
  
          <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
          <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
  
  * Sur Android M et si votre application cible un niveau d’API Android 23 ou supérieur, l’autorisation ``WRITE_EXTERNAL_STORAGE`` nécessite l’approbation de l’utilisateur. Veuillez lire [cette section](mobile-engagement-android-integrate-engagement.md#android-m-permissions).
* Pour les notifications système, vous pouvez également spécifier dans la couverture campagne si l'appareil doit sonner et/ou vibrer. Pour que cela fonctionne, vous devez déclarer l'autorisation suivante (après la balise `</application>` ) :
  
          <uses-permission android:name="android.permission.VIBRATE" />
  
  Sans cette autorisation, Android empêche les notifications système de s'afficher si vous avez activé l'option Sonner ou Vibrer dans le gestionnaire de couverture campagne.

## <a name="native-push"></a>Native Push
Maintenant que vous avez configuré le module Reach, vous devez configurer Native Push pour être en mesure de recevoir les campagnes sur l’appareil.

Nous prenons en charge les deux services sur Android :

* Appareils Google Play : utilisez [Google Cloud Messaging] en suivant le guide [Comment intégrer GCM à Mobile Engagement](mobile-engagement-android-gcm-integrate.md).
* Appareils Amazon : utilisez [Amazon Device Messaging] en suivant le guide [Comment intégrer ADM à Mobile Engagement](mobile-engagement-android-adm-integrate.md).

Si vous voulez cibler à la fois des appareils Amazon et Google Play, il est possible d'avoir tout ce qu'il faut pour le développement au sein d'un fichier AndroidManifest.xml/APK. Quand vous soumettez votre application à Amazon, celle-ci peut être rejetée si elle contient du code GCM.

Dans ce cas, vous devrez utiliser plusieurs fichiers APK.

**Votre application est maintenant prête à recevoir et afficher des couvertures campagnes.**

## <a name="how-to-handle-data-push"></a>Comment gérer les Push de données
### <a name="integration"></a>Intégration
Si vous voulez que votre application puisse recevoir des Push de données de couverture, vous devez créer une sous-classe de `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` et la référencer dans le fichier `AndroidManifest.xml` (entre les balises `<application>` et/ou `</application>`) :

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

Ensuite, vous pouvez remplacer les rappels `onDataPushStringReceived` et `onDataPushBase64Received`. Voici un exemple : 

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Catégorie
Le paramètre de catégorie est facultatif lorsque vous créez une campagne d'envoi de données (push) et vous permet de filtrer les envois de données. Ce paramètre est utile si vous possédez plusieurs récepteurs de diffusion qui gèrent différents types de Push de données, ou si vous voulez envoyer différents types de données `Base64` et identifier leur type avant de les analyser.

### <a name="callbacks-return-parameter"></a>Paramètre de retour des rappels
Voici quelques indications qui vous aideront à gérer correctement le paramètre de retour de `onDataPushStringReceived` et de `onDataPushBase64Received` :

* Un récepteur de diffusion doit retourner `null` dans le rappel s'il ne sait pas comment gérer un Push de données. Vous devez utiliser la catégorie pour déterminer si votre récepteur de diffusion doit gérer ou non le Push de données.
* L'un des récepteurs de diffusion doit retourner `true` dans le rappel s'il accepte le Push de données.
* L'un des récepteurs de diffusion doit retourner `false` dans le rappel s'il reconnaît le Push de données, mais l'ignore pour une raison quelconque. Par exemple, `false` est retourné quand les données reçues ne sont pas valides.
* Si un récepteur de diffusion retourne `true` alors que l'autre retourne `false` pour le même Push de données, cela signifie que le comportement n'est pas défini, ce qui est à éviter absolument.

Le type de retour est utilisé uniquement pour les statistiques de couverture :

* `Replied` est incrémenté si l'un des récepteurs de diffusion a retourné `true` ou `false`.
* `Actioned` est incrémenté uniquement si l'un des récepteurs de diffusion a retourné `true`.

## <a name="how-to-customize-campaigns"></a>Personnalisation des campagnes marketing
Pour personnaliser des campagnes, vous pouvez modifier les dispositions fournies dans le SDK du module couverture.

Vous devez conserver tous les identificateurs utilisés dans les dispositions et conserver les types de vues qui utilisent un identificateur, en particulier pour le texte et les images. Certaines vues sont uniquement utilisées pour masquer ou afficher des zones. Il est donc possible de modifier leur type. Vérifiez le code source si vous voulez modifier le type d'une vue dans les dispositions fournies.

### <a name="notifications"></a>Notifications
Il existe deux types de notifications : les notifications système et les notifications dans l'application qui utilisent des fichiers de disposition différents.

#### <a name="system-notifications"></a>Notifications système
Pour personnaliser les notifications système, vous devez utiliser les **catégories**. Vous pouvez accéder aux [catégories](#categories).

#### <a name="in-app-notifications"></a>Notifications dans l'application
Par défaut, une notification dans l'application est une vue qui est ajoutée dynamiquement à l'interface utilisateur de l'activité en cours grâce à la méthode Android `addContentView()`. Il s'agit d'une superposition de notification. Les superpositions de notification permettent une intégration rapide, car elles ne nécessitent pas la modification de la disposition de votre application.

Pour modifier l'apparence de vos superpositions de notification, vous pouvez simplement modifier le fichier `engagement_notification_area.xml` selon vos besoins.

> [!NOTE]
> Le fichier `engagement_notification_overlay.xml` est celui qui est utilisé pour créer une superposition de notification. Il comprend le fichier `engagement_notification_area.xml`. Vous pouvez également le personnaliser pour l'adapter à vos besoins (tels que le positionnement de la zone de notification au sein de la superposition).
> 
> 

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Inclure une disposition de notification dans une disposition d'activité
Les superpositions sont idéales pour une intégration rapide, mais peuvent être gênantes ou avoir des effets indésirables dans certains cas. Le système de superposition peut être personnalisé au niveau de l'activité, ce qui permet d'éviter les effets indésirables pour les activités spéciales.

Vous pouvez inclure notre disposition de notification dans votre disposition existante grâce à l'instruction Android **include** . Voici un exemple de disposition modifiée `ListActivity` contenant uniquement `ListView`.

**Avant l'intégration d'Engagement :**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Après l'intégration d'Engagement :**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

Dans cet exemple, nous avons ajouté un conteneur parent puisque la disposition d'origine utilisait une vue de liste comme élément de niveau supérieur. Nous avons également ajouté `android:layout_weight="1"` pour pouvoir ajouter une vue sous une vue de liste configurée avec `android:layout_height="fill_parent"`.

Le SDK du module de couverture d'Engagement détecte automatiquement que la disposition de notification est incluse dans cette activité et n'ajoutera pas de superposition pour cette activité.

> [!TIP]
> Si vous utilisez une ListActivity dans votre application, une superposition de couverture visible vous empêchera de réagir aux clics sur les éléments de la vue de liste. Il s'agit d'un problème connu. Pour contourner ce problème, nous vous suggérons d'intégrer la disposition de notification à votre disposition d'activité de liste, comme dans l'exemple précédent.
> 
> 

##### <a name="disabling-application-notification-per-activity"></a>Désactivation des notifications d'application par activité
Si vous ne voulez pas que la superposition soit ajoutée à votre activité, et si vous ne voulez pas inclure la disposition de notification dans votre propre disposition, vous pouvez désactiver la superposition pour cette activité dans `AndroidManifest.xml` en ajoutant une section `meta-data`, comme dans l'exemple suivant :

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a> Catégories
Quand vous modifiez les dispositions fournies, vous modifiez l'apparence de toutes vos notifications. Les catégories permettent de définir diverses recherches ciblées (et éventuellement des comportements) pour les notifications. Une catégorie peut être spécifiée lorsque vous créez une campagne Reach. N'oubliez pas que les catégories vous permettent également de personnaliser les annonces et les sondages, décrits plus avant dans ce document.

Pour inscrire un gestionnaire de catégories pour vos notifications, vous devez ajouter un appel quand l'application est initialisée.

> [!IMPORTANT]
> Lisez l’avertissement concernant l’attribut android:process \<android-sdk-engagement-process\> dans la rubrique « Comment intégrer Engagement sur Android » avant de continuer.
> 
> 

L'exemple suivant suppose que vous avez pris en compte l'avertissement précédent et que vous utilisez une sous-classe de `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

L'objet `MyNotifier` est l'implémentation du gestionnaire de catégories des notifications. Il s'agit soit d'une implémentation de l'interface `EngagementNotifier`, soit d’une sous-classe de l'implémentation par défaut : `EngagementDefaultNotifier`.

Notez que le même notificateur peut gérer plusieurs catégories. Vous pouvez les inscrire de la façon suivante :

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Pour remplacer l'implémentation de catégorie par défaut, vous pouvez inscrire votre implémentation comme dans l'exemple suivant :

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

La catégorie utilisée actuellement dans le gestionnaire est passée en tant que paramètre dans la plupart des méthodes que vous pouvez remplacer dans `EngagementDefaultNotifier`.

Elle est passée en tant que paramètre `String` ou indirectement dans un objet `EngagementReachContent` ayant une méthode `getCategory()`.

Vous pouvez modifier la plupart des processus de création de notifications en redéfinissant des méthodes dans `EngagementDefaultNotifier`. Pour une personnalisation avancée, vous pouvez consulter la documentation technique et le code source.

##### <a name="in-app-notifications"></a>Notifications dans l'application
Si vous voulez simplement utiliser d'autres dispositions pour une catégorie spécifique, vous pouvez implémenter ceci, comme dans l'exemple suivant :

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Exemple de `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Comme vous pouvez le voir, l'identificateur de la vue de superposition est différent de l'identificateur standard. Il est important que chaque disposition utilise un identificateur unique pour les superpositions.

**Exemple de `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Comme vous pouvez le voir, l'identificateur de vue de zone de notification est différent de l'identificateur standard. Il est important que chaque disposition utilise un identificateur unique pour les zones de notification.

Cet exemple simple de catégorie permet d'afficher les notifications d'application en haut de l'écran. Nous n'avons pas changé les identificateurs standard utilisés dans la zone de notification.

Si vous souhaitez les modifier, vous devez redéfinir la méthode `EngagementDefaultNotifier.prepareInAppArea` . Il est recommandé de consulter la documentation technique et le code source de `EngagementNotifier` et `EngagementDefaultNotifier` pour une personnalisation avancée.

##### <a name="system-notifications"></a>Notifications système
En étendant `EngagementDefaultNotifier`, vous pouvez remplacer `onNotificationPrepared` pour modifier la notification préparée par l'implémentation par défaut.

Par exemple : 

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

Cet exemple montre une notification système pour un contenu affiché sous la forme d'un événement en cours quand la catégorie « en cours » est utilisée.

Si vous voulez créer l'objet `Notification` à partir de zéro, vous pouvez retourner `false` à la méthode et appeler `notify` sur `NotificationManager`. Dans ce cas, il est important de conserver un `contentIntent`, un `deleteIntent` et l'identificateur de notification utilisé par `EngagementReachReceiver`.

Voici un exemple correct d'une telle implémentation :

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Annonces avec notifications uniquement
La gestion des clics sur les annonces avec notifications uniquement peut être personnalisée en remplaçant `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` pour modifier le `Intent` préparé. Cette méthode permet de paramétrer les indicateurs facilement.

Par exemple, pour ajouter l'indicateur `SINGLE_TOP` :

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

Pour les utilisateurs d'Engagement hérités, notez que les notifications système sans URL d'action lancent maintenant l'application si elle est en arrière-plan. Cette méthode peut donc être appelée avec une annonce sans URL d'action. Vous devez prendre cela en compte quand vous personnalisez l'intention.

Vous pouvez également implémenter `EngagementNotifier.executeNotifAnnouncementAction` à partir de zéro.

##### <a name="notification-life-cycle"></a>Cycle de vie des notifications
Lorsque vous utilisez la catégorie par défaut, certaines méthodes de cycle de vie sont appelées sur l'objet `EngagementReachInteractiveContent` pour communiquer les statistiques et mettre à jour l'état de la campagne :

* Lorsque la notification est affichée dans l'application ou placée dans la barre d'état, la méthode `displayNotification` est appelée (ce qui fournit les statistiques) par `EngagementReachAgent` si `handleNotification` retourne `true`.
* Si la notification est rejetée, la méthode `exitNotification` est appelée, les statistiques sont signalées et les campagnes suivantes peuvent alors être traitées.
* Si l'utilisateur clique sur la notification, `actionNotification` est appelé, les statistiques sont obtenues et l'intention correspondante est lancée.

Si votre implémentation de `EngagementNotifier` contourne le comportement par défaut, vous devez appeler ces méthodes de cycle de vie vous-même. Les exemples suivants illustrent certains cas dans lesquels le comportement par défaut est ignoré :

* Vous n'étendez pas `EngagementDefaultNotifier`, par exemple, vous avez implémenté la gestion des catégories à partir de zéro.
* Pour les notifications système, vous avez remplacé `onNotificationPrepared` et vous avez modifié `contentIntent` ou `deleteIntent` dans l'objet `Notification`.
* Pour les notifications dans l'application, vous avez remplacé `prepareInAppArea`. Veillez à mapper au moins `actionNotification` à l'un des contrôles d'interface utilisateur.

> [!NOTE]
> Si `handleNotification` lève une exception, le contenu est supprimé et `dropContent` est appelé. Ceci est signalé dans les statistiques et les campagnes suivantes peuvent être traitées.
> 
> 

### <a name="announcements-and-polls"></a>Annonces et sondages
#### <a name="layouts"></a>Mises en forme
Vous pouvez modifier les fichiers `engagement_text_announcement.xml`, `engagement_web_announcement.xml` et `engagement_poll.xml` pour personnaliser les annonces de texte, les annonces web et les sondages.

Ces fichiers partagent deux dispositions pour la zone de titre et la zone de bouton. La disposition du titre est `engagement_content_title.xml` et utilise le fichier dessinable éponyme pour l'arrière-plan. La disposition des boutons d'action et de sortie est `engagement_button_bar.xml` et utilise le fichier dessinable éponyme pour l'arrière-plan.

Dans un sondage, la disposition des questions et des choix de réponses est agrandie dynamiquement grâce à plusieurs utilisations du fichier de disposition `engagement_question.xml` pour les questions et du fichier `engagement_choice.xml` pour les choix de réponses.

#### <a name="categories"></a>Catégories
##### <a name="alternate-layouts"></a>Autres dispositions
Telles que les notifications, catégorie de la campagne peut être utilisée pour que les autres dispositions pour vos annonces et les sondages.

Par exemple, pour créer une catégorie pour une annonce de texte, vous pouvez étendre `EngagementTextAnnouncementActivity` et référencer le fichier `AndroidManifest.xml` :

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Notez que la catégorie du filtre d'intention est utilisée pour faire la différence avec l'activité d'annonce par défaut.

Le SDK du module de couverture utilise le système d'intentions pour résoudre l'activité appropriée pour une catégorie spécifique. En cas d'échec de la résolution, la catégorie par défaut est rétablie.

Vous devez ensuite implémenter `MyCustomTextAnnouncementActivity`. Si vous voulez simplement modifier la disposition (mais conserver les mêmes identificateurs de vue), il vous suffit de définir la classe comme dans l'exemple suivant :

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Pour remplacer la catégorie par défaut des annonces de texte, remplacez simplement `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` par votre implémentation.

Les annonces web et les sondages peuvent être personnalisés de la même manière.

Pour les annonces du web, vous pouvez étendre `EngagementWebAnnouncementActivity` et déclarer votre activité dans `AndroidManifest.xml`, comme dans l'exemple suivant :

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

Pour les sondages, vous pouvez étendre `EngagementPollActivity` et déclarer votre activité dans `AndroidManifest.xml`, comme dans l'exemple suivant :

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Implémentation à partir de zéro
Vous pouvez implémenter des catégories pour vos activités d'annonce (et de sondage) sans étendre l'une des classes `Engagement*Activity` fournies par le SDK du module de couverture. Cela se révèle utile, par exemple, si vous voulez définir une disposition qui n'utilise pas les mêmes vues que les dispositions standard.

Comme pour la personnalisation de notification avancée, il est recommandé d'examiner le code source de l'implémentation standard.

Voici quelques points à garder à l'esprit : le module de couverture lancera l'activité avec une intention spécifique (correspondant au filtre d'intention), plus un paramètre supplémentaire qui est l'identificateur de contenu.

Pour récupérer l'objet de contenu qui contient les champs que vous avez spécifiés lors de la création de la campagne sur le site web, vous pouvez effectuer ceci :

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

Pour les statistiques, vous devez signaler le contenu qui s'affiche dans l'événement `onResume` :

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Ensuite, n'oubliez pas d'appeler `actionContent(this)` ou `exitContent(this)` sur l'objet de contenu avant que l'activité n'aille en arrière-plan.

Si vous n'appelez pas `actionContent` ou `exitContent`, les statistiques ne seront pas envoyées (c'est-à-dire qu'il n'y aura pas d'analyse de la campagne). Plus important encore, les campagnes suivantes ne seront pas notifiées tant que le processus d'application n'aura pas redémarré.

Quand vous apportez des modifications concernant l'orientation ou d'autres modifications de configuration, il peut être difficile de déterminer si l'activité passe en arrière-plan ou non. L'implémentation standard garantit que le contenu est signalé comme fermé si l'utilisateur quitte l'activité (en appuyant sur `HOME` ou `BACK`), mais pas si l'orientation est modifiée.

Voici la partie la plus intéressante de l'implémentation :

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Comme vous pouvez le voir, si vous avez appelé `actionContent(this)` puis terminé l'activité, `exitContent(this)` peut être appelé sans que cela n'ait d'impact.

[here]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud Messaging]:http://developer.android.com/guide/google/gcm/index.html
[Amazon Device Messaging]:https://developer.amazon.com/sdk/adm.html
