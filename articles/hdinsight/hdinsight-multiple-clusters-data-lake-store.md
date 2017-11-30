---
title: Utiliser plusieurs clusters HDInsight avec un compte Azure Data Lake Store - Azure | Microsoft Docs
description: "Découvrez comment utiliser plusieurs clusters HDInsight avec un seul compte Data Lake Store"
keywords: "stockage hdinsight, hdfs, données structurées, données non structurées, data lake store"
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 1fe81286c395a529a14ba87edc26390a2bab3f90
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-store-account"></a>Utiliser plusieurs clusters HDInsight avec un compte Azure Data Lake Store

À partir de HDInsight version 3.5, vous pouvez créer des clusters HDInsight avec des comptes Azure Data Lake Store en tant que système de fichiers par défaut.
Data Lake Store prend en charge le stockage illimité qui le rend idéal non seulement pour héberger de grandes quantités de données, mais également pour héberger plusieurs clusters HDInsight qui partagent un même compte Data Lake Store. Pour obtenir des instructions sur la création d’un cluster HDInsight avec Data Lake Store en tant que stockage, consultez [Créer des clusters HDInsight avec Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Cet article fournit des recommandations à l’administrateur Data Lake Store pour configurer un compte Data Lake Store unique et partagé qui peut être utilisé sur plusieurs clusters HDInsight **actifs**. Ces recommandations s’appliquent à l’hébergement de plusieurs clusters Hadoop sécurisés ou non sécurisés sur un compte Data Lake Store partagé.


## <a name="data-lake-store-file-and-folder-level-acls"></a>ACL de niveau fichier et dossier Data Lake Store

Le reste de cet article suppose que vous avez une bonne connaissance des ACL de niveau fichier et dossier dans l’application Azure Data Lake Store, qui est décrite en détail dans [Contrôle d’accès dans Azure Data Lake Store](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-store-setup-for-multiple-hdinsight-clusters"></a>Configuration de Data Lake Store pour plusieurs clusters HDInsight
Prenons une hiérarchie de dossiers sur deux niveaux pour expliquer les recommandations quant à l’utilisation de plusieurs clusters HDInsight avec un compte Data Lake Store. Supposons que vous possédez un compte Data Lake Store avec la structure de dossiers **/clusters/finance**. Avec cette structure, tous les clusters requis par le service financier peuvent utiliser /clusters/finance comme emplacement de stockage. À l’avenir, si une autre organisation (le service marketing par ex.) souhaite créer des clusters HDInsight avec le même compte Data Lake Store, elle pourrait créer /clusters/marketing. Pour l’instant, nous allons simplement utiliser **/clusters/finance**.

Pour que cette structure de dossiers soit bien utilisée par les clusters HDInsight, l’administrateur Data Lake Store doit attribuer les autorisations appropriées, comme décrit dans le tableau. Les autorisations indiquées dans le tableau correspondent aux ACL d’accès et non aux ACL par défaut. 


|Dossier  |Autorisations  |utilisateur propriétaire  |groupe propriétaire  | Utilisateur nommé | Autorisations de l’utilisateur nommé | Groupe nommé | Autorisations du groupe nommé |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |admin |admin  |Principal du service |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x--x |admin |admin |Principal du service |--x  |FINGRP |r-x         |
|/clusters/finance | rwxr-x--t |admin |FINGRP  |Principal du service |rwx  |-  |-     |

Dans le tableau,

- **admin** est le créateur et l’administrateur du compte Data Lake Store.
- **Principal de service** est le principal de service Azure Active Directory (AAD) associé au compte.
- **FINGRP** est un groupe d’utilisateurs créé dans AAD qui contient les utilisateurs du service financier.

Pour obtenir des instructions sur la création d’une application AAD (qui crée également un principal de service), consultez [Créer une application AAD](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Pour obtenir des instructions sur la création d’un groupe d’utilisateurs dans AAD, consultez [Gestion des groupes dans Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

Quelques points importants à prendre en compte.

- La structure de dossiers sur deux niveaux (**/clusters/finance/**) doit être créée et configurée avec les autorisations appropriées par l’administrateur Data Lake Store **avant** d’utiliser le compte de stockage pour les clusters. Cette structure n’est pas créée automatiquement lors de la création des clusters.
- L’exemple ci-dessus recommande de définir le groupe propriétaire de **/clusters/finance** en tant que **FINGRP** et d’octroyer à FINGRP l’accès **r-x** à l’intégralité de la hiérarchie des dossiers à partir de la racine. Cela permet de s’assurer que les membres de FINGRP peuvent accéder à la structure de dossiers à partir de la racine.
- Dans le cas où plusieurs principaux de service AAD peuvent créer des clusters sous **/clusters/finance**, le sticky-bit (lorsque défini sur le dossier **finance**) permet de s’assurer que les dossiers créés par un principal de service ne peuvent pas être supprimés par les autres.
- Une fois que la structure de dossiers et les autorisations sont en place, le processus de création de clusters HDInsight crée un emplacement de stockage spécifique aux clusters sous **/clusters/finance/**. Par exemple, l’emplacement de stockage d’un cluster nommé fincluster01 pourrait être **/clusters/finance/fincluster01**. La propriété et les autorisations pour les dossiers créés par le cluster HDInsight sont indiquées dans le tableau présenté ici.

    |Dossier  |Autorisations  |utilisateur propriétaire  |groupe propriétaire  | Utilisateur nommé | Autorisations de l’utilisateur nommé | Groupe nommé | Autorisations du groupe nommé |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finance/fincluster01 | rwxr-x---  |Principal de service |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>Recommandations pour les données d’entrée et de sortie de tâche

Nous vous recommandons de stocker les données d’entrée et données de sortie d’une tâche en dehors de **/clusters**. Ainsi, même si le dossier spécifique aux clusters est supprimé pour récupérer de l’espace de stockage, les données d’entrée et de sortie de la tâche sont toujours disponibles pour une utilisation ultérieure. Dans ce cas, assurez-vous que la hiérarchie des dossiers servant à stocker les données d’entrée et de sortie de la tâche permet un niveau d’accès approprié pour le principal de service.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Limite sur les clusters partageant un même compte de stockage

La limite du nombre de clusters pouvant partager un même compte Data Lake Store dépend de la charge de travail exécutée sur ces clusters. En cas de nombre trop important de clusters ou de charges de travail très lourdes sur les clusters partageant un compte de stockage, l’entrée ou la sortie du compte de stockage risque d’être limitée.

## <a name="support-for-default-acls"></a>Prise en charge des ACL par défaut

Lorsque vous créez un principal de service avec un accès utilisateur nommé (comme indiqué dans le tableau ci-dessus), nous vous recommandons de ne **pas** ajouter l’utilisateur nommé avec une ACL par défaut. La configuration d’un accès utilisateur nommé utilisant des ACL par défaut se traduit par l’attribution de 770 autorisations pour l’utilisateur propriétaire, le groupe propriétaire et les autres. Bien que la valeur par défaut de 770 ne retire pas les autorisations de l’utilisateur propriétaire (7) ou du groupe propriétaire (7), elle retire les autorisations des autres (0). Cela entraîne un problème connu avec un cas d’usage particulier qui est décrit en détail dans la section [Problèmes connus et solutions de contournement](#known-issues-and-workarounds).

## <a name="known-issues-and-workarounds"></a>Problèmes connus et solutions de contournement

Cette section répertorie les problèmes connus en ce qui concerne l’utilisation de HDInsight avec Data Lake Store, ainsi que leurs solutions de contournement.

### <a name="publicly-visible-localized-yarn-resources"></a>Ressources YARN localisées visibles publiquement

Lorsqu’un nouveau compte Azure Data Lake Store est créé, le répertoire racine est automatiquement configuré avec les bits d’autorisation des ACL d’accès définis sur 770. L’utilisateur propriétaire du dossier racine est défini sur l’utilisateur qui a créé le compte (l’administrateur Data Lake Store) tandis que le groupe propriétaire est défini sur le groupe principal de l’utilisateur qui a créé le compte. Aucun accès n’est fourni pour les « autres ».

Ces paramètres sont connus pour affecter un cas d’usage HDInsight particulier capturé dans [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). L’envoi de tâches peut échouer avec un message d’erreur semblable à celui-ci :

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Comme indiqué dans le YARN JIRA associé précédemment, lors de la localisation des ressources publiques, le localisateur valide le fait que toutes les ressources demandées sont bien publiques en vérifiant leurs autorisations sur le système de fichiers distant. Les éléments LocalResource qui ne correspondent pas à cette condition sont rejetés pour la localisation. La vérification des autorisations inclut l’accès en lecture au fichier pour les « autres ». Ce scénario ne marche pas instantanément lors de l’hébergement des clusters HDInsight sur Azure Data Lake, car Azure Data Lake refuse tout accès aux « autres » au niveau du dossier racine.

#### <a name="workaround"></a>Solution de contournement
Définissez les autorisations d’exécution en lecture pour les **autres** dans la hiérarchie, par exemple, au niveau  **/** , **/clusters** et **/clusters/finance** comme indiqué dans le tableau ci-dessus.

## <a name="see-also"></a>Voir aussi

* [Créer un cluster HDInsight avec Data Lake Store comme stockage.](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)


