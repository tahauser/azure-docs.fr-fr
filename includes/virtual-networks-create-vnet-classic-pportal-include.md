## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Création d'un réseau virtuel classique dans le portail Azure
Pour créer un réseau virtuel classique reposant sur le scénario précédent, procédez comme suit.

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**. Notez que la liste **Sélectionner un modèle de déploiement** affiche déjà **Classique**. 3. Cliquez sur **Créer**, comme illustré ci-après.
   
    ![Créer un réseau virtuel dans le portail Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. Dans le volet **Réseau virtuel**, tapez le **Nom** du réseau virtuel, puis cliquez sur **Espace d’adressage**. Configurez les paramètres d’espace d’adressage de votre réseau virtuel et de son premier sous-réseau, puis cliquez sur **OK**. La figure ci-après illustre les paramètres des blocs CIDR de ce scénario.
   
    ![Volet Espace d’adressage](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Cliquez sur **Groupe de ressources** et sélectionnez un groupe de ressources auquel ajouter le réseau virtuel, ou cliquez sur **Créer un groupe de ressources** pour ajouter le réseau virtuel à un groupe de ressources. La figure ci-après illustre les paramètres du nouveau groupe de ressources **TestRG**. Pour plus d’informations sur les groupes de ressources, consultez [Présentation d’Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Volet Créer un groupe de ressources](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. Si nécessaire, modifiez les paramètres **Abonnement** et **Emplacement** de votre réseau virtuel. 
7. Si vous ne souhaitez pas voir le réseau virtuel sous forme de mosaïque dans le **Tableau d’accueil**, désactivez **Épingler au tableau d’accueil**. 
8. Cliquez sur **Créer** et remarquez la vignette **Création du réseau virtuel**, comme illustré ci-après.
   
    ![Créer un réseau virtuel dans le portail](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Attendez que le réseau virtuel soit créé. Lorsque vous voyez apparaître la vignette, cliquez sur cette dernière pour ajouter d’autres sous-réseaux.
   
    ![Créer un réseau virtuel dans le portail](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Vous devez voir apparaître la **Configuration** de votre réseau virtuel, comme illustré. 
   
    ![Créer un réseau virtuel dans le portail](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Cliquez sur **Sous-réseaux** > **Ajouter**, renseignez les zones **Nom** et **Plage d’adresses (bloc CIDR)** pour votre sous-réseau, puis cliquez sur **OK**. La figure ci-après illustre les paramètres de notre scénario actuel.
    
    ![Créer un réseau virtuel dans le portail Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

