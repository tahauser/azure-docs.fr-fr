<a name="virtual-networking-limits-classic"></a>Les limites suivantes s’appliquent uniquement aux ressources de réseau gérées par le biais du modèle de déploiement classique par abonnement.

| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Réseaux virtuels |50 |100 |
| Sites de réseau local |20 |contacter le support |
| Serveurs DNS par réseau virtuel |20 |100 |
| Adresses IP privées par réseau virtuel |4096 |4096 |
| Flux TCP ou UDP simultanés par carte réseau de machine virtuelle ou instance de rôle |500K |500K |
| Groupes de sécurité réseau (NSG) |100 |200 |
| Règles de groupe de sécurité réseau par groupe de sécurité réseau |200 |400 |
| Tables d'itinéraires définis par l'utilisateur |100 |200 |
| Itinéraires définis par l'utilisateur par table d'itinéraire |100 |400 |
| Adresses IP publiques (dynamiques) |5. |contacter le support |
| Adresses IP publiques réservées |20 |contacter le support |
| Adresse IP virtuelle publique par déploiement |5. |contacter le support |
| Adresse IP virtuelle privée (ILB) par déploiement |1 |1 |
| Listes de contrôle d'accès (ACL) par point de terminaison |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limites de mise en réseau – Azure Resource Manager
Les limites suivantes s’appliquent uniquement aux ressources de réseau gérées par le biais d’Azure Resource Manager par région et par abonnement.

| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Réseaux virtuels |50 |1 000 |
| Nombre de sous-réseaux par réseau virtuel |1 000 |10000 |
| Homologations VNet par réseau virtuel |10 |50 |
| Serveurs DNS par réseau virtuel |9. |25 |
| Adresses IP privées par réseau virtuel |4096 |8 192 |
| Adresses IP privées par interface réseau |256 |1 024 |
| Flux TCP ou UDP simultanés par carte réseau de machine virtuelle ou instance de rôle |500K |500K |
| Interfaces réseau (NIC) |350 |20000 |
| Groupes de sécurité réseau (NSG) |100 |5 000 |
| Règles de groupe de sécurité réseau par groupe de sécurité réseau |200 |1 000 |
| Adresses IP et plages spécifiées pour la source et la destination dans un groupe de sécurité |2000 |4000 |
| Groupes de sécurité d’application |200 |500 |
| Groupes de sécurité d’application par configuration IP, par carte réseau |10 |20 |
| Configurations IP par groupe de sécurité d’application |1 000 |4000 |
| Groupes de sécurité d’application qui peuvent être spécifiés dans toutes les règles de sécurité d’un groupe de sécurité réseau |50 |100 |
| Tables d'itinéraires définis par l'utilisateur |100 |200 |
| Itinéraires définis par l'utilisateur par table d'itinéraire |100 |400 |
| Adresses IP publiques (dynamiques) |(De base) 60 |contacter le support |
| Adresses IP publiques (statiques) |(Basic) 20 |contacter le support |
| Adresses IP publiques (statiques) |(Standard) 20 |contacter le support |
| Certificats racines point à site pour chaque passerelle VPN |20 |20 |

#### <a name="load-balancer"></a>Limites de l’équilibreur de charge

| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Équilibreurs de charge | 100 | 1 000 |
| Règles par ressource, De base | 150 | 250 |
| Règles par ressource, Standard | 1250 | 1 500 |
| Règles par configuration IP | 299 |299 |
| Configurations d’adresses IP frontales, De base | 10 | contacter le support |
| Configurations d’adresses IP frontales, Standard | 10 | 600 |
| Pool principal, De base | 100, un seul groupe à haute disponibilité | - |
| Pool principal, Standard | 1000, un seul réseau virtuel | - |
| Ports à haute disponibilité, Standard | 1 par serveur frontal interne | - |

Pour accroître les limites par défaut, [contactez le support technique](../articles/azure-supportability/resource-manager-core-quotas-request.md ).

