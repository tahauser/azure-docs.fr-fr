## <a name="set-up-your-development-environment"></a>Configuration de l'environnement de développement
Ensuite, configurez votre environnement de développement dans Visual Studio afin d’être prêt pour essayer les exemples de code fournis dans ce guide.

### <a name="create-a-windows-console-application-project"></a>Créer un projet d’application de console Windows
Dans Visual Studio, créez une application de console Windows. Les étapes suivantes vous montrent comment créer une application de console dans Visual Studio 2017. Les étapes sont semblables pour d’autres versions de Visual Studio.

1. Sélectionnez **Fichier** > **Nouveau** > **Projet**.
2. Sélectionnez **Installé** > **Modèles** > **Visual C#** > **Bureau classique Windows**.
3. Sélectionnez **Application console (.NET Framework)**.
4. Saisissez un nom pour votre application dans le champ **Nom**.
5. Sélectionnez **OK**.

![Capture d’écran de la boîte de dialogue Nouveau projet dans Visual Studio](./media/storage-development-environment-include/storage-development-environment-include-1.png)

Tous les exemples de code figurant dans ce didacticiel peuvent être ajoutés à la méthode `Main()` du fichier `Program.cs` de votre application de console.

Vous pouvez utiliser la bibliothèque cliente d’Azure Storage dans n’importe quelle application .NET, y compris un service cloud Azure, une application web, une application de bureau ou une application mobile. Dans ce guide, nous utilisons une application console pour plus de simplicité.

### <a name="use-nuget-to-install-the-required-packages"></a>Utiliser NuGet pour installer les packages requis
Vous devez référencer deux packages dans votre projet pour terminer ce didacticiel :

* [Bibliothèque cliente Microsoft Azure Storage pour .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): ce package fournit un accès par programme aux ressources de données dans votre compte de stockage.
* [Bibliothèque Microsoft Azure Configuration Manager pour .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) : ce package fournit une classe pour l’analyse d’une chaîne de connexion à partir d’un fichier de configuration, quel que soit l’emplacement d’exécution de votre application.

Vous pouvez utiliser NuGet pour obtenir ces deux packages. Procédez comme suit :

1. Cliquez avec le bouton droit sur votre projet dans **l’Explorateur de solutions**, puis sélectionnez **Gérer les packages NuGet**.
2. Recherchez « WindowsAzure.Storage » en ligne, puis sélectionnez **Installer** pour installer la bibliothèque cliente Azure Storage et ses dépendances.
3. Recherchez « WindowsAzure.ConfigurationManager » en ligne, puis sélectionnez **Installer** pour installer Azure Configuration Manager.

> [!NOTE]
> Le package de bibliothèque cliente Azure Storage est également disponible dans le [Kit de développement logiciel (SDK) Azure pour .NET](https://azure.microsoft.com/downloads/). Toutefois, nous vous recommandons d’installer également la bibliothèque cliente Azure Storage à partir de NuGet pour vous assurer que vous avez toujours la dernière version de la bibliothèque cliente.
> 
> Les dépendances ODataLib de la bibliothèque de client de stockage pour .NET sont résolues via les packages ODataLib disponibles sur NuGet et non à partir des services de données WCF. Vous pouvez télécharger directement les bibliothèques ODataLib ou les référencer avec votre projet de code via NuGet. Les packages ODataLib utilisés par la bibliothèque cliente de stockage sont [OData](http://nuget.org/packages/Microsoft.Data.OData/), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/) et [Spatial](http://nuget.org/packages/System.Spatial/). Bien qu’elles soient utilisées par les classes de stockage de Table Azure, ces bibliothèques sont des dépendances requises pour la programmation avec la bibliothèque cliente de stockage.
> 
> 

### <a name="determine-your-target-environment"></a>Déterminer votre environnement cible
Vous avez le choix entre deux environnements pour exécuter les exemples de ce guide :

* Vous pouvez exécuter votre code sur un compte Azure Storage dans le cloud. 
* Vous pouvez exécuter votre code sur l’émulateur de stockage Azure. L’émulateur de stockage est un environnement local qui émule un compte Azure Storage dans le cloud. L’émulateur est une option gratuite permettant de tester et déboguer votre code lors du développement de votre application. L’émulateur utilise un compte et une clé connus. Pour plus d’informations, consultez [Utilisation de l’émulateur de stockage Azure pour le développement et le test](../articles/storage/common/storage-use-emulator.md).

Si vous ciblez un compte de stockage dans le cloud, copiez la clé d’accès primaire de votre compte de stockage à partir du portail Azure. Pour plus d’informations, voir [Affichage et copie de clés d’accès de stockage](../articles/storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys).

> [!NOTE]
> Vous pouvez cibler l’émulateur de stockage pour éviter les frais liés à l’utilisation des services de stockage Azure. Toutefois, si vous choisissez de cibler un compte de stockage Azure situé dans le cloud, les frais associés à l’utilisation de ce didacticiel seront négligeables.
> 
> 

### <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage
La bibliothèque du client de stockage Azure pour .NET prend en charge l’utilisation d’une chaîne de connexion de stockage pour la configuration de points de terminaison et d’informations d’identification permettant d’accéder aux services de stockage. La meilleure façon de conserver votre chaîne de connexion de stockage est dans un fichier de configuration. 

Pour plus d’informations sur les chaînes de connexion, voir [Configuration d’une chaîne de connexion dans Stockage Azure](../articles/storage/common/storage-configure-connection-string.md).

> [!NOTE]
> Votre clé de compte de stockage est similaire au mot de passe racine pour votre compte de stockage. Veillez toujours à protéger votre clé de compte de stockage. Évitez de la communiquer à d’autres utilisateurs, de la coder en dur ou de l’enregistrer dans un fichier texte brut accessible à d’autres personnes. Régénérez votre clé à l’aide du portail Azure si vous pensez que sa confidentialité est compromise.
> 
> 

Pour configurer votre chaîne de connexion, ouvrez le fichier `app.config` depuis l’Explorateur de solutions dans Visual Studio. Ajoutez le contenu de l’élément `<appSettings>` indiqué ci-dessous. Remplacez `account-name` par le nom de votre compte de stockage et `account-key` par votre clé d’accès au compte :

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Par exemple, votre paramètre de configuration est semblable à :

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Pour cibler l’émulateur de stockage, vous pouvez utiliser un raccourci qui correspond à la clé et au nom de compte connus. Dans ce cas, le paramètre de votre chaîne de connexion est :

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

