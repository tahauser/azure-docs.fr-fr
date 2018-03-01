

![Machines virtuelles dans un service cloud autonome](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Si vous placez vos machines virtuelles dans un réseau virtuel, vous pouvez décider combien de services cloud vous voulez utiliser pour utiliser l’équilibrage de charge et des groupes à haute disponibilité. En outre, vous pouvez organiser les machines virtuelles sur les mêmes sous-réseaux que votre réseau local et connecter le réseau virtuel à votre réseau local. Voici un exemple :

![Machines virtuelles dans un réseau virtuel](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

Les réseaux virtuels sont la méthode recommandée pour connecter des machines virtuelles dans Azure. La meilleure pratique consiste à configurer chaque couche de votre application dans un service cloud distinct. Toutefois, vous devrez peut-être combiner des machines virtuelles à partir de différentes couches d'application dans le même service cloud pour ne pas dépasser le nombre maximum de services cloud par abonnement (200). Pour vérifier cela et les autres limites, consultez [Abonnement Azure et limites, quotas et contraintes du service](../articles/azure-subscription-service-limits.md).

## <a name="connect-vms-in-a-virtual-network"></a>Connexion de machines virtuelles dans un réseau virtuel
Pour connecter des machines virtuelles dans un réseau virtuel :

1. Créer le réseau virtuel dans le [portail Azure](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md) et spécifiez « déploiement classique ».
2. Créez l’ensemble des services cloud pour votre déploiement de manière à refléter votre conception des groupes à haute disponibilité et l’équilibrage de la charge. Dans le portail Azure, cliquez sur **Créer une ressource > Calcul > Service cloud** pour chaque service cloud.

  Pendant que vous remplissez les détails du service cloud, choisissez le même _groupe de ressources_ utilisé avec le réseau virtuel.

3. Pour créer chaque machine virtuelle, cliquez sur **Créer une ressource > Calcul**, puis sélectionnez l’image de machine virtuelle appropriée dans **Applications proposées**.

  Dans le panneau **Concepts de base**, cliquez sur le même _groupe de ressources_ utilisé avec le réseau virtuel.

  ![Panneau Concepts de base de la machine virtuelle lors de l’utilisation d’un réseau virtuel](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. Lorsque vous remplissez les **Paramètres** de la machine virtuelle, choisissez le _service cloud_ ou le _réseau virtuel_ pour la machine virtuelle.

  Azure sélectionne l’autre élément en fonction de votre sélection.

  ![Panneau Paramètres de la machine virtuelle lors de l’utilisation d’un réseau virtuel](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>Connexion de machines virtuelles dans un service cloud autonome
Pour connecter des machines virtuelles dans un service cloud autonome :

1. Créez le service cloud dans le [portail Azure](http://portal.azure.com). Cliquez sur **Nouveau > Compute > Service cloud**. Vous pouvez également créer le service cloud pour votre déploiement lorsque vous créez votre première machine virtuelle.
2. Lorsque vous créez les machines virtuelles, choisissez le même groupe de ressources que celui utilisé avec le service cloud.

  ![Ajouter un ordinateur virtuel à un service cloud existant](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3.  Lorsque vous remplissez les informations relatives à la machine virtuelle, sélectionnez le nom du service cloud créé lors de la première étape.

  ![Sélection d'un service cloud pour une machine virtuelle](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
