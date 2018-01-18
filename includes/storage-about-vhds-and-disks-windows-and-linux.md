
## <a name="about-vhds"></a>À propos des VHD

Les VHD utilisés dans Azure sont des fichiers .vhd stockés comme objets blob de pages dans un compte de stockage Standard ou Premium dans Azure. Pour des informations sur les objets blob de pages, consultez la page [Présentation des objets blob de blocs et des objets blob de pages](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). Pour plus d’informations sur le stockage Premium, consultez [High-performance premium storage and Azure VMs](../articles/virtual-machines/windows/premium-storage.md) (Stockage Premium hautes performances et machines virtuelles Azure).

Azure prend en charge le format VHD de disque fixe. Le format fixe définit linéairement le disque logique dans le fichier de façon que l'offset du disque X soit stocké à l'offset de l'objet blob X. Un petit pied de page à la fin de l'objet blob décrit les propriétés du disque dur virtuel. Souvent, le format fixe gaspille de l'espace du fait que la plupart des disques comportent de grandes plages inutilisées. Cependant, Azure stocke les fichiers .vhd dans un format fragmenté : vous profitez donc en même temps de disques fixes et dynamiques. Pour plus d’informations, consultez [Prise en main des disques durs virtuels](https://technet.microsoft.com/library/dd979539.aspx).

Dans Azure, tous les fichiers .vhd que vous souhaitez utiliser comme source pour créer des disques ou des images sont en lecture seule, à l’exception des fichiers .vhd téléchargés ou copiés vers le stockage Azure par l’utilisateur (en lecture seule ou en lecture-écriture). Lorsque vous créez un disque ou une image, Azure copie les fichiers .vhd source. Ces copies peuvent être accessibles en lecture seule et en lecture/écriture selon votre utilisation du VHD.

Lorsque vous créez une machine virtuelle à partir d’une image, Azure crée, pour la machine virtuelle, un disque qui est une copie du fichier .vhd source. Pour vous protéger contre une suppression accidentelle, Azure place un bail sur tout fichier .vhd source utilisé pour créer une image, un disque de système d’exploitation ou un disque de données.

Avant de pouvoir supprimer un fichier .vhd source, vous devrez supprimer le bail en supprimant le disque ou l’image. Pour supprimer un fichier .vhd utilisé par une machine virtuelle comme disque de système d’exploitation, vous pouvez supprimer simultanément la machine virtuelle, le disque de système d’exploitation et le fichier .vhd source en supprimant la machine virtuelle et tous les disques associés. Toutefois, la suppression d’un fichier .vhd qui constitue une source pour un disque de données requiert l’exécution de plusieurs étapes dans un ordre défini. Détachez tout d’abord le disque de la machine virtuelle, supprimez le disque, puis supprimez le fichier .vhd.

> [!WARNING]
> Si vous supprimez un fichier .vhd source d’un stockage ou si vous supprimez votre compte de stockage, Microsoft ne pourra pas récupérer ces données pour vous.
> 

## <a name="types-of-disks"></a>Types de disques 

Les disques Azure sont conçus pour offrir une disponibilité de 99,999 %. Les disques Azure ont toujours fourni une durabilité de classe Entreprise pour les disques IaaS, avec un taux de défaillance annuel inégalé dans le secteur de zéro %.

Lorsque vous créez vos disques, vous avez le choix entre deux niveaux de performances : stockage Standard et stockage Premium. Deux types de disques, gérés et non gérés, sont également proposés pour l’un ou l’autre de ces niveaux de performances.


### <a name="standard-storage"></a>Stockage Standard 

Le stockage Standard s’appuie sur des disques durs et offre un stockage économique qui n’en est pas moins performant. Le stockage Standard peut être répliqué localement dans un centre de données ou être géoredondant avec des centres de données principal et secondaire. Pour plus d’informations sur la réplication du stockage, consultez [Réplication du stockage Azure](../articles/storage/common/storage-redundancy.md). 

Pour plus d’informations sur l’utilisation du stockage Standard avec des disques de machine virtuelle, consultez [Standard Storage and Disks](../articles/virtual-machines/windows/standard-storage.md) (Stockage Standard et disques).

### <a name="premium-storage"></a>Stockage Premium 

Le stockage Premium s’appuie sur des disques SSD afin d’assurer de hautes performances et une faible latence pour les machines virtuelles qui exécutent des charges de travail nécessitant de nombreuses E/S. Vous pouvez utiliser le stockage Premium avec les machines virtuelles Azure de série DS, DSv2, GS, Ls, ou FS. Pour plus d’informations, consultez [Stockage Premium](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Disques non gérés

Les disques non gérés sont le type de disques utilisé traditionnellement par les machines virtuelles. Avec ces disques, vous créez votre propre compte de stockage et spécifiez ce dernier lors de la création du disque. Vous devez vous assurer que vous ne placez un trop grand nombre de disques dans le même compte de stockage, car vous risquez de dépasser les [objectifs d’extensibilité](../articles/storage/common/storage-scalability-targets.md) du compte de stockage (20 000 E/S par seconde, par exemple), avec pour conséquence la limitation des machines virtuelles. Avec les disques non gérés, vous devez déterminer comment maximiser l’utilisation d’un ou plusieurs comptes de stockage pour obtenir des performances optimales de vos machines virtuelles.

### <a name="managed-disks"></a>Disques gérés 

Managed Disks gère la création/gestion du compte de stockage en arrière-plan, éliminant les préoccupations liées aux limites d’extensibilité du compte de stockage. Vous spécifiez simplement la taille du disque et le niveau de performances (Standard/Premium) et Azure crée et gère le disque pour vous. Même si vous ajoutez des disques ou faites monter ou descendre en puissance la machine virtuelle, vous n’avez pas à vous soucier du stockage utilisé. 

Vous pouvez également gérer vos images personnalisées dans un compte de stockage par région Azure et les utiliser pour créer des centaines de machines virtuelles dans le même abonnement. Pour plus d’informations sur les disques gérés, consultez [Vue d’ensemble d’Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

Nous vous recommandons d’utiliser Azure Managed Disks pour les nouvelles machines virtuelles et de convertir vos anciens disques non gérés en disques gérés afin de tirer parti des nombreuses fonctionnalités disponibles dans Managed Disks.

### <a name="disk-comparison"></a>Comparaison des disques

Le tableau suivant offre une comparaison des niveaux Premium et Standard pour les disques gérés et non gérés afin de vous aider dans votre choix.

|    | Disque Premium Azure | Disque Standard Azure |
|--- | ------------------ | ------------------- |
| Type de disque | SSD (Solid State Drive) | Disques durs  |
| Vue d'ensemble  | Stockage hautes performances et à faible latence sur disque SSD pour les machines virtuelles qui exécutent des charges de travail nécessitant de nombreuses E/S ou qui hébergent un environnement de production stratégique | Stockage économique sur disque dur pour les machines virtuelles utilisées à des fins de développement/test |
| Scénario  | Charges de travail de production et sensibles aux performances | Développement/test, non stratégique, <br>accès peu fréquent |
| Taille du disque | P4 : 32 Go (Disques gérés uniquement)<br>P6 : 64 Go (Disques gérés uniquement)<br>P10 : 128 Go<br>P20 : 512 Go<br>P30 : 1 024 Go<br>P40 : 2 048 GO<br>P50 : 4095 GO | Disques non gérés : 1 Go – 4 To (4095 GO) <br><br>Disques gérés :<br> S4 : 32 Go <br>S6 : 64 Go <br>S10 : 128 Go <br>S20 : 512 Go <br>S30 : 1 024 Go <br>S40 : 2 048 GO<br>S50 : 4095 GO| 
| Débit max. par disque | 250 Mo/s | 60 Mo/s | 
| Nb max. d’E/S par seconde par disque | 7 500 E/S PAR SECONDE | 500 E/S par seconde | 

