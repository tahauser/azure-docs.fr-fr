# [Documentation Réseau virtuel](index.md)

# Vue d'ensemble
## [Réseaux virtuels](virtual-networks-overview.md)
## [Routage](virtual-networks-udr-overview.md)
## [Homologation de réseaux virtuels](virtual-network-peering-overview.md)
## [Points de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md)
## [Réseau virtuel pour les services Azure](virtual-network-for-azure-services.md)
## [Sécurité](security-overview.md)
## [Mise en réseau de conteneurs](container-networking.md)
## [Continuité de l’activité](virtual-network-disaster-recovery-guidance.md)
## [Adressage IP](virtual-network-ip-addresses-overview-arm.md)
## [Protection DDOS](ddos-protection-overview.md)
## [FORUM AUX QUESTIONS](virtual-networks-faq.md)
## Classique
### [Adressage IP](virtual-network-ip-addresses-overview-classic.md)
### [Listes de contrôle d'accès](virtual-networks-acl.md)

# Prise en main
## [Créer un réseau virtuel - Portail](quick-create-portal.md)
## [Créer un réseau virtuel - PowerShell](quick-create-powershell.md)
## [Créer un réseau virtuel - Azure CLI](quick-create-cli.md)

# Procédure
## Planifier et concevoir
### [Réseaux virtuels](virtual-network-vnet-plan-design-arm.md)
### [Groupes de sécurité réseau](virtual-networks-nsg.md)

## Déployer

### Groupes de sécurité réseau
#### [Azure PowerShell](tutorial-filter-network-traffic.md)
#### [interface de ligne de commande Azure](tutorial-filter-network-traffic-cli.md)
#### Sans groupes de sécurité d’application
##### [Portail Azure](virtual-networks-create-nsg-arm-pportal.md)
##### [Modèle](virtual-networks-create-nsg-arm-template.md)
#### Classique
##### [Azure PowerShell](virtual-networks-create-nsg-classic-ps.md)
##### [Azure CLI 1.0](virtual-networks-create-nsg-classic-cli.md)

### Tables de routage
#### [Portail Azure](tutorial-create-route-table-portal.md)
#### [Azure PowerShell](tutorial-create-route-table-powershell.md)
#### [interface de ligne de commande Azure](tutorial-create-route-table-cli.md)
#### [Modèle](virtual-network-create-udr-arm-template.md)
#### Classique
##### [Azure PowerShell](virtual-network-create-udr-classic-ps.md)
##### [interface de ligne de commande Azure](virtual-network-create-udr-classic-cli.md)

### Homologation de réseaux virtuels
#### Même mode de déploiement - même abonnement
##### [Portail Azure](tutorial-connect-virtual-networks-portal.md)
##### [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md)
##### [interface de ligne de commande Azure](tutorial-connect-virtual-networks-cli.md)
#### [Même mode de déploiement - mêmes abonnements](create-peering-different-subscriptions.md)
#### [Mode de déploiement différent - même abonnement](create-peering-different-deployment-models.md)
#### [Mode de déploiement différent - mêmes abonnements](create-peering-different-deployment-models-subscriptions.md)

### Points de terminaison du service Réseau virtuel
#### [Portail Azure](tutorial-restrict-network-access-to-resources.md)
#### [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md)
#### [interface de ligne de commande Azure](tutorial-restrict-network-access-to-resources-cli.md)

### Machines virtuelles
#### [Débit réseau de machine virtuelle](virtual-machine-network-throughput.md)
#### Créer une machine virtuelle avec une adresse IP publique statique
##### [Portail Azure](virtual-network-deploy-static-pip-arm-portal.md)
##### [Azure PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [interface de ligne de commande Azure](virtual-network-deploy-static-pip-arm-cli.md)
##### [Modèle](virtual-network-deploy-static-pip-arm-template.md)
##### Classique
###### [Azure PowerShell](virtual-networks-reserved-public-ip.md)

#### Créer des machines virtuelles : adresse IP privée statique
##### [Portail Azure](virtual-networks-static-private-ip-arm-pportal.md)
##### [Azure PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [interface de ligne de commande Azure](virtual-networks-static-private-ip-arm-cli.md)
##### Classique
###### [Portail Azure](virtual-networks-static-private-ip-classic-pportal.md)
###### [Azure PowerShell](virtual-networks-static-private-ip-classic-ps.md)
###### [interface de ligne de commande Azure](virtual-networks-static-private-ip-classic-cli.md)

#### Créer des machines virtuelles : plusieurs interfaces réseau
##### [Azure PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [interface de ligne de commande Azure](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Modèle](virtual-network-deploy-multinic-arm-template.md)

##### Classique
###### [Azure PowerShell](virtual-network-deploy-multinic-classic-ps.md)
###### [interface de ligne de commande Azure](virtual-network-deploy-multinic-classic-cli.md)

#### Créer des machines virtuelles : plusieurs adresses IP
##### [Portail Azure](virtual-network-multiple-ip-addresses-portal.md)
##### [Azure PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [interface de ligne de commande Azure](virtual-network-multiple-ip-addresses-cli.md)
##### [Modèle](virtual-network-multiple-ip-addresses-template.md)

#### Créer des machines virtuelles : mise en réseau accélérée
##### [Azure PowerShell](create-vm-accelerated-networking-powershell.md)
##### [interface de ligne de commande Azure](create-vm-accelerated-networking-cli.md)

### Scénarios de connectivité
#### [Réseau virtuel à réseau virtuel](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Réseau virtuel (Resource Manager) à réseau virtuel (classique)](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Réseau virtuel à réseau local (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Réseau virtuel à réseau local (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Architecture réseau hybride hautement disponible](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### Scénarios de sécurité
#### [Sécuriser les réseaux avec des appliances virtuelles](virtual-network-scenario-udr-gw-nva.md)
#### [Zone DMZ entre Azure et Internet](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Service cloud et sécurité réseau](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Créer une zone DMZ avec groupes de sécurité réseau](virtual-networks-dmz-nsg.md)
##### [Créer une zone DMZ avec groupes de sécurité réseau (classique)](virtual-networks-dmz-nsg-asm.md)
##### [Créer une zone DMZ avec pare-feu et groupes de sécurité réseau (classique)](virtual-networks-dmz-nsg-fw-asm.md)
##### [Zone DMZ avec pare-feu, itinéraires définis par l’utilisateur et groupes de sécurité réseau (classique)](virtual-networks-dmz-nsg-fw-udr-asm.md)

##### [Exemple d’application](virtual-networks-sample-app.md)

### Classique
#### [Réseau virtuel](create-virtual-network-classic.md)
##### [Portail Azure](virtual-networks-create-vnet-classic-pportal.md)
##### [Azure PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)
##### [interface de ligne de commande Azure](virtual-networks-create-vnet-classic-cli.md)
#### [Définir des paramètres DNS dans un fichier de configuration de réseau virtuel](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
#### [Spécifier les paramètres DNS dans un fichier de configuration de service](virtual-networks-specifying-dns-settings-in-a-service-configuration-file.md)

## Configuration
### Machines virtuelles
#### [Ajouter ou supprimer des interfaces réseau](virtual-network-network-interface-vm.md)
#### [Résolution de noms pour les machines virtuelles et les services cloud](virtual-networks-name-resolution-for-vms-and-role-instances.md)
#### [Utiliser un DNS dynamique pour inscrire les noms d’hôte sur votre propre serveur DNS](virtual-networks-name-resolution-ddns.md)
#### [Optimiser le débit du réseau](virtual-network-optimize-network-bandwidth.md)
#### [Afficher et modifier les noms d’hôte](virtual-networks-viewing-and-modifying-hostnames.md)
#### Classique
##### Adresses IP statiques
###### [PowerShell](virtual-networks-reserved-private-ip.md)
###### [INTERFACE DE LIGNE DE COMMANDE](virtual-networks-static-private-ip-classic-cli.md)
##### [Adresses IP publiques au niveau de l’instance](virtual-networks-instance-level-public-ip.md)

### Classique
#### Listes de contrôle d'accès
##### [Portail Azure](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure PowerShell](virtual-networks-acl-powershell.md)

## gérer
### [Réseaux virtuels](manage-virtual-network.md)
#### [Sous-réseaux](virtual-network-manage-subnet.md)
#### [Homologations](virtual-network-manage-peering.md)
#### Classique
##### [Fichier de configuration réseau](virtual-networks-using-network-configuration-file.md)
##### [Migrer depuis un groupe d’affinités vers une région](virtual-networks-migrate-to-regional-vnet.md)
### Groupes de sécurité réseau
#### [Portail Azure](virtual-network-manage-nsg-arm-portal.md)
#### [Azure PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [Interface de ligne de commande Azure](virtual-network-manage-nsg-arm-cli.md)

#### [Journaux](virtual-network-nsg-manage-log.md)
### [Tables de routage](manage-route-table.md)
### Interfaces réseau (cartes réseau)
#### [Créer, modifier ou supprimer des cartes réseau](virtual-network-network-interface.md)
#### [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md)
### Machines virtuelles
#### [Déplacer une machine virtuelle vers un autre sous-réseau](virtual-networks-move-vm-role-to-subnet.md)
### [Adresses IP publiques](virtual-network-public-ip-address.md)
### Protection DDOS
#### [Portail Azure](ddos-protection-manage-portal.md)
#### [Azure PowerShell](ddos-protection-manage-ps.md)

## Résolution des problèmes
### Groupes de sécurité réseau
#### [Portail Azure](virtual-network-nsg-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Itinéraires
#### [Portail Azure](virtual-network-routes-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [Test de débit](virtual-network-bandwidth-testing.md)
### [Impossible de supprimer des réseaux virtuels](virtual-network-troubleshoot-cannot-delete-vnet.md)
### [Problèmes de connectivité entre machines virtuelles](virtual-network-troubleshoot-connectivity-problem-between-vms.md)
### [Configurer PTR pour la vérification de la bannière SMTP](create-ptr-for-smtp-service.md)

## Exemples de scripts
### [interface de ligne de commande Azure](cli-samples.md)
### [Azure PowerShell](powershell-samples.md)

# Informations de référence
## [Exemples de code](https://azure.microsoft.com/resources/samples/?service=virtual-network)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.network)
## [Azure PowerShell (Classic)](/powershell/module/azure/)
## [interface de ligne de commande Azure](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (classique)](https://msdn.microsoft.com/library/jj157182.aspx)


# Rubriques connexes
## [Machines virtuelles](/azure/virtual-machines/)
## [Application Gateway](/azure/application-gateway/)
## [DNS Azure](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Équilibreur de charge](/azure/load-balancer/)
## [Passerelle VPN](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# Ressources
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Blog sur la mise en réseau](http://azure.microsoft.com/blog/topics/networking)
## [Forum sur la mise en réseau](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Tarification](https://azure.microsoft.com/pricing/details/virtual-network)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
## [Dépassement de capacité de la pile](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Fournisseur de ressources réseau](resource-groups-networking.md)
