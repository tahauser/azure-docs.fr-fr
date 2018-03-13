---
title: "Architecture de cluster Azure HDInsight joint à un domaine | Microsoft Docs"
description: "Découvrez comment planifier les clusters HDInsight joints à un domaine."
services: hdinsight
documentationcenter: 
author: bhanupr
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/14/2017
ms.author: bprakash
ms.openlocfilehash: 5285199d22528ed6b9fa3b7dbc85e382e7b28569
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planifier des clusters Hadoop Azure joints à un domaine dans HDInsight

Le cluster HDInsight standard est un cluster à un seul utilisateur. Il convient à la plupart des entreprises qui font appel à des équipes d’application plus petites pour créer leurs charges de travail de données importantes. À mesure qu’Hadoop a gagné en popularité, nombre d’entreprises ont commencé la transition vers un modèle dans lequel les clusters sont gérés par les équipes informatiques et partagés par plusieurs équipes d’application. Ainsi, les fonctionnalités impliquant des clusters multi-utilisateur font partie des fonctionnalités les plus demandées dans Azure HDInsight.

Au lieu de créer une authentification et une autorisation multi-utilisateurs qui lui soient propres, HDInsight s’appuie sur le fournisseur d’identité le plus courant : Active Directory (AD). Dans AD, la puissante fonctionnalité de sécurité permet de gérer l’authentification multi-utilisateur dans HDInsight. En intégrant HDInsight à AD, vous pouvez communiquer avec les clusters au moyen de vos informations d’identification AD. Les machines virtuelles dans HDInsight sont jointes au domaine AD. C’est ainsi que HDInsight mappe un utilisateur AD à un utilisateur Hadoop local afin que tous les services exécutés sur HDInsight (Ambari, serveur Hive, Ranger, serveur Spark Thrift, etc.) fonctionnent de manière transparente pour l’utilisateur authentifié. Les administrateurs peuvent ensuite créer des stratégies d’autorisation strictes à l’aide d’Apache Ranger, en vue de contrôler l’accès en fonction du rôle pour les ressources dans HDInsight.


## <a name="integrate-hdinsight-with-active-directory"></a>Intégrer HDInsight avec Active Directory

Quand vous intégrez HDInsight avec Active Directory, les nœuds du cluster HDInsight sont joints à un domaine AD. La sécurité Kerberos est configurée pour les composants Hadoop sur le cluster. Pour chacun des composants Hadoop, un principal de service est créé dans Active Directory. Un principal de machine correspondant est également créé pour chaque machine jointe au domaine. Ces principaux de service et de machine peuvent encombrer votre répertoire Active Directory. Par conséquent, il est nécessaire de fournir une unité d’organisation dans Active Directory, afin d’y placer ces principaux. 

Pour résumer, vous devez configurer un environnement avec :

- un contrôleur de domaine Active Directory avec LDAPS configuré ;
- une connexion du réseau virtuel HDInsight à votre contrôleur de domaine Active Directory ;
- une unité d’organisation créée dans Active Directory ;
- un compte de service qui dispose des autorisations pour :

    - créer des principaux de service dans l’unité d’organisation ;
    - joindre des machines au domaine et créer des principaux de machine dans l’unité d’organisation.

La capture d’écran suivante montre une unité d’organisation créée dans contoso.com. Certains des principaux de service et de machine figurent également sur la capture d’écran.

![Unité d’organisation des clusters HDInsight joints à un domaine](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="two-ways-of-bringing-your-own-active-directory-domain-controllers"></a>Deux manières de configurer vos propres contrôleurs de domaine Active Directory

Il existe deux manières de faire en sorte que les contrôleurs de domaine Active Directory créent des clusters HDInsight joints au domaine. 

- **Azure Active Directory Domain Services** : ce service fournit un domaine Active Directory managé qui est compatible avec Windows Server Active Directory. Microsoft effectue la gestion, les mises à jour correctives et la surveillance du domaine AD. Vous pouvez déployer votre cluster sans vous préoccuper de la maintenance des contrôleurs de domaine. Les utilisateurs, les groupes et les mots de passe sont synchronisés à partir de votre répertoire Azure Active Directory, ce qui permet aux utilisateurs de se connecter au cluster à l’aide de leurs informations d’identification professionnelles. Pour plus d’informations, voir [Configurer les clusters HDInsight joints à un domaine à l’aide d’Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

- **Active Directory sur des machines virtuelles Azure IaaS** : cette option vous permet de déployer et gérer votre propre domaine Windows Server Active Directory sur des machines virtuelles Azure IaaS. Pour plus d’informations, voir [Configurer un environnement de bac à sable joint à un domaine](./apache-domain-joined-configure.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour configurer un cluster HDInsight joint à un domaine, consultez l’article [Configurer des clusters HDInsight joints à un domaine](apache-domain-joined-configure.md).
* Pour gérer des clusters HDInsight joints à un domaine, consultez l’article [Gestion des clusters HDInsight joints à un domaine](apache-domain-joined-manage.md).
* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez l’article [Configure Hive policies for domain-joined HDInsight clusters (Configurer des stratégies Hive pour les clusters HDInsight joints à un domaine)](apache-domain-joined-run-hive.md).
* Pour exécuter des requêtes Hive en utilisant SSH sur des clusters HDInsight joints au domaine, voir [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
