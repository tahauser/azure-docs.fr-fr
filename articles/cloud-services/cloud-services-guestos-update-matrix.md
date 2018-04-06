---
title: En savoir plus sur les dernières publications du SE invité de Microsoft Azure | Microsoft Docs
description: Dernières informations de publication et de compatibilité du Kit de développement logiciel (SDK) pour le SE invité de Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 3/19/2018
ms.author: raiye
ms.openlocfilehash: 0456bf8d8cdd84078d12c3fc0f48d16b0370cddd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Versions du SE invité et matrice de compatibilité du Kit de développement logiciel (SDK) Azure
Fournit des informations récentes sur les dernières publications du SE (système d’exploitation) invité de Microsoft Azure pour Cloud Services. Ces informations vous permettent de planifier votre mise à niveau avant la désactivation d’un SE invité. Si vous configurez vos rôles pour utiliser les mises à jour *automatiques* d’un SE invité comme cela est décrit dans [Paramètres de mise à jour du SE invité Azure][Azure Guest OS Update Settings], il n’est pas indispensable que vous lisiez cette page.

> [!IMPORTANT]
> Le contenu de cette page s’applique aux rôles web et de travail Cloud Services, qui s’exécutent sur un SE invité. Il **ne s’applique pas** aux machines virtuelles IaaS.
>
>


> [!TIP]
>  Abonnez-vous au [Flux RSS de mise à jour du SE invité] pour recevoir en temps voulu les notifications relatives à toutes les modifications du SE invité.
>
>

> [!IMPORTANT]
> À partir de la version de novembre, seules les 2 dernières versions du SE invité seront prises en charge et disponibles dans le portail Azure.
>
>

Vous ne savez pas exactement ce qu’est un SE invité ou en quoi consistent les publications du SE invité ? Lisez [cette](#how-it-works) section.

## <a name="news-updates"></a>Nouvelles mises à jour
###### <a name="march-19-2018"></a>**19 mars 2018**
Le SE invité de février a publié.

###### <a name="january-29-2018"></a>**29 janvier 2018**
Le système d’exploitation invité de janvier a été publié pour les familles de système d’exploitation 2 (WA-GUEST-OS-2.70_201801-01) & 3 (WA-GUEST-OS-3.57_201801-01)

###### <a name="january-4-2018"></a>**4 janvier 2018**
Le système d’exploitation invité de janvier a été publié pour les familles de système d’exploitation 4 (WA-GUEST-OS-4.50_201801-01) et 5 (WA-GUEST-OS-5.15_201801-01). Il contient des correctifs de sécurité importants.  

###### <a name="january-4-2018"></a>**4 janvier 2018**
Le système d’exploitation invité de décembre a été publié.

###### <a name="december-14-2017"></a>**14 décembre 2017**
Le système d’exploitation invité de novembre a été publié.

###### <a name="november-8-2017"></a>**8 novembre 2017**
Publication de l’OS invité octobre.

###### <a name="october-6-2017"></a>**6 octobre 2017**
Publication du SE invité septembre. Pour la version de septembre de Windows Server 2016, netfx3 est activé par défaut. Les clients doivent ajouter « dism /online /disable-feature /featurename:netfx3 » dans leur OnStart si leur flux de travail en a besoin pour exécuter une application .NET 2.x avec un runtime 4.x ou s’ils ont exécuté une application .NET 2.x, ont géré une erreur, puis ont exécuté une application .NET 4.x.

###### <a name="september-14-2017"></a>**14 septembre 2017**
Le déploiement du système d’exploitation invité du mois de septembre commence le 14 septembre, et sa publication est prévue le 9 octobre.

###### <a name="august-24-2017"></a>**24 août 2017**
Publication du SE invité août.

###### <a name="august-3-2017"></a>**3 août 2017**
Publication du SE invité juillet.

###### <a name="july-19-2017"></a>**19 juillet 2017**
Début du déploiement du SE invité de juillet le 19 juillet et publication projetée le 8 août.


## <a name="releases"></a>Publications
## <a name="family-5-releases"></a>Publications de famille 5
**Windows Server 2016**

.NET Framework est installé : 4.0, 4.5, 4.5.1, 4.5.2, 4.6, 4.6.1, 4.6.2

> [!NOTE]
> Les dates précédées de * sont susceptibles de changer.
>
> Le mot de passe RDP pour la famille 5 de SE doit comporter au moins 10 caractères.
>

| Chaîne de configuration | Date de lancement | Date de désactivation | Date d’expiration |
| --- | --- | --- | --- |
| WA-GUEST-OS-5.16_201802-01 |12 mars 2018 |Post 5.18 |TBD |
| WA-GUEST-OS-5.15_201801-01 |4 janvier 2018 |Publication 5.17 |TBD |
|~~WA-GUEST-OS-5.14_201712-01~~ |4 janvier 2018 |12 mars 2018 |TBD |
|~~WA-GUEST-OS-5.13_201711-01~~ |14 décembre 2017 |4 janvier 2018|TBD |
|~~WA-GUEST-OS-5.12_201710-02~~ |8 novembre 2017 |4 janvier 2018 |TBD |
|~~WA-GUEST-OS-5.11_201709-01~~ |6 octobre 2017 |14 décembre 2017 |TBD |
|~~WA-GUEST-OS-5.10_201708-01~~ |24 août 2017 |14 décembre 2017 |TBD |
|~~WA-GUEST-OS-5.9_201707-01~~ |3 août 2017 |8 novembre 2017 |TBD |
|~~WA-GUEST-OS-5.8_201706-01~~ |7 juillet 2017 |6 octobre 2017 |TBD |
|~~WA-GUEST-OS-5.7_201705-01~~ |5 juin 2017 |24 août 2017 |TBD |
|~~WA-GUEST-OS-5.6_201704-01~~ |9 mai 2017 |3 août 2017 |TBD |
|~~WA-GUEST-OS-5.5_201703-01~~ |10 avril 2017 |7 juillet 2017 |TBD |
|~~WA-GUEST-OS-5.4_201612-01~~ |10 janvier 2017 |5 juin 2017|TBD |

## <a name="family-4-releases"></a>Publications de famille 4
**Windows Server 2012 R2**

.NET Framework est installé : 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Les dates précédées de * sont susceptibles de changer.
>
>

| Chaîne de configuration | Date de lancement | Date de désactivation | Date d’expiration |
| --- | --- | --- | --- |
| WA-GUEST-OS-4.51_201802-01 |12 mars 2018 |Post 4.53 |TBD |
| WA-GUEST-OS-4.50_201801-01 |4 janvier 2018 |Publication 4.52 |TBD |
|~~WA-GUEST-OS-4.49_201712-01~~ |4 janvier 2018 |12 mars 2018 |TBD |
|~~WA-GUEST-OS-4.48_201711-01~~ |14 décembre 2017 |4 janvier 2018 |TBD |
|~~WA-GUEST-OS-4.47_201710-02~~ |8 novembre 2017 |4 janvier 2018 |TBD |
|~~WA-GUEST-OS-4.46_201709-01~~ |6 octobre 2017 |14 décembre 2017 |TBD |
|~~WA-GUEST-OS-4.45_201708-01~~ |24 août 2017 |14 décembre 2017 |TBD |
|~~WA-GUEST-OS-4.44_201707-01~~ |3 août 2017 |8 novembre 2017 |TBD |
|~~WA-GUEST-OS-4.43_201706-01~~ |7 juillet 2017 |6 octobre 2017 |TBD |
|~~WA-GUEST-OS-4.42_201705-01~~ |5 juin 2017 |24 août 2017 |TBD |
|~~WA-GUEST-OS-4.41_201704-01~~ |9 mai 2017 |3 août 2017 |TBD |
|~~WA-GUEST-OS-4.40_201703-01~~ |10 avril 2017 |7 juillet 2017 |TBD |
|~~WA-GUEST-OS-4.39_201612-01~~ |10 janvier 2017 |5 juin 2017 |TBD |

## <a name="family-3-releases"></a>Publications de famille 3
**Windows Server 2012**

.NET Framework est installé : 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Les dates précédées de * sont susceptibles de changer.
>
>

| Chaîne de configuration | Date de lancement | Date de désactivation | Date d’expiration |
| --- | --- | --- | --- |
| WA-GUEST-OS-3.58_201802-01 |19 mars 2018 |Post 3.60 |TBD |
| WA-GUEST-OS-3.57_201801-01 |29 janvier 2018 |Billet 3.59 |TBD |
|~~WA-GUEST-OS-3.56_201712-01~~ |4 janvier 2018 |19 mars 2018 |TBD |
|~~WA-GUEST-OS-3.55_201711-01~~ |14 décembre 2017 |29 janvier 2018 |TBD |
|~~WA-GUEST-OS-3.54_201710-02~~ |8 novembre 2017 |4 janvier 2018 |TBD |
|~~WA-GUEST-OS-3.53_201709-01~~ |6 octobre 2017 |14 décembre 2017 |TBD |
|~~WA-GUEST-OS-3.52_201708-01~~ |24 août 2017 |14 décembre 2017 |TBD |
|~~WA-GUEST-OS-3.51_201707-01~~ |3 août 2017 |8 novembre 2017 |TBD |
|~~WA-GUEST-OS-3.50_201706-01~~ |7 juillet 2017 |6 octobre 2017 |TBD |
|~~WA-GUEST-OS-3.49_201705-01~~ |5 juin 2017 |24 août 2017 |TBD |
|~~WA-GUEST-OS-3.48_201704-01~~ |9 mai 2017 |3 août 2017 |TBD |
|~~WA-GUEST-OS-3.47_201703-01~~ |10 avril 2017 |7 juillet 2017 |TBD |
|~~WA-GUEST-OS-3.46_201612-01~~ |10 janvier 2017 |5 juin 2017 |TBD |

## <a name="family-2-releases"></a>Publications de famille 2
**Windows Server 2008 R2 SP1**

.NET Framework est installé : 3.5, 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Les dates précédées de * sont susceptibles de changer.
>
>

| Chaîne de configuration | Date de lancement | Date de désactivation | Date d’expiration |
| --- | --- | --- | --- |
| WA-GUEST-OS-2.71_201802-01 |12 mars 2018 |Post 2.73 |TBD |
| WA-GUEST-OS-2.70_201801-01 |29 janvier 2018 |Billet 2.72 |TBD |
|~~WA-GUEST-OS-2.69_201712-01~~ |4 janvier 2018 |12 mars 2018 |TBD |
|~~WA-GUEST-OS-2.68_201711-01~~ |14 décembre 2017 |29 janvier 2018 |TBD |
|~~WA-GUEST-OS-2.67_201710-02~~ |8 novembre 2017 |4 janvier 2018 |TBD |
|~~WA-GUEST-OS-2.66_201709-01~~ |6 octobre 2017 |14 décembre 2017 |TBD |
|~~WA-GUEST-OS-2.65_201708-01~~ |24 août 2017 |14 décembre 2017 |TBD |
|~~WA-GUEST-OS-2.64_201707-01~~ |3 août 2017 |8 novembre 2017 |TBD |
|~~WA-GUEST-OS-2.63_201706-01~~ |7 juillet 2017 |6 octobre 2017 |TBD |
|~~WA-GUEST-OS-2.62_201705-01~~ |5 juin 2017 |24 août 2017 |TBD |
|~~WA-GUEST-OS-2.61_201704-01~~ |9 mai 2017 |3 août 2017 |TBD |
|~~WA-GUEST-OS-2.60_201703-01~~ |10 avril 2017 |7 juillet 2017 |TBD |
|~~WA-GUEST-OS-2.59_201701-01~~ |10 janvier 2017 |5 juin 2017 |TBD |
|~~WA-GUEST-OS-2.58_201612-01~~ |10 janvier 2017 |9 mai 2017|TBD |


## <a name="msrc-patch-updates"></a>Mises à jour correctives MSRC
La liste des correctifs inclus dans chaque publication mensuelle du SE invité est disponible [ici][patches].

## <a name="sdk-support"></a>Prise en charge des Kits de développement logiciel (SDK)
La [stratégie de déclassement pour le Kit de développement logiciel (SDK) Azure][retire policy sdk] indique que seules les versions postérieures à la version 2.2 sont prises en charge. Cependant, certaines familles de SE invités permettent l’utilisation de versions antérieures. Vous devez toujours utiliser le Kit de développement logiciel (SDK) pris en charge le plus récent.

| Famille de SE invité | Versions du Kit de développement logiciel (SDK) compatibles |
| --- | --- |
| 5. |Versions 2.9.5.1 et ultérieures |
| 4 |Versions 2.1 et ultérieures |
| 3 |Versions 1.8 et ultérieures |
| 2 |Versions 1.3 et ultérieures |
| 1 |Versions 1.0 et ultérieures |

## <a name="guest-os-release-information"></a>Informations de publication du SE invité
Trois dates sont importantes à propos des publications du SE invité : la date de **publication**, la date de **désactivation** et la date **d’expiration**. Un SE invité est considéré comme disponible quand il est proposé dans le portail et qu’il peut être sélectionné comme SE invité cible. Quand un SE invité atteint sa date de **désactivation**, il est supprimé d’Azure. Toutefois, tous les services cloud qui ciblent ce SE invité continuent de fonctionner normalement.

Durant la période entre la date de **désactivation** et la date **d’expiration**, vous pouvez facilement passer de votre SE invité actuel à une version plus récente. Si vous utilisez la mise à jour *automatique* pour votre SE invité, vous disposez toujours de la version la plus récente. Vous n’avez donc pas à vous soucier de la date d’expiration.

Après la date **d’expiration** du SE invité, tous les services cloud qui utilisent encore ce SE invité sont arrêtés, supprimés ou mis à niveau de manière forcée. Des informations supplémentaires sur la stratégie de déclassement sont fournies [ici][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Informations sur les versions des familles de SE invité
Les familles de SE invité sont basées sur les versions publiées de Microsoft Windows Server. Le SE invité est le système d’exploitation sous-jacent sur lequel s’exécute Azure Cloud Services. Chaque SE invité possède un numéro de famille, de version et de publication.

* **Guest OS family**  
  Version du système d’exploitation Windows Server sur laquelle est basé un SE invité. Par exemple, la *famille 3* est basée sur Windows Server 2012.
* **Version de SE invité**  
  Correspond à l’image de la famille de SE invité et aux correctifs [Microsoft Security Response Center (MSRC)][msrc] appropriés qui sont disponibles à la date de production de la nouvelle version du SE invité. Il est possible que les correctifs ne soient pas tous inclus.

    Les numéros commencent à 0 et augmentent de 1 chaque fois qu’un nouvel ensemble de mises à jour est ajouté. Les zéros à droite sont uniquement affichés s’ils sont importants. Autrement dit, la version 2.10 est une version différente, bien plus récente, que la version 2.1.
* **Publication de SE invité**  
  Nouvelle publication d’une version de SE invité. Une nouvelle publication est produite si Microsoft détecte des problèmes pendant les tests nécessitant des modifications. La publication la plus récente remplace toujours les publications précédentes, publiques ou non. Le portail Azure permet uniquement aux utilisateurs de choisir la publication la plus récente pour une version donnée. Les déploiements s’exécutant sur une publication antérieure ne sont généralement pas mis à niveau de force en fonction de la gravité du bogue.

Dans l’exemple ci-dessous, 2 est la famille, 12 est la version et « rel2 » est la publication.

**Publication du SE invité** - 2.12 rel2

**Chaîne de configuration pour cette publication** - WA-GUEST-OS-2.12_201208-02

Ces informations sont intégrées dans la chaîne de configuration d’un SE invité, ainsi qu’une date indiquant les correctifs MSRC envisagés pour cette publication. Dans cet exemple, les correctifs MSRC produits pour Windows Server 2008 R2 jusqu’à août 2012 inclus ont été envisagés pour l’inclusion. Seuls les correctifs s’appliquant spécifiquement à cette version de Windows Server sont inclus. Par exemple, si un correctif logiciel de MSRC s’applique à Microsoft Office, il ne sera pas inclus, car ce produit ne fait pas partie de l’image de base de Windows Server.

## <a name="guest-os-system-update-process"></a>Processus de mise à jour du SE invité
Cette page contient des informations sur les prochaines publications de SE invités. Les clients ont indiqué qu’ils souhaitent être informés lors d’une publication, car leurs rôles de service cloud redémarreront s’ils sont définis pour la mise à jour « Automatique ». Les publications de SE invités se produisent généralement au moins 5 jours après la publication de mise à jour MSRC survenant le deuxième mardi de chaque mois. Les nouvelles publications incluent tous les correctifs MSRC adéquats pour chaque famille de SE invité.

Microsoft Azure publie constamment des mises à jour. Le SE invité n’est qu’une mise à jour parmi d’autres. Une version peut être affectée par de nombreux facteurs trop nombreux pour être répertoriés ici. En outre, Azure s’exécute sur des centaines de milliers d’ordinateurs. Cela signifie qu’il est impossible de donner une date et une heure précises pour le redémarrage de vos rôles. Nous travaillons sur un plan visant à limiter ou minuter les redémarrages.

La propagation complète sur Azure d’une nouvelle publication de SE invité peut prendre du temps. Lorsque les services sont mis à jour pour le nouveau SE invité, ils sont redémarrés en respectant les domaines de mise à jour. Les services définis pour utiliser des mises à jour « Automatiques » obtiennent une publication en premier. Après la mise à jour, la nouvelle version du SE invité est répertoriée pour votre service dans le portail Azure. De nouvelles publications peuvent se produire pendant cette période. Il est possible que certaines versions soient déployées sur de longues périodes et donc que des redémarrages de mise à niveau automatiques ne se produisent pas pendant plusieurs semaines après la date officielle de la publication. Une fois qu’un SE invité est disponible, vous pouvez choisir explicitement cette version à partir du portail ou dans votre fichier de configuration.

Pour consulter un grand nombre d’informations utiles sur les redémarrages et des orientations vers d’autres informations techniques sur les mises à jour de SE invités et hôtes, consultez le blog de MSDN intitulé [Redémarrage d’instance de rôle en raison d’une mise à niveau du SE][restarts].

Si vous procédez à une mise à jour manuelle de votre SE invité, consultez la page [Stratégie de suppression du SE invité][retirepolicy] pour plus d’informations.

## <a name="guest-os-supportability-and-retirement-policy"></a>Prise en charge et stratégie de suppression du SE invité
La prise en charge et la stratégie de suppression du SE invité sont détaillées [ici][retirepolicy].

[Flux RSS de mise à jour du SE invité]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/en-us/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: http://www.microsoft.com/security/msrc/default.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
