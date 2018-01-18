## <a name="create-a-storage-account-by-using-the-azure-portal"></a>Créer un compte de stockage à l’aide du portail Azure

Commencez par créer un compte de stockage à usage général pour les besoins de ce guide de démarrage rapide. 

1. Accédez au [portail Azure](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) et connectez-vous avec votre compte Azure. 
2. Entrez un nom unique pour votre compte de stockage. Gardez les règles suivantes à l’esprit lorsque vous nommez votre compte de stockage :
    - Le nom doit compter 3 à 24 caractères.
    - Le nom peut contenir uniquement des chiffres et des lettres minuscules.
3. Vérifiez que les valeurs par défaut suivantes sont définies : 
    - **Modèle de déploiement** est défini sur **Resource Manager**.
    - **Type de compte** est défini sur **Usage général**.
    - **Performances** est défini sur **Standard**.
    - **Réplication** est défini sur **Stockage localement redondant (LRS)**.
4. Sélectionnez votre abonnement. 
5. Pour **Groupe de ressources**, créez-en un et donnez-lui un nom unique. 
6. Sélectionnez l’emplacement à utiliser pour votre compte de stockage.
7. Sélectionnez **Épingler au tableau de bord** et **Créer** pour créer votre compte de stockage. 

Une fois votre compte de stockage créé, il est épinglé au tableau de bord. Sélectionnez-le pour l’ouvrir. Sous **Paramètres**, sélectionnez **Clés d’accès**. Sélectionnez la clé primaire et copiez la chaîne de connexion associée dans le Presse-papiers. Collez ensuite la chaîne dans un éditeur de texte pour l’utiliser plus tard.
