---
title: "Utilisation du stockage Azure pour la sauvegarde et la restauration de SQL Server | Microsoft Docs"
description: "Découvrez comment sauvegarder SQL Server avec Azure Storage. Explique les avantages de la sauvegarde des bases de données SQL sur le stockage Azure."
services: virtual-machines-windows
documentationcenter: 
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: 39d4f452143454a345bd91f550e44c93651ff933
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Utilisation du stockage Azure pour la sauvegarde et la restauration de SQL Server
## <a name="overview"></a>Vue d'ensemble
À partir de SQL Server 2012 SP1 CU2, vous pouvez écrire des sauvegardes SQL Server directement dans le service de stockage d’objets blob Azure. Vous pouvez utiliser cette fonctionnalité pour les opérations de sauvegarde et de restauration à partir du service d’objets blob Azure avec une base de données SQL Server locale ou une base de données SQL Server sur une machine virtuelle Azure. La sauvegarde dans le cloud offre les avantages de la disponibilité, du stockage hors site géo-répliqué sans limite et de la facilité de migration des données vers et depuis le cloud. Vous pouvez émettre des instructions BACKUP ou RESTORE en utilisant Transact-SQL ou SMO.

SQL Server 2016 introduit de nouvelles fonctionnalités ; Vous pouvez utiliser la [sauvegarde instantanée de fichier](http://msdn.microsoft.com/library/mt169363.aspx) pour effectuer des sauvegardes quasi instantanées et des restaurations extrêmement rapides.

Cette rubrique explique pourquoi vous pourriez préférer utiliser le stockage Azure pour les sauvegardes SQL, puis décrit les composants impliqués. Vous pouvez utiliser les ressources fournies à la fin de l’article pour accéder aux procédures pas à pas et à des informations supplémentaires pour utiliser ce service avec vos sauvegardes SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Avantages de l’utilisation du service d’objets Azure pour les sauvegardes SQL Server
La sauvegarde de SQL Server pose diverses difficultés. Ces défis comprennent la gestion du stockage, le risque de défaillance du stockage, l’accès à un stockage hors site et la configuration matérielle. La plupart de ces difficultés peuvent être résolues avec le service de stockage d’objets blob Azure pour les sauvegardes SQL Server. Considérez les avantages suivants :

* **Simplicité d’utilisation**: le stockage de vos sauvegardes dans des objets blob Azure peut représenter une option hors site pratique, flexible et facile d’accès. La création d’un stockage hors site pour vos sauvegardes SQL Server peut être aussi facile que la modification de vos scripts/tâches existants pour utiliser la syntaxe **BACKUP TO URL** . Le stockage hors site doit généralement être suffisamment éloigné de l'emplacement de la base de données de production afin d'empêcher qu'un seul sinistre touche à la fois l'emplacement hors site et la base de données de production. En choisissant de [répliquer géographiquement vos objets blob Azure](../../../storage/common/storage-redundancy.md), vous obtenez une couche de protection supplémentaire en cas de sinistre susceptible d’affecter l’ensemble de la région.
* **Archive de sauvegarde**: le service BLOB Azure offre une meilleure alternative à l’option sur bande souvent utilisée pour archiver les sauvegardes. Le stockage sur bande peut nécessiter un transport physique vers une installation hors site ainsi que la prise de mesures de protection du support. Le stockage de vos sauvegardes dans le service BLOB Azure offre une option d’archivage instantané, hautement disponible et durable.
* **Matériel géré**: il n’y a aucun frais de gestion du matériel avec les services Azure. Ces derniers gèrent le matériel et fournissent une géo-réplication à des fins de redondance et de protection contre les défaillances matérielles.
* **Stockage illimité**: en activant une sauvegarde directe dans les objets blob Azure, vous avez accès à un stockage quasi illimité. De façon alternative, la sauvegarde sur un disque de machine virtuelle Azure présente des limites basées sur la taille de la machine. Le nombre de disques que vous pouvez attacher à une machine virtuelle Azure est limité pour les sauvegardes. Cette limite est de 16 disques pour une instance très volumineuse et un nombre inférieur pour les instances plus petites.
* **Disponibilité des sauvegardes**: Les sauvegardes stockées dans des objets blob Azure sont disponibles depuis n’importe où et à tout moment et facilement accessibles pour les restaurations sur un serveur SQL Server local ou un autre serveur SQL en cours d’exécution sur une machine virtuelle Azure, sans avoir besoin d’attacher/détacher la base de données ni télécharger et attacher le disque dur virtuel.
* **Coût**: ne payez que pour le service utilisé. Peut être économique comme option d'archivage hors site et de sauvegarde. Pour plus d’informations, consultez les pages [Calcul des coûts Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calcul des coûts") et [Article sur les tarifs Azure](http://go.microsoft.com/fwlink/?LinkId=277059 "Article sur les tarifs").
* **Instantanés de stockage**: quand les fichiers de base de données sont stockés dans un objet blob Azure et que vous utilisez SQL Server 2016, vous pouvez utiliser la [sauvegarde instantanée de fichiers](http://msdn.microsoft.com/library/mt169363.aspx) pour effectuer des sauvegardes quasi instantanées et des restaurations extrêmement rapides.

Pour plus d'informations, consultez la page [Sauvegarde et restauration SQL Server avec le service de stockage d'objets blob Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

Les deux sections suivantes présentent le service de stockage d’objets blob Azure, notamment les composants SQL Server requis. Il est important de comprendre les composants et leur interaction pour réaliser une sauvegarde ou une restauration depuis le service de stockage d’objets blob Azure.

## <a name="azure-blob-storage-service-components"></a>Composants du service BLOB Azure
Les composants Azure suivants sont utilisés pour les sauvegardes sur le service de stockage d’objets blob Azure.

| Composant | Description |
| --- | --- |
| **Compte de stockage** |le compte de stockage est le point de départ de tous les services de stockage. Pour accéder au service BLOB Azure, commencez par créer un compte de stockage Azure. Pour plus d'informations sur le service BLOB Azure, consultez la page [Utilisation du service BLOB Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Conteneur** |Un conteneur fournit un regroupement contenant un nombre illimité d’objets blob. Pour écrire une sauvegarde SQL Server sur un service BLOB Azure, au moins un conteneur racine doit être créé. |
| **Objet blob** |fichier de n'importe quel type et de n'importe quelle taille. Les objets blob sont adressables à l’aide du format d’URL suivant : **https:\\[compte stockage].blob.core.windows.net/[conteneur]/[blob]**. Pour plus d’informations sur les blobs, consultez [Présentation des objets blob de blocs et des objets blob de pages](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Composants SQL Server
Les composants SQL Server suivants sont utilisés pour les sauvegardes sur le service de stockage d’objets blob Azure.

| Composant | Description |
| --- | --- |
| **URL** |une URL attribue un URI (Uniform Resource Identifier) à un fichier de sauvegarde unique. L’URL fournit l’emplacement et le nom du fichier de sauvegarde SQL Server. L’URL doit pointer vers un objet blob réel, pas juste un conteneur. Si l’objet blob n’existe pas, il est créé. Si un objet blob existant est indiqué, BACKUP échoue, sauf si l’option > WITH FORMAT est indiquée. Voici un exemple d’URL que vous pouvez spécifier dans la commande BACKUP : **http[s]://[comptestockage].blob.core.windows.net/[conteneur]/[NOMFICHIER.bak]**. HTTPS n’est pas requis, mais recommandé. |
| **Informations d'identification** |les informations requises pour se connecter au service BLOB Azure et pour s'y authentifier sont stockées sous la forme d'informations d'identification.  Vous devez créer des informations d'identification SQL Server afin que ce dernier écrive les sauvegardes sur un service BLOB Azure ou les restaure depuis celui-ci. Pour plus d’informations, consultez [Informations d’identification SQL Server](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Si vous choisissez de copier et de charger un fichier de sauvegarde dans le service BLOB Azure, vous devez utiliser un type d’objet blob de pages comme option de stockage si vous prévoyez d’utiliser ce fichier pour des opérations de restauration. La commande RESTORE depuis un type d’objet blob de blocs échouera avec une erreur.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
1. Création d’un compte Azure si vous n’en avez pas encore. Si vous évaluez Azure, envisagez l’ [essai gratuit](https://azure.microsoft.com/free/).
2. Consultez ensuite un des didacticiels suivants pour créer un compte de stockage et effectuez une restauration.
   
   * **SQL Server 2014** : [Didacticiel : Sauvegarde et restauration SQL Server 2014 sur le service Stockage Blob Microsoft Azure](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [Didacticiel : utilisation du service de stockage d’objets blob Microsoft Azure avec des bases de données SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Passez en revue la documentation supplémentaire commençant par [Sauvegarde et restauration SQL Server avec le service de stockage d’objets blob Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

Si vous rencontrez des problèmes, consultez la rubrique [Meilleures pratiques et résolution des problèmes pour la sauvegarde de SQL Server vers une URL](https://msdn.microsoft.com/library/jj919149.aspx).

Pour les autres options de sauvegarde et de restauration de SQL Server, voir [Sauvegarde et restauration de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

