# [Documentation sur la passerelle VPN](index.md)

# Vue d'ensemble
## [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md)
## [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md)
## [Limites du service et de l’abonnement](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# Prise en main
## [Planification et conception de la passerelle VPN](vpn-gateway-plan-design.md)
## [À propos des paramètres de la passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md)
## [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md)
## [À propos des exigences de chiffrement](vpn-gateway-about-compliance-crypto.md)
## [À propos des passerelles VPN et BGP](vpn-gateway-bgp-overview.md)
## [À propos de la connectivité hautement disponible](vpn-gateway-highlyavailable.md)
## [À propos des connexions de point à site](point-to-site-about.md)

# Procédure
## Configurer des connexions de site à site
### [Portail Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [interface de ligne de commande Azure](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Portail Azure (classique)](vpn-gateway-howto-site-to-site-classic-portal.md)

## [Télécharger des script de configuration de périphérique VPN](vpn-gateway-download-vpndevicescript.md)

## Configurer des connexions de point à site - authentification par certificat Azure natif
### Configurer un VPN de point à site
#### [Portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [Portail Azure (classique)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### Générer des certificats auto-signés
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [Créer et installer les fichiers de configuration du client VPN](point-to-site-vpn-client-configuration-azure-cert.md)
### [Installer les certificats clients](point-to-site-how-to-vpn-client-install-azure-cert.md)

## Configurer des connexions de point à site - Authentification RADIUS
### Configurer un VPN de point à site
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [Créer et installer les fichiers de configuration du client VPN](point-to-site-vpn-client-configuration-radius.md)
### [Intégrer l’authentification RADIUS VPN P2S avec le serveur NPS](vpn-gateway-radiuis-mfa-nsp.md)

## Configurer des connexions de réseau virtuel à réseau virtuel
### [Portail Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [interface de ligne de commande Azure](vpn-gateway-howto-vnet-vnet-cli.md)
### [Portail Azure (classique)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## Configurer une connexion de réseau virtuel à réseau virtuel entre modèles de déploiement
### [Portail Azure](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## Configurer la coexistence de connexions ExpressRoute et de site à site
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## Configurer plusieurs connexions site à site
### [Portail Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell (classic)](vpn-gateway-multi-site.md)
## Connecter plusieurs appareils VPN en fonction de stratégies
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## Configurer des stratégies IPsec/IKE sur des connexions
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## Configurer les connexions haut actif-actif
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## Configurer BGP pour une passerelle VPN
### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
### [interface de ligne de commande Azure](bgp-how-to-cli.md)
## Configurer un tunneling forcé
### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell (classic)](vpn-gateway-about-forced-tunneling.md)
## Modifier les paramètres de la passerelle réseau locale
### [Portail Azure](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [interface de ligne de commande Azure](vpn-gateway-modify-local-network-gateway-cli.md)
## [Vérifier une connexion de passerelle VPN](vpn-gateway-verify-connection-resource-manager.md)
## [Réinitialiser une passerelle VPN](vpn-gateway-resetgw-classic.md)
## Supprimer une passerelle VPN
### [Portail Azure](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell (classic)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [Configurer une passerelle VPN (déploiement classique)](vpn-gateway-configure-vpn-gateway-mp.md)
## [SKU de passerelle (hérité)](vpn-gateway-about-skus-legacy.md)
## Configurer des appareils VPN tiers
### [Vue d’ensemble et configuration d’Azure](vpn-gateway-3rdparty-device-config-overview.md)
### [Exemple : appareil Cisco ASA (IKEv2/sans BGP)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## [Migration du modèle Classic vers le modèle Resource Manager](vpn-gateway-classic-resource-manager-migration.md)
## [Résolution des problèmes](vpn-gateway-troubleshoot.md)
### [Valider le débit VPN sur un réseau virtuel](vpn-gateway-validate-throughput-to-vnet.md)
### [Paramètres du VPN suggéré par la communauté ou du dispositif de pare-feu](vpn-gateway-third-party-settings.md)
### [Problèmes de connexion point à site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
#### [Problèmes de connexion point à site - client VPN Mac OS X](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
### [Problèmes de connexion site à site](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)
#### [Déconnexion intermittente de la connexion site à site](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)
### [Configurer et valider les connexions VNet ou VPN](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)

# Informations de référence
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell (classic)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST (classique)](https://msdn.microsoft.com/library/jj154113)
## [interface de ligne de commande Azure](/cli/azure/network/vnet-gateway)

# Rubriques connexes
## [Réseau virtuel](/azure/virtual-network/)
## [Application Gateway](/azure/application-gateway/)
## [DNS Azure](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Équilibreur de charge](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# Ressources
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Blog](https://azure.microsoft.com/blog/topics/networking)
## [Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Tarification](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
## [Contrat SLA](https://azure.microsoft.com/support/legal/sla)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
