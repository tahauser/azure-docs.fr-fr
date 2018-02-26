# Vue d'ensemble
## [À propos de la mise en réseau Azure](networking-overview.md)
## Architecture
### [Centres de données virtuels](networking-virtual-datacenter.md)
### [Routage asymétrique avec plusieurs chemins d’accès réseau](../expressroute/expressroute-asymmetric-routing.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conceptions de réseau sécurisé](../best-practices-network-security.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Topologie hub-and-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
### [Meilleures pratiques en matière de sécurité réseau](../security/azure-security-network-security-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Appliances virtuelles réseau hautement disponible](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha )
### [Combiner les méthodes d’équilibrage de charge](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Planifier et concevoir
### [Réseaux virtuels](../virtual-network/virtual-network-vnet-plan-design-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Filtrage du trafic](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Connectivité intersite - VPN](../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Options de connectivité intersite - connexion privée dédiée](../expressroute/expressroute-workflows.md?toc=%2fazure%2fnetworking%2ftoc.json)

##  Concepts
### [Réseaux virtuels](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Équilibrage de charge réseau](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Équilibrage de charge application](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [DNS](../dns/dns-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Distribution du trafic DNS](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Se connecter en local - VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Se connecter en local - dédié](../expressroute/expressroute-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)


# Prise en main
## [Créer votre premier réseau virtuel](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Procédures
## Connectivité Internet
### [Équilibrage de charge réseau public serveurs](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Serveurs publics application charge solde](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Protéger les applications web](../application-gateway/application-gateway-web-application-firewall-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Répartir le trafic entre les emplacements](../traffic-manager/traffic-manager-configure-geographic-routing-method.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Connectivité interne
### [Équilibrage de charge réseau privés serveurs](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Serveurs d’application charge solde privés](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Connecter des réseaux virtuels (même emplacement)](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Connecter des réseaux virtuels (emplacements)](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Connectivité intersite
### [Créer une connexion VPN S2S (IPsec/IKE)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Créer une connexion VPN P2S (SSTP avec certificats)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Créer une connexion privée dédiée (ExpressRoute)](../expressroute/expressroute-howto-circuit-portal-resource-manager.md?toc=%2fazure%2fnetworking%2ftoc.json)

## gestion
### [Topologie du réseau](../network-watcher/network-watcher-topology-powershell.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Gérer la capture des paquets](../network-watcher/network-watcher-packet-capture-manage-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Déterminer le saut suivant routage](../network-watcher/network-watcher-check-next-hop-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Vérifier le flux IP pour une machine virtuelle](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Vue d’ensemble de la surveillance réseau](network-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)

## Exemples de scripts
### [interface de ligne de commande Azure](cli-samples.md)
### [Azure PowerShell](powershell-samples.md)

## Didacticiels
### [Équilibrer la charge des machines virtuelles](../virtual-machines/linux/tutorial-load-balance-nodejs.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Connecter un ordinateur à un réseau virtuel](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Informations de référence
## [interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/network)
## [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/?view=azurermps-3.8.0)
## [.Net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.network?view=azuremgmtnetwork-9.1.0-preview)
## [Node.JS](https://azure.microsoft.com/develop/nodejs/#azure-sdk)
## [REST](https://msdn.microsoft.com/library/mt163658.aspx)

# Ressources
## [Créer des modèles](/azure/azure-resource-manager/resource-group-authoring-templates?toc=%2fazure%2fnetworking%2ftoc.json)
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Modèles fournis par la communauté](https://azure.microsoft.com/resources/templates/)
## [Blog sur la mise en réseau](http://azure.microsoft.com/blog/topics/networking)
## [Tarification](https://azure.microsoft.com/pricing)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
## [Disponibilité régionale](https://azure.microsoft.com/regions/services/)
## [Dépassement de capacité de la pile](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Vidéos](https://azure.microsoft.com/resources/videos/index/?services=virtual-network)

