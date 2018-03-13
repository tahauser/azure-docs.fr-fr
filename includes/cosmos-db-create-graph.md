Vous pouvez désormais utiliser l’outil Explorateur de données dans le portail Azure pour créer une base de données de graphiques. 

1. Dans le menu de gauche du portail Azure, sélectionnez **Explorateur de données (préversion)**.

2. Sous **Explorateur de données (préversion)**, sélectionnez **New Graph** (Nouveau graphique). Remplissez ensuite la page en utilisant les informations suivantes :

    ![Explorateur de données dans le portail Azure](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer.png)

    Paramètre|Valeur suggérée|Description
    ---|---|---
    ID de base de données|sample-database|Entrez le nom *sample-database* pour la nouvelle base de données. Les noms de bases de données doivent inclure entre 1 et 255 caractères et ne peuvent pas contenir `/ \ # ?`, ni d’espace de fin.
    ID de graphique|sample-graph|Entrez le nom *sample-graph* pour votre nouvelle collection. Les noms de graphiques sont soumis aux mêmes exigences de nombre de caractères que les ID de bases de données.
    Capacité de stockage| 10 Go|Conservez la valeur par défaut. Il s’agit de la capacité de stockage de la base de données.
    Débit|400 unités de requête|Conservez la valeur par défaut. Vous pourrez augmenter le débit ultérieurement si vous souhaitez réduire la latence.

3. Une fois le formulaire rempli, sélectionnez **OK**.
