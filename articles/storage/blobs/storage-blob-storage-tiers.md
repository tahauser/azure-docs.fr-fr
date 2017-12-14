---
title: Stockage chaud, froid et archive Azure pour fichiers blobs | Microsoft Docs
description: Stockage chaud, froid et archive pour les comptes de stockage Blob Azure.
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/05/2017
ms.author: mihauss
ms.openlocfilehash: 501fc59efb8bacf58fea2825752d3a33c6ea5963
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="azure-blob-storage-hot-cool-and-archive-preview-storage-tiers"></a>Stockage Blob Azure : niveaux de stockage chaud, froid et archive (version préliminaire)

## <a name="overview"></a>Vue d'ensemble

Le stockage Azure offre trois niveaux de stockage d’objets blob afin que vous puissiez stocker vos données de manière plus économique en fonction de leur utilisation. Le **niveau de stockage chaud** Azure est optimisé pour le stockage des données souvent sollicitées. Le **niveau de stockage à froid** Azure est optimisé pour le stockage des données rarement sollicitées et stockées depuis au moins 30 jours. Le **niveau de stockage archive**  Azure (version préliminaire) est optimisé pour le stockage des données rarement sollicitées et stockées depuis au moins 180 jours, sous des conditions de latence flexibles (selon l’ordre des heures). Le niveau de stockage archive est uniquement disponible au niveau de l’objet blob et non au niveau du compte de stockage. Les données du niveau de stockage froid peuvent tolérer une disponibilité légèrement inférieure, mais nécessitent toujours une durabilité élevée, ainsi qu’un temps d’accès et des caractéristiques de débit similaires à ceux des données chaudes. Concernant les données froides, un contrat SLA de disponibilité légèrement inférieure et des coûts d’accès supérieurs comparés aux données chaudes sont des compromis acceptables pour des coûts de stockage plus faibles. Le stockage archive est hors connexion et offre les coûts de stockage les plus bas, mais également les coûts d’accès les plus élevés.

Aujourd’hui, les données stockées dans le cloud connaissent une croissance exponentielle. Pour gérer les coûts liés à vos besoins de stockage en pleine expansion, il est utile d’organiser vos données selon des attributs tels que la fréquence d’accès et la période de rétention prévue pour optimiser les coûts. Les données stockées dans le cloud peuvent être différentes en termes de mode de génération, de traitement et d’accès tout au long de leur durée de vie. Certaines données sont activement sollicitées et modifiées tout au long de leur durée de vie. Certaines sont fréquemment sollicitées au début de leur durée de vie, puis les accès se raréfient considérablement à mesure qu’elles deviennent plus anciennes. D’autres sont inactives dans le cloud dès le départ et sont peu, voire pas sollicitées une fois stockées.

Chacun des scénarios d’accès aux données peut bénéficier des avantages d’un niveau de stockage différent, gage d’optimisation pour un modèle d’accès particulier. Les niveaux de stockage chauds, froids et d’archive permettent au stockage d’objets blob Azure de répondre à ce besoin de niveaux de stockage différenciés aux modèles de tarification distincts.

## <a name="blob-storage-accounts"></a>Comptes de stockage d’objets blob

Les **comptes de stockage d’objets blob** sont des comptes de stockage spécialisés pour le stockage des données non structurées en tant qu’objets blob dans Azure Storage. Avec les comptes de stockage Blob, vous pouvez désormais choisir un niveau de stockage chaud ou froid au niveau du compte, ou bien un niveau de stockage chaud, froid ou archive au niveau du fichier blob, en fonction des modèles d’accès. Stockez les données utilisées rarement, peu fréquemment et fréquemment dans des niveaux de stockage respectivement archive, froid et chaud pour optimiser les coûts. Les comptes de stockage d’objets blob sont similaires à vos comptes de stockage à usage général existants et offrent les excellents niveaux de durabilité, disponibilité, évolutivité et performances dont vous bénéficiez aujourd’hui. Ils assurent notamment la cohérence d’API à 100 % pour les objets blob de blocs et d’ajout.

> [!NOTE]
> Les comptes de stockage d’objets blob prennent en charge uniquement les objets blob de blocs et d’ajout, mais pas les objets blob de pages.

Les comptes de stockage d’objets blob exposent l’attribut **niveau d’accès** au niveau du compte, spécifiant le niveau de compte de stockage par défaut comme **Chaud** ou **Froid**. Le niveau de compte de stockage par défaut est appliqué à tout objet blob n’ayant aucun niveau explicitement défini au niveau de l’objet blob. Si le modèle d’utilisation de vos données est modifié, vous pouvez également basculer entre ces niveaux de stockage à tout moment. Le **niveau de stockage archive** (version préliminaire) peut être appliqué uniquement au niveau de l’objet blob.

> [!NOTE]
> La modification du niveau de stockage peut entraîner des frais supplémentaires. Consultez la section [Tarification et facturation](#pricing-and-billing) pour plus de détails.

### <a name="hot-access-tier"></a>Niveau d’accès chaud

Le stockage chaud possède des coûts de stockage plus élevés que le stockage froid et archive, mais également les coûts d’accès les plus faibles. Voici quelques exemples de scénarios d’utilisation pour le niveau de stockage chaud :

* Données activement utilisées ou censées être fréquemment sollicitées (accès en lecture et écriture).
* Données conservées pour traitement et migration éventuelle vers le niveau de stockage froid.

### <a name="cool-access-tier"></a>Niveau d’accès froid

Le niveau de stockage froid possède des coûts de stockage plus faibles et des coûts d’accès plus élevés que le stockage chaud. Ce niveau est prévu pour les données qui resteront dans le niveau froid pendant au moins 30 jours. Voici quelques exemples de scénarios d’utilisation pour le niveau de stockage froid :

* Sauvegarde à court terme et récupération d’urgence de jeux de données.
* Ancien contenu multimédia qui n’est plus consulté fréquemment mais qui est censé être disponible immédiatement lors d’un accès.
* Jeux de données volumineux qui doivent être stockés de manière économique tout en permettant la collecte de plus de données à des fins de traitement ultérieur. (*Par exemple*, le stockage à long terme de données scientifiques, les données de télémétrie brute d’un site de production)

### <a name="archive-access-tier-preview"></a>Niveau d’accès archive (version préliminaire)

Le stockage d’archive dispose du plus faible coût de stockage et des coûts d’extraction de données les plus élevés par rapport aux stockages chaud et froid. Ce niveau est destiné aux données qui peuvent tolérer plusieurs heures de latence de récupération et restant dans le niveau archive pendant au moins 180 jours.

Lorsqu’un objet blob est dans un stockage archive, il est hors connexion et ne peut pas être lu (à l’exception des métadonnées, qui sont en ligne et disponibles), ni copié, remplacé ou modifié. Vous ne pouvez pas non plus prendre d’instantanés d’un fichier blob dans un stockage archive. Toutefois, vous pouvez utiliser des opérations existantes pour supprimer, répertorier, obtenir les propriétés/métadonnées d’un fichier blob, ou pour changer le niveau de votre fichier blob.

#### <a name="blob-rehydration"></a>Rafraîchissement des objets blob
Pour lire des données dans le stockage archive, vous devez d’abord attribuer un niveau chaud ou froid à l’objet blob. Ce processus est appelé « réalimentation » et peut durer jusqu’à 15 heures pour les objets blob d’une taille inférieure à 50 Go. Le temps supplémentaire requis pour des objets blob plus volumineux varie en fonction de leur limite de débit.

Pendant la réalimentation, vous pouvez consulter la propriété « état archive » de l’objet blob pour vous assurer que le niveau a changé. L’état affiche « réalimentation-vers-chaud » ou « réalimentation-vers-froid » selon le niveau choisi. Une fois le processus terminé, la propriété « état archive » de l’objet blob est supprimée, et la propriété « niveau d’accès » de l’objet blob indique le niveau chaud ou froid.  

Voici quelques exemples de scénarios d’utilisation pour le niveau de stockage archive :

* Sauvegarde à long terme, archivage et récupération d’urgence de jeux de données
* Données d’origine (brutes) qui doivent être conservées, même après leur traitement sous un format final exploitable (*Par exemple*, des fichiers multimédias bruts après transcodage dans d’autres formats)
* Données de conformité et d’archivage qui doivent être stockées à long terme et qui sont très rarement sollicitées (*Par exemple*, séquences vidéo de sécurité, anciens clichés de radiographie ou d’IRM pour des organismes de santé ou enregistrements audio et transcriptions d’appels de clients pour des services financiers)

### <a name="recommendations"></a>Recommandations

Pour plus d’informations sur les comptes de stockage, consultez [À propos des comptes de stockage Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

Pour les applications qui requièrent uniquement le stockage d’objets blob de blocs ou d’objets blob d’ajout, nous recommandons d’utiliser des comptes de stockage d’objets blob, pour tirer parti du modèle de tarification différencié du stockage hiérarchisé. Toutefois, nous comprenons que cela n’est pas possible dans certains cas, notamment lorsque l’utilisation de comptes de stockage à usage général représente la meilleure option, par exemple :

* Vous devez utiliser des tables, files d’attente ou fichiers et souhaitez que vos objets blob soient stockés dans le même compte de stockage. Il n’existe aucun avantage technique à les stocker dans le même compte, si ce n’est que les clés partagées seront les mêmes.

* Vous devez toujours utiliser le modèle de déploiement Classic. Les comptes de stockage d’objets blob sont uniquement disponibles via le modèle de déploiement Azure Resource Manager.

* Vous devez utiliser des objets blob de pages. Les comptes de stockage d’objets blob ne gèrent pas les objets blob de pages. En général, nous recommandons d’utiliser des objets blob de blocs, sauf si vous avez spécifiquement besoin d’objets blob de pages.

* Vous utilisez une version de l’ [API REST des services de stockage](https://msdn.microsoft.com/library/azure/dd894041.aspx) antérieure à celle du 14/02/2014 ou une bibliothèque cliente avec une version inférieure à 4.x, et vous ne pouvez pas mettre à niveau votre application.

> [!NOTE]
> Les comptes de stockage d’objets blob sont actuellement pris en charge dans toutes les régions Azure.


## <a name="blob-level-tiering-feature-preview"></a>Fonctionnalité de hiérarchisation au niveau de l’objet blob (version préliminaire)

La hiérarchisation au niveau de l’objet blob vous permet de modifier le niveau de vos données au niveau de l’objet, à l’aide d’une seule opération nommée [Définir le niveau de l’objet blob](/rest/api/storageservices/set-blob-tier). Vous pouvez facilement modifier le niveau d’accès (chaud, froid ou archive) d’un objet blob, comme si vous modifiez le mode d’utilisation, sans avoir à déplacer des données entre les comptes. Toutes les modifications de niveau sont immédiates, sauf dans le cas où un objet blob est réalimenté depuis le niveau archive. L’heure de la dernière modification du niveau de l’objet blob est exposée via l’attribut **Access Tier Change Time** dans les propriétés de l’objet blob. Si un objet blob est au niveau archive, il ne peut pas être remplacé, le téléchargement de l’objet blob dans un tel cas n’est donc pas autorisé. Vous pouvez remplacer un objet blob chaud et froid, et dans ce cas, le nouvel objet blob hérite du niveau de l’ancien objet blob remplacé.

Les objets blob des trois niveaux de stockage peuvent coexister au sein d’un même compte. Tout objet blob ne disposant pas d’un niveau explicitement attribué déduit le niveau à partir du paramètre de niveau d’accès du compte. Si le niveau d’accès est déduit à partir du compte, vous voyez l’attribut **Niveau d’accès déduit** configuré avec la valeur « True » et l’attribut d’objet blob **Niveau d’accès** correspond au niveau du compte. Dans le portail Azure, la propriété du niveau d’accès déduit est affichée avec le niveau d’accès des objets blob (par exemple, chaud [déduit] ou froid [déduit]).

> [!NOTE]
> Le stockage archive et la hiérarchisation au niveau de l’objet blob prennent en charge uniquement les objets blob de blocs. De plus, vous ne pouvez pas modifier le niveau d’un objet blob de blocs comportant des captures instantanées.

### <a name="blob-level-tiering-billing"></a>Facturation de la hiérarchisation au niveau de l’objet blob

Lorsqu’un objet blob est déplacé vers un niveau plus froid (Chaud -> Froid, Chaud -> Archive ou Froid -> Archive), l’opération est facturée comme une écriture dans le niveau de destination, et les taux du niveau de destination s’appliquent également aux opérations d’écriture (par 10 000) ainsi qu’à l’écriture des données (par Go). Si un objet blob est déplacé vers un niveau plus chaud (Archive-> Froid, Archive-> Chaud ou Froid -> Chaud), l’opération est facturée comme une lecture dans le niveau source, et les taux du niveau source s’appliquent également aux opérations de lecture (par 10 000) ainsi qu’à l’extraction des données (par Go).

Pour utiliser ces fonctionnalités en version préliminaire, suivez les instructions sur la page du blog [Azure Archive and Blob-Level Tiering](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering) (Archive Azure et hiérarchisation au niveau d’un objet blob).

Voici certaines restrictions qui s’appliquent dans la version préliminaire de la hiérarchisation au niveau d’un objet blob :

* Seuls les comptes de stockage blob créés dans l’est des États-Unis 2, l’est des États-Unis ou l’ouest des États-Unis après réussite de la prise en charge de l’inscription de la version préliminaire du stockage archive.

* Seuls les comptes de stockage blob créés dans les régions publiques après réussite de la prise en charge de l’inscription de la version préliminaire de la hiérarchisation au niveau d’un objet blob.

* La hiérarchisation et le stockage archive sont uniquement pris en charge dans le stockage [LRS] (../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#locally-redundant-storage). [GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#geo-redundant-storage) et [RA-GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#read-access-geo-redundant-storage) seront pris en charge à l’avenir.

* Vous ne pouvez pas modifier le niveau d’un objet blob avec des instantanés.

* Vous ne pouvez pas copier ou prendre un instantané d’un objet blob dans un compte archive.

## <a name="comparison-of-the-storage-tiers"></a>Comparaison des niveaux de stockage

Le tableau suivant présente une comparaison des niveaux de stockage chaud et froid. L’archive au niveau de l’objet blob est en version préliminaire; elle ne dispose donc d’aucun SLA.

| | **Niveau de stockage chaud** | **Niveau de stockage froid** | **Niveau de stockage archive**
| ---- | ----- | ----- | ----- |
| **Availability** | 99,9 % | 99 % | N/A |
| **Availability** <br> **(Lectures RA-GRS)**| 99,99 % | 99,9 % | N/A |
| **Frais d’utilisation** | Coûts de stockage supérieurs, coûts d’accès et de transaction inférieurs | Coûts de stockage inférieurs, coûts d’accès et de transaction supérieurs | Coûts de stockage les plus faibles, coûts d’accès et de transaction les plus élevés |
| **Taille minimale des objets** | N/A | N/A | N/A |
| **Durée de stockage minimale** | N/A | N/A | 180 jours
| **Latence** <br> **(Temps jusqu’au premier octet)** | millisecondes | millisecondes | < 15 h
| **Cibles de performance et d’évolutivité** | Identiques aux comptes de stockage à usage général | Identiques aux comptes de stockage à usage général | Identiques aux comptes de stockage à usage général |

> [!NOTE]
> Les comptes de stockage d’objets blob présentent les mêmes objectifs de performance et d’évolutivité que les comptes de stockage à usage général. Pour plus d’informations, consultez la page [Objectifs de performance et évolutivité d'Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .


## <a name="pricing-and-billing"></a>Tarification et facturation
Les comptes de stockage d’objets blob utilisent un modèle de tarification pour le stockage d’objets blob basé sur le niveau de chaque objet blob. Les considérations de facturation suivantes s’appliquent à l’utilisation des comptes de stockage d’objets blob :

* **Coûts de stockage**: les coûts de stockage de données varient selon la quantité de données stockées et le niveau de stockage. Le coût par gigaoctet diminue à mesure que le niveau refroidit.

* **Coûts d’accès aux données** : les frais d’accès aux données augmentent à mesure que le niveau refroidit. Pour les données des niveaux de stockage froid et archive, des frais d’accès aux données en lecture vous sont facturés par gigaoctet.

* **Coûts de transaction** : des frais par transaction pour tous les niveaux, augmentant à mesure que le niveau refroidit.

* **Coûts de transfert de données de géoréplication**: ces coûts s’appliquent uniquement aux comptes pour lesquels la géoréplication est configurée, y compris GRS et RA-GRS. Le transfert de données de géoréplication implique des frais par gigaoctet.

* **Coûts de transfert de données sortantes** : les transferts de données sortantes (données transférées hors d’une région Azure) sont facturés pour l’utilisation de la bande passante par gigaoctet. Cette facturation est cohérente avec les comptes de stockage à usage général.

* **Modification du niveau de stockage** : passer d’un niveau de stockage de compte froid à un niveau de stockage chaud implique des frais correspondant à la lecture de toutes les données existantes du compte de stockage. Toutefois, la modification du niveau de stockage de compte chaud vers un niveau de stockage froid induit des frais équivalents à l’écriture de toutes les données dans le niveau froid.

> [!NOTE]
> Pour plus d’informations sur le modèle de tarification des comptes de stockage d’objets blob, consultez la page [Tarification du stockage Azure](https://azure.microsoft.com/pricing/details/storage/). Pour plus d’informations sur les frais qu’implique le transfert de données sortantes, consultez la page [Détails de la tarification – Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quick-start"></a>Démarrage rapide

Dans cette section, les scénarios suivants sont décrits à l’aide du Portail Azure :

* Création d’un compte de stockage d’objets blob
* Gestion d’un compte de stockage d’objets blob

Vous ne pouvez pas définir le niveau d’accès archive dans les exemples suivants, car ce paramètre s’applique à l’ensemble du compte de stockage. Le niveau d’accès archive peut uniquement être défini sur un objet blob spécifique.

### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Créer un compte de stockage d’objets blob à l’aide du Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu Hub, sélectionnez **Nouveau** > **Données et stockage** > **Compte de stockage**.

3. Entrez un nom pour votre compte de stockage.

    Il doit s’agir d’un nom global unique ; il fait partie de l’URL permettant d’accéder aux objets du compte de stockage.  

4. Sélectionnez **Resource Manager** comme modèle de déploiement.

    Le stockage hiérarchisé est uniquement utilisable avec des comptes de stockage Resource Manager ; ce modèle de déploiement est recommandé pour les nouvelles ressources. Pour plus d’informations, voir [Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).  

5. Dans la liste déroulante Account Kind (Type de compte), sélectionnez **Stockage d’objets blob**.

    Cette liste vous permet de sélectionner le type de compte de stockage. Le stockage hiérarchisé n’est pas disponible dans le stockage à usage général ; il l’est uniquement dans le type de compte Stockage d’objets blob.     

    Lorsque vous sélectionnez cette option, le niveau de performances est défini sur Standard. Le stockage hiérarchisé n’est pas disponible avec le niveau de performances Premium.

6. Sélectionnez l’option de réplication pour le compte de stockage : **LRS**, **GRS** ou **RA-GRS**. La valeur par défaut est **RA-GRS**.

    LRS = stockage localement redondant ; GRS = stockage géo-redondant (deux régions) ; RA-GRS = stockage géo-redondant avec accès en lecture (2 régions avec accès en lecture à la seconde).

    Pour plus d’informations sur les options de réplication d’Azure Storage, voir [Réplication Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

7. Sélectionnez le niveau de stockage adapté à vos besoins : définissez le **Niveau d’accès** sur **Froid** ou **Chaud**. Le niveau par défaut est **Chaud**.

8. Sélectionnez l’abonnement dans lequel vous souhaitez créer le compte de stockage.

9. Spécifiez un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

10. Sélectionnez la région de votre compte de stockage.

11. Cliquez sur **Créer** pour créer le compte de stockage.

### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Modifier le niveau de stockage d’un compte de stockage d’objets blob à l’aide du Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Pour accéder à votre compte de stockage, sélectionnez Toutes les ressources, puis sélectionnez votre compte de stockage.

3. Dans le panneau Paramètres, cliquez sur **Configuration** pour afficher et/ou modifier la configuration du compte.

4. Sélectionnez le niveau de stockage adapté à vos besoins : définissez le **Niveau d’accès** sur **Froid** ou **Chaud**.

5. Cliquez sur Enregistrer dans la partie supérieure du panneau.

### <a name="change-the-storage-tier-of-a-blob-using-the-azure-portal"></a>Modifier le niveau de stockage d’un objet blob à l’aide du Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Pour accéder à votre objet blob dans votre compte de stockage, sélectionnez Toutes les ressources, puis votre compte de stockage et enfin sélectionnez votre objet blob.

3. Dans le panneau des propriétés de l’objet blob, cliquez sur le menu déroulant **Niveau d’accès** pour sélectionner niveau de stockage **Chaud**, **Froid**, ou **Archive**.

5. Cliquez sur Enregistrer dans la partie supérieure du panneau.

> [!NOTE]
> La modification du niveau de stockage peut entraîner des frais supplémentaires. Consultez la section [Tarification et facturation](#pricing-and-billing) pour plus de détails.


## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>Évaluation et migration vers des comptes de stockage d’objets blob
Cette section vise à aider les utilisateurs à effectuer une transition en douceur vers les comptes de stockage d’objets blob. Il existe deux scénarios utilisateur :

* Vous disposez d’un compte de stockage à usage général et envisagez de passer à un compte de stockage d’objets blob avec le niveau de stockage approprié.
* Vous souhaitez utiliser un compte de stockage d’objets blob ou vous disposez déjà d’un tel compte et souhaitez savoir si vous devez utiliser le niveau de stockage chaud ou froid.

Dans les deux cas, vous devez d’abord estimer les frais de stockage et d’accès aux données stockées dans un compte de stockage d’objets blob pour les comparer avec vos frais actuels.

## <a name="evaluating-blob-storage-account-tiers"></a>Évaluation des niveaux de compte de stockage d’objets blob

Pour estimer le coût de stockage et d’accès aux données stockées dans un compte de stockage d’objets blob, vous devez évaluer votre modèle d’utilisation existant ou faire une estimation du modèle d’utilisation souhaité. En général, vous souhaitez connaître :

* Votre consommation de stockage : quel est le volume de données stockées et quelle est son évolution mensuelle ?

* Votre modèle d’accès au stockage : quel est le volume de données du compte faisant l’objet d’accès en lecture et en écriture (y compris les nouvelles données) ? Le nombre et le type de transactions utilisées pour accéder aux données.

## <a name="monitoring-existing-storage-accounts"></a>Analyse des comptes de stockage existants

Pour analyser vos comptes de stockage existants et rassembler ces informations, vous pouvez utiliser Azure Storage Analytics qui assure la journalisation et fournit les données de mesure d’un compte de stockage. Storage Analytics peut stocker des métriques qui comprennent les statistiques de transactions agrégées et les données de capacité relatives aux demandes adressées au service de stockage d’objets blob aussi bien pour les comptes de stockage à usage général que pour les comptes de stockage d’objets blob. Ces données sont stockées dans des tables connues dans le même compte de stockage.

Pour plus d’informations, consultez [À propos des métriques de Storage Analytics](https://msdn.microsoft.com/library/azure/hh343258.aspx) et [Schéma de table de métriques Storage Analytics](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> Les comptes de stockage d’objets blob exposent le point de terminaison du service de table uniquement pour le stockage et l’accès aux métriques associées à ce compte.

Pour analyser la consommation de stockage pour le service de stockage d’objets blob, vous devez activer les métriques de capacité.
Lorsque cette option est activée, les données de capacité sont enregistrées quotidiennement pour le service blob d’un compte de stockage comme une entrée de table écrite dans la table *$MetricsCapacityBlob* dans le même compte de stockage.

Pour analyser le modèle d’accès aux données pour le service de stockage d’objets blob, vous devez activer les métriques de transaction par heure au niveau de l’API. Lorsque cette option est activée, les transactions par API sont agrégées toutes les heures et enregistrées comme une entrée de table écrite dans la table *$MetricsHourPrimaryTransactionsBlob* dans le même compte de stockage. La table *$MetricsHourSecondaryTransactionsBlob* enregistre les transactions vers le point de terminaison secondaire lorsqu’il s’agit de comptes de stockage RA-GRS.

> [!NOTE]
> Ce processus d’estimation n’est pas applicable si vous avez un compte de stockage à usage général dans lequel vous avez stocké des objets blob de pages et des disques de machines virtuelles en même temps que des données d’objets blob de blocs et d’ajout. Cela s’explique par le fait que vous n’avez aucun moyen de dissocier, en fonction du type d’objet blob, les métriques de capacité et les métriques de transaction associées aux objets blob de blocs et d’ajout qui peuvent être migrés vers un compte de stockage d’objets blob.

Pour avoir une bonne estimation de votre consommation de données et de votre modèle d’accès, nous vous recommandons de sélectionner pour les métriques une période de rétention représentative de votre utilisation régulière et d’extrapoler. Une option consiste à conserver les données de métriques pendant sept jours et à collecter les données chaque semaine pour les analyser à la fin du mois. Une autre option consiste à conserver les données de métriques pendant les 30 derniers jours et à collecter et analyser les données à la fin de la période de 30 jours.

Pour plus d’informations sur l’activation, la collecte et l’affichage des données de métriques, voir [Activation des métriques de stockage Azure et affichage des données associées](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Le stockage, l’accès et le téléchargement des données d’analyse sont également facturés comme des données utilisateur standard.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilisation des mesures d’utilisation pour estimer les coûts

### <a name="storage-costs"></a>Coûts de stockage

La dernière entrée de la table de métriques de capacité *$MetricsCapacityBlob* avec la clé de ligne *'data'* affiche la capacité de stockage utilisée par les données utilisateur. La dernière entrée de la table de métriques de capacité *$MetricsCapacityBlob* avec la clé de ligne *'analytics'* affiche la capacité de stockage utilisée par les journaux d’analyse.

Cette capacité totale utilisée par les données utilisateur et les journaux d’analyse (si l’option est activée) permet ensuite d’estimer le coût de stockage des données dans le compte de stockage. La même méthode peut également être utilisée pour estimer les coûts de stockage des objets blob de blocs et d’ajout dans les comptes de stockage à usage général.

### <a name="transaction-costs"></a>Coûts de transaction

La somme des entrées *'TotalBillableRequests'*d’une API dans la table de métriques de transaction indique le nombre total de transactions pour cette API. *Par exemple*, le nombre total de transactions *'GetBlob'* pendant une période donnée peut être calculé par la somme du total de demandes facturables pour toutes les entrées avec la clé de ligne *'user;GetBlob'*.

Pour estimer les frais de transaction pour les comptes de stockage d’objets blob, vous devez classer les transactions en trois groupes correspondant aux trois modèles de tarification.

* Les transactions d’écriture telles que *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* et *'CopyBlob'*.
* Les transactions de suppression telles que *'DeleteBlob'* et *'DeleteContainer'*.
* Toutes les autres transactions.

Pour estimer les frais de transaction pour les comptes de stockage à usage général, vous devez regrouper toutes les transactions, quelle que soit l’opération/l’API associée.

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Coûts d’accès aux données et de transfert de données de géoréplication

La quantité de données lues et écrites dans un compte de stockage n’est pas fournie par Storage Analytics mais peut être estimée en consultant la table de métriques de transaction. La somme des entrées *'TotalIngress'* d’une API dans la table de métriques de transaction indique la quantité totale de données entrantes en octets pour cette API. De même, la somme des entrées *'TotalEgress'* indique la quantité totale des données sortantes en octets.

Pour estimer les coûts d’accès aux données pour les comptes de stockage d’objets blob, vous devez classer les transactions en deux groupes.

* La quantité de données récupérées à partir du compte de stockage peut être estimée en additionnant les entrées *'TotalEgress'* pour les opérations *'GetBlob'* et *'CopyBlob'*.

* La quantité de données écrites dans le compte de stockage peut être estimée en additionnant les entrées *'TotalIngress'* pour les opérations *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* et *'AppendBlock'*.

Le coût de transfert de données de géoréplication des comptes de stockage d’objets blob peut également être calculé en estimant la quantité de données écrites lors de l’utilisation d’un compte de stockage GRS ou RA-GRS.

> [!NOTE]
> Pour un exemple plus détaillé de calcul des coûts d’un niveau de stockage chaud ou froid, consultez l’article *« Que sont les niveaux Froid et Chaud et comment savoir lequel utiliser ? »* sur la [page relative à la tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="migrating-existing-data"></a>Migration des données existantes

Un compte de stockage d’objets blob est un compte spécialisé pour stocker uniquement les objets blob de blocs et d’ajout. Les comptes de stockage à usage général existants, qui vous permettent également de stocker des tables, des files d’attente, des fichiers, des disques et des objets blob ne peuvent pas être convertis en comptes de stockage d’objets blob. Pour utiliser les niveaux de stockage, vous devez créer des comptes de stockage d’objets blob et migrer vos données existantes vers les comptes nouvellement créés.

Vous pouvez utiliser les méthodes suivantes pour migrer les données existantes vers les comptes de stockage d’objets blob à partir d’une solution de stockage local, d’un fournisseur de stockage cloud tiers ou de vos comptes de stockage à usage général existants dans Azure :

### <a name="azcopy"></a>AzCopy

AzCopy est un utilitaire de ligne de commande Windows conçu pour la copie de données hautes performances vers ou à partir d’Azure Storage. Vous pouvez utiliser AzCopy pour copier des données dans votre compte de stockage d’objets blob à partir de vos comptes de stockage à usage général existants, ou pour charger des données à partir de vos périphériques de stockage locaux vers votre compte de stockage d’objets blob.

Pour plus d’informations, voir [Transfert de données avec l’utilitaire de ligne de commande AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Bibliothèque de déplacement des données

La bibliothèque de déplacement de données Azure Storage pour .NET est basée sur l’infrastructure principale de déplacement de données sous-tendant AzCopy. La bibliothèque est conçue pour assurer des opérations de transfert de données fiables, simples et hautes performances, comme AzCopy. Cela vous permet de tirer pleinement parti des fonctionnalités offertes par AzCopy dans votre application de façon native, sans avoir à gérer l’exécution et la surveillance des instances externes d’AzCopy.

Pour plus d’informations, voir [Azure Storage Data Movement Library for .Net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API REST ou bibliothèque cliente

Vous pouvez créer une application personnalisée pour migrer vos données vers un compte de stockage d’objets blob à l’aide de l’une des bibliothèques clientes Azure ou de l’API REST des services Azure Storage. Azure Storage offre des bibliothèques clientes enrichies pour une diversité de langages et plateformes, par exemple .NET, Java, C++, Node.JS, PHP, Ruby et Python. Les bibliothèques clientes offrent des fonctionnalités avancées telles que la logique de nouvelle tentative, la journalisation et les téléchargements parallèles. Vous pouvez également développer votre application directement avec l’API REST, qui peut être appelée à l’aide de n’importe quel langage permettant de créer des requêtes HTTP/HTTPS.

Pour plus d’informations, voir [Prise en main du stockage d’objets blob Azure](storage-dotnet-how-to-use-blobs.md).

> [!NOTE]
> Les objets blob chiffrés à l’aide du chiffrement côté client stockent les métadonnées relatives au chiffrement stockées avec l’objet blob. Il est absolument essentiel que n’importe quel mécanisme de copie s’assure de la préservation des métadonnées de blob et en particulier des métadonnées relatives au chiffrement. Si vous copiez des objets blob sans ces métadonnées, le contenu de l’objet blob ne peut plus être récupéré. Pour plus d’informations concernant les métadonnées liées au chiffrement, voir [Chiffrement côté client et Azure Key Vault pour Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="faq"></a>Forum Aux Questions

1. **Les comptes de stockage existants restent-ils disponibles ?**

    Oui. Les comptes de stockage existants restent disponibles, avec les mêmes tarifs et fonctionnalités.  Ils ne permettent pas de choisir un niveau de stockage et n’offrent plus de possibilité de hiérarchisation.

2. **Quand et pourquoi dois-je commencer à utiliser des comptes de stockage d’objets blob ?**

    Les comptes de stockage d’objets blob sont des comptes spécialisés pour le stockage des objets blob et ils introduisent de nouvelles fonctionnalités axées sur les objets blob. Désormais, les comptes de stockage d’objets blob représentent la méthode recommandée pour le stockage des objets blob. En effet, certaines fonctionnalités, telles que la hiérarchisation du stockage, seront introduites pour ce type de compte. Toutefois, la migration s’effectue au moment où vous le souhaitez, selon vos besoins.

3. **Puis-je convertir mon compte de stockage existant en compte de stockage d’objets blob ?**

    Non. Le compte de stockage d’objets blob est un type de compte de stockage différent. Vous devez créer un compte et migrer vos données comme expliqué précédemment.

4. **Puis-je stocker des objets dans les deux niveaux de stockage d’un même compte ?**

    Oui. L’attribut *Niveau d’accès* configuré sur un niveau de compte est le niveau par défaut applicable à tous les objets du compte ne possédant par un niveau d’ensemble explicite. Toutefois, la hiérarchisation au niveau de l’objet blob (version préliminaire) permet de définir le niveau d’accès au niveau de l’objet, quel que soit le paramètre de niveau accès du compte. Des objets blob configurés sur l’un des trois niveaux de stockage (chaud, froid ou archive) peuvent exister dans le même compte.

5. **Puis-je modifier le niveau de stockage de mon compte de stockage d’objets blob ?**

    Oui, vous pouvez modifier le niveau de stockage en définissant l’attribut *Niveau d’accès* du compte de stockage. La modification du niveau de stockage s’applique à tous les objets stockés dans le compte et ne possédant pas un ensemble de niveau explicite. Passer d’un niveau de stockage chaud à un niveau de stockage froid implique des opérations d’écriture (par 10 000) et des frais d’écriture de données (par Go) (comptes de stockage blob uniquement), tandis que le passage d’un niveau froid à un niveau chaud entraîne des opérations de lecture (par 10 000) et des frais d’extraction de données (par Go) pour la lecture de toutes les données dans le compte.

6. **À quelle fréquence puis-je modifier le niveau de stockage de mon compte de stockage d’objets blob ?**

    Nous n’appliquons pas de limite concernant la fréquence de modification du niveau de stockage. Cependant, notez que le passage d’un niveau de stockage froid à un niveau chaud entraîne des frais importants. Il est déconseillé de modifier le niveau de stockage trop fréquemment.

7. **Les objets blob au niveau de stockage froid se comportent-ils différemment de ceux se trouvant au niveau de stockage chaud ?**

    Les objets blob au niveau de stockage chaud ont la même latence que les objets blob des comptes de stockage à usage général. Les objets blob au niveau de stockage froid ont une latence similaire (en millisecondes) à celle des objets blob des comptes de stockage à usage général. Les objets blob dans le niveau de stockage archive ont une latence de plusieurs heures.

    Les objets blob au niveau de stockage froid ont un contrat SLA de disponibilité légèrement inférieur à celui des objets blob stockés au niveau de stockage chaud. Pour plus d’informations, voir [SLA pour Storage](https://azure.microsoft.com/support/legal/sla/storage).

8. **Puis-je stocker des objets blob de pages et des disques de machine virtuelle dans les comptes de stockage d’objets blob ?**

    Les comptes de stockage d’objets blob prennent en charge uniquement les objets blob de blocs et d’ajout, mais pas les objets blob de pages. Les disques de machine virtuelle Azure sont soutenus par des objets blob de pages. Par conséquent, les comptes de stockage d’objets blob ne peuvent pas être utilisés pour stocker des disques de machine virtuelle. Toutefois, il est possible de stocker des sauvegardes de disques de machine virtuelle sous forme d’objets blob de blocs dans un compte de stockage d’objets blob.

9. **Dois-je modifier mes applications existantes pour utiliser des comptes de stockage d’objets blob ?**

    Les comptes de stockage d’objets blob sont cohérents à 100 % avec l’API avec les comptes de stockage à usage général pour les objets blob de blocs et d’ajout. Tant que votre application utilise des objets blob de blocs ou d’ajout, et que vous utilisez la version 2014-02-14 de [l’API REST Storage Services](https://msdn.microsoft.com/library/azure/dd894041.aspx) ou une version ultérieure, votre application doit fonctionner. Si vous utilisez une version antérieure du protocole, vous devez mettre à jour votre application pour utiliser la nouvelle version afin de travailler en toute transparence avec les deux types de comptes de stockage. En général, nous recommandons d’utiliser la dernière version, quel que soit le type de compte de stockage que vous utilisez.

10. **L’expérience utilisateur change-t-elle ?**

    Les comptes de stockage d’objets blob sont très similaires aux comptes de stockage à usage général d’objets blob de blocs et d’ajout et héritent de toutes les fonctionnalités clés de Azure Storage, notamment de niveaux élevés de durabilité, disponibilité, évolutivité, performances et sécurité. Hormis les fonctionnalités et restrictions spécifiques aux comptes de stockage d’objets blob et aux niveaux de stockage correspondants évoqués plus haut, il n’existe aucune différence.

## <a name="next-steps"></a>Étapes suivantes

### <a name="evaluate-blob-storage-accounts"></a>Évaluer des comptes de stockage d’objets blob

[Vérifier la disponibilité de comptes de stockage d’objets blob par région](https://azure.microsoft.com/regions/#services)

[Évaluer l’utilisation des comptes de stockage actuels en activant les métriques Azure Storage](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Vérifier le prix du stockage d’objets blob par région](https://azure.microsoft.com/pricing/details/storage/)

[Vérifier la tarification des transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>Commencer à utiliser des comptes de stockage d’objets blob

[Prise en main du stockage d’objets blob Azure](storage-dotnet-how-to-use-blobs.md)

[Transfert de données vers et à partir d’Azure Storage](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Transfert de données avec l'utilitaire de ligne de commande AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Parcourez et explorez vos comptes de stockage](http://storageexplorer.com/)
