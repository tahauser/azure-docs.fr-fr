---
title: "Copier des données depuis SQL Server vers le stockage Blob à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment copier les données d’un magasin de données local dans le cloud Azure en utilisant le runtime d’intégration auto-hébergé d’Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: a2abe0733f52c1e032a718fd8f870c3ec9686a41
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Didacticiel : Copier des données depuis une base de données SQL Server locale vers un compte de stockage d’objets blob Azure
Dans ce didacticiel, vous allez utiliser Azure PowerShell pour créer un pipeline Data Factory qui copie les données d’une base de données SQL Server locale dans un stockage Blob Azure. Vous allez créer et utiliser un runtime d’intégration auto-hébergé, qui déplace les données entre les banques de données locales et cloud. 

> [!NOTE]
> Cet article s’applique à la version 2 d’Azure Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Cet article ne fournit pas de présentation détaillée du service Data Factory. Pour plus d’informations, consultez [Présentation d’Azure Data Factory](introduction.md). 

Dans ce didacticiel, vous effectuerez les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créez un runtime d’intégration auto-hébergé.
> * Créer des services liés SQL Server et au Stockage Azure. 
> * Créer des jeux de données SQL Server et Blob Azure.
> * Créer un pipeline avec une activité de copie pour déplacer les données.
> * Démarrer une exécution de pipeline.
> * Surveiller l’exécution du pipeline.

## <a name="prerequisites"></a>Prérequis
### <a name="azure-subscription"></a>Abonnement Azure
Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

### <a name="azure-roles"></a>Rôles Azure
Pour créer des instances de fabrique de données, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être un membre des rôles *contributeur* ou *propriétaire*, ou un *administrateur* de l’abonnement Azure. 

Dans le portail Azure, cliquez sur votre nom d’utilisateur dans le coin supérieur droit, puis sélectionnez **Autorisations** pour afficher les autorisations dont vous disposez dans l’abonnement. Si vous avez accès à plusieurs abonnements, sélectionnez l’abonnement approprié. Pour des exemples d’instructions concernant l’ajout d’un utilisateur à un rôle, consultez l’article [Ajout de rôles](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 et 2017
Dans le cadre de ce didacticiel, vous utilisez une base de données SQL Server locale comme magasin de données *source*. Le pipeline de la fabrique de données que vous créez dans ce didacticiel copie les données de cette base de données SQL Server locale (source) dans un stockage Blob Azure (récepteur). Créez ensuite une table nommée **emp** dans votre base de données SQL Server, puis insérez quelques exemples d’entrées dans la table. 

1. Exécutez SQL Server Management Studio. S’il n’est pas déjà installé sur votre machine, accédez à [Télécharger SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Connectez-vous à votre instance SQL Server à l’aide de vos informations d’identification. 

3. Créez un exemple de base de données. Dans l’arborescence, cliquez avec le bouton droit sur **Bases de données**, puis sur **Nouvelle base de données**. 
 
4. Dans la fenêtre **Nouvelle base de données**, entrez un nom pour la base de données, puis cliquez sur **OK**. 

5. Exécutez le script de requête suivant sur la base de données pour créer la table **emp** et y insérer quelques données d’exemple :

   ```
       INSERT INTO emp VALUES ('John', 'Doe')
       INSERT INTO emp VALUES ('Jane', 'Doe')
       GO
   ```

6. Dans l’arborescence, cliquez avec le bouton droit sur la base de données créée, puis sur **Nouvelle requête**.

### <a name="azure-storage-account"></a>Compte de Stockage Azure
Dans ce didacticiel, vous utilisez un compte Stockage Azure à usage général (stockage d’objets Blob Azure spécifiquement) comme banque de données réceptrice/de destination. Si vous ne possédez pas de compte Stockage Azure à usage général, consultez [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account). Le pipeline de la fabrique de données que vous créez dans ce didacticiel copie les données de la base de données SQL Server locale (source) dans ce stockage Blob Azure (récepteur). 

#### <a name="get-storage-account-name-and-account-key"></a>Obtenir le nom de compte de stockage et la clé de compte
Dans ce didacticiel, vous spécifiez le nom et la clé de votre compte Stockage Azure. Procédez comme suit pour obtenir le nom et la clé de votre compte de stockage : 

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec vos nom d’utilisateur et mot de passe Azure. 

2. Dans le volet gauche, sélectionnez **Plus de services**, filtrez à l’aide du mot-clé **Stockage**, puis sélectionnez **Comptes de stockage**.

    ![Rechercher le compte de stockage](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. Dans la liste des comptes de stockage, appliquez un filtre pour votre compte de stockage (si nécessaire), puis sélectionnez votre compte de stockage. 

4. Dans la fenêtre **Compte de stockage**, sélectionnez **Clés d’accès**.

    ![Obtenir le nom et la clé du compte de stockage](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Dans les zones **Nom du compte de stockage** et **key1**, copiez les valeurs, puis collez-les dans le bloc-notes ou un autre éditeur pour une utilisation ultérieure dans le didacticiel. 

#### <a name="create-the-adftutorial-container"></a>Créer le conteneur adftutorial 
Dans cette section, vous allez créer un conteneur d’objets blob nommé **adftutorial** dans votre stockage Blob Azure. 

1. Dans la fenêtre **Compte de stockage**, basculez vers **Vue d’ensemble**, puis sélectionnez **Objets blob**. 

    ![Sélection de l’option Objets blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. Dans la fenêtre **Service Blob**, sélectionnez **Conteneur**. 

    ![Bouton d’ajout de conteneur](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. Dans la fenêtre **Nouveau conteneur**, dans la zone **Nom** , entrez **adftutorial**, puis sélectionnez **OK**. 

    ![Saisie du nom du conteneur](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Cliquez sur **adftutorial** dans la liste des conteneurs.  

    ![Sélection du conteneur](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Gardez la fenêtre **conteneur** de **adftutorial** ouverte. Elle vous permet de vérifier la sortie à la fin du didacticiel. Data Factory crée automatiquement le dossier de sortie de ce conteneur, de sorte que vous n’avez pas besoin d’en créer.

    ![Fenêtre de conteneur](media/tutorial-hybrid-copy-powershell/container-page.png)

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Installation d'Azure PowerShell
Installez la dernière version d’Azure PowerShell, si elle n’est pas installée sur votre machine. 

1. Accédez à [Téléchargements du Kit de développement logiciel Azure (SDK)](https://azure.microsoft.com/downloads/). 

2. Sous **Outils de ligne de commande**, dans la section **PowerShell**, sélectionnez **Installation Windows**. 

3. Pour installer Azure PowerShell, exécutez le fichier MSI. 

Pour des instructions détaillées, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Se connecter à PowerShell

1. Démarrez PowerShell sur votre machine et laissez-le ouvert jusqu’à la fin de ce didacticiel de démarrage rapide. Si vous le fermez, puis le rouvrez, vous devez réexécuter ces commandes.

    ![Démarrer PowerShell](media/tutorial-hybrid-copy-powershell/search-powershell.png)

2. Exécutez la commande suivante, puis saisissez le nom d’utilisateur et le mot de passe Azure que vous utilisez pour la connexion au portail Azure :
       
    ```powershell
    Login-AzureRmAccount
    ```        

3. Si vous avez plusieurs abonnements Azure, exécutez la commande suivante pour sélectionner l’abonnement avec lequel vous souhaitez travailler. Remplacez **SubscriptionId** par l’ID de votre abonnement Azure :

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Définissez une variable pour le nom du groupe de ressources que vous utiliserez ultérieurement dans les commandes PowerShell. Copiez la commande suivante dans PowerShell, spécifiez un nom pour le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) (entre guillemets doubles, par exemple `"adfrg"`), puis exécutez la commande. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

2. Pour créer le groupe de ressources Azure, exécutez la commande suivante : 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Si le groupe de ressources existe déjà, vous pouvez ne pas le remplacer. Affectez une valeur différente à la variable `$resourceGroupName` et exécutez à nouveau la commande.

3. Définissez une variable pour le nom de la fabrique de données que vous pourrez utiliser dans les commandes PowerShell plus tard. Les noms doivent commencer par une lettre ou un chiffre, et peuvent comporter uniquement des lettres, des chiffres et des tirets (-).

    > [!IMPORTANT]
    >  Mettez à jour le nom de la fabrique de données afin qu’il soit globalement unique. Par exemple, ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

4. Définissez une variable pour l’emplacement de la fabrique de données : 

    ```powershell
    $location = "East US"
    ```  

5. Exécutez l’applet de commande `Set-AzureRmDataFactoryV2` suivante pour créer la fabrique de données : 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

> [!NOTE]
> 
> * Le nom de la fabrique de données doit être un nom global unique. Si vous recevez l’erreur suivante, changez le nom, puis réessayez.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Pour créer des instances de fabrique de données, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être un membre des rôles *contributeur* ou *propriétaire*, ou un *administrateur* de l’abonnement Azure.
> * À l’heure actuelle, Data Factory version 2 vous permet de créer des fabriques de données uniquement dans les régions Est des États-Unis, Est des États-Unis 2 et Europe de l’Ouest. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (Azure HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.
> 
> 

## <a name="create-a-self-hosted-integration-runtime"></a>Créer un runtime d’intégration auto-hébergé

Dans cette section, vous allez créer un runtime d’intégration auto-hébergé et l’associer à un ordinateur local avec la base de données SQL Server. Le runtime d’intégration auto-hébergé est le composant qui copie les données de la base de données SQL Server sur votre machine dans le stockage Blob Azure. 

1. Créez une variable pour le nom du runtime d’intégration. Utilisez un nom unique, et notez-le. Vous l’utiliserez ultérieurement dans ce didacticiel. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

2. Créez un runtime d’intégration auto-hébergé. 

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ``` 
    Voici l'exemple de sortie :

    ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       : selfhosted IR description
    ```

3. Exécutez la commande suivante pour récupérer l’état du runtime d’intégration créé :

    ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
    ```

    Voici l'exemple de sortie :
    
    ```json
    Nodes                     : {}
    CreateTime                : 9/14/2017 10:01:21 AM
    InternalChannelEncryption :
    Version                   :
    Capabilities              : {}
    ScheduledUpdateDate       :
    UpdateDelayOffset         :
    LocalTimeZoneOffset       :
    AutoUpdate                :
    ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
    ResourceGroupName         : <ResourceGroup name>
    DataFactoryName           : <DataFactory name>
    Name                      : <Integration Runtime name>
    State                     : NeedRegistration
    ```

4. Exécutez la commande suivante pour récupérer les *clés d’authentification* permettant d’enregistrer le runtime d’intégration auto-hébergé auprès du service de fabrique de données dans le cloud. Copiez l’une des clés (sans les guillemets) pour enregistrer le runtime d’intégration auto-hébergé que vous allez installer sur votre ordinateur à l’étape suivante. 

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
    ```
    
    Voici l'exemple de sortie :
    
    ```json
    {
        "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
        "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
    }
    ```

## <a name="install-the-integration-runtime"></a>Installer le runtime d’intégration
1. Téléchargez le [runtime d’intégration Azure Data Factory](https://www.microsoft.com/download/details.aspx?id=39717) sur un ordinateur Windows local, puis exécutez l’installation. 

2. Sur la page **Bienvenue dans l’assistant Installation de Microsoft Integration Runtime**, cliquez sur **Suivant**.  

3. Dans la fenêtre **Contrat de licence utilisateur final**, acceptez les conditions et le contrat de licence, puis cliquez sur **Suivant**. 

4. Dans la fenêtre **Dossier de destination**, cliquez sur **Suivant**. 

5. Dans la fenêtre **Prêt à installer Microsoft Integration Runtime**, cliquez sur **Installer**. 

6. Si un message d’avertissement indiquant que l’ordinateur en cours de configuration passera en mode veille ou veille prolongée en cas de non utilisation, s’affiche, cliquez sur **OK**. 

7. Si la fenêtre **Options d’alimentation** s’affiche, fermez-la et basculez vers la fenêtre d’installation. 

8. Dans l’**Assistant Installation de Microsoft Integration Runtime terminé**, cliquez sur **Terminer**.

9. Dans la fenêtre **Inscrire Microsoft Integration Runtime (auto-hébergé)**, collez la clé que vous avez enregistrée dans la section précédente, puis cliquez sur **Inscrire**. 

    ![Inscrire le runtime d’intégration](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

    Le message suivant s’affiche une fois que le runtime d’intégration auto-hébergé est bien inscrit : 

    ![Inscription réussie](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

10. Dans la fenêtre **Nouveau Integration Runtime (auto-hébergé)**, cliquez sur **suivant**. 

    ![Fenêtre Nouveau nœud Integration Runtime](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

11. Dans la fenêtre **Canal de communication Intranet**, cliquez sur **Ignorer**.  
    Vous pouvez sélectionner une certification TLS/SSL pour sécuriser les communications intra-nœud dans un environnement de runtime d’intégration à plusieurs nœuds.

    ![Fenêtre du canal de communication Intranet](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)

12. Dans la fenêtre **Inscrire Microsoft Integration Runtime (auto-hébergé)**, cliquez sur **Lancer Configuration Manager**. 

13. Le message suivant apparaît une fois que le nœud est connecté au service cloud :

    ![Le nœud est connecté](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

14. Testez la connectivité à votre base de données SQL Server en procédant comme suit :

    ![Onglet Diagnostic](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    a. Dans la fenêtre **Gestionnaire de configuration**, basculez vers l’onglet **Diagnostics**.

    b. Dans la zone **Type de source de données**, sélectionnez **SqlServer**.

    c. Saisissez le nom du serveur.

    d. Saisissez le nom de la base de données. 

    e. Sélectionnez le mode d’authentification. 

    f. Saisissez le nom d’utilisateur. 

    g. Entrez le mot de passe associé au nom d’utilisateur.

    h. Cliquez sur **Tester** pour vérifier que le runtime d’intégration se connecte à SQL Server.  
    Une coche verte apparaît si la connexion est établie. Sinon, c’est un message d’erreur concernant l’échec qui apparaît. Corrigez les problèmes et assurez-vous que le runtime d’intégration peut se connecter à votre instance SQL Server.

    Notez toutes les valeurs précédentes pour une utilisation ultérieure dans ce didacticiel.
    
## <a name="create-linked-services"></a>Créez des services liés
Créez des services liés dans la fabrique de données pour lier vos magasins de données et vos services de calcul à la fabrique de données. Dans ce didacticiel, vous liez votre compte Stockage Azure et votre instance SQL Server locale à la banque de données. Les services liés comportent les informations de connexion utilisées par le service de fabrique de données lors de l’exécution pour s’y connecter. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Créer un service lié Stockage Azure (destination/réception)
Dans cette étape, vous liez votre compte Stockage Azure à la fabrique de données.

1. Créez un fichier JSON sous le nom *AzureSqlDWLinkedService.json* dans le dossier *C:\ADFv2Tutorial* avec le code suivant. Créez le dossier *ADFv2Tutorial* s’il n’existe pas déjà.  

    > [!IMPORTANT]
    > Remplacez \<accountName> et \<accountKey> par le nom et la clé de votre compte de stockage Azure avant d’enregistrer le fichier. Vous les avez notées dans la section [Conditions préalables](#get-storage-account-name-and-account-key).

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>;EndpointSuffix=core.windows.net"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. Dans PowerShell, accédez au dossier *C:\ADFv2Tutorial*.

3. Exécutez l’applet de commande `Set-AzureRmDataFactoryV2LinkedService` suivante pour créer le service lié AzureStorageLinkedService : 

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Voici un exemple de sortie :

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

    Si vous recevez une erreur indiquant « fichier introuvable », vérifiez que le fichier existe en exécutant la commande `dir`. Si le nom du fichier a l’extension *.txt* (par exemple, AzureStorageLinkedService.json.txt), supprimez-la puis exécutez la commande PowerShell à nouveau. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Créer et chiffrer un service lié SQL Server (source)
Dans cette étape, vous liez votre instance SQL Server locale à la fabrique de données.

1. Créez un fichier JSON sous le nom *SqlServerLinkedService.json* dans le dossier *C:\ADFv2Tutorial* avec le code suivant :

    > [!IMPORTANT]
    > Sélectionnez la section en fonction de l’authentification utilisée pour établir la connexion à SQL Server.

    **Utilisation de l’authentification SQL :**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```    

    **Utilisation de l’authentification Windows :**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }    
    ```

    > [!IMPORTANT]
    > - Sélectionnez la section en fonction de l’authentification utilisée pour établir la connexion à votre instance SQL Server.
    > - Remplacez **\<integration runtime name>** par le nom de votre runtime d’intégration.
    > - Remplacez **\<servername>**, **\<databasename>**, **\<username>**, et **\<password>** par les valeurs de votre instance SQL Server avant d’enregistrer le fichier.
    > - Si vous avez besoin d’utiliser une barre oblique (\\) dans votre nom de serveur ou de compte d’utilisateur, faites-la précéder d’un caractère d’échappement (\\). Par exemple, utilisez *mydomain\\\\myuser*. 

2. Pour chiffrer les données sensibles (nom d’utilisateur, mot de passe etc.) exécutez l’applet de commande `New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential`.  
    Les informations d’identification sont alors chiffrées à l’aide de l’API de protection des données (DPAPI). Les informations d’identification chiffrées sont stockées localement sur le nœud de runtime d’intégration auto-hébergé (ordinateur local). La charge utile de sortie peut être redirigée vers un autre fichier JSON (dans ce cas, *encryptedLinkedService.json*) qui contient les informations d’identification chiffrées.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Exécutez la commande suivante, qui crée EncryptedSqlServerLinkedService :

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Créez les jeux de données
Dans cette étape, vous allez créer des jeux de données d’entrée et de sortie. Ils représentent les données d’entrée et de sortie pour l’opération de copie, qui copie les données depuis la base de données SQL Server locale vers le stockage d’objets blob Azure.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>Créer un jeu de données pour la base de données SQL Server source
Dans cette étape, vous définissez un jeu de données qui représente les données dans l’instance de la base de données SQL Server. Le jeu de données est de type SqlServerTable. Il fait référence au service lié SQL Server que vous avez créé à l’étape précédente. Le service lié comporte les informations de connexion utilisées par le service de fabrique de données pour établir la connexion à l’instance SQL Server lors de l’exécution. Ce jeu de données spécifie la table SQL dans la base de données qui contient les données. Dans ce didacticiel, c’est la table **emp** qui contient les données source. 

1. Créez un fichier JSON sous le nom *SqlServerDataset.json* dans le dossier *C:\ADFv2Tutorial* avec le code suivant :  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Pour créer le jeu de données SqlServerDataset, exécutez l’applet de commande `Set-AzureRmDataFactoryV2Dataset`.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Voici l'exemple de sortie :

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Créer un jeu de données pour le stockage Blob Azure (récepteur)
Dans cette étape, vous définissez un jeu de données qui représente les données devant être copiées dans le stockage Blob Azure. Le jeu de données est de type AzureBlob. Il fait référence au service lié Stockage Azure que vous avez créé précédemment dans ce didacticiel. 

Le service lié comporte les informations de connexion utilisées par le service de fabrique de données lors de l’exécution pour établir la connexion à votre compte Stockage Azure. Ce jeu de données spécifie le dossier du stockage Azure dans lequel les données sont copiées à partir de la base de données SQL Server. Dans ce didacticiel, le dossier est : *adftutorial/fromonprem* où `adftutorial` est le conteneur d’objets blob et `fromonprem` le dossier. 

1. Créez un fichier JSON sous le nom *AzureBlobDataset.json* dans le dossier *C:\ADFv2Tutorial* avec le code suivant :

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Pour créer le jeu de données AzureBlobDataset, exécutez l’applet de commande `Set-AzureRmDataFactoryV2Dataset`.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Voici l'exemple de sortie :

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Créer un pipeline
Dans ce didacticiel, vous allez créer un pipeline avec une activité de copie. L’activité de copie utilise SqlServerDataset en tant que jeu de données d’entrée et AzureBlobDataset en tant que jeu de données de sortie. Le type de source est défini sur *SqlSource* et le type de récepteur sur *BlobSink*.

1. Créez un fichier JSON sous le nom *SqlServerToBlobPipeline.json* dans le dossier *C:\ADFv2Tutorial* avec le code suivant :

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Pour créer le pipeline SqlServerToBlobPipeline, exécutez l’applet de commande `Set-AzureRmDataFactoryV2Pipeline`.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Voici l'exemple de sortie :

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```

## <a name="create-a-pipeline-run"></a>Créer une exécution du pipeline
Démarrez l’exécution du pipeline SQLServerToBlobPipeline et capturez l’ID d’exécution du pipeline pour une surveillance ultérieure.

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Surveiller l’exécution du pipeline.

1. Pour vérifier en permanence l’état d’exécution du pipeline SQLServerToBlobPipeline, exécutez le script suivant dans PowerShell et imprimez le résultat final :

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Voici la sortie de l’exemple d’exécution :

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

2. Vous pouvez obtenir l’ID d’exécution du pipeline SQLServerToBlobPipeline, puis vérifiez le résultat détaillé de l’exécution d’activité en exécutant la commande suivante : 

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Voici la sortie de l’exemple d’exécution :

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```

## <a name="verify-the-output"></a>Vérifier la sortie
Le pipeline crée automatiquement le dossier de sortie nommé *fromonprem* dans le conteneur d’objets blob `adftutorial`. Vérifiez que le fichier *dbo.emp.txt* se trouve dans le dossier de sortie. 

1. Dans le portail Azure, dans la fenêtre du conteneur **adftutorial**, cliquez sur **Actualiser** pour afficher le dossier de sortie.

    ![Dossier de sortie créé](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
2. Sélectionnez `fromonprem` dans la liste des dossiers. 
3. Vérifiez que le fichier nommé `dbo.emp.txt` s’affiche.

    ![Fichier de sortie](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créez un runtime d’intégration auto-hébergé.
> * Créer des services liés SQL Server et au Stockage Azure. 
> * Créer des jeux de données SQL Server et Blob Azure.
> * Créer un pipeline avec une activité de copie pour déplacer les données.
> * Démarrer une exécution de pipeline.
> * Surveiller l’exécution du pipeline.

Pour obtenir la liste des magasins de données pris en charge par Data Factory, consultez l’article [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Passez au didacticiel suivant pour découvrir comment copier des données en bloc d’une source vers une destination :

> [!div class="nextstepaction"]
>[Copier des données en bloc](tutorial-bulk-copy.md)
