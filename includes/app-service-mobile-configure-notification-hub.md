La fonctionnalité Mobile Apps d’Azure App Service utilise [Azure Notification Hubs] pour envoyer des notifications Push ; vous allez donc devoir configurer un hub de notification pour votre application mobile.

1. Dans le [portail Azure], accédez à **App Services**, puis sélectionnez votre serveur principal d’applications. Sous **Paramètres**, sélectionnez **Notifications push**.
2. Pour ajouter une ressource de Hub de notification à l’application, sélectionnez **Connecter**. Vous pouvez créer un concentrateur ou vous connecter à un autre existant.

    ![Configurer un Hub](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Vous avez désormais connecté un hub de notification à votre projet de serveur principal Mobile Apps. Plus tard, vous allez configurer ce Hub de notification pour qu’il se connecte à un système PNS (Platform Notification System) qui envoie des notifications Push aux appareils.

[portail Azure]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/
