# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>Stockage Premium hautes performances et disques gérés pour machines virtuelles
Le stockage Premium Azure offre une prise en charge très performante et à faible latence des disques pour les machines virtuelles avec des charges de travail qui utilisent beaucoup d’entrée/sortie (E/S). Les disques de machine virtuelle qui utilisent le stockage Premium stockent les données sur des disques SSD. Pour tirer parti de la vitesse et des performances des disques de stockage Premium, vous pouvez migrer les disques de machines virtuelles existantes vers le stockage Premium.

Dans Azure, vous pouvez associer plusieurs disques de stockage Premium à une machine virtuelle. L’utilisation de plusieurs disques permet à vos applications d’atteindre 256 To de stockage par machine virtuelle. Avec le stockage Premium, vos applications peuvent réaliser jusqu’à 80 000 opérations E/S par seconde par machine virtuelle et obtenir un débit de disque maximum de 2 000 mégaoctets par seconde (Mo/s) par machine virtuelle. Les opérations de lecture offrent une latence très faible.

Avec le stockage Premium, Azure offre la possibilité de réellement migrer vers le cloud les applications d’entreprise exigeantes comme Dynamics AX, Dynamics CRM, Exchange Server, SAP Business Suite et les batteries de serveurs SharePoint. Vous pouvez exécuter des charges de travail de bases de données intensives et exigeantes dans des applications comme SQL Server, Oracle, MongoDB, MySQL et Redis, qui nécessitent continuellement des performances élevées et une faible latence.

> [!NOTE]
> Pour que votre application bénéficie de performances optimales, nous vous recommandons de migrer les disques de machine virtuelle nécessitant un nombre élevé d’E/S par seconde dans le stockage Premium. Si votre disque ne nécessite pas une valeur élevée d’E/S par seconde, vous pouvez limiter les coûts en le maintenant en Stockage Azure standard. Dans le cadre d’un stockage standard, les données de disque des machines virtuelles sont stockées sur des disques durs au lieu de disques SSD.
> 

Azure propose deux façons de créer des disques de Stockage Premium pour les machines virtuelles :

* **Disques non gérés**

    La méthode d’origine est d’utiliser des disques non gérés. Sur un disque non géré, vous gérez les comptes de stockage que vous utilisez pour stocker les fichiers de disque dur virtuel (VHD) qui correspondent à vos disques de machine virtuelle. Les fichiers VHD sont stockés en tant qu’objets blob de pages dans les comptes de stockage Azure. 

* **Disques gérés**

    Lorsque vous choisissez [Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md), Azure gère les comptes de stockage que vous utilisez pour vos disques de machine virtuelle. Vous spécifiez le type de disque (Premium ou Standard) et la taille de disque dont vous avez besoin. Azure crée et gère le disque pour vous. Vous n’avez pas à positionner les disques sur plusieurs comptes de stockage afin de garantir le respect des limites d’extensibilité de vos comptes de stockage. Azure gère cela à votre place.

Nous vous recommandons de choisir des disques gérés, pour tirer parti de leurs nombreuses fonctionnalités.

Pour la prise en main du Stockage Premium, [Créez votre compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/). 

Pour plus d’informations sur la migration de vos machines virtuelles existantes vers le stockage Premium, consultez [Convertir une machine virtuelle à partir de disques non gérés vers des disques gérés](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md) ou [Convertir une machine virtuelle Linux à partir de disques non gérés vers des disques gérés](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> Le stockage Premium est disponible dans la plupart des régions. Pour obtenir la liste des régions disponibles, consultez la ligne dédiée au **Stockage sur disque** de la section [Disponibilité des produits par région](https://azure.microsoft.com/regions/#services).
> 

## <a name="features"></a>Caractéristiques

Voici certaines des fonctionnalités du stockage Premium :

* **Disques de stockage Premium**

    Le stockage Premium prend en charge les disques de machines virtuelles pouvant être associés à des machines virtuelles de taille spécifique. Le stockage Premium prend en charge les machines virtuelles des séries DS, DSv2, GS, Ls, Fs et Esv3. Sept tailles de disque différentes vous sont proposées : P4 (32 Go), P6 (64 Go), P10 (128 Go), P20 (512 Go), P30 (1024 Go), P40 (2048 Go), P50 (4095 Go). Les tailles de disque P4 et P6 ne sont actuellement prises en charge que par Managed Disks. Chaque taille de disque a ses propres spécifications en matière de performances. Selon les besoins de votre application, vous pouvez associer un ou plusieurs disques à votre machine virtuelle. Nous donnons plus de détails sur ces spécifications dans [Objectifs de performance et d’extensibilité du stockage Premium](#scalability-and-performance-targets).

* **Objets blob de pages Premium**

    Le stockage Premium prend en charge les objets blob de pages. Utilisez des objets blob de pages pour stocker des disques persistants, non gérés pour les machines virtuelles dans le stockage Premium. Contrairement au stockage Azure standard, le stockage Premium ne prend pas en charge les objets blob de blocs, les objets blob d’ajout, les fichiers, les tables ou les files d’attente. Les objets blob de pages Premium prennent en charge six tailles, de P10 à P50 et P60 (8191 Gio). L’objet de page Premium P60 ne peut pas être joint en tant que disques de machines virtuelles. 

    Tout objet placé dans un compte de stockage Premium est un objet blob de pages. L’objet blob de page s’aligne sur l’une des tailles configurées prise en charge. Un compte de stockage Premium n’est donc pas conçu pour stocker de petits objets blob.

* **Compte de stockage Premium**

    Pour commencer à utiliser le stockage Premium, créez un compte de stockage Premium pour les disques non gérés. Dans le [portail Azure](https://portal.azure.com), pour créer un compte de stockage Premium, choisissez le niveau de performance **Premium**. Sélectionnez l’option de réplication **Stockage localement redondant (LRS)**. Vous pouvez également créer un compte de stockage Premium en définissant le type sur **Premium_LRS** dans un des emplacements suivants :
    * [API REST de stockage](https://docs.microsoft.com/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference) (version 2014-02-14 ou version ultérieure)
    * [API REST de Gestion des services](http://msdn.microsoft.com/library/azure/ee460799.aspx) (version 2014-10-01 ou version ultérieure ; pour les déploiements classiques d’Azure)
    * [API REST du fournisseur de ressources Stockage Azure](https://docs.microsoft.com/rest/api/storagerp) (pour les déploiements Azure Resource Manager)
    * [Azure PowerShell](/powershell/azureps-cmdlets-docs.md) (version 0.8.10 ou version ultérieure)

    Pour en savoir plus sur les limites de compte de stockage Premium, consultez [Objectifs de performance et d’extensibilité du stockage Premium](#premium-storage-scalability-and-performance-targets).

* **Stockage Premium localement redondant**

    Un compte de stockage Premium prend uniquement en charge le stockage localement redondant comme option de réplication. Le stockage localement redondant conserve trois copies des données dans une même région. Pour la récupération d’urgence régionale, vous devez sauvegarder vos disques de machines virtuelles dans une autre région à l’aide de [Sauvegarde Azure](../articles/backup/backup-introduction-to-azure-backup.md). Vous devez également utiliser un compte de stockage géoredondant (GRS) comme coffre de sauvegarde. 

    Azure utilise votre compte de stockage comme conteneur pour vos disques non gérés. Lorsque vous créez une machine virtuelle Azure de série DS, DSv2, GS ou Fs avec des disques non gérés et que vous sélectionnez un compte de stockage Premium, votre système d’exploitation et les disques de données sont stockés dans ce compte de stockage.

## <a name="supported-vms"></a>Machines virtuelles prises en charge
Le stockage Premium prend en charge les machines virtuelles des séries DS, DSv2, GS, Ls, Fs et B. Vous pouvez utiliser des disques de stockage Standard et Premium avec ces types de machines virtuelles. Vous ne pouvez pas utiliser des disques de stockage Premium avec des séries de machines virtuelles qui ne sont pas compatibles avec le stockage Premium.

Pour plus d’informations sur les types et les tailles de machines virtuelles dans Azure pour Windows, consultez [Tailles des machines virtuelles Windows](../articles/virtual-machines/windows/sizes.md). Pour plus d’informations sur les types et les tailles de machines virtuelles dans Azure pour Linux, consultez [Tailles des machines virtuelles Linux](../articles/virtual-machines/linux/sizes.md).

Voici quelques-unes des fonctionnalités des machines virtuelles des séries DS, DSv2, GS, Ls et Fs :

* **Service cloud**

    Vous pouvez ajouter des machines virtuelles de la série DS à un service cloud incluant uniquement des machines virtuelles de la série DS. Évitez d’ajouter des machines virtuelles de la série DS à un service cloud incluant des machines virtuelles d’un autre type. Vous pouvez migrer vos disques durs virtuels vers un nouveau service cloud exécutant uniquement des machines virtuelles de la série DS. Si vous souhaitez utiliser la même adresse IP virtuelle pour le nouveau service cloud hébergeant vos machines virtuelles de la série DS, utilisez les [adresses IP réservées](../articles/virtual-network/virtual-networks-instance-level-public-ip.md). Des machines virtuelles de la série GS peuvent être ajoutées à un service cloud existant incluant uniquement des machines virtuelles de la série GS.

* **Disque de système d’exploitation**

    Vous pouvez configurer votre machine virtuelle de stockage Premium pour utiliser un disque de système d’exploitation Standard ou Premium. Pour une meilleure expérience, nous vous recommandons d’utiliser un disque de système d’exploitation basé sur du stockage Premium.

* **Disques de données**

    Vous pouvez utiliser des disques Standard et Premium dans la même machine virtuelle de stockage Premium. Avec le stockage Premium, vous pouvez configurer une machine virtuelle et associer plusieurs disques de données persistants sur la machine virtuelle. Si nécessaire, pour augmenter la capacité et les performances du volume, vous pouvez répartir les données sur les disques.

    > [!NOTE]
    > Si vous équilibrez les disques de données de stockage Premium à l’aide des [espaces de stockage](http://technet.microsoft.com/library/hh831739.aspx), configurez les espaces de stockage avec 1 colonne pour chaque disque utilisé. Dans le cas contraire, les performances globales du volume agrégé par bandes peuvent être limitées, en raison d’une distribution inégale du trafic sur les disques. Par défaut, dans le Gestionnaire de serveur, vous pouvez définir des colonnes pour 8 disques maximum. Au-delà de 8 disques, utilisez PowerShell pour créer le volume. Spécifiez le nombre de colonnes manuellement. Dans le cas contraire, l’IU du Gestionnaire de serveur continue d’utiliser 8 colonnes, même si vous disposez de plusieurs disques. Par exemple, si vous disposez de 32 disques dans un agrégat unique, spécifiez 32 colonnes. Pour spécifier le nombre de colonnes utilisées par le disque virtuel, dans l’applet de commande PowerShell [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx), utilisez le paramètre *NumberOfColumns*. Pour plus d’informations, consultez [Vue d’ensemble des espaces de stockage](http://technet.microsoft.com/library/hh831739.aspx) et [FAQ sur les espaces de stockage](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
    >
    > 

* **Cache**

    Les machines virtuelles de la gamme de taille prenant en charge le stockage Premium ont une capacité de mise en cache unique des niveaux élevés de débit et de latence. La fonctionnalité de mise en cache dépasse les performances de disque de stockage Premium sous-jacent. Vous pouvez définir la stratégie de mise en cache du disque sur les disques de stockage Premium sur **ReadOnly (lecture seule)**, **ReadWrite (lecture/écriture)** ou **None (aucune)**. La stratégie de mise en cache de disque par défaut est **ReadOnly** pour tous les disques de données Premium, et **ReadWrite** pour les disques de système d’exploitation. Pour obtenir des performances optimales pour votre application, utilisez le paramètre de cache approprié. Ainsi, pour préparer des disques de données lourds ou en lecture seule, contenant par exemple des fichiers de données SQL Server, définissez la stratégie de mise en cache sur **ReadOnly**. Pour écrire des disques de données lourds ou en écriture seule, contenant, par exemple, des fichiers journaux SQL Server, définissez la stratégie de mise en cache sur **None**. Pour en savoir plus sur l’optimisation de votre conception avec le stockage Premium, consultez [Conception optimisée pour les performances avec le stockage Premium](../articles/virtual-machines/windows/premium-storage-performance.md).

* **Analyse**

    Pour analyser les performances des machines virtuelles à l’aide de disques dans le stockage Premium, activez les diagnostics de machines virtuelles dans le [portail Azure](https://portal.azure.com). Pour plus d’informations, consultez [Contrôle des machines virtuelles Microsoft Azure avec l’extension Azure Diagnostics](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/). 

    Pour afficher les performances du disque, utilisez les outils du système d’exploitation, tels que [l’Analyseur de performances Windows](https://technet.microsoft.com/library/cc749249.aspx) pour les machines virtuelles Windows, et la commande [iostat](http://linux.die.net/man/1/iostat) pour les machines virtuelles Linux.

* **Performances et limites de mise à l’échelle des machines virtuelles**

    À chaque taille de machine virtuelle prise en charge par le stockage Premium est associée une spécification relative aux performances et limites de mise à l’échelle en relation avec les opérations d’E/S par seconde, la bande passante et le nombre de disques pouvant être connectés par machine virtuelle. Lorsque vous utilisez des disques de stockage Premium avec des machines virtuelles, assurez-vous de disposer d’une capacité suffisante d’E/S par seconde et de bande passante sur vos machines virtuelles pour gérer le trafic du disque.

    Par exemple, une machine virtuelle STANDARD_DS1 a une bande passante dédiée de 32 Mo/s pour le trafic des disques de stockage Premium. Un disque de stockage Premium P10 peut fournir 100 Mo/s de bande passante. Si un disque de stockage Premium P10 est attaché à cette machine virtuelle, elle ne peut pas excéder 32 Mo/s. Elle ne peut utiliser le maximum de 100 Mo/s que le disque P10 est capable de fournir.

    Actuellement, la machine virtuelle la plus puissante de la série DS est Standard_DS15_v2. Standard_DS15_v2 peut fournir jusqu’à 960 Mo/s sur tous les disques. La machine virtuelle la plus puissante de la série GS est Standard_GS5. Standard_GS5 peut fournir jusqu’à 2 000 Mo/s sur tous les disques.

    Notez que ces limites s’appliquent uniquement au trafic de disques. Ces limites n’incluent pas le trafic réseau et les présences dans le cache. Une bande passante distincte est disponible pour le trafic réseau de machines virtuelles. La bande passante pour le trafic réseau est différente de la bande passante dédiée utilisée par les disques de stockage Premium.

    Pour obtenir les informations les plus récentes au sujet du maximum d’E/S par seconde et de bande passante concernant les machines virtuelles prises en charge par le stockage Premium, consultez [Tailles des machines virtuelles Windows](../articles/virtual-machines/windows/sizes.md) ou [Tailles des machines virtuelles Linux](../articles/virtual-machines/linux/sizes.md).

    Pour plus d’informations sur les disques de stockage Premium et leurs limites d’E/S par seconde et de débit, consultez le tableau dans la section suivante.

## <a name="scalability-and-performance-targets"></a>Cibles de performance et d’évolutivité
Dans cette section, nous décrivons les objectifs de performances et d’extensibilité à prendre en considération lorsque vous utilisez le stockage Premium.

Les objectifs d’extensibilité des comptes de stockage Premium sont les suivants :

| Capacité totale des comptes | Bande passante totale pour un compte de stockage localement redondant |
| --- | --- | 
| Capacité du disque : 35 To <br>Capacité d’instantané : 10 To | Jusqu’à 50 Go par seconde pour les données entrantes <sup>1</sup> et sortantes <sup>2</sup> |

<sup>1</sup> Toutes les données (demandes) envoyées à un compte de stockage

<sup>2</sup> Toutes les données (réponses) reçues d’un compte de stockage

Pour plus d’informations, consultez [Objectifs d’extensibilité et de performances de stockage Azure](../articles/storage/common/storage-scalability-targets.md).

Si vous utilisez des comptes de stockage Premium pour des disques non gérés et que votre application dépasse les objectifs d’extensibilité d’un compte de stockage unique, vous pouvez envisager la migration vers des disques gérés. Si vous ne souhaitez pas migrer vers des disques gérés, générez votre application pour utiliser plusieurs comptes de stockage. Ensuite, partitionnez vos données sur ces comptes de stockage. Par exemple, si vous souhaitez attacher des disques de 51 To sur plusieurs machines virtuelles, répartissez-les entre deux comptes de stockage. La limite pour un compte de stockage Premium unique est de 35 To. Vérifiez qu’un compte de stockage Premium n’a jamais plus de 35 To de disques configurés.

### <a name="premium-storage-disk-limits"></a>Limites des disques de stockage Premium
Lorsque vous configurez un disque de stockage Premium, la taille du disque détermine la valeur maximale d’E/S par seconde et le débit (bande passante). Azure propose sept types de disques de stockage premium : P4 (Managed Disks uniquement), P6 (Managed Disks uniquement), P10, P20, P30, P40 et P50. Chaque type de disque de stockage Premium a des limites d’E/S par seconde et de débit spécifiques. Les limites pour ces types de disques sont décrites dans le tableau suivant :

| Type de disque Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Taille du disque           | 32 Go| 64 Go| 128 Go| 512 Go            | 1024 Go (1 To)    | 2 048 Go (2 To)    | 4 095 Go (4 To)    | 
| IOPS par disque       | 120   | 240   | 500   | 2 300              | 5 000              | 7500              | 7500              | 
| Débit par disque | 25 Mo par seconde  | 50 Mo par seconde  | 100 Mo par seconde | 150 Mo par seconde | 200 Mo par seconde | 250 Mo par seconde | 250 Mo par seconde | 

> [!NOTE]
> Assurez-vous que suffisamment de bande passante soit disponible sur votre machine virtuelle pour le trafic du disque, comme décrit dans [Machines virtuelles prises en charge par le stockage Premium](#premium-storage-supported-vms). Dans le cas contraire, votre débit de disque et les E/S par seconde sont limités à des valeurs moindres. Le débit maximal et les E/S par seconde sont basés sur les limites de la machine virtuelle, pas sur les limites du disque décrites dans le tableau précédent.  
> 
> 

Voici quelques éléments importants à connaître sur les objectifs de performances et d’extensibilité du stockage Premium :

* **Capacité allouée et performances**

    Lorsque vous configurez un disque de stockage Premium, contrairement au stockage standard, la capacité, les E/S par seconde et le débit de ce disque sont assurés. Par exemple, si vous créez un disque P50, Azure configure une capacité de stockage de 4095 Go, 7500 E/S par seconde et un débit de 250 Mo/s pour ce disque. Votre application peut utiliser tout ou partie de la capacité et des performances.

* **Taille du disque**

    Azure mappe la taille du disque (arrondie à la valeur supérieure) sur l’option de disque de stockage Premium la plus proche, comme indiqué dans le tableau dans la section précédente. Par exemple, une taille de disque de 100 Go correspond à l’option P10. Elle peut effectuer jusqu’à 500 E/S par seconde, avec un débit maximum de 100 Mo/s. De même, une taille de disque de 400 Go correspond à l’option P20. Elle peut effectuer jusqu’à 2 300 E/S par seconde, avec un débit de 150 Mo/s.
    
    > [!NOTE]
    > Vous pouvez facilement augmenter la taille des disques existants. Par exemple, vous pouvez augmenter la taille d’un disque de 30 Go à 128 Go ou même 1 To. Ou bien, vous pouvez convertir votre disque P20 en disque P30, si vous avez besoin de davantage de capacités ou de plus d’E/S par seconde et de débit. 
    > 
 
* **Taille des E/S**

    La taille d’une E/S est de 256 Ko. Si la taille des données transférées est inférieure à 256 Ko, elles sont considérées comme 1 unité d’E/S. Les tailles d’E/S supérieures sont divisées en plusieurs unités d’E/S de 256 Ko. Par exemple, 1 100 Ko d’E/S correspondent à 5 unités d’E/S.

* **Débit**

    La limite de débit comprend les écritures sur le disque ainsi que les opérations de lecture sur ce disque qui ne sont pas effectuées à partir du cache. Par exemple, un disque P10 offre un débit de 100 Mo/s. Quelques exemples de débits valides pour un disque P10 figurent dans le tableau suivant :

    | Débit maximum par disque P10 | Lectures à partir du disque non mises en cache | Écritures sur le disque non mises en cache |
    | --- | --- | --- |
    | 100 Mo/s | 100 Mo/s | 0 |
    | 100 Mo/s | 0 | 100 Mo/s |
    | 100 Mo/s | 60 Mo/s | 40 Mo/s |

* **Présences dans le cache**

    Les présences dans le cache ne sont pas limitées par les E/S par seconde ou le débit alloués du disque. Par exemple, quand vous utilisez un disque de données avec le paramètre de cache **ReadOnly** sur une machine virtuelle prise en charge par le stockage Premium, les lectures effectuées à partir du cache ne sont pas soumises aux limites d’E/S par seconde et de débit du disque. Si la charge de travail d’un disque concerne essentiellement des lectures, vous pouvez obtenir un très haut débit. Le cache est soumis à des limites d’E/S par seconde et de débit séparées au niveau de la machine virtuelle, en fonction de la taille de celle-ci. Les machines virtuelles DS exécutent environ 4 000 E/S par seconde et ont un débit de 33 Mo/s par cœur pour les E/S du cache et du disque SSD local. Les machines virtuelles GS ont une limite de 5 000 E/S par seconde et un débit de 50 Mo/s par cœur pour les E/S du cache et du disque SSD local. 

## <a name="throttling"></a>Limitation
La limitation peut se produire si les E/S par seconde de votre application ou son débit dépassent les limites allouées à un disque de stockage Premium. La limitation peut également survenir si le trafic total sur tous les disques de la machine virtuelle dépasse la limite de bande passante disque disponible pour la machine virtuelle. Pour éviter cette situation, nous vous recommandons de limiter le nombre de demandes d’E/S en attente pour le disque. Utilisez une limite basée sur les objectifs d’évolutivité et de performances pour le disque que vous avez configuré et sur la bande passante disque disponible pour la machine virtuelle.  

Quand votre application est conçue pour éviter les limitations, elle bénéficie d'une latence moindre. Toutefois, si le nombre de demandes d’E/S en attente est trop faible, votre application ne peut pas bénéficier des niveaux maximum de débit et d’E/S par seconde disponibles sur le disque.

Les exemples suivants montrent comment calculer les niveaux de limitation. Tous les calculs sont basés sur une taille d’unité d’E/S de 256 Ko.

### <a name="example-1"></a>Exemple 1
Votre application a traité 495 unités d’E/S d’une taille de 16 Ko par seconde sur un disque P10. Les unités d’E/S correspondent à 495 E/S par seconde. Si vous tentez une E/S de 2 Mo dans la même seconde, le total d’unités d’E/S est égal à 495 + 8 E/S par seconde. En effet, une E/S de 2 Mo = 2 048 Ko / 256 Ko = 8 unités d’E/S, avec une taille d’unité d’E/S égale à 256 Ko. Étant donné que la somme de 495 + 8 dépasse la limite de 500 E/S par seconde du disque, il se produit une limitation.

### <a name="example-2"></a>Exemple 2
Votre application a traité 400 unités d’E/S de 256 Ko sur un disque P10. La bande passante totale consommée est de (400 &#215; 256) / 1 024 Ko = 100 Mo/s. Un disque P10 a une limite de débit de 100 Mo/s. Si votre application tente d’effectuer davantage d’opérations d’E/S dans la même seconde, elle sera limitée, car elle dépassera la limite allouée.

### <a name="example-3"></a>Exemple 3
Vous avez une machine virtuelle DS4 avec deux disques P30. Chaque disque P30 peut gérer un débit de 200 Mo/s. Toutefois, une machine virtuelle DS4 a une bande passante disque maximale de 256 Mo/s. Vous ne pouvez pas obtenir un débit maximal sur les deux disques de cette machine virtuelle DS4 en même temps. La solution consiste à maintenir un trafic de 200 Mo/s sur un disque et un trafic de 56 Mo/s sur l’autre disque. Si la somme de votre trafic de disques excède 256 Mo/s, il est limité.

> [!NOTE]
> Si votre trafic de disque consiste essentiellement en des E/S de petite taille, il est probable que votre application atteigne la limite d’E/S par seconde avant la limite de débit. Toutefois, si le trafic du disque consiste essentiellement en des E/S de grande taille, il est probable que votre application atteigne la limite de débit avant la limite d’E/S par seconde. Vous pouvez optimiser les E/S par seconde et la capacité de débit de votre application à l’aide des tailles d’E/S optimales. Vous pouvez également limiter le nombre de demandes d’E/S en attente pour un disque.
> 

Pour en savoir plus sur la conception appropriée permettant des performances élevées à l’aide du stockage Premium, consultez [Conception optimisée pour les performances avec le stockage Premium](../articles/virtual-machines/windows/premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Captures instantanées et copie d’objets blob

Pour le service de stockage, le fichier VHD est un objet blob de pages. Vous pouvez réaliser des captures instantanées d’objets blob de pages et les copier dans un autre emplacement, par exemple dans un compte de stockage différent.

### <a name="unmanaged-disks"></a>Disques non gérés

Créez des [instantanés incrémentiels](../articles/virtual-machines/linux/incremental-snapshots.md) pour les disques Premium non gérés, de la même manière que vous utilisez des captures instantanées avec le stockage Standard. Le stockage Premium prend uniquement en charge le stockage localement redondant comme option de réplication. Nous vous recommandons de créer des captures instantanées et de les copier sur un compte de stockage Standard géoredondant. Pour plus d’informations, consultez [Options de redondance du stockage Azure](../articles/storage/common/storage-redundancy.md).

Si un disque est attaché à une machine virtuelle, certaines opérations d’API ne sont pas autorisées sur les disques. Par exemple, vous ne pouvez pas effectuer une opération de [copie d’un objet blob](/rest/api/storageservices/Copy-Blob) sur cet objet blob, tant que le disque est attaché à une machine virtuelle. Au lieu de cela, créez d’abord un instantané de cet objet blob à l’aide de l’API REST [Snapshot Blob](/rest/api/storageservices/Snapshot-Blob). Exécutez ensuite l’opération de [copie de l’objet blob](/rest/api/storageservices/Copy-Blob) de l’instantané pour copier le disque attaché. Vous pouvez également dissocier le disque et effectuer les opérations nécessaires.

Les limites suivantes s’appliquent aux captures instantanées d’objets blob de stockage Premium :

| Limites de stockage Premium | Valeur |
| --- | --- |
| Nombre maximal d’instantanés par objet blob | 100 |
| Capacité du compte de stockage pour les instantanés<br>(Inclut uniquement les données des instantanés. N’inclut pas celles d’un objet blob de base.) | 10 To |
| Intervalle de temps minimal entre deux instantanés consécutifs | 10 minutes |

Pour conserver des copies géo-redondantes de vos captures instantanées, vous pouvez copier des captures instantanées d'un compte de stockage Premium vers un compte de stockage Standard géo-redondant à l'aide des opérations AzCopy ou Copy Blob. Pour plus d’informations, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](../articles/storage/common/storage-use-azcopy.md) et [Copie d’un objet blob](/rest/api/storageservices/Copy-Blob).

Pour plus d’informations sur l’exécution d’opérations REST sur les objets blob de pages dans les comptes de stockage Premium, consultez [Opérations de service blob avec le stockage Premium Azure ](http://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Disques gérés

Une capture instantanée d’un disque géré est une copie en lecture seule du disque géré. La capture instantanée est stockée comme un disque géré standard. Actuellement, les [instantanés incrémentiels](../articles/virtual-machines/windows/incremental-snapshots.md) ne sont pas pris en charge pour les disques gérés. Pour savoir comment réaliser une capture instantanée d’un disque géré, consultez [Créer une copie d’un disque dur virtuel stocké en tant que disque Azure géré à l’aide de captures instantanées gérées dans Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md) ou [Créer une copie d’un disque dur virtuel stocké en tant que disque Azure géré à l’aide de captures instantanées gérées dans Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md).

Si un disque géré est attaché à une machine virtuelle, certaines opérations d’API ne sont pas autorisées sur les disques. Par exemple, vous ne pouvez pas générer une signature d’accès partagé (SAP) pour effectuer une opération de copie alors que le disque est associé à une machine virtuelle. Au lieu de cela, commencez par créer une capture instantanée du disque, puis réalisez-en une copie. Sinon, vous pouvez aussi détacher le disque, puis générer une signature d’accès partagé (SAP) pour réaliser l’opération de copie.


## <a name="premium-storage-for-linux-vms"></a>Stockage Premium pour les machines virtuelles Linux
Vous pouvez utiliser les informations suivantes pour vous aider à configurer vos machines virtuelles Linux dans le stockage Premium :

Pour atteindre les objectifs d’évolutivité dans le stockage Premium, pour tous les disques de stockage Premium dont le paramètre de cache est **ReadOnly** ou **None**, vous devez désactiver les « barrières » lorsque vous montez le système de fichiers. Ces barrières sont inutiles dans ce cas de figure, car les écritures sur les disques de stockage Premium sont pérennes avec ces paramètres de cache. Lorsque la demande d’écriture se termine, les données sont stockées dans le magasin permanent. Utilisez l’une des méthodes suivantes pour désactiver les « barrières ». Choisissez celle qui correspond à votre système de fichiers :
  
* Pour **reiserFS**, pour désactiver les barrières, utilisez l’option de montage `barrier=none`. (Pour activer les barrières, utilisez `barrier=flush`.)
* Pour **ext3/ext4**, pour désactiver les barrières, utilisez l’option de montage `barrier=0`. (Pour activer les barrières, utilisez `barrier=1`.)
* Pour **XFS**, pour désactiver les barrières, utilisez l’option de montage `nobarrier`. (Pour activer les barrières, utilisez `barrier`.)
* Pour les disques de stockage Premium avec une mise en cache définie sur **ReadWrite**, activez les barrières de durabilité de l’écriture.
* Pour conserver les étiquettes de volume après avoir redémarré la machine virtuelle, vous devez mettre à jour /etc/fstab avec les références identificateur unique universel (UUID) aux disques. Pour plus d’informations, consultez [Ajouter un disque géré à une machine virtuelle Linux](../articles/virtual-machines/linux/add-disk.md).

Les distributions Linux suivantes ont été validées pour le stockage Premium Azure. Pour améliorer les performances et la stabilité du stockage Premium, nous vous recommandons au minimum de mettre vos machines virtuelles au niveau de l’une de ces versions (ou d’une version ultérieure). Certaines versions nécessitent également la dernière version 4.0 de Linux Integration Services (LIS) pour Azure. Pour télécharger et installer une distribution, suivez le lien figurant dans le tableau suivant. Nous ajoutons des images à la liste à chaque validation. Notez que nos validations indiquent que les performances varient pour chaque image. Les performances dépendent des caractéristiques de la charge de travail et de vos paramètres d’image. Chaque image est optimisée pour des charges de travail particulières.

| Distribution | Version | Noyau pris en charge | Détails |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-Server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-Server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 requis](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Consultez la remarque dans la section suivante* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 recommandé](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Consultez la remarque dans la section suivante* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 ou RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 ou RHCK avec [LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 ou RHCK avec [LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Pilotes LIS pour Openlogic CentOS

Si vous exécutez des machines virtuelles OpenLogic CentOS, exécutez la commande suivante pour installer les pilotes les plus récents :

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Pour activer les nouveaux pilotes, redémarrez l’ordinateur.

## <a name="pricing-and-billing"></a>Tarification et facturation

Les considérations de facturation suivantes s’appliquent lorsque vous utilisez le stockage Premium :

* **Taille de disque de stockage Premium et de l’objet blob**

    La facturation pour un disque de stockage Premium ou un objet blob dépend de la taille configurée du disque ou de l’objet blob. Azure mappe la taille configurée (arrondie à la valeur supérieure) à l’option de disque de stockage Premium la plus proche. Pour plus d’informations, consultez le tableau dans [Objectifs de performance et d’extensibilité du stockage Premium](#premium-storage-scalability-and-performance-targets). Chaque disque mappe sur une taille de disque configurée prise en charge et est facturé en conséquence. La facturation de n’importe quel disque configuré est calculée au prorata horaire sur la base du tarif mensuel de l’offre de stockage Premium. Par exemple, si vous configurez un disque P10 et le supprimez au bout de 20 heures, vous êtes facturé 20 heures pour l'offre P10. Le montant facturé est indépendant de la quantité de données écrites sur le disque ou de la quantité de débit et E/S par seconde utilisés.

* **Captures instantanées de disques non gérés Premium**

    Les captures instantanées sur les disques non gérés Premium sont facturées en fonction de la capacité supplémentaire utilisée par les captures instantanées. Pour plus d’informations sur les captures instantanées, consultez [Créer un instantané d’objet blob](/rest/api/storageservices/Snapshot-Blob).

* **Captures instantanées de disques gérés Premium**

    Une capture instantanée d’un disque géré est une copie en lecture seule du disque. Le disque est stocké comme un disque géré standard. Le coût d’une capture instantanée est le même que celui d’un disque géré Standard. Par exemple, si vous avez une capture instantanée d’un disque géré Premium de 128 Go, le coût de la capture instantanée équivaut à celui d’un disque géré Standard de 128 Go.  

* **Transferts de données sortantes**

    Les [Transferts de données sortantes](https://azure.microsoft.com/pricing/details/data-transfers/) (données sortant des centres de données Azure) sont facturés en fonction de la bande passante utilisée.

Pour plus d’informations sur la tarification du stockage Premium, les machines virtuelles prises en charge par le stockage Premium et les disques gérés, consultez ces articles :

* [Tarification du stockage Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Tarification des disques gérés](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Support Sauvegarde Azure 

Pour la récupération d’urgence régionale, vous devez sauvegarder vos disques de machines virtuelles dans une autre région à l’aide de [Sauvegarde Azure](../articles/backup/backup-introduction-to-azure-backup.md) et d’un compte de stockage GRS comme coffre de sauvegarde.

Pour créer une tâche de sauvegarde avec des sauvegardes périodiques, une restauration facile des machines virtuelles et des stratégies de rétention de sauvegarde, utilisez Sauvegarde Azure. Vous pouvez utiliser la sauvegarde avec des disques gérés et non gérés. Pour plus d’informations, consultez [Sauvegarde Azure de machines virtuelles avec des disques non gérés](../articles/backup/backup-azure-vms-first-look-arm.md) et [Sauvegarde Azure de machines virtuelles avec des disques gérés](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le stockage Premium, voir les articles suivants.

### <a name="design-and-implement-with-premium-storage"></a>Conception et implémentation avec le stockage Premium
* [Conception optimisée pour les performances avec le stockage Premium](../articles/virtual-machines/windows/premium-storage-performance.md)
* [Opérations de stockage Blob avec le stockage Premium](http://go.microsoft.com/fwlink/?LinkId=521969)

### <a name="operational-guidance"></a>Instructions d’utilisation
* [Migration vers le stockage Azure Premium](../articles/storage/common/storage-migration-to-premium-storage.md)

### <a name="blog-posts"></a>Billets de blog :
* [Mise à la disposition générale du stockage Premium Azure](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
* [Annonce de la série GS : ajout de prise en charge du stockage Premium pour les machines virtuelles les plus grandes dans le cloud Public](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)
