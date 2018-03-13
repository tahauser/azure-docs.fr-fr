### <a name="azure-storage-linked-service"></a>Service lié Stockage Azure
Le **service lié Stockage Azure** vous permet de lier un compte de stockage Azure à une fabrique de données Azure à l’aide de la **clé de compte**, qui permet à la fabrique de données d’avoir un accès global au Stockage Azure. Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Azure Storage.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type |La propriété de type doit être définie sur : **AzureStorage** |Oui |
| connectionString |Spécifier les informations requises pour la connexion au stockage Azure pour la propriété connectionString. |Oui |

Pour plus d’informations sur l’affichage ou la copie de la clé de compte pour un stockage Azure, consultez l’article [Affichage, copie et régénération de clés d’accès de stockage](../articles/storage/common/storage-create-storage-account.md#manage-your-storage-account).

**Exemple :**  

```json
{  
    "name": "StorageLinkedService",  
    "properties": {  
        "type": "AzureStorage",  
        "typeProperties": {  
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
        }  
    }  
}  
```

### <a name="azure-storage-sas-linked-service"></a>Service lié SAP Azure Storage
Une signature d’accès partagé (SAP) fournit un accès délégué aux ressources de votre compte de stockage. Cela vous permet d’octroyer à un client des autorisations d’accès limité à des objets de votre compte de stockage pendant une période donnée et avec un ensemble défini d’autorisations, sans partager les clés d’accès de votre compte. La SAP est un URI qui englobe dans ses paramètres de requête toutes les informations nécessaires pour obtenir un accès authentifié à une ressource de stockage. Pour accéder aux ressources de stockage avec la signature d'accès partagé, il suffit au client de transmettre cette dernière à la méthode ou au constructeur approprié. Pour plus d’informations sur SAP, consultez [Signatures d’accès partagé : présentation du modèle SAP](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Azure Data Factory prend maintenant uniquement en charge la **signature d’accès partagé SAS**, et pas la signature d’accès partagé du compte. Pour plus d’informations sur ces deux types et leur construction, consultez [Types de signatures d’accès partagé](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) . Notez l’URL SAS qu’il est possible de générer à partir du portail Azure ou l’Explorateur de stockage est une signature d’accès partagé de compte qui n’est pas prise en charge.

> [!TIP]
> Vous pouvez exécuter les commandes PowerShell ci-dessous pour générer un SAP de service pour votre compte de stockage (remplacez les espaces réservés et accordez l’autorisation nécessaire) : `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Le service lié Stockage Azure SAS vous permet de lier un compte de stockage Azure à une fabrique de données Azure à l’aide d’une signature d’accès partagé (SAP). Ainsi, la fabrique de données dispose d’un accès restreint ou limité dans le temps à tout ou partie des ressources (objet blob/conteneur) dans le stockage. Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Azure Storage SAS. 

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type |La propriété de type doit être définie sur : **AzureStorageSas** |Oui |
| sasUri |Spécifiez l’URI de signature d’accès partagé des ressources Stockage Azure, telles qu’un objet blob, un conteneur ou une table.  |Oui |

**Exemple :**

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"   
        }  
    }  
}  
```

Quand vous créez un **URI SAP**, prenez en compte les points suivants :  

* Définissez des **autorisations** de lecture/écriture appropriées sur les objets en fonction de l’utilisation du service lié (lecture, écriture, lecture/écriture) dans votre fabrique de données.
* Définissez le paramètre **Heure d’expiration** correctement. Assurez-vous que l’accès aux objets Azure Storage n’expire pas pendant la période active du pipeline.
* L’URI doit être créé au niveau table ou objet blob/conteneur approprié en fonction des besoins. Un URI SAP vers un objet blob Azure rend ce dernier accessible au service Data Factory. Un URI SAP vers un conteneur d’objets blob Azure permet au service Data Factory de parcourir les objets blob dans ce conteneur. Si vous êtes amené à accorder l’accès à plus ou moins d’objets ou à mettre à jour l’URI SAP, mettez à jour le service lié avec le nouvel URI.   

