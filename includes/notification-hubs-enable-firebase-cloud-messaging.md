

1. Connectez-vous à la [console Firebase](https://firebase.google.com/console/). Créer un nouveau projet Firebase si vous n’en avez pas encore.
2. Après avoir créé votre projet, cliquez sur **Add Firebase to your Android app** (Ajouter Firebase à votre application Android) et suivez les instructions fournies.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Dans la console Firebase, cliquez sur la roue dentée pour votre projet, puis sur **Project Settings (Paramètres du projet)**.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Cliquez sur l’onglet **Général** dans les paramètres de votre projet, puis téléchargez le fichier **google-services.json** contenant la clé d’API de serveur et l’ID client.
5. Cliquez sur l’onglet **Messagerie cloud (Cloud Messaging)** dans les paramètres de votre projet, puis copiez la valeur des champs **Legacy Server key (Clé serveur héritée)**. Cette valeur sera utilisée pour configurer la stratégie d’accès du hub de notification.
