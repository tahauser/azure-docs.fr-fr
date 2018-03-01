

1. Sur votre Mac, démarrez **Trousseau d’accès**. Dans la barre de navigation gauche, sous **Catégorie**, ouvrez **Mes certificats**. Recherchez le certificat SSL que vous avez téléchargé à la section précédente, puis affichez son contenu. Sélectionnez uniquement le certificat (sans la clé privée). Puis [exportez-le](https://support.apple.com/kb/PH20122?locale=en_US).
2. Dans le [portail Azure](https://portal.azure.com/), sélectionnez **Parcourir tout** > **App Services**. Puis sélectionnez votre serveur principal Mobile Apps. 
3. Sous **Paramètres**, sélectionnez **App Service Push** (Notification Push App Service). Ensuite, sélectionnez le nom de votre Hub de notification. 
3. Accédez à **Services de notifications Push Apple** > **Télécharger le certificat**. Chargez le fichier .p12 en sélectionnant le **mode** approprié (en fonction du type de votre certificat SSL client précédemment utilisé : Production ou Sandbox). Enregistrez les modifications.

Votre service est désormais configuré et prêt à fonctionner avec les notifications Push sur iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
