1. Dans une nouvelle fenêtre du navigateur, connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Créer une ressource** > **Bases de données** > **Azure Cosmos DB**.
   
   ![Volet Bases de données du portail Azure](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-1.png)

3. Dans la page **Nouveau compte**, entrez les paramètres pour le nouveau compte Azure Cosmos DB. 
 
    Paramètre|Valeur suggérée|Description
    ---|---|---
    ID|*Entrez un nom unique*|Entrez un nom unique pour identifier ce compte Azure Cosmos DB. Étant donné que *documents.azure.com* est ajouté à l’ID que vous fournissez pour créer votre point de contact, utilisez un ID unique mais identifiable.<br><br>L’ID ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-), et doit comporter entre 3 et 50 caractères.
    API|Cassandra|L’API détermine le type de compte à créer. Azure Cosmos DB fournit cinq API pour répondre aux besoins de votre application : SQL (base de données Document), Gremlin (base de données de graphiques), MongoDB (base de données Document), Table Azure et Cassandra, qui nécessitent toutes un compte séparé. <br><br>Sélectionnez **Cassandra**, car dans ce démarrage rapide, vous créez une base de données à colonne large qui peut être interrogée avec la syntaxe CQL.<br><br>Si Cassandra (colonne large) n’est pas affiché dans la liste, vous devez [demander à participer](../articles/cosmos-db/cassandra-introduction.md#sign-up-now) au programme d’évaluation de l’API Cassandra.<br><br> [En savoir plus sur l’API Cassandra](../articles/cosmos-db/cassandra-introduction.md)|
    Abonnement|*Votre abonnement*|Sélectionnez l’abonnement Azure que vous voulez utiliser pour ce compte Azure Cosmos DB. 
    Groupe de ressources|*Entrez le même nom unique que celui fourni plus haut dans ID*|Entrez le nom du nouveau groupe de ressources pour votre compte. Pour plus de simplicité, vous pouvez utiliser le même nom que votre ID. 
    Lieu|*Sélectionner la région la plus proche de vos utilisateurs*|Sélectionnez l’emplacement géographique où héberger votre compte Azure Cosmos DB. Utilisez l’emplacement le plus proche de vos utilisateurs, pour leur donner l’accès le plus rapide possible aux données.
    Épingler au tableau de bord | Sélectionnez | Cochez cette case pour que votre nouveau compte de base de données soit ajouté à votre tableau de bord du portail pour un accès facilité.

    Cliquez ensuite sur **Créer**.

    ![Panneau de nouveau compte pour Azure Cosmos DB](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-2.png)

4. La création du compte prend quelques minutes. Pendant la création du compte, le tableau de bord du portail affiche la vignette **Déploiement d’Azure Cosmos DB**.

    ![Vignette Notifications du portail Azure](./media/cosmos-db-create-dbaccount-cassandra/deploying-cosmos-db.png)

    Une fois que le compte est créé, la page **Félicitations ! Votre compte Azure Cosmos DB a été créé** s’affiche. 

