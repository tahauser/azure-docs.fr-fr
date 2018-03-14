---
title: "Configuration d’Azure Key Vault avec une rotation des clés et un audit de bout en bout | Microsoft Docs"
description: "Utilisez cette procédure pour configurer la rotation des clés et la surveillance des journaux de Key Vault."
services: key-vault
documentationcenter: 
author: swgriffith
manager: mbaldwin
tags: 
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: jodehavi;stgriffi
ms.openlocfilehash: edc9a870257536235c2bef139dc4d1ead7dd7a8f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="set-up-azure-key-vault-with-end-to-end-key-rotation-and-auditing"></a>Configurer Azure Key Vault avec une rotation des clés et un audit de bout en bout
## <a name="introduction"></a>Introduction
Une fois votre coffre de clés (Key Vault) créé, vous pouvez commencer à l’utiliser pour stocker vos clés et secrets. Vos applications ne doivent plus nécessairement conserver vos clés et secrets, mais les demanderont au coffre de clés en cas de besoin. Cela vous permet de mettre à jour les clés et les secrets sans affecter le comportement de votre application, et de disposer ainsi de toute une multitude de possibilités de gestion des clés et secrets.

>[!IMPORTANT]
> Les exemples de cet article sont fournis à titre d’illustration uniquement. Ils ne sont pas conçus pour être utilisés pour la production. 

Cet article présente un exemple d’utilisation d’Azure Key Vault pour stocker un secret, dans notre exemple une clé de compte de stockage Azure à laquelle une application accède. Il vous propose également une démonstration d’implémentation d’une rotation planifiée de cette clé de compte de stockage. Pour finir, il vous présente, par le biais d’une démonstration, comment contrôler les journaux d’audit du coffre de clés et déclencher des alertes en cas de requêtes inattendues.

> [!NOTE]
> Ce didacticiel n’a pas pour vocation d’expliquer en détail la configuration initiale de votre coffre de clés. Pour plus d’informations, consultez [Prise en main d’Azure Key Vault](key-vault-get-started.md). Pour connaître la marche à suivre avec l’interface de ligne de commande interplateforme, consultez la rubrique [Gestion de Key Vault à l’aide de l’interface de ligne de commande (CLI)](key-vault-manage-with-cli2.md).
>
>

## <a name="set-up-key-vault"></a>Configuration d’Azure Key Vault
Pour qu’une application puisse récupérer un secret dans Key Vault, vous devez tout d’abord créer le secret et le télécharger dans votre coffre. Procédez en démarrant une session Azure PowerShell et en vous connectant à votre compte Azure avec la commande suivante :

```powershell
Login-AzureRmAccount
```

Dans la fenêtre contextuelle de votre navigateur, entrez votre nom d’utilisateur et votre mot de passe Azure. PowerShell obtient alors tous les abonnements associés à ce compte. PowerShell utilise le premier par défaut.

Si vous disposez de plusieurs abonnements, vous devrez peut-être spécifier celui qui a été utilisé pour créer votre coffre de clés. Entrez la commande suivante pour afficher les abonnements de votre compte :

```powershell
Get-AzureRmSubscription
```

Pour spécifier l’abonnement associé au coffre de clés que vous allez consigner, entrez :

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

Cet article présentant le stockage d’une clé de compte de stockage sous la forme d’un secret, vous devez obtenir cette clé de compte de stockage.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Une fois votre secret récupéré (votre clé de compte de stockage dans notre exemple), vous devez le convertir en une chaîne sécurisée, puis créer un secret avec cette valeur dans votre coffre de clés.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
Ensuite, obtenez l’URI pour le secret que vous avez créé. Il sera utilisé dans une étape ultérieure lorsque vous appellerez le coffre de clés pour récupérer votre secret. Exécutez la commande PowerShell suivante et notez la valeur de l’ID, qui correspond à l’URI du secret :

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurer d’application
Maintenant que vous disposez d’une clé secrète stockée, vous pouvez utiliser le code pour la récupérer et l’utiliser. Quelques étapes sont nécessaires pour y parvenir. La première et la plus importante d’entre elles consiste à enregistrer votre application dans Azure Active Directory et à fournir à Key Vault des informations sur votre application afin qu’il autorise les requêtes provenant de votre application.

> [!NOTE]
> Votre application doit être créée sur le même client Azure Active Directory que votre coffre de clés.
>
>

Ouvrez l’onglet des applications d’Azure Active Directory.

![Ouvrir des applications dans Azure Active Directory](./media/keyvault-keyrotation/AzureAD_Header.png)

Choisissez **AJOUTER** pour ajouter une application à votre Azure Active Directory.

![Choisir AJOUTER](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Conservez le type d’application **APPLICATION WEB ET/OU API WEB** et donnez un nom à votre application.

![Nommer l’application](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Attribuez à votre application une **URL DE CONNEXION** et un **URI ID D’APPLICATION**. Pour cette démonstration, il peut s’agir de n’importe quel URL/URI, et vous pouvez les modifier ultérieurement si nécessaire.

![Fournir les URI nécessaires](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Une fois l’application ajoutée à Azure Active Directory, vous accédez à la page de l’application. Cliquez sur l’onglet **Configurer**, puis recherchez et copiez la valeur de **l’ID client**. Notez l’ID client pour les étapes ultérieures.

Ensuite, générez une clé pour votre application afin qu’elle puisse interagir avec votre Azure Active Directory. Vous pouvez la créer dans la section **Clés** de l’onglet **Configuration**. Notez la clé générée par votre application Azure Active Directory pour pouvoir l’utiliser dans une étape ultérieure.

![Clés d’application Azure Active Directory](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Avant de créer des appels de votre coffre de clés par votre application, vous devez fournir au coffre de clés des informations sur votre application et ses autorisations. La commande suivante récupère le nom du coffre et l’ID client dans votre application Azure Active Directory et accorde à l’application un accès **Get** à votre coffre de clés.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

À ce stade, vous êtes prêt à créer vos appels d’application. Dans votre application, vous devez installer les packages NuGet nécessaires pour interagir avec Azure Key Vault et Azure Active Directory. Entrez les commandes suivantes dans la console du gestionnaire de package Visual Studio. Au moment de la rédaction de cet article, la version du package Azure Active Directory est la version 3.10.305231913. Nous vous conseillons donc de vérifier que vous disposez de la dernière version et de mettre à jour si nécessaire.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Dans votre code d’application, créez une classe qui contiendra la méthode d’authentification de votre Azure Active Directory. Dans cet exemple, la classe est appelée **Utils**. Ajoutez les instructions using suivantes :

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Ajoutez ensuite la méthode suivante pour récupérer le jeton JWT d’Azure Active Directory. Pour faciliter la maintenance, vous souhaiterez peut-être déplacer les valeurs de chaîne codées en dur dans votre configuration web ou d’application.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Ajoutez le code nécessaire pour appeler Key Vault et récupérer votre valeur secrète. Vous devez tout d’abord ajouter l’instruction « using » suivante :

```csharp
using Microsoft.Azure.KeyVault;
```

Ajoutez les appels de méthode pour appeler Key Vault et récupérer votre secret. Dans cette méthode, vous fournissez l’URI de secret que vous avez enregistré dans une étape précédente. Notez l’utilisation de la méthode **GetToken** à partir de la classe **Utils** créée précédemment.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Lorsque vous exécutez votre application, vous devez désormais vous authentifier auprès d’Azure Active Directory et récupérer votre valeur secrète dans Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Rotation des clés à l’aide d’Azure Automation
Diverses options de mise en œuvre d’une stratégie de rotation sont possibles pour les valeurs que vous stockez sous forme de secrets Azure Key Vault. La rotation des secrets peut être manuelle, elle peut également être effectuée par programmation à l’aide d’appels d’API ou à l’aide d’un script Automation. Dans cet article, vous utiliserez Azure PowerShell et Azure Automation pour modifier une clé d’accès de compte de stockage Azure. Ensuite, vous mettrez à jour un secret de coffre de clés avec cette nouvelle clé.

Pour qu’Azure Automation puisse définir des valeurs secrètes dans votre coffre de clés, vous devez obtenir l’ID client pour la connexion nommée AzureRunAsConnection créée pendant la définition de votre instance Azure Automation. Vous pouvez obtenir cet ID en choisissant **Actifs** dans votre instance Azure Automation. Sélectionnez ensuite **Connexions**, puis le principal du service **AzureRunAsConnection**. Notez **l’ID d’application**.

![ID client Azure Automation](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Dans **Actifs**, choisissez **Modules**. Sous **Modules**, sélectionnez **Galerie**, puis recherchez et **importez** les versions à jour de chacun des modules suivants :

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> Au moment de la rédaction de cet article, seuls les modules mentionnés précédemment devaient être mis à jour pour le script suivant. Si vous constatez un échec de votre tâche d’automatisation, vérifiez que tous les modules nécessaires et leurs dépendances ont été importés.
>
>

Une fois que vous avez récupéré l’ID d’application pour votre connexion Azure Automation, vous devez indiquer à votre coffre de clés que cette application est autorisée à accéder aux secrets dans votre coffre pour les mettre à jour. Cela peut être effectué avec la commande PowerShell suivante :

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Sélectionnez ensuite **Runbooks** sous votre instance Azure Automation, puis **Ajouter un runbook**. Sélectionnez **Création rapide**. Donnez un nom à votre runbook et sélectionnez **PowerShell** comme type de runbook. Vous avez la possibilité d’ajouter une description. Pour finir, cliquez sur **Créer**.

![Créer un runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Collez le script PowerShell suivant dans le volet de l’éditeur de votre nouveau runbook :

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Dans le volet de l’éditeur, choisissez le volet **Test** pour tester votre script. Une fois le script exécutez sans erreur, vous pouvez sélectionner **Publier**, puis appliquer une planification du runbook dans le volet de configuration du runbook.

## <a name="key-vault-auditing-pipeline"></a>Pipeline d’audit de Key Vault
Lorsque vous configurez un coffre de clés, vous pouvez activer la fonction d’audit afin de collecter des journaux de demandes d’accès au coffre de clés. Ces journaux sont stockés dans un compte de stockage Azure spécifié et peuvent être récupérés, contrôlés et analysés. Le scénario suivant utilise Azure Functions, Azure Logic Apps et des journaux d’audit du coffre de clés afin de créer un pipeline pour envoyer un e-mail lorsqu’une application qui ne correspond pas à l’ID de l’application web récupère´des secrets du coffre.

Vous devez tout d’abord activer la journalisation sur votre coffre de clés. Vous pouvez utiliser pour cela les commandes PowerShell suivantes (pour plus de détails, consultez [key-vault-logging](key-vault-logging.md)) :

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Une fois cette option activée, les journaux d’audit commencent la collecte dans le compte de stockage spécifié. Ces journaux contiennent des événements indiquant la méthode et la date/l’heure d’accès à vos coffres de clés, et qui y a accédé.

> [!NOTE]
> Vous pouvez accéder aux informations de journalisation 10 minutes après l’opération sur le coffre de clés. Elles sont généralement disponibles plus rapidement.
>
>

L’étape suivante consiste à [créer une file d’attente Azure Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). C’est dans celle-ci que les journaux d’audit de coffre de clés sont envoyés. Lorsque les messages du journal d’audit se trouvent dans la file d’attente, l’application logique les récupère et agit en conséquence. Procédez comme suit pour créer un Service Bus :

1. Créez un espace de noms Service Bus (si vous en avez déjà un et souhaitez l’utiliser ici, passez à l’étape 2).
2. Recherchez le Service Bus dans le portail Azure, puis sélectionnez l’espace de noms dans lequel vous souhaitez créer la file d’attente.
3. Sélectionnez **Créer une ressource**, **Intégration Entreprise**, **Service Bus**, puis entrez les informations requises.
4. Sélectionnez les informations de connexion Service Bus en choisissant l’espace de noms et en cliquant sur **Informations de connexion**. Vous aurez besoin de ces informations dans la section suivante.

Ensuite, [créez une fonction Azure](../azure-functions/functions-create-first-azure-function.md) pour interroger les journaux de coffre de clés dans le compte de stockage et récupérer les nouveaux événements. Il s’agit d’une fonction déclenchée de manière planifiée.

Pour créer une fonction Azure, choisissez **Créer une ressource**, recherchez _Function App_ sur la Place de Marché, puis cliquez sur **Créer**. Lors de la création, vous pouvez utiliser un plan d’hébergement existant ou en créer un nouveau. Vous pouvez également opter pour un hébergement dynamique. Vous trouverez plus d’informations sur les options d’hébergement de fonction dans la rubrique [Mise à l’échelle d’Azure Functions](../azure-functions/functions-scale.md).

Une fois la fonction Azure créée, accédez-y, choisissez une fonction de minuteur et C\#. Cliquez ensuite sur **Créer cette fonction**.

![Panneau d’accueil d’Azure Functions](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Dans l’onglet **Développement** , remplacez le code run.csx par le code suivant :

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```


> [!NOTE]
> Veillez à remplacer les variables du code précédent de façon à ce qu’elles pointent vers votre compte de stockage dans lequel les journaux de coffre de clés sont écrits, le Service Bus que vous avez créé précédemment, et le chemin d’accès spécifique des journaux de stockage du coffre de clés.
>
>

La fonction récupère le dernier fichier journal du compte de stockage dans lequel les journaux de coffre de clés sont écrits, extrait les événements les plus récents de ce fichier et les place dans une file d’attente Service Bus. Étant donné qu’un même fichier peut comporter plusieurs événements, vous devez créer un fichier sync.txt que la fonction examine également pour déterminer l’horodatage du dernier événement récupéré. Cela vous permet de vous assurer que le même événement n’est pas envoyé plusieurs fois. Ce fichier sync.txt contient l’horodatage du dernier événement rencontré. Les journaux, une fois chargés, doivent être triés en fonction de l’horodatage pour s’assurer qu’ils sont classés correctement.

Pour cette fonction, nous faisons référence à deux bibliothèques supplémentaires qui ne sont pas disponibles dans Azure Functions. Pour pouvoir les inclure, Azure Functions doit le faire à l’aide de NuGet. Choisissez l’option **Afficher les fichiers**.

![Option Afficher les fichiers](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Et ajoutez un fichier appelé project.json avec le contenu suivant :

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
Lorsque vous cliquez sur **Enregistrer**, Azure Functions télécharge les fichiers binaires nécessaires.

Basculez vers l’onglet **Intégration** et donnez un nom explicite au paramètre du minuteur à utiliser dans la fonction. Dans le code précédent, le minuteur est appelé *myTimer*. Spécifiez une [expression CRON](../app-service/web-sites-create-web-jobs.md#CreateScheduledCRON) comme suit : 0 \* \* \* \* \* pour le minuteur qui activera l’exécution de la fonction une fois par minute.

Dans ce même onglet **Intégration**, ajoutez une entrée de type **Stockage Blob Azure**. Elle pointera vers le fichier sync.txt contenant l’horodatage du dernier événement examiné par la fonction. Elle sera disponible dans la fonction en tant que nom de paramètre. Dans le code précédent, l’entrée Azure Blob Storage attend le nom de paramètre *inputBlob*. Choisissez le compte de stockage dans lequel se trouvera le fichier sync.txt (il peut s’agir du même compte de stockage ou d’un autre). Dans le champ Chemin d’accès, indiquez le chemin d’accès au fichier au le format {container-name}/path/to/sync.txt.

Ajoutez une sortie de type *Stockage Blob Azure*. Elle pointe vers le fichier sync.txt que vous avez défini dans l’entrée. Elle est utilisée par la fonction pour écrire l’horodatage du dernier événement examiné. Dans le code précédent, le paramètre doit être appelé *outputBlob*.

À ce stade, la fonction est prête. N’oubliez pas de revenir à l’onglet **Développement** et d’enregistrer le code. Dans la fenêtre de résultat, recherchez d’éventuelles erreurs de compilation et corrigez-les. S’il est compilé, le code doit maintenant vérifier les journaux du coffre de clés chaque minute et placer tout nouvel événement dans la file d’attente Service Bus définie. Vous devez voir des informations de journalisation dans la fenêtre du journal à chaque déclenchement de la fonction.

### <a name="azure-logic-app"></a>Application logique Azure
Vous devez ensuite créer une application logique Azure qui sélectionne les événements que la fonction place dans la file d’attente Service Bus, analyse le contenu et envoie un e-mail lorsqu’une condition est remplie.

[Créez une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md) en accédant à **Nouveau &gt; Application logique**.

Une fois l’application logique créée, accédez à celle-ci et choisissez **Modifier**. Dans l’éditeur d’application logique, choisissez la **File d’attente Service Bus** et entrez vos informations d’identification Service Bus pour la connecter à la file d’attente.

![Service Bus d’application logique Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Choisissez ensuite **Ajouter une condition**. Dans la condition, basculez vers l’éditeur avancé, puis entrez le code suivant, en remplaçant l’APP_ID par le véritable APP_ID réel de votre application web :

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Cette expression retourne essentiellement la valeur **false** si *l’appid* de l’événement entrant (à savoir le corps du message Service Bus) ne correspond pas à *l’appid* de l’application.

Créez maintenant une action sous **Si non, ne rien faire**.

![Choisir une action d’application logique Azure](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Pour l’action, choisissez **Office 365 - Envoyer un message électronique**. Renseignez les champs pour créer un e-mail à envoyer lorsque la condition définie retourne **false**. Si vous n’avez pas Office 365, vous pouvez rechercher des alternatives pour parvenir aux mêmes résultats.

À ce stade, vous disposez d’un pipeline de bout en bout qui recherche les nouveaux journaux d’audit de coffre de clés une fois par minute. Il place les nouveaux journaux qu’il trouve dans une file d’attente Service Bus. L’application logique est déclenchée lorsqu’un nouveau message arrive dans la file d’attente. Si *l’appid* au sein de l’événement ne correspond pas à l’ID d’application de l’application appelante, il envoie un e-mail.
