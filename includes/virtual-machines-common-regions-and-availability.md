# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Régions et disponibilité des machines virtuelles dans Azure
Azure fonctionne dans plusieurs centres de données à travers le monde. Ces centres de données sont regroupés en régions géographiques, ce qui vous donne une certaine flexibilité dans le choix de l’emplacement où vous créez vos applications. Il est important de comprendre le fonctionnement des machines virtuelles et de connaître leur emplacement dans Azure, ainsi que les différentes options disponibles pour optimiser les performances, la disponibilité et la redondance. Cet article fournit une vue d’ensemble des fonctionnalités d’optimisation de la disponibilité et de redondance d’Azure.

## <a name="what-are-azure-regions"></a>Que représentent les régions Azure ?
Vous créez des ressources Azure dans des régions géographiques définies comme « États-Unis de l'Ouest », « Europe du Nord » ou « Sud-Est asiatique ». Vous pouvez consulter la [liste de ces régions et leurs emplacements](https://azure.microsoft.com/regions/). Chaque région comporte plusieurs centres de données, afin de garantir la redondance et la disponibilité. Lorsque vous concevez vos applications, vous bénéficiez ainsi d’une certaine flexibilité pour créer des machines virtuelles plus proches de vos utilisateurs et pour répondre à n’importe quel besoin juridique, de conformité ou fiscal.

## <a name="special-azure-regions"></a>Régions Azure spéciales
Lors de la création de vos applications, vous pouvez utiliser certaines régions Azure spéciales pour répondre à vos besoins de conformité ou juridiques. Ces régions spéciales sont les suivantes :

* **Gouvernement des États-Unis - Virginie** et **Gouvernement des États-Unis - Iowa**
  * Instance physique et logique d’Azure isolée du réseau pour les administrations et les partenaires des États-Unis, gérée par un personnel autorisé aux États-Unis. Inclut des certifications de conformité supplémentaires comme [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) et [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). En savoir plus sur [Azure Government](https://azure.microsoft.com/features/gov/).
* **Est de la Chine** et **Nord de la Chine**
  * Ces régions sont disponibles grâce à un partenariat unique conclu entre Microsoft et 21Vianet, qui stipule que Microsoft ne gère pas directement les centres de données. En savoir plus sur [Microsoft Azure en Chine](http://www.windowsazure.cn/).
* **Centre de l’Allemagne** et **Nord-Est de l’Allemagne**
  * Ces régions sont disponibles par le biais d’un modèle d’administration des données, dans lequel les données client sont conservées en Allemagne sous le contrôle de T-Systems, société Deutsche Telekom responsable de l’administration des données allemandes.

## <a name="region-pairs"></a>Paires de régions
Chaque région Azure est associée à une autre région au sein de la même région géographie (par exemple, États-Unis, Europe ou Asie). Cette approche permet la réplication des ressources, par exemple le stockage des machines virtuelles, sur l’ensemble d’une région, réduisant ainsi la probabilité de catastrophes naturelles, de troubles civils, de coupures de courant ou de pannes de réseau physique affectant les deux régions en même temps. Les paires de régions offrent les avantages supplémentaires suivants :

* En cas d’une panne Azure plus importante, une région est hiérarchisée dans chaque paire afin de réduire le temps de restauration des applications. 
* Les mises à jour Azure planifiées sont déployées vers les régions jumelées une par une pour limiter les interruptions de service et les risques de panne de l’application.
* Les données continuent de résider dans la même zone géographique que la région avec laquelle elle est jumelée (sauf le Sud du Brésil) pour répondre aux exigences en termes d’impôts et d’application de la loi.

Voici quelques exemples de paires de régions :

| Primaire | Secondaire |
|:--- |:--- |
| États-Unis de l’Ouest |Est des États-Unis |
| Europe du Nord |Europe de l'Ouest |
| Asie du Sud-Est |Est de l'Asie |

Vous pouvez afficher la [liste des paires de régions ici](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Disponibilité des fonctionnalités
Certains services ou fonctionnalités des machines virtuelles ne sont disponibles que dans certaines régions, par exemple des tailles de machines virtuelles ou des types de stockage spécifiques. De même, certains services Azure globaux ne vous obligent pas à sélectionner une région particulière, par exemple [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md), ou [Azure DNS](../articles/dns/dns-overview.md). Pour vous aider à concevoir votre environnement d’application, vous pouvez vérifier la [disponibilité des services Azure dans chaque région](https://azure.microsoft.com/regions/#services). Vous pouvez également [interroger par programmation les tailles de machine virtuelle et les restrictions prises en charge dans chacune des régions](../articles/azure-resource-manager/resource-manager-sku-not-available-errors.md).

## <a name="storage-availability"></a>Disponibilité du stockage
Il est important de bien comprendre les régions géographiques Azure lorsque vous considérez les options de réplication de stockage disponibles. Selon le type de stockage, vous disposez d’options de réplication différentes.

**Azure Managed Disks**
* Stockage localement redondant (LRS)
  * Réplique trois fois vos données dans la région dans laquelle vous avez créé votre compte de stockage.

**Disques basés sur un compte de stockage**
* Stockage localement redondant (LRS)
  * Réplique trois fois vos données dans la région dans laquelle vous avez créé votre compte de stockage.
* Stockage redondant dans une zone (ZRS)
  * Réplique trois fois vos données dans deux ou trois installations au sein d’une même région ou dans deux régions distinctes.
* Stockage géo-redondant (GRS)
  * Réplique vos données vers une région secondaire à des centaines de kilomètres de la région primaire.
* Stockage géo-redondant avec accès en lecture (RA-GRS)
  * Réplique vos données dans une région secondaire, comme avec GRS, mais fournit également un accès en lecture seule aux données dans l’emplacement secondaire.

Le tableau suivant fournit une rapide vue d’ensemble des différences entre les types de réplications de stockage :

| Stratégie de réplication | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Les données sont répliquées entre plusieurs installations. |Non  |OUI |OUI |OUI |
| Les données peuvent être lues à partir de l’emplacement secondaire comme de l’emplacement principal. |Non  |Non  |Non  |OUI |
| Nombre de copies de données conservées sur des nœuds distincts. |3 |3 |6. |6. |

Vous pouvez en savoir plus sur les [options de réplication de stockage Azure ici](../articles/storage/common/storage-redundancy.md). Pour plus d’informations sur les disques gérés, consultez [Vue d’ensemble d’Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

### <a name="storage-costs"></a>Coûts de stockage
Les prix varient selon le type de stockage et la disponibilité que vous sélectionnez.

**Azure Managed Disks**
* Les disques gérés Premium s’appuient sur des disques SSD et les disques gérés Standard sur des disques à rotation classique. Les disques gérés Premium et Standard sont facturés en fonction de la capacité déployée pour le disque.

**Disques non gérés**
* Le stockage Premium s’appuie sur des disques SSD et est facturé en fonction de la capacité du disque.
* Le stockage standard s’appuie sur des disques rotatifs ordinaires et est facturé en fonction de la capacité en cours d’utilisation et la disponibilité de stockage souhaitée.
  * Pour RA-GRS, les frais de transfert de données de géo-réplication supplémentaires correspondent au coût de la bande passante nécessaire à la réplication de ces données vers une autre région Azure.

Consultez [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/) pour connaître les informations de tarification des différents types de stockage et options de disponibilité.

## <a name="availability-sets"></a>Groupes à haute disponibilité
Un groupe à haute disponibilité est un regroupement logique de machines virtuelles au sein d’un centre de données qui permet à Azure de comprendre comment votre application est conçue, afin de garantir la redondance et la disponibilité. Il est recommandé de créer au moins deux machines virtuelles dans un groupe à haute disponibilité, de manière à fournir une application hautement disponible et à répondre aux exigences du [niveau de 99,95 % inscrit dans les contrats de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Le groupe à haute disponibilité proprement dit ne vous coûte rien ; vous payez uniquement pour chaque instance de machine virtuelle que vous créez. Lorsqu’une seule machine virtuelle utilise le [stockage Azure Premium](../articles/virtual-machines/windows/premium-storage.md), le SLA Azure s’applique pour les événements de maintenance non planifiés. 

Un groupe à haute disponibilité est composé de deux regroupements supplémentaires qui vous protègent contre les défaillances matérielles et autorisent l’application des mises à jour en toute sécurité dans des domaines d’erreur (FD) et des domaines de mise à jour (UD). Vous pouvez en savoir plus sur la gestion de la disponibilité des [machines virtuelles Linux](../articles/virtual-machines/linux/manage-availability.md) ou des [machines virtuelles Windows](../articles/virtual-machines/windows/manage-availability.md).

### <a name="fault-domains"></a>Domaines d’erreur
Un domaine d’erreur est un groupe logique de matériels sous-jacents qui partagent la même source d’alimentation et le même commutateur réseau, par exemple un rack dans un centre de données local. Lorsque vous créez des machines virtuelles au sein d’un groupe à haute disponibilité, la plateforme Azure distribue automatiquement vos machines virtuelles dans ces domaines d’erreur. Cette approche limite l’impact des défaillances du matériel physique, des pannes de réseau ou des coupures de courant susceptibles de survenir.

### <a name="update-domains"></a>Domaines de mise à jour
Un domaine de mise à jour est un groupe logique de matériels sous-jacents qui peuvent faire l’objet d’une opération de maintenance ou être redémarrés en même temps. Lorsque vous créez des machines virtuelles au sein d’un groupe à haute disponibilité, la plateforme Azure distribue automatiquement vos machines virtuelles dans ces domaines de mise à jour. Cette approche garantit qu’au moins une instance de votre application reste toujours en cours d’exécution, car la plateforme Azure fait l’objet d’une maintenance périodique. Le redémarrage des domaines de mise à jour peut ne pas suivre un ordre séquentiel au cours de la maintenance planifiée, mais un seul domaine de mise à jour peut être redémarré à la fois.

### <a name="managed-disk-fault-domains"></a>Domaines d’erreur des disques gérés
Les machines virtuelles faisant appel à des [disques gérés Azure](../articles/virtual-machines/windows/faq-for-disks.md) sont alignées sur les domaines d’erreur des disques gérés lorsqu’un groupe à haute disponibilité géré est utilisé. Cet alignement garantit que tous les disques gérés attachés à une machine virtuelle se trouvent dans le même domaine d’erreur de disques gérés. Seules des machines virtuelles avec des disques gérés peuvent être créées dans un groupe à haute disponibilité géré. Le nombre de domaines d’erreur de disques gérés varie en fonction de la région (deux ou trois par région). Vous pouvez en savoir plus sur ces domaines d’erreur de disque géré pour [les machines virtuelles Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) ou [les machines virtuelles Windows](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

## <a name="availability-zones"></a>Zones de disponibilité

Les [Zones de disponibilité](../articles/availability-zones/az-overview.md) (version préliminaire) sont une alternative aux groupes à haute disponibilité, et élargissent le niveau de contrôle dont vous disposez pour maintenir la disponibilité des applications et des données sur vos machines virtuelles. Une zone de disponibilité est une zone physiquement séparée au sein d’une région Azure. On distingue trois zones de disponibilité par région Azure prise en charge. Chaque zone dispose d’une source d’alimentation, d’un réseau et d’un refroidissement. Elles sont toutes séparées logiquement des autres zones de disponibilité de la même région Azure. En concevant vos solutions pour répliquer des machines virtuelles dans des zones, vous pouvez protéger vos applications et vos données de la perte d’un centre de données. Si une zone est compromise, les applications et données répliquées sont instantanément rendues disponibles dans une autre zone. 

![Zones de disponibilité](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

[!INCLUDE [availability-zones-preview-statement.md](availability-zones-preview-statement.md)]

Apprenez-en davantage sur le déploiement d’une machine virtuelle [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) ou [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) dans une zone de disponibilité.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez maintenant commencer à utiliser ces fonctionnalités de gestion de la redondance et de la disponibilité pour créer votre environnement Azure. Pour en savoir plus sur les meilleures pratiques, consultez la rubrique relative aux [meilleures pratiques Azure en matière de disponibilité](../articles/best-practices-availability-checklist.md).

