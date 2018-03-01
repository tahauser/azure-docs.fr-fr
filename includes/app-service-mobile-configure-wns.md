
1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez **Parcourir tout** > **App Services**. Puis sélectionnez votre serveur principal Mobile Apps. Sous **Paramètres**, sélectionnez **App Service Push** (Notification Push App Service). Ensuite, sélectionnez le nom de votre Hub de notification.
2. Accédez à **Windows (WNS)**. Ensuite, entrez la **Clé de sécurité** (clé secrète client) et le **SID du package** que vous avez obtenus à partir du site des services Microsoft Live. Puis sélectionnez **Enregistrer**.

    ![Définition de la clé WNS dans le portail](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Votre serveur principal est désormais configuré pour utiliser WNS afin d’envoyer des notifications Push.
