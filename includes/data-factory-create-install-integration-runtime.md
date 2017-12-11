## <a name="create-a-self-hosted-ir"></a>Créer un runtime d’intégration auto-hébergé

Dans cette section, vous allez créer un runtime d’intégration auto-hébergé et l’associer à un ordinateur local avec la base de données SQL Server. Le runtime d’intégration auto-hébergé est le composant qui copie les données de SQL Server sur votre ordinateur dans le stockage Blob Azure. 

1. Créez une variable pour le nom du runtime d’intégration. Utilisez un nom unique, et notez-le. Vous l’utiliserez ultérieurement dans ce didacticiel. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. Créez un runtime d’intégration auto-hébergé. 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Voici l'exemple de sortie :

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Exécutez la commande suivante pour récupérer l’état du runtime d’intégration créé. Vérifiez que la valeur de la propriété **État** est définie sur **NeedRegistration**. 

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

3. Exécutez la commande suivante pour récupérer les **clés d’authentification** permettant d’enregistrer le runtime d’intégration auto-hébergé auprès du service de fabrique de données dans le cloud. 

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
4. Copiez l’une des clés (sans les guillemets doubles) pour enregistrer le runtime d’intégration auto-hébergé que vous allez installer sur votre ordinateur à l’étape suivante.  

## <a name="install-integration-runtime"></a>Installer le runtime d’intégration
1. Si **Microsoft Integration Runtime** est déjà installé sur votre machine, désinstallez-le à l’aide de **Ajouter ou supprimer des programmes**. 
2. [Téléchargez](https://www.microsoft.com/download/details.aspx?id=39717) le runtime d’intégration auto-hébergé sur un ordinateur Windows local, puis exécutez l’installation. 
3. Sur la page **Bienvenue dans l’assistant Installation de Microsoft Integration Runtime**, cliquez sur **Suivant**.  
4. Sur la page **Contrat de licence utilisateur final**, acceptez les conditions et le contrat de licence, puis cliquez sur **Suivant**. 
5. Sur la page **Dossier de destination**, cliquez sur **Suivant**. 
6. Sur la page **Prêt à installer Microsoft Integration Runtime**, cliquez sur **Installer**. 
7. Si un message d’avertissement indiquant que l’ordinateur en cours de configuration passera en mode veille ou veille prolongée en cas de non utilisation, s’affiche, cliquez sur **OK**. 
8. Si la fenêtre **Options d’alimentation** s’affiche, fermez-la et basculez vers la fenêtre d’installation. 
9. Sur la page **Assistant Installation de Microsoft Integration Runtime terminé**, cliquez sur **Terminer**.
10. Sur la page **Inscrire Microsoft Integration Runtime (auto-hébergé)**, collez la clé que vous avez enregistrée dans la section précédente, puis cliquez sur **Inscrire**. 

   ![Inscrire le runtime d’intégration](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)
2. Le message suivant s’affiche une fois que le runtime d’intégration auto-hébergé est bien inscrit :

   ![Inscription réussie](media/data-factory-create-install-integration-runtime/registered-successfully.png)

3. Sur la page **Nouveau Integration Runtime (auto-hébergé)**, cliquez sur **suivant**. 

    ![Page Nouveau nœud Integration Runtime](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)
4. Sur le **Canal de communication Intranet**, cliquez sur **Ignorer**. Vous pouvez sélectionner une certification TLS/SSL pour sécuriser les communications intra-nœud dans un environnement de runtime d’intégration à plusieurs nœuds. 

    ![Page du canal de communication Intranet](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)
5. Sur la page **Inscrire Microsoft Integration Runtime (auto-hébergé)**, cliquez sur **Lancer Configuration Manager**. 
6. La page suivante apparaît une fois que le nœud est connecté au service cloud :

   ![Le nœud est connecté](media/data-factory-create-install-integration-runtime/node-is-connected.png)
7. Maintenant, testez la connectivité à votre base de données SQL Server.

    ![Onglet Diagnostic](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    - Dans la fenêtre **Gestionnaire de configuration**, basculez vers l’onglet **Diagnostics**.
    - Sélectionnez **SqlServer** pour **Type de source de données**.
    - Saisissez le nom du **serveur**.
    - Saisissez le nom de la **base de données**. 
    - Sélectionnez le mode **d’authentification**. 
    - Saisissez le nom **d’utilisateur**. 
    - Saisissez le **mot de passe** du nom d’utilisateur.
    - Cliquez sur **Tester** pour vérifier que le runtime d’intégration se connecte à SQL Server. Une coche verte apparaît si la connexion est établie. Sinon, c’est un message d’erreur concernant l’échec qui apparaît. Corrigez les problèmes et assurez-vous que le runtime d’intégration peut se connecter à votre serveur SQL Server.    

    > [!NOTE]
    > Notez ces valeurs (type d’authentification, serveur, base de données, utilisateur, mot de passe). Vous les utiliserez ultérieurement dans ce didacticiel. 
    
