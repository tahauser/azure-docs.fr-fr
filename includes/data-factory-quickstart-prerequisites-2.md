### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-powershell"></a>Installer PowerShell
Installez la dernière version de PowerShell, si elle n’est pas installée sur votre ordinateur. 

1. Dans votre navigateur web, accédez à la page [Téléchargements Azure](https://azure.microsoft.com/downloads/). 
2. Cliquez sur **Installation Windows** dans la section **Outils de ligne de commande** -> **PowerShell**. 
3. Pour installer PowerShell, exécutez le fichier **MSI**. 

Pour des instructions détaillées, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Se connecter à PowerShell

1. Lancez **PowerShell** sur votre ordinateur. Gardez PowerShell ouvert jusqu’à la fin de ce guide de démarrage rapide. Si vous la fermez, puis la rouvrez, vous devez réexécuter ces commandes.

    ![Lancement de PowerShell](media/data-factory-quickstart-prerequisites-2/search-powershell.png)
1. Exécutez la commande suivante, puis saisissez le nom d’utilisateur et le mot de passe Azure que vous utilisez pour vous connecter au portail Azure :
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Exécutez la commande suivante pour afficher tous les abonnements de ce compte :

    ```powershell
    Get-AzureRmSubscription
    ```
3. Si plusieurs abonnements sont associés à votre compte, exécutez la commande suivante pour sélectionner l’abonnement avec lequel vous souhaitez travailler. Remplacez **SubscriptionId** par l’ID de votre abonnement Azure :

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
