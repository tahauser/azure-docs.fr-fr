Les organisations ont des besoins informatiques à grande échelle. Ces problèmes informatiques à grande échelle comprennent l’analyse et la conception d’ingénierie, les calculs de risques financiers, le rendu d’image, la modélisation complexe, les simulations Monte Carlo, etc. 

Utilisez le cloud Azure pour exécuter efficacement des charges de travail de calcul Linux et Windows nécessitant beaucoup de ressources système, allant de travaux par lots parallèles à des simulations de calcul haute performance classique. Exécutez vos charges de travail HPC et de lot sur une infrastructure Azure, selon votre choix de services de calcul, de gestionnaires de grilles, de solutions de la Place de marché et d’applications hébergées par le revendeur (SaaS). Azure offre des solutions flexibles pour répartir le travail et mettre à l’échelle de milliers de machines virtuelles ou de noyaux, et descendre en puissance s’il vous faut moins de ressources. 



## <a name="solution-options"></a>Options de la solution



* **Solutions en libre service**
    * Configurez votre environnement de cluster dans des machines virtuelles Azure ou dans des [groupes de machines virtuelles identiques](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 
    * Effectuez la migration « lift-and-shift » d’un cluster local, ou déployez un nouveau cluster dans Azure pour une capacité plus importante. 
    * Utilisez les modèles Azure Resource Manager pour déployer des [gestionnaires de charge de travail](#workload-managers) principaux, des infrastructures et des [applications](#hpc-applications). 
    * Choisissez les [tailles de HPC et de GPU de la machine virtuelle](#hpc-and-gpu-sizes), comprenant des matériels et connexions réseau spécialisés pour les charges de travail MPI et GPU. 
    * Ajoutez du [stockage de haute performance](#hpc-storage) aux charges de travail intensives d’E/S.
* **Solutions hybrides**
    * Étendre votre solution locale à des charges de travail de déchargement en pic (« burst ») dans une infrastructure Azure
    * Utilisez les calculs du cloud à la demande avec votre [gestionnaire de charges de travail](#workload-manager) existant.
    * Tirez parti des [tailles de HPC et de GPU de la machine virtuelle](#hpc-and-gpu-sizes) pour les charges de travail MPI et GPU.
* **Solutions Big Compute en tant que service**
    * Développez des solutions Big Compute et des worklows personnalisés à l’aide d’[Azure Batch](#azure-batch) et autres [services Azure](#related-azure-services) associés.
    * Exécutez des solutions de simulation et d’ingénierie pour Azure de vendeurs tels que [Altair](http://www.altair.com/), [Rescale](https://www.rescale.com/azure/) et [Cycle Computing](https://cyclecomputing.com/) (et [Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/) depuis peu).
* **Solutions de la Place de marché**
    * Utilisez la mise à l’échelle des [solutions](#marketplace-solutions) et des [applications HPC](#hpc-applications)offertes dans la [Place de marché Azure](https://azuremarketplace.microsoft.com/). 
    


La section suivante fournit plus d’informations sur les technologies prises en charge et des liens pour obtenir des conseils.



## <a name="marketplace-solutions"></a>Solutions de la Place de marché

Visitez la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/) pour les solutions et images de machines virtuelles Linux et Windows conçues pour HPC. Voici quelques exemples :

* [HPC basé sur RogueWave CentOS](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server (SLES)](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [TIBCO Grid Server Engine](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Machine virtuelle de science des données Azure pour Windows et Linux](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [D3View](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)
* [Intel Cloud Edition pour Lustre Software](https://azuremarketplace.microsoft.com/marketplace/apps/intel.lustre-cloud-edition-eval?tab=Overview)


 
## <a name="hpc-applications"></a>Applications HPC

Exécuter des applications HPC commerciales ou personnalisées dans Azure. Plusieurs exemples dans cette section ont été testés et se montrent efficaces pour la mise à l’échelle avec des machines virtuelles ou des cœurs de calcul supplémentaires. Visitez la [Place de marché Azure](https://marketplace.azure.com) pour obtenir des solutions prêtes au déploiement.

> [!NOTE]
> Vérifiez auprès du fournisseur de toute application commerciale les questions de licence ou toute autre restriction relative à l’exécution dans le cloud. Tous les fournisseurs ne proposent pas le paiement à l'utilisation pour les licences. Vous aurez peut-être besoin d’un serveur de licences dans le cloud pour votre solution ou de vous connecter à un serveur de licences sur site.

### <a name="engineering-applications"></a>Applications d’ingénierie


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB Distributed Computing Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM +](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>Graphiques et rendu

* [Autodesk Maya, 3ds Max et Arnold](../articles/batch/batch-rendering-service.md) sur Azure Batch 

### <a name="ai-and-deep-learning"></a>Intelligence artificielle et apprentissage approfondi

* Formation [Batch AI](../articles/batch-ai/overview.md) pour les modèles d’apprentissage profond
* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Machine virtuelle d’apprentissage profond](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Recettes Batch Shipyard pour l’apprentissage approfondi](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)






## <a name="hpc-and-gpu-vm-sizes"></a>Tailles de machines virtuelles HPC et GPU
Azure propose une plage de tailles de machines virtuelles [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) et [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), y compris des tailles conçues pour des charges de travail nécessitant beaucoup de ressources système. Par exemple, des machines virtuelles H16r et H16mr peuvent se connecter à un réseau RDMA principal au débit élevé. Ce réseau cloud peut améliorer les performances d’applications parallèles étroitement liées s’exécutant sous [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) ou Intel MPI. 

Les machines virtuelles de série N comportent des processeurs graphiques NVIDIA conçus pour des applications graphiques ou de calcul nécessitant beaucoup de ressources système, y compris la visualisation et l’apprentissage de l’intelligence artificielle (AI). 

En savoir plus :

* Tailles HPC pour machines virtuelles [Linux](../articles/virtual-machines/linux/sizes-hpc.md) et [Windows](../articles/virtual-machines/windows/sizes-hpc.md) 
* Tailles GPU pour machines virtuelles [Linux](../articles/virtual-machines/linux/sizes-gpu.md) et [Windows](../articles/virtual-machines/windows/sizes-gpu.md) 

Découvrez comment :

* [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configuration d’un cluster Windows RDMA avec Microsoft HPC Pack pour exécuter des applications MPI](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Utiliser des machines virtuelles nécessitant beaucoup de ressources système dans des pools Batch](../articles/batch/batch-pool-compute-intensive-sizes.md)



## <a name="azure-batch"></a>Azure Batch
[Batch](../articles/batch/batch-technical-overview.md) est une plateforme qui permet d’exécuter efficacement des applications de calcul haute performance (HPC) en parallèle et à grande échelle dans le cloud. Azure Batch planifie les travaux nécessitant une grande quantité de ressources système à exécuter sur un pool géré de machines virtuelles. Il peut mettre automatiquement à l’échelle les ressources de calcul pour répondre aux besoins de vos travaux. 

Les fournisseurs et développeurs SaaS peuvent utiliser les outils et kits de développement logiciel (SDK) pour intégrer des applications HPC ou des charges de travail de conteneur dans Azure, stocker des données dans Azure et générer des pipelines d’exécution du travail. 

Découvrez comment :

* [Commencer à développer avec Batch](../articles/batch/quick-run-dotnet.md)
* [Utiliser les exemples de code Azure Batch](https://github.com/Azure/azure-batch-samples)
* [Utiliser des machines virtuelles de faible priorité avec Batch](../articles/batch/batch-low-pri-vms.md)
* [Exécuter des charges de travail HPC en conteneur avec Batch Shipyard](https://github.com/Azure/batch-shipyard)
* [Utiliser le langage R avec Batch](https://github.com/Azure/doAzureParallel)
* [Exécuter le kit de ressources Engineering données distribuées Azure sur Batch](https://github.com/Azure/aztk)

## <a name="workload-managers"></a>Gestionnaires de charges de travail

Voici quelques exemples de cluster et de gestionnaires de charges de travail qui peuvent s’exécuter dans une architecture Azure. Créer des clusters autonomes dans des machines virtuelles Azure ou effectuez un burst dans les machines virtuelles Azure depuis un cluster local. 
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM Spectrum Symphony and Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org)
* [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029(v=ws.11).aspx) : consultez les options pour exécuter dans des machines virtuelles [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) et [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 



## <a name="hpc-storage"></a>Stockage HPC

Les charges de travail HPC et Batch à grande échelle nécessitent un stockage des données et un accès à ces dernières dépassant les capacités des systèmes de fichiers cloud classiques. Implémentez des solutions de système de fichiers parallèles dans Azure comme [Lustre](http://lustre.org/) et [BeeGFS](http://www.beegfs.com/content/).

En savoir plus :

* [Systèmes de fichiers parallèles pour le stockage HPC sur Azure](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)
* Solutions de stockage cloud hautes performances [d’Avere](http://www.averesystems.com/about-us/about-avere) (désormais [joint à Microsoft](https://blogs.microsoft.com/blog/2018/01/03/microsoft-to-acquire-avere-systems-accelerating-high-performance-computing-innovation-for-media-and-entertainment-industry-and-beyond/))


## <a name="related-azure-services"></a>Services Azure connexes

La plupart des solutions HPC Azure reposent sur les machines virtuelles Azure, les groupes de machines virtuelles identiques, Batch et les services de calcul associés. Toutefois, votre solution peut bénéficier de nombreux services Azure associés. En voici la liste non-exhaustive.

### <a name="storage"></a>Stockage

* [Stockage Objet BLOB, Table et File d’attente](../articles/storage/storage-introduction.md)
* [Stockage Fichier](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Données et analyse
* [HDInsight](../articles/hdinsight/hadoop/apache-hadoop-introduction.md)
* [Data Factory](../articles/data-factory/introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Machine Learning](../articles/machine-learning/machine-learning-what-is-machine-learning.md)
* [Base de données SQL](../articles/sql-database/sql-database-technical-overview.md)

### <a name="ai-and-cognitive-services"></a>Intelligence artificielle et services cognitifs
* [Batch AI](../articles/batch-ai/overview.md)

### <a name="networking"></a>Mise en réseau
* [Réseau virtuel](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Conteneurs
* [Service de conteneur](../articles/container-service/dcos-swarm/container-service-intro.md)
* [Container Registry](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>Témoignages client

Voici des exemples de clients ayant résolu des problèmes d’entreprise à l’aide de solutions Azure HPC :

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&amp;C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les solutions Big Compute pour la [simulation d’ingénierie](https://simulation.azure.com/), le [rendu](https://simulation.azure.com/), les [banques et marchés financiers](https://finance.azure.com/) et la [génomique](https://enterprise.microsoft.com/en-us/industries/health/genomics/).
* Pour les dernières annonces, consultez le [blog de l’équipe Microsoft HPC et Batch](http://blogs.technet.com/b/windowshpc/) et le [blog Azure](https://azure.microsoft.com/blog/tag/hpc/).

* Utiliser le service Azure [Batch](https://azure.microsoft.com/services/batch/) géré et évolutif pour exécuter des charges de travail nécessitant beaucoup de ressources système sans gérer d’infrastructures sous-jacentes [En savoir plus](https://azure.microsoft.com/en-us/solutions/architecture/hpc-big-compute-saas/)



