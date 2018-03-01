
Si votre problème avec Azure n’est pas traité dans cet article, parcourez les [forums Azure sur MSDN et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez publier votre problème sur ces forums ou sur @AzureSupport sur Twitter. Vous pouvez également créer une demande de support Azure en sélectionnant **Obtenir de l’aide** sur le site du [support Azure](https://azure.microsoft.com/support/options/) .

## <a name="general-troubleshooting-steps"></a>Étapes de dépannage générales
### <a name="troubleshoot-common-allocation-failures-in-the-classic-deployment-model"></a>Résoudre les problèmes courants liés à l’allocation dans le modèle de déploiement classique
Ces étapes peuvent aider à résoudre de nombreux échecs d’allocation survenant au niveau des machines virtuelles :

* Redimensionnez la machine virtuelle à une taille différente.<br>
    Cliquez sur **Parcourir tout** > **Machines virtuelles (classiques)** > votre machine virtuelle > **Paramètres** > **Taille**. Pour connaître la procédure détaillée, consultez [Redimensionner la machine virtuelle](https://msdn.microsoft.com/library/dn168976.aspx).
* Supprimez toutes les machines virtuelles du service cloud et recréez-les.<br>
    Cliquez sur **Parcourir tout** > **Machines virtuelles (classiques)** > votre machine virtuelle > **Supprimer**. Ensuite, cliquez sur **Créer une ressource** > **Calcul** > [image de machine virtuelle].

### <a name="troubleshoot-common-allocation-failures-in-the-azure-resource-manager-deployment-model"></a>Résoudre les problèmes courants liés à l’allocation dans le modèle de déploiement Azure Resource Manager
Ces étapes peuvent aider à résoudre de nombreux échecs d’allocation survenant au niveau des machines virtuelles :

* Arrêtez (libérez) toutes les machines virtuelles dans le même groupe à haute disponibilité, puis redémarrez chacune d’elles.<br>
    Procédure d’arrêt : cliquez sur **Groupes de ressources** > votre groupe de ressources > **Ressources** > votre groupe à haute disponibilité > **Machines virtuelles** > votre machine virtuelle > **Arrêter**.
  
    Une fois toutes les machines virtuelles arrêtées, sélectionnez la première et cliquez sur **Démarrer**.

## <a name="background-information"></a>Informations contextuelles
### <a name="how-allocation-works"></a>Fonctionnement de l’allocation
Les serveurs des centres de données Azure sont partitionnés en clusters. En règle générale, les tentatives de demande d’allocation sont exécutées dans plusieurs clusters, mais il est possible que certaines contraintes de la demande d’allocation forcent la plateforme Azure à exécuter la tentative dans un seul cluster. Dans cet article, nous parlerons « d’épinglage à un cluster ». La Figure 1 ci-dessous illustre le cas d’une allocation normale dans laquelle la tentative est exécutée dans plusieurs clusters. La Figure 2 illustre le cas d’une allocation épinglée au Cluster 2, là où est hébergé le service cloud CS_1 ou le groupe à haute disponibilité.
![Schéma d’allocation](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Raisons des échecs d’une allocation
Lorsqu’une demande d’allocation est épinglée à un cluster, il y a plus de risque de ne pas trouver les ressources disponibles puisque le pool de ressources disponibles est réduit. En outre, si votre demande d’allocation est épinglée à un cluster alors que le type de ressource que vous avez demandé n’est pas pris en charge par ce cluster, votre demande échouera, même si le cluster comporte des ressources disponibles. La Figure 3 ci-dessous illustre le cas d’une allocation épinglée qui échoue car le seul cluster candidat ne comporte pas de ressources disponibles. La Figure 4 illustre le cas de figure où une allocation épinglée échoue parce que le seul cluster candidat ne prend pas en charge la taille de machine virtuelle demandée, bien qu'il puisse libérer des ressources.

![Échec d’allocation épinglée](./media/virtual-machines-common-allocation-failure/Allocation2.png)

## <a name="detailed-troubleshoot-steps-specific-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Procédure détaillée de résolution des échecs d’allocation spécifiques dans le modèle de déploiement classique
Voici des scénarios courants d’allocation qui entraînent l’épinglage d’une demande d’allocation. Nous aborderons chaque scénario ultérieurement dans cet article.

* Redimensionner une machine virtuelle ou ajouter des machines virtuelles ou des instances de rôle à un service cloud existant
* Redémarrer des machines virtuelles partiellement arrêtées (désallouées)
* Redémarrer des machines virtuelles complètement arrêtées (désallouées)
* Déploiements en environnement intermédiaire ou de production (PaaS uniquement)
* Groupe d’affinités (proximité entre la machine virtuelle et le service)
* Réseau virtuel par groupe d’affinités

Quand vous recevez une erreur d’allocation, voyez si l’un des scénarios décrits s’applique à votre erreur. Utilisez l’erreur d’allocation renvoyée par la plateforme Azure pour identifier le scénario correspondant. Si votre demande est épinglée, supprimez certaines contraintes d’épinglage pour élargir votre demande à plusieurs clusters et augmenter ainsi les chances de succès de l’allocation.

En règle générale, tant que l’erreur n’indique pas que la taille de machine virtuelle demandée n’est pas prise en charge, vous pouvez toujours réessayer ultérieurement, car il est possible que des ressources suffisantes aient été libérées dans le cluster pour prendre en charge votre demande. Si le problème est lié à la non-prise en charge de la taille de machine virtuelle demandée, essayez une autre taille de machine virtuelle. Dans le cas contraire, la seule option consiste à supprimer la contrainte d’épinglage.

Deux scénarios d’échec courants sont liés aux groupes d’affinités. Le groupe d’affinités était auparavant utilisé pour assurer une proximité étroite aux instances de machines virtuelles ou de services, ou encore pour permettre la création d’un réseau virtuel. Avec l’introduction des réseaux virtuels régionaux, les groupes d’affinités ne sont plus nécessaires pour la création d’un réseau virtuel. La réduction du problème de latence du réseau dans l’infrastructure Microsoft Azure a modifié la recommandation relative à l’utilisation de groupes d’affinités pour la proximité entre la machine virtuelle et le service.

La Figure 5 ci-dessous présente la taxonomie des scénarios d’allocation (épinglés).
![Taxonomie d’une allocation épinglée](./media/virtual-machines-common-allocation-failure/Allocation3.png)

> [!NOTE]
> L’erreur répertoriée dans chaque scénario d’allocation est une forme abrégée. Pour consulter les chaînes d’erreur détaillées, reportez-vous à la section [Recherche de chaîne d’erreur](#Error string lookup).
> 
> 

## <a name="allocation-scenario-resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Scénario d’allocation : redimensionner une machine virtuelle ou ajouter des machines virtuelles ou des instances de rôle à un service cloud existant
**Error**

Upgrade_VMSizeNotSupported ou GeneralError

**Cause de l’épinglage au cluster**

Une demande de redimensionnement d’une machine virtuelle ou d’ajout d’une machine virtuelle ou d’une instance de rôle à un service cloud existant doit être exécutée sur le cluster d’origine qui héberge le service cloud existant. La création d’un service cloud permet à la plateforme Azure de trouver un autre cluster disposant de ressources ou prenant en charge la taille de machine virtuelle que vous avez demandée.

**Solution de contournement**

Si l’erreur est Upgrade_VMSizeNotSupported*, essayez une autre taille de machine virtuelle. Si vous ne pouvez pas utiliser une autre taille de machine virtuelle, mais qu’il est possible d’utiliser une autre adresse IP virtuelle (VIP), créez un service cloud pour héberger la nouvelle machine virtuelle puis ajoutez le nouveau service cloud au réseau virtuel régional où sont exécutées les machines virtuelles existantes. Si votre service cloud n’utilise pas de réseau virtuel régional, vous pouvez toujours en créer un pour le nouveau service cloud, puis connecter votre [réseau virtuel à celui créé](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Pour plus d’informations, consultez [Réseaux virtuels régionaux](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Si l’erreur est GeneralError*, le type de ressource (par exemple, une taille de machine virtuelle particulière) est probablement pris en charge par le cluster, mais ce dernier ne peut pas libérer de ressources pour le moment. Comme dans le scénario ci-dessus, ajoutez de la ressource de calcul en créant un service cloud (notez que le nouveau service cloud doit utiliser une adresse IP virtuelle différente) et utilisez un réseau virtuel régional pour connecter vos services cloud.

## <a name="allocation-scenario-restart-partially-stopped-deallocated-vms"></a>Scénario d’allocation : redémarrer des machines virtuelles partiellement arrêtées (désallouées)
**Error**

GeneralError*

**Cause de l’épinglage au cluster**

Une désallocation partielle signifie que vous avez arrêté (désalloué) une ou plusieurs machines virtuelles, mais pas toutes, dans un service cloud. Quand vous arrêtez (désallouez) une machine virtuelle, les ressources associées sont libérées. Le redémarrage de cette machine virtuelle constitue donc une nouvelle demande d’allocation. Redémarrer des machines virtuelles dans un service cloud partiellement désalloué équivaut à ajouter des machines virtuelles à un service cloud existant. La demande d’allocation doit être exécutée sur le cluster d’origine qui héberge le service cloud existant. La création d’un service cloud différent permet à la plateforme Azure de trouver un autre cluster disposant de ressources ou prenant en charge la taille de machine virtuelle que vous avez demandée.

**Solution de contournement**

S’il est possible d’utiliser une autre adresse IP virtuelle, supprimez les machines virtuelles arrêtées (désallouées) tout en conservant les disques associés, puis ajoutez les machines virtuelles par le biais d’un autre service cloud. Utilisez le réseau virtuel régional pour connecter vos services cloud :

* Si votre service cloud existant utilise un réseau virtuel régional, ajoutez simplement le nouveau service cloud au même réseau virtuel.
* Si votre service cloud n’utilise pas de réseau virtuel régional, créez-en un pour le nouveau service cloud, puis connectez votre [réseau virtuel existant à celui créé](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Pour plus d’informations, consultez [Réseaux virtuels régionaux](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="allocation-scenario-restart-fully-stopped-deallocated-vms"></a>Scénario d’allocation : redémarrer des machines virtuelles complètement arrêtées (désallouées)
**Error**

GeneralError*

**Cause de l’épinglage au cluster**

Une désallocation complète signifie que vous avez arrêté (désalloué) toutes les machines virtuelles d’un service cloud. Les demandes d’allocation pour redémarrer ces machines virtuelles doivent être exécutées sur le cluster d’origine qui héberge le service cloud. La création d’un service cloud permet à la plateforme Azure de trouver un autre cluster disposant de ressources ou prenant en charge la taille de machine virtuelle que vous avez demandée.

**Solution de contournement**

S’il est possible d’utiliser une autre adresse IP virtuelle, supprimez les machines virtuelles d’origine qui ont été arrêtées (désallouées) tout en conservant les disques associés, puis supprimez le service cloud correspondant (les ressources de calcul associées ont déjà été libérées quand vous avez arrêté (désalloué) les machines virtuelles). Créez un service cloud pour ajouter les machines virtuelles.

## <a name="allocation-scenario-stagingproduction-deployments-platform-as-a-service-only"></a>Scénario d’allocation : déploiements en environnement intermédiaire ou de production (PaaS uniquement)
**Error**

New_General* ou New_VMSizeNotSupported*

**Cause de l’épinglage au cluster**

Les déploiements d’un service cloud en environnement intermédiaire et en environnement de production sont hébergés dans le même cluster. Lorsque vous ajoutez le deuxième déploiement, la demande d’allocation correspondante sera exécutée au niveau du cluster qui héberge le premier déploiement.

**Solution de contournement**

Supprimez le premier déploiement et le service cloud d’origine, puis redéployez le service cloud. Cette action peut avoir pour effet de réaffecter le premier déploiement dans un cluster qui dispose de suffisamment de ressources pour les deux déploiements ou dans un cluster qui prend en charge les tailles de machine virtuelle demandées.

## <a name="allocation-scenario-affinity-group-vmservice-proximity"></a>Scénario d’allocation : groupe d’affinités (proximité entre la machine virtuelle et le service)
**Error**

New_General* ou New_VMSizeNotSupported*

**Cause de l’épinglage au cluster**

Les ressources de calcul affectées à un groupe d’affinités sont liées à un seul cluster. Les nouvelles demandes de ressources de calcul portant sur ce groupe d’affinités sont exécutées dans le même cluster hébergeant les ressources existantes. Cela est vrai que les ressources soient créées par le biais d’un nouveau service cloud ou d’un service cloud existant.

**Solution de contournement**

Si un groupe d’affinités n’est pas nécessaire, n’utilisez pas de groupe d’affinités ou regroupez vos ressources de calcul dans plusieurs groupes d’affinités.

## <a name="allocation-scenario-affinity-group-based-virtual-network"></a>Scénario d’allocation : réseau virtuel par groupe d’affinités
**Error**

New_General* ou New_VMSizeNotSupported*

**Cause de l’épinglage au cluster**

Avant l’introduction des réseaux virtuels régionaux, vous avez dû associer un réseau virtuel à un groupe d’affinités. Les ressources de calcul placées dans un groupe d’affinités sont donc liées aux mêmes contraintes que celles décrites dans le « scénario d’allocation : groupe d’affinités (proximité entre la machine virtuelle et le service) » ci-dessus. Les ressources de calcul sont liées à un cluster.

**Solution de contournement**

Si vous n’avez pas besoin d’un groupe d’affinités, créez un réseau virtuel régional pour les nouvelles ressources que vous souhaitez ajouter, puis [connectez votre réseau virtuel à celui créé](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Pour plus d’informations, consultez [Réseaux virtuels régionaux](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Vous pouvez également [migrer votre réseau virtuel basé sur le groupe d’affinités vers un réseau virtuel régional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), puis ajouter à nouveau les ressources souhaitées.

## <a name="detailed-troubleshooting-steps-specific-allocation-failure-scenarios-in-the-azure-resource-manager-deployment-model"></a>Procédure détaillée de résolution des échecs d’allocation spécifiques dans le modèle de déploiement Azure Resource Manager
Voici des scénarios courants d’allocation qui entraînent l’épinglage d’une demande d’allocation. Nous aborderons chaque scénario ultérieurement dans cet article.

* Redimensionner une machine virtuelle ou ajouter des machines virtuelles ou des instances de rôle à un service cloud existant
* Redémarrer des machines virtuelles partiellement arrêtées (désallouées)
* Redémarrer des machines virtuelles complètement arrêtées (désallouées)

Quand vous recevez une erreur d’allocation, voyez si l’un des scénarios décrits s’applique à votre erreur. Utilisez l’erreur d’allocation renvoyée par la plateforme Azure pour identifier le scénario correspondant. Si votre demande est épinglée à un cluster existant, supprimez certaines contraintes d’épinglage pour élargir votre demande à plusieurs clusters et augmenter ainsi les chances de succès de l’allocation.

En règle générale, tant que l’erreur n’indique pas que la taille de machine virtuelle demandée n’est pas prise en charge, vous pouvez toujours réessayer ultérieurement, car il est possible que des ressources suffisantes aient été libérées dans le cluster pour prendre en charge votre demande. Si le problème est lié à la non-prise en charge de la taille de machine virtuelle demandée, reportez-vous aux solutions de contournement ci-dessous.

## <a name="allocation-scenario-resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Scénario d’allocation : redimensionner une machine virtuelle ou ajouter des machines virtuelles à un groupe à haute disponibilité existant
**Error**

Upgrade_VMSizeNotSupported* ou GeneralError*

**Cause de l’épinglage au cluster**

Une demande pour redimensionner une machine virtuelle ou ajouter une machine virtuelle à un groupe à haute disponibilité existant doit être exécutée sur le cluster d’origine qui héberge le groupe à haute disponibilité existant. La création d’un groupe à haute disponibilité permet à la plateforme Azure de trouver un autre cluster disposant de ressources ou prenant en charge la taille de machine virtuelle que vous avez demandée.

**Solution de contournement**

Si l’erreur est Upgrade_VMSizeNotSupported*, essayez une autre taille de machine virtuelle. Si vous ne pouvez pas utiliser une autre taille de machine virtuelle, arrêtez toutes les machines virtuelles dans le groupe à haute disponibilité. Vous pouvez ensuite modifier la taille de la machine virtuelle, ce qui vous permettra d’allouer la machine virtuelle à un cluster prenant en charge la taille de machine virtuelle souhaitée.

Si l’erreur est GeneralError*, le type de ressource (par exemple, une taille de machine virtuelle particulière) est probablement pris en charge par le cluster, mais ce dernier ne peut pas libérer de ressources pour le moment. Si la machine virtuelle peut faire partie d’un autre groupe à haute disponibilité, créez une machine virtuelle dans un autre groupe à haute disponibilité (dans la même région). Cette nouvelle machine virtuelle peut ensuite être ajoutée au même réseau virtuel.  

## <a name="allocation-scenario-restart-partially-stopped-deallocated-vms"></a>Scénario d’allocation : redémarrer des machines virtuelles partiellement arrêtées (désallouées)
**Error**

GeneralError*

**Cause de l’épinglage au cluster**

Une désallocation partielle signifie que vous avez arrêté (désalloué) une ou plusieurs machines virtuelles, mais pas toutes, dans un groupe à haute disponibilité. Quand vous arrêtez (désallouez) une machine virtuelle, les ressources associées sont libérées. Le redémarrage de cette machine virtuelle constitue donc une nouvelle demande d’allocation. Redémarrer des machines virtuelles dans un groupe à haute disponibilité partiellement désalloué équivaut à ajouter des machines virtuelles à un groupe à haute disponibilité existant. La demande d’allocation doit être exécutée sur le cluster d’origine qui héberge le groupe à haute disponibilité existant.

**Solution de contournement**

Arrêtez toutes les machines virtuelles du groupe à haute disponibilité avant de redémarrer la première machine. Vous pourrez ainsi effectuer une nouvelle tentative d’allocation et sélectionner un nouveau cluster ayant une capacité disponible.

## <a name="allocation-scenario-restart-fully-stopped-deallocated"></a>Scénario d’allocation : redémarrer des machines virtuelles complètement arrêtées (désallouées)
**Error**

GeneralError*

**Cause de l’épinglage au cluster**

Une désallocation complète signifie que vous avez arrêté (désalloué) toutes les machines virtuelles d’un groupe à haute disponibilité. La demande d’allocation pour redémarrer ces machines virtuelles ciblera tous les clusters prenant en charge la taille souhaitée.

**Solution de contournement**

Sélectionnez une nouvelle taille de machine virtuelle à allouer. Si cette opération ne fonctionne pas, réessayez ultérieurement.

<a name="Error string lookup"></a>

## <a name="error-string-lookup"></a>Recherche de chaîne d’erreur
**New_VMSizeNotSupported***

« La taille de machine virtuelle (ou une combinaison de tailles de machines virtuelles) requise par ce déploiement ne peut pas être mise en service en raison des contraintes de la demande de déploiement. Si possible, essayez de relâcher les contraintes telles que les liaisons de réseau virtuel, de déployer vers un service hébergé sans aucun autre déploiement et vers un autre groupe d’affinités ou sans groupe d’affinités, ou essayez de déployer vers une autre région. »

**New_General***

« L’allocation a échoué. Impossible de satisfaire aux contraintes spécifiées dans la demande. Le nouveau déploiement de service demandé est lié à un groupe d’affinités ou cible un réseau virtuel, ou un déploiement existant se trouve sous ce service hébergé. Toutes ces conditions réduisent le nouveau déploiement à certaines ressources Azure spécifiques. Veuillez réessayer ultérieurement, ou bien essayez de réduire la taille de la machine virtuelle ou le nombre d’instances de rôle. Vous pouvez aussi éventuellement supprimer les contraintes mentionnées précédemment ou essayer de déployer dans une autre région. »

**Upgrade_VMSizeNotSupported***

« Impossible de mettre à niveau le déploiement. La taille de la machine virtuelle XXX demandée n’est peut-être pas disponible dans les ressources prenant en charge le déploiement existant. Réessayez plus tard, utilisez une autre taille de machine virtuelle ou diminuez le nombre d’instances de rôle, ou créez un déploiement dans un service hébergé, avec une liaison à un nouveau groupe d’affinités ou sans liaison de groupe d’affinités. »

**GeneralError***

« Le serveur a rencontré une erreur interne. Relancez la demande. » Ou « Impossible de produire une allocation pour le service. »

