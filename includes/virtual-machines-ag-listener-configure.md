L’écouteur de groupe de disponibilité est une adresse IP et un nom réseau sur lesquels le groupe de disponibilité de SQL Server écoute. Pour créer l’écouteur de groupe de disponibilité, procédez comme suit :

1. <a name="getnet"></a>Récupérez le nom de la ressource réseau de cluster.

    a. Utilisez le protocole RDP pour vous connecter à la machine virtuelle Azure qui héberge le réplica principal. 

    b. Ouvrez le Gestionnaire du cluster de basculement.

    c. sélectionnez le nœud **Réseaux** et notez le nom de réseau du cluster. Utilisez ce nom dans la variable `$ClusterNetworkName` du script PowerShell. Dans l’image suivante, le nom réseau du cluster est **Cluster Network 1** :

   ![Nom réseau du cluster](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

2. <a name="addcap"></a>Ajoutez le point d’accès client.  
    Le point d’accès client est le nom réseau que les applications utilisent pour se connecter aux bases de données dans un groupe de disponibilité. Créez le point d’accès client dans le Gestionnaire du cluster de basculement .

    a. Développez le nom du cluster, puis cliquez sur **Rôles**.

    b. Dans le volet **Rôles**, cliquez avec le bouton droit sur le nom du groupe de disponibilité, puis sélectionnez **Ajouter une ressource** > **Point d’accès client**.

   ![Point d’accès client](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Dans la zone **Nom**, créez un nom pour ce nouvel écouteur. 
   Le nom du nouvel écouteur est le nom réseau que les applications utilisent pour se connecter aux bases de données dans le groupe de disponibilité de SQL Server.
   
    d. Pour terminer la création de l’écouteur, cliquez sur **Suivant** deux fois, puis cliquez sur **Terminer**. Ne mettez pas l'écouteur ou la ressource en ligne à ce stade.

3. <a name="congroup"></a>Configurez la ressource IP du groupe de disponibilité.

    a. Cliquez sur l’onglet **Ressources**, puis développez le point d’accès client que vous avez créé.  
    Le point d’accès client est hors connexion.

   ![Point d’accès client](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Cliquez avec le bouton droit sur la ressource IP, puis cliquez sur Propriétés. Notez le nom de l’adresse IP et utilisez-le dans la variable `$IPResourceName` dans le script PowerShell.

    c. Sous **Adresse IP**, cliquez sur **Adresse IP statique**. Pour l’adresse IP, utilisez l’adresse que vous avez définie pour l’équilibreur de charge sur le portail Azure.

   ![Ressource IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

4. <a name = "dependencyGroup"></a>Créez une dépendance entre la ressource de groupe de disponibilité de SQL Server et le point d’accès client.

    a. Dans le Gestionnaire du cluster de basculement, cliquez sur **Rôles**, puis sur votre groupe de disponibilité.

    b. Dans l’onglet **Ressources**, sous **Autres ressources**, cliquez avec le bouton droit sur la ressource de groupe de disponibilité, puis cliquez sur **Propriétés**. 

    c. Dans l’onglet Dépendances, ajoutez le nom de la ressource du point d’accès client (écouteur).

   ![Ressource IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Cliquez sur **OK**.

5. <a name="listname"></a>Créez une dépendance entre la ressource du point d’accès client et l’adresse IP.

    a. Dans le Gestionnaire du cluster de basculement, cliquez sur **Rôles**, puis sur votre groupe de disponibilité. 

    b. Dans l’onglet **Ressources**, cliquez avec le bouton droit sur la ressource du point d’accès client sous **Nom du serveur**, puis cliquez sur **Propriétés**. 

   ![Ressource IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Cliquez sur l'onglet **Dépendances** . Vérifiez que l’adresse IP est une dépendance. Si tel n’est pas le cas, définissez une dépendance sur l’adresse IP. Si plusieurs ressources sont répertoriées, vérifiez que les adresses IP ont des dépendances OR (et non des dépendances AND). Cliquez sur **OK**. 

   ![Ressource IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    d. Cliquez avec le bouton droit sur le nom de l’écouteur, puis cliquez sur **Mettre en ligne**. 

    >[!TIP]
    >Vous pouvez vérifier que les dépendances sont correctement configurées. Dans le Gestionnaire du cluster de basculement, accédez à Rôles, cliquez avez le bouton droit sur le groupe de disponibilité, et cliquez sur **Autres actions**, puis sur **Afficher le rapport de dépendance**. Lorsque les dépendances sont correctement configurées, le groupe de disponibilité dépend du nom réseau, et le nom réseau dépend de l’adresse IP. 


6. <a name="setparam"></a>Définissez les paramètres de cluster dans PowerShell.
    
    a. Copiez le script PowerShell suivant sur l’une de vos instances SQL Server. Mettez à jour les variables de votre environnement.     
    
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
    
    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    b. Définissez les paramètres du cluster en exécutant le script PowerShell sur l’un des nœuds du cluster.  

    > [!NOTE]
    > Si vos instances SQL Server se trouvent dans différentes régions, vous devez exécuter le script PowerShell à deux reprises. La première fois, utilisez `$ILBIP` et `$ProbePort` à partir de la première région. La seconde fois, utilisez `$ILBIP` et `$ProbePort` à partir de la seconde région. Le nom réseau du cluster et le nom de ressource IP du cluster sont identiques. 
