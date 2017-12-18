---
title: "Sécurité des données Azure Security Center| Microsoft Docs"
description: "Ce document explique comment les données sont gérées et protégées dans le Centre de sécurité Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2017
ms.author: yurid
ms.openlocfilehash: c41e233e177f540562d80649c7bffc15e208e870
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="azure-security-center-data-security"></a>Sécurité des données du Centre de sécurité Azure
Pour aider les clients à prévenir, détecter et contrer les menaces, Azure Security Center collecte et traite des données de sécurité, notamment des informations de configuration, des métadonnées, des journaux d’événements et des fichiers de vidage sur incident. Microsoft adhère aux recommandations strictes de conformité et de sécurité, du codage jusqu'à l'exploitation d'un service.

Cet article explique comment les données sont gérées et protégées dans le Centre de sécurité Azure.

## <a name="data-sources"></a>Sources de données
Pour assurer une visibilité sur l’état de la sécurité, identifier les vulnérabilités, recommander des mesures d’atténuation et détecter les menaces actives, Azure Security Center analyse les données provenant des sources suivantes :

- Services Azure : utilise les informations relatives à la configuration des services Azure que vous avez déployés en communiquant avec le fournisseur de ressources de ce service.
- Trafic réseau : tire parti des métadonnées de trafic réseau échantillonnées provenant de l’infrastructure de Microsoft, telles que l’IP/le port source/de destination, la taille de paquet et le protocole réseau.
- Solutions de partenaires : collecte les alertes de sécurité des solutions de partenaires intégrées, telles que les solutions de pare-feu et anti-programme malveillant. 
- Vos machines virtuelles et vos serveurs : utilise les informations de configuration et les données relatives aux événements de sécurité, telles que les journaux des événements Windows et les journaux d’audit, les journaux IIS, les messages syslog et les fichiers de vidage sur incident, qui figurent sur vos machines virtuelles. En outre, lorsqu’une alerte est créée, Azure Security Center peut générer une capture instantanée du disque de machine virtuelle affecté et extraire des artefacts associés à l’alerte à partir du disque de machine virtuelle (fichier de registre, par exemple) à des fins d’investigation.


## <a name="data-protection"></a>Protection des données
**Ségrégation des données :**les données client sont maintenues séparées logiquement sur chaque composant, dans l’ensemble du service. Toutes les données sont balisées en fonction de l'organisation. Ce balisage est conservé tout au long du cycle de vie des données, et il est appliqué dans chaque couche du service.

**Accès aux données** : pour fournir des recommandations en matière de sécurité et enquêter sur les éventuelles menaces de sécurité, le personnel de Microsoft peut accéder aux informations collectées ou analysées par les services Azure, notamment les fichiers de vidage sur incident, les événements de création de processus, les captures instantanées et artefacts de disque de machine virtuelle, qui peuvent involontairement exposer des données client ou personnelles provenant de vos machines virtuelles. Nous respectons les [Conditions d’utilisation et la Déclaration de confidentialité de Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), qui indiquent que Microsoft n’utilisera pas les données client ou ne tirera pas d’informations de ces dernières à des fins commerciales, publicitaires ou similaires. Nous utilisons uniquement les données client en fonction des besoins pour vous proposer des services Azure, notamment des utilisations compatibles avec la fourniture de ces services. Vous conservez tous les droits sur les données client.

**Utilisation des données** : Microsoft utilise des modèles et des informations sur les menaces observées auprès de multiples locataires pour améliorer ses fonctionnalités de prévention et de détection. Cette utilisation s’effectue en accord avec les engagements de confidentialité décrits dans la [Déclaration de confidentialité](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx) de Microsoft.

## <a name="data-location"></a>Emplacement des données

**Vos espaces de travail** : un espace de travail est spécifié pour les zones géographiques ci-après, et les données collectées à partir de vos machines virtuelles Azure, notamment les vidages sur incident et certains types de données d’alerte, sont stockées dans l’espace de travail le plus proche. 

| Zone géographique de machine virtuelle                        | Zone géographique d’espace de travail |
|-------------------------------|---------------|
| États-Unis, Brésil, Canada | États-Unis |
| Europe, Royaume-Uni        | Europe        |
| Asie-Pacifique, Japon, Inde    | Asie-Pacifique  |
| Australie                     | Australie     |

 
Les captures instantanées des disques de machine virtuelle sont stockées dans le même compte de stockage que celui de ces disques.
 
Pour les machines virtuelles et les serveurs qui s’exécutent dans d’autres environnements, par exemple au niveau local, vous pouvez spécifier l’espace de travail et la région dans lesquels les données collectées sont stockées. 

**Stockage Azure Security Center** : les informations relatives aux alertes de sécurité, notamment les alertes des partenaires, sont stockées au niveau régional en fonction de l’emplacement de la ressource Azure associée, alors que les informations concernant l’état d’intégrité de la sécurité et les recommandations sont stockées de manière centralisée aux États-Unis ou en Europe, selon l’emplacement du client.
Le Centre de sécurité Azure collecte des copies éphémères de vos fichiers de vidage sur incident et les analyse pour obtenir des preuves de tentatives d’attaque par le biais de code malveillant exploitant une faille de sécurité et de compromis ayant abouti. Azure Security Center effectue cette analyse dans la même région géographique que l’espace de travail, puis supprime les copies éphémères une fois l’analyse terminée.

Les artefacts des ordinateurs sont stockés de manière centralisée dans la même région que la machine virtuelle. 


## <a name="managing-data-collection-from-virtual-machines"></a>Gestion de la collecte de données à partir de machines virtuelles

Lorsque vous activez Security Center dans Azure, la collecte de données est activée pour chacun de vos abonnements Azure. Vous pouvez également activer la collecte de données pour vos abonnements dans la section « Stratégie de sécurité » d’Azure Security Center. Lorsque la collecte de données est activée, Azure Security Center approvisionne Microsoft Monitoring Agent sur toutes les machines virtuelles Azure prises en charge existantes et sur toutes celles nouvellement créées. Microsoft Monitoring Agent analyse diverses configurations de sécurité et crée des événements sous la forme de traces de [Suivi d’événements pour Windows (ETW)](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx). En outre, le système d’exploitation déclenche des événements du Journal des événements au cours de l’exécution de la machine. Il peut s’agir des données suivantes : type et version de système d’exploitation, journaux de système d’exploitation (journaux d’événements Windows), processus en cours d’exécution, nom de machine, adresses IP, utilisateur connecté et ID de locataire. Microsoft Monitoring Agent lit les entrées du journal des événements et les traces ETW, puis les copie dans vos espaces de travail à des fins d’analyse. Microsoft Monitoring Agent copie également les fichiers de vidage sur incident dans votre espace de travail, active les événements de création de processus et l’audit de la ligne de commande.

Si vous utilisez Azure Security Center gratuitement, vous pouvez également désactiver la collecte de données à partir des machines virtuelles dans la stratégie de sécurité. La collecte de données est obligatoire pour les abonnements du niveau Standard. La collecte des artefacts et des captures instantanées des disques de machine virtuelle reste activée, même si la collecte de données est désactivée.

## <a name="data-consumption"></a>Consommation des données

Les clients peuvent consommer des données liées au Centre de sécurité à partir de différents flux de données, comme indiqué ci-dessous :

* **Azure Activity** : toutes les alertes de sécurité, les requêtes approuvées [juste à temps](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) du Centre de sécurité, et toutes les alertes générées par les [contrôles d’application adaptative](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application).
* **Log Analytics** : toutes les alertes de sécurité.


> [!NOTE]
> Les recommandations de sécurité peuvent également être consommées via l’API REST. Voir [Référence d’API REST de fournisseur de ressources de sécurité](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx) pour plus d’information. 

## <a name="see-also"></a>Voir aussi
Ce document explique comment les données sont gérées et protégées dans le Centre de sécurité Azure. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md) : découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [FAQ d’Azure Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.
