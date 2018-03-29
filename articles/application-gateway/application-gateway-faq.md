---
title: Forum aux questions pour Azure Application Gateway | Microsoft Docs
description: Cette page fournit des réponses aux questions les plus souvent posées sur Azure Application Gateway
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d54ee7ec-4d6b-4db7-8a17-6513fda7e392
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.openlocfilehash: 5b400b373577fc38fe108a74eb8bad936a82be0c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Forum aux questions pour Azure Application Gateway

## <a name="general"></a>Généralités

**Q. Qu’est-ce qu’Application Gateway ?**

Azure Application Gateway est un contrôleur de remise d’application proposé sous la forme d’un service, qui offre diverses fonctionnalités d’équilibrage de charge de couche 7 pour vos applications. Il fournit un service hautement disponible et évolutif, entièrement géré par Azure.

**Q. Quelles sont les fonctionnalités prises en charge par Application Gateway ?**

Application Gateway prend en charge le déchargement SSL et SSL de bout en bout, le pare-feu d’application web, l’affinité de session basée sur les cookies, le routage basé sur le chemin d’accès de l’URL, l’hébergement de plusieurs sites, et bien plus encore. Pour obtenir une liste complète des fonctionnalités prises en charge, consultez [Vue d’ensemble d’Application Gateway](application-gateway-introduction.md).

**Q. Quelle est la différence entre Application Gateway et Azure Load Balancer ?**

Application Gateway est un équilibreur de charge de couche 7, ce qui signifie qu’il fonctionne avec le trafic web uniquement (HTTP/HTTPS/WebSocket). Il prend en charge des fonctionnalités telles que la terminaison SSL, l’affinité de session basée sur les cookies et le tourniquet (round robin) pour le trafic d’équilibrage de charge. Load Balancer équilibre la charge du trafic au niveau de la couche 4 (TCP/UDP).

**Q. Quels sont les protocoles pris en charge par Application Gateway ?**

Application Gateway prend en charge les protocoles HTTP, HTTPS et WebSocket.

**Q. Quelles sont les ressources actuellement prises en charge dans le pool backend ?**

Les pools backend peuvent être composés de cartes d’interface réseau, de groupes de machines virtuelles identiques, d’adresses IP publiques, d’adresses IP internes, de noms de domaine complets et de serveurs principaux multi-locataires comme Azure Web Apps. Les membres du pool backend d’Application Gateway ne sont pas liés à un groupe à haute disponibilité. Les membres des pools backend peuvent être sur des clusters, des centres de données ou en dehors d’Azure tant qu’ils disposent d’une connectivité IP.

**Q. Dans quelles régions le service est-il disponible ?**

Application Gateway est disponible dans toutes les régions de la version globale d’Azure. Il est également disponible dans [Azure en Chine](https://www.azure.cn/) et [Azure Government](https://azure.microsoft.com/en-us/overview/clouds/government/).

**Q. S’agit-il d’un déploiement dédié à mon abonnement ou est-il partagé entre les clients ?**

Application Gateway est un déploiement dédié dans votre réseau virtuel.

**Q. La redirection HTTP->HTTPS est-elle prise en charge ?**

La redirection est prise en charge. Consultez la rubrique [Vue d’ensemble de la redirection Application Gateway](application-gateway-redirect-overview.md) pour en savoir plus.

**Q. Dans quel ordre les écouteurs sont-ils traités ?**

Les écouteurs sont traités selon leur ordre d’affichage. Pour cette raison, si un écouteur de base correspond à une demande entrante, il la traite en premier.  Les écouteurs multisites doivent être configurés avant un écouteur élémentaire pour garantir l’acheminement du trafic vers le serveur back-end correct.

**Q. Où puis-je trouver le DNS et l’adresse IP d’Application Gateway ?**

Lorsque vous utilisez une adresse IP publique en tant que point de terminaison, ces informations sont disponibles dans la ressource d’adresse IP publique ou sur la page de présentation d’Application Gateway dans le portail. Si vous utilisez des adresses IP internes, les données se trouvent sur la page de présentation.

**Q. Est-ce que l’adresse IP ou le DNS changent au cours de la vie d’Application Gateway ?**

L’adresse IP virtuelle peut changer si la passerelle est arrêtée et démarrée par le client. Le DNS associé à Application Gateway ne change pas au cours du cycle de vie de la passerelle. Pour cette raison, il est recommandé d’utiliser un alias CNAME et de le pointer vers l’adresse DNS d’Application Gateway.

**Q. Application Gateway prend-il en charge les adresses IP statiques ?**

Non, Application Gateway ne prend pas en charge les adresses IP publiques statiques. Néanmoins, les adresses IP internes statiques sont prises en charge.

**Q. Application Gateway prend-il en charge plusieurs adresses IP publiques sur la plateforme ?**

Une seule adresse IP publique est prise en charge sur Application Gateway.

**Q. Application Gateway prend-il en charge les en-têtes x-forwarded-for ?**

Oui, Application Gateway insère les en-têtes x-forwarded-for, x-forwarded-proto et x-forwarded-port dans la demande transmise au backend. Le format d’en-tête x-forwarded-for est une liste séparée par des virgules d’éléments IP:Port. Les valeurs valides pour x-forwarded-proto sont http ou https. X-forwarded-port spécifie le port atteint par la demande au niveau d’Application Gateway.

**Q. Combien de temps faut-il pour déployer une Application Gateway ? Mon Application Gateway continue-t-elle de fonctionner après une mise à jour  ?**

L’approvisionnement de nouveaux déploiements d’Application Gateway peut prendre jusqu’à 20 minutes. Les modifications apportées à la taille et au nombre des instances n’entraînent pas d’interruption, et la passerelle reste active pendant ce temps.

## <a name="configuration"></a>Configuration

**Q. Application Gateway est-il toujours déployé dans un réseau virtuel ?**

Oui, Application Gateway est toujours déployé dans un sous-réseau de réseau virtuel. Ce sous-réseau ne peut contenir que des Application Gateway.

**Q. Application Gateway peut-il communiquer avec des instances en dehors de son réseau virtuel ?**

Application Gateway peut communiquer avec des instances en dehors du réseau virtuel où il se trouve tant qu’une connectivité IP existe. Si vous prévoyez d’utiliser des adresses IP internes en tant que membres du pool backend, il vous faudra utiliser [VNET Peering](../virtual-network/virtual-network-peering-overview.md) ou [une passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

**Q. Puis-je déployer autre chose dans le sous-réseau d’Application Gateway ?**

Non, mais vous pouvez déployer d’autres passerelles d’application dans le sous-réseau.

**Q. Les groupes de sécurité réseau sont-ils pris en charge sur le sous-réseau d’Application Gateway ?**

Les groupes de sécurité réseau sont pris en charge sur le sous-réseau d’Application Gateway avec les restrictions suivantes :

* Des exceptions doivent être imposées au trafic entrant sur les ports 65503-65 534 pour que l’intégrité backend opère correctement.

* La connectivité Internet sortante ne peut pas être bloquée.

* Le trafic en provenance de la balise AzureLoadBalancer doit être autorisé.

**Q. Quelles sont les limites d’Application Gateway ? Puis-je augmenter ces limites ?**

Consultez [Limites d’Application Gateway](../azure-subscription-service-limits.md#application-gateway-limits) pour afficher les limites.

**Q. Puis-je utiliser Application Gateway pour le trafic externe et interne simultanément ?**

Oui, Application Gateway peut avoir une adresse IP interne et une adresse IP externe par Application Gateway.

**Q. VNET Peering est-il pris en charge ?**

Oui, VNET Peering est pris en charge et est utile pour équilibrer la charge du trafic des autres réseaux virtuels.

**Q. Puis-je communiquer avec les serveurs locaux lorsqu’ils sont connectés via ExpressRoute ou des tunnels VPN ?**

Oui, tant que le trafic est autorisé.

**Q. Puis-je avoir un pool backend servant plusieurs applications sur des ports différents ?**

L’architecture orientée microservices est prise en charge. Plusieurs paramètres HTTP doivent être configurés pour une sonde sur différents ports.

**Q. Les sondes personnalisées prennent-elles en charge les caractères génériques/les expressions régulières sur les données de réponse ?**

Les sondes personnalisées ne prennent pas en charge les caractères génériques/les expressions régulières sur les données de réponse. 

**Q. Comment les règles sont-elles traitées ?**

Les règles sont traitées dans l’ordre où elles sont configurées. Il est recommandé de configurer les règles multisites avant les règles élémentaires pour réduire les risques que le trafic soit acheminé vers le serveur backend inapproprié si une règle élémentaire mettait en correspondance le trafic basé sur le port avant l’évaluation de la règle multisite.

**Q. À quoi correspond le champ Hôte pour les sondes personnalisées ?**

Le champ Hôte indique le nom auquel envoyer la sonde. S’applique uniquement lorsque plusieurs sites sont configurés sur Application Gateway, sinon utilisez '127.0.0.1'. Cette valeur est différente du nom d’hôte de machine virtuelle et se trouve au format suivant : \<protocole\>://\<hôte\>:\<port\>\<chemin d’accès\>.

**Q. Puis-je autoriser quelques adresses IP de sources à accéder à Application Gateway ?**

Ce scénario peut être réalisé en utilisant des groupes de sécurité réseau sur le sous-réseau d’Application Gateway. Les restrictions suivantes doivent être imposées au sous-réseau dans l’ordre de priorité indiqué :

* Autoriser le trafic entrant à partir de l’adresse IP ou de la plage d’adresses IP sources.

* Autoriser les demandes entrantes de toutes sources aux ports 65503-65 534 pour les [communications relatives à l’intégrité backend](application-gateway-diagnostics.md).

* Autoriser les sondes entrantes d’Azure Load Balancer (balise AzureLoadBalancer) et le trafic réseau virtuel entrant (balise VirtualNetwork) sur le [Groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

* Bloquer tout autre trafic entrant avec une règle Refuser tout.

* Autoriser le trafic sortant vers internet pour toutes les destinations.

## <a name="performance"></a>Performances

**Q. Comment Application Gateway prend-il en charge la haute disponibilité et l’évolutivité ?**

Application Gateway prend en charge les scénarios de haute disponibilité lorsque vous avez deux instances déployées ou plus. Azure distribue ces instances entre les domaines de mise à jour et d’erreur pour garantir que toutes les instances n’échouent pas en même temps. Application Gateway prend en charge l’évolutivité en ajoutant plusieurs instances de la même passerelle pour partager la charge.

**Q. Comment puis-je obtenir le scénario de récupération d’urgence dans les centres de données avec Application Gateway ?**

Les clients peuvent utiliser Traffic Manager pour répartir le trafic entre plusieurs Application Gateway dans différents centres de données.

**Q. La mise à l’échelle automatique est-elle prise en charge ?**

Non, mais Application Gateway a une mesure de débit qui peut être utilisée pour vous avertir lorsqu’un seuil est atteint. Les opérations d’ajout d’instances ou de modification de la taille effectuées manuellement ne redémarrent pas la passerelle et n’affectent pas le trafic existant.

**Q. Est-ce que les opérations de montée/descente en puissance effectuées manuellement interrompent le service ?**

Aucune interruption de service n’a lieu, les instances sont réparties entre les domaines de mise à niveau et les domaines d’erreur.

**Q. Puis-je passer d’une taille d’instance moyenne à une taille d’instance grande sans interruption de service ?**

Oui, Azure distribue les instances entre les domaines de mise à jour et d’erreur pour garantir que toutes les instances n’échouent pas en même temps. Application Gateway prend en charge la mise à l’échelle en ajoutant plusieurs instances de la même passerelle pour partager la charge.

## <a name="ssl-configuration"></a>Configuration SSL

**Q. Quels sont les certificats pris en charge sur Application Gateway ?**

Les certificats auto-signés, les certificats d’autorité de certification et les certificats génériques sont pris en charge. Les certificats de validation étendue ne sont pas pris en charge.

**Q. Quelles sont les suites de chiffrement actuellement prises en charge par Application Gateway ?**

Vous trouverez ci-dessous les suites de chiffrement actuellement prises en charge par Application Gateway. Visitez la page [Configurer les versions de stratégie SSL et les suites de chiffrement sur Application Gateway](application-gateway-configure-ssl-policy-powershell.md) pour apprendre à personnaliser les options SSL.

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

**Q. Application Gateway prend-il également en charge le nouveau chiffrement du trafic vers le serveur principal ?**

Oui, Application Gateway prend en charge le déchargement SSL et SSL de bout en bout, qui chiffre à nouveau le trafic vers le serveur principal.

**Q. Puis-je configurer la stratégie SSL pour contrôler les versions du protocole SSL ?**

Oui, vous pouvez configurer Application Gateway pour refuser TLS1.0, TLS1.1 et TLS1.2. SSL 2.0 et 3.0 sont déjà désactivés par défaut et ne sont pas configurables.

**Q. Puis-je configurer les suites de chiffrement et l’ordre de la stratégie ?**

Oui, la [configuration des suites de chiffrement](application-gateway-ssl-policy-overview.md) est prise en charge. Lorsque vous définissez une stratégie personnalisée, au moins une des suites de chiffrement suivantes doit être activée. Application Gateway utilise SHA256 pour la gestion des serveurs principaux.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

**Q. Quel est le nombre de certificats SSL pris en charge ?**

20 certificats SSL maximum sont pris en charge.

**Q. Quel est le nombre de certificats d’authentification pour le nouveau chiffrement du backend pris en charge ?**

10 certificats d’authentification maximum sont pris en charge, dont 5 par défaut.

**Q. Application Gateway s’intègre-t-il à Azure Key Vault en mode natif ?**

Non, il n’est pas intégré à Azure Key Vault.

## <a name="web-application-firewall-waf-configuration"></a>Configuration du pare-feu d’application web

**Q. La référence de pare-feu d’application web propose-t-elle toutes les fonctionnalités disponibles avec la référence Standard ?**

Oui, le pare-feu d’application web prend en charge toutes les fonctionnalités de la référence Standard.

**Q. Quelle est la version CRS qu’Application Gateway prend en charge ?**

Application Gateway prend en charge CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) et CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

**Q. Comment puis-je surveiller le pare-feu d’application web ?**

Le pare-feu d’application web est surveillé via la journalisation des diagnostics. Vous trouverez plus d’informations sur la journalisation des diagnostics dans l’article [Intégrité du serveur principal, journalisation des diagnostics et métriques pour la passerelle Application Gateway](application-gateway-diagnostics.md).

**Q. Est-ce que le mode de détection bloque le trafic ?**

Non, le mode de détection journalise uniquement le trafic, ce qui a déclenché une règle de pare-feu d’application web.

**Q. Comment puis-je personnaliser les règles de pare-feu d’application web ?**

Oui, les règles WAF sont personnalisables. Pour plus d’informations sur la façon de les personnaliser, rendez-vous sur [Customize WAF rule groups and rules](application-gateway-customize-waf-rules-portal.md) (Personnaliser les règles et groupes de règles WAF)

**Q. Quelles sont les règles actuellement disponibles ?**

Le pare-feu d’application web prend actuellement en charge CRS  [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) et [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), qui fournit la sécurité de ligne de base pour la plupart des 3.0 premières vulnérabilités identifiées par l’OWASP, présentées ici [OWASP top 10 Vulnerabilities](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013) (10 premières vulnérabilités identifiées par l’OWASP)

* Protection contre les injections de code SQL

* Protection de l’exécution de script de site à site

* Protection contre les attaques web courante comme l’injection de commande, les dissimulations de requêtes HTTP, la séparation de réponse HTTP et les attaques RFI (Remote File Inclusion)

* Protection contre les violations de protocole HTTP

* Protection contre les anomalies de protocole HTTP comme un agent-utilisateur hôte manquant et les en-têtes Accept

* Protection contre les robots, les crawlers et les scanneurs

 * Détection des erreurs de configuration d’application courantes (par exemple, Apache, IIS, etc.)

**Q. Le pare-feu d’application web prend-il également en charge la prévention DDoS ?**

Non, le pare-feu d’application web ne fournit pas de prévention DDoS.

## <a name="diagnostics-and-logging"></a>Diagnostics et journalisation

**Q. Quels sont les types de journaux disponibles avec Application Gateway ?**

Trois journaux sont disponibles pour Application Gateway. Pour plus d’informations sur ces journaux et d’autres fonctionnalités de diagnostic, consultez l’article [Intégrité backend, journaux des diagnostics et métriques pour la passerelle Application Gateway](application-gateway-diagnostics.md).

- **ApplicationGatewayAccessLog** : le journal d’accès contient toutes les demandes envoyées au serveur frontal d’Application Gateway. Les données incluent l’adresse IP de l’appelant, l’URL demandée, la latence de réponse, le code de retour, les octets d’entrée et de sortie. Le journal d’accès est collecté toutes les 300 secondes. Ce journal contient un enregistrement par instance Application Gateway.
- **ApplicationGatewayPerformanceLog** : le journal des performances capture des informations sur les performances par instance, notamment le nombre total de demandes traitées, le débit en octets, le nombre total de demandes présentées, le nombre de demandes ayant échoué, le nombre d’instances du serveur principal correctes et incorrectes.
- **ApplicationGatewayFirewallLog** : le journal des pare-feux contient les demandes consignées via le mode de détection ou de prévention d’une passerelle d’application configuré avec un pare-feu d’application web.

**Q. Comment savoir si les membres de mon pool backend sont intègres ?**

Vous pouvez utiliser l’applet de commande PowerShell `Get-AzureRmApplicationGatewayBackendHealth` ou vérifier l’intégrité via le portail en consultant l’article [Intégrité backend, journalisation des diagnostics et métriques pour la passerelle Application Gateway](application-gateway-diagnostics.md).

**Q. Quelle est la stratégie de rétention sur les journaux de diagnostic ?**

Les journaux de diagnostic circulent vers le compte de stockage des clients, et les clients peuvent définir la stratégie de rétention en fonction de leurs préférences. Les journaux de diagnostic peuvent également être envoyés à un Event Hub ou Log Analytics. Consultez l’article [Intégrité du serveur principal, journalisation des diagnostics et métriques pour la passerelle Application Gateway](application-gateway-diagnostics.md) pour plus de détails.

**Q. Comment puis-je obtenir des journaux d’audit pour Application Gateway ?**

Les journaux d’audit sont disponibles pour Application Gateway. Dans le portail, cliquez sur **Journal d’activité** dans le panneau de menu d’Application Gateway pour accéder au journal d’audit. 

**Q. Puis-je définir des alertes avec Application Gateway ?**

Oui, Application Gateway prend en charge les alertes ; les alertes sont configurées à partir des mesures.  Application Gateway possède actuellement une mesure de « débit », qui peut être configurée pour avertir l’utilisateur. Pour en savoir plus sur les alertes, consultez l’article [Réception de notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

**Q. L’intégrité du serveur principal renvoie un état inconnu, à quoi est dû cet état ?**

La raison la plus courante est le blocage de l’accès au serveur principal par un groupe de sécurité réseau ou un DNS personnalisé. Consultez l’article [Intégrité du serveur principal, journalisation des diagnostics et métriques pour la passerelle Application Gateway](application-gateway-diagnostics.md) pour en savoir plus.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Application Gateway, consultez [Vue d’ensemble d’Application Gateway](application-gateway-introduction.md).
