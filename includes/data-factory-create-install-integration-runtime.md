## <a name="create-a-self-hosted-integration-runtime"></a>Créer un runtime d’intégration auto-hébergé

Dans cette section, vous allez créer un runtime d’intégration auto-hébergé et l’associer à un ordinateur local avec la base de données SQL Server. Le runtime d’intégration auto-hébergé est le composant qui copie les données de SQL Server sur votre ordinateur dans le stockage Blob Azure. 

1. Créez une variable pour le nom du runtime d’intégration. Utilisez un nom unique et notez-le. Vous l’utiliserez ultérieurement dans ce didacticiel. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Créez un runtime d’intégration auto-hébergé. 

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
 
3. Exécutez la commande suivante pour récupérer l’état du runtime d’intégration créé : Vérifiez que la valeur de la propriété **État** est définie sur **NeedRegistration**. 

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

4. Exécutez la commande suivante pour récupérer les clés d’authentification permettant d’enregistrer le runtime d’intégration auto-hébergé auprès du service Azure Data Factory dans le cloud : 

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

5. Copiez l’une des clés (sans les guillemets) pour enregistrer le runtime d’intégration auto-hébergé que vous allez installer sur votre ordinateur à l’étape suivante.  

## <a name="install-the-integration-runtime"></a>Installer le runtime d’intégration
1. Si le runtime d’intégration est déjà installé sur votre machine, désinstallez-le à l’aide de **Ajouter ou supprimer des programmes**. 

2. [Téléchargez](https://www.microsoft.com/download/details.aspx?id=39717) le runtime d’intégration auto-hébergé sur un ordinateur Windows local. Exécutez l’installation.

3. Sur la page **Bienvenue dans l’assistant d’installation de Microsoft Integration Runtime**, cliquez sur **Suivant**.

4. Sur la page **Contrat de Licence utilisateur final**, acceptez les conditions et le contrat de licence, puis cliquez sur **Suivant**.

5. Sur la page **Dossier de destination**, cliquez sur **Suivant**.

6. Sur la page **Prêt à installer Microsoft Integration Runtime**, cliquez sur **Installer**.

7. Si un message d’avertissement indiquant que l’ordinateur en cours de configuration passera en mode veille ou veille prolongée en cas de non utilisation, s’affiche, cliquez sur **OK**.

8. Si la page **Options d’alimentation** s’affiche, fermez-la et accédez à la page d’installation.

9. Sur la page **Assistant d’installation de Microsoft Integration Runtime terminé**, cliquez sur **Terminer**.

10. Sur la page **Inscrire le Integration Runtime (auto-hébergé)**, collez la clé que vous avez enregistrée dans la section précédente, puis cliquez sur **Inscrire**. 

    ![Inscrire le runtime d’intégration](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

11. Le message suivant s’affiche une fois que le runtime d’intégration auto-hébergé est bien inscrit :

    ![Inscription réussie](media/data-factory-create-install-integration-runtime/registered-successfully.png)

12. Sur la page **Nouveau runtime d’intégration (auto-hébergé)**, cliquez sur **suivant**. 

    ![Page Nouveau nœud Integration Runtime](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)

13. Sur la page **Canal de communication Intranet**, cliquez sur **Ignorer**. Sélectionnez une certification TLS/SSL pour sécuriser les communications intra-nœud dans un environnement de runtime d’intégration à plusieurs nœuds. 

    ![Page du canal de communication Intranet](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)

14. Sur la page **Inscrire le runtime d’intégration (auto-hébergé)**, cliquez sur **Lancer le Gestionnaire de configuration**.

15. La page suivante apparaît une fois que le nœud est connecté au service cloud :

    ![Page Le nœud est connecté](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. Maintenant, testez la connectivité à votre base de données SQL Server.

    ![Onglet Diagnostic](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. Sur la page **Gestionnaire de configuration**, accédez à l’onglet **Diagnostics**.

    b. Sélectionnez **SqlServer** comme type de source de données.

    c. Saisissez le nom du serveur.

    d. Saisissez le nom de la base de données.

    e. Sélectionnez le mode d’authentification.

    f. Entrer le nom d'utilisateur.

    g. Entrez le mot de passe correspondant au nom d’utilisateur.

    h. Cliquez sur **Tester** pour vérifier que le runtime d’intégration se connecte à SQL Server. Une coche verte apparaît si la connexion est établie. Un message d’erreur apparaît si la connexion échoue. Corrigez les problèmes et assurez-vous que le runtime d’intégration peut se connecter au serveur SQL Server.    

    > [!NOTE]
    > Notez les valeurs pour le type d’authentification, le serveur, la base de données, l’utilisateur et le mot de passe. Vous les utiliserez ultérieurement dans ce didacticiel. 
    
