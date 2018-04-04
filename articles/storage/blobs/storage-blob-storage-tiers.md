---
title: Stockage chaud, froid et archive Azure pour fichiers blobs | Microsoft Docs
description: Stockage chaud, froid et archive pour les comptes de stockage Azure.
services: storage
documentationcenter: ''
author: kuhussai
manager: jwillis
editor: ''
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/11/2017
ms.author: kuhussai
ms.openlocfilehash: c62f3a92e6199f6467556054c9f58c20b6ceba2c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-blob-storage-hot-cool-and-archive-storage-tiers"></a>Stockage Blob Azure : niveaux de stockage chaud, froid et archive

## <a name="overview"></a>Vue d'ensemble

Le stockage Azure offre trois niveaux de stockage d’objets blob afin que vous puissiez stocker vos données de manière plus économique en fonction de leur utilisation. Le **niveau de stockage chaud** Azure est optimisé pour le stockage des données souvent sollicitées. Le **niveau de stockage à froid** Azure est optimisé pour le stockage des données rarement sollicitées et stockées depuis au moins 30 jours. Le **niveau de stockage archive**  Azure est optimisé pour le stockage des données rarement sollicitées et stockées depuis au moins 180 jours, sous des conditions de latence flexibles (selon l’ordre des heures). Le niveau de stockage archive est uniquement disponible au niveau de l’objet blob et non au niveau du compte de stockage. Les données du niveau de stockage froid peuvent tolérer une disponibilité légèrement inférieure, mais nécessitent toujours une durabilité élevée, ainsi qu’un temps d’accès et des caractéristiques de débit similaires à ceux des données chaudes. Concernant les données froides, un contrat SLA de disponibilité légèrement inférieure et des coûts d’accès supérieurs comparés aux données chaudes sont des compromis acceptables pour des coûts de stockage plus faibles. Le stockage archive est hors connexion et offre les coûts de stockage les plus bas, mais également les coûts d’accès les plus élevés. Seuls les niveaux de stockage chaud et froid (pas archive) peuvent être définis au niveau du compte. Les trois niveaux peuvent être définis au niveau objet.

Aujourd’hui, les données stockées dans le cloud connaissent une croissance exponentielle. Pour gérer les coûts liés à vos besoins de stockage en pleine expansion, il est utile d’organiser vos données selon des attributs tels que la fréquence d’accès et la période de rétention prévue pour optimiser les coûts. Les données stockées dans le cloud peuvent être différentes en termes de mode de génération, de traitement et d’accès tout au long de leur durée de vie. Certaines données sont activement sollicitées et modifiées tout au long de leur durée de vie. Certaines sont fréquemment sollicitées au début de leur durée de vie, puis les accès se raréfient considérablement à mesure qu’elles deviennent plus anciennes. D’autres sont inactives dans le cloud dès le départ et sont peu, voire pas sollicitées une fois stockées.

Chacun des scénarios d’accès aux données peut bénéficier des avantages d’un niveau de stockage différent, gage d’optimisation pour un modèle d’accès particulier. Les niveaux de stockage chauds, froids et d’archive permettent au stockage d’objets blob Azure de répondre à ce besoin de niveaux de stockage différenciés aux modèles de tarification distincts.

## <a name="storage-accounts-that-support-tiering"></a>Comptes de stockage prenant en charge la hiérarchisation

Vous pouvez uniquement hiérarchiser vos données de stockage d’objet sur chaud, froid ou archive dans des comptes de stockage d’objets blob ou Usage général v2 (GPv2). Les comptes Usage général v1 (GPv1) ne prennent pas en charge la hiérarchisation. Cependant, les clients peuvent facilement convertir leurs comptes de stockage GPv1 ou d’objets blob en des comptes GPv2 en un simple clic depuis le portail Azure. GPv2 fournit une nouvelle structure de tarification pour les objets blobs, les fichiers et les files d’attente, ainsi que l’accès à de nouvelles fonctionnalités de stockage. De plus, de nouvelles fonctionnalités à venir et des réductions des prix ne seront offerts qu’aux comptes GPv2. Par conséquent, les clients doivent considérer le fait d’utiliser des comptes GPv2, mais seulement après avoir examiné la tarification de tous les services car certaines charges de travail peuvent revenir plus chères sur GPv2 que sur GPv1. Voir [Options de compte de stockage Azure](../common/storage-account-options.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pour en savoir plus.

Les comptes de stockage d’objets blob et GPv2 expose l’attribut **Niveau d’accès** au niveau du compte, vous permettant de spécifier un niveau de stockage par défaut chaud ou froid pour tous les objets blob présents dans le compte de stockage et ne disposant pas déjà d’un niveau établi au niveau de l’objet. Le niveau du compte ne s’applique pas aux objets disposant d’un niveau établi au niveau de l’objet. Le niveau de stockage archive peut être appliqué uniquement au niveau de l’objet. Vous pouvez passer d’un niveau de stockage à un autre à tout moment.

## <a name="hot-access-tier"></a>Niveau d’accès chaud

Le stockage chaud possède des coûts de stockage plus élevés que le stockage froid et archive, mais également les coûts d’accès les plus faibles. Voici quelques exemples de scénarios d’utilisation pour le niveau de stockage chaud :

* Données activement utilisées ou censées être fréquemment sollicitées (accès en lecture et écriture).
* Données conservées pour traitement et migration éventuelle vers le niveau de stockage froid.

## <a name="cool-access-tier"></a>Niveau d’accès froid

Le niveau de stockage froid possède des coûts de stockage plus faibles et des coûts d’accès plus élevés que le stockage chaud. Ce niveau est prévu pour les données qui resteront dans le niveau froid pendant au moins 30 jours. Voici quelques exemples de scénarios d’utilisation pour le niveau de stockage froid :

* Sauvegarde à court terme et récupération d’urgence de jeux de données.
* Ancien contenu multimédia qui n’est plus consulté fréquemment mais qui est censé être disponible immédiatement lors d’un accès.
* Jeux de données volumineux qui doivent être stockés de manière économique tout en permettant la collecte de plus de données à des fins de traitement ultérieur. (*Par exemple*, le stockage à long terme de données scientifiques, les données de télémétrie brute d’un site de production)

## <a name="archive-access-tier"></a>Niveau d’accès archive

Le stockage d’archive dispose du plus faible coût de stockage et des coûts d’extraction de données les plus élevés par rapport aux stockages chaud et froid. Ce niveau est destiné aux données qui peuvent tolérer plusieurs heures de latence de récupération et restant dans le niveau archive pendant au moins 180 jours.

Lorsqu’un objet blob est dans un stockage archive, il est hors connexion et ne peut pas être lu (à l’exception des métadonnées, qui sont en ligne et disponibles), ni copié, remplacé ou modifié. Vous ne pouvez pas non plus prendre d’instantanés d’un fichier blob dans un stockage archive. Toutefois, vous pouvez utiliser des opérations existantes pour supprimer, répertorier, obtenir les propriétés/métadonnées d’un fichier blob, ou pour changer le niveau de votre fichier blob.

Voici quelques exemples de scénarios d’utilisation pour le niveau de stockage archive :

* Sauvegarde à long terme, sauvegarde secondaire et jeux de données d’archivage
* Données d’origine (brutes) qui doivent être conservées, même après leur traitement sous un format final exploitable (*Par exemple*, des fichiers multimédias bruts après transcodage dans d’autres formats)
* Données de conformité et d’archivage qui doivent être stockées à long terme et qui sont très rarement sollicitées (*Par exemple*, séquences vidéo de sécurité, anciens clichés de radiographie ou d’IRM pour des organismes de santé ou enregistrements audio et transcriptions d’appels de clients pour des services financiers)

### <a name="blob-rehydration"></a>Rafraîchissement des objets blob
Pour lire des données dans le stockage archive, vous devez d’abord attribuer un niveau chaud ou froid à l’objet blob. Ce processus est appelé « réalimentation » et peut durer jusqu’à 15 heures. Les grandes tailles des objets blob sont fortement recommandées pour des performances optimales. La réalimentation simultanée de plusieurs petits objets blob peut demander du temps supplémentaire.

Pendant la réalimentation, vous pouvez consulter la propriété **État archive** de l’objet blob pour vous assurer que le niveau a changé. L’état affiche « réalimentation-vers-chaud » ou « réalimentation-vers-froid » selon le niveau choisi. Une fois le processus terminé, la propriété d’état archive est supprimée, et la propriété **Niveau d’accès** de l’objet blob indique le niveau chaud ou froid.  

## <a name="blob-level-tiering"></a>Hiérarchisation au niveau de l’objet blob

La hiérarchisation au niveau de l’objet blob vous permet de modifier le niveau de vos données au niveau de l’objet, à l’aide d’une seule opération nommée [Définir le niveau de l’objet blob](/rest/api/storageservices/set-blob-tier). Vous pouvez facilement modifier le niveau d’accès (chaud, froid ou archive) d’un objet blob, comme si vous modifiez le mode d’utilisation, sans avoir à déplacer des données entre les comptes. Toutes les modifications de niveau sont immédiates, sauf dans le cas où un objet blob est réalimenté depuis le niveau archive, ce qui peut prendre plusieurs heures. L’heure de la dernière modification du niveau de l’objet blob est exposée via la propriété de l’objet blob **Access Tier Change Time**. Si un objet blob est au niveau archive, il ne peut pas être remplacé, le téléchargement de l’objet blob dans un tel cas n’est donc pas autorisé. Vous pouvez remplacer un objet blob chaud et froid, et dans ce cas, le nouvel objet blob hérite du niveau de l’ancien objet blob remplacé.

Les objets blob des trois niveaux de stockage peuvent coexister au sein d’un même compte. Tout objet blob ne disposant pas d’un niveau explicitement attribué déduit le niveau à partir du paramètre de niveau d’accès du compte. Si le niveau d’accès est déduit à partir du compte, vous voyez la propriété de l’objet blob **Niveau d’accès déduit** configuré avec la valeur « True » et la propriété de l’objet blob **Niveau d’accès** correspond au niveau du compte. Dans le portail Azure, la propriété du niveau d’accès déduit est affichée avec le niveau d’accès des objets blob (par exemple, chaud [déduit] ou froid [déduit]).

> [!NOTE]
> Le stockage archive et la hiérarchisation au niveau de l’objet blob prennent en charge uniquement les objets blob de blocs. De plus, vous ne pouvez pas modifier le niveau d’un objet blob de blocs comportant des captures instantanées.

### <a name="blob-level-tiering-billing"></a>Facturation de la hiérarchisation au niveau de l’objet blob

Lorsqu’un objet blob est déplacé vers un niveau plus froid (chaud -> froid, chaud -> archive ou froid -> archive), l’opération est facturée comme une écriture dans le niveau de destination, où les taux du niveau de destination s’appliquent également aux opérations d’écriture (par 10 000) ainsi qu’à l’écriture des données (par Go). Si un objet blob est déplacé vers un niveau plus chaud (archive-> froid, archive-> chaud ou froid -> chaud), l’opération est facturée comme une lecture dans le niveau source, où les taux du niveau source s’appliquent également aux opérations de lecture (par 10 000) ainsi qu’à l’extraction des données (par Go).

Si vous passez le niveau du compte de chaud à froid, vous sera facturé pour les opérations d’écriture (par 10 000) pour tous les objets blobs ne disposant pas d’un niveau configuré dans les comptes GPv2 uniquement. Ceci n’est pas facturé dans les comptes de stockage d’objets blob. Vous serez facturé pour les opérations d’écriture (par 10,000) et l’extraction de données (par Go) si vous passez votre compte de stockage d’objets blob ou GPv2 de froid à chaud. Des frais de suppression anticipée peut également s’appliquer pour tout objet blob déplacé hors du niveau froid ou archive.

### <a name="cool-and-archive-early-deletion"></a>Suppression anticipée dans les niveaux Froid et Archive

En plus des frais par Go et par mois, chaque objet blob déplacé dans le niveau froid (comptes GPv2 uniquement) est sujet à une période de suppression anticipée froide de 30 jours, et chaque objet blob déplacé dans le niveau archive est sujet à une période de suppression anticipée d’archive de 180 jours. Ces charges sont calculées au prorata. Par exemple, si un objet blob est déplacé en archive puis supprimé ou déplacé vers le niveau chaud après 45 jours, il vous sera facturé des frais de suppression anticipé équivalent à 135 (180 moins 45) jours de stockage de cet objet blob en archive.

## <a name="comparison-of-the-storage-tiers"></a>Comparaison des niveaux de stockage

Le tableau suivant présente une comparaison des niveaux de stockage chaud, froid et archive.

| | **Niveau de stockage chaud** | **Niveau de stockage froid** | **Niveau de stockage archive**
| ---- | ----- | ----- | ----- |
| **Disponibilité** | 99,9 % | 99 % | N/A |
| **Disponibilité** <br> **(Lectures RA-GRS)**| 99,99 % | 99,9 % | N/A |
| **Frais d’utilisation** | Coûts de stockage supérieurs, coûts d’accès et de transaction inférieurs | Coûts de stockage inférieurs, coûts d’accès et de transaction supérieurs | Coûts de stockage les plus faibles, coûts d’accès et de transaction les plus élevés |
| **Taille minimale des objets** | N/A | N/A | N/A |
| **Durée de stockage minimale** | N/A | 30 jours (GPv2 uniquement) | 180 jours
| **Latence** <br> **(Temps jusqu’au premier octet)** | millisecondes | millisecondes | < 15 h
| **Cibles de performance et d’évolutivité** | Identiques aux comptes de stockage à usage général | Identiques aux comptes de stockage à usage général | Identiques aux comptes de stockage à usage général |

> [!NOTE]
> Les comptes de stockage d’objets blob présentent les mêmes objectifs de performance et d’évolutivité que les comptes de stockage à usage général. Pour plus d’informations, consultez la page [Objectifs de performance et évolutivité d'Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

## <a name="quickstart-scenarios"></a>Scénarios de démarrage rapide

Dans cette section, les scénarios suivants sont décrits à l’aide du Portail Azure :

* Comment changer le niveau d’accès de compte par défaut d’un compte de stockage GPv2 ou d’objets blob.
* Comment changer le niveau d’un objet blob dans un compte de stockage GPv2 ou d’objets blob.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Changer le niveau d’accès de compte par défaut d’un compte de stockage GPv2 ou d’objets blob

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Pour accéder à votre compte de stockage, sélectionnez Toutes les ressources, puis sélectionnez votre compte de stockage.

3. Dans le panneau Paramètres, cliquez sur **Configuration** pour afficher et/ou modifier la configuration du compte.

4. Sélectionnez le niveau de stockage adapté à vos besoins : définissez le **Niveau d’accès** sur **Froid** ou **Chaud**.

5. Cliquez sur Enregistrer dans la partie supérieure du panneau.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Changer le niveau d’un objet blob dans un compte de stockage GPv2 ou d’objets blob.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Pour accéder à votre objet blob dans votre compte de stockage, sélectionnez Toutes les ressources, puis votre compte de stockage et enfin sélectionnez votre objet blob.

3. Dans le panneau des propriétés de l’objet blob, cliquez sur le menu déroulant **Niveau d’accès** pour sélectionner niveau de stockage **Chaud**, **Froid**, ou **Archive**.

5. Cliquez sur Enregistrer dans la partie supérieure du panneau.

## <a name="faq"></a>Forum Aux Questions

**Dois-je utiliser un compte de stockage GPv2 ou d’objets blob si je souhaite niveler mes données ?**

Nous vous recommandons d’utiliser des comptes GPv2 pour la hiérarchisation à la place des comptes de stockage d’objets blob. GPv2 prend en charge toutes les fonctionnalités prises en charge par les comptes de stockage d’objets blob, plus beaucoup d’autres. La tarification entre le stockage d’objets blob et GPv2 est presque identique, mais certaines nouvelles fonctionnalités et réduction de tarifs sont uniquement disponibles sur les comptes GPv2. Les comptes GPv1 ne prennent pas en charge la hiérarchisation.

La structure de tarification entre des comptes GPv1 et GPv2 est différente et les clients doivent évaluer soigneusement le fait de passer à des comptes GPv2. Vous pouvez facilement convertir un compte de stockage d’objets blob ou GPv1 existant vers un compte GPv2 en un simple clic. Voir [Options de compte de stockage Azure](../common/storage-account-options.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pour en savoir plus.

**Est-ce que je peux stocker des objets dans les trois niveaux de stockage (chaud, froid et archive) au sein d’un même compte ?**

Oui. L’attribut **Niveau d’accès** configuré au niveau du compte est le niveau par défaut applicable à tous les objets du compte ne possédant par un niveau d’ensemble explicite. Toutefois, la hiérarchisation au niveau de l’objet blob permet de définir le niveau d’accès au niveau de l’objet, quel que soit le paramètre de niveau accès du compte. Des objets blob configurés sur l’un des trois niveaux de stockage (chaud, froid ou archive) peuvent exister dans le même compte.

**Puis-je modifier le niveau de stockage par défaut de mon compte de stockage GPv2 ou d’objets blob ?**

Oui, vous pouvez modifier le niveau de stockage par défaut en définissant l’attribut **Niveau d’accès** du compte de stockage. La modification du niveau de stockage s’applique à tous les objets stockés dans le compte et ne possédant pas un ensemble de niveau explicite. Passer d’un niveau de stockage chaud à un niveau de stockage froid implique des opérations d’écriture (par 10 000) pour tous les objets blob sans niveau établi dans les comptes GPv2 uniquement, tandis que le passage d’un niveau froid à un niveau chaud entraîne des opérations de lecture (par 10 000) et des frais d’extraction de données (par Go) pour tous les objets blob dans des comptes de stockage GPv2 et d’objets blob.

**Puis-je configurer le niveau d’accès par défaut de mon compte sur Archive ?**

Non. Seuls les niveaux de stockage chaud et froid peuvent être choisis comme le niveau d’accès par défaut du compte. Le niveau archive ne peut être choisi qu’au niveau d’un objet.

**Dans quelles régions les niveaux de stockage chaud, froid et archive sont-ils disponibles ?**

Les niveaux de stockage chaud et froid ainsi que la hiérarchisation au niveau de l’objet blob sont disponibles dans toutes les régions. Le stockage archive ne sera disponible au départ que dans certaines régions sélectionnées. Pour avoir une liste complète, voir [Régions Azure disponibles par région](https://azure.microsoft.com/regions/services/).

**Les objets blob au niveau de stockage froid se comportent-ils différemment de ceux se trouvant au niveau de stockage chaud ?**

Les objets blob au niveau de stockage chaud ont la même latence que les objets blob des comptes de stockage d’objets blob, GPv1 et GPv2. Les objets blob au niveau de stockage froid ont une latence similaire (en millisecondes) à celle des objets blob des comptes de stockage d’objets blob, GPv1 et GPv2. Les objets blob au niveau de stockage archive ont plusieurs heures de latence dans les comptes de stockage d’objets blob, GPv1 et GPv2.

Les objets blob au niveau de stockage froid ont un contrat SLA de disponibilité légèrement inférieur à celui des objets blob stockés au niveau de stockage chaud. Pour plus d’informations, voir [SLA pour Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Est-ce que les opérations dans les niveaux chaud, froid et archive sont les mêmes ?**

Oui. Toutes les opérations entre les niveaux chaud et froid sont 100% cohérents. Toutes les opérations d’archive valides, comprenant la suppression, la liste, l’obtention des propriétés/médadonnées d’objets blob, et le niveau de l’objet blob sont 100 % cohérents entre les niveaux chaud et froid. Un objet blob ne peut pas être lu ou modifié en étant dans le niveau archive.

**Lors de la réalimentation d’un objet blob depuis le niveau archive vers le niveau chaud ou froid, comment serais-je averti de la fin de la réalimentation ?**

Lors de la réalimentation, vous pouvez utiliser l’opération d’obtention des propriétés d’objet blob afin d’interroger l’attribut **État d’archive** pour confirmer la fin du changement de niveau. L’état affiche « réalimentation-vers-chaud » ou « réalimentation-vers-froid » selon le niveau choisi. Une fois le processus terminé, la propriété d’état archive est supprimée, et la propriété **Niveau d’accès** de l’objet blob indique le niveau chaud ou froid.  

**Après le réglage du niveau d’un objet blob, quand vais-je commencer à être facturé au taux approprié ?**

Chaque objet blob est toujours facturé en fonction du niveau indiqué par la propriété d’objet blob **Niveau d’accès**. Lors du réglage d’un nouveau niveau sur un objet blob, la propriété **Niveau d’accès** reflétera immédiatement le nouveau niveau pour toutes les transitions sauf pendant la réalimentation d’un objet blob depuis le niveau archive vers le niveau chaud ou froid, ce qui peut prendre plusieurs heures. Dans un tel cas, vous continuerez à être facturé aux taux d’archive jusqu’à la fin de la réalimentation, où **Niveau d’accès** reflétera le nouveau niveau. Vous serez facturé au nouveau tarif chaud ou froid seulement à partir de ce moment.

**Comment puis-je déterminer si je vais faire l’objet de frais de suppression anticipée lors de la suppression ou du déplacement d’un objet blob en dehors du niveau froid ou archive ?**

Tout objet blob supprimé ou déplacé en dehors du niveau froid (comptes GPv2 uniquement) ou archive respectivement avant 30 jours et 180 jours fera l’objet de frais de suppression anticipée calculés au prorata. Vous pouvez déterminer combien de temps un objet blob est resté au niveau froid ou archive en vérifiant la propriété d’objet blob **Access Tier Change Time** qui date le dernier changement de niveau. Voir la section [Suppression anticipée froid et archive](#cool-and-archive-early-deletion) pour plus de détails.

**Quels outils et kits de développement Azure prennent en charge la hiérarchisation au niveau de l’objet blob et le stockage archive ?**

Le portail Azure, PowerShell et les outils CLI et les bibliothèques de client .NET, Java, Python, et Node.js prennent toutes en charge la hiérarchisation au niveau de l’objet blob et le stockage archive.  

**Combien de données puis-je stocker dans les niveaux chaud, froid et archive ?**

Le stockage des données ainsi que d’autres limites sont établis à partir du niveau de compte et pas à partir du niveau de stockage. Par conséquent, vous pouvez choisir d’utiliser toute votre limite sur un seul niveau ou sur les trois niveaux. Pour plus d’informations, consultez la page [Objectifs de performance et évolutivité d'Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

## <a name="next-steps"></a>Étapes suivantes

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Évaluer les niveaux chaud, froid et archive dans les comptes de stockage d’objets blob GPv2

[Vérifier la disponibilité de niveau chaud, froid et archive par région](https://azure.microsoft.com/regions/#services)

[Évaluer l’utilisation des comptes de stockage actuels en activant les métriques Azure Storage](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Vérifier la tarification du niveau chaud, froid et archive dans les comptes de stockage d’objets blob et GPv2 par région](https://azure.microsoft.com/pricing/details/storage/)

[Vérifier la tarification des transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/)
