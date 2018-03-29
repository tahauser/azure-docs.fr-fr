# [Documentation Azure Network Watcher](index.md)

# Vue d'ensemble
## [Qu’est-ce que Network Watcher ?](network-watcher-monitoring-overview.md)
## [Topologie](network-watcher-topology-overview.md)
## [Capture de paquets variable](network-watcher-packet-capture-overview.md)
## [Journalisation des flux de groupe de sécurité réseau](network-watcher-nsg-flow-logging-overview.md)
## [Vérification des flux IP](network-watcher-ip-flow-verify-overview.md)
## [Affichage des groupes de sécurité](network-watcher-security-group-view-overview.md)
## [Tronçon suivant](network-watcher-next-hop-overview.md)
## [Résolution des problèmes de connectivité VPN](network-watcher-troubleshoot-overview.md)
## [Résoudre les problèmes de connexion](network-watcher-connectivity-overview.md)
## [Limites du service](../azure-subscription-service-limits.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-watcher-limits)

# Prise en main
## [Configurer Network Watcher](network-watcher-create.md)
## Gérer les captures de paquets
### [Portail Azure](network-watcher-packet-capture-manage-portal.md)
### [Azure PowerShell](network-watcher-packet-capture-manage-powershell.md)
### [Azure CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
### [interface de ligne de commande Azure](network-watcher-packet-capture-manage-cli.md)
### [REST](network-watcher-packet-capture-manage-rest.md)
## Vérifier les flux IP vers et depuis une machine virtuelle
### [Portail Azure](network-watcher-check-ip-flow-verify-portal.md)
### [Azure PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
### [Azure CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
### [interface de ligne de commande Azure](network-watcher-check-ip-flow-verify-cli.md)
### [REST](network-watcher-check-ip-flow-verify-rest.md)
## Analyser un tronçon suivant
### [Portail Azure](network-watcher-check-next-hop-portal.md)
### [Azure PowerShell](network-watcher-check-next-hop-powershell.md)
### [Azure CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
### [interface de ligne de commande Azure](network-watcher-check-next-hop-cli.md)
### [REST](network-watcher-check-next-hop-rest.md)
## Afficher la topologie du réseau
### [Azure PowerShell](network-watcher-topology-powershell.md)
### [Azure CLI 1.0](network-watcher-topology-cli-nodejs.md)
### [interface de ligne de commande Azure](network-watcher-topology-cli.md)
### [REST](network-watcher-topology-rest.md)
## Résoudre les problèmes de connexions et de passerelle VPN
### [Portail Azure](network-watcher-troubleshoot-manage-portal.md)
### [Azure PowerShell](network-watcher-troubleshoot-manage-powershell.md)
### [Azure CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
### [interface de ligne de commande Azure](network-watcher-troubleshoot-manage-cli.md)
### [REST](network-watcher-troubleshoot-manage-rest.md)
## Analyser l’affichage des groupes de sécurité réseau
### [Azure PowerShell](network-watcher-security-group-view-powershell.md)
### [Azure CLI 1.0](network-watcher-security-group-view-cli-nodejs.md)
### [interface de ligne de commande Azure](network-watcher-security-group-view-cli.md)
### [REST](network-watcher-security-group-view-rest.md)
## Résoudre les problèmes des connexions
### [Portail Azure](network-watcher-connectivity-portal.md)
### [Azure PowerShell](network-watcher-connectivity-powershell.md)
### [interface de ligne de commande Azure](network-watcher-connectivity-cli.md)
### [REST](network-watcher-connectivity-rest.md)
## Configurer des journaux de flux NSG
### [Portail Azure](network-watcher-nsg-flow-logging-portal.md)
### [Azure PowerShell](network-watcher-nsg-flow-logging-powershell.md)
### [Azure CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
### [interface de ligne de commande Azure](network-watcher-nsg-flow-logging-cli.md)
### [REST](network-watcher-nsg-flow-logging-rest.md)

# Procédures
## [Surveiller une passerelle VPN à l’aide d’Azure Automation](network-watcher-monitor-with-azure-automation.md)
## [Diagnostiquer la connectivité locale via une passerelle VPN](network-watcher-diagnose-on-premises-connectivity.md)
## [Surveiller les connexions réseau](connection-monitor.md)
## [Effectuer des tâches de conformité et d’audit sur votre réseau à l’aide de PowerShell](network-watcher-nsg-auditing-powershell.md)
## [Analyser les journaux Application Gateway dans Azure Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)
## Capture de paquet
### [Rechercher des anomalies](network-watcher-deep-packet-inspection.md)
### [Surveillance proactive du réseau avec Azure Functions](network-watcher-alert-triggered-packet-capture.md)
### [Effectuer la détection d’intrusion à l’aide d’outils open source](network-watcher-intrusion-detection-open-source-tools.md)
### [Visualiser les modèles de trafic réseau à l’aide d’outils open source](network-watcher-using-open-source-tools.md)
## Analyser les journaux de flux NSG
### [Lire des journaux de flux NSG](network-watcher-read-nsg-flow-logs.md)
### Analyse du trafic
#### [Utiliser l’analyse du trafic](traffic-analytics.md)
#### [Forum Aux Questions](traffic-analytics-faq.md)
### [Utiliser Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
### [Utiliser la pile élastique](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
### [Utiliser Grafana](network-watcher-nsg-grafana.md)
### [Utiliser Graylog](network-watcher-analyze-nsg-flow-logs-graylog.md)
## [Déterminer la latence relative entre un emplacement et une région Azure](view-relative-latencies.md)

# Informations de référence
## [interface de ligne de commande Azure](/cli/azure/network/watcher)
## [Azure PowerShell](/powershell/module/azurerm.network/#network_watcher)
## [Java](/java/api/com.microsoft.azure.management.network)
## [Ruby](http://www.rubydoc.info/gems/azure_mgmt_network/Azure/Network/Mgmt/V2016_09_01/Models/NetworkWatcher) 
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.network.html#azure.mgmt.network.NetworkManagementClient.network_watchers)

## [.NET](/dotnet/api)
## [REST](/rest/api/networkwatcher/)
# Rubriques connexes
## [Réseau virtuel](/azure/virtual-network/)
## [Machines virtuelles](/azure/virtual-machines/)
## [Passerelle VPN](/azure/vpn-gateway/)
# Ressources
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork)
## [Tarification](https://azure.microsoft.com/pricing/details/network-watcher/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=network-watcher)
## [Contrat SLA](https://azure.microsoft.com/support/legal/sla/)
## [Dépassement de capacité de la pile](http://stackoverflow.com/questions/tagged/network-watcher)
