Vous pouvez désormais utiliser l’outil Explorateur de données dans le portail Azure pour créer une base de données et une table. 

1. Cliquez sur **Explorateur de données** > **Nouvelle table**. 
    
    La zone **Ajouter une table** est affichée à l’extrême droite. Il peut donc être nécessaire de faire défiler à droite pour l’afficher.

    ![Explorateur de données dans le portail Azure](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. Dans la page **Ajouter une table**, entrez les paramètres de la nouvelle table.

    Paramètre|Valeur suggérée|Description
    ---|---|---
    ID de la table|sample-database|L’ID de votre nouvelle table. Les noms de tables sont soumis aux mêmes exigences de nombre de caractères que les ID de bases de données. Les noms de base de données doivent inclure entre 1 et 255 caractères et ne peuvent pas contenir `/ \ # ?` ni d’espace de fin.
    Capacité de stockage| Fixe (10 Go)|Modifiez la valeur en sélectionnant **Fixe (10 Go)**. Cette valeur correspond à la capacité de stockage de la base de données.
    Throughput|400 unités de requête|Changez le débit en indiquant 400 unités de requête par seconde (RU/s). Si vous souhaitez réduire la latence, vous pourrez augmenter le débit par la suite.

    Cliquez sur **OK**.

    L’Explorateur de données affiche la nouvelle base de données et la nouvelle table.

    ![L’Explorateur de données du portail Azure, affichant la nouvelle base de données et la nouvelle collection](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)