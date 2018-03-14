## <a name="prerequisites"></a>Prérequis

### <a name="azure-subscription"></a>Abonnement Azure
Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

### <a name="azure-roles"></a>Rôles Azure
Pour créer des instances Data Factory, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être membre des rôles *Contributeur* ou *Propriétaire*, ou *administrateur* de l’abonnement Azure. Dans le portail Azure, cliquez sur votre nom d’utilisateur dans le coin supérieur droit, puis sélectionnez **Autorisations** pour afficher les autorisations dont vous disposez dans l’abonnement. Si vous avez accès à plusieurs abonnements, sélectionnez l’abonnement approprié. Pour des exemples d’instructions concernant l’ajout d’un utilisateur à un rôle, consultez l’article [Ajout de rôles](../articles/billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Compte Azure Storage
Dans ce guide de démarrage rapide, vous allez utiliser un compte Stockage Azure (un compte Stockage Blob, plus précisément) à usage général à la fois comme magasin de données *source* et de *destination*. Si vous ne possédez pas de compte Stockage Azure à usage général, consultez [Créer un compte de stockage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) pour en créer un. 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obtenir le nom de compte de stockage et la clé de compte
Dans ce guide de démarrage rapide, vous spécifiez le nom et la clé de votre compte Stockage Azure. La procédure suivante détaille les étapes à suivre pour obtenir le nom et la clé de votre compte de stockage : 

1. Dans un navigateur web, accédez au [portail Azure](https://portal.azure.com). Connectez-vous avec votre nom d’utilisateur et votre mot de passe Azure. 
2. Sélectionnez **Plus de services** dans le menu de gauche, filtrez en utilisant le mot clé **Stockage**, puis sélectionnez **Comptes de stockage**.

   ![Rechercher un compte de stockage](media/data-factory-quickstart-prerequisites/search-storage-account.png)
3. Dans la liste des comptes de stockage, appliquez un filtre pour votre compte de stockage (si nécessaire), puis sélectionnez votre compte de stockage. 
4. Sur la page **Compte de stockage**, sélectionnez **Clés d’accès** dans le menu.

   ![Obtenir le nom et la clé du compte de stockage](media/data-factory-quickstart-prerequisites/storage-account-name-key.png)
5. Copiez les valeurs des champs **Nom du compte de stockage** et **key1** dans le presse-papiers. Collez-les dans un bloc-notes ou tout autre éditeur et enregistrez le fichier. Vous les utiliserez ultérieurement dans ce guide de démarrage rapide.   

#### <a name="create-the-input-folder-and-files"></a>Créer les fichiers et le dossier d’entrée
Dans cette section, vous allez créer un conteneur d’objets blob nommé **adftutorial** dans un stockage Blob Azure. Ensuite, vous créerez un dossier nommé **input** (entrée) dans le conteneur et chargerez un exemple de fichier dans ce dossier. 

1. Sur la page **Compte de stockage**, basculez vers **Vue d’ensemble**, puis sélectionnez **Objets blob**. 

   ![Sélection de l’option Objets blob](media/data-factory-quickstart-prerequisites/select-blobs.png)
2. Dans la page **Service BLOB**, sélectionnez **+ Conteneur** dans la barre d’outils. 

   ![Bouton d’ajout de conteneur](media/data-factory-quickstart-prerequisites/add-container-button.png)    
3. Dans la boîte de dialogue **Nouveau conteneur**, saisissez le nom **adftutorial**, puis sélectionnez **OK**. 

   ![Saisie du nom du conteneur](media/data-factory-quickstart-prerequisites/new-container-dialog.png)
4. Sélectionnez **adftutorial** dans la liste des conteneurs. 

   ![Sélection du conteneur](media/data-factory-quickstart-prerequisites/seelct-adftutorial-container.png)
1. Sur la page **Conteneur**, sélectionnez **Charger** dans la barre d’outils.  

   ![Bouton Télécharger](media/data-factory-quickstart-prerequisites/upload-toolbar-button.png)
6. Sur la page **Charger l’objet blob**, sélectionnez **Avancé**.

   ![Sélectionner le lien Avancé](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
7. Ouvrez le **Bloc-notes** et créez un fichier nommé **emp.txt** avec le contenu suivant. Enregistrez-le dans le dossier **c:\ADFv2QuickStartPSH**. S’il n’existe pas déjà, créez le dossier **ADFv2QuickStartPSH**.
    
   ```
   John, Doe
   Jane, Doe
   ```    
8. Dans le portail Azure, sur la page **Charger l’objet blob**, recherchez et sélectionnez le fichier **emp.txt** pour le champ **Fichiers**. 
9. Entrez **input** dans le champ **Charger dans le dossier**. 

    ![Paramètres de chargement de l’objet blob](media/data-factory-quickstart-prerequisites/upload-blob-settings.png)    
10. Vérifiez que le dossier est **input** et que le fichier est **emp.txt**, puis sélectionnez **Charger**.
    
    Vous devriez voir le fichier **emp.txt** et l’état du chargement dans la liste. 
12. Fermez la page **Charger l’objet blob** en cliquant sur **X** en haut à droite. 

    ![Fermeture de la page Charger l’objet blob](media/data-factory-quickstart-prerequisites/close-upload-blob.png)
1. Laissez la page **Conteneur** ouverte. Vous l’utiliserez pour vérifier la sortie à la fin de ce guide de démarrage rapide.