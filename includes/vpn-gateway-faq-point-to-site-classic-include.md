Ce FAQ s’applique aux connexions P2S utilisant le modèle de déploiement classique.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Quels systèmes d’exploitation clients puis-je utiliser avec une connexion de point à site ?

Les systèmes d’exploitation clients pris en charge sont les suivants :

* Windows 7 (32 bits et 64 bits)
* Windows Server 2008 R2 (64 bits uniquement)
* Windows 8 (32 bits et 64 bits)
* Windows 8.1 (32 bits et 64 bits)
* Windows Server 2012 (64 bits uniquement)
* Windows Server 2012 R2 (64 bits uniquement)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>Puis-je utiliser un client VPN logiciel pour une connexion point à site qui prend en charge le protocole SSTP ?

Non. La prise en charge est limitée aux versions de système d'exploitation Windows répertoriées ci-dessus.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Combien de points de terminaison clients VPN puis-je avoir dans ma configuration point à site ?

Nous prenons en charge jusqu'à 128 clients VPN pouvant se connecter à un réseau virtuel en même temps.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Puis-je utiliser ma propre AC racine PKI interne pour une connectivité point à site ?

Oui. Auparavant, seuls les certificats racines auto-signés pouvaient être utilisés. Vous pouvez toujours charger 20 certificats racine.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Puis-je parcourir les serveurs proxy et les pare-feu à l’aide de la fonctionnalité point à site ?

Oui. Nous utilisons SSTP (Secure Socket Tunneling Protocol) pour avoir un tunnel traversant les pare-feu. Ce tunnel apparaît comme une connexion HTTPS.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Si je redémarre un ordinateur client avec une configuration point à site, le réseau VPN va-t-il se reconnecter automatiquement ?

Par défaut, l'ordinateur client ne rétablit pas automatiquement la connexion VPN.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>La configuration point à site prend-elle en charge la reconnexion automatique et DDNS sur les clients VPN ?

La reconnexion automatique et DDNS ne sont actuellement pas pris en charge dans les configurations VPN point à site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Puis-je avoir des configurations coexistantes site à site et point à site pour un même réseau virtuel ?

Oui. Ces deux solutions fonctionnent si vous avez un type de réseau VPN basé sur un itinéraire pour votre passerelle. Pour le modèle de déploiement Classic, vous avez besoin d’une passerelle dynamique. Nous ne prenons pas en charge la configuration point à site pour les passerelles VPN à routage statique ou les passerelles utilisant l’applet de commande `-VpnType PolicyBased`.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Puis-je configurer un client point à site pour me connecter à plusieurs réseaux virtuels en même temps ?

Oui, vous pouvez. Néanmoins les réseaux virtuels ne peuvent pas avoir de préfixes IP qui se chevauchent, et les espaces d’adressage point à site ne doivent pas se chevaucher entre les réseaux virtuels.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Quel débit puis-je attendre des connexions site à site ou point à site ?

Il est difficile de maintenir le débit exact des tunnels VPN. IPsec et SSTP sont des protocoles VPN de chiffrement lourd. Le débit est également limité par la latence et la bande passante entre vos locaux et Internet.
