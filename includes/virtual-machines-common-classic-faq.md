


Cet article aborde certaines questions courantes sur les machines virtuelles Azure créées avec le modèle de déploiement classique.

## <a name="can-i-migrate-my-vm-created-in-the-classic-deployment-model-to-the-new-resource-manager-model"></a>Puis-je migrer mes machines virtuelles créées dans le modèle de déploiement classique vers le nouveau modèle Resource Manager ?
Oui. Pour obtenir des instructions sur la migration, consultez :

* [Migration d’un modèle de déploiement classique vers Azure Resource Manager à l’aide d’Azure PowerShell](../articles/virtual-machines/windows/migration-classic-resource-manager-ps.md).
* [Migration d’un modèle de déploiement classique vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure](../articles/virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md).

## <a name="what-can-i-run-on-an-azure-vm"></a>Qu’est-il possible d’exécuter sur une machine virtuelle Azure ?
Tous les abonnés peuvent exécuter des logiciels serveur sur une machine virtuelle Azure. Vous pouvez exécuter des versions récentes de Windows Server, ainsi que diverses distributions de Linux. Pour les détails de prise en charge, consultez les liens suivants :

• Pour les machines virtuelles Windows : [Prise en charge logicielle du serveur Microsoft pour les machines virtuelles Microsoft Azure](http://go.microsoft.com/fwlink/p/?LinkId=393550)

• Pour les machines virtuelles Linux : [Linux dans des distributions prises en charge par Azure](http://go.microsoft.com/fwlink/p/?LinkId=393551)

Pour les images du client Windows, certaines versions de Windows 7 et Windows 8.1 sont disponibles pour les abonnés MSDN Azure et les abonnés Développement et test MSDN avec paiement à l’utilisation (pour les tâches de test et de développement). Pour plus d’informations, notamment des instructions et des limitations, voir [Images de client Windows pour les abonnés MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/).

## <a name="why-are-affinity-groups-being-deprecated"></a>Pourquoi les groupes d’affinités sont-ils déconseillés ?
Les groupes d’affinités sont un concept hérité du regroupement géographique des déploiements de service cloud et des comptes de stockage d’un client dans Azure. À l’origine, ils ont été créés pour améliorer les performances des réseaux entre machines virtuelles dans les premiers réseaux Azure. Ils prenaient également en charge la version initiale des réseaux virtuels, qui se limitaient à un sous-ensemble restreint de matériels dans une région.

Dans une région, le réseau Azure actuel est conçu de sorte que les groupes d’affinités ne sont plus nécessaires. Les réseaux virtuels sont également présents à l’échelon régional, de sorte qu’aucun groupe d’affinités n’est requis en cas d’utilisation d’un réseau virtuel. En raison de ces améliorations, l’utilisation des groupes d’affinités n’est plus recommandée, car ils peuvent se révéler contraignants dans certains cas. L’utilisation de groupes d’affinités associe inutilement vos machines virtuelles à un matériel spécifique qui limite le choix des tailles de machines virtuelles à votre disposition. Elle peut également entraîner des erreurs de capacité lors de l’ajout de nouvelles machines virtuelles, si le matériel associé au groupe d’affinités est presque saturé.

Les fonctionnalités des groupes d’affinités sont déjà déconseillées dans le modèle de déploiement Azure Resource Manager et sur le portail Azure. Dans le portail Azure classique, nous déconseillons la prise en charge de la création de groupes d’affinités et de la création de ressources de stockage épinglées à un groupe d’affinités. Il est inutile de modifier les services cloud qui utilisent un groupe d’affinités. Toutefois, n’utilisez pas de groupes d’affinités pour les nouveaux services cloud, sauf instruction contraire de la part d’un spécialiste du support Azure.

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quelle quantité de stockage puis-je utiliser avec une machine virtuelle ?
Chaque disque de données peut avoir une capacité allant jusqu’à 1 To Le nombre de disques de données que vous pouvez utiliser dépend de la taille de la machine virtuelle. Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Un compte de stockage Azure fournit le stockage pour le disque du système d’exploitation et tout disque de données. Chaque disque est un fichier .vhd stocké sous la forme d’un objet blob de pages. Pour plus d’informations sur la tarification, voir [Tarification – Stockage](http://go.microsoft.com/fwlink/p/?LinkId=396819).

## <a name="which-virtual-hard-disk-types-can-i-use"></a>Quels types de disque dur virtuel puis-je utiliser ?
Azure prend uniquement en charge les disques durs virtuels fixes au format VHD. Si vous souhaitez utiliser un disque VHDX dans Azure, convertissez-le d’abord à l’aide du Gestionnaire Hyper-V ou de l’applet de commande [convert-VHD](http://go.microsoft.com/fwlink/p/?LinkId=393656) . Une fois l’opération effectuée, utilisez l’applet de commande [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) (en mode de gestion des services) pour télécharger le VHD sur un compte de stockage dans Azure afin de pouvoir l’utiliser avec des machines virtuelles.

* Pour obtenir des instructions concernant Linux, consultez l’article [Création et téléchargement d’un disque dur virtuel contenant le système d’exploitation Linux](../articles/virtual-machines/linux/classic/create-upload-vhd-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="are-these-virtual-machines-the-same-as-hyper-v-virtual-machines"></a>Ces machines virtuelles sont-elles identiques aux machines virtuelles Hyper-V ?
Si elles ressemblent sur de nombreux points aux machines virtuelles Hyper-V de première génération, elles ne sont pas tout à fait identiques. Les deux types fournissent un matériel virtualisé, et les disques durs virtuels de format VHD sont compatibles. Cela signifie que vous pouvez les déplacer entre Hyper-V et Azure. Les trois différences principales qui surprennent parfois les utilisateurs d’Hyper-V sont :

* Azure ne fournit pas un accès console à une machine virtuelle. Il n’existe aucun moyen d’accéder à une machine virtuelle avant d’avoir terminé le démarrage.
* La plupart des [tailles](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) de machines virtuelles Azure ne comportent qu’une seule carte réseau virtuelle, ce qui signifie qu’elles ne peuvent donc avoir qu’une seule adresse IP externe. (Dans certains scénarios limités, les tailles A8 et A9 utilisent une deuxième carte réseau pour la communication des applications entre les instances.)
* Les machines virtuelles Azure ne prennent pas en charge les fonctionnalités des machines virtuelles Hyper-V de deuxième génération. Pour plus d’informations sur ces fonctionnalités, consultez les articles [Virtual Machine Specifications for Hyper-V](http://technet.microsoft.com/library/dn592184.aspx) (Spécifications des machines virtuelles pour Hyper-V) et [Vue d’ensemble de machines virtuelles de Génération 2](https://technet.microsoft.com/library/dn282285.aspx).

## <a name="can-these-virtual-machines-use-my-existing-on-premises-networking-infrastructure"></a>Ces machines virtuelles peuvent-elles utiliser mon infrastructure réseau existante locale ?
Pour les machines virtuelles créées dans le modèle de déploiement classique, vous pouvez utiliser Azure Virtual Network pour étendre votre infrastructure existante. L’approche est comparable à l’installation d’une filiale. Vous pouvez configurer et gérer des réseaux privés virtuels (VPN) dans Azure et les lier à l’infrastructure informatique locale en toute sécurité. Pour plus d’informations, voir [Présentation du réseau virtuel](../articles/virtual-network/virtual-networks-overview.md).

Vous devrez indiquer le réseau auquel vous voulez que la machine virtuelle appartienne au moment de la création de celle-ci. Vous ne pouvez pas joindre une machine virtuelle existante à un réseau virtuel. Toutefois, vous pouvez contourner ce problème en détachant le disque dur virtuel (VHD) d’une machine virtuelle existante, puis l’utiliser pour créer une machine virtuelle avec la configuration réseau de votre choix.

## <a name="how-can-i-access--my-virtual-machine"></a>Comment puis-je accéder à ma machine virtuelle ?
Vous devez vous connecter à la machine virtuelle, en utilisant Connexion Bureau à distance pour une machine virtuelle Windows ou une connexion Secure Shell (SSH) pour une machine virtuelle Linux. Pour obtenir des instructions, consultez les liens suivants :

* [Connexion à une machine virtuelle exécutant Windows Server](../articles/virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 2 connexions simultanées maximum sont prises en charge, sauf si le serveur est configuré en tant qu’hôte de session Services Bureau à distance.  
* [Connexion à une machine virtuelle exécutant Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Par défaut, SSH autorise un maximum de 10 connexions simultanées. Vous pouvez augmenter ce nombre en modifiant le fichier de configuration.

En cas de problème avec Bureau à distance ou SSH, installez et utilisez l’extension [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour résoudre le problème.

Pour les machines virtuelles Windows, les options supplémentaires incluent :

* Dans le portail Azure, recherchez la machine virtuelle, puis cliquez sur **Réinitialiser l’accès à distance** à partir de la barre de commandes.
* Consultez l’article [Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Utilisez Windows PowerShell Remoting pour vous connecter à la machine virtuelle ou créer des points de terminaison supplémentaires pour la connexion d'autres ressources à la machine virtuelle. Pour plus d’informations, consultez l’article [Configuration des points de terminaison sur une machine virtuelle](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Si vous connaissez bien Hyper-V, vous pouvez rechercher un outil similaire à VMConnect. Azure n’offre pas d’outil similaire car l’accès console à une machine virtuelle n’est pas pris en charge.

## <a name="can-i-use-the-temporary-disk-the-d-drive-for-windows-or-devsdb1-for-linux-to-store-data"></a>Puis-je utiliser le disque temporaire (le lecteur D: pour Windows ou /dev/sdb1 pour Linux) pour stocker des données ?
Vous ne devez pas utiliser le disque temporaire (le lecteur D: pour Windows ou /dev/sdb1 pour Linux) pour stocker des données. Ils ne permettent qu’un stockage temporaire, vous risqueriez donc de perdre des données sans pouvoir les récupérer. Cela peut arriver si la machine virtuelle est déplacée vers un autre hôte, après le redimensionnement d’une machine virtuelle, la mise à jour de l’hôte ou une panne matérielle sur l’hôte, par exemple.

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Comment puis-je modifier la lettre de lecteur d’un disque temporaire ?
Sur une machine virtuelle Windows, vous pouvez changer la lettre de lecteur en déplaçant le fichier d’échange et en réaffectant les lettres de lecteur. Toutefois, vous devrez veiller à effectuer les étapes dans le bon ordre. Pour obtenir des instructions, consultez la page [Modification de la lettre de lecteur du disque temporaire Windows](../articles/virtual-machines/windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="how-can-i-upgrade-the-guest-operating-system"></a>Comment puis-je mettre à niveau le système d’exploitation invité ?
Le terme de mise à niveau a généralement pour signification le passage à une version plus récente de votre système d’exploitation tout en conservant le même matériel. Pour les machines virtuelles Azure, le processus de passage à une version plus récente est différent pour Linux et Windows :

* Pour les machines virtuelles Linux, utilisez les outils et procédures de gestion de packages appropriés pour la distribution.
* Pour une machine virtuelle Windows, vous devez migrer le serveur à l’aide d’outils tels que les outils de migration de Windows Server. N’essayez pas de mettre à niveau le système d’exploitation invité résidant sur Azure. Il n’est pas pris en charge en raison du risque de perte de l’accès à la machine virtuelle. En cas de problèmes au cours de la mise à niveau, vous risqueriez de perdre la possibilité de démarrer une session Bureau à distance et vous ne pourriez pas résoudre les problèmes.

Pour des informations générales sur les outils et processus de migration vers Windows Server, consultez la rubrique [Migrer des rôles et des fonctionnalités vers Windows Server](http://go.microsoft.com/fwlink/p/?LinkId=396940).

## <a name="whats-the-default-user-name-and-password-on-the-virtual-machine"></a>Quels sont le nom d’utilisateur et le mot de passe par défaut sur la machine virtuelle ?
Les images fournies par Azure n’ont pas de nom d’utilisateur et de mot de passe préconfigurés. Lorsque vous créez une machine virtuelle à l’aide de l’une de ces images, vous devez fournir un nom d’utilisateur et un mot de passe que vous utiliserez pour vous connecter à la machine virtuelle.

Si vous avez oublié le nom d’utilisateur ou le mot de passe et que vous avez installé l’agent de machine virtuelle, vous pouvez installer et utiliser l’extension [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour résoudre le problème.

Informations supplémentaires :

* Pour les images Linux, si vous utilisez le portail Azure, le nom d’utilisateur par défaut est « azureuser », mais vous pouvez le modifier en utilisant « À partir de la galerie » au lieu de « Création rapide » pour créer la machine virtuelle. La méthode « À partir de la galerie » vous permet également de décider si vous souhaitez un mot de passe, une clé SSH ou les deux pour vous connecter. Le compte « user » est un utilisateur sans privilège ayant un accès « sudo » pour exécuter des commandes privilégiées. Le compte « root » est désactivé.
* Pour les images Windows, vous devez fournir un nom d’utilisateur et un mot de passe lorsque vous créez la machine virtuelle. Le compte est ajouté au groupe Administrateurs.

## <a name="can-azure-run-anti-virus-on-my-virtual-machines"></a>Azure peut-il exécuter un antivirus sur mes machines virtuelles ?
Azure offre plusieurs options de solutions antivirus, mais c’est à vous qu’il incombe de les gérer. Par exemple, vous pouvez avoir besoin d’un abonnement distinct pour un logiciel anti-programme malveillant. Dans ce cas, c’est à vous qu’il reviendra de décider du moment où exécuter les analyses et installer les mises à jour. Vous pouvez ajouter une prise en charge d’antivirus avec une extension de machine virtuelle pour Microsoft Antimalware, Symantec Endpoint Protection ou TrendMicro Deep Security Agent lors de la création d’une machine virtuelle Windows ou à un moment ultérieur. Les extensions Symantec et TrendMicro permettent d’utiliser un abonnement d’évaluation limité dans le temps et gratuit, ou un abonnement d’entreprise existant. Microsoft Antimalware est gratuit. Pour plus d'informations, consultez les rubriques :

* [Installation et configuration de Symantec Endpoint Protection sur une machine virtuelle Azure](http://go.microsoft.com/fwlink/p/?LinkId=404207)
* [Installation et configuration de Trend Micro Deep Security comme service sur une machine virtuelle Azure](http://go.microsoft.com/fwlink/p/?LinkId=404206)
* [Déploiement de solutions anti-programmes malveillants sur des machines virtuelles Azure (en anglais)](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## <a name="what-are-my-options-for-backup-and-recovery"></a>Quelles sont les options disponibles en matière de sauvegarde et de récupération d’urgence ?
Azure Backup est disponible en version préliminaire dans certaines régions. Pour plus d’informations, voir [Sauvegarde des machines virtuelles Azure](../articles/backup/backup-azure-arm-vms.md). D’autres solutions sont disponibles auprès de partenaires certifiés. Pour savoir ce qui est actuellement disponible, faites une recherche dans Azure Marketplace.

Une autre option consiste à utiliser les fonctionnalités de capture instantanée de stockage d’objets blob. Pour ce faire, vous devez arrêter la machine virtuelle avant toute opération qui repose sur un instantané d’objet blob. Cela enregistre les écritures de données en attente et assure la cohérence du fichier système.

## <a name="how-does-azure-charge-for-my-vm"></a>À quel mode de facturation ma machine virtuelle est-elle soumise dans Azure ?
Azure facture un prix horaire basé sur la taille et le système d’exploitation de la machine virtuelle. Pour les heures partielles, Azure vous facture uniquement les minutes d'utilisation. Si vous créez la machine virtuelle à partir d’une image de machine virtuelle contenant certains logiciels préinstallés, des coûts horaires logiciels supplémentaires peuvent s’appliquer. Azure facture séparément le stockage du système d’exploitation et celui des disques de données de la machine virtuelle. Le stockage sur disque temporaire est gratuit.

Vous êtes facturé quand l’état de la machine virtuelle est « En cours d’exécution » ou « Arrêté », mais pas quand elle est en état « Arrêté (désalloué) ». Pour mettre une machine virtuelle en état « Arrêté (désalloué) », effectuez l’une des opérations suivantes :

* Arrêtez ou supprimez la machine virtuelle à partir du portail Azure.
* Utilisez l’applet de commande Stop-AzureVM disponible dans le module Azure PowerShell.
* Utilisez l’opération Shutdown Role dans l’API REST de gestion des services et spécifiez StoppedDeallocated pour l’élément PostShutdownAction.

Pour plus d’informations, voir [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="will-azure-reboot-my-vm-for-maintenance"></a>Dois-je m’attendre à ce qu’Azure redémarre ma machine virtuelle aux fins de maintenance ?
Azure redémarre parfois votre machine virtuelle dans le cadre de mises à jour de maintenance normales et planifiées dans les centres de données Azure.

Des événements de maintenance non planifiés peuvent se produire quand Azure détecte un problème matériel sérieux qui affecte votre machine virtuelle. Pour les événements non planifiés, Azure migre automatiquement la machine virtuelle vers un hôte intègre, puis redémarre la machine virtuelle.

Pour toute machine virtuelle autonome (c’est-à-dire ne faisant pas partie d’un groupe à haute disponibilité), Azure notifie par courrier électronique l’administrateur de service de l’abonnement au moins une semaine avant la maintenance planifiée, car les machines virtuelles pourraient être redémarrées au cours de la mise à jour. Les applications exécutées sur les machines virtuelles peuvent subir un temps d’arrêt.

Vous pouvez également utiliser le Portail Azure ou Azure PowerShell pour afficher les journaux de redémarrage quand le redémarrage s’est produit en raison d’une maintenance planifiée. Pour plus d’informations, voir [Affichage des journaux de redémarrage de machines virtuelles (en anglais)](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/).

Pour assurer la redondance, placez au moins deux machines virtuelles configurées de manière similaire dans le même groupe à haute disponibilité. Cela contribue à garantir qu’au moins une machine virtuelle est disponible pendant la maintenance, planifiée ou non. Azure garantit certains niveaux de disponibilité des machines virtuelles pour cette configuration. Pour plus d’informations, consultez l’article [Gestion de la disponibilité des machines virtuelles](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="additional-resources"></a>Ressources supplémentaires
[À propos de Machines virtuelles Azure](../articles/virtual-machines/virtual-machines-linux-about.md)

[Créer et gérer des machines virtuelles Linux avec l’interface Azure CLI](../articles/virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Créer et gérer des machines virtuelles Windows avec le module Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

