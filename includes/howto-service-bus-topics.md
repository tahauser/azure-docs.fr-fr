## <a name="what-are-service-bus-topics-and-subscriptions"></a>Présentation des rubriques et des abonnements Service Bus
Les rubriques et les abonnements Service Bus prennent en charge un modèle de communication de messagerie *de publication et d'abonnement* . Lors de l’utilisation de rubriques et d’abonnements, les composants d’une application distribuée ne communiquent pas directement entre eux ; ils échangent plutôt des messages via une rubrique, qui fait office d’intermédiaire.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

Contrairement aux files d’attente Service Bus, où chaque message est traité par un seul consommateur, les rubriques et les abonnements offrent une forme de communication « un-à-plusieurs » à l’aide d’un modèle de publication et d’abonnement. Il est possible d’inscrire plusieurs abonnements à une rubrique. Lorsqu’un message est envoyé à une rubrique, il est alors mis à disposition de chaque abonnement pour être géré ou traité indépendamment.

Un abonnement à une rubrique ressemble à une file d’attente virtuelle qui reçoit des copies des messages envoyés à la rubrique. Si vous le souhaitez, vous pouvez inscrire des règles de filtre pour une rubrique par abonnement. Les règles de filtre vous permettent de filtrer ou de restreindre les messages d’une rubrique reçus en fonction des abonnements à une rubrique.

Les rubriques et les abonnements Service Bus vous donnent la possibilité de procéder à une mise à l’échelle et de traiter un grand nombre de messages parmi de nombreux utilisateurs et applications.

## <a name="create-a-namespace"></a>Créer un espace de noms
Pour commencer à utiliser les rubriques et les abonnements Service Bus dans Azure, vous devez d’abord créer un *espace de noms de service*. Ce dernier fournit un conteneur d'étendue pour l'adressage des ressources Service Bus au sein de votre application.

Pour créer un espace de noms :

1. Connectez-vous au [portail Azure][Azure portal].
2. Dans le volet de navigation de gauche du portail, cliquez sur **Créer une ressource**, sur **Intégration Entreprise**, puis sur **Service Bus**.
3. Dans la boîte de dialogue **Créer un espace de noms** , entrez un nom d’espace de noms. Le système vérifie immédiatement si le nom est disponible.
4. Lorsque vous avez vérifié la disponibilité de l’espace de noms, sélectionnez le niveau tarifaire (Basique, Standard ou Premium).
5. Dans le champ **Abonnement** , sélectionnez un abonnement Azure dans lequel créer l’espace de noms.
6. Dans le champ **Groupe de ressources**, choisissez un groupe de ressources existant dans lequel l’espace de noms est utilisé, ou créez-en un.      
7. Dans **Emplacement**, sélectionnez le pays ou la région où votre espace de noms doit être hébergé.
   
    ![Créer un espace de noms][create-namespace]
8. Cliquez sur le bouton **Créer** . Le système crée l'espace de noms de service et l'active. Vous devrez peut-être attendre plusieurs minutes afin que le système approvisionne des ressources pour votre compte.

### <a name="obtain-the-credentials"></a>Obtenir les informations d’identification
1. Dans la liste des espaces de noms, cliquez sur le nom de l’espace de noms que vous venez de créer.
2. Dans le volet **Espace de noms Service Bus**, cliquez sur **Stratégies d’accès partagé**.
3. Dans le volet **Stratégies d’accès partagé**, cliquez sur **RootManageSharedAccessKey**.
   
    ![informations de connexion][connection-info]
4. Dans le volet **Stratégie : RootManageSharedAccessKey**, cliquez sur le bouton de copie situé en regard de **Clé primaire de la chaîne de connexion** pour copier la chaîne de connexion dans le Presse-papiers à des fins d’utilisation ultérieure.
   
    ![connection-string][connection-string]

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png


