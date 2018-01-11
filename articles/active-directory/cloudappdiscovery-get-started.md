---
title: Configurer le service Cloud App Discovery dans Azure Active Directory | Microsoft Docs
description: "Recherchez et gérez des applications avec Cloud App Discovery pour fournir des informations actionnables concernant l’utilisation du cloud et l’informatique fantôme."
services: active-directory
keywords: "détection d'applications cloud, gestion d'applications"
documentationcenter: 
author: curtand
manager: mtillman
tags: ignite
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: curtand
ms.reviewer: nigu
ms.openlocfilehash: 4a0cb1b7793c846f98ae4e89b99b4bda984cd5e4
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="set-up-cloud-app-discovery-in-azure-ad"></a>Configurer Cloud App Discovery dans Azure AD

Cloud App Discovery dans Azure AD est désormais basé sur l’intégration des données disponibles de Microsoft Cloud App Security. Pour fournir des informations en continu sur l’utilisation du cloud et l’informatique fantôme, Cloud App Discovery compare vos journaux de trafic au catalogue Cloud App Security comprenant plus de 15 000 applications cloud. Cet article décrit le processus d’installation et fournit des liens vers des informations détaillées sur chacune des étapes. Il aborde également les informations de proxy et de pare-feu, ainsi que la prise en charge des fichiers journaux.

## <a name="prerequisites"></a>Composants requis

* Votre organisation doit disposer d’une licence Azure AD Premium P1 pour utiliser le produit. Pour plus d’informations, consultez [Tarification Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
* Pour configurer Cloud App Discovery, vous devez être administrateur général ou lecteur sécurité dans Azure Active Directory.

## <a name="setup-steps"></a>Étapes de configuration

1. [Configurez les rapports d’instantané](cloudappdiscovery-set-up-snapshots.md) pour vérifier le format des journaux, et vous assurer que ces derniers fournissent des informations utiles à Cloud App Discovery. Ces rapports fournissent une visibilité ad hoc sur les journaux de trafic que vous chargez manuellement à partir de vos pare-feu et serveurs proxy.

2. [Configurez des rapports continus](https://docs.microsoft.com/cloud-app-security/discovery-docker) afin d’analyser tous les journaux qui sont transférés à partir de votre réseau à l’aide du collecteur de journaux Cloud App Security. Vous pouvez les utiliser pour identifier les nouvelles applications et les tendances d’utilisation.

3. Si vos journaux ne sont pas actuellement pris en charge, [configurez un analyseur des journaux personnalisé](https://docs.microsoft.com/cloud-app-security/custom-log-parser) pour que Cloud App Discovery puisse les analyser.
  
## <a name="log-processing-flow"></a>Flux de traitement des journaux

Suivant le volume de données, la génération de rapports peut prendre entre quelques minutes et plusieurs heures. Voici ce qui est analysé :

* **Chargement** Les journaux de trafic web de votre réseau sont chargés sur le portail.
* **Extraction** Cloud App Security analyse et extrait les données de trafic à partir des journaux de trafic avec un analyseur dédié pour chaque source de données.
* **Analyse** Les données de trafic sont analysées par rapport au catalogue Cloud App Security pour identifier plus de 15 000 applications cloud. Les adresses IP et les utilisateurs actifs sont également identifiés dans le cadre de l’analyse.
* **Génération d’un rapport** Cloud App Security génère un rapport des données extraites des fichiers journaux et le met à la disposition de Cloud App Discovery.

> [!NOTE]
> * Les données des rapports continus sont analysées deux fois par jour.
> * Le collecteur de journaux compresse les données avant qu’elles ne soient chargées. Le trafic sortant sur le collecteur de journaux représente environ 10 % de la taille des journaux de trafic reçu.

## <a name="using-traffic-logs-for-cloud-app-discovery"></a>Utilisation des journaux de trafic pour Cloud App Discovery

Cloud App Discovery utilise les données de vos journaux de trafic. Plus le contenu du journal est détaillé, meilleure est la visibilité obtenue. Les données de trafic web analysées par Cloud App Discovery doivent présenter les attributs suivants :

* Date de la transaction
* Adresse IP source
* Utilisateur source **recommandé**
* Adresse IP de destination
* URL de destination **recommandée** (les URL fournissent plus de précision pour la détection d’applications cloud que les adresses IP)
* Quantité totale de données
* Quantité de données chargées ou téléchargées, pour obtenir des insights sur les modèles d’utilisation des applications cloud
* Action effectuée (autorisée/bloquée)

Cloud App Discovery ne peut pas afficher ou analyser les attributs qui ne sont pas inclus dans vos journaux. Par exemple, le format de journal standard pour **Cisco ASA Firewall** ne contient pas la **quantité d’octets chargés par transaction**, le **nom d’utilisateur**, ou **l’URL cible**, mais uniquement l’adresse IP de destination. Ainsi, vous pouvez avoir moins de visibilité des applications cloud à partir de cette source de données. Pour les pare-feu Cisco ASA, définissez le niveau d’informations sur 6.1.

Pour que vous puissiez générer un rapport Cloud App Discovery, vos journaux de trafic doivent remplir les conditions suivantes :

1.  La source de données est [un pare-feu ou un serveur proxy pris en charge](#supported-firewalls-and-proxies).
2.  Le format de journal correspond au format standard attendu. Ce point est vérifié au moment du chargement. Pour optimiser le format de journal, consultez [Créer des rapports d’instantané Cloud App Discovery](cloudappdiscovery-set-up-snapshots.md).
3.  Les événements datent au maximum de 90 jours.
4.  Le fichier journal est valide et inclut des informations sur le trafic sortant.

## <a name="supported-firewalls-and-proxy-servers"></a>Pare-feu et serveurs proxy pris en charge

* Barracuda - Pare-feu d’application web (W3C)
* Blue Coat Proxy SG - Journal d’accès (W3C)
* Check Point
* Cisco ASA FirePOWER
* Cisco ASA Firewall (pour les pare-feu Cisco ASA, définissez le niveau d’informations sur 6)
* Cisco IronPort WSA
* Cisco ScanSafe
* Cisco Meraki – journal des URL
* Clavister NGFW (Syslog)
* Dell Sonicwall
* Fortinet Fortigate
* Juniper SRX
* Juniper SSG
* McAfee Secure Web Gateway
* Microsoft Forefront Threat Management Gateway (W3C)
* Pare-feu de la série Palo Alto
* Sophos SG
* Sophos Cyberoam
* Squid (commun)
* Squid (natif)
* Websense - Solutions de sécurité web - Rapport détaillé d’investigation (CSV)
* Websense - Solutions de sécurité web - Journal d’activité Internet (CEF)
* Zscaler

> [!NOTE]
> Cloud App Discovery prend en charge les adresses IPv4 et IPv6.

Si votre journal n’est pas pris en charge, sélectionnez **Autre** comme **Source de données** et spécifiez l’appareil et le journal que vous essayez de charger. Votre journal est examiné par l’équipe d’analystes cloud Cloud App Security. Quand la prise en charge de votre type de journal est ajoutée, nous vous avertissons. Toutefois, si vous préférez, vous pouvez définir un analyseur personnalisé qui correspond à votre format de journal. Pour plus d’informations, consultez [Utiliser un analyseur de journaux personnalisé](https://docs.microsoft.com/cloud-app-security/custom-log-parser).

## <a name="data-attributes-according-to-vendor-documentation"></a>Attributs de données (selon la documentation du fournisseur)

| Source de données         | URL de l’application ciblée | Adresse IP de l’application ciblée | Nom d’utilisateur | Adresse IP d’origine | Total du trafic | Octets chargés |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **Oui**        | **Oui**       | **Oui**  | **Oui**   | Non            | Non             |
| Blue Coat                               | **Oui**        | Non            | **Oui**  | **Oui**   | **Oui**       | **Oui**        |
| Point de contrôle                              | Non             | **Oui**       | Non       | **Oui**   | Non            | Non             |
| Cisco ASA                               | Non             | **Oui**       | Non       | **Oui**   | **Oui**       | Non             |
| Cisco FWSM                              | Non             | **Oui**       | Non       | **Oui**   | **Oui**       | Non             |
| Cisco Ironport WSA                      | **Oui**        | **Oui**       | **Oui**  | **Oui**   | **Oui**       | **Oui**        |
| Cisco Meraki                            | **Oui**        | **Oui**       | Non       | **Oui**   | Non            | Non             |
| Clavister NGFW (Syslog)                 | **Oui**        | **Oui**       | **Oui**  | **Oui**   | **Oui**       | **Oui**        |
| Dell SonicWall                          | **Oui**        | **Oui**       | Non       | **Oui**   | **Oui**       | **Oui**        |
| Fortigate                               | Non             | **Oui**       | Non       | **Oui**   | **Oui**       | **Oui**        |
| Juniper SRX                             | Non             | **Oui**       | Non       | **Oui**   | **Oui**       | **Oui**        |
| Juniper SSG                             | Non             | **Oui**       | Non       | **Oui**   | **Oui**       | **Oui**        |
| McAfee SWG                              | **Oui**        | Non            | Non       | **Oui**   | **Oui**       | **Oui**        |
| MS TMG                                  | **Oui**        | Non            | **Oui**  | **Oui**   | **Oui**       | **Oui**        |
| Palo Alto Networks                      | **Oui**        | **Oui**       | **Oui**  | **Oui**   | **Oui**       | **Oui**        |
| Sophos                                  | **Oui**        | **Oui**       | **Oui**  | **Oui**   | **Oui**       | Non             |
| Squid (commun)                          | **Oui**        | Non            | **Oui**  | **Oui**   | Non            | **Oui**        |
| Squid (natif)                          | **Oui**        | Non            | **Oui**  | **Oui**   | Non            | **Oui**        |
| Websense - Rapport d’examen (CSV)   | **Oui**        | **Oui**       | **Oui**  | **Oui**   | **Oui**       | **Oui**        |
| Websense - Journal d’activité Internet (CEF)  | **Oui**        | **Oui**       | **Oui**  | **Oui**   | **Oui**       | **Oui**        |
| Zscaler                                 | **Oui**        | **Oui**       | **Oui**  | **Oui**   | **Oui**       | **Oui**        |


## <a name="next-steps"></a>Étapes suivantes
Les liens suivants permettent de poursuivre la configuration de Cloud App Discovery dans Azure AD.

* [Créer des rapports d’instantanés](cloudappdiscovery-set-up-snapshots.md)
* [Configurer le reporting continu](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Utiliser un analyseur de journal personnalisé](https://docs.microsoft.comcommit/cloud-app-security/custom-log-parser)
