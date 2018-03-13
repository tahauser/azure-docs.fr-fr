---
title: "Composants et versions Hadoop - Azure HDInsight | Microsoft Docs"
description: "Découvrez les composants et versions Hadoop de HDInsight et les niveaux de service disponibles dans cette distribution cloud de la solution Hortonworks Data Platform."
keywords: "versions Hadoop,composants de l’écosystème hadoop,composants hadoop,comment vérifier la version hadoop"
services: hdinsight
editor: cgronlun
manager: asadk
author: bprakash
tags: azure-portal
documentationcenter: 
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: bprakash
ms.openlocfilehash: 35ded349e9ced6463ece876ca1fd92423d20c625
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>Quels sont les composants et versions Hadoop disponibles avec HDInsight ?

Découvrez les composants et versions de l’écosystème Apache Hadoop inclus dans Microsoft Azure HDInsight, ainsi que le package de sécurité d’entreprise. Découvrez également comment vérifier les versions des composants Hadoop dans HDInsight. 

Chaque version de HDInsight est une distribution cloud d’une version de la solution Hortonworks Data Platform (HDP).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Composants Hadoop disponibles avec différentes versions de HDInsight
Azure HDInsight prend en charge plusieurs versions de cluster Hadoop qui peuvent être déployées à tout moment. Le choix d’une version crée une version spécifique de la distribution de la plateforme HDP ainsi qu’un ensemble de composants, qui sont contenus dans cette distribution. Depuis le 17 février 2017, la version de cluster par défaut utilisée par Azure HDInsight est la version 3.5 ; elle est basée sur la plateforme HDP 2.5.

Les versions de composants associées aux versions de cluster HDInsight sont répertoriées dans le tableau suivant : 

> [!NOTE]
> La version par défaut associée au service HDInsight peut changer sans préavis. Si vous dépendez d’une version, spécifiez cette dernière lorsque vous créez des clusters à l’aide du Kit de développement logiciel (SDK) .NET avec Azure PowerShell et Azure CLI.

| Composant | HDInsight 3.6 (par défaut) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 | HDInsight 3.1 | HDInsight 3.0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop et YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive et HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez/Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.1.0 (Linux uniquement) |1.6.2 + 2.0 (Linux uniquement) |1.6.0 (Linux uniquement) |1.5.2 (Linux uniquement, version expérimentale) |1.3.1 (Windows uniquement) |-|-|
| Apache Kafka | 0.10.0 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.5.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |3.2.8 |-|-|-|

## <a name="check-for-current-hadoop-component-version-information"></a>Comment vérifier les informations de version du composant Hadoop actuel

Les versions de composant d’écosystème Hadoop associées aux versions de cluster HDInsight peuvent changer avec les mises à jour de HDInsight. Pour vérifier les composants Hadoop ainsi que les versions utilisées pour un cluster, utilisez l’API REST Ambari. La commande **GetComponentInformation** extrait des informations sur les composants du service. Pour plus d’informations, consultez la page [Documentation Ambari][ambari-docs].

Il existe un autre moyen d’obtenir la version des composants dans le cas des clusters Windows : vous pouvez vous connecter à un cluster à l’aide du Bureau à distance, puis examiner le contenu du répertoire C:\apps\dist\.

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, voir [Mise hors service de HDInsight sur Windows](#hdinsight-windows-retirement).

### <a name="release-notes"></a>Notes de publication

Pour d’autres notes de publication sur les dernières versions de HDInsight, consultez la page [Notes de publication de HDInsight](hdinsight-release-notes.md) .

## <a name="supported-hdinsight-versions"></a>Versions de HDInsight prises en charge
Les tableaux suivants listent les versions HDInsight. Les versions de la plateforme HDP qui correspondent à chaque version de HDInsight sont répertoriées, ainsi que les dates de mise en production des logiciels. Les dates d’expiration et de mise hors service sont également fournies, lorsqu’elles sont connues.

### <a name="available-versions"></a>Versions disponibles

Le tableau suivant liste les versions HDInsight qui sont disponibles dans le portail Azure, ainsi que d’autres méthodes de déploiement telles que PowerShell et le kit .NET SDK.

| Version de HDInsight | Version de la plateforme HDP | SYSTÈME D’EXPLOITATION DE LA MACHINE VIRTUELLE | Date de lancement | Date d’expiration du support | Date de mise hors service | Haute disponibilité |  Disponibilité sur le portail Azure | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16.0.4 LTS |4 avril 2017 | | |OUI |OUI |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30 septembre 2016 |5 septembre 2017 |31 mai 2018 |OUI |OUI |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 décembre 2015 |27 juin 2016 |31 juillet 2018 |OUI |Non  |

> [!NOTE]
> Quand une version cesse d’être prise en charge, elle n’est plus proposée dans le portail Microsoft Azure. Toutefois, les versions des clusters restent disponibles avec le paramètre `Version` de la commande Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) et le Kit de développement logiciel (SDK) .NET, jusqu’à leur date de mise hors service.
>

### <a name="retired-versions"></a>Versions supprimées

Le tableau suivant liste les versions HDInsight qui **ne sont pas** disponibles dans le portail Azure.

| Version de HDInsight | Version de la plateforme HDP | SYSTÈME D’EXPLOITATION DE LA MACHINE VIRTUELLE | Date de lancement | Date d’expiration du support | Date de mise hors service | Haute disponibilité |  Disponibilité sur le portail Azure | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 mars 2016 |29 décembre 2016 |9 janvier 2018 |OUI |Non  |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 décembre 2015 |27 juin 2016 |31 juillet 2017 |OUI |Non  |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS ou Windows Server 2012 R2 |18 février 2015 |1 mars 2016 |1 avril 2017 |OUI |Non  |
| HDInsight 3.1 |HDP 2,1 |Windows Server 2012 R2 |24 juin 2014 |18 mai 2015 |30 juin 2016 |OUI |Non  |
| HDInsight 3.0 |HDP 2,0 |Windows Server 2012 R2 |11 février 2014 |17 septembre 2014 |30 juin 2015 |OUI |Non  |
| HDInsight 2.1 |HDP 1,3 |Windows Server 2012 R2 |28 octobre 2013 |12 mai 2014 |31 mai 2015 |OUI |Non  |
| HDInsight 1.6 |HDP 1.1 | |28 octobre 2013 |26 avril 2014 |31 mai 2015 |Non  |Non  |

> [!NOTE]
> Les clusters à haute disponibilité avec deux nœuds principaux sont déployés par défaut pour les clusters HDInsight 2.1 et versions ultérieures. Ils ne sont pas disponibles pour les clusters HDInsight version 1.6.

## <a name="enterprise-security-package-for-hdinsight"></a>Package de sécurité d’entreprise pour HDInsight

Azure HDInsight est un package facultatif que vous pouvez ajouter à votre cluster HDInsight dans le cadre du flux de travail de création du cluster. Le package de sécurité d’entreprise prend en charge les aspects suivants :

- Intégration à Active Directory pour l’authentification.

    Avant, vous pouviez créer des clusters HDInsight uniquement avec un utilisateur administrateur local et un utilisateur SSH local. L’utilisateur administrateur local pouvait accéder à tous les fichiers, dossiers, tables et colonnes.  Avec le package de sécurité d’entreprise, vous pouvez activer le contrôle d’accès en fonction du rôle en intégrant les clusters HDInsight à votre propre annuaire Active Directory, comprenant l’annuaire Active Directory local, Azure Active Directory Domain Services ou Active Directory sur une machine virtuelle IaaS. L’administrateur de domaine sur le cluster peut autoriser les utilisateurs à utiliser leurs propres nom d’utilisateur et mot de passe d’entreprise (domaine) pour accéder au cluster. 

    Pour plus d'informations, consultez les pages suivantes :

    - [Introduction aux fonctions de sécurité Hadoop sur la base de clusters HDInsight joints à un domaine](./domain-joined/apache-domain-joined-introduction.md)
    - [Planifier des clusters Hadoop Azure joints à un domaine dans HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Configurer un environnement de bac à sable joint à un domaine](./domain-joined/apache-domain-joined-configure.md)
    - [Configurer les clusters HDInsight joints à un domaine avec Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorisation pour les données

    - Intégration à Apache Ranger pour l’autorisation sur les files d’attente Yarn Hive et Spark SQL.
    - Vous pouvez définir le contrôle d’accès sur les fichiers et dossiers.

    Pour plus d'informations, consultez les pages suivantes :

    - [Configurer les stratégies Hive dans HDInsight joint à un domaine](./domain-joined/apache-domain-joined-run-hive.md)

- Affichage des journaux d’audit pour surveiller les accès et les stratégies configurées. 

### <a name="supported-cluster-types"></a>Types de cluster pris en charge

Seuls les types de cluster suivants prennent en charge le package de sécurité d’entreprise :

- Hadoop (HDInsight 3.6 uniquement)
- Spark
- Interactive Query

### <a name="support-for-azure-data-lake-store"></a>Prise en charge d’Azure Data Lake Store

Le package de sécurité d’entreprise prend en charge l’utilisation d’Azure Data Lake Store comme stockage principal et stockage complémentaire.

### <a name="pricing-and-sla"></a>Tarifs et contrat SLA
Pour plus d’informations sur les tarifs et le contrat SLA du package de sécurité d’entreprise, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="hdinsight-windows-retirement"></a>Mise hors service de HDInsight sur Windows
La version 3.3 est la dernière version de Microsoft Azure HDInsight sur Windows. La date de mise hors service de HDInsight sur Windows est le 31 juillet 2018. Si vous disposez de clusters HDInsight sur Windows dont la version est 3.3 ou antérieure, vous devez migrer vers HDInsight sur Linux (version 3.5 ou ultérieure) avant le 31 juillet 2018. Grâce à la migration vers le système d’exploitation Linux, vous pouvez continuer de créer ou de redimensionner vos clusters HDInsight. La prise en charge de HDInsight version 3.3 sur Windows a expiré le 27 juin 2016.

Depuis HDInsight version 3.4, Microsoft a publié ce logiciel uniquement sur le système d’exploitation Linux. Par conséquent, certains composants de HDInsight sont disponibles pour Linux uniquement. Il s’agit notamment des applications Apache Ranger, Kafka, Interactive Query, Spark et HDInsight, et d’Azure Data Lake Store en tant que système de fichiers principal. Les versions ultérieures de HDInsight sont également disponibles sur Linux uniquement. Aucune version de HDInsight ne sera proposée sur Windows. 

## <a name="faqs"></a>FAQ

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>Quelle est la chronologie de mise hors service de HDInsight sur Windows ?
La date de mise hors service de HDInsight sous Windows est le 31 juillet 2018. Si la date de mise hors service est différente dans votre région, vous en êtes averti au cas par cas. 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>Quel est l’impact de la mise hors service de HDInsight sur Windows pour les clients existants ?
Une fois HDInsight mis hors service sur Windows, vous ne pourrez plus créer de clusters HDInsight sur Windows, ni redimensionner un cluster HDInsight existant sur ce système. La prise en charge de HDInsight version 3.3 a expiré le 27 juin 2016. Par conséquent, la résolution des bogues et le support ne sont plus assurés pour HDInsight 3.3 et versions antérieures. Les versions ultérieures de HDInsight sont également disponibles sur Linux uniquement. Aucune version de HDInsight ne sera proposée sur Windows.
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>Quelles sont les versions de HDInsight sur Windows qui seront affectées ?
La version 3.3 est la dernière version de HDInsight sur Windows. Avant la mise hors service de HDInsight sur Windows, tous les clusters HDInsight de version 3.3 ou antérieure installés sur Windows doivent être migrés vers HDInsight sur Linux de versions 3.5 et ultérieures. Grâce à la migration des clusters vers HDInsight sur Linux, vous pouvez continuer de créer des clusters ou de redimensionner les clusters existants. 

### <a name="what-do-i-need-to-do"></a>Que dois-je faire ?
Effectuez la migration des clusters HDInsight sur Windows vers HDInsight sur Linux avant le 31 juillet 2018. Pour en savoir plus, voir [Effectuer la migration d’un cluster HDInsight Windows vers un cluster Linux](hdinsight-migrate-from-windows-to-linux.md). Pour en savoir plus sur les versions d’Azure HDInsight, consultez la liste des [versions prises en charge](hdinsight-component-versioning.md#supported-hdinsight-versions). 

### <a name="where-do-i-find-the-cluster-os-type"></a>Où puis-je trouver le type de système d’exploitation du cluster ?
Dans le portail Azure, accédez à la page de vue d’ensemble du cluster HDInsight et recherchez l’option **Type de cluster** sous **Essentials**. Les types de système d’exploitation du cluster sont répertoriés dans cette page. 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>Je ne pourrai pas effectuer la migration vers un cluster HDInsight sur Linux avant le 31 juillet 2018. Que va-t-il arriver à mon cluster HDInsight sur Windows ?
Votre cluster pourra s’exécuter en l’état, mais vous ne pourrez pas le redimensionner, ni créer des clusters HDInsight sur Windows. 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>Mon cluster présente une dépendance à .NET. Comment puis-je la résoudre sur Linux ?
Vous pouvez résoudre la dépendance de votre cluster à Linux à l’aide du [projet Mono](http://www.mono-project.com/). Cette implémentation open source de .NET est disponible pour les clusters HDInsight sur Linux. Pour en savoir plus, voir [Effectuer la migration d’un cluster HDInsight Windows vers un cluster Linux](hdinsight-migrate-from-windows-to-linux.md). 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>Je n’utilise HDInsight sur Windows que depuis peu. Comment puis-je créer un cluster HDInsight sur Windows ?
Depuis le 3 juillet 2017, seuls les clients HDInsight sur Windows existants peuvent créer des clusters HDInsight sur Windows. Les nouveaux clients ne peuvent pas utiliser PowerShell ou le Kit de développement logiciel (SDK) pour créer un cluster HDInsight sur le portail Azure. Nous recommandons aux nouveaux clients de créer un cluster HDInsight sur Linux. Les clients existants peuvent créer des clusters HDInsight sur Windows jusqu’à la mise hors service de HDInsight sur Windows. 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>La migration de HDInsight sur Windows à Linux entraîne-t-elle des changements de tarification ?
Non, la tarification est identique pour HDInsight, que ce soit sur Windows ou sur Linux. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>Une fois la migration effectuée, en quoi l’utilisation de HDInsight sur Linux uniquement est-elle avantageuse pour les clients ?
* Un délai de mise sur le marché plus court pour les technologies Big Data open source, grâce au service HDInsight
* Communauté et écosystème de support importants
* Possibilité de procéder à un développement actif, par l’intermédiaire de la communauté Hadoop open source et d’autres technologies de Big Data

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>La version de HDInsight sur Linux propose-t-elle des fonctionnalités supplémentaires par rapport à la version de ce logiciel sous Windows ?
Depuis HDInsight version 3.4, Microsoft a publié ce logiciel uniquement sur le système d’exploitation Linux. Par conséquent, certains composants de HDInsight sont disponibles pour Linux uniquement. Il s’agit notamment des applications Apache Ranger, Kafka, Interactive Query, Spark et HDInsight, et d’Azure Data Lake Store en tant que système de fichiers principal. 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Contrat de niveau de service pour les versions de cluster HDInsight
Le contrat de niveau de service (SLA) est défini en termes de _fenêtre de support_. L’expression « fenêtre de support » désigne la durée pendant laquelle une version de cluster HDInsight est prise en charge par le support technique et le service clientèle Microsoft. Si la version du logiciel est associée à une _date d’expiration du support_ dépassée, cela signifie que le cluster HDInsight n’est plus inclus dans la fenêtre de support. Pour en savoir plus sur les versions gérées, consultez la liste des [versions de cluster HDInsight prises en charge](hdinsight-migrate-from-windows-to-linux.md). La date d’expiration du support d’une version donnée de HDInsight (lorsqu’une version X+1 plus récente est disponible) est calculée comme suit, la date la plus tardive prévalant :  

* Formule 1 : ajoutez 180 jours à la date de lancement du cluster HDInsight version X.
* Formule 2 : ajoutez 90 jours à la date de lancement du cluster HDInsight version X+1 dans le portail Azure.

La _date de mise hors service_ est la date après laquelle la version du cluster ne peut plus être créée sur HDInsight. Depuis le 31 juillet 2017, vous ne pouvez pas redimensionner un cluster HDInsight après sa date de mise hors service. 

> [!NOTE]
> Les clusters HDInsight sous Windows (y compris les versions 2.1, 3.0, 3.1, 3.2 et 3.3) s’exécutent sur la 4e famille de systèmes d’exploitation invités d’Azure, qui utilise la version 64 bits de Windows Server 2012 R2. Cette famille prend en charge les versions de .NET Framework 4.0, 4.5, 4.5.1 et 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Notes de publication de Hortonworks associées aux versions de HDInsight

Cette section fournit des liens vers des notes de publication des distributions Hortonworks Data Platform et des composants Apache utilisés avec HDInsight.
* Le cluster HDInsight version 3.6 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* Le cluster HDInsight version 3.5 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). HDInsight 3.5 est la version _par défaut_ du cluster Hadoop créé dans le portail Azure.
* Le cluster HDInsight version 3.4 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* Le cluster HDInsight version 3.3 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * Les [notes de publication d’Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) sont disponibles sur le site web d’Apache.
  * Les [notes de publication d’Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) sont disponibles sur le site web d’Apache.
* Le cluster HDInsight version 3.2 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.2][hdp-2-2].

  * Des notes de publication sont proposées pour des composants Apache spécifiques : [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112) et [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Le cluster HDInsight version 3.1 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Les clusters HDInsight 3.1 créés avant le 7 novembre 2014 sont basés sur [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* Le cluster HDInsight version 3.0 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.0][hdp-2-0-8].
* Le cluster HDInsight version 2.1 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 1.3][hdp-1-3-0].
* Le cluster HDInsight version 1.6 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 1.1][hdp-1-1-0].






## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Tailles des machines virtuelles et configuration des nœuds par défaut pour les clusters
Les tableaux suivants répertorient les tailles de machine virtuelle par défaut des clusters HDInsight.

> [!IMPORTANT]
> Si vous avez besoin de plus de 32 nœuds worker dans un cluster, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
> 
> 

* Toutes les régions prises en charge à l’exception du sud du Brésil et de l’ouest du Japon :

  | Type de cluster | Hadoop | hbase | Interactive Query | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- | --- |
  | Head : taille de machine virtuelle par défaut |D3 v2 |D3 v2 | D13, D14 |A3 |D12 v2 |D12 v2 |
  | Head : tailles de machine virtuelle recommandées |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |A3, A4, A5 |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | Worker : taille de machine virtuelle par défaut |D3 v2 |D3 v2  | D13, D14 |D3 v2 |Windows : D12 v2 ; Linux : D4 v2 |Windows : D12 v2 ; Linux : D4 v2 |
  | Worker : tailles de machine virtuelle recommandées |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |D3 v2, D4 v2, D12 v2 |Windows : D12 v2, D13 v2, D14 v2; Linux : D4 v2, D12 v2, D13 v2, D14 v2 |Windows : D12 v2, D13 v2, D14 v2; Linux : D4 v2, D12 v2, D13 v2, D14 v2 |
  | Zookeeper : taille de machine virtuelle par défaut | |A3 | |A2 | | |
  | Zookeeper : tailles de machine virtuelle recommandées | |A3, A4, A5 | | A2, A3, A4 | | |
  | Edge : taille de machine virtuelle par défaut | | | | | |Windows : D12 v2 ; Linux : D4 v2 |
  | Edge : taille de machine virtuelle recommandée | | | | | |Windows : D12 v2, D13 v2, D14 v2; Linux : D4 v2, D12 v2, D13 v2, D14 v2 |
* Sud du Brésil et ouest du Japon uniquement (aucune taille pour V2) :

  | Type de cluster | Hadoop | hbase | Interactive Query |Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- | --- |
  | Head : taille de machine virtuelle par défaut |D3 |D3  | D13, D14 |A3 |D12 |D12 |
  | Head : tailles de machine virtuelle recommandées |D3, D4, D12 |D3, D4, D12  | D13, D14 |A3, A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | Worker : taille de machine virtuelle par défaut |D3 |D3  | D13, D14 |D3 |Windows : D12 ; Linux : D4 |Windows : D12 ; Linux : D4 |
  | Worker : tailles de machine virtuelle recommandées |D3, D4, D12 |D3, D4, D12  | D13, D14 |D3, D4, D12 |Windows : D12, D13, D14 ; Linux : D4, D12, D13, D14 |Windows : D12, D13, D14 ; Linux : D4, D12, D13, D14 |
  | Zookeeper : taille de machine virtuelle par défaut | |A2 | | A2 | | |
  | Zookeeper : tailles de machine virtuelle recommandées | |A2, A3, A4 | |A2, A3, A4 | | |
  | Edge : tailles de machine virtuelle par défaut | | | | | |Windows : D12 ; Linux : D4 |
  | Edge : tailles de machine virtuelle recommandées | | | | | |Windows : D12, D13, D14 ; Linux : D4, D12, D13, D14 |

> [!NOTE]
> - Head est appelé *Nimbus* pour le type de cluster Storm.
> - L’élément Worker est appelé *Supervisor* pour le type de cluster Storm.
> - L’élément Worker est appelé *Region* pour le type de cluster HBase.

## <a name="next-steps"></a>Étapes suivantes
- [Création de clusters Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Travailler à partir d’un PC Windows dans Hadoop sur HDInsight](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/
